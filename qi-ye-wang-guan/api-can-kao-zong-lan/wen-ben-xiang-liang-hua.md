# 文本向量化

> 把文本转换为数字向量，用于语义搜索、相似内容匹配和知识库检索。

## 它能解决什么问题

文本向量化不会直接生成问答答案，而是把文本转换成用于比较语义相似度的一组数字。例如把知识库文章和用户问题分别向量化，找出最接近的文章，再交给聊天模型生成回答。同一检索库应保持模型和向量维度一致；更换模型或维度时重新核对索引。

## 请求接口

从当前项目复制 Base URL，并在其后追加 `/embeddings`，不要再次拼接 `/v1`。 项目请求完整路径为 `/api/projects/{project_external_id}/v1/embeddings`，历史地址为 `/api/v1/embeddings`。先设置 SSY\_BASE\_URL 和 SSY\_API\_KEY 环境变量；model 使用该项目中有权限的向量模型 ID，示例模型需按实际可用目录替换。

## 请求参数

| 参数               | 类型                  | 必填 | 说明                 |
| ---------------- | ------------------- | -- | ------------------ |
| model            | string              | 是  | 项目内可用的向量模型 ID      |
| input            | string \| string\[] | 是  | 需要转换的文本或文本数组       |
| encoding\_format | string              | 否  | 向量编码格式             |
| dimensions       | integer             | 否  | 目标向量维度，按所选模型能力填写整数 |

## 请求示例

```bash
curl "$SSY_BASE_URL/embeddings" \
  -H "Authorization: Bearer $SSY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "ali/text-embedding-v3",
    "input": ["如何部署模型", "如何训练模型"]
  }'
```

## 响应与错误

