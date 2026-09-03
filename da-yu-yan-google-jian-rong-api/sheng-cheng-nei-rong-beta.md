# 生成内容（Beta）

**POST** `/v1beta/models/{model}:generateContent`

使用 Gemini 模型生成内容（Beta 版本，支持最新实验性功能）

## 请求参数

### Authorization

#### API Key

在 query 添加参数 `key`

示例：

`key: ********************`

#### API Key

在 header 添加参数 `x-goog-api-key`

示例：

`x-goog-api-key: ********************`

### Path 参数

#### model

`string`\
必需

模型名称（Beta 版本可能包含实验性模型）

示例：

`google/gemini-3-pro-preview`

### Query 参数

#### key

`string`\
可选

Google AI Studio API Key

#### alt

`enum<string>`\
可选

响应格式

枚举值：

* `json`
* `sse`

默认值：`json`

### Body 参数 `application/json` 必填

#### contents

`array[object (Gemini Chat 请求体-Content)]`\
必需

对话内容列表，按时间顺序排列。每条消息包含角色和内容部分（parts）

* `role`
  * `enum<string>`\
    可选
  * 消息角色：
    * `user`: 用户发送的消息
    * `model`: 模型（Gemini）的回复
  * 注意：`systemInstruction` 中不需要指定 `role`
* `parts`
  * `array[object (Gemini Chat 请求体-Part)]`\
    必需
  * 内容部分列表。一条消息可以包含多个部分（文本、图片、音频、视频、函数调用等）

#### systemInstruction

`object(Gemini Chat 请求体-Content)`\
可选

系统指令，用于设置模型的角色、行为准则、背景知识等。在对话开始前生效

* role
  * `enum<string>`\
    可选
  * 消息角色：
    * `user`: 用户发送的消息
    * `model`: 模型（Gemini）的回复
  * 注意：`systemInstruction` 中不需要指定 `role`
* parts
  * `array[object (Gemini Chat 请求体-Part)]`\
    必需
  * 内容部分列表。一条消息可以包含多个部分（文本、图片、音频、视频、函数调用等）

#### generationConfig

`object(Gemini Chat 请求体-ChatGenerationConfig)`\
可选

生成配置，控制输出的特性（温度、最大长度、输出格式等）

* temperature
  * `number`\
    可选
  * 采样温度，控制输出的随机性。
  * 0.0: 几乎确定性输出
  * 1.0: 默认值，平衡创造性和一致性
  * 2.0: 最大值，输出更随机
  * 使用场景：创意写作用高温度，数据分析用低温度
  * 范围：`0` 到 `2`
  * 示例：`0.9`
* topP
  * `number`\
    可选
  * Nucleus sampling 参数。模型会从累积概率达到 `topP` 的最小 token 集合中采样
  * 范围：`0` 到 `1`
  * 示例：`1`
* topK
  * `number`\
    可选
  * Top-K sampling 参数。模型只从概率最高的 K 个 token 中采样
  * 范围：`>= 1`
  * 示例：`40`
* maxOutputTokens
  * `integer`\
    可选
  * 模型生成响应的最大 token 数量。不同模型有不同的上限：
    * `gemini-1.5-pro`: 8192
    * `gemini-1.5-flash`: 8192
    * `gemini-2.0-flash-exp`: 8192
  * 范围：`1` 到 `65536`
  * 示例：`2048`
* candidateCount
  * `integer`\
    可选
  * 生成的候选响应数量。通常设为 1，设置更大值会增加成本
  * 范围：`1` 到 `8`
  * 默认值：`1`
* stopSequences
  * `array[string]`\
    可选
  * 自定义停止序列。当模型生成这些字符串之一时，会立即停止生成
  * 最多 `5` 项
* responseMimeType
  * `enum<string>`\
    可选
  * 响应的 MIME 类型。
  * `text/plain`: 纯文本（默认）
  * `application/json`: JSON 格式（需配合 `responseSchema` 使用）
  * 枚举值：
    * `text/plain`
    * `application/json`
  * 默认值：`text/plain`
* responseSchema
  * `object`\
    可选
  * JSON Schema 格式的响应结构定义。当 `responseMimeType` 为 `application/json` 时使用，强制模型输出符合指定格式的 JSON
* presencePenalty
  * `number`\
    可选
  * 存在惩罚。正值会降低已出现 token 的概率，鼓励模型谈论新话题
  * 范围：`-2` 到 `2`
