# 聊天补全

`POST` `/v1/chat/completions`

与 AI 模型进行多轮对话。

## 请求参数

### Header 参数

* `HTTP-Referer` `string`（可选）
  * 用于在 <code class="expression">space.vars.mainname</code>  Router 中排名的应用网站 URL
  * 示例：`https://www.postman.com`
* `X-Title` `string`（可选）
  * 用于在 <code class="expression">space.vars.mainname</code>  Router 中排名的应用名称
  * 示例：`Postman`

### Body 参数 `application/json`

* `model` `string`（必需）
  * 请求使用的模型 ID。
* `messages` `array[object (OpenAI-Chat 请求体-Message)]`
  * 对话的消息列表。
  * `role` `enum<string>`（必需）
    * 该消息的角色。
    * 枚举值：`system` `user` `assistant` `tool`
  * `content`
    * 该消息的内容。
  * `name` `string`（可选）
    * 为模型提供信息以区分相同角色。
  * `tool_calls` `array[object (OpenAI-Chat 请求体-ToolCall)]`（可选）
    * 模型生成的工具调用。
  * `tool_call_id` `string`（可选）
    * 此消息所回应的工具调用 ID，当 `role` 为 `tool` 时必填。
* `stream` `boolean`（可选）
  * 是否使用流式响应。
  * 默认值：`false`
* `online_search` `boolean`（可选）
  * 是否启用联网搜索功能。默认使用博查 AI 搜索。
  * 默认值：`false`
* `online_search_options` `object`（可选）
  * 联网搜索可选参数
  * `global` `boolean`（可选）
    * `true`，使用 tavily 国际搜索
* `stream_options` `object (OpenAI-Chat 请求体-StreamOptions)`（可选）
  * 流式输出相关选项，只有在 `stream` 参数为 `true` 时，此参数才有效。
  * `include_usage` `boolean`（可选）
    * 是否在响应中包括 token 的使用信息
* `max_tokens` `integer`（可选）
  * 限制一次请求中模型生成 completion 的最大 token 数。
* `temperature` `number`（可选）
  * 采样温度，用于控制模型生成文本的多样性。
  * 更高的值，如 `0.8`，会使输出更随机，而更低的值，如 `0.2`，会使其更加集中和确定。
  * `>= 0` `<= 2`
* `top_p` `number`（可选）
  * 核采样概率阈值，用于控制模型生成文本的多样性。
  * `top_p` 越高，生成的文本更多样。反之，生成的文本更确定。
  * 由于 `temperature` 与 `top_p` 均可以控制生成文本的多样性，因此建议您只设置其中一个值。
  * `>= 0` `<= 1`
* `stop`
  * 模型遇到 `stop` 字段所指定的字符串时将停止继续生成，这个词语本身不会输出。
  * One of:
    * `string`
    * `array[string]`
* `n` `integer`（可选）
  * 生成响应的个数。对于需要生成多个响应的场景（如创意写作、广告文案等），可以设置较大的 `n` 值。
  * 当前仅支持 `qwen-plus`、`doubao-pro` 模型，且在传入 `tools` 参数时固定为 `1`。
  * 设置较大的 `n` 值不会增加输入 Token 消耗，会增加输出 Token 的消耗。
  * `>= 1` `<= 4`
* `frequency_penalty` `number`（可选）
  * 频率惩罚系数。如果值为正，会根据新 token 在文本中出现的频率对其进行惩罚，从而降低模型逐字重复的可能性。
  * `>= -2` `<= 2`
* `presence_penalty` `number`（可选）
  * 存在惩罚系数。如果值为正，会根据新 token 到目前为止是否出现在文本中对其进行惩罚，从而增加模型谈论新主题的可能性。
  * `>= -2` `<= 2`
* `response_format` `object (OpenAI-Chat 请求体-ResponseFormat)`（可选）
  * `Response format specification`
  * `type` `enum<string>`（必需）
    * `Response format type`
    * 枚举值：`json_schema` `json_object`
  * `json_schema` `object (OpenAI-Chat 请求体-FormatJsonSchema)`（可选）
    * `JSON schema specification`
