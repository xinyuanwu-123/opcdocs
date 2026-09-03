# 异步任务提交

**POST**`/v1/tasks/generations`

大多数 AI 生成任务（视频生成、图像生成等）都使用统一的任务提交接口。任务提交成功后，系统会返回任务ID，您可以使用该ID查询任务状态和获取结果。

## 请求参数

### Authorization

在 Header 添加参数 `Authorization`，其值为在 `Bearer` 之后拼接 Token。

示例：

`Authorization: Bearer ********************`

### Header 参数

| 参数名          | 类型     | 必需 | 示例               |
| ------------ | ------ | -- | ---------------- |
| Content-Type | string | 必需 | application/json |

### Body 参数

**application/json**

| 参数名           | 类型     | 说明                            | 必需 |
| ------------- | ------ | ----------------------------- | -- |
| model         | string | 模型ID                          | 必需 |
| callback\_url | string | 任务完成后的回调url，返回格式和任务查询接口一致。    | 可选 |
| others        | string | 每个模型都有各自特有的返回值，可以从模型特有参数文档中获取 | 必需 |

### 示例

{% tabs %}
{% tab title="图像生成任务" %}
```json
{
    "model": "ali/qwen-image",
    "prompt": "一只坐着的橘黄色的猫，表情愉悦，活泼可爱，逼真准确",
    "negative_prompt": "低分辨率、错误、最差质量、低质量、残缺、多余的手指、比例不良等",
    "size": "1664*928",
    "n": 1,
    "prompt_extend": true,
    "watermark": false
}
```
{% endtab %}

{% tab title="图像编辑任务" %}
```json
{
    "model": "ali/qwen-image-edit",
    "prompt": "将图中的人物改为趴姿势，伸手握住狗的前爪",
    "image": "https://dashscope.oss-cn-beijing.aliyuncs.com/images/dog_and_girl.jpeg",
    "negative_prompt": "",
    "watermark": false,
    "seed": 1
}
```
{% endtab %}
{% endtabs %}

## 请求示例代码

{% tabs %}
{% tab title="python" %}
{% code title="python request 图像生成任务" %}
```python
import http.client
import json

conn = http.client.HTTPSConnection("router.shengsuanyun.com")
payload = json.dumps({
   "model": "ali/qwen-image",
   "prompt": "一只坐着的橘黄色的猫，表情愉悦，活泼可爱，逼真准确",
   "negative_prompt": "低分辨率、错误、最差质量、低质量、残缺、多余的手指、比例不良等",
   "size": "1664*928",
   "n": 1,
   "prompt_extend": True,
   "watermark": False
})
headers = {
   'Authorization': 'Bearer <token>',
   'Content-Type': 'application/json'
}
conn.request("POST", "/api/v1/tasks/generations", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))
```
{% endcode %}
{% endtab %}

{% tab title="js" %}
{% code title="js fetch 图像编辑任务" %}
```json
const myHeaders = new Headers();
myHeaders.append("Authorization", "Bearer <token>");
myHeaders.append("Content-Type", "application/json");

const raw = JSON.stringify({
   "model": "ali/qwen-image-edit",
   "prompt": "将图中的人物改为趴姿势，伸手握住狗的前爪",
   "image": "https://dashscope.oss-cn-beijing.aliyuncs.com/images/dog_and_girl.jpeg",
   "negative_prompt": "",
   "watermark": false,
   "seed": 1
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
{% code title="curl shell 图像生成任务" %}
```shellscript
curl --location 'https://router.shengsuanyun.com/api/v1/tasks/generations' \
--header 'Authorization: Bearer <token>' \
--header 'Content-Type: application/json' \
--data '{
    "model": "ali/qwen-image",
    "prompt": "一只坐着的橘黄色的猫，表情愉悦，活泼可爱，逼真准确",
    "negative_prompt": "低分辨率、错误、最差质量、低质量、残缺、多余的手指、比例不良等",
    "size": "1664*928",
    "n": 1,
    "prompt_extend": true,
    "watermark": false
}'
```
{% endcode %}
{% endtab %}

{% tab title="c" %}
{% code title="c 图像编辑任务" %}
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
   headers = curl_slist_append(headers, "Authorization: Bearer <token>");
   headers = curl_slist_append(headers, "Content-Type: application/json");
   curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);
   const char *data = "{\n    \"model\": \"ali/qwen-image-edit\",\n    \"prompt\": \"将图中的人物改为趴姿势，伸手握住狗的前爪\",\n    \"image\": \"https://dashscope.oss-cn-beijing.aliyuncs.com/images/dog_and_girl.jpeg\",\n    \"negative_prompt\": \"\",\n    \"watermark\": false,\n    \"seed\": 1\n}";
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

### 🟢200成功

`application/json`

任务提交成功

#### **Bodyapplication/json**

#### 生成代码

| 参数名     | 类型                   | 说明           | 必需 | 示例      |
| ------- | -------------------- | ------------ | -- | ------- |
| code    | string               |              | 可选 | success |
| message | string               |              | 可选 |         |
| data    | object(异步Task响应Data) | 异步Task响应Data | 可选 |         |

#### 异步Task响应Data

| 参数名          | 类型                     | 说明                          | 必需 | 备注                                                                    |
| ------------ | ---------------------- | --------------------------- | -- | --------------------------------------------------------------------- |
| request\_id  | string                 | 请求的唯一标识符                    | 可选 |                                                                       |
| task\_id     | string                 | 任务 ID，系统内部生成的任务标识符          | 可选 |                                                                       |
| action       | enum                   | 任务类型                        | 可选 | 枚举值：VIDEO\_GENERATIONIMAGE\_GENERATIONAUDIO\_GENERATION3D\_GENERATION |
| status       | enum                   | 任务状态，详见任务状态说明               | 可选 | 枚举值：SUBMITTINGSUBMITTEDIN\_PROGRESSCOMPLETEDFAILEDCANCELLED           |
| fail\_reason | string                 | 失败原因，任务成功时为空                | 可选 |                                                                       |
| submit\_time | integer                | 任务提交时间戳（Unix 时间戳，秒）         | 可选 |                                                                       |
| start\_time  | integer                | 任务开始处理时间戳（Unix 时间戳，秒）       | 可选 |                                                                       |
| finish\_time | integer                | 任务完成时间戳（Unix 时间戳，秒），未完成时为 0 | 可选 |                                                                       |
| progress     | string                 | 进度百分比                       | 可选 | 示例：85%                                                                |
| data         | object(异步Task响应Result) | 任务结果数据对象                    | 可选 |                                                                       |

### 任务提交成功响应参数错误响应

```json
{
    "code": "success",
    "message": "",
    "data": {
        "request_id": "20250829203041325320000CFVslBwb",
        "task_id": "",
        "action": "IMAGE_GENERATION",
        "status": "SUBMITTING",
        "fail_reason": "",
        "submit_time": 1756470641,
        "start_time": 0,
        "finish_time": 0,
        "progress": "0%",
        "data": {}
    }
}
```

### 🟠400请求有误

### 🟠401未认证

### 🔴500服务器内部错误
