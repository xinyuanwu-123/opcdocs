# Wan-创建万相视频生成任务

**POST** `/v1/tasks/generations`

创建异步视频任务，返回 `task_id`，然后通过 `GET /v1/tasks/generations/{task_id}` 轮询结果。

5 个模型共用本接口，按 `model` 字段路由。

`task_id` 有效期 24 小时；建议 15 秒轮询间隔。

## 请求参数

### Authorization

### Body 参数 `application/json`（必填）

5 个万相视频模型共用同一请求结构体。字段是否生效由 `model` 决定。

#### `model`

* 类型：`enum<string>`
* 必需
* 说明：模型名称，决定后续字段的生效集合。

枚举值：

* `ali/wan2.7-t2v`
* `ali/wan2.7-i2v`
* `ali/wan2.7-r2v`
* `ali/wan2.7-videoedit`
* `ali/wan2.6-i2v-flash`

示例：

```
ali/wan2.7-t2v
```

#### `input`

* 类型：`object(WanInput)`
* 必需
* 说明：输入信息。不同模型对各字段的必填要求不同。

#### `prompt`

* 类型：`string`
* 可选
* 说明：文本提示词。支持中英文，超长自动截断。

**长度限制：**

* `wan2.7-t2v`：≤ 5000 字符
* `wan2.7-i2v`：可选，≤ 5000 字符
* `wan2.7-r2v`：≤ 5000 字符
* `wan2.7-videoedit`：≤ 5000 字符
* `wan2.6-i2v-flash`：≤ 1500 字符；使用 `template` 视频特效时该字段无效

**必填性：**

* `wan2.7-t2v` / `wan2.7-r2v` / `wan2.7-videoedit`：必填
* `wan2.7-i2v` / `wan2.6-i2v-flash`：可选

**引用写法：**

* `wan2.7-r2v`：用 `图1/图2...`、`视频1/视频2...`（英文 `Image 1` / `Video 1`）分别指代 `media` 中对应位置的 `reference_image` / `reference_video`。
* `wan2.6` 系列：用 `character1 / character2 ...` 指代 `reference_urls` 数组中的参考素材。

示例：

```
一只橘猫穿着宇航服漂浮在太空中，身后是壮丽的星云，镜头缓缓拉近。
```

#### `negative_prompt`

* 类型：`string`
* 可选
* 说明：反向提示词，描述不希望出现在视频中的内容。5 个模型均支持，≤ 500 字符。

示例：

```
低分辨率、错误、最差质量、低质量、残缺、比例不良
```

#### `img_url`

* 类型：`string`
* 可选
* 说明：**仅 `wan2.6-i2v-flash` 使用（必填）**：首帧图像 URL 或 Base64。

格式：JPEG/JPG/PNG（无透明通道）/BMP/WEBP。

分辨率：`[240, 8000]` 像素。

文件大小：wan2.6/2.5 ≤ 20MB；wan2.2/wanx2.1 ≤ 10MB。

支持公网 URL、OSS 临时 URL、Base64（`data:{MIME};base64,{data}`）。

wan2.7 系列请勿使用本字段，改用 `media[{type:"first_frame"}]`。

示例：

```
https://cdn.translate.alibaba.com/r/wanx-demo-1.png
```

#### `audio_url`

* 类型：`string`
* 可选
* 说明：**仅 `wan2.6-i2v-flash`（及 wan2.5 系列）支持**：音频 URL，用于给视频配音。

格式：wav / mp3。

时长：3\~30s。

文件大小：≤ 15MB。

若音频超过 `parameters.duration`，自动截取前 5s / 10s；不足则末尾静音。

与 `parameters.audio` 优先级：`audio > audio_url`，`audio=false` 时即使传入 `audio_url` 也输出无声视频。

wan2.7-i2v 的音频输入改走 `media[{type:"driving_audio"}]`。

示例：

```
https://help-static-aliyun-doc.aliyuncs.com/file-manage-files/zh-CN/20250925/ozwpvi/rap.mp3
```

#### `template`

* 类型：`string`
* 可选
* 说明：**仅旧协议图生视频模型支持**：视频特效模板名称，`wan2.6-i2v-flash` 等图生视频模型可用。使用 `template` 时 `prompt` 字段无效（建议留空）。wan2.7 系列不支持此字段。

