# 创建消息

**POST** `/v1/messages`

以 Anthropic 格式发送消息到 <code class="expression">space.vars.mainname</code> 模型并获取响应。支持流式和非流式响应，可以处理纯文本、多模态内容（文本+图片）、工具调用等多种场景。与官方接口完全一致，更新不及时可直接参考 Anthropic 官方文档。

## 请求参数

### Authorization

Bearer Token

在 Header 添加参数 `Authorization`，其值为在 Bearer 之后拼接 Token。

示例：

```http
Authorization: Bearer ********************
```

### Body 参数 `application/json` 必填

#### model

`enum<string>`，必需

要使用的模型名称。支持<code class="expression">space.vars.mainname</code>平台所有大语言模型。

枚举值：

* `anthropic/claude-sonnet-4.5`
* `anthropic/claude-sonnet-4`

示例：

```json
anthropic/claude-sonnet-4.5
```

#### messages

`array[object (Claude 请求体-Message)]`，必需

对话消息列表，按时间顺序排列。必须以 user 角色开始，角色必须交替出现（user/assistant/user/assistant...）。

> \= 1 items

**role**

`enum<string>`，必需

消息角色：

* `user`：用户发送的消息
* `assistant`：助手（Claude）的回复

注意：对话必须以 user 开始，角色必须交替出现。

枚举值：

* `user`
* `assistant`

**content**

`<string>` 必需

消息内容。可以是简单的文本字符串，也可以是包含多种类型内容的数组（文本、图片、工具调用等）。

#### max\_tokens

`integer`，必需

模型生成响应的最大 token 数量。Claude 必须指定此参数。不同模型有不同的上限：

* claude-3-5-sonnet: 8192
* claude-3-opus: 4096
* claude-3-7-sonnet: 65536（支持扩展思考时）

范围：>= 1 ,<= 65536

示例：

```json
1024
```

#### system

可选

系统提示，在对话开始前设置模型的行为。可以是简单的文本字符串，也可以是包含多个内容块的数组（用于提示词缓存）。

类型：

* `string`
* `array[object (Claude 响应 - messages)]`

系统提示（纯文本格式）- 用于设置模型的角色、行为准则、背景知识等。

#### temperature

`number`，可选

采样温度，控制输出的随机性。值越高，输出越随机；值越低，输出越确定。

* 0.0：几乎确定性输出，适合需要一致性的任务
* 1.0：默认值，平衡创造性和一致性

使用场景：创意写作用高温度，代码生成用低温度

范围：>= 0 , <= 1

默认值：`1`

示例：

```json
0.7
```

#### top\_p

`number`，可选

Nucleus sampling 参数。模型会从累积概率达到 top\_p 的最小 token 集合中采样。

* 0.1：只考虑最可能的 10% token
* 1.0：考虑所有 token

推荐在 temperature 和 top\_p 之间只调整一个。

范围：>= 0 , <= 1

示例：

```json
0.9
```

#### top\_k

`integer`，可选

Top-K sampling 参数。模型只从概率最高的 K 个 token 中采样。

* 值越小，输出越保守
* 值越大，输出越多样
* 设为 0 或不设置则不限制

范围：>= 0

示例：

```json
40
```

#### stop\_sequences

`array[string]`，可选

自定义停止序列。当模型生成这些字符串之一时，会立即停止生成。最多可以指定 4 个停止序列。

使用场景：限制输出格式、控制生成长度等。

`<= 4 items`

示例：

```json
["\n\nHuman:","\n\nAssistant:"]
```

#### stream

`boolean`，可选

是否使用流式响应（Server-Sent Events）。

* `true`：实时接收模型输出，适合长文本生成
* `false`：等待完整响应后一次性返回

默认值：`false`

#### tools

`array[object (Claude 请求体-Tool)]`，可选

可供模型调用的工具列表。模型会根据用户请求自动决定是否调用这些工具。

每个工具需要定义：名称、描述、输入参数的 JSON Schema。

**name**

`string`，必需

工具的唯一名称。应该使用描述性的名称，如 `get_weather`、`search_database` 等。

**description**

`string`，可选

工具的详细描述。清晰的描述能帮助模型更好地理解何时以及如何使用这个工具。

**input\_schema**

`object`，必需

JSON Schema 格式的工具输入参数定义。定义工具需要哪些参数、参数类型、是否必需等。

#### tool\_choice

可选

控制模型如何使用工具。默认为 `auto`（模型自主决定）。

类型：

* `object`

**type**

`enum<string>`，必需

工具选择模式：

* `auto`：模型自动决定是否使用工具（默认）
* `any`：模型必须使用某个工具
* `tool`：模型必须使用指定的工具（需配合 name 字段）

枚举值：

* `auto`
* `any`
* `tool`

**name**

`string`，可选

当 type 为 `tool` 时，指定必须使用的工具名称。

#### thinking

`object (Claude 请求体-Thinking)`，可选

扩展思考配置。启用后，模型会进行更深入的内部推理。

**type**