* frequencyPenalty
  * `number`\
    可选
  * 频率惩罚。正值会根据 token 出现频率降低其概率，减少重复内容
  * 范围：`-2` 到 `2`
* responseLogprobs
  * `boolean`\
    可选
  * 是否返回 logprobs（对数概率）。用于分析模型的置信度
  * 默认值：`false`
* logprobs
  * `integer`\
    可选
  * 返回的 top logprobs 数量（每个 token 位置返回概率最高的 N 个候选）
  * 范围：`0` 到 `5`
* seed
  * `integer`\
    可选
  * 随机种子。设置相同的种子可以获得更一致的输出（但不保证完全确定性）
* responseModalities
  * `array[string]`\
    可选
  * 响应模态。指定模型输出的类型
  * 枚举值：
    * `TEXT`
    * `IMAGE`
    * `AUDIO`
  * 示例：`["TEXT"]`
* thinkingConfig
  * `object(Gemini Chat 请求体-ThinkingConfig)`\
    可选
  * 思考配置。启用后，模型会展示内部推理过程

#### safetySettings

`array[object (Gemini Chat 请求体-ChatSafetySettings)]`\
可选

安全设置，用于控制内容过滤的严格程度。可以针对不同类别的有害内容设置不同的阈值

* category
  * `enum<string>`\
    必需
  * 有害内容类别：
    * `HARASSMENT`: 骚扰
    * `HATE_SPEECH`: 仇恨言论
    * `SEXUALLY_EXPLICIT`: 色情内容
    * `DANGEROUS_CONTENT`: 危险内容
    * `CIVIC_INTEGRITY`: 公民诚信
  * 枚举值：
    * `HARM_CATEGORY_HARASSMENT`
    * `HARM_CATEGORY_HATE_SPEECH`
    * `HARM_CATEGORY_SEXUALLY_EXPLICIT`
    * `HARM_CATEGORY_DANGEROUS_CONTENT`
    * `HARM_CATEGORY_CIVIC_INTEGRITY`
* threshold
  * `enum<string>`\
    必需
  * 阻止阈值：
    * `BLOCK_LOW_AND_ABOVE`: 阻止低危及以上
    * `BLOCK_MEDIUM_AND_ABOVE`: 阻止中危及以上（推荐）
    * `BLOCK_ONLY_HIGH`: 只阻止高危
    * `BLOCK_NONE`: 不阻止（需谨慎使用）
  * 枚举值：
    * `HARM_BLOCK_THRESHOLD_UNSPECIFIED`
    * `BLOCK_LOW_AND_ABOVE`
    * `BLOCK_MEDIUM_AND_ABOVE`
    * `BLOCK_ONLY_HIGH`
    * `BLOCK_NONE`

#### tools

`array[object (Gemini Chat 请求体-ChatTool)]`\
可选

可用工具列表。支持函数调用、Google 搜索、代码执行等多种工具

* functionDeclarations
  * `array [object]`\
    可选
  * 函数声明列表。定义模型可以调用的函数
* googleSearch
  * `object`\
    可选
  * Google 搜索工具（基础搜索）
* googleSearchRetrieval
  * `object`\
    可选
  * Google 搜索检索工具（增强版，可以搜索实时信息并整合到回答中）
* codeExecution
  * `object`\
    可选
  * 代码执行工具。启用后，模型可以编写和运行 Python 代码来解决问题

#### toolConfig

`object(Gemini Chat 请求体-Config)`\
可选

工具配置，用于控制工具的调用行为（如强制调用、允许的函数列表等）

* functionCallingConfig
  * `object(Gemini Chat 请求体-FunctionCallingConfig)`\
    可选
  * 函数调用配置

#### cachedContent

`string`\
可选

缓存内容的名称。使用上下文缓存功能可以减少重复内容的处理，降低延迟和成本

示例：

`cachedContents/abc123`

## 请求示例代码

