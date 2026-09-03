# Paraformer-v2音频转录-异步

**POST** `/v1/tasks/generations`

提交一个异步音频转录任务。注意：同步转录接口不受支持。

## 请求参数

### Authorization

### Body 参数 `application/json`

| 参数名                           | 类型                    | 必需 | 说明                                                   | 值 / 默认值             |
| ----------------------------- | --------------------- | -- | ---------------------------------------------------- | ------------------- |
| `model`                       | `enum<string>`        | 是  | 模型ID                                                 | `ali/paraformer-v2` |
| `files`                       | `array[string <uri>]` | 否  | 多个音频文件的外部 URL 列表，优先使用 `files`。                       |                     |
| `file`                        | `string<uri>`         | 否  | 单个音频文件 URL；当 `files` 为空时可使用 `file` 字段。               |                     |
| `vocabulary_id`               | `string`              | 否  | 最新热词 ID（可选）                                          |                     |
| `resource_id`                 | `string`              | 否  | 旧版热词 `resource_id`（可选，部分模型兼容）                        |                     |
| `resource_type`               | `string`              | 否  | 若与 `resource_id` 一起使用，则固定值为 `asr_phrase`             |                     |
| `channel_id`                  | `array[integer]`      | 否  | 多音轨输入时指定要识别的音轨索引，例如 `[0]` 或 `[0,1]`。默认 `[0]`。        |                     |
| `disfluency_removal_enabled`  | `boolean`             | 否  | 是否过滤语气词，默认 `false`                                   |                     |
| `timestamp_alignment_enabled` | `boolean`             | 否  | 是否启用时间戳校准，默认 `false`                                 |                     |
| `special_word_filter`         | `string`              | 否  | 敏感词处理策略，传入 JSON 字符串以自定义过滤/替换行为                       |                     |
| `language_hints`              | `array[string]`       | 否  | 候选语言代码（仅 `paraformer-v2` 支持），例如 `["zh", "en"]`       | 默认值：`zhen`          |
| `diarization_enabled`         | `boolean`             | 否  | 是否启用说话人分离（仅单声道支持）                                    |                     |
| `speaker_count`               | `integer`             | 否  | 说话人数量参考值（2-100），用于 `diarization_enabled=true` 时的辅助手段 |                     |

### 示例

```json
{
  "model": "ali/paraformer-v2",
  "files": [
    "https://dashscope.oss-cn-beijing.aliyuncs.com/samples/audio/paraformer/hello_world_female2.wav",
    "https://dashscope.oss-cn-beijing.aliyuncs.com/samples/audio/paraformer/hello_world_male2.wav"
  ],
  "vocabulary_id": "",
  "channel_id": [
    0
  ],
  "language_hints": [
    "zh",
    "en"
  ],
  "disfluency_removal_enabled": false,
  "timestamp_alignment_enabled": false
}
```

## 请求示例代码

{% tabs %}
{% tab title="python" %}
{% code title="python request示例 " %}
```python
import requests
import json

url = "https://router.shengsuanyun.com/api/v1/tasks/generations"

payload = json.dumps({
   "model": "ali/paraformer-v2",
   "files": [
      "https://dashscope.oss-cn-beijing.aliyuncs.com/samples/audio/paraformer/hello_world_female2.wav",
      "https://dashscope.oss-cn-beijing.aliyuncs.com/samples/audio/paraformer/hello_world_male2.wav"
   ],
   "vocabulary_id": "",
   "channel_id": [
      0
   ],
   "language_hints": [
      "zh",
      "en"
   ],
   "disfluency_removal_enabled": False,
   "timestamp_alignment_enabled": False
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
{% code title="js fetch 示例 " %}
```json
const myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

const raw = JSON.stringify({
   "model": "ali/paraformer-v2",
   "files": [
      "https://dashscope.oss-cn-beijing.aliyuncs.com/samples/audio/paraformer/hello_world_female2.wav",
      "https://dashscope.oss-cn-beijing.aliyuncs.com/samples/audio/paraformer/hello_world_male2.wav"
   ],
   "vocabulary_id": "",
   "channel_id": [
      0
   ],
   "language_hints": [
      "zh",
      "en"
   ],
   "disfluency_removal_enabled": false,
   "timestamp_alignment_enabled": false
});

const requestOptions = {
   method: "POST",
   headers: myHeaders,
   body: raw,
   redirect: "follow"
};

fetch("https://router.shengsuanyun.com/api/v1/tasks/generations", requestOptions)
   .then((response) => response.text())
   .then((result) => console.log(result))
   .catch((error) => console.error(error));
