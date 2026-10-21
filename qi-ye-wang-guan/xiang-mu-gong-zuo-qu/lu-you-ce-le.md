# 路由策略

> 在直连模型验证完成后，用统一路由 ID 配置权重、故障转移或混合调度。

## 先判断是否需要路由

路由把多个候选模型封装成一个路由 ID，应用仍只提交一个 model 值。例：新业务先把少量流量分给候选模型观察效果，或按顺序设置主模型和备用模型。路由决定模型如何选择，不会授予调用者原本没有的模型权限。

| 需求           | 建议方式   | 原因            |
| ------------ | ------ | ------------- |
| 第一次完成模型调用    | 直连模型   | 先排除凭证和模型权限问题  |
| 业务始终固定一个模型   | 直连模型   | 配置更少，执行结果直接   |
| 多个模型按比例分流    | 负载均衡路由 | 统一使用一个路由 ID   |
| 主模型失败后尝试备用模型 | 故障转移路由 | 按明确顺序切换候选     |
| 既要分流又要组间容灾   | 混合路由   | 先按组内权重，再按组间顺序 |

## 创建前检查

* 当前项目状态允许新调用。
* 至少一个目标模型已加入当前项目。
* 当前账号拥有路由管理权限，并对目标模型有权限。
* 已经用同一项目的个人 API Key 直连至少一个目标模型成功。
* 请求使用网关已开放的大语言模型接口协议，并按对应协议填写路径、字段和流式参数。

## 选择个人或全局路由

个人路由只供当前账号在当前项目使用；全局路由供项目内符合权限条件的成员使用。两者都不能跨项目调用。

## 按调度目标选择模式

| 模式   | 调度方式        | 适用场景       |
| ---- | ----------- | ---------- |
| 负载均衡 | 按权重分配请求     | 多渠道分流与成本控制 |
| 故障转移 | 按顺序尝试目标     | 优先稳定性与备用路径 |
| 混合模式 | 权重分配并结合故障转移 | 同时需要分流和容灾  |

## 选择目标并设置顺序或权重

目标从当前项目可用模型中选择。故障转移关注尝试顺序，负载均衡关注同组权重，混合模式分别配置负载均衡组和故障转移组；负载均衡组的权重合计必须为 100%。

> **影响与边界**：权重表示长期请求分配倾向，不保证每几次调用就严格按比例出现。小样本结果不能直接判定配置错误。

## 创建并保存策略

1. 先进入「我的策略」或「全局策略」，再点击对应的新增策略按钮。
2. 填写名称，确认策略范围，再选择路由模式。
3. 从当前项目模型池添加目标，并按业务优先级分组或排序。
4. 配置所选模式需要的权重和顺序。
5. 保存后确认策略状态可用，并复制路由策略 ID。

保存后复制路由 ID，并用调用日志验证实际模型。

> **需要注意**：个人策略与全局策略分别管理；达到当前范围的策略数量上限时，页面会阻止新增。不要把策略数量限制理解为单条策略的模型数量限制。

## 把路由 ID 放入请求

将路由策略 ID 作为 model 参数发起请求，其他认证方式与直连模型一致。所用项目 API Key、路由和路由中的模型必须属于同一项目。

### Shell

```bash
export SSY_API_KEY="YOUR_PROJECT_API_KEY"
export SSY_BASE_URL="YOUR_PROJECT_BASE_URL" # 从当前项目复制，保留项目和版本路径

curl --fail-with-body --max-time 60 "$SSY_BASE_URL/chat/completions" \
  -H "Authorization: Bearer $SSY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "YOUR_ROUTE_ID",
    "messages": [{"role": "user", "content": "你好"}],
    "stream": false
  }'
```

### Python

```python
import os
import requests

response = requests.post(
    f"{os.environ['SSY_BASE_URL']}/chat/completions",
    headers={"Authorization": f"Bearer {os.environ['SSY_API_KEY']}"},
    json={
        "model": "YOUR_ROUTE_ID",
        "messages": [{"role": "user", "content": "你好"}],
        "stream": False,
    },
    timeout=60,
)
response.raise_for_status()
print(response.json())
```

### JavaScript

```javascript
const response = await fetch(
  `${process.env.SSY_BASE_URL}/chat/completions`,
  {
    method: "POST",
    headers: {
      "Authorization": `Bearer ${process.env.SSY_API_KEY}`,
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      model: "YOUR_ROUTE_ID",
      messages: [{ role: "user", content: "你好" }],
      stream: false,
    }),
  }
);

if (!response.ok) throw new Error(`HTTP ${response.status}: ${await response.text()}`);
console.log(await response.json());
```

## 用多次调用验证调度结果

1. 连续发起多次低风险测试请求，保存每次请求 ID。
2. 在调用日志中核对实际模型、供应商、状态和平台费用。
3. 负载均衡看一段样本的整体分布，不用单次结果判断权重。
4. 故障转移需在受控环境验证备用目标，不在生产流量中主动制造故障。
5. BYOK 同时适用时，先完成当前候选模型的允许接入路径，再进入下一模型。

> **需要注意**：验证完成的标志是调用日志能解释每次实际模型和接入方式，而不只是策略状态显示“可用”。

## 模型或权限变更会影响路由

项目移除被路由引用的模型时会同步解除引用；成员失去调用目标模型的权限后，不能通过路由绕过限制。没有可用目标的策略会停用或无法继续工作，系统不会自动补入替代模型。

## 路由调用失败时

1. 确认 API Key、路由 ID 和目标模型都属于当前项目。
2. 确认当前成员拥有路由内至少一个可用模型的权限。
3. 确认路由仍有可用目标，权重和顺序配置完整。
4. 确认项目未暂停，预算和速率限额仍可用。
5. 涉及 BYOK 时，区分同模型渠道回退和跨模型故障转移，不把两类结果混在一起。
6. 用请求 ID 在调用日志中确认实际模型和接入方式。
