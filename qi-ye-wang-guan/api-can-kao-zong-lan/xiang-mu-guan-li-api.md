# 项目管理 api

> 项目、项目路由、项目模型和项目 API Key 的管理接口。

## 概述

项目管理API用于创建和管理企业网关项目，配置项目的路由、模型和团队成员。每个项目都有独立的配置和API Token。

## 基础信息

* **Base URL**: `YOUR_GATEWAY_MANAGEMENT_BASE_URL`
* **认证方式**: 见下方认证与权限说明
* **请求格式**: JSON
* **响应格式**: JSON

## 认证与权限说明

管理地址使用 `YOUR_GATEWAY_MANAGEMENT_BASE_URL`，与模型调用 Base URL 分开配置。JWT 通过登录 Cookie 或 `x-token` 请求头传递；AccessKey 请求使用下文签名请求头。

开启操作保护后，受保护的写操作需要 JWT 和有效的敏感操作验证授权。收到 `JWT_REQUIRED` 时使用登录态；收到 `STEP_UP_REQUIRED` 时按返回的验证方式完成授权后重试。认证规则见[API 认证与签名方法](/broken/pages/e8167923d9de15f636aa273e8250fa11b46d254a)。

| 操作            | 主账号   | RAM管理员 | RAM成员-Manager        | RAM成员-Viewer |
| ------------- | ----- | ------ | -------------------- | ------------ |
| 创建项目          | ✅     | ✅      | ❌                    | ❌            |
| 查看项目列表        | ✅     | ✅      | ✅                    | ✅            |
| 删除项目          | ✅     | ✅      | ✅ (自己项目的manager)     | ❌            |
| 编辑项目          | ✅     | ✅      | ✅ (自己项目的manager)     | ❌            |
| 管理路由          | ✅     | ✅      | ✅ (项目manager)        | ❌            |
| 查看路由          | ✅     | ✅      | ✅ (项目manager/viewer) | ✅            |
| 管理模型          | ✅     | ✅      | ✅ (项目manager)        | ❌            |
| 查看模型          | ✅     | ✅      | ✅ (项目manager/viewer) | ✅            |
| 生成本人的 API Key | 按项目授权 | 按项目授权  | 按项目授权                | 按项目授权        |
| 管理成员          | ✅     | ✅      | ✅ (项目manager)        | ❌            |

以下查询还支持项目 API Key：`GET /project/list`、`POST /project/router/list`、`POST /project/models/list`、`POST /project/apikey/list`。数据限制在 Key 的项目作用域内，并校验 Key 状态、关联账号及 IP 白名单。账号的企业身份不自动授予其他项目的访问权。

## API接口列表

### 1. 获取项目列表

查询用户有权访问的项目列表。

#### 接口信息

* **Path**: `/project/list`
* **Method**: `GET`
* **权限**: 已认证且具备相应项目访问权限的账号

#### 请求参数（Query参数）

| 参数名           | 类型     | 必填 | 说明                     | 示例      |
| ------------- | ------ | -- | ---------------------- | ------- |
| project\_name | string | 否  | 项目名称                   | xxxxxx  |
| status        | string | 否  | 项目状态 (active, expired) | active  |
| role          | string | 否  | 项目角色 (manager, viewer) | manager |

#### 请求示例

```http
GET /project/list
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx
```

#### 响应示例

