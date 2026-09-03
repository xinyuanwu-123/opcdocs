# 快速入门

## 🎉 欢迎来到 <code class="expression">space.vars.mainname</code> ！

<code class="expression">space.vars.mainname</code> 是一个 AI 模型智能路由云服务平台，通过 <code class="expression">space.vars.mainname</code> 提供的 API 可以调用大语言模型 LLM、多媒体模型和开发者模型与数据模型。针对大语言模型 LLM， <code class="expression">space.vars.mainname</code> 提供统一的 API 接入点，您只需调用一个 API 接口，即可访问全球主流大语言模型（如 GLM、Deepseek、Qwen等，ChatGPT、Claude、Gemini 等是否可接入视情况而定）。且 <code class="expression">space.vars.mainname</code> 完美覆盖 openai 兼容接口及 responses 接口、Anthropic 兼容接口、Google 兼容接口，可在 Claude code、codex、cline、opencode 等实现海内外模型的流畅调用。 <code class="expression">space.vars.mainname</code> 大语言模型 API 接入点带有高可用架构处理能力，针对同一个模型会自动处理上游供应商错误和不稳定状态，出错或不稳定的时候，平台会自动在多个供应商中切换选择最优模型路径，在保障响应质量的同时，帮助您节省调用成本、提升调用稳定性。我们的愿景是成为 AI 经济时代的底层操作系统与超级代工厂，感谢你的加入与我们共同前行！

💡本平台仅提供多云算力集群计算服务，海外模型API案例介绍相关信息和数据仅用于技术比较参考，用户需自主部署合规模型并对调用模型API做好合规检查。

本平台坚决不接逆向、掺水、低质量API，全站API均为原厂集采，货真价实，稳定性超高、速度更快。

***

## 快速上手指南

无论您是开发者、产品经理还是内容创作者，以下是最快的上手路径。请选择符合您需求的方式开始体验。

若您未完成注册，请先完成账号注册。

注册请参考 [账号指南](shi-yong-zhi-nan/zhang-hao-zhi-nan.md)。

***

### 我是开发者，想快速调用 API

💻 **开发者快速接入**

一个账户，一次接入，一次充值，统一调用主流 AI 大语言模型。

平台自动选路 + 成本优化，提升调用效率。

提供完整文档、多语言 SDK 和调用示例。

👉 [**查看开发者快速入门指南**](shi-yong-zhi-nan/kai-fa-zhe-kuai-su-ru-men-zhi-nan.md)

***

### 我是 AI 编程工具使用者，在用 claude code、codex、cline 等编程工具里，想直接调用模型

❇️ **Claude Code 配置使用** <code class="expression">space.vars.mainname</code> （ Anthropic 兼容接口模式）无需环境变量，可在 claude code 的方便快捷使用模型列表里支持的所有 带/v1/messages 标签模型。文档👉： [claude code配置使用文档](shi-yong-zhi-nan/claude-code-pei-zhi-shi-yong-anthropic-jian-rong-jie-kou-mo-shi.md)

❇️ **CodeX 接入 API**（支持 responses 接口）需配置环境变量，可在 codex 里用 Claude、Gemini 和国内大模型。文档👉： [codex配置使用文档](shi-yong-zhi-nan/codex-jie-ru-api.md)

❇️ **Cline Chinese**  <code class="expression">space.vars.mainname</code> 联合资深开发者 leo 基于 cline 打造的 vs code 编码代理，全流程中文界面交互，小白友好，处理复杂任务、适配 vscode 开发习惯。vscode 商店地址👉： [cline chinese配置使用文档](https://marketplace.visualstudio.com/items?itemName=HybridTalentComputing.cline-chinese)

***

### AI Agent 使用者 / 开发者？

🤖 **AI Agent 生态支持**

为了让用户和开发者能够享受使用模型的自由，方便地用上各种大模型， <code class="expression">space.vars.mainname</code> 积极帮助开源项目发展，如：

🔹 [**N8N-Nodes-Shengsuanyun**](shi-yong-zhi-nan/n8nnodesshengsuanyun-an-zhuang-shi-yong-zhi-nan-yi-shang-xian-n8n-she-qu.md) 这是一个为 n8n 工作流自动化平台开发的社区节点，用于集成 <code class="expression">space.vars.mainname</code> LLM API 路由服务。

🔹 [**Openclaw**](https://docs.router.shengsuanyun.com/8561142m0) 顶顶有名的小龙虾就不用过多介绍了吧？

🔹 [**Hermes Agent**](shi-yong-zhi-nan/hermes-agent-zui-xin-ban-jie-ru-api-wen-dang.md) 是 Nous Research 开发的开源自进化 AI Agent，支持持久记忆、自动生成技能、无缝接入任意 OpenAI 兼容提供商。通过 `hermes model` 和 `hermes config` 可快速接入 <code class="expression">space.vars.mainname</code> ，无需修改代码。

🔹 [**langbot**](https://s.apifox.cn/%E7%AE%80%E5%8D%95%E6%98%93%E7%94%A8%E7%9A%84%E5%A4%A7%E6%A8%A1%E5%9E%8B%E5%8D%B3%E6%97%B6%E9%80%9A%E4%BF%A1%E6%9C%BA%E5%99%A8%E4%BA%BA%E5%BC%80%E5%8F%91%E5%B9%B3%E5%8F%B0) 简单易用的大模型即时通信机器人开发平台，几乎可以接入所有 IM 平台（如 QQ / QQ 频道 / Discord / WeChat（微信）/ Telegram / 飞书 / 钉钉 / Slack）

想在您构建的系统中接入？请查看 [开发者快速入门](shi-yong-zhi-nan/kai-fa-zhe-kuai-su-ru-men-zhi-nan.md)。