* `tools` `array[object (OpenAI-Chat 请求体-ToolCall)]`（可选）
  * 模型可以调用的工具列表。
  * `id` `string`（必需）
    * 当前工具调用 ID。
  * `type` `string`（必需）
    * 工具类型，当前仅支持 `function`。
  * `function` `object (OpenAI-Chat 请求体-Function)`（必需）
    * 模型需要调用的函数。
* `auto_route` `boolean`（可选）
  * 是否自动路由模型供应商。
  * 默认值：`true`
* `supplier` `enum<string>`（可选）
  * 指定模型供应商。只有在 `auto_route=false` 时该参数才会生效。
  * 枚举值：`DeepSeek` `Bytedance` `OpenRouter` `Ali`
* `reasoning` `object`（可选）
  * 设置思维链模式
  * `effort` `enum<string>`（可选）
    * 支持允许控制思维链长度模型
    * 枚举值：
      * `low` 低思考 token 量
      * `medium` 中等思考 token 量
      * `high` 高思考 token 量
  * `max_tokens` `integer`（可选）
    * 最大推理 Token 数，控制非 OpenAI 思考模型思维链长度
  * `type` `string`（可选）
    * 开启和关闭思考模式，可选参数：`enabled`、`disabled`
    * 支持大多数混合推理模型

### 示例

{% tabs %}
{% tab title="基础调用" %}
```json
{
  "model": "deepseek/deepseek-v3",
  "stream": true,
  "messages": [
    {
      "role": "user",
      "content": "你好"
    }
  ],
  "stream_options": {
    "include_usage": true
  }
}
```
{% endtab %}

{% tab title="指定供应商" %}
```json
{
    "model": "anthropic/claude-3.7-sonnet",
    "messages": [
        {
            "role": "user",
            "content": "9.8和9.11谁更大"
        }
    ],
    "auto_route": false,
    "supplier": "OpenRouter"
}
```
{% endtab %}

{% tab title="联网搜索" %}
```json
{
    "stream": true,
    "online_search": true,
    "messages": [
        {
            "role": "user",
            "content": "今天上海天气如何"
        }
    ],
    "stream_options": {
        "include_usage": true
    },
    "model": "deepseek/deepseek-v3"
}
```
{% endtab %}

{% tab title="思考模式" %}
```json
{
    "stream": true,
    "messages": [
        {
            "role": "user",
            "content": [
                {
                    "type": "text",
                    "text": "你好,你是谁"
                }
            ]
        }
    ],
    "stream_options": {
        "include_usage": true
    },
    "reasoning":{
        "max_tokens": 2048
    },
    "model": "google/gemini-2.5-flash-lite-preview"
}
```
{% endtab %}
{% endtabs %}

## 请求示例代码

{% tabs %}
{% tab title="python" %}
{% code title="python request 示例" %}
```python
import requests
import json

url = "https://router.shengsuanyun.com/api/v1/chat/completions"

payload = json.dumps({
   "model": "deepseek/deepseek-v3",
   "stream": True,
   "messages": [
      {
         "role": "user",
         "content": "你好"
      }
   ],
   "stream_options": {
      "include_usage": True
   }
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

{% tab title="CURL" %}
{% code title="curl shell示例" %}
```shellscript
curl --location 'https://router.shengsuanyun.com/api/v1/chat/completions' \
--header 'HTTP-Referer: https://www.postman.com' \
--header 'X-Title: Postman' \
--header 'Content-Type: application/json' \
--data '{
  "model": "deepseek/deepseek-v3",
  "stream": true,
  "messages": [
    {
      "role": "user",
      "content": "你好"
    }
  ],
  "stream_options": {
    "include_usage": true
  }
}'
```
{% endcode %}
{% endtab %}

{% tab title="JS" %}
{% code title="js fetch示例" %}
```json
const myHeaders = new Headers();
myHeaders.append("HTTP-Referer", "https://www.postman.com");
myHeaders.append("X-Title", "Postman");
myHeaders.append("Content-Type", "application/json");

const raw = JSON.stringify({
   "model": "deepseek/deepseek-v3",
   "stream": true,
   "messages": [
      {
         "role": "user",
         "content": "你好"
      }
   ],
   "stream_options": {
      "include_usage": true
   }
});

