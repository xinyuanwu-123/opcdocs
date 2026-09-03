# 使用Cherry Studio接入API

{% stepper %}
{% step %}
### 第一步：获取 <code class="expression">space.vars.mainname</code> API Key

#### 获取 API Key

1. 打开 <code class="expression">space.vars.mainname</code>控制台（ <code class="expression">space.vars.console</code> ）生成 API 密钥。

#### 获取模型信息

访问 模型列表（ <code class="expression">space.vars.modellist</code> ）查看可用模型及详细参数，其中涵盖如 Claude Sonnet 4、Gemini 2.5 Pro 等大厂模型，以及 DeepSeek - R1、Llama3.2 - 3B 等定制模型。
{% endstep %}

{% step %}
### 第二步：配置 Cherry Studio

1. 运行 Cherry Studio，选择设置

![image.png](https://s.apifox.cn/api/v1/projects/6613585/resources/568087/image-preview?onlineShareType=shareDoc\&locale=zh-CN)

2. 选择 `添加`

![image.png](https://s.apifox.cn/api/v1/projects/6613585/resources/568079/image-preview?onlineShareType=shareDoc\&locale=zh-CN)

3. 添加提供商

提供商名称，自选填入

类型选择 `OpenAI`

![image.png](https://s.apifox.cn/api/v1/projects/6613585/resources/569808/image-preview?onlineShareType=shareDoc\&locale=zh-CN)

4. 填写相关信息

API Key 获取参考第一步

`API地址`：[https://router.shengsuanyun.com/api](https://router.shengsuanyun.com/api)

![image.png](https://s.apifox.cn/api/v1/projects/6613585/resources/568083/image-preview?onlineShareType=shareDoc\&locale=zh-CN)

5. 选择 `管理`，添加模型

![image.png](https://s.apifox.cn/api/v1/projects/6613585/resources/568094/image-preview?onlineShareType=shareDoc\&locale=zh-CN)
{% endstep %}
{% endstepper %}
