# ❇️ Claude Code 配置使用（Anthropic 兼容接口模式）

本模式适用于 **macOS、Linux（含 WSL）、Windows** 用户，目标是让你用 <code class="expression">space.vars.mainname</code>的 Claude 兼容 API 运行官方 Claude Code（无需官方 Anthropic 账号或订阅）。可在 claude 的方便快捷使用<code class="expression">space.vars.mainname</code>支持的所有 `/v1/messages` 模型，Claude Code 对于 `/v1/messages` 本身兼容适配性较好，此模式比传统 openai 兼容接口环境变量配置要更稳定，能解决大部分 openai 兼容接口环境出现的各种报错。

### 前提条件

* 操作系统：macOS 12+ / Linux / Windows 10/11（推荐 WSL2）
* 终端工具：已安装 `curl`（macOS/Linux 自带）或 PowerShell（Windows）
* Git（推荐，用于项目管理）：`git --version`
* <code class="expression">space.vars.mainname</code>账号：登录控制台（ <code class="expression">space.vars.console</code> ）获取 API Key

{% stepper %}
{% step %}
### 第一步：安装官方 Claude Code（推荐原生安装）

**macOS / Linux / WSL**（最推荐）：

**Windows PowerShell**（以管理员身份运行推荐）：

```powershell
irm https://claude.ai/install.ps1 | iex
```

**Windows CMD**：

```cmd
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

**其他可选方式**：

* macOS/Linux Homebrew：`brew install --cask claude-code`
* Windows WinGet：`winget install Anthropic.ClaudeCode`

安装完成后，**关闭并重新打开终端**，让 PATH 生效。**验证安装**：
{% endstep %}

{% step %}
### 第二步：获取<code class="expression">space.vars.mainname</code> API Key

1. 打开浏览器访问 <code class="expression">space.vars.console</code>（或<code class="expression">space.vars.mainname</code>控制台 → API Key 管理）
2. 创建新 Key 或复制已有 Key
3. 复制 Key 并妥善保存
{% endstep %}

{% step %}
### 第三步：配置<code class="expression">space.vars.mainname</code>（推荐持久化方式）

**推荐方式**：使用 `~/.claude/settings.json`（全局生效，推荐）创建或编辑配置文件：

**macOS / Linux**：

**Windows**（PowerShell）：

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude"
@"
{
  "`$schema": "https://json.schemastore.org/claude-code-settings.json",
  "env": {
    "ANTHROPIC_BASE_URL": "https://router.dt-unicom-ai.com/api",
    "ANTHROPIC_AUTH_TOKEN": "你的API_Key_粘贴在这里",
    "ANTHROPIC_MODEL": "bigmodel/glm-4.5",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "bigmodel/glm-4.5",
    "CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS": 1
  },
  "permissions": {
    "allow": ["Bash(*)"]
  }
}
"@ | Out-File -FilePath "$env:USERPROFILE\.claude\settings.json" -Encoding utf8
```

**模型推荐**（在<code class="expression">space.vars.mainname</code>模型列表中复制）：

* `bigmodel/glm-4.5`（主力推荐）
* `bigmodel/glm-4.6`
* `bigmodel/glm-5.1`
{% endstep %}

{% step %}
### 第四步：临时环境变量测试（可选，验证用）

如果你不想立即用配置文件，可以在终端临时设置：

**macOS / Linux**：

**Windows PowerShell**：

```powershell
$env:ANTHROPIC_BASE_URL = "https://router.dt-unicom-ai.com/api"
$env:ANTHROPIC_AUTH_TOKEN = "你的API_Key"
$env:ANTHROPIC_MODEL = "bigmodel/glm-4.5"
```
{% endstep %}

{% step %}
### 第五步：首次运行与测试

1. 进入任意项目目录（推荐新建测试目录）：
2. 启动 Claude Code：
3. 首次运行会显示欢迎信息，直接输入提示测试：

```
你好，请介绍一下你自己，并用 Python 写一个简单的 Hello World 程序。
```

4. 常用命令（进入 Claude Code 后输入）：

* `/help` → 查看所有命令
* `/model` → 查看或切换模型
* `/exit` 或 `Ctrl + C` → 退出
* `/edit 文件名` → 编辑文件
* `/commit` → Git 提交

如果看到模型回复正常（使用<code class="expression">space.vars.mainname</code>的 glm-4-plus 等），则配置成功！

#### 常见问题排查

<details>

<summary>命令未找到</summary>

重新打开终端，或运行 `hash -r` / 重启电脑。

</details>

<details>

<summary>认证失败（401）</summary>

检查 API Key 是否正确复制（无多余空格）。

</details>

<details>

<summary>模型不存在</summary>

确认模型名称完全一致（带 `anthropic/` 前缀）。

</details>

<details>

<summary>网络问题</summary>

确保能访问 `https://router.shengsuanyun.com`。

</details>

<details>

<summary>想禁用遥测</summary>

在 `settings.json` 的 `"env"` 中添加 `"DISABLE_TELEMETRY": "1"`。

</details>

<details>

<summary>权限问题</summary>

首次运行时 Claude Code 会询问是否允许执行命令，建议根据需要批准。

</details>
{% endstep %}

{% step %}
### 第六步：如何更换模型（<code class="expression">space.vars.mainname</code>支持的所有 /v1/messages 模型）

<code class="expression">space.vars.mainname</code>的 Anthropic 兼容接口支持很多模型，包括 **Claude 系列** 和 **GLM-5、Qwen、DeepSeek** 等国产模型。只要模型在<code class="expression">space.vars.mainname</code>平台支持 `/v1/messages`，就可以直接使用。

**1. 最简单实时切换（推荐，进入 Claude Code 后使用）**

启动 `claude` 后，在对话中输入以下命令：

查看当前可用模型：

```
/model
```

切换模型（直接输入）：

```
/model bigmodel/glm-4.5
/model bigmodel/glm-5.6
/model bigmodel/glm-5          # ← 你举例的 GLM-5
/model bigmodel/glm-5-turbo
/model ali/qwen3.5-plus
```

切换后立即生效，下一次回复就会使用新模型。

**2. 启动时指定模型（命令行方式）**

```
claude --model bigmodel/glm-5
```

**3. 永久修改默认模型（修改配置文件）**

编辑 `.claude/settings.json`（项目级）或全局 `settings.json`，把 `"ANTHROPIC_MODEL"` 改成你想要的模型，例如：

```json
"ANTHROPIC_MODEL": "bigmodel/glm-5"
```

保存后，重启 `claude` 生效。
{% endstep %}
{% endstepper %}
