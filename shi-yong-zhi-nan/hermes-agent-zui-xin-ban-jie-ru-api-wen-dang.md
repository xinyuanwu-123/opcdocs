# ❇️ Hermes Agent 最新版接入API 文档

**文档版本**：2026 年 4 月（基于 Hermes Agent 最新版 v0.9+，Nous Research 官方）

**适用对象**：Hermes Agent 用户（支持 Linux/macOS/Windows，推荐 VPS 或本地服务器运行）

**我们的特性**：提供统一的 OpenAI 兼容接口（<code class="expression">space.vars.baseurl</code>/v1），一个 Key 可调用数百种模型（Kimi、DeepSeek、Qwen、GPT 等），完美适配 Hermes 的自定义 OpenAI 兼容端点。Hermes Agent 是 Nous Research 开发的开源自进化 AI Agent，支持持久记忆、自动生成技能、无缝接入任意 OpenAI 兼容提供商。通过 `hermes model` 和 `hermes config` 可快速接入<code class="expression">space.vars.mainname</code>，无需修改代码。

{% stepper %}
{% step %}
### 前置准备

1. **安装最新版 Hermes Agent**

官方推荐方式（最新安装命令以官网为准）：

```bash
# 推荐一键安装脚本（自动注册 hermes 命令）
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash

# 或从 GitHub 安装

# git clone https://github.com/NousResearch/hermes-agent.git && cd hermes-agent && make install
```

验证：

```bash
hermes --version
```

安装完成后会自动启动 **初始配置向导**（Setup Wizard）。

2. **获取API Key**

访问控制台（ <code class="expression">space.vars.console</code> ）→ 生成 API Key（格式 `sk-...`）。

3. **环境要求**

Node.js / Python / Rust 依赖已由安装脚本处理

推荐服务器运行（支持 Docker / VPS）
{% endstep %}

{% step %}
### 初始界面引导配置（推荐新手：安装后自动向导 或 `hermes model`）

Hermes Agent 最新版提供交互式向导，一键完成模型提供商配置。

1. **运行配置命令**（安装后自动进入，或手动运行）：

```bash
hermes model
```

或

```bash
hermes setup
```

2. **向导步骤**（全中文友好，支持自定义端点）：

**选择 Inference Provider**：

若出现内置提供商列表，选择 **Custom endpoint (self-hosted / VLLM / etc.)**（推荐用于<code class="expression">space.vars.mainname</code>）。

Hermes 会自动验证 `/models` 接口。

**填写参数**（向导提示）：

**API Base URL**：<code class="expression">space.vars.baseurl</code>/v1

**API Key**：粘贴你的<code class="expression">space.vars.mainname</code> Key

**默认 Model ID**：输入<code class="expression">space.vars.mainname</code>支持的模型 ID（示例：`deepseek/deepseek-v3`、`deepseek/deepseek-chat`、`ali/qwen-max`等，具体以<code class="expression">space.vars.mainname</code>模型列表为准）

**确认默认 Agent 模型**：向导会设置为 `custom/你的模型ID`（或直接保存为默认）。

**辅助模型配置**（可选）：可为工具调用、压缩、搜索等设置独立模型（同样支持<code class="expression">space.vars.mainname</code>）。

**完成**：配置自动保存到 `~/.hermes/config.yaml` 和 `.env`，并进行连接验证。

3. **启动验证**：

```bash
hermes status
hermes chat          # 进入 TUI 交互测试
hermes gateway setup # （可选）配置 Telegram / 飞书 / Discord 等消息通道
```

**提示**：向导结束后即可直接使用 Hermes Agent 与<code class="expression">space.vars.mainname</code>模型对话。如需二次修改，继续第 3 节。
{% endstep %}

{% step %}
### 使用 `hermes config` 打开配置面板修改（进阶/二次配置）

向导完成后，可随时修改配置。

#### 方式一：交互式配置面板（推荐）

```bash
hermes config          # 查看当前配置
hermes config edit     # 直接打开 config.yaml 编辑器
hermes model           # 重新进入模型选择向导（推荐修改主模型）
```

#### 方式二：CLI 快捷命令（快速修改）

```bash
# 设置/修改自定义端点
hermes config set model.provider custom
hermes config set model.base_url "https://router.dt-unicom-ai.com/api/v1"
hermes config set model.api_key "sk-你的API Key"
hermes config set model.default "deepseek-v3"   # 或你想要的模型ID

# 查看配置
hermes config get model
```

#### 方式三：直接编辑配置文件（最灵活）

配置文件路径：`~/.hermes/config.yaml`（推荐备份后再编辑）。**完整示例配置片段**（主要部分）：

```yaml
model:
  default: deepseek/deepseek-r1-0528                    # 或 deepseek-chat 等模型ID
  provider: custom
  base_url: https://router.dt-unicom-ai.com/api/v1
  api_key: sk-你的API Key

# 可选：辅助任务使用不同模型
auxiliary:
  vision:
    provider: custom
    base_url: https://router.shengsuanyun.com/api/v1
    api_key: sk-你的API Key
    model: qwen-vl-max
```

**修改后生效**：

```bash
hermes config validate   # 校验配置
hermes restart           # 或 hermes gateway restart
```
{% endstep %}

{% step %}
### 测试与验证

1. **TUI 测试**：

```bash
hermes chat
```

直接对话测试<code class="expression">space.vars.mainname</code>模型。

2. **Gateway 消息通道**（可选，已接入飞书/Telegram 等）：

发送消息测试 Agent 响应。

3. **查看状态**：

```bash
hermes status --deep
hermes doctor          # 诊断工具
```

4. **技能与记忆验证**：Hermes 会自动从对话中生成技能，<code class="expression">space.vars.mainname</code>模型响应越稳定，自我进化越快。
{% endstep %}

{% step %}
### 常见问题与注意事项

**Base URL 必须带 `/v1`**：<code class="expression">space.vars.mainname</code>正确地址为 `https://router.shengsuanyun.com/api/v1`。Hermes 会自动验证 `/models` 接口。

**模型 ID**：必须与<code class="expression">space.vars.mainname</code>控制台实际模型列表一致（支持所有 OpenAI 格式模型）。

**多模型并存**：`provider: custom` 可与 OpenRouter、Nous Portal 等同时使用，互不冲突。

**更新 Hermes Agent**：

```bash
hermes self-update
```

更新后重新运行 `hermes model` 即可。

**安全建议**：API Key 保存在 `~/.hermes/.env`（自动加密处理），生产环境建议使用环境变量或 secret 管理。

**与 OpenClaw 区别**：Hermes 更注重 **自进化循环**（自动生成技能、持久记忆），配置方式更简洁（config.yaml + hermes model）。

**官方参考**：

Hermes Agent 官方文档：[https://hermes-agent.nousresearch.com/docs/](https://hermes-agent.nousresearch.com/docs/)

Hermes GitHub：[https://github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)

配置完成后，Hermes Agent 将通过<code class="expression">space.vars.mainname</code>的高性价比模型实现稳定、持久的自进化体验。越用越聪明，技能自动积累！如遇问题，运行 `hermes doctor` 诊断，或在官方 Discord 求助。祝使用愉快！如需进一步配置技能（Skills）、定时任务或多 Agent，可继续在 `hermes tools` 或 Dashboard 中探索。
{% endstep %}
{% endstepper %}