`enum<string>`，必需

是否启用扩展思考功能：

* `enabled`：启用，模型会进行更深入的推理
* `disabled`：禁用（默认）

枚举值：

* `enabled`
* `disabled`

**budget\_tokens**

`integer`，可选

思考预算 token 数量。限制模型内部推理可使用的最大 token 数。

范围：0-10000

注意：思考 token 会计入总 token 消耗。

示例：

```json
5000
```

## 示例

* 简单文本对话
* 多模态消息（含图片）
* 带思考过程（Extended Thinking）

{% tabs %}
{% tab title="简单文本对话" %}
```json
{
    "model": "anthropic/claude-sonnet-4.5",
    "max_tokens": 1024,
    "messages": [\
        {\
            "role": "user",\
            "content": "你好，Claude！请介绍一下你自己。"\
        }\
    ]
}
```
{% endtab %}

{% tab title="多模态消息（含图片）" %}
```json
{ 
    "model": "anthropic/claude-sonnet-4.5",
    "max_tokens": 2048,
    "messages": [
        {
            "role": "user",
            "content": [
                {
                    "type": "text",
                    "text": "这张图片中有什么内容？请详细描述。"
                },
                {
                    "type": "image",
                    "source": {
                        "type": "base64",
                        "media_type": "image/jpeg",
                        "data": "iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVR42mNk+M9QDwADhgGAWjR9awAAAABJRU5ErkJggg=="
                    }
                }
            ]
        }
    ]
}
```
{% endtab %}

{% tab title="带思考过程" %}
```json
{
    "model": "anthropic/claude-sonnet-4.5",
    "max_tokens": 16000,
    "thinking": {
        "type": "enabled",
        "budget_tokens": 10000
    },
    "messages": [
        {
            "role": "user",
            "content": "请一步步分析并解决这个复杂的数学问题：如果有5个人参加会议，每个人都要和其他人握手一次，总共需要握手多少次？请详细说明推理过程。"
        }
    ]
}
```
{% endtab %}
{% endtabs %}

## 请求示例代码

### cURL

{% tabs %}
{% tab title="python" %}
{% code title="python request 简单文本示例" %}
```python
import requests
import json

url = "https://router.shengsuanyun.com/api/v1/messages"

payload = json.dumps({
   "model": "anthropic/claude-sonnet-4.5",
   "max_tokens": 1024,
   "messages": [
      {
         "role": "user",
         "content": "你好，Claude！请介绍一下你自己。"
      }
   ]
})
headers = {
   'Authorization': 'Bearer <token>',
   'Content-Type': 'application/json'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```
{% endcode %}
{% endtab %}

{% tab title="js" %}
{% code title="js fetch多模态带图片示例" %}
```json
const myHeaders = new Headers();
myHeaders.append("Authorization", "Bearer <token>");
myHeaders.append("Content-Type", "application/json");

const raw = JSON.stringify({
   "model": "anthropic/claude-sonnet-4.5",
   "max_tokens": 2048,
   "messages": [
      {
         "role": "user",
         "content": [
            {
               "type": "text",
               "text": "这张图片中有什么内容？请详细描述。"
            },
            {
               "type": "image",
               "source": {
                  "type": "base64",
                  "media_type": "image/jpeg",
                  "data": "iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVR42mNk+M9QDwADhgGAWjR9awAAAABJRU5ErkJggg=="
               }
            }
         ]
      }
   ]
});

const requestOptions = {
   method: "POST",
   headers: myHeaders,
   body: raw,
   redirect: "follow"
};

fetch("https://router.shengsuanyun.com/api/v1/messages", requestOptions)
   .then((response) => response.text())
   .then((result) => console.log(result))
   .catch((error) => console.error(error));
```
{% endcode %}
{% endtab %}

{% tab title="curl" %}
{% code title="curl shell 带系统提示词示例" %}
```shellscript
curl --location 'https://router.shengsuanyun.com/api/v1/messages' \
--header 'Authorization: Bearer <token>' \
--header 'Content-Type: application/json' \
--data '{
    "model": "anthropic/claude-sonnet-4.5",
    "max_tokens": 1024,
    "system": "你是一个专业的 Python 编程助手，擅长代码优化和最佳实践指导。",
    "messages": [
        {
            "role": "user",
            "content": "如何优化 Python 列表推导式的性能？"
        }
    ],
    "temperature": 0.7
}'
```
{% endcode %}
{% endtab %}

