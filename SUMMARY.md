# Table of contents

* [快速入门](README.md)

## 使用指南

* [账号指南](shi-yong-zhi-nan/zhang-hao-zhi-nan.md)
* [开发者快速入门指南](shi-yong-zhi-nan/kai-fa-zhe-kuai-su-ru-men-zhi-nan.md)
* [❇️ Claude Code 桌面版IDE（免登录、超方便、全功能）](shi-yong-zhi-nan/claude-code-zhuo-mian-ban-ide-mian-deng-lu-chao-fang-bian-quan-gong-neng.md)
* [❇️ Claude Code 配置使用（Anthropic 兼容接口模式）](shi-yong-zhi-nan/claude-code-pei-zhi-shi-yong-anthropic-jian-rong-jie-kou-mo-shi.md)
* [❇️ Claude 桌面端免登录接入API](shi-yong-zhi-nan/claude-zhuo-mian-duan-mian-deng-lu-jie-ru-api.md)
* [❇️ Claude Code / Agent 接入API](shi-yong-zhi-nan/claude-code-agent-jie-ru-api.md)
* [❇️ OpenClaw 最新版接入API 文档](shi-yong-zhi-nan/openclaw-zui-xin-ban-jie-ru-api-wen-dang.md)
* [❇️ OpenCode 接入（桌面版IDE）](shi-yong-zhi-nan/opencode-jie-ru-zhuo-mian-ban-ide.md)
* [❇️ CodeX 接入API](shi-yong-zhi-nan/codex-jie-ru-api.md)
* [❇️ Hermes Agent 最新版接入API 文档](shi-yong-zhi-nan/hermes-agent-zui-xin-ban-jie-ru-api-wen-dang.md)
* [N8N-Nodes-Shengsuanyun 安装使用指南（已上线n8n社区）](shi-yong-zhi-nan/n8nnodesshengsuanyun-an-zhuang-shi-yong-zhi-nan-yi-shang-xian-n8n-she-qu.md)
* [使用ComfyUI 调用API （暂支持同步任务节点，异步任务节点开发中）](shi-yong-zhi-nan/shi-yong-comfyui-tiao-yong-api-zan-zhi-chi-tong-bu-ren-wu-jie-dian-yi-bu-ren-wu-jie-dian-kai-fa-zhon.md)
* [使用LobeChat接入API](shi-yong-zhi-nan/shi-yong-lobechat-jie-ru-api.md)
* [使用LangBot 接入API](shi-yong-zhi-nan/shi-yong-langbot-jie-ru-api.md)
* [使用Cherry Studio接入API](shi-yong-zhi-nan/shi-yong-cherry-studio-jie-ru-api.md)
* [VS Code插件快速入门指南](shi-yong-zhi-nan/vs-code-cha-jian-kuai-su-ru-men-zhi-nan.md)

## loomloom批处理引擎

* [批处理引擎使用教程](loomloom-pi-chu-li-yin-qing/pi-chu-li-yin-qing-shi-yong-jiao-cheng.md)

## 龙虾农场

* [智能体农场](long-xia-nong-chang/zhi-neng-ti-nong-chang.md)

## 企业网关

* [OPC 企业级 AI 大模型网关](qi-ye-wang-guan/opc-qi-ye-ji-ai-da-mo-xing-wang-guan.md)

## 大语言模型接入

* [错误处理](da-yu-yan-mo-xing-jie-ru/cuo-wu-chu-li.md)
* [常见问题](da-yu-yan-mo-xing-jie-ru/chang-jian-wen-ti.md)
* [API 错误代码说明](da-yu-yan-mo-xing-jie-ru/api-cuo-wu-dai-ma-shuo-ming.md)
* [获取APIKey详情](da-yu-yan-mo-xing-jie-ru/huo-qu-apikey-xiang-qing.md)

## 大语言模型-openai兼容api

* ```yaml
  type: builtin:openapi
  props:
    models: true
    downloadLink: false
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: openaiapi
  ```

## 大语言模型-anthropic兼容api

* ```yaml
  type: builtin:openapi
  props:
    models: true
    downloadLink: false
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: anthropic3
  ```

## 大语言模型-google兼容api

* ```yaml
  type: builtin:openapi
  props:
    models: true
    downloadLink: false
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: googleapi
  ```

## 大语言-OpenAi兼容Api

* [联网搜索](da-yu-yan-openai-jian-rong-api/lian-wang-sou-suo.md)
* [模型路由](da-yu-yan-openai-jian-rong-api/mo-xing-lu-you.md)
* [工具调用](da-yu-yan-openai-jian-rong-api/gong-ju-diao-yong.md)
* [聊天补全](da-yu-yan-openai-jian-rong-api/liao-tian-bu-quan.md)
* [文本向量化](da-yu-yan-openai-jian-rong-api/wen-ben-xiang-liang-hua.md)

## 大语言-anthropic兼容api

* [创建消息](da-yu-yan-anthropic-jian-rong-api/chuang-jian-xiao-xi.md)

## 大语言-google兼容api