示例：

```
flying
```

#### `media`

* 类型：`array[object (WanMediaItem)]`
* 可选
* 说明：媒体素材数组，**仅 wan2.7 系列使用**。不同模型要求不同：

`wan2.7-t2v`：**不使用**。

`wan2.7-i2v`：**必填**，允许的 type 组合：

* `first_frame` × 1
* `first_frame` × 1 + `driving_audio` × 1
* `first_frame` × 1 + `last_frame` × 1
* `first_frame` × 1 + `last_frame` × 1 + `driving_audio` × 1
* `first_clip` × 1（视频续写）
* `first_clip` × 1 + `last_frame` × 1

每种 type 最多 1 个。

`wan2.7-r2v`：**必填**，可混含：

* `reference_image` × 0\~n
* `reference_video` × 0\~n
* `first_frame` × 0\~1

至少 1 个 `reference_image` 或 `reference_video`；`reference_image + reference_video ≤ 5`。

顺序决定 prompt 中的 `图n / 视频n` 索引；每个 item 可独立附带 `reference_voice` 音色参考。

`wan2.7-videoedit`：**必填**，恰好 1 个 `type=video` + 0\~4 个 `type=reference_image`。

`wan2.6-i2v-flash` 不使用本字段。

#### `parameters`

* 类型：`object(WanParameters)`
* 可选
* 说明：视频生成参数。所有字段均可选；若不传则采用各模型的默认值。

#### `size`

* 类型：`string`
* 可选
* 说明：**旧协议字段**：格式 `宽*高`（如 `1280*720`），由 `model` 决定档位。

本文档覆盖的 5 个模型中 **不建议使用 `size`**：

* wan2.7 系列：改用 `resolution` + `ratio`
* wan2.6-i2v-flash：改用 `resolution`（枚举 720P/1080P）

此字段保留以兼容旧版接口。

示例：

```
1280*720
```

#### `resolution`

* 类型：`enum<string>`
* 可选
* 说明：分辨率档位，直接影响计费。

`wan2.7-t2v` / `wan2.7-i2v` / `wan2.7-r2v` / `wan2.7-videoedit`：枚举 `720P`、`1080P`，默认 `1080P`。

`wan2.6-i2v-flash`：枚举 `720P`、`1080P`，默认 `1080P`。

wan2.7 按 `resolution` + `ratio` 自动选择宽高像素；wan2.6-i2v-flash 按首帧比例自动缩放。

枚举值：

* `480P`
* `720P`
* `1080P`

默认值：

```
1080P
```

示例：

```
720P
```

#### `ratio`

* 类型：`enum<string>`
* 可选
* 说明：视频宽高比。**仅 wan2.7 系列使用**。生效逻辑：

`wan2.7-t2v`：必生效，默认 `16:9`。

`wan2.7-r2v`：未传 `first_frame` 时按 `ratio` 生成；传入 `first_frame` 则自动跟随首帧比例。

`wan2.7-videoedit`：未传时跟随输入视频比例。

`wan2.7-i2v`：忽略（自动跟随输入首帧）。

`wan2.6-i2v-flash`：忽略（自动跟随首帧图像）。

枚举值：

* `16:9`
* `9:16`
* `1:1`
* `4:3`
* `3:4`

默认值：

```
16:9
```

示例：

```
16:9
```

#### `duration`

* 类型：`integer`
* 可选
* 说明：生成视频时长（秒），直接影响计费。

`wan2.7-t2v` / `wan2.7-i2v`：`[2, 15]`，默认 5。

`wan2.7-r2v`：参考素材含视频时 `[2, 10]`；纯图参考时 `[2, 15]`；默认 5。

`wan2.7-videoedit`：`[2, 10]`；默认 0 表示跟随输入视频时长（超过 10 秒会被截断）。

`wan2.6-i2v-flash`：`[2, 15]`，默认 5。

默认值：

```
5
```

示例：

```
5
```

#### `prompt_extend`

* 类型：`boolean`
* 可选
* 说明：是否启用 prompt 智能改写。5 个模型均支持，默认 `true`。开启后对短 prompt 效果提升明显，但会略增加耗时。

