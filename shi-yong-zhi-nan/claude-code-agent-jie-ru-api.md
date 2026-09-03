# ❇️ Claude Code / Agent 接入API

{% stepper %}
{% step %}
### 安装配置 Claude Code / Agent

Claude Code / Claude Agent SDK 支持通过环境变量调用 <code class="expression">space.vars.mainname</code> API，请根据操作系统选择正确的设置方式。

```
ANTHROPIC_MODEL={处理复杂任务模型，最好使用claude系列, 例如：bigmodel/glm-4.6 }

ANTHROPIC_DEFAULT_HAIKU_MODEL={处理简单对话模型， 例如：bigmodel/glm-4-flash.5 }

CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS=1
```

模型名称可以在模型列表（ <code class="expression">space.vars.modellist</code> ）中复制。
{% endstep %}

{% step %}
### 前往获取<code class="expression">space.vars.mainname</code>API key

前往控制台，获取你的 API key：

<code class="expression">space.vars.console</code>
{% endstep %}

{% step %}
### 配置环境变量

完成 Claude code / Claude Agent SDK 安装后，有以下 2 种方式设置环境变量。

#### 方式一：使用脚本（首次使用推荐）

在你的终端或 IDE 中运行以下命令，下载一个帮你自动配置环境变量的 shell 脚本，运行即可。

{% tabs %}
{% tab title="Mac" %}
```bash
curl -O "https://shengsuanyun.oss-cn-shanghai.aliyuncs.com/claude-code/claude_code_prod.sh"
chmod +x claude_code_prod.sh
./claude_code_prod.sh
```
{% endtab %}

{% tab title="Windows" %}
```powershell
$scriptUrl = "https://shengsuanyun.oss-cn-shanghai.aliyuncs.com/claude-code/claude_code_install_windows.ps1"

# 下载脚本到本地临时路径
$localPath = "$env:TEMP\claude_code_install_windows.ps1"

Invoke-WebRequest -Uri $scriptUrl -OutFile $localPath

# 执行脚本
& PowerShell -ExecutionPolicy Bypass -File $localPath
```
{% endtab %}
{% endtabs %}

#### 方式二：手动配置

{% tabs %}
{% tab title="Mac / Linux" %}
在 `~/.zshrc` 或 `~/.bashrc` 中加入以下内容：

```bash
export ANTHROPIC_AUTH_TOKEN=你的API密钥
export ANTHROPIC_BASE_URL={{space.vars.homeurl}}/api
export ANTHROPIC_DEFAULT_OPUS_MODEL=bigmodel/glm-4.6
export ANTHROPIC_DEFAULT_SONNET_MODEL=bigmodel/glm-4.6
export ANTHROPIC_MODEL=bigmodel/glm-4.6
export CLAUDE_CODE_SUBAGENT_MODEL=bigmodel/glm-4.6
export ANTHROPIC_DEFAULT_HAIKU_MODEL=bigmodel/glm-4-flash.5
```

执行：

```bash
source ~/.zshrc  # 或 source ~/.bashrc
```
{% endtab %}

{% tab title="Windows" %}
**方法 1：PowerShell（临时）**

```powershell
$env:ANTHROPIC_AUTH_TOKEN="你的皋智荟API密钥"
$env:ANTHROPIC_BASE_URL="https://router.shengsuanyun.com/api"
$env:ANTHROPIC_MODEL="bigmodel/glm-4.6"
$env:ANTHROPIC_DEFAULT_HAIKU_MODEL="bigmodel/glm-4-flash.5"
$env:ANTHROPIC_DEFAULT_OPUS_MODEL="bigmodel/glm-4.6"
$env:ANTHROPIC_DEFAULT_SONNET_MODEL="bigmodel/glm-4.6"
$env:CLAUDE_CODE_SUBAGENT_MODEL="bigmodel/glm-4.6"
```

> 仅在当前 PowerShell 会话中生效，关闭后失效。

**方法 2：PowerShell（永久）**

```powershell
setx ANTHROPIC_AUTH_TOKEN "你的API密钥"
setx ANTHROPIC_BASE_URL "https://router.shengsuanyun.com/api"
setx ANTHROPIC_MODEL "bigmodel/glm-4.6"
setx ANTHROPIC_DEFAULT_HAIKU_MODEL "bigmodel/glm-4-flash.5"
setx ANTHROPIC_DEFAULT_OPUS_MODEL "bigmodel/glm-4.6"
setx ANTHROPIC_DEFAULT_SONNET_MODEL "bigmodel/glm-4.6"
setx CLAUDE_CODE_SUBAGENT_MODEL "bigmodel/glm-4.6"
```

**方法 3：图形界面**

1. 右键 “此电脑” → 属性 → 高级系统设置 → 环境变量
2. 在“用户变量”中依次添加：

`ANTHROPIC_AUTH_TOKEN` → `你的API密钥`

`ANTHROPIC_BASE_URL` → `https://router.shengsuanyun.com/api`

`ANTHROPIC_MODEL` → `bigmodel/glm-4.6`

`ANTHROPIC_DEFAULT_HAIKU_MODEL` → `bigmodel/glm-4-flash.5`

`ANTHROPIC_DEFAULT_OPUS_MODEL` → `bigmodel/glm-4.6`

`ANTHROPIC_DEFAULT_SONNET_MODEL` → `bigmodel/glm-4.6`

`CLAUDE_CODE_SUBAGENT_MODEL` → `bigmodel/glm-4.6`

3. 确认保存
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
### 如何切换使用的模型

我们支持用户通过修改 Claude Code 配置文件修改模型：

* `ANTHROPIC_MODEL`
* `ANTHROPIC_DEFAULT_OPUS_MODEL`
* `ANTHROPIC_DEFAULT_SONNET_MODEL`
* `ANTHROPIC_DEFAULT_HAIKU_MODEL`
* `CLAUDE_CODE_SUBAGENT_MODEL`

运行 claude code 配置脚本后，会在 `~/.claude/settings.json` 目录下创建默认模型配置文件：

```json
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "***",
    "ANTHROPIC_BASE_URL": "https://router.shengsuanyun.com/api",
    "API_TIMEOUT_MS": "3000000",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "bigmodel/glm-4.6",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "bigmodel/glm-4.6",
    "ANTHROPIC_MODEL": "bigmodel/glm-4.6",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "bigmodel/glm-4-flash.5",
    "CLAUDE_CODE_SUBAGENT_MODEL": "bigmodel/glm-4.6"
  }
}
```

可以手动将 **ANTHROPIC\_MODEL** 替换为模型列表（ <code class="expression">space.vars.modellist</code> ）中的模型。
{% endstep %}

{% step %}
### 常见问题

<details>

<summary>claude -p 失败，“tools.3.custom.input_examples： 不允许额外输入”</summary>

**问题描述：**

claude -p 命令（无头模式）在尝试执行任何提示符时会立即以 API 错误 400 失败。**错误信息：**`API Error: 400 {"error":{"message":"{\"message\":\"tools.3.custom.input_examples: Extra inputs are not permitted\"}. Received Model Group=glm-4-5-20250929\nAvailable Model Group Fallbacks=None","type":"None","param":"None","code":"400"}}`

**解决方法：** [https://github.com/anthropics/claude-code/issues/11678](https://github.com/anthropics/claude-code/issues/11678)

</details>

<details>

<summary>system.2.cache_control.ephemeral.scope: Extra inputs are not</summary>

**解决办法**

环境变量或者 setting.json 中配置：

```
CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS=1
```

</details>
{% endstep %}
{% endstepper %}
