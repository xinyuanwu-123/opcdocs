# 聊天补全

> 向模型提交对话消息，获取回答，用于企业助手、内容生成和应用内问答。

## 从最小对话开始

把用户问题放入 messages，使用 model 指定模型或路由。stream=false 时一次返回完整结果；stream=true 时应用逐段接收内容。工具调用返回的是模型建议执行的函数和参数，实际执行及权限检查由你的应用负责。先跑通普通文本，再按模型能力加入工具、推理和其他可选参数。

## 请求接口

从当前项目复制 Base URL，保留项目和版本路径。本页在该地址后追加 `/chat/completions`，不要重复追加 `/v1`。 项目请求完整路径为 `/api/projects/{project_external_id}/v1/chat/completions`，历史地址为 `/api/v1/chat/completions`。`project_external_id` 是项目对外标识。model 填写项目内可用模型 ID 或同一项目路由 ID。先按[普通成员快速调用](../kai-shi-shi-yong/pu-tong-cheng-yuan-kuai-su-shi-yong.md)准备运行环境与环境变量。

## 核心请求参数

| 参数          | 类型      | 必填 | 说明                    |
| ----------- | ------- | -- | --------------------- |
| model       | string  | 是  | 项目模型 ID 或路由策略 ID      |
| messages    | array   | 是  | 非空对话消息数组，至少包含一条消息     |
| stream      | boolean | 否  | 是否使用流式响应，默认 false     |
| max\_tokens | integer | 否  | 本次生成的最大 Token 数       |
| temperature | number  | 否  | 采样温度，建议与 top\_p 二选一调整 |
| tools       | array   | 否  | 模型可调用的工具列表            |
| reasoning   | object  | 否  | 支持模型的推理模式与预算          |

## 基础请求示例

### Shell

```bash
export SSY_API_KEY="YOUR_PROJECT_API_KEY"
export SSY_BASE_URL="YOUR_PROJECT_BASE_URL" # 从当前项目复制，保留项目和版本路径

curl --fail-with-body --max-time 60 "$SSY_BASE_URL/chat/completions" \
  -H "Authorization: Bearer $SSY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "deepseek/deepseek-v3",
    "messages": [{"role": "user", "content": "你好"}],
    "stream": false
  }'
```

### Python

```python
import os
import requests

response = requests.post(
    f"{os.environ['SSY_BASE_URL']}/chat/completions",
    headers={"Authorization": f"Bearer {os.environ['SSY_API_KEY']}"},
    json={
        "model": "deepseek/deepseek-v3",
        "messages": [{"role": "user", "content": "你好"}],
        "stream": False,
    },
    timeout=60,
)
response.raise_for_status()
print(response.json())
```

### JavaScript

```javascript
const response = await fetch(
  `${process.env.SSY_BASE_URL}/chat/completions`,
  {
    method: "POST",
    headers: {
      "Authorization": `Bearer ${process.env.SSY_API_KEY}`,
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      model: "deepseek/deepseek-v3",
      messages: [{ role: "user", content: "你好" }],
      stream: false,
    }),
  }
);

if (!response.ok) throw new Error(`HTTP ${response.status}: ${await response.text()}`);
console.log(await response.json());
```

## 流式响应

将 stream 设为 true 后按事件流接收增量结果。需要统计用量时，按模型能力配置 stream\_options。

## 供应商与路由

默认由网关自动选择可用渠道。指定供应商或路由策略时，仍需满足当前项目模型范围、角色模型权限和项目状态。

## 项目地址与凭证

项目地址无效返回 HTTP 404；地址与 Key 所属项目不匹配返回 HTTP 403。请使用同一项目复制的 Base URL 和 API Key。完整地址示例：`https://YOUR_GATEWAY_HOST/api/projects/YOUR_PROJECT_EXTERNAL_ID/v1/chat/completions`。

## 响应与错误

成功响应返回 choices、实际模型、供应商和 Token 用量。认证、项目、模型或参数校验失败时按接口错误结构返回。

## 完整 OpenAPI 规范

## OpenAPI Specification

