# 使用LangBot 接入API

[LangBot](https://github.com/langbot-app/LangBot) 是一个开源的大语言模型原生即时通信机器人开发平台，旨在提供开箱即用的 IM 机器人开发体验，具有 Agent、RAG、MCP 等多种 LLM 应用功能，适配微信、QQ、飞书、钉钉、Discord、Slack等全球主流即时通信平台，并提供丰富的 API 接口，支持自定义开发。结合 <code class="expression">space.vars.mainname</code>**提供的大模型 API 服务**，LangBot 可以快速地接入国内外主流大模型，用户可根据不同场景需求选择合适的模型。以下为完整的配置教程，简单几步即可拥有专属智能助手。

{% stepper %}
{% step %}
### 第一步：获取<code class="expression">space.vars.mainname</code> API Key

#### 获取 API Key

1. 打开<code class="expression">space.vars.mainname</code>控制台（ <code class="expression">space.vars.console</code> ），生成 API 密钥。

#### 获取模型信息

访问模型列表（ <code class="expression">space.vars.modellist</code> ）查看可用模型及详细参数，其中涵盖如 Claude Sonnet 4、Gemini 2.5 Pro 等大厂模型，以及 DeepSeek - R1、Llama3.2 - 3B 等定制模型。
{% endstep %}

{% step %}
### 第二步：部署并配置 LangBot

#### 使用 Docker 部署 LangBot

详细部署步骤可 [参考文档](https://docs.langbot.app/zh/deploy/langbot/docker.html)。确保已安装 Git 和 Docker。

```bash
git clone https://github.com/langbot-app/LangBot
cd LangBot
docker compose up -d
```

> 如果在中国大陆使用，可将 `docker-compose.yaml` 中的镜像替换为：

```bash
docker.langbot.app/langbot-public/rockchin/langbot:latest
```

#### 访问 WebUI

启动后访问：

```
http://127.0.0.1:5300
```

首次运行会提示创建配置文件，请根据提示完成初始化。

#### 配置对话模型

1. 登录 WebUI，进入 **模型配置** 页面。
2. 添加新模型，填写如下信息：

| 字段      | 内容                                                               |
| ------- | ---------------------------------------------------------------- |
| 模型名称    | 从<code class="expression">space.vars.mainname</code>模型列表中选择的模型名称 |
| 模型提供商   | <code class="expression">space.vars.mainname</code> （或生态合作方：胜算云） |
| API Key | 从<code class="expression">space.vars.mainname</code>获取的密钥        |
{% endstep %}

{% step %}
<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### 第三步：接入平台（以钉钉为例）

> 企业微信、飞书、Discord、Telegram、QQ、微信 等更多平台，请参考 [LangBot 文档](https://docs.langbot.app/zh/insight/guide.html)

#### 创建钉钉机器人应用

1. 登录 [钉钉开发者后台](https://open-dev.dingtalk.com/)
2. 进入组织，点击「应用开发」→「创建应用」，填写基本信息。
3. 添加「机器人」能力，完成基础配置并发布。

#### 配置机器人

在「机器人」选项卡中填写相关信息并发布。

在「版本管理」中配置版本号。

在「事件订阅」中选择 **Stream 模式**，无需公网回调地址。

在「凭证与基础信息」中记录：

* Client ID
* Client Secret
* RobotCode
* 机器人名称

#### 配置 LangBot 平台绑定

1. 打开 LangBot WebUI，编辑机器人。
2. 绑定流水线（默认已有 `ChatPipeline`），平台选择 **钉钉**。
3. 编辑流水线，在 AI 能力中选择 **内置 Agent**，并选择此前配置好的<code class="expression">space.vars.mainname</code>模型。

![image-20250821114553729](<../.gitbook/assets/image 20250821114553729.png>)
{% endstep %}

{% step %}
### 第四步：使用机器人

1. 在钉钉搜索机器人名称，点击即可开始聊天。
2. 如需在群聊中使用，可在群设置中点击「添加机器人」，搜索名称添加。
{% endstep %}
{% endstepper %}
