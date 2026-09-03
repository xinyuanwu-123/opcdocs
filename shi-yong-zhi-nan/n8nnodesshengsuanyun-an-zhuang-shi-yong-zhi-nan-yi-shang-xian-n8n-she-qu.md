# N8N-Nodes-Shengsuanyun 安装使用指南（已上线n8n社区）

这是一个为 n8n 工作流自动化平台开发的社区节点，用于集成<code class="expression">space.vars.mainname</code> LLM API 路由服务。该项目允许用户在 n8n 工作流中使用生算云的大语言模型服务。

## 项目结构分析

### 1. 基本信息

* **项目名称**: n8n-nodes-shengsuanyun
* **主页**: [https://www.npmjs.com/package/n8n-nodes-shengsuanyun](https://www.npmjs.com/package/n8n-nodes-shengsuanyun)
* **GitHub 仓库**: [https://github.com/shengsuan/n8n-nodes-shengsuanyun](https://github.com/shengsuan/n8n-nodes-shengsuanyun)

### 2. 技术实现点

**主节点 (LmChatShengSuanYun.node.ts)**:

* 实现了 `INodeType` 接口，作为 n8n 的 AI 语言模型节点
* 支持动态加载可用模型列表
* 提供丰富的配置选项：
  * 模型选择（通过 API 动态获取）
  * 温度控制 (0-2)
  * Top-P 采样 (0-1)
  * 频率惩罚 (-2 到 2)
  * 存在惩罚 (-2 到 2)
  * 最大令牌数 (最高 32768)
  * 响应格式 (文本/JSON)
  * 超时设置
  * 重试次数

**聊天模型 (ShengSuanYunChatModel.ts)**:

* 完整实现了 LangChain 兼容的聊天模型接口
* 支持同步和流式响应
* 支持工具调用 (Function Calling)
* 支持批量处理
* 实现了完整的消息格式转换
* 错误处理和重试机制

**凭据管理 (shengSyanYunApi.credentials.ts)**:

* API 密钥认证
* 可配置的基础 URL (默认: <code class="expression">space.vars.baseurl</code>/v1
* 内置凭据测试功能

### 4. 功能特性

**核心功能**:

* 与<code class="expression">space.vars.mainname</code> LLM API 路由服务集成
* 支持多种大语言模型
* 流式和非流式响应
* 工具调用支持
* 批量处理能力

**配置灵活性**:

* 动态模型列表加载
* 丰富的生成参数控制
* 自定义 API 端点
* 响应格式选择

**开发者友好**:

* 完整的 TypeScript 类型定义
* 详细的接口文档
* 错误处理机制
* 调试信息支持

_API 集成方式：_

* 使用 OpenAI 兼容的 API 格式
* 支持 Bearer Token 认证
* 自定义 HTTP 头部标识
* 完整的错误处理

## 前提条件

在开始之前，您需要：

1. 已安装并运行 n8n 平台
2. 拥有<code class="expression">space.vars.mainname</code> 的 API 密钥

{% stepper %}
{% step %}
### 第一步：安装 n8n（如果还没有安装）

{% tabs %}
{% tab title="方法1：使用 npm 安装（推荐）" %}
```bash
npm install n8n -g
```
{% endtab %}

{% tab title="方法2：使用 Docker 安装" %}
```bash
docker run -it --rm --name n8n -p 5678:5678 n8nio/n8n
```
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
### 第二步：安装 N8N-Nodes-Shengsuanyun

**方法1：通过 n8n 界面安装**

1. 启动 n8n：`n8n start`
2. 打开浏览器访问：`http://localhost:5678`
3. 登录 n8n 后，点击右上角的 "设置" 图标
4. 选择 "社区节点"

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

5. 点击 "安装社区节点"

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

6. 输入包名：`n8n-nodes-shengsuanyun`

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

7. 等待安装完成并重启 n8n

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

**方法2：通过命令行安装**

```bash
# 停止 n8n 服务

# 然后运行：
npm i n8n-nodes-shengsuanyun

# 重新启动 n8n
n8n start
```
{% endstep %}

{% step %}
### 第三步：获取 API 密钥

1. 访问<code class="expression">space.vars.mainname</code>控制台（ <code class="expression">space.vars.console</code> ）或 API 管理页面
2. 创建或获取您的 API 密钥
3. 复制下 API 密钥，稍后配置时需要用到
{% endstep %}

{% step %}
### 第四步：配置凭据

1. 新建一个 n8n 工作流，点击加号，输入 shengsuan 搜索出节点

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

2. 打开节点，点击 "新建凭据"

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

3. 填写配置信息：

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

* **API Key**: 输入您的API 密钥
* **Base URL**: 保持默认值 <code class="expression">space.vars.baseurl</code>/v1（通常不需要修改）

4. 若下方出现模型列表则表示连接成功，选择你的想用的模型工作

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## 高级配置选项

**温度设置**：

* 0.1-0.3：更准确、一致的回答
* 0.7-0.9：更有创造性的回答
* 1.0-2.0：非常有创造性但可能不太准确

**其他有用选项**：

* **频率惩罚**: 减少重复内容（-2 到 2）
* **存在惩罚**: 鼓励谈论新话题（-2 到 2）
* **Top P**: 控制词汇多样性（0-1）

## 常见问题解决

<details>

<summary>问题1：节点安装失败</summary>

* 确保 n8n 版本是最新的
* 检查网络连接
* 尝试重启 n8n 服务

</details>

<details>

<summary>问题2：API 连接失败</summary>

* 检查 API 密钥是否正确
* 确认<code class="expression">space.vars.mainname</code>账户有足够余额
* 检查网络环境设置

</details>

<details>

<summary>问题3：模型列表为空</summary>

* 检查 API 密钥
* 确认<code class="expression">space.vars.mainname</code>服务状态正常
* 尝试刷新节点配置

</details>