{% tabs %}
{% tab title="python" %}
{% code title="python request示例" %}
```python
import requests
import json

url = "https://router.shengsuanyun.com/api/v1beta/models/:generateContent?key=<api-key>&alt"

payload = json.dumps({
   "contents": [
      {
         "role": "user",
         "parts": [
            {
               "text": "string",
               "inlineData": {
                  "mimeType": "image/jpeg",
                  "data": "string"
               },
               "fileData": {
                  "mimeType": "string",
                  "fileUri": "https://generativelanguage.googleapis.com/v1beta/files/abc123"
               },
               "functionCall": {
                  "name": "string",
                  "args": {}
               },
               "functionResponse": {
                  "name": "string",
                  "response": {}
               },
               "thought": True,
               "executableCode": {
                  "language": "PYTHON",
                  "code": "string"
               },
               "codeExecutionResult": {
                  "outcome": "OUTCOME_OK",
                  "output": "string"
               }
            }
         ]
      }
   ],
   "systemInstruction": {
      "role": "user",
      "parts": [
         {
            "text": "string",
            "inlineData": {
               "mimeType": "image/jpeg",
               "data": "string"
            },
            "fileData": {
               "mimeType": "string",
               "fileUri": "https://generativelanguage.googleapis.com/v1beta/files/abc123"
            },
            "functionCall": {
               "name": "string",
               "args": {}
            },
            "functionResponse": {
               "name": "string",
               "response": {}
            },
            "thought": True,
            "executableCode": {
               "language": "PYTHON",
               "code": "string"
            },
            "codeExecutionResult": {
               "outcome": "OUTCOME_OK",
               "output": "string"
            }
         }
      ]
   },
   "generationConfig": {
      "temperature": 0.9,
      "topP": 1,
      "topK": 40,
      "maxOutputTokens": 2048,
      "candidateCount": 1,
      "stopSequences": [
         "string"
      ],
      "responseMimeType": "text/plain",
      "responseSchema": {},
      "presencePenalty": -2,
      "frequencyPenalty": -2,
      "responseLogprobs": False,
      "logprobs": 0,
      "seed": 0,
      "responseModalities": [
         "TEXT"
      ],
      "thinkingConfig": {
         "includeThoughts": False,
         "thinkingBudget": 5000
      }
   },
   "safetySettings": [
      {
         "category": "HARM_CATEGORY_HARASSMENT",
         "threshold": "HARM_BLOCK_THRESHOLD_UNSPECIFIED"
      }
   ],
   "tools": [
      {
         "functionDeclarations": [
            {
               "name": "string",
               "description": "string",
               "parameters": {}
            }
         ],
         "googleSearch": {},
         "googleSearchRetrieval": {},
         "codeExecution": {}
      }
   ],
   "toolConfig": {
      "functionCallingConfig": {
         "mode": "AUTO",
         "allowedFunctionNames": [
            "string"
         ]
      }
   },
   "cachedContent": "cachedContents/abc123"
})
headers = {
   'Content-Type': 'application/json'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```
{% endcode %}
{% endtab %}

{% tab title="js" %}
{% code title="js fetch示例" %}
```json
const myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

const raw = JSON.stringify({
   "contents": [
      {
         "role": "user",
         "parts": [
            {
               "text": "string",
               "inlineData": {
                  "mimeType": "image/jpeg",
                  "data": "string"
               },
               "fileData": {
                  "mimeType": "string",
                  "fileUri": "https://generativelanguage.googleapis.com/v1beta/files/abc123"
               },
               "functionCall": {
                  "name": "string",
                  "args": {}
               },
               "functionResponse": {
                  "name": "string",
                  "response": {}
               },
               "thought": true,
               "executableCode": {
                  "language": "PYTHON",
                  "code": "string"
               },
               "codeExecutionResult": {
                  "outcome": "OUTCOME_OK",
                  "output": "string"
               }
            }
         ]
      }
   ],
   "systemInstruction": {
      "role": "user",
      "parts": [
         {
            "text": "string",
            "inlineData": {
               "mimeType": "image/jpeg",
               "data": "string"
            },
            "fileData": {
               "mimeType": "string",
               "fileUri": "https://generativelanguage.googleapis.com/v1beta/files/abc123"
            },
            "functionCall": {
               "name": "string",
               "args": {}
            },
            "functionResponse": {
               "name": "string",
               "response": {}
            },
            "thought": true,
            "executableCode": {
               "language": "PYTHON",
               "code": "string"
            },
            "codeExecutionResult": {
               "outcome": "OUTCOME_OK",
               "output": "string"
            }
         }
      ]
   },
   "generationConfig": {
      "temperature": 0.9,
      "topP": 1,
      "topK": 40,
      "maxOutputTokens": 2048,
      "candidateCount": 1,
      "stopSequences": [
         "string"
      ],
      "responseMimeType": "text/plain",
      "responseSchema": {},
      "presencePenalty": -2,
      "frequencyPenalty": -2,
      "responseLogprobs": false,
      "logprobs": 0,
      "seed": 0,
      "responseModalities": [
         "TEXT"
      ],
      "thinkingConfig": {
         "includeThoughts": false,
         "thinkingBudget": 5000
      }
   },
   "safetySettings": [
      {
         "category": "HARM_CATEGORY_HARASSMENT",
         "threshold": "HARM_BLOCK_THRESHOLD_UNSPECIFIED"
      }
   ],
   "tools": [
      {
         "functionDeclarations": [
            {
               "name": "string",
               "description": "string",
               "parameters": {}
            }
         ],
         "googleSearch": {},
         "googleSearchRetrieval": {},
         "codeExecution": {}
      }
   ],
   "toolConfig": {
      "functionCallingConfig": {
         "mode": "AUTO",
         "allowedFunctionNames": [
            "string"
         ]
      }
   },
   "cachedContent": "cachedContents/abc123"
});

const requestOptions = {
   method: "POST",
   headers: myHeaders,
   body: raw,
   redirect: "follow"
};

fetch("https://router.shengsuanyun.com/api/v1beta/models/:generateContent?key=<api-key>&alt", requestOptions)
   .then((response) => response.text())
   .then((result) => console.log(result))
   .catch((error) => console.error(error));
```
{% endcode %}
{% endtab %}

