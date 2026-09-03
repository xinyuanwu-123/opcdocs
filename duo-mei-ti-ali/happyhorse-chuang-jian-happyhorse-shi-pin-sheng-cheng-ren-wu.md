# HappyHorse-创建HappyHorse视频生成任务

**POST**`/v1/tasks/generations`

创建异步视频任务，返回 `task_id`，然后通过 `GET /v1/tasks/generations/{task_id}` 轮询结果。

4 个模型共用本接口，按 `model` 字段路由。

`task_id` 有效期 24 小时；建议 15 秒轮询间隔。

## 请求参数

### Authorization

### Body 参数

`application/json`，必填

4 个 HappyHorse 模型共用同一请求结构体。字段是否生效由 `model` 决定，详见各字段 `description`。

#### `model`

`enum<string>`

必需

模型名称，决定后续字段的生效集合。

枚举值：

* `ali/happyhorse-1.0-t2v`
* `ali/happyhorse-1.0-i2v`
* `ali/happyhorse-1.0-r2v`
* `ali/happyhorse-1.0-video-edit`

示例：

* `ali/happyhorse-1.0-t2v`

#### `input`

`object(HappyHorseInput)`

必需

输入信息。不同模型对 `prompt` 与 `media` 的必填要求不同。

**`prompt`**

`string`

可选

文本提示词。支持任意语言；非中文不超过 5000 字符，中文不超过 2500 字符，超长自动截断。**必填性**：

* `t2v` / `r2v` / `video-edit`：必填
* `i2v`：可选

`r2v` 中可用 `character1 / character2 / ...` 指代 `media` 数组中对应位置的 `reference_image`。

`<= 5000 字符`

示例：

一座由硬纸板和瓶盖搭建的微型城市，在夜晚焕发出生机。

**`media`**

`array[object (HappyHorseMediaItem)]`

可选

媒体素材数组。不同模型要求不同：

* `t2v`：**不使用**（不传或留空）。
* `i2v`：**必填**，有且仅有 1 个 `type=first_frame`。
* `r2v`：**必填**，1\~9 个 `type=reference_image`，顺序对应 prompt 中 `character1..N`。
* `video-edit`：**必填**，恰好 1 个 `type=video` + 0\~5 个 `type=reference_image`。

#### `parameters`

`object(HappyHorseParameters)`

可选

视频生成参数。所有字段均为可选；若不传则采用各模型的默认值。

**`resolution`**

`enum<string>`

可选

生成视频的分辨率档位，4 个模型均支持。默认 `1080P`。

枚举值：

* `720P`
* `1080P`

默认值：

* `1080P`

示例：

* `720P`

**`ratio`**

`enum<string>`

可选

视频宽高比。**仅适用于 `t2v` / `r2v`**；`i2v` 自动跟随输入首帧，`video-edit` 跟随输入视频，两者传入会被忽略。

默认 `16:9`。

枚举值：

* `16:9`
* `9:16`
* `1:1`
* `4:3`
* `3:4`

默认值：

* `16:9`

示例：

* `16:9`

**`duration`**

`integer`

可选

生成视频时长（秒），取值 `[3, 15]`。**适用于 `t2v` / `i2v` / `r2v`**；`video-edit` 不支持该字段（输出时长由输入视频决定，上限 15 秒）。

默认 `5`。

> \>= 3 ， <= 15

默认值：

* `5`

示例：

* `5`

**`watermark`**

`boolean`

可选

是否添加水印（右下角「Happy Horse」）。4 个模型均支持。默认 `true`。

默认值：

* `true`

示例：

* `true`

**`seed`**

`integer`

可选

随机种子。4 个模型均支持。取值 `[0, 2147483647]`。未指定时系统随机生成；固定 seed 可提升可复现性（概率模型仍不保证完全一致）。

> \>= 0 ， <= 2147483647

示例：

* `42`

**`audio_setting`**

`enum<string>`

可选

音频控制。**仅适用于 `video-edit`**，其他模型传入会被忽略。

* `auto`：由模型自行控制（默认）
* `origin`：保留输入视频的原始声音

枚举值：

* `auto`
* `origin`

默认值：

* `auto`

示例：

* `origin`

## 示例

{% tabs %}
{% tab title="文生视频" %}
```json
{
    "model": "ali/happyhorse-1.0-t2v",
    "input": {
        "prompt": "一座由硬纸板和瓶盖搭建的微型城市，在夜晚焕发出生机。一列硬纸板火车缓缓驶过，小灯点缀其间，照亮前路。"
    },
    "parameters": {
        "resolution": "720P",
        "ratio": "16:9",
        "duration": 5
    }
}
```
{% endtab %}

{% tab title="图生视频-首帧" %}
```json
{
    "model": "ali/happyhorse-1.0-i2v",
    "input": {
        "prompt": "一只猫在草地上奔跑",
        "media": [
            {
                "type": "first_frame",
                "url": "https://cdn.translate.alibaba.com/r/wanx-demo-1.png"
            }
        ]
    },
    "parameters": {
        "resolution": "720P",
        "duration": 5
    }
}
```
{% endtab %}

{% tab title="视频编辑" %}
```json
{
    "model": "ali/happyhorse-1.0-video-edit",
    "input": {
        "prompt": "让视频中的马头人身角色穿上图片中的条纹毛衣",
        "media": [
            {
                "type": "video",
                "url": "https://help-static-aliyun-doc.aliyuncs.com/file-manage-files/zh-CN/20260409/dozxak/Wan_Video_Edit_33_1.mp4"
            },
            {
                "type": "reference_image",
                "url": "https://help-static-aliyun-doc.aliyuncs.com/file-manage-files/zh-CN/20260415/hynnff/wan-video-edit-clothes.webp"
            }
        ]
    },
    "parameters": {
        "resolution": "720P",
        "audio_setting": "origin"
    }
}
```
{% endtab %}
{% endtabs %}