```json
{
  "code": 0,
  "data": [
    {
      "id": 35,
      "mainUserName": "lichaowei",
      "projectName": "虚构示例项目 A",
      "projectTime": [
        "2025-12-17",
        "2026-01-17"
      ],
      "teamMembers": [
        {
          "id": 2,
          "name": "junior_li_test",
          "roleId": 13,
          "role": "有限制只查看",
          "type": "member"
        },
        {
          "id": 16,
          "name": "lcw",
          "roleId": 5,
          "role": "无限制管理员",
          "type": "member"
        }
      ],
      "selectedModels": [
        {
          "id": 24,
          "name": "anthropic/claude-3-7-sonnet-official",
          "type": "",
          "inputPrice": 0,
          "outputPrice": 0,
          "provider": "Anthropic",
          "supportModelId": 0,
          "icon": ""
        },
        {
          "id": 1,
          "name": "deepseek/deepseek-r1",
          "type": "",
          "inputPrice": 40000,
          "outputPrice": 160000,
          "provider": "DeepSeek",
          "supportModelId": 0,
          "icon": ""
        },
        {
          "id": 3,
          "name": "deepseek/deepseek-v3",
          "type": "",
          "inputPrice": 5100,
          "outputPrice": 8900,
          "provider": "DeepSeek",
          "supportModelId": 0,
          "icon": ""
        },
        {
          "id": 22,
          "name": "anthropic/claude-3-7-sonnet-vertex",
          "type": "",
          "inputPrice": 12500,
          "outputPrice": 100000,
          "provider": "Anthropic",
          "supportModelId": 0,
          "icon": ""
        },
        {
          "id": 92,
          "name": "google/gemini-3-pro-image-preview",
          "type": "",
          "inputPrice": 50000,
          "outputPrice": 90000,
          "provider": "Google",
          "supportModelId": 0,
          "icon": ""
        },
        {
          "id": 101,
          "name": "deepseek/deepseek-v3.2",
          "type": "",
          "inputPrice": 20000,
          "outputPrice": 30000,
          "provider": "DeepSeek",
          "supportModelId": 0,
          "icon": ""
        },
        {
          "id": 104,
          "name": "deepseek/deepseek-v3.2",
          "type": "",
          "inputPrice": 20000,
          "outputPrice": 40000,
          "provider": "DeepSeek",
          "supportModelId": 0,
          "icon": ""
        }
      ],
      "routerConfigs": [
        {
          "id": 0,
          "name": "负载均衡",
          "mode": "loadBalance",
          "models": [
            {
              "id": 168,
              "model": "qwen/qwen-plus",
              "weight": 10
            },
            {
              "id": 168,
              "model": "qwen/qvq-72b",
              "weight": 10
            },
            {
              "id": 168,
              "model": "qwen/qwen-vl-plus",
              "weight": 80
            }
          ],
          "isConfirmed": false,
          "routeId": "",
          "strategies": null
        },
        {
          "id": 0,
          "name": "故障转移测试",
          "mode": "failover",
          "models": [
            {
              "id": 169,
              "model": "qwen/qwen-plus",
              "weight": 0
            },
            {
              "id": 169,
              "model": "qwen/qvq-72b",
              "weight": 0
            }
          ],
          "isConfirmed": false,
          "routeId": "",
          "strategies": null
        }
      ]
    },
    {
      "id": 36,
      "mainUserName": "lichaowei",
      "projectName": "虚构示例项目 B",
      "projectTime": [
        "2025-12-19",
        "2026-01-19"
      ],
      "teamMembers": [
        {
          "id": 3,
          "name": "junior_lichaowei",
          "roleId": 5,
          "role": "无限制管理员",
          "type": "member"
        },
        {
          "id": 16,
          "name": "lcw",
          "roleId": 11,
          "role": "有限制管理员",
          "type": "member"
        }
      ],
      "selectedModels": [
        {
          "id": 1,
          "name": "deepseek/deepseek-r1",
          "type": "",
          "inputPrice": 40000,
          "outputPrice": 160000,
          "provider": "DeepSeek",
          "supportModelId": 0,
          "icon": ""
        },
        {
          "id": 3,
          "name": "deepseek/deepseek-v3",
          "type": "",
          "inputPrice": 5100,
          "outputPrice": 8900,
          "provider": "DeepSeek",
          "supportModelId": 0,
          "icon": ""
        },
        {
          "id": 44,
          "name": "google/gemini-2.5-flash-image",
          "type": "",
          "inputPrice": 50000,
          "outputPrice": 90000,
          "provider": "Google",
          "supportModelId": 0,
          "icon": ""
        },
        {
          "id": 92,
          "name": "google/gemini-3-pro-image-preview",
          "type": "",
          "inputPrice": 50000,
          "outputPrice": 90000,
          "provider": "Google",
          "supportModelId": 0,
          "icon": ""
        },
        {
          "id": 100,
          "name": "deepseek/deepseek-v3.2-think",
          "type": "",
          "inputPrice": 20000,
          "outputPrice": 30000,
          "provider": "DeepSeek",
          "supportModelId": 0,
          "icon": ""
        },
        {
          "id": 101,
          "name": "deepseek/deepseek-v3.2",
          "type": "",
          "inputPrice": 20000,
          "outputPrice": 30000,
          "provider": "DeepSeek",
          "supportModelId": 0,
          "icon": ""
        },
        {
          "id": 104,
          "name": "deepseek/deepseek-v3.2",
          "type": "",
          "inputPrice": 20000,
          "outputPrice": 40000,
          "provider": "DeepSeek",
          "supportModelId": 0,
          "icon": ""
        }
      ],
      "routerConfigs": [
        {
          "id": 0,
          "name": "路由配置1",
          "mode": "loadBalance",
          "models": [
            {
              "id": 171,
              "model": "deepseek/deepseek-v3.2",
              "weight": 100
            }
          ],
          "isConfirmed": false,
          "routeId": "",
          "strategies": null
        },
        {
          "id": 0,
          "name": "mix1",
          "mode": "mix",
          "models": null,
          "isConfirmed": false
        }
       ]
    }
  ],
  "msg": "获取成功"
}
```