{% tab title="curl" %}
{% code title="curl shell示例" %}
```shellscript
curl --location 'https://router.shengsuanyun.com/api/v1beta/models/:generateContent?key=%3Capi-key%3E&alt=undefined' \
--header 'Content-Type: application/json' \
--data '{
    "contents": [
        {
            "role": "user",
            "parts": [
                {
                    "text": "string",
                    "inlineData": {
                        "mimeType": "image/jpeg",
                        "data": "string"
                    },
                    "fileData": {
                        "mimeType": "string",
                        "fileUri": "https://generativelanguage.googleapis.com/v1beta/files/abc123"
                    },
                    "functionCall": {
                        "name": "string",
                        "args": {}
                    },
                    "functionResponse": {
                        "name": "string",
                        "response": {}
                    },
                    "thought": true,
                    "executableCode": {
                        "language": "PYTHON",
                        "code": "string"
                    },
                    "codeExecutionResult": {
                        "outcome": "OUTCOME_OK",
                        "output": "string"
                    }
                }
            ]
        }
    ],
    "systemInstruction": {
        "role": "user",
        "parts": [
            {
                "text": "string",
                "inlineData": {
                    "mimeType": "image/jpeg",
                    "data": "string"
                },
                "fileData": {
                    "mimeType": "string",
                    "fileUri": "https://generativelanguage.googleapis.com/v1beta/files/abc123"
                },
                "functionCall": {
                    "name": "string",
                    "args": {}
                },
                "functionResponse": {
                    "name": "string",
                    "response": {}
                },
                "thought": true,
                "executableCode": {
                    "language": "PYTHON",
                    "code": "string"
                },
                "codeExecutionResult": {
                    "outcome": "OUTCOME_OK",
                    "output": "string"
                }
            }
        ]
    },
    "generationConfig": {
        "temperature": 0.9,
        "topP": 1,
        "topK": 40,
        "maxOutputTokens": 2048,
        "candidateCount": 1,
        "stopSequences": [
            "string"
        ],
        "responseMimeType": "text/plain",
        "responseSchema": {},
        "presencePenalty": -2,
        "frequencyPenalty": -2,
        "responseLogprobs": false,
        "logprobs": 0,
        "seed": 0,
        "responseModalities": [
            "TEXT"
        ],
        "thinkingConfig": {
            "includeThoughts": false,
            "thinkingBudget": 5000
        }
    },
    "safetySettings": [
        {
            "category": "HARM_CATEGORY_HARASSMENT",
            "threshold": "HARM_BLOCK_THRESHOLD_UNSPECIFIED"
        }
    ],
    "tools": [
        {
            "functionDeclarations": [
                {
                    "name": "string",
                    "description": "string",
                    "parameters": {}
                }
            ],
            "googleSearch": {},
            "googleSearchRetrieval": {},
            "codeExecution": {}
        }
    ],
    "toolConfig": {
        "functionCallingConfig": {
            "mode": "AUTO",
            "allowedFunctionNames": [
                "string"
            ]
        }
    },
    "cachedContent": "cachedContents/abc123"
}'
```
{% endcode %}
{% endtab %}

