# 开发者快速入门指南

> 欢迎使用 <code class="expression">space.vars.mainname</code> API 接入服务。只需一次接入，即可统一调用多个主流 AI 模型，平台将根据请求内容和配置自动路由到最优模型，帮您降低成本、提升响应质量。

本文以调用 glm-4为例，引导您完成大模型 API 调用，您将了解到：

* 如何获取 API Key。
* 如何通过 <code class="expression">space.vars.mainname</code> 调用 glm-4 API。

***

### 获取 API Key

访问 控制台( <code class="expression">space.vars.console</code>) 注册并登录账号。

{% stepper %}
{% step %}
进入「控制台」页面。
{% endstep %}

{% step %}
在「API密钥」模块创建并复制您的专属 API Key。

![image.png](<../.gitbook/assets/image preview (6)>)

> 创建 API Key 时，请注意当前账户的 `RPM` 和 `TPM`，如有疑问，请 [联系客服](zhang-hao-zhi-nan.md#guan-fang-ke-fu-he-jiao-liu-qun)。
{% endstep %}

{% step %}
请妥善保管密钥，避免泄露。您可以随时在控制台中编辑或删除它。

![image.png](https://s.apifox.cn/api/v1/projects/6613585/resources/544553/image-preview?onlineShareType=shareDoc\&locale=zh-CN)
{% endstep %}
{% endstepper %}

***

### 调用统一接口

<code class="expression">space.vars.mainname</code> 提供统一的 API 调用地址，您只需调用一个端点，即可访问多个模型。

使用下面命令时，请将 `SSY_API_KEY` 替换为您的 API Key。

{% tabs %}
{% tab title="Python" %}
```python
from openai import OpenAI

client = OpenAI(
    base_url="{{space.vars.baseurl}}",
    api_key="$SSY_API_KEY",
)

try:
    completion = client.chat.completions.create(
        model="bigmodel/glm-4",
        messages=[{"role": "user", "content": "Which number is larger, 9.11 or 9.8?"}],
        temperature=0.6,
        top_p=0.7,
        stream=True,
    )

    response_text = ""

    for chunk in completion:
        if chunk.choices and chunk.choices[0].delta.content is not None:
            content = chunk.choices[0].delta.content
            print(content, end="", flush=True)
            response_text += content

except Exception as e:
    print(f"Request failed: {e}")
```
{% endtab %}

{% tab title="Node.js" %}
```json
import OpenAI from "openai";

const openai = new OpenAI({
baseURL: "{{space.vars.baseurl}}",
apiKey: "$SSY_API_KEY",
});

async function main() {
try {
  const stream = await openai.chat.completions.create({
    model: "bigmodel/glm-4",
    messages: [{ role: "user", content: "Which number is larger, 9.11 or 9.8?" }],
    temperature: 0.6,
    top_p: 0.7,
    stream: true,
  });

  for await (const chunk of stream) {
    if (chunk.choices[0].delta.content) {
      process.stdout.write(chunk.choices[0].delta.content);
    }
  }

  console.log("[chat complete]");

} catch (error) {
  console.error("Request failed:", error.message);
}
}

main();
```
{% endtab %}

{% tab title="curl" %}
```http
#!/bin/bash

API_KEY="$SSY_API_KEY"
BASE_URL="{{space.vars.baseurl}}/v1/chat/completions"
MODEL="bigmodel/glm-4"

curl -s -X POST "$BASE_URL"
     -H "Content-Type: application/json"
     -H "Authorization: Bearer $API_KEY"
     -d '{
          "model": "'"$MODEL"'",
          "messages": [{"role": "user", "content": "Which number is larger, 9.11 or 9.8?"}],
          "temperature": 0.6,
          "top_p": 0.7,
          "stream": true
      }'

echo -e "[chat complete]"
```
{% endtab %}

{% tab title="接口测试工具" %}
**API端点**

URL：\{{space.vars.baseurl\}}/v1/chat/completions

方法：`POST`

**请求头（Header）**

请求头必须包含以下内容：

Authorization: 用于身份验证的 API 密钥。格式为`Bearer <$SSY_API_KEY>`

Content-Type: 必须设置为 application/json。
{% endtab %}
{% endtabs %}

***

### API 参考

关于 API 错误代码说明，请参见 [API 错误代码说明](../da-yu-yan-mo-xing-jie-ru/api-cuo-wu-dai-ma-shuo-ming.md)。

关于其他模型，请参见 模型列表（ <code class="expression">space.vars.modellist</code> ）。