## 请求示例代码

{% tabs %}
{% tab title="python" %}
{% code title="python request 参考生视频示例" %}
```python
import requests
import json

url = "https://router.shengsuanyun.com/api/v1/tasks/generations"

payload = json.dumps({
   "model": "ali/happyhorse-1.0-r2v",
   "input": {
      "prompt": "身着红色旗袍的女性 character1，手持折扇 character2，佩戴流苏耳坠 character3，东方韵味特写。",
      "media": [
         {
            "type": "reference_image",
            "url": "https://help-static-aliyun-doc.aliyuncs.com/file-manage-files/zh-CN/20260424/mvzfud/hh-v2v-girl.jpg"
         },
         {
            "type": "reference_image",
            "url": "https://help-static-aliyun-doc.aliyuncs.com/file-manage-files/zh-CN/20260424/fvuihk/hh-v2v2-folding-fan.jpg"
         },
         {
            "type": "reference_image",
            "url": "https://help-static-aliyun-doc.aliyuncs.com/file-manage-files/zh-CN/20260424/imerii/hh-v2v-earrings.jpg"
         }
      ]
   },
   "parameters": {
      "resolution": "720P",
      "ratio": "16:9",
      "duration": 5
   }
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
{% code title="js fetch 图生视频-首帧示例" %}
```json
const myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

const raw = JSON.stringify({
   "model": "ali/happyhorse-1.0-i2v",
   "input": {
      "prompt": "一只猫在草地上奔跑",
      "media": [
         {
            "type": "first_frame",
            "url": "https://cdn.translate.alibaba.com/r/wanx-demo-1.png"
         }
      ]
   },
   "parameters": {
      "resolution": "720P",
      "duration": 5
   }
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
{% code title="curl shell文生视频示例" %}
```shellscript
curl --location 'https://router.shengsuanyun.com/api/v1/tasks/generations' \
--header 'Content-Type: application/json' \
--data '{
    "model": "ali/happyhorse-1.0-t2v",
    "input": {
        "prompt": "一座由硬纸板和瓶盖搭建的微型城市，在夜晚焕发出生机。一列硬纸板火车缓缓驶过，小灯点缀其间，照亮前路。"
    },
    "parameters": {
        "resolution": "720P",
        "ratio": "16:9",
        "duration": 5
    }
}'
```
{% endcode %}
{% endtab %}

{% tab title="c" %}
{% code title="C 视频编辑示例" %}
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
   const char *data = "{\n    \"model\": \"ali/happyhorse-1.0-video-edit\",\n    \"input\": {\n        \"prompt\": \"让视频中的马头人身角色穿上图片中的条纹毛衣\",\n        \"media\": [\n            {\n                \"type\": \"video\",\n                \"url\": \"https://help-static-aliyun-doc.aliyuncs.com/file-manage-files/zh-CN/20260409/dozxak/Wan_Video_Edit_33_1.mp4\"\n            },\n            {\n                \"type\": \"reference_image\",\n                \"url\": \"https://help-static-aliyun-doc.aliyuncs.com/file-manage-files/zh-CN/20260415/hynnff/wan-video-edit-clothes.webp\"\n            }\n        ]\n    },\n    \"parameters\": {\n        \"resolution\": \"720P\",\n        \"audio_setting\": \"origin\"\n    }\n}";
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

任务创建成功

### Body

`application/json`

#### `code`

`string`

响应状态码

可选

示例：

* `success`

#### `message`

`string`

响应消息

可选

#### `data`

`object(异步Task响应Data)`

可选

**`request_id`**

`string`

请求的唯一标识符

可选

**`task_id`**

`string`

任务ID，系统内部生成的任务标识符

可选

**`action`**

`enum<string>`

任务类型

可选

枚举值：

* `VIDEO_GENERATION`
* `IMAGE_GENERATION`
* `AUDIO_GENERATION`
* `3D_GENERATION`

示例：

* `3D_GENERATION`

**`status`**

`enum<string>`

任务状态：`SUBMITTING`(提交中)、`SUBMITTED`(已提交/排队中)、`IN_PROGRESS`(处理中)、`COMPLETED`(已完成)、`FAILED`(失败)、`CANCELLED`(已取消)

枚举值：

* `SUBMITTING`
* `SUBMITTED`
* `IN_PROGRESS`
* `COMPLETED`
* `FAILED`
* `CANCELLED`

**`fail_reason`**

`string`

失败原因，任务成功时为空

**`submit_time`**

`integer<int64>`

任务提交时间戳（Unix时间戳，秒）

**`start_time`**

`integer<int64>`

任务开始处理时间戳（Unix时间戳，秒）

**`finish_time`**

`integer<int64>`

任务完成时间戳（Unix时间戳，秒），未完成时为 0

**`progress`**

`string`

进度百分比

可选

示例：

* `85%`

**`data`**

`object(异步Task响应Result)`

任务结果数据对象

示例

```json
{
    "code": "success",
    "message": "",
    "data": {
        "request_id": "string",
        "task_id": "string",
        "action": "VIDEO_GENERATION",
        "status": "SUBMITTING",
        "fail_reason": "string",
        "submit_time": 0,
        "start_time": 0,
        "finish_time": 0,
        "progress": "0%",
        "data": {}
    }
}
```

### 🟠 4XX
