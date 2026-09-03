# 使用ComfyUI 调用API （暂支持同步任务节点，异步任务节点开发中）

请到 Github 地址下载节点文件： [https://github.com/SSYCloud/comfyui-ssy-syncapi](https://github.com/SSYCloud/comfyui-ssy-syncapi)

一个强大的 ComfyUI 集群节点集合，提供 4 个专用节点访问 banana pro、SeeDream4.5 等多个<code class="expression">space.vars.mainname</code>同步图像生成和处理模型。

## 🌟 主要特性

### 四个专用节点

#### 🌟 SSY Google Generator 同步

支持 Google Gemini 系列模型：

**Google Gemini 2.5 Flash Image** - 最先进的多模态图像生成

**Google Gemini 3 Pro Image** - 高级 Gemini 3 Pro 图像生成

#### 🎨 SSY Doubao Generator 同步

支持 ByteDance Doubao 系列模型：

**Doubao SeeDream 4.5** - 最新字节豆包文生图和图生图

**Doubao SeeDream 4.0** - 字节豆包文生图和图生图

**Doubao SeeDream 3.0 T2I** - 专用文生图模型

**Doubao SeedEdit 3.0 I2I** - 专用图生图模型

#### 🤖 SSY OpenAI Generator 同步

支持 OpenAI 系列模型：

**GPT Image 1** - OpenAI 文生图生成

#### 🔧 SSY Bytedance Processor 火山引擎图片编辑节点 同步

支持图像处理模型：

**ByteDance Image Enhance** - AI 驱动的图像增强

**ByteDance Image Upscale** - 高质量图像放大

### 核心能力

✅ **专用节点设计** - 每个节点对应一个模型系列，参数清晰不混淆

✅ **智能参数隔离** - 每个节点只显示其系列支持的参数

✅ **安全的密钥管理** - API 密钥密文显示，保护隐私

✅ **精确的 API 适配** - 每个系列使用正确的 API 请求格式

✅ **灵活的参数控制** - 控制尺寸、种子、步数、CFG 比例等

✅ **批量生成** - 一次请求生成多张图像

✅ **多种格式支持** - 支持各种宽高比和输出格式

## 📦 安装方式一

下载 zip 插件节点文件，然后复制粘贴到 `ComfyUI\custom_nodes` 文件夹。

## 📦 安装方式二

{% stepper %}
{% step %}
### 导航到你的 ComfyUI 自定义节点目录

```bash
cd ComfyUI/custom_nodes
```
{% endstep %}

{% step %}
### 克隆此仓库

```bash
git clone https://github.com/SSYCloud/comfyui-ssy-syncapi
```
{% endstep %}

{% step %}
### 安装所需依赖

```bash
pip install -r requirements.txt
```
{% endstep %}

{% step %}
### 重启 ComfyUI
{% endstep %}
{% endstepper %}

## 🔑 配置

### API 密钥设置

你需要一个  API 密钥来使用这些节点。从<code class="expression">space.vars.mainname</code>控制台（ <code class="expression">space.vars.console</code> ）获取你的密钥。**三种配置 API 密钥的方式：**

1. **在节点中** - 直接在 `api_key` 参数中输入（输入时自动显示为 \*\*\*）
2. **环境变量** - 设置 `SSY_API_KEY` 环境变量
3. **配置文件** - 首次使用后自动保存到 `config.json`

## 🎯 使用方法

{% stepper %}
{% step %}
### 1️⃣  Google Generator 🌟

**支持模型：**

`ali/qwen-plus-image-preview`

`google/gemini-3-pro-image-preview`

**参数说明：**

**model** - 选择 Gemini 模型

**prompt** - 生成提示词（必需）

**input\_image \~ input\_image11** - 输入图像（最多 12 张，可选）

**api\_key** - API 密钥（输入时显示为 \*\*\*）

**aspect\_ratio** - 生成图片比例（1:1、16:9 等）

**size** - 图像尺寸（1K/2K/4K，仅 gemini-3-pro 支持）

**response\_modalities** - 响应模态（IMAGE 或 TEXT\_IMAGE）
{% endstep %}

{% step %}
### 2️⃣ Doubao Generator 🎨

**支持模型：**

`bytedance/doubao-seedream-4.5`

`bytedance/doubao-seedream-4-0`

`bytedance/doubao-seedream-3.0-t2i`

`bytedance/doubao-seededit-3-0-i2i`

**参数说明：**

**model** - 选择 Doubao 模型

**prompt** - 生成提示词（必需）

**input\_image \~ input\_image9** - 输入图像（最多 10 张，可选）

**api\_key** - API 密钥（输入时显示为 \*\*\*）

**size** - 生成图片尺寸（1024x1024 等）

**n** - 生成图片数量（1-4）

**quality** - 图像质量（standard/hd）

**seed** - 随机种子（仅 3.0 系列支持）

**guidance\_scale** - CFG 引导比例（仅 3.0 系列支持）

**stream** - 流式输出（仅 4.0/4.5 系列支持）

**sequential\_image\_generation** - 组图功能（仅 4.0/4.5 系列支持）

**max\_count** - 组图最大数量（仅 4.0/4.5 系列）

**watermark** - 添加 AI 生成水印

**response\_format** - 返回格式（b64\_json/url）
{% endstep %}

{% step %}
### 3️⃣ OpenAI Generator 🤖

**支持模型：**

`openai/gpt-image-1`

**参数说明：**

**model** - 选择 OpenAI 模型

**prompt** - 生成提示词（必需）

**api\_key** - API 密钥（输入时显示为 \*\*\*）

**size** - 生成图像尺寸（auto、1024x1024 等）

**n** - 生成图片数量（1-10）

**quality** - 图像质量（auto/high/medium/low 等）

**background** - 背景透明度（auto/transparent/opaque）

**output\_format** - 输出格式（png/jpeg/webp）

**output\_compression** - 压缩级别（0-100）

**moderation** - 内容审核级别（auto/low）
{% endstep %}

{% step %}
### 4️⃣ Bytedance Processor 火山引擎图片编辑节点 🔧

**支持模型：**

`bytedance/image_enhance` - 图像增强

`bytedance/image_upscale` - 图像放大

**参数说明：**

**model** - 选择处理模型

**input\_image** - 输入图像（必需）

**api\_key** - API 密钥（输入时显示为 \*\*\*）

**model\_quality** - 超分模型质量（HQ/MQ/LQ，upscale 必需）

**resolution\_boundary** - 目标分辨率（144p 到 2k）

**jpg\_quality** - JPG 质量（0-100）

**result\_format** - 输出格式（0=png, 1=jpeg）
{% endstep %}
{% endstepper %}

## 🔄 模型能力对照表

| 节点           | 模型               | 文生图 | 图生图 | 特殊功能  |
| ------------ | ---------------- | --- | --- | ----- |
| Google 🌟    | Gemini 2.5 Flash | ✅   | ✅   | 多模态   |
| Google 🌟    | Gemini 3 Pro     | ✅   | ✅   | 4K 输出 |
| Doubao 🎨    | SeeDream 4.5     | ✅   | ✅   | 组图功能  |
| Doubao 🎨    | SeeDream 4.0     | ✅   | ✅   | 组图功能  |
| Doubao 🎨    | SeeDream 3.0 T2I | ✅   | -   | 种子控制  |
| Doubao 🎨    | SeedEdit 3.0 I2I | -   | ✅   | 种子控制  |
| OpenAI 🤖    | GPT Image 1      | ✅   | -   | 透明背景  |
| Processor 🔧 | Image Enhance    | -   | -   | AI 增强 |
| Processor 🔧 | Image Upscale    | -   | -   | 高清放大  |

## 🆕 版本更新

### v2.0 - 专用节点架构

✅ **4 个专用节点** - 每个系列独立节点，参数清晰

✅ **API 密钥密文显示** - 输入时显示为 \*\*\*，保护隐私安全

✅ **精确 API 适配** - 每个系列使用正确的请求格式

✅ **智能参数隔离** - 避免参数混淆和冲突

✅ **添加 Doubao 4.5** - 支持最新模型

✅ **多图输入支持** - Google 最多 12 张，Doubao 最多 10 张

### 架构优势

1. **清晰的节点分类** - 在 ComfyUI 中按系列组织
2. **参数自动适配** - 选择节点后只显示相关参数
3. **代码模块化** - 易于维护和扩展
4. **类型安全** - 每个节点有明确的参数类型

## 🐛 故障排除

### 常见问题

<details>

<summary>“未提供 API 密钥”</summary>

确保在任一节点中设置了 API 密钥。

密钥会自动保存供所有节点共享。

</details>

<details>

<summary>“模型需要输入图像”</summary>

图生图模型（如 SeedEdit 3.0 I2I）需要连接输入图像。

图像处理节点必须提供输入图像。

</details>

<details>

<summary>“参数不生效”</summary>

检查模型是否支持该参数。

参数 tooltip 中会说明支持的模型版本。

</details>

<details>

<summary>“找不到节点”</summary>

确保已重启 ComfyUI。

检查节点分类：SSY Cloud/Google、SSY Cloud/Doubao 等。

</details>

## 💡 使用技巧

1. **节点选择** - 根据需求选择对应系列节点
2. **参数优化** - 每个节点只显示相关参数，避免混淆
3. **工作流组织** - 可以在同一工作流中使用多个节点
4. **API 密钥共享** - 所有节点共享同一个 API 密钥配置



## 🔗 链接

[ComfyUI](https://github.com/comfyanonymous/ComfyUI)

## 🙏 致谢

为 ComfyUI 社区开发，提供专业的分类节点访问 <code class="expression">space.vars.mainname</code> 强大的图像生成和处理模型。