* [SDK 调用](da-yu-yan-google-jian-rong-api/sdk-diao-yong.md)
* [生成内容](da-yu-yan-google-jian-rong-api/sheng-cheng-nei-rong.md)
* [流式生成内容](da-yu-yan-google-jian-rong-api/liu-shi-sheng-cheng-nei-rong.md)
* [生成内容（Beta）](da-yu-yan-google-jian-rong-api/sheng-cheng-nei-rong-beta.md)
* [流式生成内容（Beta）](da-yu-yan-google-jian-rong-api/liu-shi-sheng-cheng-nei-rong-beta.md)
* [Live API](da-yu-yan-google-jian-rong-api/live-api.md)

## 多媒体模型接入

* [支持的模型（逐步更新）](duo-mei-ti-mo-xing-jie-ru/zhi-chi-de-mo-xing-zhu-bu-geng-xin.md)
* ```yaml
  type: builtin:openapi
  props:
    models: true
    downloadLink: false
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: basejiekou
  ```

## Copy of 多媒体模型接入

* [支持的模型（逐步更新）](copy-of-duo-mei-ti-mo-xing-jie-ru/zhi-chi-de-mo-xing-zhu-bu-geng-xin.md)
* [异步任务提交](copy-of-duo-mei-ti-mo-xing-jie-ru/yi-bu-ren-wu-ti-jiao.md)
* [查询任务状态](copy-of-duo-mei-ti-mo-xing-jie-ru/cha-xun-ren-wu-zhuang-tai.md)
* [同步图像生成](copy-of-duo-mei-ti-mo-xing-jie-ru/tong-bu-tu-xiang-sheng-cheng.md)
* [同步图像编辑](copy-of-duo-mei-ti-mo-xing-jie-ru/tong-bu-tu-xiang-bian-ji.md)
* [同步音频转录](copy-of-duo-mei-ti-mo-xing-jie-ru/tong-bu-yin-pin-zhuan-lu.md)

## 多媒体-阿里

* [Wan-创建万相视频生成任务](duo-mei-ti-ali/wan-chuang-jian-wan-xiang-shi-pin-sheng-cheng-ren-wu.md)
* [Qwenimage-千问图像编辑-同步](duo-mei-ti-ali/qwenimage-qian-wen-tu-xiang-bian-ji-tong-bu.md)
* [Qwenimage-千问图像生成/编辑-异步](duo-mei-ti-ali/qwenimage-qian-wen-tu-xiang-sheng-cheng-bian-ji-yi-bu.md)
* [Paraformer-v2音频转录-异步](duo-mei-ti-ali/paraformerv2-yin-pin-zhuan-lu-yi-bu.md)
* [HappyHorse-创建HappyHorse视频生成任务](duo-mei-ti-ali/happyhorse-chuang-jian-happyhorse-shi-pin-sheng-cheng-ren-wu.md)

## 多媒体-通义千问

* [Wan视频生成](duo-mei-ti-tong-yi-qian-wen/wan-shi-pin-sheng-cheng.md)
* [qwen image 特有参数](duo-mei-ti-tong-yi-qian-wen/qwen-image-te-you-can-shu.md)

## 多媒体-即梦

* ```yaml
  type: builtin:openapi
  props:
    models: true
    downloadLink: false
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: dream
  ```

## 多媒体-火山引擎

* ```yaml
  type: builtin:openapi
  props:
    models: true
    downloadLink: false
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: huoshan
  ```

## 多媒体-可灵

* ```yaml
  type: builtin:openapi
  props:
    models: true
    downloadLink: false
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: cling
  ```

## 多媒体-ali

* ```yaml
  type: builtin:openapi
  props:
    models: true
    downloadLink: false
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: ali
  ```

## 多媒体-腾讯混元

* ```yaml
  type: builtin:openapi
  props:
    models: true
    downloadLink: false
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: tencent
  ```

## 多媒体-google

* ```yaml
  type: builtin:openapi
  props:
    models: true
    downloadLink: false
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: google
  ```

## 多媒体-openai

* ```yaml
  type: builtin:openapi
  props:
    models: true
    downloadLink: false
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: openai
  ```

## 多媒体-豆包

* ```yaml
  type: builtin:openapi
  props:
    models: true
    downloadLink: false
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: doubao
  ```

## 多媒体-minimax

* ```yaml
  type: builtin:openapi
  props:
    models: true
    downloadLink: false
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: minimax
  ```

## 多媒体-vidu

* ```yaml
  type: builtin:openapi
  props:
    models: true
    downloadLink: false
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: vidu
  ```

## 多媒体-runway

* ```yaml
  type: builtin:openapi
  props:
    models: true
    downloadLink: false
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: runway
  ```

## 条款与协议

* [付费充值协议](tiao-kuan-yu-xie-yi/fu-fei-chong-zhi-xie-yi.md)
* [数据安全协议和分析](tiao-kuan-yu-xie-yi/shu-ju-an-quan-xie-yi-he-fen-xi.md)
* [用户充值协议](tiao-kuan-yu-xie-yi/yong-hu-chong-zhi-xie-yi.md)
* [隐私政策](tiao-kuan-yu-xie-yi/yin-si-zheng-ce.md)
* [用户协议](tiao-kuan-yu-xie-yi/yong-hu-xie-yi.md)
* [使用条款](tiao-kuan-yu-xie-yi/shi-yong-tiao-kuan.md)