{% tab title="c" %}
{% code title="c 深度思考示例" %}
```c
CURL *curl;
CURLcode res;
curl = curl_easy_init();
if(curl) {
   curl_easy_setopt(curl, CURLOPT_CUSTOMREQUEST, "POST");
   curl_easy_setopt(curl, CURLOPT_URL, "https://router.shengsuanyun.com/api/v1/messages");
   curl_easy_setopt(curl, CURLOPT_FOLLOWLOCATION, 1L);
   curl_easy_setopt(curl, CURLOPT_DEFAULT_PROTOCOL, "https");
   struct curl_slist *headers = NULL;
   headers = curl_slist_append(headers, "Authorization: Bearer <token>");
   headers = curl_slist_append(headers, "Content-Type: application/json");
   curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);
   const char *data = "{\n    \"model\": \"anthropic/claude-sonnet-4.5\",\n    \"max_tokens\": 16000,\n    \"thinking\": {\n        \"type\": \"enabled\",\n        \"budget_tokens\": 10000\n    },\n    \"messages\": [\n        {\n            \"role\": \"user\",\n            \"content\": \"请一步步分析并解决这个复杂的数学问题：如果有5个人参加会议，每个人都要和其他人握手一次，总共需要握手多少次？请详细说明推理过程。\"\n        }\n    ]\n}";
   curl_easy_setopt(curl, CURLOPT_POSTFIELDS, data);
   res = curl_easy_perform(curl);
   curl_slist_free_all(headers);
}
curl_easy_cleanup(curl); 
```
{% endcode %}
{% endtab %}
{% endtabs %}

## 返回响应

### 200

`application/json`

成功响应 - 返回模型生成的消息内容。

### Body `application/json`

#### id

`string`，可选

消息的唯一标识符，格式为 `msg_xxx`

示例：

```json
msg_01XFDUDYJgAACzvnptvVoYEL
```

#### type

`enum<string>`，可选

响应类型，固定为 `message`

值：

* `message`

#### role

`enum<string>`，可选

响应角色，固定为 `assistant`

值：

* `assistant`

#### content

`array[object (Claude 响应 - messages)]`，可选

响应内容数组。可能包含文本、工具调用、思考过程等多种类型的内容块。

**type**

`enum<string>`，可选

内容块类型：

* `text`：纯文本
* `image`：图片（通过 source 字段提供）
* `tool_use`：模型请求调用工具
* `tool_result`：工具执行结果
* `thinking`：思考过程（扩展思考功能）

枚举值：

* `text`
* `image`
* `tool_use`
* `tool_result`
* `thinking`

**text**

`string`，可选

文本内容（当 type 为 `text` 时必需）。

**source**

`object (Claude 响应 - MessageSource)`，可选

图片来源（当 type 为 `image` 时必需）。

**id**

`string`，可选

工具调用的唯一标识符（当 type 为 `tool_use` 时必需）。

**name**

`string`，可选

工具名称（当 type 为 `tool_use` 时必需）。

**input**

`object`，可选

工具输入参数（当 type 为 `tool_use` 时必需）。

**tool\_use\_id**

`string`，可选

对应的工具调用 ID（当 type 为 `tool_result` 时必需）。

**content**

可选

工具执行结果内容（当 type 为 `tool_result` 时必需）。可以是文本字符串或包含多个内容块的数组。

**cache\_control**

`object`，可选

缓存控制标记。用于提示词缓存功能，可以显著降低延迟和成本。

**thinking**

`string`，可选

思考内容（当 type 为 `thinking` 时存在）- 模型的内部推理过程。

#### model

`string`，可选

实际使用的模型名称

示例：

```json
anthropic/claude-sonnet-4.5
```

#### stop\_reason

`enum<string>`，可选

生成停止的原因：

* `end_turn`：模型自然结束回复
* `max_tokens`：达到 `max_tokens` 限制
* `stop_sequence`：遇到停止序列
* `tool_use`：模型请求使用工具

枚举值：

* `end_turn`
* `max_tokens`
* `stop_sequence`
* `tool_use`

#### usage

`object (Claude 响应-Usage)`，可选

Token 使用情况统计。

**input\_tokens**

`integer`，可选

输入 token 数量（包括消息、系统提示等）。

**cache\_creation\_input\_tokens**

`integer`，可选

用于创建缓存的输入 token 数量（提示词缓存功能）。

**cache\_read\_input\_tokens**

`integer`，可选

从缓存中读取的输入 token 数量（提示词缓存功能，这部分 token 费用更低）。

**output\_tokens**

`integer`，可选

输出 token 数量（模型生成的内容）。

### 示例

```json
{
    "id": "msg_01XFDUDYJgAACzvnptvVoYEL",
    "type": "message",
    "role": "assistant",
    "content": [\
        {\
            "type": "text",\
            "text": "你好！我是 Claude，一个由 Anthropic 开发的 AI 助手。我可以帮助你进行对话、回答问题、分析内容、编写代码等多种任务。有什么我可以帮助你的吗？"\
        }\
    ],
    "model": "anthropic/claude-sonnet-4.5",
    "stop_reason": "end_turn",
    "usage": {
        "input_tokens": 12,
        "output_tokens": 45,
        "cache_creation_input_tokens": 0,
        "cache_read_input_tokens": 0
    }
}
```

### 错误状态码

* `400`
  * 请求错误 - 请求参数格式错误或缺少必需字段
* `401`
  * 认证失败 - API Key 无效或缺失
* `429`
  * 请求频率超限 - 超过了 API 调用速率限制
* `500`
  * 服务器内部错误
