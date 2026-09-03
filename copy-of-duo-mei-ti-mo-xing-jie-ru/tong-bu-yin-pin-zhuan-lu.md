# 同步音频转录

**POST** `/v1/audio/transcriptions`

上传音频（外部 URL）并同步获取转录结果，输出格式由 `response_format` 控制。

## 请求参数

### Authorization

在 Header 添加参数 `Authorization`，其值为在 Bearer 之后拼接 Token。

示例：

```
Authorization: Bearer ********************
```

### Body 参数

`application/json`

* file `string<uri>` **必需**
  * 音频文件的外部地址（URL），必须是可访问的资源。
  * 支持的音频格式：flac、mp3、mp4、mpeg、mpga、m4a、ogg、wav、webm。
  * 请使用可公开访问或带签名的 OSS 链接。
* model `enum<string>` **必需**
  * 要使用的模型 ID。
  * 枚举值：`gpt-4o-transcribe`、`gpt-4o-mini-transcribe`、`whisper-1`
* chunking\_strategy **可选**
  * 控制音频如何切分为多个片段：可以是字符串 `"auto"`（服务器自动使用 VAD）或手动对象（启用 `server_vad`）。
  * One of:
    * `string`
    * `object`
  * 自动策略：服务器先归一化响度，然后使用 VAD 自动选择边界。
  * 值：`auto`
* language `string` **可选**
  * 输入音频的语言，使用 ISO-639-1 代码（例如：en）。
  * 提供语言可提高准确性与速度（可选）。
* prompt `string` **可选**
  * 可选的文本提示，用于指导转录风格或延续之前的音频片段。
  * 提示应与音频语言一致。
* response\_format `enum<string>` **可选**
  * 转录输出格式。可选值：`json`、`text`、`srt`、`vtt`、`verbose_json`。
  * 注：`gpt-4o-transcribe` 和 `gpt-4o-mini-transcribe` 仅支持 `json`；`whisper-1` 支持全部格式。
  * 服务端应校验模型与格式的兼容性。
  * 枚举值：`json`、`text`、`srt`、`vtt`、`verbose_json`
  * 默认值：`json`
* temperature `number<float>` **可选**
  * 采样温度，取值范围 0 到 1。
  * 较高值（如 0.8）会使输出更随机，较低值（如 0.2）更确定性。
  * 设置为 0 时，模型会基于对数概率自动在一定阈值下提升温度以避免空结果（服务器端可按策略实现）。
  * 范围：`>= 0` `<= 1`
  * 默认值：`0`
* timestamp\_granularities `array[string]` **可选**
  * 请求返回时间戳的粒度。可选 `segment`（分段时间戳）或 `word`（逐词时间戳）。
  * 注意：生成 word 级别时间戳需要较高计算量且仅在 `response_format=verbose_json` 时有效，会增加延迟。
  * 默认行为为 `segment`。
  * 枚举值：`segment`、`word`

示例：

```json
{
  "file": "https://example-bucket.oss-cn-beijing.aliyuncs.com/audio/input-01.mp3",
  "model": "whisper-1",
  "response_format": "text",
  "language": "en"
}
```

## 请求示例代码

{% tabs %}
{% tab title="python" %}
{% code title="python request 示例" %}
```python
import requests
import json

url = "https://router.shengsuanyun.com/api/v1/audio/transcriptions"

payload = json.dumps({
   "file": "https://example-bucket.oss-cn-beijing.aliyuncs.com/audio/input-01.mp3",
   "model": "whisper-1",
   "response_format": "text",
   "language": "en"
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
   "file": "https://example-bucket.oss-cn-beijing.aliyuncs.com/audio/input-01.mp3",
   "model": "whisper-1",
   "response_format": "text",
   "language": "en"
});

const requestOptions = {
   method: "POST",
   headers: myHeaders,
   body: raw,
   redirect: "follow"
};

fetch("https://router.shengsuanyun.com/api/v1/audio/transcriptions", requestOptions)
   .then((response) => response.text())
   .then((result) => console.log(result))
   .catch((error) => console.error(error));
```
{% endcode %}
{% endtab %}

{% tab title="curl" %}
{% code title="curl shell 示例" %}
```shellscript
curl --location 'https://router.shengsuanyun.com/api/v1/audio/transcriptions' \
--header 'Authorization: Bearer <token>' \
--header 'Content-Type: application/json' \
--data '{
    "file": "https://example-bucket.oss-cn-beijing.aliyuncs.com/audio/input-01.mp3",
    "model": "whisper-1",
    "response_format": "text",
    "language": "en"
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
   curl_easy_setopt(curl, CURLOPT_URL, "https://router.shengsuanyun.com/api/v1/audio/transcriptions");
   curl_easy_setopt(curl, CURLOPT_FOLLOWLOCATION, 1L);
   curl_easy_setopt(curl, CURLOPT_DEFAULT_PROTOCOL, "https");
   struct curl_slist *headers = NULL;
   headers = curl_slist_append(headers, "Authorization: Bearer <token>");
   headers = curl_slist_append(headers, "Content-Type: application/json");
   curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);
   const char *data = "{\n    \"file\": \"https://example-bucket.oss-cn-beijing.aliyuncs.com/audio/input-01.mp3\",\n    \"model\": \"whisper-1\",\n    \"response_format\": \"text\",\n    \"language\": \"en\"\n}";
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

转录结果。返回的 Content-Type 根据 response\_format 决定。

#### Body `application/json`

One of:

* `Whispher 音频转录 响应- text`
* `Whispher 音频转录 响应- json`
* `string`
* `object`

简单 JSON 格式的转录结果，仅包含文本。

* `text` `string`
  * 转录得到的文本。

示例：

```json
{
  "text": "Hello world. This is the transcript."
}
```

### 🟠 400 请求有误

### 🟠 401 未认证

### 🔴 500 服务器内部错误
