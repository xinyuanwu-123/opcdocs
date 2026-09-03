# ❇️ OpenClaw 最新版接入API 文档

**文档版本**：2026 年 4 月（基于 OpenClaw 最新版 `openclaw@latest`）

**适用对象**：OpenClaw 最新版用户（Node.js 22+ / 24 推荐）

**特性**：提供统一的 OpenAI 兼容接口（/api/v1），一个 Key 可调用数百种模型，支持 Chat Completions 协议。

### 1. 前置准备

{% stepper %}
{% step %}
### 安装最新版 OpenClaw

```bash
npm install -g openclaw@latest

# 或使用中文优化版（推荐国内用户）
npm install -g @qingchencloud/openclaw-zh@latest
```

验证：`openclaw --version`
{% endstep %}

{% step %}
### 获取<code class="expression">space.vars.mainname</code>API Key

访问 <code class="expression">space.vars.mainname</code>控制台（ <code class="expression">space.vars.console</code> ）生成 API Key（格式通常为 `sk-...`）。
{% endstep %}

{% step %}
### 确认 Node.js 环境

```bash
node -v   # 需 >= 22.14 或 24+
```
{% endstep %}
{% endstepper %}

### 2. 初始界面引导配置（推荐新手：`openclaw onboard`）

OpenClaw 最新版提供交互式向导，一键完成模型、渠道、Gateway 配置。

{% stepper %}
{% step %}
### 运行命令

```bash
openclaw onboard
```
{% endstep %}

{% step %}
### 向导步骤（中文界面会更友好）

**安全警告**：阅读后输入 `Yes` 继续。

**配置模式**：推荐选择 `QuickStart`（快速开始）。

**模型提供商选择**：

若出现 **“**<code class="expression">space.vars.mainname</code> **(国产模型)”** 或 **“Shengsuanyun”** 选项，直接选择（最新中文版已内置预设）。

若无预设，选择 **Custom Provider** → **OpenAI Compatible**。

**填写**<code class="expression">space.vars.mainname</code>**参数**（向导会提示）：

Base URL：<code class="expression">space.vars.baseurl</code>/v1

API Key：粘贴你的<code class="expression">space.vars.mainname</code> Key

Model ID：输入平台支持的模型 ID（示例：`deepseek/deepseek-v3`、`deepseek/deepseek-chat`、`ali/qwen-max` 等，具体以<code class="expression">space.vars.mainname</code>模型列表为准）

**默认 Agent 模型**：选择刚配置的 `shengsuanyun/模型ID`。

**渠道配置**（可选）：Telegram、飞书、Discord、WhatsApp 等，按提示输入 Token。

**完成**：向导自动生成 `~/.openclaw/openclaw.json` 并验证连接。
{% endstep %}

{% step %}
### 启动验证

```bash
openclaw gateway status
openclaw dashboard     # 打开浏览器访问 http://127.0.0.1:18789
```

**提示**：向导结束后已可直接使用。若想后续修改，继续第 3 节。
{% endstep %}
{% endstepper %}

### 3. 使用 `openclaw config` 打开配置面板修改（进阶/二次配置）

向导完成后，可随时通过以下方式打开配置面板进行修改：

#### 方式一：交互式配置面板（推荐）

```bash
openclaw config
```

进入可视化/命令行交互面板。

选择对应模块（Models → Providers → shengsuanyun）。

修改 Base URL、API Key、默认模型等。

保存后自动校验配置。

#### 方式二：CLI 快捷命令（快速修改）

```bash
# 设置/修改 Provider
openclaw config set models.providers.shengsuanyun.baseUrl "https://router.dt-unicom-ai.com/api/v1"
openclaw config set models.providers.shengsuanyun.apiKey "sk-你的API Key"
openclaw config set models.providers.shengsuanyun.api "openai-completions"

# 设置默认 Agent 使用该模型
openclaw config set agents.defaults.model.primary "shengsuanyun/你的模型ID"

# 查看当前配置
openclaw config get models.providers.shengsuanyun
```

#### 方式三：直接编辑配置文件（最灵活）

配置文件路径：`~/.openclaw/openclaw.json`（推荐备份后再编辑）。**完整示例配置片段**（合并到现有 JSON）：

```json
{
    ......
    {
    "auth": {
      "mode": "token",
      "token": "your-api-token"
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "shengsuanyun/bigmodel/glm-5.1"
      },
      "models": {
          "shengsuanyun/bigmodel/glm-5.1": {}
      },
    }
  }
  ...
}
```

**修改后生效**：

```bash
openclaw gateway restart
openclaw config validate   # 校验配置合法性
```

### 4. 测试与验证

1. 打开仪表板：`openclaw dashboard`
2. 在任意已配置渠道（Telegram/飞书等）发送消息测试。
3.  CLI 测试：

    ```bash
    openclaw tui chat
    ```
4.  查看日志：

    ```bash
    openclaw status --deep
    ```

### 5. 常见问题与注意事项

* **Base URL 必须带 `/v1`**：<code class="expression">space.vars.mainname</code>正确地址为 <code class="expression">space.vars.baseurl</code>/v1。
* **模型 ID**：请以<code class="expression">space.vars.mainname</code>模型列表为准（支持 OpenAI 格式的模型均可）。
* **中文版优势**：推荐使用 `@qingchencloud/openclaw-zh@latest`，onboard 向导已内置“胜算云”（<code class="expression">space.vars.mainname</code>生态合作方）预设。
* **多 Provider 并存**：`mode: "merge"` 可同时保留官方模型和<code class="expression">space.vars.mainname</code>。
* **更新 OpenClaw**：`npm update -g openclaw@latest` 后重新运行 `openclaw onboard` 或 `openclaw config` 即可。
* **安全建议**：生产环境建议使用 `gateway.auth.mode: "token"` 并限制访问来源。

配置完成后，你即可通过<code class="expression">space.vars.mainname</code>的高性价比模型在 OpenClaw 中实现稳定、快速的 AI 智能体体验。如遇问题，可运行 `openclaw doctor` 进行诊断。祝使用愉快！如需更多 Skill 或多 Agent 配置，可在 OpenClaw Dashboard 中继续探索。