const requestOptions = {
   method: "POST",
   headers: myHeaders,
   body: raw,
   redirect: "follow"
};

fetch("https://router.shengsuanyun.com/api/v1/chat/completions", requestOptions)
   .then((response) => response.text())
   .then((result) => console.log(result))
   .catch((error) => console.error(error));
```
{% endcode %}
{% endtab %}

{% tab title="C" %}
{% code title="c 基础调用示例" %}
```c
CURL *curl;
CURLcode res;
curl = curl_easy_init();
if(curl) {
   curl_easy_setopt(curl, CURLOPT_CUSTOMREQUEST, "POST");
   curl_easy_setopt(curl, CURLOPT_URL, "https://router.shengsuanyun.com/api/v1/chat/completions");
   curl_easy_setopt(curl, CURLOPT_FOLLOWLOCATION, 1L);
   curl_easy_setopt(curl, CURLOPT_DEFAULT_PROTOCOL, "https");
   struct curl_slist *headers = NULL;
   headers = curl_slist_append(headers, "HTTP-Referer: https://www.postman.com");
   headers = curl_slist_append(headers, "X-Title: Postman");
   headers = curl_slist_append(headers, "Content-Type: application/json");
   curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);
   const char *data = "{\n    \"model\": \"deepseek/deepseek-v3\",\n    \"stream\": true,\n    \"messages\": [\n        {\n            \"role\": \"user\",\n            \"content\": \"你好\"\n        }\n    ],\n    \"stream_options\": {\n        \"include_usage\": true\n    }\n}";
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

请求成功。

### Body `application/json`

* `id` `string`（必需）
  * 响应 ID
* `provider` `string`（必需）
  * Provider 名称
* `model` `string`（必需）
  * 用户调用模型名称
* `object` `string`（必需）
  * 对象类型
* `created` `integer`（必需）
  * 创建时间戳
* `choices` `array[object (OpenAI Chat响应体-Choice)]`（必需）
  * 返回消息
  * `message` `object`（可选）
    * 生成的文本
  * `index` `integer`（可选）
    * 选择索引
  * `finish_reason` `string`（可选）
    * 完成原因
* `usage` `object (OpenAI Chat 响应-Usage)`（可选）
  * 用量
  * `prompt_tokens` `integer`（可选）
    * 提示词 token 数
  * `completion_tokens` `integer`（可选）
    * 完成 token 数
  * `total_tokens` `integer`（可选）
    * 总 token 数

### 示例

{% tabs %}
{% tab title="非流式响应" %}
```json
{
  "id": "20250410143202432620157mDT6DvdC",
  "provider": "OpenRouter",
  "model": "anthropic/claude-3.7-sonnet",
  "object": "chat.completion",
  "created": 1744266722,
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "I don't have the ability to check real-time weather information for Shanghai or any other location. To get accurate and current weather information for Shanghai, you could:\n\n1. Check a weather website or app such as Weather.com, AccuWeather, or a local Chinese weather service\n2. Search for \"上海天气\" (Shanghai weather) on a search engine\n3. Look at the weather function on your smartphone\n4. Check local Shanghai news websites\n\nIf you need current weather information, these sources will provide you with accurate forecasts, temperature, precipitation chances, and other relevant weather data for Shanghai today."
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 16,
    "completion_tokens": 133,
    "total_tokens": 149
  }
} 
```
{% endtab %}