默认值：

```
true
```

示例：

```
true
```

#### `watermark`

* 类型：`boolean`
* 可选
* 说明：是否添加 “AI 生成” 水印（视频右下角）。5 个模型均支持，**默认 `false`**（与上游文档保持一致）。

默认值：

```
false
```

示例：

```
false
```

#### `seed`

* 类型：`integer`
* 可选
* 说明：随机种子，`[0, 2147483647]`。5 个模型均支持。未指定时系统随机生成；固定 seed 可提升可复现性（概率模型仍不保证完全一致）。

示例：

```
12345
```

#### `shot_type`

* 类型：`enum<string>`
* 可选
* 说明：**仅 `wan2.6-i2v-flash`（及 wan2.6 系列）支持**：镜头类型控制。

`single`（默认）：单镜头视频

`multi`：多镜头视频

优先级：`shot_type > prompt`。wan2.6-i2v-flash 需配合 `prompt_extend=true` 才生效。wan2.7 系列请通过 prompt 中描述分镜实现多镜头效果。

枚举值：

* `single`
* `multi`

默认值：

```
single
```

示例：

```
single
```

#### `audio`

* 类型：`boolean`
* 可选
* 说明：**仅 `wan2.6-i2v-flash` 支持**：是否生成有声视频。

`true`（默认）：输出有声视频

`false`：输出无声视频

优先级：`audio > audio_url`。`audio=false` 时即使传入 `audio_url` 也会输出无声视频，且按无声视频计费。

默认值：

```
true
```

示例：

```
true
```

#### `audio_setting`

* 类型：`enum<string>`
* 可选
* 说明：**仅 `wan2.7-videoedit` 支持**：音频控制。其他模型传入会被忽略。

`auto`（默认）：由模型自行控制

`origin`：保留输入视频的原始声音

枚举值：

* `auto`
* `origin`

默认值：

```
auto
```

示例：

```
origin
```

## 示例

{% tabs %}
{% tab title="文生视频" %}
```json
{
  "model": "wan2.7-t2v",
  "input": {
    "prompt": "一只橘猫穿着宇航服漂浮在太空中，身后是壮丽的星云，镜头缓缓拉近。"
  },
  "parameters": {
    "resolution": "1080P",
    "ratio": "16:9",
    "duration": 5,
    "prompt_extend": true,
    "watermark": false
  }
}
```
{% endtab %}

{% tab title="图生视频-首尾帧" %}
```json
{
    "model": "wan2.7-i2v",
    "input": {
        "media": [
            {
                "type": "first_frame",
                "url": "https://example.com/first.png"
            },
            {
                "type": "last_frame",
                "url": "https://example.com/last.png"
            }
        ]
    },
    "parameters": {
        "resolution": "1080P",
        "duration": 5
    }
}
```
{% endtab %}

{% tab title="视频编辑" %}
```json
{
    "model": "wan2.7-videoedit",
    "input": {
        "prompt": "将角色身上的衣服替换为图中的条纹毛衣。",
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
        "audio_setting": "origin",
        "watermark": false
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
import http.client
import json

conn = http.client.HTTPSConnection("router.shengsuanyun.com")
payload = json.dumps({
   "model": "wan2.7-r2v",
   "input": {
      "prompt": "视频1抱着图3，在图4的椅子上弹奏一支舒缓的乡村民谣，并说道：“今天的阳光真好。”",
      "media": [
         {
            "type": "reference_image",
            "url": "https://help-static-aliyun-doc.aliyuncs.com/file-manage-files/zh-CN/20260408/sjuytr/wan-r2v-object-girl.jpg",
            "reference_voice": "https://help-static-aliyun-doc.aliyuncs.com/file-manage-files/zh-CN/20260408/gbqewz/wan-r2v-girl-voice.mp3"
         },
         {
            "type": "reference_video",
            "url": "https://help-static-aliyun-doc.aliyuncs.com/file-manage-files/zh-CN/20260129/qigswt/wan-r2v-role2.mp4",
            "reference_voice": "https://help-static-aliyun-doc.aliyuncs.com/file-manage-files/zh-CN/20260408/isllrq/wan-r2v-boy-voice.mp3"
         },
         {
            "type": "reference_image",
            "url": "https://help-static-aliyun-doc.aliyuncs.com/file-manage-files/zh-CN/20260129/rtjeqf/wan-r2v-object3.png"
         }
      ]
   },
   "parameters": {
      "resolution": "720P",
      "ratio": "16:9",
      "duration": 10,
      "prompt_extend": False,
      "watermark": True
   }
})
headers = {
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
{% code title="js fetch 图生视频-首尾帧示例" %}
```json
const myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

