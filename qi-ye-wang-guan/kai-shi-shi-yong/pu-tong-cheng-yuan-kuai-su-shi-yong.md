# 普通成员快速使用

> 已经加入项目？使用自己的 Default API Key 和项目里的模型完成第一次调用。

## 选择模型与使用场景

普通成员无需先配置整个企业。让管理员把你加入项目后，准备当前项目的调用地址（Base URL）、自己的项目 API Key 和可用模型 ID；三者分别告诉应用“发到哪里、以谁的身份调用、调用哪个模型”。 开始前确认已经加入目标项目，在「模型管理」选择可用模型，再到「项目 API Key」复制调用地址和自己的 Key。调用地址从当前项目复制，保留原有项目路径和版本路径；没有项目入口时先联系管理员添加项目关系。

* 使用 Agent 或应用平台：选择应用支持的大语言模型接口协议，填写 Base URL、API Key 和模型 ID；OpenAI Compatible、Responses 或 Messages 等协议均由企业网关统一兼容。
* 使用 API 或 SDK：复制 Shell、Python 或 JavaScript 示例，替换 API Key 和模型 ID。
* 当前项目可调用的大语言模型和路由策略 ID 均可使用网关已开放的全部大语言模型接口协议，无需逐模型核对协议支持。

> **运行示例前**：Shell 需要 curl；Python 3 需要 requests（python3 -m pip install requests）；JavaScript 使用 Node.js 18 或以上并保存为 .mjs。先设置 SSY\_BASE\_URL、SSY\_API\_KEY 环境变量，再选择一种语言运行；替换 YOUR\_ 开头的占位值。

## 在你的应用中完成首次调用

把上一步的连接信息填入应用，或复制示例运行。收到模型返回的内容，就完成了第一次调用；随后可以在“调用日志”中确认这条记录。

> **第一次调用成功后**：没有可用模型时查看[模型管理](../xiang-mu-gong-zuo-qu/mo-xing-guan-li.md)；凭证问题查看[项目 API Key](../xiang-mu-gong-zuo-qu/xiang-mu-api-key.md)；请求完成后用请求 ID 在[调用日志](../xiang-mu-gong-zuo-qu/diao-yong-ri-zhi.md)核对结果。

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

```json
{
  "id": "20250410143202432620157mDT6DvdC",
  "provider": "OpenRouter",
  "model": "deepseek/deepseek-v3",
  "object": "chat.completion",
  "created": 1744266722,
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "你好！有什么可以帮你？"
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 4,
    "completion_tokens": 9,
    "total_tokens": 13
  }
}
```