{% tab title="流式响应" %}
```json
{"id":"20250410152636722129782COrenCqr","provider":"Bytedance","model":"deepseek/deepseek-v3","object":"chat.completion.chunk","created":1744269996,"choices":[{"delta":{"content":"你好","role":"assistant"},"logprobs":null,"finish_reason":null,"index":0}]}

{"id":"20250410152636722129782COrenCqr","provider":"Bytedance","model":"deepseek/deepseek-v3","object":"chat.completion.chunk","created":1744269996,"choices":[{"delta":{"content":"！","role":"assistant"},"logprobs":null,"finish_reason":null,"index":0}]}

{"id":"20250410152636722129782COrenCqr","provider":"Bytedance","model":"deepseek/deepseek-v3","object":"chat.completion.chunk","created":1744269996,"choices":[{"delta":{"content":"","role":"assistant"},"logprobs":null,"finish_reason":null,"index":0}]}

{"id":"20250410152636722129782COrenCqr","provider":"Bytedance","model":"deepseek/deepseek-v3","object":"chat.completion.chunk","created":1744269996,"choices":[{"delta":{"content":"😊","role":"assistant"},"logprobs":null,"finish_reason":null,"index":0}]}

...

{"id":"20250410152636722129782COrenCqr","provider":"Bytedance","model":"deepseek/deepseek-v3","object":"chat.completion.chunk","created":1744269996,"choices":[{"delta":{},"logprobs":null,"finish_reason":"stop","index":0}],"usage":{"prompt_tokens":4,"completion_tokens":15,"total_tokens":19}}
```
{% endtab %}

