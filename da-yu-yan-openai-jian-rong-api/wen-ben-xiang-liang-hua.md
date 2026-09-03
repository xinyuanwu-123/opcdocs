# 文本向量化

**POST** `/v1/embeddings`

将文本转换为向量表示。

## 请求参数

### Header 参数

* `HTTP-Referer` `string`可选
  * 用于在胜算云Router中排名的应用网站 URL
  * 示例：[`https://www.postman.com`](https://www.postman.com)&#x20;
* `X-Title` `string`可选
  * 用于在胜算云Router中排名的应用名称
  * 示例：`Postman`

### Body 参数 `application/json`

#### model

`string`必需

请求使用的模型 ID。

支持的模型ID：

* `openai/text-embedding-ada-002`
* `openai/text-embedding-3-small`
* `openai/text-embedding-3-large`
* `baai/bge-m3`
* `bytedance/doubao-embedding-large`
* `bytedance/doubao-embedding`

#### input

输入内容 必需

One of:

* `string`
* `array[string]`

#### encoding\_format

`string`可选

#### dimensions

`string`可选

#### seed

`number`可选

#### temperature

`number`可选

#### frequency\_penalty

`number`可选

#### presence\_penalty

`number`可选

## 示例

{% tabs %}
{% tab title="数组input" %}
```json
{
  "model": "ali/text-embedding-v3",
  "input": [
    "如何使用torchserve部署模型",
    "怎么使用训练机器学习模型"
  ]
}
```
{% endtab %}

{% tab title="字符串input" %}
```json
{
    "model": "ali/text-embedding-v3",
    "input": "如何使用torchserve部署模型"
}
```
{% endtab %}
{% endtabs %}

## 请求示例代码

{% tabs %}
{% tab title="python" %}
{% code title="数组input示例" %}
```python
import requests
import json

url = "https://router.shengsuanyun.com/api/v1/embeddings"

payload = json.dumps({
   "model": "ali/text-embedding-v3",
   "input": [
      "如何使用torchserve部署模型",
      "怎么使用训练机器学习模型"
   ]
})
headers = {
   'HTTP-Referer': 'https://www.postman.com',
   'X-Title': 'Postman',
   'Content-Type': 'application/json'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```
{% endcode %}
{% endtab %}

{% tab title="curl" %}
{% code title="curl shell 数组output示例" %}
```shellscript
curl --location 'https://router.shengsuanyun.com/api/v1/embeddings' \
--header 'HTTP-Referer: https://www.postman.com' \
--header 'X-Title: Postman' \
--header 'Content-Type: application/json' \
--data '{
    "model": "ali/text-embedding-v3",
    "input": [
        "如何使用torchserve部署模型",
        "怎么使用训练机器学习模型"
    ]
}'
```
{% endcode %}
{% endtab %}

{% tab title="json" %}
{% code title="js 字符串input示例" %}
```json
const myHeaders = new Headers();
myHeaders.append("HTTP-Referer", "https://www.postman.com");
myHeaders.append("X-Title", "Postman");
myHeaders.append("Content-Type", "application/json");

const raw = JSON.stringify({
   "model": "ali/text-embedding-v3",
   "input": "如何使用torchserve部署模型"
});

const requestOptions = {
   method: "POST",
   headers: myHeaders,
   body: raw,
   redirect: "follow"
};

fetch("https://router.shengsuanyun.com/api/v1/embeddings", requestOptions)
   .then((response) => response.text())
   .then((result) => console.log(result))
   .catch((error) => console.error(error));
```
{% endcode %}
{% endtab %}

{% tab title="c" %}
{% code title="c 字符串input示例" %}
```c
CURL *curl;
CURLcode res;
curl = curl_easy_init();
if(curl) {
   curl_easy_setopt(curl, CURLOPT_CUSTOMREQUEST, "POST");
   curl_easy_setopt(curl, CURLOPT_URL, "https://router.shengsuanyun.com/api/v1/embeddings");
   curl_easy_setopt(curl, CURLOPT_FOLLOWLOCATION, 1L);
   curl_easy_setopt(curl, CURLOPT_DEFAULT_PROTOCOL, "https");
   struct curl_slist *headers = NULL;
   headers = curl_slist_append(headers, "HTTP-Referer: https://www.postman.com");
   headers = curl_slist_append(headers, "X-Title: Postman");
   headers = curl_slist_append(headers, "Content-Type: application/json");
   curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);
   const char *data = "{\n    \"model\": \"ali/text-embedding-v3\",\n    \"input\": \"如何使用torchserve部署模型\"\n}";
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

### 🟢 200 成功

`application/json`

成功响应

#### Body `application/json`

**id**

`string`响应ID 可选

**provider**

`string`Provider名称 可选

**model**

`string`用户调用模型名称 可选

**object**

`string`对象类型 可选

**created**

`integer`创建时间戳可选

**data**

`array[object (OpenAI Embedding 响应- Item)]`返回消息 可选

**item 对象字段**

**object**

`string`对象类型 可选

**index**

`integer`条目索引 可选

**embedding**

`array[number]`嵌入向量 可选

**usage**

`object (OpenAI Chat 响应- Usage)`用量 可选

**prompt\_tokens**

`integer`提示词 token 数 可选

**completion\_tokens**

`integer`完成 token 数 可选

**total\_tokens**

`integer`总 token 数 可选

### 示例

```json
{
  "id": "202504101500559418281XA86wFOO",
  "provider": "Ali",
  "model": "ali/text-embedding-v3",
  "object": "list",
  "created": 1744268455,
  "data": [
    {
      "object": "embedding",
      "index": 0,
      "embedding": [
        -0.01420350931584835,
        -0.009542533196508884,
        -0.046993378549814224,
        0.0012923178728669882,
        -0.03646302595734596,
        ...
      ]
    },
    {
      "object": "embedding",
      "index": 1,
      "embedding": [
        -0.06518170237541199,
        -0.04570785164833069,
        -0.06909951567649841,
        -0.02899952046573162,
        -0.07002135366201401,
        ...
      ]
    }
  ],
  "usage": {
    "prompt_tokens": 13,
    "total_tokens": 13
  }
}
```

### 🟠 400 请求有误
