# ❇️ Claude Code 桌面版IDE（免登录、超方便、全功能）

> 基于 Claude Code 源码构建的 **图形化桌面客户端**，无需登录 Anthropic 账号，接入<code class="expression">space.vars.mainname</code> API Key 即可立即使用，支持 Windows / macOS 全平台。GitHub： [https://github.com/NanmiCoder/cc-haha](https://github.com/NanmiCoder/cc-haha)

***

## ⬇️ 下载安装包

当前最新版本： **v0.1.8**（2026-04-28 发布）

{% tabs %}
{% tab title="Windows" %}
适用于 Windows 10/11 x64 系统

[**⬇️ 点击下载 Windows 安装包**（.exe · 36.4 MB）](https://pinfans-tec.oss-cn-shanghai.aliyuncs.com/markdown/Claude.Code.Haha_0.1.8_x64-setup.exe)
{% endtab %}

{% tab title="macOS · Apple Silicon（M1 / M2 / M3 / M4 芯片）" %}
适用于搭载 Apple M 系列芯片的 Mac

[**⬇️ 点击下载 macOS Apple Silicon 安装包**（.dmg · 34.5 MB）](https://pinfans-tec.oss-cn-shanghai.aliyuncs.com/markdown/Claude.Code.Haha_0.1.8_aarch64.dmg)
{% endtab %}

{% tab title="macOS · Intel（x64 芯片）" %}
适用于搭载 Intel 芯片的旧款 Mac

[**⬇️ 点击下载 macOS Intel 安装包**（.dmg · 37 MB）](https://pinfans-tec.oss-cn-shanghai.aliyuncs.com/markdown/Claude.Code.Haha_0.1.8_x64.dmg)
{% endtab %}
{% endtabs %}

不知道自己是哪种 Mac？

点击左上角苹果图标 → **关于本机**，在「芯片」或「处理器」一栏查看：

* 显示 **Apple M1 / M2 / M3 / M4** → 下载 Apple Silicon 版
* 显示 **Intel Core** → 下载 Intel 版

***

## 与 <code class="expression">space.vars.mainname</code> 的生态合作

{% stepper %}
{% step %}
### 打开设置页 → 添加服务商 → 选择「 胜算云 」预设（ <code class="expression">space.vars.mainname</code> 生态合作方）
{% endstep %}

{% step %}
### 填入 <code class="expression">space.vars.mainname</code>  API Key
{% endstep %}

{% step %}
### 点击确认，立即开始使用
{% endstep %}
{% endstepper %}

无需手动配置 Base URL，也无需设置任何环境变量。

无需手动配置 Base URL！也无需设置任何环境变量和修改json！

***

## 全流程配置教程

{% stepper %}
{% step %}
### 第一步：安装应用

**Windows** 双击下载好的 `.exe` 安装包，按向导完成安装即可。**macOS** 双击 `.dmg` 文件，将 `Claude Code Haha.app` 拖入 `Applications` 文件夹。首次打开如果提示「已损坏」或「无法验证开发者」，在终端执行：

```bash
xattr -cr /Applications/Claude Code Haha.app
```

然后重新打开应用即可。
{% endstep %}

{% step %}
### 第二步：获取 <code class="expression">space.vars.mainname</code>  API Key

1. 前往控制台： <code class="expression">space.vars.console</code>
2. 创建一个新的 API Key，复制保存
{% endstep %}

{% step %}
### 第三步：在 CC-Haha 中配置 <code class="expression">space.vars.mainname</code>&#x20;

1. 打开 Claude Code Haha 桌面应用
2. 点击左侧边栏底部的 **设置** 图标
3. 进入「服务商」页面，点击右上角「 **添加服务商**」
4. 在预设选项中选择生态合作方 **「胜算云」**（已内置，无需手动填写接口地址）
5. 在「API 密钥」字段填入你的 <code class="expression">space.vars.mainname</code>  API Key（`sk-...` 开头）
6. 模型映射按需调整（默认已配置推荐模型）：
   * 主模型 / Sonnet：`bigmodel/glm-4.6`
   * Haiku：`bigmodel/glm-4-flash.5:thinking`
   * Opus：`bigmodel/glm-5.7`
7. 点击「 **添加**」完成配置

配置完成后，在主界面新建会话，即可开始使用 <code class="expression">space.vars.mainname</code> 驱动的 Claude Code 全功能 AI 编程助手。
{% endstep %}

{% step %}
### 第四步：新建会话开始使用

1. 点击左上角「 **新建会话**」
2. 选择一个本地工作目录（项目文件夹）
3.  在对话框输入你的任务，例如：

    `帮我分析这个项目的代码结构`

    `修复 src/main.ts 中的 bug`

    `给这个函数写单元测试`
4. CC-Haha 会调用<code class="expression">space.vars.mainname</code> API 执行任务，并在界面中展示代码变更、工具调用等详情
{% endstep %}
{% endstepper %}

***

## 常见问题

<details>

<summary>Q：macOS 提示「应用已损坏」怎么办？</summary>

在终端执行以下命令后重新打开：

```bash
xattr -cr /Applications/Claude Code Haha.app
```

</details>

<details>

<summary>Q：API Key 填写后提示认证失败？</summary>

* 确认 Key 是否正确复制（无多余空格）
* 确认 <code class="expression">space.vars.mainname</code> 账号余额是否充足
* 检查选择的模型名称是否在模型列表（<code class="expression">space.vars.modellist</code> ）中存在

</details>

<details>

<summary>Q：支持哪些模型？</summary>

接入 <code class="expression">space.vars.mainname</code> 后，可使用 <code class="expression">space.vars.mainname</code> 支持的全部模型，包括国内千问、豆包（视情况支持 Claude 系列、Gemini 系列、GPT 系列，详细需要看当前情况）等，详见模型列表（<code class="expression">space.vars.modellist</code> ）

</details>

<details>

<summary>Q：与命令行版 Claude Code 有什么区别？</summary>

CC-Haha 桌面版在命令行版全部能力的基础上，额外提供图形化界面、多标签管理、定时任务、IM 远程控制等功能，且无需登录 Anthropic 账号，对国内用户更友好。

</details>

***

## 功能

* 完整的 Ink TUI 交互界面（与官方 Claude Code 一致）
* `--print` 无头模式（脚本 / CI 场景）
* 支持 MCP 服务器、插件、Skills
* 支持自定义 API 端点和模型
* **记忆系统**（跨会话持久化记忆）— [使用指南](https://github.com/NanmiCoder/cc-haha/blob/main/docs/memory/01-usage-guide.md)
* **多 Agent 系统**（多代理编排、并行任务、Teams 协作）— [使用指南](https://github.com/NanmiCoder/cc-haha/blob/main/docs/agent/01-usage-guide.md) | [实现原理](https://github.com/NanmiCoder/cc-haha/blob/main/docs/agent/02-implementation.md)
* **Skills 系统**（可扩展能力插件、自定义工作流）— [使用指南](https://github.com/NanmiCoder/cc-haha/blob/main/docs/skills/01-usage-guide.md) | [实现原理](https://github.com/NanmiCoder/cc-haha/blob/main/docs/skills/02-implementation.md)
* **Channel 系统**（通过 Telegram / 飞书 / Discord 等 IM 远程控制 Agent）— [架构解析](https://github.com/NanmiCoder/cc-haha/blob/main/docs/channel/01-channel-system.md)
* **Computer Use 桌面控制** — [功能指南](https://github.com/NanmiCoder/cc-haha/blob/main/docs/features/computer-use.md) | [架构解析](https://github.com/NanmiCoder/cc-haha/blob/main/docs/features/computer-use-architecture.md)
* **桌面端**（Tauri 2 + React 图形化客户端，多标签多会话）— [文档](https://github.com/NanmiCoder/cc-haha/blob/main/docs/desktop/)
* 降级 Recovery CLI 模式（`CLAUDE_CODE_FORCE_RECOVERY_CLI=1 ./bin/claude-haha`）

***

## 桌面端预览

|                                                                                                                                                                                                                                                  |                                                                                                        |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------ |
| <p><strong>主界面</strong><br><img src="../.gitbook/assets/image preview" alt=""></p>                                                                                                                                                               | <p><strong>代码编辑 &#x26; Diff 视图</strong><br><img src="../.gitbook/assets/image preview (1)" alt=""></p> |
| <p><strong>权限控制 &#x26; AI 提问</strong><br><img src="../.gitbook/assets/image preview (2)" alt=""><br><img src="https://s.apifox.cn/api/v1/projects/6613585/resources/649429/image-preview?onlineShareType=shareDoc&#x26;locale=zh-CN" alt=""></p> | <p><strong>定时任务</strong><br><img src="../.gitbook/assets/image preview (3)" alt=""></p>                |
| <p><strong>IM 适配器（Telegram / 飞书）</strong><br><img src="../.gitbook/assets/image preview (4)" alt=""></p>                                                                                                                                         | <p><strong>原生 Computer Use</strong><br><img src="../.gitbook/assets/image preview (5)" alt=""></p>     |

***

## 更多资源

* GitHub 仓库： [https://github.com/NanmiCoder/cc-haha](https://github.com/NanmiCoder/cc-haha)
* 在线文档： [https://claudecode-haha.relakkesyang.org](https://claudecode-haha.relakkesyang.org/)
* <code class="expression">space.vars.mainname</code>控制台： <code class="expression">space.vars.console</code>
* <code class="expression">space.vars.mainname</code>模型列表： <code class="expression">space.vars.modellist</code>
