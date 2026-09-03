# Qwenimage-千问图像生成/编辑-异步

**POST**`/v1/tasks/generations`

支持图像生成（文生图）和图像编辑的异步任务接口，提交任务后返回任务信息。

## 请求参数

### Authorization

Header 参数

### Content-Type

* 类型：`string`
* 必需
* 示例：`application/json`

### Body 参数 `application/json`

可选

Any of:

* 文生图
* 图生图

`object`

#### model

* 类型：`enum<string>`
* 模型名称
* 必需
* 值：`openai/gpt-image-1`

#### prompt

* 类型：`string`
* 必需
* 正向提示词，描述生成图像中期望包含的元素和视觉特点
* 示例：一只可爱的小猫坐在花园里

#### negative\_prompt

* 类型：`string`
* 可选
* 反向提示词，用于描述不希望在图像中出现的内容，对画面进行限制。
* 支持中英文，长度不超过500个字符，超出部分将自动截断。示例值：低分辨率、错误、最差质量、低质量、残缺、多余的手指、比例不良等。

#### size

* 类型：`enum<string>`
* 可选
* 输出图像的分辨率，格式为宽 _高。默认分辨率为1328_ 1328。可选的分辨率及其对应的图像宽高比例为：1664\*928：16:9。1472\*1140：4:3 。1328\*1328（默认值）：1:1。1140\*1472：3:4。928\*1664：9:16
* 枚举值：`auto1024x10241536x10241024x1536256x256512x5121792x10241024x1792`
* 默认值：`auto`
* 示例：`1024x1024`

#### prompt\_extend

* 类型：`boolean`
* 可选
* 是否开启prompt智能改写。开启后，将使用大模型优化正向提示词，对描述性不足、较为简单的prompt有明显提升效果，但会增加3-4秒耗时。true：默认值，开启智能改写。false：不开启智能改写。

#### watermark

* 类型：`boolean`
* 可选
* 是否在图像右下角添加 "Qwen-Image" 水印。默认值为 false

#### seed

* 类型：`integer`
* 可选
* 随机数种子，取值范围\[0,2147483647]。使用相同的seed参数值可使生成内容保持相对稳定。若不提供，算法将自动使用随机数种子。注意：模型生成过程具有概率性，即使使用相同的seed，也不能保证每次生成结果完全一致。

## 示例



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
{% code title="python request示例 图像生成任务" %}
```python
import requests
import json

url = "https://router.shengsuanyun.com/api/v1/tasks/generations"

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
   'Content-Type': 'application/json'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```
{% endcode %}
{% endtab %}

{% tab title="js" %}
{% code title="js fetch 示例 图像编辑任务" %}
```json
const myHeaders = new Headers();
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
{% code title="curl shell 示例 图像生成任务" %}
```shellscript
curl --location 'https://router.shengsuanyun.com/api/v1/tasks/generations' \
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
{% code title="c 示例 图像编辑任务" %}
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

### 🟢200 成功

`application/json`

任务提交成功

#### Body `application/json`

#### code

* 类型：`string`
* 可选
* 示例：`success`

#### message

* 类型：`string`
* 可选
* 示例：

#### data

`object(异步Task响应Data)`

异步Task响应Data

可选

**request\_id**

* 类型：`string`
* 请求的唯一标识符
* 可选

**task\_id**

* 类型：`string`
* 任务 ID，系统内部生成的任务标识符
* 可选

**action**

* 类型：`enum<string>`
* 任务类型
* 可选
* 枚举值：`VIDEO_GENERATIONIMAGE_GENERATIONAUDIO_GENERATION3D_GENERATION`

**status**

* 类型：`enum<string>`
* 可选
* 任务状态，详见任务状态说明
* 枚举值：`SUBMITTINGSUBMITTEDIN_PROGRESSCOMPLETEDFAILEDCANCELLED`

**fail\_reason**

* 类型：`string`
* 可选
* 失败原因，任务成功时为空

**submit\_time**

* 类型：`integer<int64>`
* 可选
* 任务提交时间戳（Unix 时间戳，秒）

**start\_time**

* 类型：`integer<int64>`
* 可选
* 任务开始处理时间戳（Unix 时间戳，秒）

**finish\_time**

* 类型：`integer<int64>`
* 可选
* 任务完成时间戳（Unix 时间戳，秒），未完成时为 0

**progress**

* 类型：`string`
* 可选
* 进度百分比
* 示例：`85%`

#### data

`object(异步Task响应Result)`

异步Task响应Result

可选

任务结果数据对象

### 示例

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

### 🟠400 请求有误

### 🟠401 未认证

### 🔴500 服务器内部错误
