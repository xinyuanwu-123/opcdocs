# 同步图像编辑

**POST** `/v1/images/edits`

部分图像生成、图像编辑模型使用同步接口。生成结果会实时返回，保留上游供应商格式。

## 请求参数

### Authorization

在 Header 添加参数 `Authorization`，其值为在 Bearer 之后拼接 Token。

示例：

`Authorization: Bearer ********************`

### Header 参数

* **Content-Type**\
  `string`，必需\
  示例：`application/json`

### Body 参数 `application/json`

* **model**\
  `string`，必需\
  支持同步模式的模型 id
*   **prompt**\
    `string`，可选

    图片生成提示词
* **others**\
  `string`，必需\
  每个模型都有各自特有的请求参数，可以从模型特有参数文档中获取

### 示例

使用基本参数生成图像

```json
{
    "model": "bytedance/doubao-seedream-4.0",
    "prompt": "生成一只在阳光下奔跑的金毛犬，背景是油菜花田",
    "size": "1280x720",
    "watermark": false
}
```

## 请求示例代码

{% tabs %}
{% tab title="python" %}
{% code title="python request 示例" %}
```python
import requests
import json

url = "https://router.shengsuanyun.com/api/v1/images/edits"

payload = json.dumps({
   "model": "bytedance/doubao-seedream-4.0",
   "prompt": "生成一只在阳光下奔跑的金毛犬，背景是油菜花田",
   "size": "1280x720",
   "watermark": False
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
{% code title="js fetch示例" %}
```json
const myHeaders = new Headers();
myHeaders.append("Authorization", "Bearer <token>");
myHeaders.append("Content-Type", "application/json");

const raw = JSON.stringify({
   "model": "bytedance/doubao-seedream-4.0",
   "prompt": "生成一只在阳光下奔跑的金毛犬，背景是油菜花田",
   "size": "1280x720",
   "watermark": false
});

const requestOptions = {
   method: "POST",
   headers: myHeaders,
   body: raw,
   redirect: "follow"
};

fetch("https://router.shengsuanyun.com/api/v1/images/edits", requestOptions)
   .then((response) => response.text())
   .then((result) => console.log(result))
   .catch((error) => console.error(error));
```
{% endcode %}
{% endtab %}

{% tab title="curl" %}
{% code title="curl shell 示例" %}
```shellscript
curl --location 'https://router.shengsuanyun.com/api/v1/images/edits' \
--header 'Authorization: Bearer <token>' \
--header 'Content-Type: application/json' \
--data '{
    "model": "bytedance/doubao-seedream-4.0",
    "prompt": "生成一只在阳光下奔跑的金毛犬，背景是油菜花田",
    "size": "1280x720",
    "watermark": false
}'
```
{% endcode %}
{% endtab %}

{% tab title="c" %}
{% code title="c 示例" %}
```c
CURL *curl;
CURLcode res;
curl = curl_easy_init();
if(curl) {
   curl_easy_setopt(curl, CURLOPT_CUSTOMREQUEST, "POST");
   curl_easy_setopt(curl, CURLOPT_URL, "https://router.shengsuanyun.com/api/v1/images/edits");
   curl_easy_setopt(curl, CURLOPT_FOLLOWLOCATION, 1L);
   curl_easy_setopt(curl, CURLOPT_DEFAULT_PROTOCOL, "https");
   struct curl_slist *headers = NULL;
   headers = curl_slist_append(headers, "Authorization: Bearer <token>");
   headers = curl_slist_append(headers, "Content-Type: application/json");
   curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);
   const char *data = "{\n    \"model\": \"bytedance/doubao-seedream-4.0\",\n    \"prompt\": \"生成一只在阳光下奔跑的金毛犬，背景是油菜花田\",\n    \"size\": \"1280x720\",\n    \"watermark\": false\n}";
   curl_easy_setopt(curl, CURLOPT_POSTFIELDS, data);
   res = curl_easy_perform(curl);
   curl_slist_free_all(headers);
}
curl_easy_cleanup(curl);
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Shell

## 返回响应

### 🟢 200 成功

`application/json`

图像生成成功

**Body `application/json`**

* **body**\
  `string`，必需\
  每个模型完全使用上游返回格式，可在各自模型的特定参数文档中查看

```json
{
    "body": "string"
}
```

### 🟢 200 流式响应

`text/event-stream`\
流式生成成功\
**Body**`text/event-stream`\
SSE 封装结构\
No schema defined

### 🟠 400 请求有误

### 🟠 401 未认证

### 🔴 500 服务器内部错误
