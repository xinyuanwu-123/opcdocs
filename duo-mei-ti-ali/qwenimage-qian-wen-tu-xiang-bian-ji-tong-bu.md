# Qwenimage-千问图像编辑-同步

**POST** `/v1/images/edits`

基于文本提示词对图像进行编辑处理的同步接口

## 请求参数

### Authorization

Header 参数

### Content-Type

* 类型：`string`
* 必需
* 示例：`application/json`

### Body 参数 `application/json`

| 参数               | 类型             | 必需 | 说明                                                                                                                                                     | 限制                                                                      | 示例                                                                       |
| ---------------- | -------------- | -: | ------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| model            | `enum<string>` |  是 | 模型名称                                                                                                                                                   | 枚举值：`ali/qwen-image-edit`、`ali/qwen-image-edit-plus`                    | `ali/qwen-image-edit`                                                    |
| prompt           | `string`       |  是 | 正向提示词，描述生成图像中期望包含的元素和视觉特点                                                                                                                              | `>= 1` 字符，`<= 1000` 字符                                                  | `将图中的人物改为趴姿势，伸手握住狗的前爪`                                                   |
| image            | `string<uri>`  |  是 | 需要编辑的图片URL                                                                                                                                             |                                                                         | `https://dashscope.oss-cn-beijing.aliyuncs.com/images/dog_and_girl.jpeg` |
| n                | `integer`      |  否 | 输出图像的数量，默认值为 `1`。对于 `qwen-image-edit-plus`、`qwen-image-edit-plus-2025-10-30`，可选择输出 1-6 张图片。对于 `qwen-image-edit`，仅支持输出 1 张图片。                           |                                                                         |                                                                          |
| negative\_prompt | `string`       |  否 | 反向提示词，描述生成图像中期望排除的元素和视觉特点                                                                                                                              | `<= 1000` 字符                                                            |                                                                          |
| size             | `string`       |  否 | 设置输出图像的分辨率，格式为宽 `_` 高，例如 `"1024_2048"`。宽和高的取值范围均为 `[512, 2048]` 像素。默认行为：若不设置，输出图像将保持与原图相似的长宽比，接近 `1024*1024` 分辨率。使用限制：该参数仅在输出图像数量 `n` 为 `1` 时可用，否则将报错。 | 支持模型：仅 `qwen-image-edit-plus` 和 `qwen-image-edit-plus-2025-10-30` 模型支持。 |                                                                          |
| watermark        | `boolean`      |  否 | 是否添加水印标识                                                                                                                                               | 默认值：`false`                                                             | `false`                                                                  |
| prompt\_extend   | `boolean`      |  否 | 是否开启 prompt 智能改写。开启后，将使用大模型优化正向提示词，对描述性不足、较为简单的 prompt 提升效果较明显。`true`：默认值，开启智能改写。`false`：不开启智能改写。                                                      | 支持模型：仅 `qwen-image-edit-plus` 和 `qwen-image-edit-plus-2025-10-30` 模型支持。 |                                                                          |
| seed             | `integer`      |  否 | 生成图片的种子                                                                                                                                                | `>= 0`，`<= 2147483647`                                                  | `1`                                                                      |

#### 示例

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

## 请求示例代码

{% tabs %}
{% tab title="python" %}
{% code title="python request示例" %}
```python
import requests
import json

url = "https://router.shengsuanyun.com/api/v1/images/edits"

payload = json.dumps({
   "model": "ali/qwen-image-edit",
   "prompt": "将图中的人物改为趴姿势，伸手握住狗的前爪",
   "image": "https://dashscope.oss-cn-beijing.aliyuncs.com/images/dog_and_girl.jpeg",
   "negative_prompt": "",
   "watermark": False,
   "seed": 1
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
{% code title="js fetch 示例" %}
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
--header 'Content-Type: application/json' \
--data '{
    "model": "ali/qwen-image-edit",
    "prompt": "将图中的人物改为趴姿势，伸手握住狗的前爪",
    "image": "https://dashscope.oss-cn-beijing.aliyuncs.com/images/dog_and_girl.jpeg",
    "negative_prompt": "",
    "watermark": false,
    "seed": 1
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

### 🟢 200 成功

`application/json`

图像编辑成功

#### Body `application/json`

| 参数           | 类型                                | 必需 | 说明             | 示例                                     |
| ------------ | --------------------------------- | -: | -------------- | -------------------------------------- |
| output       | `object`                          |  是 |                |                                        |
| choices      | `array[object (千问图像编辑响应-Choice)]` |  是 | 图像编辑结果选择列表     | `>= 1 items`                           |
| usage        | `object (千问图像编辑响应-Usage)`         |  是 | 千问图像编辑响应-Usage |                                        |
| width        | `integer`                         |  是 | 图像宽度（像素）       | `1248`                                 |
| height       | `integer`                         |  是 | 图像高度（像素）       | `832`                                  |
| image\_count | `integer`                         |  是 | 生成的图像数量        | `1`                                    |
| request\_id  | `string`                          |  是 | 请求ID           | `12bc3915-4e9d-9d84-83b8-584fd54c8194` |
| code         | `string`                          |  否 | 错误码（仅在错误时存在）   | `InvalidParameter`                     |
| message      | `string`                          |  否 | 错误信息（仅在错误时存在）  | `参数错误`                                 |

#### 示例

```json
{
  "output": {
    "choices": [
      {
        "finish_reason": "stop",
        "message": {
          "role": "assistant",
          "content": [
            {
              "image": "https://****"
            }
          ]
        }
      }
    ]
  },
  "usage": {
    "width": 1248,
    "image_count": 1,
    "height": 832
  },
  "request_id": "12bc3915-4e9d-9d84-83b8-584fd54c8194"
}
```

### 🟠 400 请求有误

### 🟠 401 未认证

### 🔴 500 服务器内部错误
