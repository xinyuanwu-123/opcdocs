# 项目 api key

> 使用系统准备的个人 Default API Key 完成调用，再按应用、环境和安全边界管理其他 Key。

## 先确认项目和个人范围

项目 API Key 用于模型调用，列表默认掩码展示归属于当前登录账号和当前项目的 Key。当前账号可查看并复制本人完整 Key；切换项目后会进入另一组凭证。主账号、系统管理员或项目管理员身份也不会自动看到其他账号的完整凭证。 它相当于应用的调用凭证，不是登录密码、企业管理 AccessKey，也不是供应商 BYOK 密钥。

## 优先使用个人 Default API Key

当前账号首次进入有权限且本人尚无个人 Key 的项目时，系统会幂等准备仅本人可见和管理的 Default API Key。已有任一个人 Key 时不重复补建；财务对账人员不进入项目工作区，也不会生成个人默认 Key。

确认项目与个人范围，凭证按掩码展示。

> **需要注意**：首次接入不需要先创建密钥。页面出现 Default API Key 且当前账号能查看、复制本人完整 Key，才算凭证准备完成；密钥不可用时，先到项目 API Key 检查是否被禁用或删除。

## 上线前设置真实来源 IP

Default API Key 初始不限制来源 IP。生产接入时，应根据业务网络边界选择是否限制来源，并填写服务访问公网时实际使用的出口 IPv4 或 CIDR。

* 先在测试环境确认真实出口 IP，不要直接填写开发电脑的临时地址。
* 通过代理、NAT、云函数或多出口网络访问时，按真实调用链整理所有合法出口。
* 修改限制后立即发起测试调用，确认合法来源可用、非白名单来源被拒绝。

## 只有需要隔离时才创建其他 Key

当不同应用、环境、团队或轮换周期需要独立停用和追踪时，再创建额外 Key。不要为每次调用或每个模型重复建 Key。

1. 进入目标项目的「项目 API Key」，点击「创建」。
2. 用应用和环境都能识别的名称区分用途。
3. 按真实出口配置来源 IP；暂不限制时明确保留默认结果。
4. 提交后在页面查看或复制本人完整 Key，并保存到密钥管理服务或安全环境变量。
5. 记录负责人和使用位置，便于后续轮换与停用。

> **需要注意**：当前账号在每个项目最多创建 5 把 API Key，页面会显示已用数量。项目 API Key 可由本人后续查看和复制完整值；企业 AccessKey 的 SecretKey 只在创建或轮换结果中展示一次。

## 确认调用链已经打通

* 当前账号是目标项目成员，并绑定了允许模型调用的项目角色。
* 模型已加入当前项目的可用范围。
* 请求使用网关已开放的大语言模型接口协议，并按对应协议填写路径、字段和流式参数。
* 当前账号具备调用权限，且预算、RPM、TPM 等用量限额满足本次调用。
* 项目状态允许新调用。
* 项目 API Key、目标模型或路由 ID 都属于当前项目。

## 决定直连模型还是使用路由

| 调用方式 | model 参数    | 优先选择它的情况         |
| ---- | ----------- | ---------------- |
| 直连模型 | 项目内可用模型 ID  | 第一次联调，或业务明确固定模型  |
| 路由策略 | 当前项目路由策略 ID | 需要权重分流、故障转移或混合调度 |

> **需要注意**：建议先用直连模型完成最小调用，再接入路由。这样可以区分凭证、模型权限和路由配置问题。

## 发送第一次请求

从当前项目的接入信息复制 Base URL，保留项目和版本路径。本页以 Chat Completions 为例，在该地址后追加 `/chat/completions`，不要再次拼接 `/v1`。把本人的项目 API Key 放入 Authorization 请求头，model 使用当前项目内可用的模型 ID；运行环境见[普通成员快速调用](/broken/pages/f8c9ca81d86cce5a9e45812543adfb2507a9158a)。

### Shell

```bash
export SSY_API_KEY="YOUR_PROJECT_API_KEY"
export SSY_BASE_URL="YOUR_PROJECT_BASE_URL" # 从当前项目复制，保留项目和版本路径

curl --fail-with-body --max-time 60 "$SSY_BASE_URL/chat/completions" \
  -H "Authorization: Bearer $SSY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "deepseek/deepseek-v3",
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
        "model": "deepseek/deepseek-v3",
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
      model: "deepseek/deepseek-v3",
      messages: [{ role: "user", content: "你好" }],
      stream: false,
    }),
  }
);

if (!response.ok) throw new Error(`HTTP ${response.status}: ${await response.text()}`);
console.log(await response.json());
```

## 用请求 ID 完成验证

1. 确认接口返回成功并保存响应中的请求 ID。
2. 进入同一项目的「调用日志」，用请求 ID 定位记录。
3. 核对调用账号、项目 API Key、实际模型、接入方式、Token、性能和平台费用。
4. 使用路由或 BYOK 时，确认实际执行路径与预期一致。

> **需要注意**：只有请求成功且日志中的项目、模型、渠道和费用都正确，才算接入完成。

## 轮换、停用和删除

轮换时先创建并接入新 Key，完成真实调用验证后再停用旧 Key；观察业务稳定后再删除。停用用于可恢复阻断，删除用于确认不再使用的凭证。

## 调用失败时按顺序检查

1. 确认 Base URL、Authorization 格式和请求路径正确。
2. 确认当前项目、Key、模型或路由属于同一项目。
3. 确认账号、角色、模型权限和项目状态允许调用。
4. 确认来源 IP 使用真实出口，且命中当前 Key 的允许范围。
5. 确认预算、RPM、TPM 和供应商或平台额度没有阻断。
6. 能获得请求 ID 时转到调用日志继续排查；未形成完成记录时保留时间和错误响应。