成功响应返回向量数组、模型和 Token 用量。先按 index 将向量与输入文本对应，再检查向量数量和维度。可选参数按所选模型能力使用。项目地址无效返回 HTTP 404，地址与 Key 所属项目不匹配返回 HTTP 403。dimensions 的整数类型参考[阿里云接口说明](https://www.alibabacloud.com/help/zh/model-studio/text-embedding-synchronous-api)，不代表所有模型均支持该参数。

## 完整 OpenAPI 规范

## OpenAPI Specification

```yaml
openapi: 3.0.1
info:
  title: 企业网关文本向量化 API
  description: 使用同一项目的 Base URL 与 API Key；可选能力按模型和供应商支持范围使用。
  version: 1.0.0
paths:
  /embeddings:
    post:
      summary: 文本向量化
      deprecated: false
      description: 将文本转换为向量表示
      tags:
        - 网关AI模型接口/大语言模型
        - Embedding
      parameters:
        - name: HTTP-Referer
          in: header
          description: 用于在胜算云Router中排名的应用网站URL
          required: false
          example: https://www.postman.com
          schema:
            type: string
        - name: X-Title
          in: header
          description: 用于在胜算云Router中排名的应用名称
          required: false
          example: Postman
          schema:
            type: string
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                model:
                  type: string
                  description: |-
                    请求使用的模型 ID。
                    支持的模型ID:
                    - openai/text-embedding-ada-002
                    - openai/text-embedding-3-small
                    - openai/text-embedding-3-large
                    - baai/bge-m3
                    - bytedance/doubao-embedding-large
                    - bytedance/doubao-embedding
                input:
                  oneOf:
                    - type: string
                    - type: array
                      items:
                        type: string
                  description: 输入内容
                encoding_format:
                  type: string
                dimensions:
                  type: integer
                seed:
                  type: number
                temperature:
                  type: number
                frequency_penalty:
                  type: number
                presence_penalty:
                  type: number
              required:
                - model
                - input
              x-apifox-orders:
                - model
                - input
                - encoding_format
                - dimensions
                - seed
                - temperature
                - frequency_penalty
                - presence_penalty
              x--orders:
                - model
                - input
                - encoding_format
                - dimensions
                - seed
                - temperature
                - frequency_penalty
                - presence_penalty
              x--ignore-properties: []
            examples:
              '1':
                value:
                  model: ali/text-embedding-v3
                  input:
                    - 如何使用torchserve部署模型
                    - 怎么使用训练机器学习模型
                summary: 数组 input
              '2':
                value:
                  model: ali/text-embedding-v3
                  input: 如何使用torchserve部署模型
                summary: 字符串 input
      responses:
        '200':
          description: 成功响应（下方向量为缩短后的结构示意，非实际维度）
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/OpenAI%20Embedding%20%E5%93%8D%E5%BA%94'
              example: |-
                {
                    "id": "202504101500559418281XA86wFOO",
                    "provider": "Ali",
                    "model": "ali/text-embedding-v3",
                    "object": "list",
                    "created": 1744268455,
                    "data": [
                        {
                            "object": "embedding",
                            "index": 0,
                            "embedding": [
                                -0.01420350931584835,
                                -0.009542533196508884,
                                -0.046993378549814224,
                                0.0012923178728669882,
                                -0.03646302595734596
                            ]
                        },
                        {
                            "object": "embedding",
                            "index": 1,
                            "embedding": [
                                -0.06518170237541199,
                                -0.04570785164833069,
                                -0.06909951567649841,
                                -0.02899952046573162,
                                -0.07002135366201401
                            ]
                        }
                    ],
                    "usage": {
                        "prompt_tokens": 13,
                        "total_tokens": 13
                    }
                }
          headers: {}
          x-apifox-name: ''
        '400':
          description: 参数错误
          content:
            application/json:
              schema:
                type: string
              example:
                error:
                  message: 'input is empty (request id: 20250410172315544779037X7p0lu5R)'
                  type: ssy_api_error
                  param: ''
                  code: invalid_embedding_request
          headers: {}
          x-apifox-name: ''
      security:
        - bearerAuth: []
      x-apifox-folder: 网关AI模型接口/大语言模型
components:
  schemas:
    OpenAI Embedding 响应:
      type: object
      properties:
        id:
          type: string
          description: 响应ID
        provider:
          type: string
          description: Provider名称
        model:
          type: string
          description: 用户调用模型名称
        object:
          type: string
          description: 对象类型
        created:
          type: integer
          description: 创建时间戳
        data:
          type: array
          items:
            $ref: >-
              #/components/schemas/OpenAI%20Embedding%20%E5%93%8D%E5%BA%94-%20Item
          description: 返回消息
        usage:
          type: object
          properties: {}
          x--orders: []
          x--ignore-properties: []
      x-apifox-orders:
        - id
        - provider
        - model
        - object
        - created
        - data
        - usage
      x--orders:
        - id
        - provider
        - model
        - object
        - created
        - data
        - usage
      x--ignore-properties: []
      x-apifox-folder: ''
    OpenAI Embedding 响应- Item:
      type: object
      properties:
        object:
          type: string
          description: 对象类型
        index:
          type: integer
          description: 条目索引
        embedding:
          type: array
          items:
            type: number
          description: 嵌入向量
      x-apifox-orders:
        - object
        - index
        - embedding
      x--orders:
        - object
        - index
        - embedding
      x--ignore-properties: []
      x-apifox-folder: ''
  securitySchemes:
    bearerAuth:
      description: 使用 Bearer Token 进行身份验证
      scheme: bearer
      type: http
servers:
  - url: '{baseUrl}'
    description: 从当前项目复制完整 Base URL，保留 /v1；path 只追加接口后缀。
    variables:
      baseUrl:
        default: https://gateway.example.invalid/api/projects/YOUR_PROJECT_EXTERNAL_ID/v1
        description: 替换为当前项目完整 Base URL；历史地址使用 https://YOUR_GATEWAY_HOST/api/v1。
security:
  - bearerAuth: []

```