***

### 2. 创建项目

创建一个新的企业网关项目。

#### 接口信息

* **Path**: `/project/create`
* **Method**: `POST`
* **权限**: 主账号、RAM管理员

#### 请求参数

| 参数名            | 类型              | 必填 | 说明     | 示例            |
| -------------- | --------------- | -- | ------ | ------------- |
| projectName    | string          | 是  | 项目名称   | xxxxx         |
| projectTime    | \[]string       | 否  | 项目持续时间 | \[开始时间, 结束时间] |
| teamMembers    | \[]TeamMember   | 是  | 团队成员   |               |
| selectedModels | \[]ModelPrice   | 否  | 选择的模型  |               |
| routerConfigs  | \[]RouterConfig | 是  | 路由配置   |               |

#### 请求示例

```http
POST /project/create
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "mainUserName": "string",
  "projectName": "string",
  "projectTime": [
    "string"
  ],
  "routerConfigs": [
    {
      "id": 0,
      "isConfirmed": true,
      "mode": "load_balancing",
      "models": [
        {
          "id": 0,
          "model": "string",
          "weight": 0
        }
      ],
      "name": "string",
      "routeId": "string",
      "strategies": [
        "string"
      ]
    }
  ],
  "selectedModels": [
    {
      "inputPrice": 0,
      "name": "string",
      "outputPrice": 0,
      "provider": "string",
      "supportModelId": 0,
      "type": "string"
    }
  ],
  "teamMembers": [
    {
      "name": "string",
      "role": "string",
      "roleId": 0,
      "type": "string"
    }
  ]
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": {},
  "msg": "创建项目成功"
}
```

***

### 3. 编辑项目

更新项目的基本信息。

#### 接口信息

* **Path**: `/project/edit`
* **Method**: `POST`
* **权限**: 主账号、RAM管理员、项目管理员

#### 请求参数

| 参数名         | 类型        | 必填 | 说明     | 示例                        |
| ----------- | --------- | -- | ------ | ------------------------- |
| projectId   | integer   | 是  | 项目ID   | 1                         |
| startTime   | time.Time | 否  | 项目开始时间 | 2025-01-01T12:00:00+00:00 |
| endTime     | time.Time | 否  | 项目结束时间 | 2026-01-01T12:00:00+00:00 |
| projectName | string    | 否  | 项目名称   | xxxxx                     |

#### 请求示例

```http
POST /project/edit?project_id=1
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "project_id": 1,
  "startTime": "更新后的项目名称",
  "endTime": "更新后的项目描述",
  "projectName": "xxxxxx"
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": {},
  "msg": "编辑项目成功"
}
```

***

### 4. 删除项目

删除指定的项目。

#### 接口信息

* **Path**: `/project/delete`
* **Method**: `POST`
* **权限**: 主账号、RAM管理员、项目管理员

#### 请求参数

