# ❇️ CodeX 接入API

## 安装配置 CodeX

CodeX 支持通过环境变量调用<code class="expression">space.vars.mainname</code>API，请根据操作系统选择正确的设置方式。

💡

* 安装成功后可使用 `codex app` 安装 Codex 客户端使用
* 可修改 `~/.codex/config.toml``wire_api` 字段
* `responses` 使用最新 GPT Responses 接口；`chat` 使用 openai 兼容接口

```toml
model = "openai/gpt-5.3-codex"
model_provider = "shengsuanyun"

[model_providers.shengsuanyun]
name = "Shengsuanyun"
base_url = "https://router.shengsuanyun.com/api/v1"
env_key = "SSY_API_KEY"
wire_api = "responses"

[model_providers.shengsuanyun.http_headers]
"HTTP-Referer" = "https://openai.com/zh-Hans-CN/codex/"
"X-Title" = "CodeX"
```

{% stepper %}
{% step %}
### 前往获取<code class="expression">space.vars.mainname</code> API key

前往控制台，获取你的 API key ： <code class="expression">space.vars.console</code>
{% endstep %}

{% step %}
### 配置环境变量

#### Mac / Linux

{% stepper %}
{% step %}
在你的终端或 IDE 中运行以下命令，下载一个帮你自动配置环境变量的 shell 脚本，运行即可

```bash
curl -O "https://shengsuanyun.oss-cn-shanghai.aliyuncs.com/codex/codex_prod.sh"
chmod +x codex_prod.sh
./codex_prod.sh
```
{% endstep %}

{% step %}
之后，需要在环境变量中设置API key

**临时设置（当前终端生效）**

```bash
export SSY_API_KEY={你的API key}
```

**永久设置**

在 `~/.zshrc` 或 `~/.bashrc` 中加入以下内容：

```bash
export SSY_API_KEY={你的API key}
```

执行：

```bash
source ~/.zshrc  # 或 source ~/.bashrc
```
{% endstep %}

{% step %}
现在你可以使用 CodeX 调用<code class="expression">space.vars.mainname</code> API 啦

```bash
codex
```
{% endstep %}
{% endstepper %}

#### Windows

{% stepper %}
{% step %}
在你的终端或 IDE 中运行以下命令，下载一个帮你自动配置环境变量的 windows 脚本，运行即可

```powershell
$scriptUrl = "https://shengsuanyun.oss-cn-shanghai.aliyuncs.com/codex/codex_windows.ps1"

# 下载脚本到本地临时路径
$localPath = "$env:TEMP\codex_windows.ps1"

Invoke-WebRequest -Uri $scriptUrl -OutFile $localPath

# 执行脚本
& PowerShell -ExecutionPolicy Bypass -File $localPath
```
{% endstep %}

{% step %}
之后，需要在环境变量中设置皋智荟 API key

**a. 临时设置（当前终端生效）**

```powershell
$env:SSY_API_KEY="你的API密钥"
```

**b. 永久设置**

```powershell
setx SSY_API_KEY "你的API密钥"
```

**c. 图形界面**

右键 “此电脑” → 属性 → 高级系统设置 → 环境变量

在“用户变量”中添加：

`SSY_API_KEY` → `你的API密钥`

确认保存
{% endstep %}

{% step %}
现在你可以使用 CodeX 调用<code class="expression">space.vars.mainname</code> API 啦

```powershell
codex
```
{% endstep %}
{% endstepper %}
{% endstep %}

{% step %}
### 如何切换使用的模型

运行 CodeX 配置脚本后，会在 `~/.codex/config.toml` 目录下创建默认模型配置文件：

```toml
model = "openai/gpt-5.3-codex"
model_provider = "shengsuanyun"

[model_providers.shengsuanyun]
name = "Shengsuanyun"
base_url = "https://router.shengsuanyun.com/api/v1"
env_key = "SSY_API_KEY"
wire_api = "responses"

[model_providers.shengsuanyun.http_headers]
"HTTP-Referer" = "https://openai.com/zh-Hans-CN/codex/"
"X-Title" = "CodeX"
```

可以手动将 **model** 替换为模型列表中支持的模型。
{% endstep %}
{% endstepper %}
