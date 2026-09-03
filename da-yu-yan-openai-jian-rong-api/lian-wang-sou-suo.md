# 联网搜索

| 联网搜索引擎    | 价格/次    |
| --------- | ------- |
| 博查AI搜索    | 0.036 ¥ |
| Tavily 搜索 | 0.008 $ |

&#x20;<code class="expression">space.vars.mainname</code> 提供联网搜索功能。您只需在请求体里增加 `"online_search": true` 即可启用搜索功能。使用 Tavily 搜索可以添加以下参数：

```json
{
    ...
    "online_search": true,
    "online_search_options": {
        "global": true
    },
    ...
}
```

以 `Python` 代码为例，处理带有联网搜索的流式响应。

```python
def handle_streaming_with_search():
    url = "https://router.shengsuanyun.com/api/v1/chat/completions"
    headers = {
        "Content-Type": "application/json",
        "Authorization": "Bearer YOUR_API_KEY"
    }

    data = {
        "model": "deepseek/deepseek-v3",
        "messages": [
            {"role": "user", "content": "今天上海的天气怎么样？"}
        ],
        "stream": True,
        "online_search": True
    }

    response = requests.post(url, headers=headers, json=data, stream=True)
    client = sseclient.SSEClient(response)

    search_queries = []
    search_results = []
    search_indexes = []
    content = ""

    for event in client.events():
        if event.data != "[DONE]":
            chunk = json.loads(event.data)
            if len(chunk["choices"]) > 0:
                delta = chunk["choices"][0].get("delta", {})

                # 处理搜索查询
                if delta.get("type") == "search_queries":
                    search_queries.extend(delta.get("search_queries", []))
                    print("搜索查询:", delta.get("search_queries"))

                # 处理搜索结果
                elif delta.get("type") == "search_results":
                    search_results.extend(delta.get("search_results", []))
                    print("搜索结果:", delta.get("search_results"))

                # 处理搜索索引
                elif delta.get("type") == "search_indexes":
                    search_indexes.extend(delta.get("search_indexes", []))
                    print("搜索索引:", delta.get("search_indexes"))

                # 处理常规内容
                elif delta.get("content"):
                    content_piece = delta.get("content")
                    content += content_piece
                    print(content_piece, end="", flush=True)

    # 处理完成后，可以展示所有获取的信息
    print("\n\n搜索查询:", search_queries)
    print("搜索结果:", search_results)
    print("搜索索引:", search_indexes)
    print("生成内容:", content)
```

要有效利用联网搜索功能，可以遵循以下最佳实践：

* **明确的事实性问题：** 联网搜索对于需要最新信息或特定事实的问题效果最佳，如“今天的天气”、“最新的电影上映信息”等。
* **处理多轮对话：** 在多轮对话中，可以将之前的上下文和新问题一起发送，模型会根据整体上下文进行联网搜索。
* **解析并展示搜索结果：** 客户端应用可以单独展示搜索结果，或者通过格式化模型回复中的引用使其更易读。