| 参数名         | 类型      | 必填 | 说明   | 示例 |
| ----------- | ------- | -- | ---- | -- |
| project\_id | integer | 是  | 项目ID | 1  |

#### 请求示例

```http
POST /project/delete?project_id=1
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "project_id": 1
}
```

#### 响应示例

```json
{
  "code": 0,
  "msg": "删除项目成功"
}
```

***

### 5. 获取项目路由列表

查询项目的路由配置列表。

#### 接口信息

* **Path**: `/project/router/list`
* **Method**: `POST`
* **权限**: 主账号、RAM管理员、项目管理员、项目查看者

#### 请求参数

| 参数名         | 类型      | 必填 | 说明   | 示例 |
| ----------- | ------- | -- | ---- | -- |
| project\_id | integer | 是  | 项目ID | 1  |

#### 请求示例

```http
POST /project/router/list
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "project_id": 1
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": [
    {
      "id": 168,
      "name": "负载均衡",
      "mode": "loadBalance",
      "models": [
        {
          "id": 214,
          "model": "qwen/qwen-plus",
          "weight": 10
        },
        {
          "id": 215,
          "model": "qwen/qvq-72b",
          "weight": 10
        },
        {
          "id": 216,
          "model": "qwen/qwen-vl-plus",
          "weight": 80
        }
      ],
      "isConfirmed": false,
      "routeId": "3919c69336acb30d",
      "strategies": []
    },
    {
      "id": 169,
      "name": "故障转移",
      "mode": "failover",
      "models": [
        {
          "id": 217,
          "model": "qwen/qwen-plus",
          "weight": 0
        },
        {
          "id": 218,
          "model": "qwen/qvq-72b",
          "weight": 0
        }
      ],
      "isConfirmed": false,
      "routeId": "754149d7e6e8b932",
      "strategies": []
    }
  ],
  "msg": "获取路由详情成功"
}
```

***

### 6. 添加项目路由

为项目添加新的路由配置。

#### 接口信息

* **Path**: `/project/router/add`
* **Method**: `POST`
* **权限**: 主账号、RAM管理员、项目管理员

#### 请求参数

| 参数名          | 类型      | 必填 | 说明   | 示例 |
| ------------ | ------- | -- | ---- | -- |
| project\_id  | integer | 是  | 项目ID | 1  |
| RouterConfig | string  | 是  | 路由配置 |    |

#### 请求示例

```http
POST /project/router/add?project_id=1
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "project_id": 0,
  "router_config": {
    "id": 0,
    "isConfirmed": true,
    "mode": "load_balancing",
    "models": [
      {
        "id": 0,
        "model": "string",
        "weight": 0
      }
    ],
    "name": "string",
    "routeId": "string",
    "strategies": [
      "string"
    ]
  }
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": { },
  "msg": "添加成功"
}
```

***

### 7. 获取项目模型列表

查询项目可用的模型列表。

#### 接口信息

* **Path**: `/project/models/list`
* **Method**: `POST`
* **权限**: 主账号、RAM管理员、项目管理员、项目查看者

#### 请求参数

| 参数名         | 类型      | 必填 | 说明   | 示例 |
| ----------- | ------- | -- | ---- | -- |
| project\_id | integer | 是  | 项目ID | 1  |

#### 请求示例

```http
POST /project/models/list?project_id=1
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "project_id": 1
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": [
    {
      "id": 92,
      "name": "google/gemini-3-pro-image-preview",
      "type": "图像生成",
      "inputPrice": 50000,
      "outputPrice": 90000,
      "provider": "Google",
      "supportModelId": 0,
      "icon": ""
    },
    {
      "id": 104,
      "name": "deepseek/deepseek-v3.2",
      "type": "LLM",
      "inputPrice": 20000,
      "outputPrice": 40000,
      "provider": "DeepSeek",
      "supportModelId": 0,
      "icon": ""
    }
  ],
  "msg": "获取项目模型详情成功"
}
```

***

### 获取可用模型

#### 接口信息

* **Path**: `/modelrouter/list/byoks?pageSize=1000&modelTypeId=1,2,3,4`
* **Method**: `GET`