{% tab title="联网搜索结果" %}
```json
{
    "id": "20250411103329715595694rce8eVJf",
    "provider": "OpenRouter:DeepInfra",
    "model": "deepseek/deepseek-v3",
    "object": "chat.completion.chunk",
    "created": 1744338809,
    "choices": [
        {
            "delta": {
                "type": "search_queries",
                "search_queries": [
                    "今天上海天气如何"
                ]
            },
            "logprobs": null,
            "finish_reason": null,
            "index": 0
        }
    ]
}

{
    "id": "20250411103329715595694rce8eVJf",
    "provider": "OpenRouter:DeepInfra",
    "model": "deepseek/deepseek-v3",
    "object": "chat.completion.chunk",
    "created": 1744338809,
    "choices": [
        {
            "delta": {
                "type": "search_results",
                "search_results": [
                    {
                        "url": "https://new.qq.com/rain/a/20250411A01JF400",
                        "site_name": "腾讯网",
                        "title": "【AI诗象】今日上海天气:春雨缝花,把人间心事,悄悄种成下一个春天",
                        "snippet": "今日上海天气      15-29℃      多云转阴有阵雨或雷雨      今天白天多云,最高气温29℃左右,夜间到周六上午本市有强对流天气,伴有雷雨大风｡周六受冷空气影响,白天气温一路下行,有大风､降温天气｡周日白天风力仍较大｡      下周本市以晴或者多云天气为主,气温逐日上升,周后期的最高气温又将接近或超过30℃｡春季天气变化较快,请及时关注最新的预报信息,合理安排生活和出行｡    "
                    },
                    {
                        "url": "https://www.sohu.com/a/882395913_121976704",
                        "site_name": "搜狐",
                        "title": "2025年4月11日 上海 今日多云 气温适中适合出行_洗车_天气_建议",
                        "snippet": "今天,上海市的天气将以多云为主,气温在18到24摄氏度之间波动｡早高峰时段,气温较为凉爽,适合穿着轻便的外套｡随着阳光的逐渐升温,中午时分气温将达到高点,对于上班族来说,穿着舒适的衬衫和长裤是个不错的选择｡午后可能会有西南风轻拂,给人带来一丝清爽的感觉｡ 展望明天,周六的早晨,气温依然保持在18摄氏度左右,天气同样以多云为主,午后气温有小幅度上升,达到26摄氏度｡这种适中的气温将持续整个周末,预计未来一周,上海的天气大部分时间都将保持相对稳定,偶有小雨,特别是在下周中期｡"
                    },
                    {
                        "url": "https://www.sohu.com/a/882445102_121976704",
                        "site_name": "搜狐",
                        "title": "2025年4月11日 上海 市区天气:晴天转多云,适合外出活动_建议_气温_外套",
                        "snippet": "今天,上海的天气以晴天为主,气温在19°C到26°C之间,白天气温逐渐回升,适合外出｡早晨上班时,气温较为凉爽,建议出门时可以搭配轻便的外套｡到了中午,阳光正好,市区内的温度会达到最高点,使得午餐时分非常适合在户外享用美食｡ 未来天气展望 明天的天气依旧保持良好,早晨出门时气温在20°C左右,适合穿着T恤或者轻便的衬衣｡"
                    },
                    {
                        "url": "https://www.sohu.com/a/882471581_121976704",
                        "site_name": "搜狐",
                        "title": "2025年4月11日 上海市 今日阴雨绵绵,气温适中_天气_建议_未来的",
                        "snippet": "今天,上海市的天气可谓是阴雨绵绵,湿度较高,气温保持在12℃到18℃之间｡早上的出行时间,由于降雨,路面可能会湿滑,建议市民在上下班时尽量选择公共交通,减少驾驶出行的风险｡同时,可随身携带雨具,以备不时之需｡ 未来的天气预报显示,明天早上气温仍然维持在相对较低的水平,大约在13℃到19℃之间,预计会有持续的小雨伴随｡进入未来一周,天气变化不大,周末天气稍有好转,气温可能升高至20℃以上,但仍需注意间歇性降水的影响｡"
                    },
                    {
                        "url": "https://www.sohu.com/a/882446049_121976704",
                        "site_name": "搜狐",
                        "title": "2025年4月11日 上海 今日多云,未来几天温差较大_气温_洗车_天气",
                        "snippet": "今天,上海的天气主要为多云,气温保持在16到22度之间,早晨和晚上会感觉稍显凉爽｡上班高峰期和放学时间段,气温适中,适宜外出通行｡在城市的潮湿环境中,空气湿度略高,可能会让人感到不适,尤其是在早晨出门的时候｡ 明天,即4月12日,上海将迎来一个晴朗的日子｡早晨出门时,气温将会在15度左右,到午后会升高到24度,白天气温上升明显,适合人们外出活动｡"
                    },
                    {
                        "url": "https://www.sohu.com/a/882534214_121976704",
                        "site_name": "搜狐",
                        "title": "2025年4月11日,城市名称:上海,今天晴朗,气温适中_天气_进行_洗车",
                        "snippet": "今天,上海的天气非常宜人,整个城市沐浴在阳光下｡早晨的气温约为15摄氏度,适合出门时穿轻便的春装｡而在中午时分,气温可能升高至22摄氏度,阳光直射,建议大家在户外活动时适当防晒｡傍晚时分,气温降至18摄氏度,非常适合外出散步或约会｡ 明天的天气同样看好,早晨上班时气温在16摄氏度左右,适合穿着舒适的上衣｡预计午后气温将上升至24摄氏度,仍然保持晴朗的天气状态,外出时可以选择轻便的衣物｡"
                    },
                    {
                        "url": "https://www.sohu.com/a/882512474_121976704",
                        "site_name": "搜狐",
                        "title": "2025年4月11日 上海市 今日天气晴,适宜户外活动_建议_气温_洗车",
                        "snippet": "今天,上海市的天气情况预示着一个明媚的春日｡白天的气温预计在18°C到25°C之间,阳光普照,适合外出游玩｡上班高峰时段早晨8点,气温将会在20°C左右,虽然温度适中,但建议乘客注意防晒,做好防护措施｡ 下午时分,气温将逐渐升高,预计在24°C到25°C之间,温度表现良好,晚上的气温将回落至20°C｡未来几天,天气相对稳定,气温在18°C到26°C之间波动,天气晴好,适合计划外出活动的朋友们｡ "
                    },
                    {
                        "url": "https://new.qq.com/rain/a/20250411A01W6W00",
                        "site_name": "腾讯网",
                        "title": "注意!今天夜间到明天上午上海有强对流天气:局部可达大雨伴雷雨大风,暴跌10",
                        "snippet": "申城今天白天多云      早间全市最低气温在10~16℃之间      受锋前增温影响      预计今天的最高气温可达29℃左右      今天一早      浦东和奉贤分别发布了      大雾黄色预警信号      在冷暖空气共同影响      上海今天半夜转阴有阵雨或雷雨      局部地区累积雨量可达大雨      并伴有雷雨大风      白天吹的是东到东南风      风力有3-4级      傍晚起增大至5级阵风6~7级      相对湿度30%-95%      白天注意防晒和补水      晚归记得携带雨具      据上海市气象局预报      今天夜间到周六上午有强对流天气      冷空气周六上午开始影响上海      白天气温一路下行,体感明显变凉变冷      周日早间最低气温降至10度左右      周六和周日上午风力较大      陆地最大阵风7~8级      沿江沿海地区和长江口区8~10级      洋山港区和上海市沿海海面9~11级      周日下午开始风力逐渐减小      强对流天气主要出现在周五的下半夜到周六凌晨,对周六白天出行影响不大｡"
                    },
                    {
                        "url": "https://new.qq.com/rain/a/20250411A01G8E00",
                        "site_name": "腾讯网",
                        "title": "上海天气今晚剧变!减少美国影片进口!男子睡觉1小时智驾狂飙百公里!女子花60万断骨增高!",
                        "snippet": "今天(4月11日)天气:      【晨雾】早晨沿江沿海地区会出现能见度小于500米的浓雾,出行留意哦｡      【强对流天气】白天多云,最高气温29℃左右,最低气温15℃左右｡夜间到明天(4月12日)上午有强对流天气,伴有雷雨大风,雨量中到大雨,西部和北部大雨｡      未来天气:      【周末降温】明天上午,冷空气开始影响本市,白天气温一路下行,体感明显变凉变冷,周日早间最低气温降至10℃左右｡      【大风】周六和周日上午风力较大,陆地最大阵风7-8级,沿江沿海地区和长江口区8-10级,洋山港区和上海市沿海海面9-11级｡周日下午开始,风力逐渐减小｡"
                    },
                    {
                        "url": "https://www.163.com/v/video/VRRTKO2VJ.html",
                        "site_name": "网易",
                        "title": "今日上海天气:15-29,多云转阴有阵雨或雷雨骤雨天气情况天气现象降温天气雷暴天气预报上海市_网易视频",
                        "snippet": "点击按住拖动小窗 关闭 1997-2025 网易公司版权所有 About NetEase 不良信息举报 Complaint "
                    }
                ]
            },
            "logprobs": null,
            "finish_reason": null,
            "index": 0
        }
    ]
}

{
    "id": "20250411103329715595694rce8eVJf",
    "provider": "OpenRouter:DeepInfra",
    "model": "deepseek/deepseek-v3",
    "object": "chat.completion.chunk",
    "created": 1744338809,
    "choices": [
        {
            "delta": {
                "type": "search_indexes",
                "search_indexes": [
                    {
                        "index": 0,
                        "url": "https://new.qq.com/rain/a/20250411A01JF400"
                    },
                    {
                        "index": 1,
                        "url": "https://www.sohu.com/a/882395913_121976704"
                    },
                    {
                        "index": 2,
                        "url": "https://www.sohu.com/a/882445102_121976704"
                    },
                    {
                        "index": 3,
                        "url": "https://www.sohu.com/a/882471581_121976704"
                    },
                    {
                        "index": 4,
                        "url": "https://www.sohu.com/a/882446049_121976704"
                    },
                    {
                        "index": 5,
                        "url": "https://www.sohu.com/a/882534214_121976704"
                    },
                    {
                        "index": 6,
                        "url": "https://www.sohu.com/a/882512474_121976704"
                    },
                    {
                        "index": 7,
                        "url": "https://new.qq.com/rain/a/20250411A01W6W00"
                    },
                    {
                        "index": 8,
                        "url": "https://new.qq.com/rain/a/20250411A01G8E00"
                    },
                    {
                        "index": 9,
                        "url": "https://www.163.com/v/video/VRRTKO2VJ.html"
                    }
                ]
            },
            "logprobs": null,
            "finish_reason": null,
            "index": 0
        }
    ]
}
```
{% endtab %}

{% tab title="请求参数错误" %}
```json
{
    "error": {
        "message": "The parameter `messages.role` specified in the request are not valid: invalid value: ``, supported values are: `system`, `assistant`, `user`, `tool`. Request id: 021744272965931802bd7fc252857e9b80307f5b7e794cb7f19c1 (request id: 20250410161605607865683UCAWaQJj)",
        "type": "BadRequest",
        "param": "messages.role",
        "code": "InvalidParameter"
    }
}
```
{% endtab %}
{% endtabs %}

### 🟠 400 请求有误
