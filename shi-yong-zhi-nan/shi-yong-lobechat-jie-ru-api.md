# 使用LobeChat接入API

{% stepper %}
{% step %}
### 第一步：获取 API Key

#### 获取 API Key

1. 打开<code class="expression">space.vars.mainname</code>控制台（ <code class="expression">space.vars.console</code> )，生成 API 密钥。

#### 获取模型信息

访问模型列表（ <code class="expression">space.vars.modellist</code> ）查看可用模型及详细参数，其中涵盖如 Claude Sonnet 4、Gemini 2.5 Pro 等大厂模型，以及 DeepSeek - R1、Llama3.2 - 3B 等定制模型。
{% endstep %}

{% step %}
### 第二步：配置 LobeChat

1.  运行LobeChat，选择 `应用设置` - `AI服务商`

    ![image.png](https://s.apifox.cn/api/v1/projects/6613585/resources/570193/image-preview?onlineShareType=shareDoc\&locale=zh-CN) ![image.png](https://s.apifox.cn/api/v1/projects/6613585/resources/570194/image-preview?onlineShareType=shareDoc\&locale=zh-CN)
2.  选择 `添加自定义服务商`

    ![image.png](<../.gitbook/assets/image preview (7)>)
3.  创建自定义 AI 服务商，填写相关信息

    服务商ID，自选填入

    服务商名称，自选填入

    请求格式，选择 `OpenAI`

    代理地址， [https://router.shengsuanyun.com/api/v1](https://router.shengsuanyun.com/api/v1)

    API Key，获取参考第一步

    ![image.png](https://s.apifox.cn/api/v1/projects/6613585/resources/570197/image-preview?onlineShareType=shareDoc\&locale=zh-CN)
4.  模型配置

    获取模型列表

    ![image.png](https://s.apifox.cn/api/v1/projects/6613585/resources/570198/image-preview?onlineShareType=shareDoc\&locale=zh-CN) ![image.png](https://s.apifox.cn/api/v1/projects/6613585/resources/570199/image-preview?onlineShareType=shareDoc\&locale=zh-CN)

    打开 `使用客户端请求模式`

    ![image.png](https://s.apifox.cn/api/v1/projects/6613585/resources/570201/image-preview?onlineShareType=shareDoc\&locale=zh-CN)
{% endstep %}
{% endstepper %}
