# ❇️ Claude 桌面端免登录接入API

通过配置第三方推理（Third-Party Inference），您无需登录 Claude 官方账号，即可将 Claude Desktop 转化为“<code class="expression">space.vars.mainname</code>”专属的桌面客户端。

配置生效后，模型调用将直接走<code class="expression">space.vars.mainname</code>的 API 接口，不再消耗 Claude 官方订阅额度（扣除<code class="expression">space.vars.mainname</code>账户内的余额）。

同时，您可以在原生桌面端中完美体验 Cowork、Projects、Artifacts 等高阶功能，享受极致流畅的桌面 AI 体验。

## ⚠️ 重要前置须知

在开始配置前，请务必确认以下事项：

1. **需使用最新版客户端**：旧版本可能没有开发者模式入口，请确保下载最新版。
2. **保持未登录状态**：配置前不需要登录 Claude 官方账号。如果已经登录，建议先在软件内退出登录（Log out）后再进行配置。
3. **接口兼容性**：Claude Desktop 仅支持 Anthropic 格式的接口，<code class="expression">space.vars.mainname</code>已提供完全兼容的规范网关地址，请直接使用后文提供的填入即可。

## 一、下载与安装

1. **访问官网下载**：请在浏览器打开 [Claude 官方下载页](https://claude.ai/download)。
2.  **根据系统安装**：

    **Windows 用户**：下载 `.exe` 文件，双击运行，一路点击“下一步”完成安装。

    **macOS 用户**：下载 `.dmg` 文件，双击打开后，将 Claude 图标拖拽至“应用程序 (Applications)”文件夹即可。

## 二、核心配置指南

{% stepper %}
{% step %}
### 第一步：开启开发者模式 (Developer Mode)

首次打开 Claude Desktop（停留在未登录界面），需要唤出隐藏的开发者菜单。

#### Windows 与常规 macOS 操作

1. 留意控制台顶部菜单栏。(提示：若因界面焦点问题不易点击，可按键盘 Tab 键切到左上角区域，再按回车展开菜单)
2. 点击 **Help（帮助） -> Troubleshooting（疑难解答）**。
3. 在展开的二级菜单中，点击 **Enable Developer Mode（启用开发者模式）**。

![aa182af6-6912-4959-817e-1885dd2eb8e8.png](<../.gitbook/assets/image preview (12)>)

💡 **macOS 专属快捷键操作**（如鼠标无法唤出菜单）：

1. 保持 Claude Desktop 为当前激活窗口。
2. 直接按 **Cmd + Shift + ?** 组合键唤起 “Help (帮助)” 菜单。
3. 使用键盘 **上下方向键** 移动到 Troubleshooting，按 **右方向键** 展开子菜单。
4. 选中 **Enable Developer Mode**，点击 Enable。

![71bf6820-323c-45e7-9277-741578690dc7.png](<../.gitbook/assets/image preview (13)>) ![d4defbc5-42fd-4814-87c9-3071df74bf35.png](<../.gitbook/assets/image preview (14)>)

开启成功后，顶部菜单栏会新增一个稳定的 **Developer（开发者）** 选项。
{% endstep %}

{% step %}
### 第二步：配置<code class="expression">space.vars.mainname</code>API 节点

1. 点击顶部新出现的 **Developer** 菜单。
2. 选择 **Configure Third-Party Inference…（配置第三方推理）**。
3. 在弹出的配置窗口中，填写<code class="expression">space.vars.mainname</code>参数如下：

| 配置项                   | 填写内容                     | 说明                                                                          |
| --------------------- | ------------------------ | --------------------------------------------------------------------------- |
| Connection Gateway    | 必选                       | Gateway base URL                                                            |
| Gateway base URL      | `{{space.vars.baseurl}}` | <code class="expression">space.vars.mainname</code>专用安全中转地址，无需写 v1（可直接复制示例） |
| Gateway API key       | `xxxxxx...`              | 粘贴您的<code class="expression">space.vars.mainname</code> API Key             |
| Gateway auth scheme   | (留空)                     | 保持默认，无需填写                                                                   |
| Gateway extra headers | (留空)                     | 保持默认，无需填写                                                                   |

![07ca9c89-1181-44c6-a37c-03abd3d9ae04.png](<../.gitbook/assets/image preview (15)>)

![bada6cb3-08d1-4b38-95a2-fba0d9addaa5.jpg](<../.gitbook/assets/image preview (16)>)

4. 验证参数无误后，点击右下角 **Apply locally（本地应用）** 保存设置。
{% endstep %}

{% step %}
### 第三步：重启与验证

1. **重启软件**：配置完成后，建议完全关闭/退出 Claude Desktop，然后重新打开。
2. **免登录进入工作区**：成功重启后，软件将自动跳过官方账号登录页，直接进入主交互界面。

![e5ed0893-cad4-462c-8df0-e0f46c047061.png](<../.gitbook/assets/image preview (17)>)

3. **连通性测试**：在屏幕下方的输入框（Cowork / Code 等页面）中输入简单测试语，例如：

```
你好，请介绍一下你自己
```

如果模型正常响应，说明<code class="expression">space.vars.mainname</code> API 已成功接管您的桌面端！

![f1f15b63-6a41-4e48-80cd-62fef727cda0.jpg](<../.gitbook/assets/image preview (18)>) ![66cee978-e193-44d9-98ae-86a6e48ebedd.jpg](<../.gitbook/assets/image preview (19)>)
{% endstep %}
{% endstepper %}

## 三、常见问题解答 (FAQ)

<details>

<summary>Q1：找不到 Developer（开发者）菜单怎么办？</summary>

请确认是否已经点击 **Help -> Troubleshooting -> Enable Developer Mode**。

确认后需彻底关闭并重新启动客户端。

检查 Claude Desktop 是否为官方最新版本。

</details>

<details>

<summary>Q2：配置后重启依然进入官方登录页？</summary>

配置尚未生效，检查：

1. 参数填写无误（无空格）
2. 点击 **Apply locally** 保存
3. 彻底退出应用（Mac 用户记得 **Cmd + Q** 完全退出）

</details>

<details>

<summary>Q3：对话时提示“报错”或“无法连接”？</summary>

排查以下关键点：

* API Key：是否完整复制，前后是否带空格
* Base URL：必须以 `https://` 开头
* 账户状态：检查<code class="expression">space.vars.mainname</code>控制台余额及调用权限

</details>

{% hint style="info" %}
此配置仅保存在本地电脑上，不会影响网页版 Claude，也不会上传认证信息。

建议在个人信任的设备上操作。

如遇问题，可加入<code class="expression">space.vars.mainname</code>服务群获取技术支持。
{% endhint %}