{% tab title="c" %}
{% code title="c示例" %}
```c
CURL *curl;
CURLcode res;
curl = curl_easy_init();
if(curl) {
   curl_easy_setopt(curl, CURLOPT_CUSTOMREQUEST, "POST");
   curl_easy_setopt(curl, CURLOPT_URL, "https://router.shengsuanyun.com/api/v1beta/models/:generateContent?key=%3Capi-key%3E&alt=undefined");
   curl_easy_setopt(curl, CURLOPT_FOLLOWLOCATION, 1L);
   curl_easy_setopt(curl, CURLOPT_DEFAULT_PROTOCOL, "https");
   struct curl_slist *headers = NULL;
   headers = curl_slist_append(headers, "Content-Type: application/json");
   curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);
   const char *data = "{\n    \"contents\": [\n        {\n            \"role\": \"user\",\n            \"parts\": [\n                {\n                    \"text\": \"string\",\n                    \"inlineData\": {\n                        \"mimeType\": \"image/jpeg\",\n                        \"data\": \"string\"\n                    },\n                    \"fileData\": {\n                        \"mimeType\": \"string\",\n                        \"fileUri\": \"https://generativelanguage.googleapis.com/v1beta/files/abc123\"\n                    },\n                    \"functionCall\": {\n                        \"name\": \"string\",\n                        \"args\": {}\n                    },\n                    \"functionResponse\": {\n                        \"name\": \"string\",\n                        \"response\": {}\n                    },\n                    \"thought\": true,\n                    \"executableCode\": {\n                        \"language\": \"PYTHON\",\n                        \"code\": \"string\"\n                    },\n                    \"codeExecutionResult\": {\n                        \"outcome\": \"OUTCOME_OK\",\n                        \"output\": \"string\"\n                    }\n                }\n            ]\n        }\n    ],\n    \"systemInstruction\": {\n        \"role\": \"user\",\n        \"parts\": [\n            {\n                \"text\": \"string\",\n                \"inlineData\": {\n                    \"mimeType\": \"image/jpeg\",\n                    \"data\": \"string\"\n                },\n                \"fileData\": {\n                    \"mimeType\": \"string\",\n                    \"fileUri\": \"https://generativelanguage.googleapis.com/v1beta/files/abc123\"\n                },\n                \"functionCall\": {\n                    \"name\": \"string\",\n                    \"args\": {}\n                },\n                \"functionResponse\": {\n                    \"name\": \"string\",\n                    \"response\": {}\n                },\n                \"thought\": true,\n                \"executableCode\": {\n                    \"language\": \"PYTHON\",\n                    \"code\": \"string\"\n                },\n                \"codeExecutionResult\": {\n                    \"outcome\": \"OUTCOME_OK\",\n                    \"output\": \"string\"\n                }\n            }\n        ]\n    },\n    \"generationConfig\": {\n        \"temperature\": 0.9,\n        \"topP\": 1,\n        \"topK\": 40,\n        \"maxOutputTokens\": 2048,\n        \"candidateCount\": 1,\n        \"stopSequences\": [\n            \"string\"\n        ],\n        \"responseMimeType\": \"text/plain\",\n        \"responseSchema\": {},\n        \"presencePenalty\": -2,\n        \"frequencyPenalty\": -2,\n        \"responseLogprobs\": false,\n        \"logprobs\": 0,\n        \"seed\": 0,\n        \"responseModalities\": [\n            \"TEXT\"\n        ],\n        \"thinkingConfig\": {\n            \"includeThoughts\": false,\n            \"thinkingBudget\": 5000\n        }\n    },\n    \"safetySettings\": [\n        {\n            \"category\": \"HARM_CATEGORY_HARASSMENT\",\n            \"threshold\": \"HARM_BLOCK_THRESHOLD_UNSPECIFIED\"\n        }\n    ],\n    \"tools\": [\n        {\n            \"functionDeclarations\": [\n                {\n                    \"name\": \"string\",\n                    \"description\": \"string\",\n                    \"parameters\": {}\n                }\n            ],\n            \"googleSearch\": {},\n            \"googleSearchRetrieval\": {},\n            \"codeExecution\": {}\n        }\n    ],\n    \"toolConfig\": {\n        \"functionCallingConfig\": {\n            \"mode\": \"AUTO\",\n            \"allowedFunctionNames\": [\n                \"string\"\n            ]\n        }\n    },\n    \"cachedContent\": \"cachedContents/abc123\"\n}";
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

成功响应

#### Body `application/json`

* candidates
  * `array[object (Gemini Chat 响应-Candidate)]`\
    可选
  * 候选响应列表。通常包含一个候选，但可以通过 `candidateCount` 生成多个
  * content
    * `object(Gemini Chat 请求体-Content)`\
      可选
    * 生成的内容
  * finishReason
    * `enum<string>`\
      可选
    * 结束原因：
      * `STOP`: 模型自然结束
      * `MAX_TOKENS`: 达到最大 token 限制
      * `SAFETY`: 安全过滤器阻止
      * `RECITATION`: 检测到背诵内容
      * `OTHER`: 其他原因
    * 枚举值：
      * `FINISH_REASON_UNSPECIFIED`
      * `STOP`
      * `MAX_TOKENS`
      * `SAFETY`
      * `RECITATION`
      * `OTHER`
  * index
    * `integer`\
      可选
    * 候选索引（从 0 开始）
  * safetyRatings
    * `array[object (Gemini Chat 响应-SafetyRating)]`\
      可选
    * 安全评级。展示不同类别有害内容的概率
* promptFeedback
  * `object(Gemini Chat 响应-PromptFeedback)`\
    可选
  * 提示词反馈。如果输入被安全过滤器阻止，会在这里说明原因
  * blockReason
    * `enum<string>`\
      可选
    * 阻止原因（如果输入被阻止）
    * 枚举值：
      * `BLOCK_REASON_UNSPECIFIED`
      * `SAFETY`
      * `OTHER`
  * safetyRatings
    * `array[object (Gemini Chat 响应-SafetyRating)]`\
      可选
    * 输入内容的安全评级
* usageMetadata
  * `object(Gemini Chat 响应-UsageMetadata)`\
    可选
  * Token 使用情况统计
  * promptTokenCount
    * `integer`\
      可选
    * 提示词 token 数量（输入）
  * cachedContentTokenCount
    * `integer`\
      可选
    * 缓存内容 token 数量（使用上下文缓存时）
  * candidatesTokenCount
    * `integer`\
      可选
    * 所有候选响应的 token 数量（输出）
  * totalTokenCount
    * `integer`\
      可选
    * 总 token 数量（`promptTokenCount + candidatesTokenCount`）
  * thoughtsTokenCount
    * `integer`\
      可选
    * 思考过程的 token 数量（启用 `thinkingConfig` 时）

### 示例

```json
{
    "candidates": [
        {
            "content": {
                "role": "user",
                "parts": [
                    {
                        "text": "string",
                        "inlineData": {
                            "mimeType": "image/jpeg",
                            "data": "string"
                        },
                        "fileData": {
                            "mimeType": "string",
                            "fileUri": "https://generativelanguage.googleapis.com/v1beta/files/abc123"
                        },
                        "functionCall": {
                            "name": "string",
                            "args": {}
                        },
                        "functionResponse": {
                            "name": "string",
                            "response": {}
                        },
                        "thought": true,
                        "executableCode": {
                            "language": "PYTHON",
                            "code": "string"
                        },
                        "codeExecutionResult": {
                            "outcome": "OUTCOME_OK",
                            "output": "string"
                        }
                    }
                ]
            },
            "finishReason": "FINISH_REASON_UNSPECIFIED",
            "index": 0,
            "safetyRatings": [
                {
                    "category": "string",
                    "probability": "NEGLIGIBLE"
                }
            ]
        }
    ],
    "promptFeedback": {
        "blockReason": "BLOCK_REASON_UNSPECIFIED",
        "safetyRatings": [
            {
                "category": "string",
                "probability": "NEGLIGIBLE"
            }
        ]
    },
    "usageMetadata": {
        "promptTokenCount": 0,
        "cachedContentTokenCount": 0,
        "candidatesTokenCount": 0,
        "totalTokenCount": 0,
        "thoughtsTokenCount": 0
    }
}
```
