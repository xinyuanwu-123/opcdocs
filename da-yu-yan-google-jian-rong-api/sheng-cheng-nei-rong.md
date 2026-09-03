# 生成内容

**POST** `/v1/models/{model}:generateContent`

使用 Gemini 模型生成内容。支持纯文本、多模态输入（图片、音频、视频、PDF）、工具调用、JSON 模式输出等多种功能，与 Google ai studio 官方接口相同，更新不及时可参考官方文档。

## 请求参数

### Authorization

#### API Key

在 header 添加参数 `x-goog-api-key`

示例：

`x-goog-api-key: ********************`

#### API Key

在 query 添加参数 `key`

示例：

`key: ********************`

### Path 参数

#### `model`

* 类型：`enum<string>`
* 必需：是

当前仅支持 Gemini 系列模型，包括图像生成模型，后续会兼容<code class="expression">space.vars.mainname</code> 平台所有模型。

枚举值：

* `google/gemini-3-pro-preview`
* `google/gemini-2.5-flash`

示例：

`google/gemini-3-pro-preview`

### Query 参数

#### `key`

* 类型：`string`
* 可选：是

Google AI Studio API Key（认证方式1：Query 参数）。从 [https://aistudio.google.com/apikey](https://aistudio.google.com/apikey) 获取。

#### `alt`

* 类型：`enum<string>`
* 可选：是

响应格式：

* `json`：标准 JSON 响应（默认）
* `sse`：Server-Sent Events 流式响应

枚举值：

* `json`
* `sse`

默认值：

`json`

### Body 参数 `application/json`

#### `contents`

* 类型：`array[object (Gemini Chat 请求体-Content)]`
* 必需：是

对话内容列表，按时间顺序排列。每条消息包含角色和内容部分（parts）。

`>= 1 items`

**`role`**

* 类型：`enum<string>`
* 可选：是

消息角色：

* `user`：用户发送的消息
* `model`：模型（Gemini）的回复

注意：`systemInstruction` 中不需要指定 `role`

枚举值：

* `user`
* `model`

**`parts`**

* 类型：`array[object (Gemini Chat 请求体-Part)]`
* 必需：是

内容部分列表。一条消息可以包含多个部分（文本、图片、音频、视频、函数调用等）。

`>= 1 items`

#### `systemInstruction`

* 类型：`object(Gemini Chat 请求体-Content)`
* 可选：是

Gemini Chat 请求体-Content

系统指令，用于设置模型的角色、行为准则、背景知识等。在对话开始前生效。

**`role`**

* 类型：`enum<string>`
* 可选：是

消息角色：

* `user`：用户发送的消息
* `model`：模型（Gemini）的回复

注意：`systemInstruction` 中不需要指定 `role`

枚举值：

* `user`
* `model`

**`parts`**

* 类型：`array[object (Gemini Chat 请求体-Part)]`
* 必需：是

内容部分列表。一条消息可以包含多个部分（文本、图片、音频、视频、函数调用等）。

`>= 1 items`

#### `generationConfig`

* 类型：`object(Gemini Chat 请求体-ChatGenerationConfig)`
* 可选：是

Gemini Chat 请求体-ChatGenerationConfig

生成配置，控制输出的特性（温度、最大长度、输出格式等）。

**`temperature`**

* 类型：`number`
* 可选：是

采样温度，控制输出的随机性。

* `0.0`：几乎确定性输出
* `1.0`：默认值，平衡创造性和一致性
* `2.0`：最大值，输出更随机

使用场景：创意写作用高温度，数据分析用低温度。

`>= 0 <= 2`

示例：

`0.9`

**`topP`**

* 类型：`number`
* 可选：是

Nucleus sampling 参数。模型会从累积概率达到 `topP` 的最小 token 集合中采样。

`>= 0 <= 1`

示例：

`1`

**`topK`**

* 类型：`number`
* 可选：是

Top-K sampling 参数。模型只从概率最高的 K 个 token 中采样。

`>= 1`

示例：

`40`

**`maxOutputTokens`**

* 类型：`integer`
* 可选：是

模型生成响应的最大 token 数量。不同模型有不同的上限：

* `gemini-1.5-pro: 8192`
* `gemini-1.5-flash: 8192`
* `gemini-2.0-flash-exp: 8192`

`>= 1 <= 65536`

示例：

`2048`

**`candidateCount`**

* 类型：`integer`
* 可选：是

生成的候选响应数量。通常设为 1，设置更大值会增加成本。

`>= 1 <= 8`

默认值：

`1`

**`stopSequences`**

* 类型：`array[string]`
* 可选：是

自定义停止序列。当模型生成这些字符串之一时，会立即停止生成。

`<= 5 items`

**`responseMimeType`**

* 类型：`enum<string>`
* 可选：是

响应的 MIME 类型。

* `text/plain`：纯文本（默认）
* `application/json`：JSON 格式（需配合 `responseSchema` 使用）

枚举值：

* `text/plain`
* `application/json`

默认值：

`text/plain`

**`responseSchema`**

* 类型：`object`
* 可选：是

JSON Schema 格式的响应结构定义。当 `responseMimeType` 为 `application/json` 时使用，强制模型输出符合指定格式的 JSON。

**`presencePenalty`**

* 类型：`number`
* 可选：是

存在惩罚。正值会降低已出现 token 的概率，鼓励模型谈论新话题。

`>= -2 <= 2`

**`frequencyPenalty`**

* 类型：`number`
* 可选：是

频率惩罚。正值会根据 token 出现频率降低其概率，减少重复内容。

`>= -2 <= 2`

**`responseLogprobs`**

* 类型：`boolean`
* 可选：是

是否返回 logprobs（对数概率）。用于分析模型的置信度。

默认值：

`false`

**`logprobs`**

* 类型：`integer`
* 可选：是

返回的 top logprobs 数量（每个 token 位置返回概率最高的 N 个候选）。

`>= 0 <= 5`

**`seed`**

* 类型：`integer`
* 可选：是

随机种子。设置相同的种子可以获得更一致的输出（但不保证完全确定性）。

**`responseModalities`**

* 类型：`array[string]`
* 可选：是

响应模态。指定模型输出的类型。

枚举值：

* `TEXT`
* `IMAGE`
* `AUDIO`

示例：

`["TEXT"]`

**`thinkingConfig`**

* 类型：`object(Gemini Chat 请求体-ThinkingConfig)`
* 可选：是

Gemini Chat 请求体-ThinkingConfig

思考配置。启用后，模型会展示内部推理过程。

#### `safetySettings`

* 类型：`array[object (Gemini Chat 请求体-ChatSafetySettings)]`
* 可选：是

安全设置，用于控制内容过滤的严格程度。可以针对不同类别的有害内容设置不同的阈值。

**`category`**

* 类型：`enum<string>`
* 必需：是

有害内容类别：

* `HARASSMENT`：骚扰
* `HATE_SPEECH`：仇恨言论
* `SEXUALLY_EXPLICIT`：色情内容
* `DANGEROUS_CONTENT`：危险内容
* `CIVIC_INTEGRITY`：公民诚信

枚举值：

* `HARM_CATEGORY_HARASSMENT`
* `HARM_CATEGORY_HATE_SPEECH`
* `HARM_CATEGORY_SEXUALLY_EXPLICIT`
* `HARM_CATEGORY_DANGEROUS_CONTENT`
* `HARM_CATEGORY_CIVIC_INTEGRITY`

**`threshold`**

* 类型：`enum<string>`
* 必需：是

阻止阈值：

* `BLOCK_LOW_AND_ABOVE`：阻止低危及以上
* `BLOCK_MEDIUM_AND_ABOVE`：阻止中危及以上（推荐）
* `BLOCK_ONLY_HIGH`：只阻止高危
* `BLOCK_NONE`：不阻止（需谨慎使用）

枚举值：

* `HARM_BLOCK_THRESHOLD_UNSPECIFIED`
* `BLOCK_LOW_AND_ABOVE`
* `BLOCK_MEDIUM_AND_ABOVE`
* `BLOCK_ONLY_HIGH`
* `BLOCK_NONE`

#### `tools`

* 类型：`array[object (Gemini Chat 请求体-ChatTool)]`
* 可选：是

可用工具列表。支持函数调用、Google 搜索、代码执行等多种工具。

**`functionDeclarations`**

* 类型：`array[object]`
* 可选：是

函数声明列表。定义模型可以调用的函数。

**`googleSearch`**

* 类型：`object`
* 可选：是

Google 搜索工具（基础搜索）。

**`googleSearchRetrieval`**

* 类型：`object`
* 可选：是

Google 搜索检索工具（增强版，可以搜索实时信息并整合到回答中）。

**`codeExecution`**

* 类型：`object`
* 可选：是

代码执行工具。启用后，模型可以编写和运行 Python 代码来解决问题。

#### `toolConfig`

* 类型：`object(Gemini Chat 请求体-Config)`
* 可选：是

Gemini Chat 请求体-Config

工具配置，用于控制工具的调用行为（如强制调用、允许的函数列表等）。

**`functionCallingConfig`**

* 类型：`object(Gemini Chat 请求体-FunctionCallingConfig)`
* 可选：是

Gemini Chat 请求体-FunctionCallingConfig

函数调用配置。

#### `cachedContent`

* 类型：`string`
* 可选：是

缓存内容的名称。使用上下文缓存功能可以减少重复内容的处理，降低延迟和成本。

示例：

`cachedContents/abc123`

## 示例

### 最基础的对话示例

<pre class="language-json"><code class="lang-json"><strong>{
</strong>    "contents": [\
        {\
            "role": "user",\
            "parts": [\
                {\
                    "text": "你好，Gemini！请介绍一下你自己。"\
                }\
            ]\
        }\
    ]
}
</code></pre>

{% tabs %}
{% tab title="简单文本对话" %}
```json
{
    "contents": [
        {
            "role": "user",
            "parts": [
                {
                    "text": "你好，Gemini！请介绍一下你自己。"
                }
            ]
        }
    ]
}
```
{% endtab %}

{% tab title="多模态消息（带图片）" %}
```json
{
    "contents": [
        {
            "role": "user",
            "parts": [
                {
                    "text": "这张图片中有什么内容？请详细描述。"
                },
                {
                    "inlineData": {
                        "mimeType": "image/jpeg",
                        "data": "/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDAAgGBgcGBQgHBwcJCQgKDBQNDAsLDBkSEw8UHRofHh0aHBwgJC4nICIsIxwcKDcpLDAxNDQ0Hyc5PTgyPC4zNDL..."
                    }
                }
            ]
        }
    ],
    "generationConfig": {
        "maxOutputTokens": 2048
    }
}
```
{% endtab %}

{% tab title="多模态消息（带视频）" %}
```json
{
    "contents": [
        {
            "role": "user",
            "parts": [
                {
                    "text": "这个视频中发生了什么？"
                },
                {
                    "inlineData": {
                        "mimeType": "video/mp4",
                        "data": "AAAAIGZ0eXBpc29tAAACAGlzb21pc28yYXZjMW1wNDEAAAAIZnJlZQAAAs1tZGF0..."
                    }
                }
            ]
        }
    ]
}
```
{% endtab %}

{% tab title="多模态消息（带音频）" %}
```json
{
    "contents": [
        {
            "role": "user",
            "parts": [
                {
                    "text": "请转录这段音频的内容。"
                },
                {
                    "inlineData": {
                        "mimeType": "audio/mp3",
                        "data": "SUQzBAAAAAAAI1RTU0UAAAAPAAADTGF2ZjU4Ljc2LjEwMAAAAAAAAAAAAAAA//tQAAAAAAAAAAAA..."
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
    "contents": [
        {
            "role": "user",
            "parts": [
                {
                    "text": "请一步步分析并解决这个复杂的数学问题：如果有5个人参加会议，每个人都要和其他人握手一次，总共需要握手多少次？"
                }
            ]
        }
    ],
    "generationConfig": {
        "maxOutputTokens": 8192,
        "thinkingConfig": {
            "includeThoughts": true,
            "thinkingBudget": 5000
        }
    }
}
```
{% endtab %}
{% endtabs %}

## 请求示例代码

### cURL

### 最基础的对话示例

{% tabs %}
{% tab title="python" %}
{% code title="python request简单文本示例" %}
```python
import http.client
import json

conn = http.client.HTTPSConnection("router.shengsuanyun.com")
payload = json.dumps({
   "contents": [
      {
         "role": "user",
         "parts": [
            {
               "text": "你好，Gemini！请介绍一下你自己。"
            }
         ]
      }
   ]
})
headers = {
   'x-goog-api-key': '<api-key>',
   'Content-Type': 'application/json'
}
conn.request("POST", "/api/v1/models/:generateContent?key=null&alt=null", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))
```
{% endcode %}
{% endtab %}

{% tab title="js" %}
{% code title="js fetch多模态带图片示例" %}
```json
const myHeaders = new Headers();
myHeaders.append("x-goog-api-key", "<api-key>");
myHeaders.append("Content-Type", "application/json");

const raw = JSON.stringify({
   "contents": [
      {
         "role": "user",
         "parts": [
            {
               "text": "这张图片中有什么内容？请详细描述。"
            },
            {
               "inlineData": {
                  "mimeType": "image/jpeg",
                  "data": "/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDAAgGBgcGBQgHBwcJCQgKDBQNDAsLDBkSEw8UHRofHh0aHBwgJC4nICIsIxwcKDcpLDAxNDQ0Hyc5PTgyPC4zNDL..."
               }
            }
         ]
      }
   ],
   "generationConfig": {
      "maxOutputTokens": 2048
   }
});

const requestOptions = {
   method: "POST",
   headers: myHeaders,
   body: raw,
   redirect: "follow"
};

fetch("https://router.shengsuanyun.com/api/v1/models/:generateContent?key&alt", requestOptions)
   .then((response) => response.text())
   .then((result) => console.log(result))
   .catch((error) => console.error(error));
```
{% endcode %}
{% endtab %}

{% tab title="curl" %}
{% code title="curl shell 多模态带视频示例" %}
```shellscript
curl --location 'https://router.shengsuanyun.com/api/v1/models/:generateContent?key=undefined&alt=undefined' \
--header 'x-goog-api-key: <api-key>' \
--header 'Content-Type: application/json' \
--data '{
    "contents": [
        {
            "role": "user",
            "parts": [
                {
                    "text": "这个视频中发生了什么？"
                },
                {
                    "inlineData": {
                        "mimeType": "video/mp4",
                        "data": "AAAAIGZ0eXBpc29tAAACAGlzb21pc28yYXZjMW1wNDEAAAAIZnJlZQAAAs1tZGF0..."
                    }
                }
            ]
        }
    ]
}'
```
{% endcode %}
{% endtab %}

{% tab title="c" %}
{% code title="c 带思考过程示例" %}
```c
CURL *curl;
CURLcode res;
curl = curl_easy_init();
if(curl) {
   curl_easy_setopt(curl, CURLOPT_CUSTOMREQUEST, "POST");
   curl_easy_setopt(curl, CURLOPT_URL, "https://router.shengsuanyun.com/api/v1/models/:generateContent?key=undefined&alt=undefined");
   curl_easy_setopt(curl, CURLOPT_FOLLOWLOCATION, 1L);
   curl_easy_setopt(curl, CURLOPT_DEFAULT_PROTOCOL, "https");
   struct curl_slist *headers = NULL;
   headers = curl_slist_append(headers, "x-goog-api-key: <api-key>");
   headers = curl_slist_append(headers, "Content-Type: application/json");
   curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);
   const char *data = "{\n    \"contents\": [\n        {\n            \"role\": \"user\",\n            \"parts\": [\n                {\n                    \"text\": \"请一步步分析并解决这个复杂的数学问题：如果有5个人参加会议，每个人都要和其他人握手一次，总共需要握手多少次？\"\n                }\n            ]\n        }\n    ],\n    \"generationConfig\": {\n        \"maxOutputTokens\": 8192,\n        \"thinkingConfig\": {\n            \"includeThoughts\": true,\n            \"thinkingBudget\": 5000\n        }\n    }\n}";
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

### 🟢 200

`application/json`

成功响应 - 返回模型生成的内容

### Body `application/json`

#### `candidates`

* 类型：`array[object (Gemini Chat 响应-Candidate)]`
* 可选：是

候选响应列表。通常包含一个候选，但可以通过 `candidateCount` 生成多个。

**`content`**

* 类型：`object(Gemini Chat 请求体-Content)`
* 可选：是

Gemini Chat 请求体-Content

生成的内容。

**`finishReason`**

* 类型：`enum<string>`
* 可选：是

结束原因：

* `STOP`：模型自然结束
* `MAX_TOKENS`：达到最大 token 限制
* `SAFETY`：安全过滤器阻止
* `RECITATION`：检测到背诵内容
* `OTHER`：其他原因

枚举值：

* `FINISH_REASON_UNSPECIFIED`
* `STOP`
* `MAX_TOKENS`
* `SAFETY`
* `RECITATION`
* `OTHER`

**`index`**

* 类型：`integer`
* 可选：是

候选索引（从 0 开始）。

**`safetyRatings`**

* 类型：`array[object (Gemini Chat 响应-SafetyRating)]`
* 可选：是

安全评级。展示不同类别有害内容的概率。

#### `promptFeedback`

* 类型：`object(Gemini Chat 响应-PromptFeedback)`
* 可选：是

Gemini Chat 响应-PromptFeedback

提示词反馈。如果输入被安全过滤器阻止，会在这里说明原因。

**`blockReason`**

* 类型：`enum<string>`
* 可选：是

阻止原因（如果输入被阻止）。

枚举值：

* `BLOCK_REASON_UNSPECIFIED`
* `SAFETY`
* `OTHER`

**`safetyRatings`**

* 类型：`array[object (Gemini Chat 响应-SafetyRating)]`
* 可选：是

输入内容的安全评级。

#### `usageMetadata`

* 类型：`object(Gemini Chat 响应-UsageMetadata)`
* 可选：是

Gemini Chat 响应-UsageMetadata

Token 使用情况统计。

**`promptTokenCount`**

* 类型：`integer`
* 可选：是

提示词 token 数量（输入）。

**`cachedContentTokenCount`**

* 类型：`integer`
* 可选：是

缓存内容 token 数量（使用上下文缓存时）。

**`candidatesTokenCount`**

* 类型：`integer`
* 可选：是

所有候选响应的 token 数量（输出）。

**`totalTokenCount`**

* 类型：`integer`
* 可选：是

总 token 数量（`promptTokenCount + candidatesTokenCount`）。

**`thoughtsTokenCount`**

* 类型：`integer`
* 可选：是

思考过程的 token 数量（启用 `thinkingConfig` 时）。

### 示例

```json
{
    "candidates": [
        {
            "content": {
                "parts": [
                    {
                        "text": "你好！我是 Gemini，由 Google 开发的大型语言模型。我可以帮助你进行对话、回答问题、分析图片和视频、编写代码等多种任务。有什么我可以帮助你的吗？"
                    }
                ],
                "role": "model"
            },
            "finishReason": "STOP",
            "index": 0,
            "safetyRatings": [
                {
                    "category": "HARM_CATEGORY_SEXUALLY_EXPLICIT",
                    "probability": "NEGLIGIBLE"
                },
                {
                    "category": "HARM_CATEGORY_HATE_SPEECH",
                    "probability": "NEGLIGIBLE"
                },
                {
                    "category": "HARM_CATEGORY_HARASSMENT",
                    "probability": "NEGLIGIBLE"
                },
                {
                    "category": "HARM_CATEGORY_DANGEROUS_CONTENT",
                    "probability": "NEGLIGIBLE"
                }
            ]
        }
    ],
    "usageMetadata": {
        "promptTokenCount": 8,
        "candidatesTokenCount": 45,
        "totalTokenCount": 53
    }
}
```

### 🟠 400

请求错误 - 请求参数格式错误或缺少必需字段

### 🟠 401

认证失败 - API Key 无效或缺失

### 🟠 429

请求频率超限 - 超过了 API 调用速率限制

### 🔴 500

服务器内部错误
