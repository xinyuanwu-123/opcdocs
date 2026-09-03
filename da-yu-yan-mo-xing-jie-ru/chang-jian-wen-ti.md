# 常见问题

<details>

<summary> <code class="expression">space.vars.mainname</code> Router怎样提升AI编码效率？</summary>

&#x20;<code class="expression">space.vars.mainname</code> 为您精选顶尖编程大模型，验证参数真实，杜绝虚标漏标，并通过智能算力调度 + 海外直连等技术，确保API使用稳定流畅，无卡顿、限流或掉线。您无需为海外账号难开、缴费麻烦，或API网关乱扣费、数据隐患等常见困扰而烦恼，一次充值畅用多款大模型，尽享沉浸式编程！

</details>

<details>

<summary> <code class="expression">space.vars.mainname</code> Router的价格是怎么计算的？</summary>

&#x20;<code class="expression">space.vars.mainname</code> Router采用先充值、后扣费模式。我们为每款支持的大模型明确标示供应商每百万输入/输出tokens的价格，并清晰列出上下文长度、参数数量等关键指标——这些直接影响推理性能和费用，却常被其他平台漏标或虚标，连同所有额外收费项目一并透明呈现。

每次请求时，我们根据供应商处理的tokens总数计算成本，加收10%的平台费（含税），从您的余额扣除。您可通过“使用记录”查看完整历史和扣费详情。

</details>

<details>

<summary>怎样使用 <code class="expression">space.vars.mainname</code> Router？</summary>

请先创建账户并充值，余额会实时显示于充值页面。使用API时，我们将从余额中扣除请求费用。每款大模型的参数和价格详情，可在 <code class="expression">space.vars.mainname</code> 首页或“所有大模型”页面查看。

充值后，您可创建API密钥并启用API。如需代码示例及更多指引，请参阅快速入门指南。

</details>

<details>

<summary>使用 <code class="expression">space.vars.mainname</code> Router遇到问题该怎么办？</summary>

除查阅“常见问题”、“使用指南”等在线文档外，工作时间您可联系人工客服获取即时解答，非工作时间可加入 <code class="expression">space.vars.mainname</code> Router飞书群，向群主及AI编程爱好者提问。 <code class="expression">space.vars.mainname</code> Router由专注分布式智能算力与大模型一体化云平台的技术团队负责产品研发和运营，我们认真对待您的每一个问题。

</details>

<details>

<summary> <code class="expression">space.vars.mainname</code> Router提供免费大模型和免费试用吗？</summary>

当前市场上的免费大模型普遍存在限速或限流，无法满足AI编程需求。因此， <code class="expression">space.vars.mainname</code> Router暂不提供免费模型。一旦出现符合我们严选标准的优质编程大模型，我们将立即提供支持。

&#x20;<code class="expression">space.vars.mainname</code> Router会不定期为新用户提供小额试用额度，解锁平台全部功能。请关注我们的社交媒体，获取最新通知。

</details>

<details>

<summary> <code class="expression">space.vars.mainname</code> Router记录哪些用户数据？</summary>

&#x20;<code class="expression">space.vars.mainname</code> Router仅记录基本请求元数据（时间戳、所用模型、token数），不记录提示词或生成结果。

未来，我们将推出可选设置，用户可选择记录提示词与生成结果，享受1%使用费用折扣。

</details>

<details>

<summary> <code class="expression">space.vars.mainname</code> Router会上线更多大模型吗？</summary>

我们致力于支持所有符合 <code class="expression">space.vars.mainname</code> Router严选标准的编程大模型。如需推荐或请求支持特定大模型，请通过在线客服或 <code class="expression">space.vars.mainname</code> Router飞书群直接联系我们。

</details>

<details>

<summary>如何获取我使用的推理tokens用量？</summary>

在流式响应中，您可以通过设置 `stream_options.include_usage = true` 在最后⼀个响应块中

获取token使⽤情况：

```json
{
  "stream": true,
  "stream_options": {
    "include_usage": true
  },
  "messages": [{"role": "user", "content": "简短介绍一下大模型"}],
  "model": "deepseek-v3"
}
```

最后一个块中将包含您的token使用信息：

```json
{
  "id": "chatcmpl-123456",
  "object": "chat.completion.chunk",
  "choices": [...],
  "usage": {
    "prompt_tokens": 15,
    "completion_tokens": 120,
    "total_tokens": 135
  }
}
```

</details>

<details>

<summary>参数设置时，有没有推荐的最佳实践参数？</summary>

1. **设置合理的max\_tokens**: 根据需求设置合适的token上限，避免生成过长或过短的响应。
2. **调整temperature参数**: 对于创意类任务可以使用较高温度，对于事实类任务使用较低温度。
3. **使用流式响应**: 对于交互式应用，使用流式响应可以提升用户体验。
4. **利用联网搜索**: 需要最新信息时启用联网搜索功能。
5. **指定供应商**: 当需要特定模型能力时，可以指定具体的供应商。

</details>

<details>

<summary>如何获取API密钥？</summary>

请查看控制台-API密钥。

</details>

<details>

<summary>流式响应如何解析？</summary>

流式响应使用SSE(Server-Sent Events)格式，每行以"data: "开头，需要按行解析JSON数据。

</details>

<details>

<summary>如何处理超时问题？</summary>

对于复杂查询，建议设置较长的客户端超时时间，通常为60-120秒。

</details>

<details>

<summary>如何有效控制API调用成本？</summary>

合理设置max\_tokens，并根据实际需求选择合适的模型。

</details>