```
{% endcode %}
{% endtab %}

{% tab title="curl" %}
{% code title="curl shell 示例 " %}
```shellscript
curl --location 'https://router.shengsuanyun.com/api/v1/tasks/generations' \
--header 'Content-Type: application/json' \
--data '{
    "model": "ali/paraformer-v2",
    "files": [
        "https://dashscope.oss-cn-beijing.aliyuncs.com/samples/audio/paraformer/hello_world_female2.wav",
        "https://dashscope.oss-cn-beijing.aliyuncs.com/samples/audio/paraformer/hello_world_male2.wav"
    ],
    "vocabulary_id": "",
    "channel_id": [
        0
    ],
    "language_hints": [
        "zh",
        "en"
    ],
    "disfluency_removal_enabled": false,
    "timestamp_alignment_enabled": false
}'
```
{% endcode %}
{% endtab %}

{% tab title="c" %}
{% code title="c 示例 " %}
```c
CURL *curl;
CURLcode res;
curl = curl_easy_init();
if(curl) {
   curl_easy_setopt(curl, CURLOPT_CUSTOMREQUEST, "POST");
   curl_easy_setopt(curl, CURLOPT_URL, "https://router.shengsuanyun.com/api/v1/tasks/generations");
   curl_easy_setopt(curl, CURLOPT_FOLLOWLOCATION, 1L);
   curl_easy_setopt(curl, CURLOPT_DEFAULT_PROTOCOL, "https");
   struct curl_slist *headers = NULL;
   headers = curl_slist_append(headers, "Content-Type: application/json");
   curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);
   const char *data = "{\n    \"model\": \"ali/paraformer-v2\",\n    \"files\": [\n        \"https://dashscope.oss-cn-beijing.aliyuncs.com/samples/audio/paraformer/hello_world_female2.wav\",\n        \"https://dashscope.oss-cn-beijing.aliyuncs.com/samples/audio/paraformer/hello_world_male2.wav\"\n    ],\n    \"vocabulary_id\": \"\",\n    \"channel_id\": [\n        0\n    ],\n    \"language_hints\": [\n        \"zh\",\n        \"en\"\n    ],\n    \"disfluency_removal_enabled\": false,\n    \"timestamp_alignment_enabled\": false\n}";
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

任务提交成功，返回 `task_id`（下游 Ali 的 task id）

#### Body `application/json`

| 字段            | 类型                       | 说明                          | 示例                                                                       |
| ------------- | ------------------------ | --------------------------- | ------------------------------------------------------------------------ |
| `code`        | `string`                 |                             | `success`                                                                |
| `message`     | `string`                 |                             |                                                                          |
| `data`        | `object(异步Task响应Data)`   | 异步Task响应Data                |                                                                          |
| `request_id`  | `string`                 | 请求的唯一标识符                    |                                                                          |
| `task_id`     | `string`                 | 任务 ID，系统内部生成的任务标识符          |                                                                          |
| `action`      | `enum<string>`           | 任务类型                        | `VIDEO_GENERATION` `IMAGE_GENERATION` `AUDIO_GENERATION` `3D_GENERATION` |
| `status`      | `enum<string>`           | 任务状态，详见任务状态说明               | `SUBMITTING` `SUBMITTED` `IN_PROGRESS` `COMPLETED` `FAILED` `CANCELLED`  |
| `fail_reason` | `string`                 | 失败原因，任务成功时为空                |                                                                          |
| `submit_time` | `integer<int64>`         | 任务提交时间戳（Unix 时间戳，秒）         |                                                                          |
| `start_time`  | `integer<int64>`         | 任务开始处理时间戳（Unix 时间戳，秒）       |                                                                          |
| `finish_time` | `integer<int64>`         | 任务完成时间戳（Unix 时间戳，秒），未完成时为 0 |                                                                          |
| `progress`    | `string`                 | 进度百分比                       | `85%`                                                                    |
| `data`        | `object(异步Task响应Result)` | 异步Task响应Result，任务结果数据对象     |                                                                          |

### 示例

```json
{
  "code": "success",
  "message": "",
  "data": {
    "request_id": "20250925172708855186000X7N0JqUG",
    "task_id": "f200345e-5dc0-45ef-aa1a-40ed2bf70d3f",
    "action": "AUDIO_TRANSCRIPTION",
    "status": "PENDING",
    "fail_reason": "",
    "submit_time": 1758792429,
    "start_time": 0,
    "finish_time": 0,
    "progress": "0%",
    "data": {
      "text_urls": [],
      "progress": 0
    }
  }
}
```

### 🟠 400 请求有误

### 🟠 401 未认证

### 🔴 500 服务器内部错误
