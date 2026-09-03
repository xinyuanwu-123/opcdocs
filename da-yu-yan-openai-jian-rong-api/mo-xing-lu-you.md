# 模型路由

&#x20;<code class="expression">space.vars.mainname</code> 为模型路由提供了两种选择。

## 自动路由

可以通过设置 `auto_route=true` 来实现自动路由。

```json
{
  "model": "bigmodel/glm-4-plus",
  "messages": [{ "role": "user", "content": "请解释量⼦计算的基本原理" }],
  "auto_route": true
}
```

## 提供商路由

当需要使⽤特定供应商的模型时，可以通过设置 `auto_route=false` 并指定 `supplier` 参数来实现。

```json
{
  "model": "bigmodel/glm-4-plus",
  "messages": [{ "role": "user", "content": "请解释量⼦计算的基本原理" }],
  "auto_route": false,
  "supplier": "OpenRouter"
}
```