const raw = JSON.stringify({
   "model": "wan2.7-i2v",
   "input": {
      "media": [
         {
            "type": "first_frame",
            "url": "https://example.com/first.png"
         },
         {
            "type": "last_frame",
            "url": "https://example.com/last.png"
         }
      ]
   },
   "parameters": {
      "resolution": "1080P",
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
    "model": "wan2.7-t2v",
    "input": {
        "prompt": "一只橘猫穿着宇航服漂浮在太空中，身后是壮丽的星云，镜头缓缓拉近。"
    },
    "parameters": {
        "resolution": "1080P",
        "ratio": "16:9",
        "duration": 5,
        "prompt_extend": true,
        "watermark": false
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
   const char *data = "{\n    \"model\": \"wan2.7-videoedit\",\n    \"input\": {\n        \"prompt\": \"将角色身上的衣服替换为图中的条纹毛衣。\",\n        \"media\": [\n            {\n                \"type\": \"video\",\n                \"url\": \"https://help-static-aliyun-doc.aliyuncs.com/file-manage-files/zh-CN/20260409/dozxak/Wan_Video_Edit_33_1.mp4\"\n            },\n            {\n                \"type\": \"reference_image\",\n                \"url\": \"https://help-static-aliyun-doc.aliyuncs.com/file-manage-files/zh-CN/20260415/hynnff/wan-video-edit-clothes.webp\"\n            }\n        ]\n    },\n    \"parameters\": {\n        \"resolution\": \"720P\",\n        \"audio_setting\": \"origin\",\n        \"watermark\": false\n    }\n}";
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

### 🟢 200 `application/json`

任务创建成功

**Body `application/json`**

#### `code`

* 类型：`string`
* 说明：响应状态码

示例：

```
success
```

#### `message`

* 类型：`string`
* 说明：响应消息

#### `data`

* 类型：`object(异步Task响应Data)`
* 说明：异步Task响应Data

#### `request_id`

* 类型：`string`
* 说明：请求的唯一标识符

#### `task_id`

* 类型：`string`
* 说明：任务ID，系统内部生成的任务标识符

#### `action`

* 类型：`enum<string>`
* 说明：任务类型

枚举值：

* `VIDEO_GENERATION`
* `IMAGE_GENERATION`
* `AUDIO_GENERATION`
* `3D_GENERATION`

示例：

```
3D_GENERATION
```

#### `status`

* 类型：`enum<string>`
* 说明：任务状态：`SUBMITTING`(提交中)、`SUBMITTED`(已提交/排队中)、`IN_PROGRESS`(处理中)、`COMPLETED`(已完成)、`FAILED`(失败)、`CANCELLED`(已取消)

枚举值：

* `SUBMITTING`
* `SUBMITTED`
* `IN_PROGRESS`
* `COMPLETED`
* `FAILED`
* `CANCELLED`

#### `fail_reason`

* 类型：`string`
* 说明：失败原因，任务成功时为空

#### `submit_time`

* 类型：`integer<int64>`
* 说明：任务提交时间戳（Unix时间戳，秒）

#### `start_time`

* 类型：`integer<int64>`
* 说明：任务开始处理时间戳（Unix时间戳，秒）

#### `finish_time`

* 类型：`integer<int64>`
* 说明：任务完成时间戳（Unix时间戳，秒），未完成时为 0

#### `progress`

* 类型：`string`
* 说明：进度百分比

示例：

```
85%
```

#### `data`

* 类型：`object(异步Task响应Result)`
* 说明：任务结果数据对象

#### 示例

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

### 🟠 4XX 请求参数错误 / 鉴权失败