此接口使用主站模型目录服务地址 `YOUR_MODEL_CATALOG_BASE_URL`，与企业管理地址分开配置。

#### 请求参数

| 参数名         | 类型      | 必填 | 说明                                         | 示例      |
| ----------- | ------- | -- | ------------------------------------------ | ------- |
| pageSize    | integer | 是  | 每页记录数                                      | 1000    |
| modelTypeId | \[]uint | 是  | 模型类型id. 1:llm ,2:embedding, 3:图像生成, 4:视频生成 | 1,2,3,4 |

#### 请求示例

```http
GET /modelrouter/list/byoks?pageSize=1000&modelTypeId=1,2,3,4
Content-Type: application/json
```

#### 响应示例

```json
{
    "code": 0,
    "data": {
        "infos": [
            {
                "id": 268,
                "icon_64": "",
                "company": "MiniMax",
                "name": "MiniMax M2.1 lightning",
                "api_name": "minimax/minimax-m2.1-lightning",
                "description": "",
                "support_id": 364,
                "context": 0,
                "provider": "MiniMax",
                "model_type": 1,
                "pricing": {
                    "price": 0,
                    "input_price": 0,
                    "output_price": 0,
                    "cached_price": 0,
                    "image_price": 0,
                    "currency": ""
                },
                "provider_pricing": {
                    "price": 0,
                    "input_price": 0,
                    "output_price": 0,
                    "cached_price": 0,
                    "image_price": 0,
                    "currency": ""
                },
                "endpoint": {
                    "provider": "",
                    "quantization": "",
                    "context_length": 0,
                    "max_output": 0
                },
                "architecture": {
                    "input": "",
                    "output": "",
                    "tokenizer": ""
                },
                "total_tokens": 0,
                "supported_apis": "",
                "is_byok": false,
                "byok_router_names": [
                    "/v1/chat/completions",
                    "/v1/messages"
                ]
            }
        ]
    },
  "msg": "获取项目模型详情成功"
}
```

***

### 8. 编辑项目模型配置

更新项目的模型配置（启用/禁用模型）。

#### 接口信息

* **Path**: `/project/models/edit`
* **Method**: `POST`
* **权限**: 主账号、RAM管理员、项目管理员

#### 请求参数

| 参数名         | 类型                    | 必填 | 说明   | 示例    |
| ----------- | --------------------- | -- | ---- | ----- |
| project\_id | integer               | 是  | 项目ID | 1     |
| models      | \[]tensify.ModelPrice | 是  | 项目名称 | 见下方示例 |

#### 请求示例

```http
POST /project/models/edit
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "models": [
    {
      "icon": "string",
      "id": 0,
      "inputPrice": 0,
      "name": "string",
      "outputPrice": 0,
      "provider": "string",
      "supportModelId": 0,
      "type": "string"
    }
  ],
  "project_id": 0
}
```

#### 响应示例

```json
{
  "code": 0,
  "msg": "编辑项目模型详情成功"
}
```

***

### 9. 生成项目API Token

在指定项目中为当前账号生成 API Key，用于模型调用及支持项目 Key 的查询。

#### 接口信息

* **Path**: `/project/generate/apitoken`
* **Method**: `POST`
* **权限**: 校验当前账号的项目成员关系与本人凭证归属

#### 请求参数

| 参数                | 类型      | 要求   | 说明                       |
| ----------------- | ------- | ---- | ------------------------ |
| project\_id       | integer | 必填   | 显式指定项目；0 表示历史项目          |
| name              | string  | 名称字段 | API Key 名称               |
| api\_name         | string  | 兼容字段 | name 为空时作为名称使用           |
| expire\_days      | integer | 可选   | 0 永不过期；负数无效；正数截至目标日期当天末尾 |
| max\_quota        | integer | 可选   | 消费上限，单位分；0 不限制           |
| rpm               | integer | 可选   | 每分钟请求上限；0 不限制            |
| tpm               | integer | 可选   | 每分钟 Token 上限；0 不限制       |
| supported\_models | array   | 可选   | 模型 ID 数组；空数组表示不额外限制      |
| ip\_whitelist     | string  | 可选   | 逗号分隔的 IP 白名单；空字符串表示不限制   |