```yaml
openapi: 3.0.1
info:
  title: 企业网关聊天补全 API
  description: 使用同一项目的 Base URL 与 API Key；可选能力按模型和供应商支持范围使用。
  version: 1.0.0
paths:
  /chat/completions:
    post:
      summary: 聊天补全
      deprecated: false
      description: 与AI模型进行多轮对话
      tags:
        - 网关AI模型接口/大语言模型
        - Chat
      parameters:
        - name: HTTP-Referer
          in: header
          description: 用于在Router中排名的应用网站URL
          required: false
          example: https://www.postman.com
          schema:
            type: string
        - name: X-Title
          in: header
          description: 用于在Router中排名的应用名称
          required: false
          example: Postman
          schema:
            type: string
      requestBody:
        required: true
        content:
          application/json:
            schema:
              properties:
                model:
                  type: string
                  description: 请求使用的模型 ID。
                messages:
                  type: array
                  minItems: 1
                  items:
                    $ref: >-
                      #/components/schemas/OpenAI-Chat%20%E8%AF%B7%E6%B1%82%E4%BD%93-Message
                  description: 必填且非空的对话消息数组。
                stream:
                  type: boolean
                  description: 是否使用流式响应。
                  default: false
                online_search:
                  type: boolean
                  description: 是否启用联网搜索功能，需所选模型支持。
                  default: false
                stream_options:
                  $ref: >-
                    #/components/schemas/OpenAI-Chat%20%E8%AF%B7%E6%B1%82%E4%BD%93-%20StreamOptions
                  description: 流式输出相关选项，只有在 stream 参数为 true 时，此参数才有效。
                max_tokens:
                  type: integer
                  description: 限制一次请求中模型生成 completion 的最大 token 数。
                temperature:
                  type: number
                  description: |-
                    采样温度，用于控制模型生成文本的多样性。
                    更高的值，如 0.8，会使输出更随机，而更低的值，如 0.2，会使其更加集中和确定。
                  minimum: 0
                  maximum: 2
                  exclusiveMaximum: true
                top_p:
                  type: number
                  description: |-
                    核采样概率阈值，用于控制模型生成文本的多样性。
                    top_p 越高，生成的文本更多样。反之，生成的文本更确定。
                    由于 temperature 与 top_p 均可以控制生成文本的多样性，因此建议您只设置其中一个值。
                  minimum: 0
                  maximum: 1
                  exclusiveMaximum: true
                stop:
                  oneOf:
                    - type: string
                    - type: array
                      items:
                        type: string
                  description: 模型遇到 stop 字段所指定的字符串时将停止继续生成，这个词语本身不会输出。
                'n':
                  type: integer
                  description: |-
                    生成响应的个数。对于需要生成多个响应的场景（如创意写作、广告文案等），可以设置较大的 n 值。
                    当前仅支持 qwen-plus 、doubao-pro 模型，且在传入 tools 参数时固定为1。
                    设置较大的 n 值不会增加输入 Token 消耗，会增加输出 Token 的消耗。
                  minimum: 1
                  maximum: 4
                frequency_penalty:
                  type: number
                  minimum: -2
                  maximum: 2
                  description: 频率惩罚系数。如果值为正，会根据新 token 在文本中的出现频率对其进行惩罚，从而降低模型逐字重复的可能性。
                presence_penalty:
                  type: number
                  minimum: -2
                  maximum: 2
                  description: 存在惩罚系数。如果值为正，会根据新 token 到目前为止是否出现在文本中对其进行惩罚，从而增加模型谈论新主题的可能性。
                response_format:
                  $ref: >-
                    #/components/schemas/OpenAI-Chat%20%E8%AF%B7%E6%B1%82%E4%BD%93-ResponseFormat
                  description: Response format specification
                tools:
                  type: array
                  items: &ref_0
                    $ref: >-
                      #/components/schemas/OpenAI-Chat%20%E8%AF%B7%E6%B1%82%E4%BD%93-ToolCall
                  description: 模型可以调用的工具列表。
                auto_route:
                  type: boolean
                  default: true
                  description: 是否自动路由模型供应商。
                supplier:
                  type: string
                  description: 指定模型供应商。只有在 `auto_route=false` 时该参数才会生效。
                  enum:
                    - DeepSeek
                    - Bytedance
                    - OpenRouter
                    - Ali
                reasoning:
                  type: object
                  properties:
                    effort:
                      type: string
                      description: 支持允许控制思维链长度模型
                      enum:
                        - low
                        - medium
                        - high
                    max_tokens:
                      type: integer
                      description: 最大推理Token数，控制非OpenAI思考模型思维链长度
                    type:
                      type: string
                      description: |-
                        开启和关闭思考模式，可选参数：
                        **enabled**、**disabled**
                        支持大多数混合推理模型
                  description: 设置思维链模式
                  x-apifox-orders:
                    - effort
                    - max_tokens
                    - type
                  x-apifox-ignore-properties: []
              type: object
              required:
                - model
                - messages
              x-apifox-orders:
                - model
                - messages
                - stream
                - online_search
                - stream_options
                - max_tokens
                - temperature
                - top_p
                - stop
                - 'n'
                - frequency_penalty
                - presence_penalty
                - response_format
                - tools
                - auto_route
                - supplier
                - reasoning
              x-apifox-ignore-properties: []
            examples:
              '1':
                value:
                  model: deepseek/deepseek-v3
                  stream: true
                  messages:
                    - role: user
                      content: 你好
                  stream_options:
                    include_usage: true
                summary: 基础调用
              '2':
                value:
                  model: anthropic/claude-3.7-sonnet
                  messages:
                    - role: user
                      content: 9.8和9.11谁更大
                  auto_route: false
                  supplier: OpenRouter
                summary: 指定供应商
              '3':
                value:
                  stream: true
                  online_search: true
                  messages:
                    - role: user
                      content: 今天上海天气如何
                  stream_options:
                    include_usage: true
                  model: deepseek/deepseek-v3
                summary: 联网搜索
              '4':
                value:
                  stream: true
                  messages:
                    - role: user
                      content:
                        - type: text
                          text: 你好,你是谁
                  stream_options:
                    include_usage: true
                  reasoning:
                    max_tokens: 2048
                  model: google/gemini-2.5-flash-lite-preview
                summary: 思考模式
      responses:
        '200':
          description: 请求成功
          content:
            application/json:
              schema:
                $ref: >-
                  #/components/schemas/OpenAI%20Chat%20%E5%93%8D%E5%BA%94%E4%BD%93
              examples:
                '1':
                  summary: 非流式响应
                  value:
                    id: 20250410143202432620157mDT6DvdC
                    provider: OpenRouter
                    model: anthropic/claude-3.7-sonnet
                    object: chat.completion
                    created: 1744266722
                    choices:
                      - index: 0
                        message:
                          role: assistant
                          content: >-
                            I don't have the ability to check real-time weather
                            information for Shanghai or any other location. To
                            get accurate and current weather information for
                            Shanghai, you could:


                            1. Check a weather website or app such as
                            Weather.com, AccuWeather, or a local Chinese weather
                            service

                            2. Search for "上海天气" (Shanghai weather) on a search
                            engine

                            3. Look at the weather function on your smartphone

                            4. Check local Shanghai news websites


                            If you need current weather information, these
                            sources will provide you with accurate forecasts,
                            temperature, precipitation chances, and other
                            relevant weather data for Shanghai today.
                        finish_reason: stop
                    usage:
                      prompt_tokens: 16
                      completion_tokens: 133
                      total_tokens: 149
                '2':
                  summary: 流式响应
                  value: >-
                    {"id":"20250410152636722129782COrenCqr","provider":"Bytedance","model":"deepseek/deepseek-v3","object":"chat.completion.chunk","created":1744269996,"choices":[{"delta":{"content":"你好","role":"assistant"},"logprobs":null,"finish_reason":null,"index":0}]}


                    {"id":"20250410152636722129782COrenCqr","provider":"Bytedance","model":"deepseek/deepseek-v3","object":"chat.completion.chunk","created":1744269996,"choices":[{"delta":{"content":"！","role":"assistant"},"logprobs":null,"finish_reason":null,"index":0}]}


                    {"id":"20250410152636722129782COrenCqr","provider":"Bytedance","model":"deepseek/deepseek-v3","object":"chat.completion.chunk","created":1744269996,"choices":[{"delta":{"content":"","role":"assistant"},"logprobs":null,"finish_reason":null,"index":0}]}


                    {"id":"20250410152636722129782COrenCqr","provider":"Bytedance","model":"deepseek/deepseek-v3","object":"chat.completion.chunk","created":1744269996,"choices":[{"delta":{"content":"😊","role":"assistant"},"logprobs":null,"finish_reason":null,"index":0}]}


                    ...


                    {"id":"20250410152636722129782COrenCqr","provider":"Bytedance","model":"deepseek/deepseek-v3","object":"chat.completion.chunk","created":1744269996,"choices":[{"delta":{},"logprobs":null,"finish_reason":"stop","index":0}],"usage":{"prompt_tokens":4,"completion_tokens":15,"total_tokens":19}}
                '3':
                  summary: 联网搜索结果
                  value: |-
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
                '4':
                  summary: 请求参数错误
                  value:
                    error:
                      message: >-
                        The parameter `messages.role` specified in the request
                        are not valid: invalid value: ``, supported values are:
                        `system`, `assistant`, `user`, `tool`. Request id:
                        021744272965931802bd7fc252857e9b80307f5b7e794cb7f19c1
                        (request id: 20250410161605607865683UCAWaQJj)
                      type: BadRequest
                      param: messages.role
                      code: InvalidParameter
          headers: {}
          x-apifox-name: ''
        '400':
          description: 参数错误
          content:
            application/json:
              schema:
                type: string
          headers: {}
          x-apifox-name: ''
      security:
        - bearerAuth: []
      x-apifox-folder: 网关AI模型接口/大语言模型
components:
  schemas:
    OpenAI-Chat 请求体-ToolCall:
      type: object
      properties:
        id:
          type: string
          description: 当前工具调用 ID。
        type:
          type: string
          description: 工具类型，当前仅支持function。
        function:
          $ref: >-
            #/components/schemas/OpenAI-Chat%20%E8%AF%B7%E6%B1%82%E4%BD%93-Function
          description: 模型需要调用的函数。
      required:
        - id
        - type
        - function
      x-apifox-orders:
        - id
        - type
        - function
      x-apifox-ignore-properties: []
      x-apifox-folder: ''
    OpenAI-Chat 请求体-Function:
      type: object
      properties:
        name:
          type: string
          description: Function name
        description:
          type: string
          description: Function description
        parameters:
          type: object
          description: Function parameters schema
          properties: {}
          x-apifox-orders: []
          x-apifox-ignore-properties: []
      required:
        - name
      x-apifox-orders:
        - name
        - description
        - parameters
      x-apifox-ignore-properties: []
      x-apifox-folder: ''
    OpenAI-Chat 请求体-ResponseFormat:
      type: object
      properties:
        type:
          type: string
          description: Response format type
          enum:
            - json_schema
            - json_object
        json_schema:
          $ref: >-
            #/components/schemas/OpenAI-Chat%20%E8%AF%B7%E6%B1%82%E4%BD%93-%20FormatJsonSchema
          description: JSON schema specification
      required:
        - type
      x-apifox-orders:
        - type
        - json_schema
      x-apifox-ignore-properties: []
      x-apifox-folder: ''
    OpenAI-Chat 请求体- FormatJsonSchema:
      type: object
      properties:
        name:
          type: string
          description: Schema name
        description:
          type: string
          description: Schema description
        schema:
          type: object
          description: JSON schema definition
          properties: {}
          x-apifox-orders: []
          x-apifox-ignore-properties: []
        strict:
          type: boolean
          description: Whether to enforce strict schema validation
      required:
        - name
      x-apifox-orders:
        - name
        - description
        - schema
        - strict
      x-apifox-ignore-properties: []
      x-apifox-folder: ''
    OpenAI-Chat 请求体- StreamOptions:
      type: object
      properties:
        include_usage:
          type: boolean
          description: 是否在响应中包括 token 的使用信息
      description: 流式输出相关选项
      x-apifox-orders:
        - include_usage
      x-apifox-ignore-properties: []
      x-apifox-folder: ''
    OpenAI-Chat 请求体-Message:
      type: object
      properties:
        role:
          type: string
          enum:
            - system
            - user
            - assistant
            - tool
          description: 该消息的角色。
        content:
          anyOf:
            - type: string
              description: DeepSeek 相关模型仅支持 string 类型的 content
            - description: 文本类型 content
              type: object
              properties: {}
              x-apifox-orders: []
              x-apifox-ignore-properties: []
            - description: 图片类型 content
              type: object
              properties: {}
              x-apifox-orders: []
              x-apifox-ignore-properties: []
            - description: 文件类型 content
              type: object
              properties: {}
              x-apifox-orders: []
              x-apifox-ignore-properties: []
            - type: object
              properties: {}
              description: 视频类型 content
              x-apifox-orders: []
              x-apifox-ignore-properties: []
          description: 该消息的内容。
        name:
          type: string
          description: 为模型提供信息以区分相同角色。
        tool_calls:
          type: array
          items: *ref_0
          description: 模型生成的工具调用。
        tool_call_id:
          type: string
          description: 此消息所回应的工具调用 ID，当 role 为 tool 时必填。
      required:
        - role
        - content
      description: 对话消息。
      x-apifox-orders:
        - role
        - content
        - name
        - tool_calls
        - tool_call_id
      x-apifox-ignore-properties: []
      x-apifox-folder: ''
    OpenAI Chat 响应体:
      type: object
      properties:
        id:
          type: string
          description: 响应ID
        provider:
          type: string
          description: Provider名称
        model:
          type: string
          description: 用户调用模型名称
        object:
          type: string
          description: 对象类型
        created:
          type: integer
          description: 创建时间戳
        choices:
          type: array
          items:
            $ref: >-
              #/components/schemas/OpenAI%20Chat%E5%93%8D%E5%BA%94%E4%BD%93-Choice
          description: 返回消息
        usage:
          $ref: '#/components/schemas/OpenAI%20Chat%20%E5%93%8D%E5%BA%94-%20Usage'
          description: 用量
      required:
        - id
        - provider
        - model
        - object
        - created
        - choices
      x-apifox-orders:
        - id
        - provider
        - model
        - object
        - created
        - choices
        - usage
      x-apifox-ignore-properties: []
      x-apifox-folder: ''
    OpenAI Chat 响应- Usage:
      type: object
      properties:
        prompt_tokens:
          type: integer
          description: 提示词token数
        completion_tokens:
          type: integer
          description: 完成token数
        total_tokens:
          type: integer
          description: 总token数
      x-apifox-orders:
        - prompt_tokens
        - completion_tokens
        - total_tokens
      x-apifox-ignore-properties: []
      x-apifox-folder: ''
    OpenAI Chat响应体-Choice:
      type: object
      properties:
        message:
          description: 生成的文本
          type: object
          properties:
            role:
              type: string
              enum:
                - system
                - user
                - assistant
                - tool
              description: 该消息的角色。
            content:
              anyOf:
                - type: string
                  description: DeepSeek 相关模型仅支持 string 类型的 content
                - description: 文本类型 content
                  type: object
                  properties: {}
                  x-apifox-orders: []
                  x-apifox-ignore-properties: []
                - description: 图片类型 content
                  type: object
                  properties: {}
                  x-apifox-orders: []
                  x-apifox-ignore-properties: []
                - description: 文件类型 content
                  type: object
                  properties: {}
                  x-apifox-orders: []
                  x-apifox-ignore-properties: []
              description: 该消息的内容。
            name:
              type: string
              description: 为模型提供信息以区分相同角色。
            tool_calls:
              type: array
              items: *ref_0
              description: 模型生成的工具调用。
            tool_call_id:
              type: string
              description: 此消息所回应的工具调用 ID，当 role 为 tool 时必填。
            images:
              type: array
              items:
                type: object
                properties:
                  type:
                    type: string
                    description: image_url
                  image_url:
                    type: object
                    properties:
                      url:
                        type: string
                        description: 图片base64
                    required:
                      - url
                    x-apifox-orders:
                      - url
                    x-apifox-ignore-properties: []
                  index:
                    type: integer
                    description: 索引
                required:
                  - type
                  - image_url
                x-apifox-orders:
                  - type
                  - image_url
                  - index
                x-apifox-ignore-properties: []
              description: 图片生成结果
          required:
            - role
            - content
          x-apifox-orders:
            - role
            - content
            - name
            - tool_calls
            - tool_call_id
            - images
          x-apifox-ignore-properties: []
        index:
          type: integer
          description: 选择索引
        finish_reason:
          type: string
          description: 完成原因
      x-apifox-orders:
        - message
        - index
        - finish_reason
      x-apifox-ignore-properties: []
      x-apifox-folder: ''
  securitySchemes:
    bearerAuth:
      description: 使用 Bearer Token 进行身份验证
      scheme: bearer
      type: http
servers:
  - url: '{baseUrl}'
    description: 从当前项目复制完整 Base URL，保留 /v1；path 只追加接口后缀。
    variables:
      baseUrl:
        default: https://gateway.example.invalid/api/projects/YOUR_PROJECT_EXTERNAL_ID/v1
        description: 替换为当前项目完整 Base URL；历史地址使用 https://YOUR_GATEWAY_HOST/api/v1。
security:
  - bearerAuth: []

```