生成 Key 时校验项目成员关系与凭证归属。项目身份、可操作范围和 Key 归属共同决定权限。

#### 请求示例

```http
POST /project/generate/apitoken
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "project_id": 1,
  "name": "业务服务",
  "expire_days": 30,
  "max_quota": 10000,
  "rpm": 60,
  "tpm": 100000,
  "supported_models": [],
  "ip_whitelist": ""
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": {
    "ID": 114,
    "CreatedAt": "2025-12-26T16:04:38.031504+08:00",
    "UpdatedAt": "2025-12-26T16:04:38.031504+08:00",
    "UserID": 4,
    "IsBanned": false,
    "IsExpired": false,
    "Name": "xxxxxx",
    "Desc": "",
    "Key": "DHjiThiu",
    "Token": "YOUR_PROJECT_API_KEY",
    "Tpm": 0,
    "Rpm": 0,
    "MaxQuota": 0,
    "ConsumedAmount": 0,
    "SupportedModels": "",
    "IsDefault": false,
    "ExpiresAt": null,
    "RamUserId": 3,
    "EnterpriseGatewayProjectID": 35
  },
  "msg": "查询成功"
}
```

***

### 10. 获取项目API Key列表

查询当前账号在指定项目权限范围内的 API Key。

#### 接口信息

* **Path**: `/project/apikey/list`
* **Method**: `POST`
* **权限**: 校验当前账号的项目成员关系与本人凭证归属

#### 请求参数

| 参数名         | 类型      | 必填 | 说明   | 示例 |
| ----------- | ------- | -- | ---- | -- |
| project\_id | integer | 是  | 项目ID | 1  |

#### 请求示例

```http
POST /project/apikey/list
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "project_id": 1
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": [
    {
      "ID": 105,
      "CreatedAt": "2025-12-17T12:00:35.50753+08:00",
      "UpdatedAt": "2025-12-18T11:08:38.576763+08:00",
      "UserID": 4,
      "IsBanned": false,
      "IsExpired": false,
      "Name": "测试router",
      "Desc": "",
      "Key": "EuBTkJ52",
      "Token": "YOUR_PROJECT_API_KEY",
      "Tpm": 0,
      "Rpm": 0,
      "MaxQuota": 0,
      "ConsumedAmount": 0,
      "SupportedModels": "",
      "IsDefault": false,
      "ExpiresAt": null,
      "RamUserId": 0,
      "EnterpriseGatewayProjectID": 35
    }
  ],
  "msg": "获取apikey成功"
}
```

***

### 11. 获取项目团队成员

查询项目的团队成员列表及其角色。

#### 接口信息

* **Path**: `/project/team/members`
* **Method**: `POST`
* **权限**: 主账号、RAM管理员、项目管理员

#### 请求参数

| 参数名         | 类型      | 必填 | 说明   | 示例 |
| ----------- | ------- | -- | ---- | -- |
| project\_id | integer | 是  | 项目ID | 1  |

#### 请求示例

```http
POST /project/team/members
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "project_id": 1
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": [
    {
      "id": 2,
      "name": "junior_li_test",
      "roleId": 13,
      "role": "有限制只查看",
      "type": "member"
    },
    {
      "id": 16,
      "name": "lcw",
      "roleId": 5,
      "role": "无限制管理员",
      "type": "member"
    }
  ],
  "msg": "获取项目成员详情成功"
}
```

***

### 12. 编辑项目团队成员

添加或移除项目团队成员，分配角色。

#### 接口信息

* **Path**: `/project/team/members/edit`
* **Method**: `POST`
* **权限**: 主账号、RAM管理员、项目管理员

#### 请求参数

| 参数名         | 类型      | 必填 | 说明     | 示例    |
| ----------- | ------- | -- | ------ | ----- |
| project\_id | integer | 是  | 项目ID   | 1     |
| members     | array   | 是  | 成员配置数组 | 见下方示例 |

#### 请求示例

```http
POST /project/team/members/edit
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "project_id": 0,
  "team_members": [
    {
      "id": 0,
      "name": "string",
      "role": "string",
      "roleId": 0,
      "type": "string"
    }
  ]
}
```

#### 响应示例

```json
{
  "code": 0,
  "msg": "编辑项目成员详情成功"
}
```

***

## 完整调用示例

### Python完整示例

将 BYOK 页完整 Python 客户端中的导入语句和 BYOKAPI 类保存为 `byok_client.py`（不包含底部调用演示），再运行以下扩展。

```python
from byok_client import BYOKAPI

class ProjectAPI(BYOKAPI):
    # ========== Project ==========

    def list_projects(self, filters=None):
        return self._request(
            "GET",
            "/project/list",
            params=filters or {}
        )

    def create_project(self, project_payload: dict):
        """
        project_payload:
        {
          projectName,
          projectTime?,
          teamMembers,
          selectedModels?,
          routerConfigs
        }
        """
        return self._request(
            "POST",
            "/project/create",
            body=project_payload
        )

    def edit_project(self, project_id: int, updates: dict):
        body = {"project_id": project_id, **updates}
        return self._request("POST", "/project/edit", body)

    def delete_project(self, project_id: int):
        return self._request(
            "POST",
            "/project/delete",
            {"project_id": project_id}
        )

    # ========== Router ==========

    def list_routers(self, project_id: int):
        return self._request(
            "POST",
            "/project/router/list",
            {"project_id": project_id}
        )

    def add_router(self, project_id: int, router_config: dict):
        return self._request(
            "POST",
            "/project/router/add",
            {
                "project_id": project_id,
                "router_config": router_config
            }
        )

    # ========== Models ==========

    def list_project_models(self, project_id: int):
        return self._request(
            "POST",
            "/project/models/list",
            {"project_id": project_id}
        )

    def edit_project_models(self, project_id: int, models: list):
        return self._request(
            "POST",
            "/project/models/edit",
            {
                "project_id": project_id,
                "models": models
            }
        )

    # ========== API Token ==========

    def generate_api_token(self, project_id, name, expire_days=0, max_quota=0,
                           rpm=0, tpm=0, supported_models=None, ip_whitelist=""):
        if project_id is None:
            raise ValueError("必须显式提供 project_id；历史项目使用 0")
        return self._request("POST", "/project/generate/apitoken", {
            "project_id": project_id, "name": name, "expire_days": expire_days,
            "max_quota": max_quota, "rpm": rpm, "tpm": tpm,
            "supported_models": [] if supported_models is None else supported_models,
            "ip_whitelist": ip_whitelist
        })

    def list_api_tokens(self, project_id: int):
        return self._request(
            "POST",
            "/project/apikey/list",
            {"project_id": project_id}
        )

    # ========== Team ==========

    def list_team_members(self, project_id: int):
        return self._request(
            "POST",
            "/project/team/members",
            {"project_id": project_id}
        )

    def edit_team_members(self, project_id: int, team_members: list):
        return self._request(
            "POST",
            "/project/team/members/edit",
            {
                "project_id": project_id,
                "team_members": team_members
            }
        )

```

***

## 错误处理

HTTP 状态码与响应 JSON 中的业务 `code` 分别处理；不能把 HTTP 400、401、403 直接当作业务码，也不能只凭 HTTP 200 判断操作成功。结合 `code`、`msg` 和 `data.reason` 处理异常。

* 认证失败：检查凭证状态、时间戳、签名 Path 与请求体字节。
* 参数或权限错误：检查本页参数、账号身份及项目授权范围。
* `JWT_REQUIRED`：该操作需要 JWT 登录态。
* `STEP_UP_REQUIRED`：按响应提供的验证方式完成敏感操作验证后重试。

公共响应说明见[认证与签名](api-ren-zheng-yu-qian-ming-ban-fa.md)。

## 注意事项

1. **project\_id 参数**：按各接口参数表放入 Query 或请求体，显式选择目标项目
2. **签名计算**：签服务接收到的 URL Path，查询参数不参与签名，但仍随请求发送
3. **权限验证**: 项目相关操作会验证用户在该项目中的角色权限
4. **项目 API Key**：用于模型调用，也可访问本页列明的项目查询接口；不能通用于其他管理操作
