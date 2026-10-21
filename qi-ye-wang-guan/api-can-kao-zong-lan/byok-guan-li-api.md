# byok 管理 api

> 查询供应商、维护多 Key 配置和删除单个 Key。

## 概述

BYOK（Bring Your Own Key）管理API用于管理用户自带的API密钥，支持添加、查看和编辑各种AI服务提供商的密钥配置。这允许企业使用自己的API密钥而不是平台提供的密钥。



## 基础信息

* **Base URL**: `YOUR_GATEWAY_MANAGEMENT_BASE_URL`
* **认证方式**: 见下方认证与权限说明
* **请求格式**: JSON
* **响应格式**: JSON

## 认证与权限说明

管理地址使用 `YOUR_GATEWAY_MANAGEMENT_BASE_URL`，与模型调用 Base URL 分开配置。JWT 通过登录 Cookie 或 `x-token` 请求头传递；AccessKey 请求使用下文签名请求头。

开启操作保护后，受保护的写操作需要 JWT 和有效的敏感操作验证授权。收到 `JWT_REQUIRED` 时使用登录态；收到 `STEP_UP_REQUIRED` 时按返回的验证方式完成授权后重试。认证规则见[API 认证与签名方法](/broken/pages/e8167923d9de15f636aa273e8250fa11b46d254a)。

`GET /byok/list` 和 `GET /byok/user/list` 支持主账号、RAM 管理员、成员和财务账号。创建、编辑与删除 Key 需要相应管理权限；敏感写操作还受操作保护约束。

## API接口列表

### 1. 获取BYOK提供商列表

查询系统支持的BYOK提供商,以及模型列表。

#### 接口信息

* **Path**: `/byok/list`
* **Method**: `GET`
* **权限**: 主账号、RAM管理员、RAM成员、财务账号

#### 请求参数

无参数

#### 请求示例

```http
GET /byok/list
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
      "ID": 10,
      "Name": "百度OpenAI",
      "Desc": "百度OpenAI",
      "Schema": "{\n  \"title\": \"API Token\",\n  \"properties\": {\n    \"api_key\": {\n      \"type\": \"string\",\n      \"title\": \"ApiKey\"\n    }\n  }\n}",
      "ChannelTypeID": 40,
      "Icon": "",
      "BaseUrl": "https://qianfan.baidubce.com/v2",
      "TestRouter": "",
      "ChannelType": {
        "ID": 40,
        "CreatedAt": "0001-01-01T00:00:00Z",
        "UpdatedAt": "0001-01-01T00:00:00Z",
        "name": "Baidu",
        "provider_name": "baidu",
        "description": "baidu",
        "label": "llm",
        "base_url": "https://qianfan.baidubce.com/v2",
        "channel": null,
        "support_models": [
        {
                        "ID": 343,
                        "CreatedAt": "2025-12-10T15:02:24.726661+08:00",
                        "UpdatedAt": "2025-12-17T17:01:00.030477+08:00",
                        "type_id": 4,
                        "name": "deepseek-v3.2",
                        "model_info_id": 245,
                        "description": "",
                        "price": 0,
                        "ratio": 1,
                        "inputprice": 20000,
                        "outputprice": 30000,
                        "cached_price": 2000,
                        "image_price": 0,
                        "completion_ratio": 1,
                        "other_price": "0",
                        "provider_price": 0,
                        "provider_input_price": 20000,
                        "provider_output_price": 30000,
                        "provider_cached_price": 2000,
                        "provider_image_price": 0,
                        "provider_other_price": "0",
                        "provider_rpm": 15000,
                        "provider_tpm": 1200000,
                        "tpm_threshold": 99,
                        "rpm_threshold": 99,
                        "context": 128000,
                        "max_output": 64000,
                        "latency": 20153,
                        "throughput": 11,
                        "precision": "fp16",
                        "auto_route": true,
                        "currency": "CNY",
                        "provider_filter": "",
                        "country_code": "CN",
                        "manual_priority": -20,
                        "supported_apis": "/v1/chat/completions,/v1/messages",
                        "url_path": "",
                        "other_info": "",
                        "model_info": {
                            "ID": 245,
                            "CreatedAt": "2025-12-02T11:12:23.716222+08:00",
                            "UpdatedAt": "2025-12-02T11:15:27.214011+08:00",
                            "company": "DeepSeek",
                            "company_id": 9,
                            "company_info": {
                                "ID": 0,
                                "CreatedAt": "0001-01-01T00:00:00Z",
                                "UpdatedAt": "0001-01-01T00:00:00Z",
                                "icon64": "",
                                "name": "",
                                "description": "",
                                "website": "",
                                "model_infos": null
                            },
                            "model_name": "Deepseek V3.2",
                            "copy_name": "deepseek/deepseek-v3.2",
                            "desc": "DeepSeek-V3.2 是一款大型语言模型，......  ",
                            "offical_site": "https://deepseek.com",
                            "open_source": "https://deepseek.com",
                            "licence": "https://deepseek.com",
                            "model_type_id": 1,
                            "model_type": {
                                "ID": 0,
                                "CreatedAt": "0001-01-01T00:00:00Z",
                                "UpdatedAt": "0001-01-01T00:00:00Z",
                                "display_name": "",
                                "ModelInterfaceCatalog": null,
                                "status": 0
                            },
                            "support_models": null,
                            "supports_prompt_cache": true,
                            "priority": 1,
                            "currency": "CNY",
                            "original_price": 0,
                            "original_input_price": 20000,
                            "original_output_price": 30000,
                            "original_cached_price": 2000,
                            "original_image_price": 0,
                            "original_other_price": "",
                            "model_use_cases": null,
                            "input_modalities": null,
                            "output_modalities": null,
                            "extend_info": {
                                "ID": 0,
                                "CreatedAt": "0001-01-01T00:00:00Z",
                                "UpdatedAt": "0001-01-01T00:00:00Z",
                                "model_info_id": 0,
                                "input_schema": "",
                                "output_schema": "",
                                "price_schema": "",
                                "usage": 0,
                                "preview_url": "",
                                "preview_video": ""
                            }
                        },
                        "channel_types": {
                            "ID": 0,
                            "CreatedAt": "0001-01-01T00:00:00Z",
                            "UpdatedAt": "0001-01-01T00:00:00Z",
                            "name": "",
                            "provider_name": "",
                            "description": "",
                            "label": "",
                            "base_url": "",
                            "channel": null,
                            "support_models": null
                        }
                    }
        ]
      }
    }
  ],
  "msg": "查询成功"
}
```

***

### 2. 获取用户BYOK列表

查询当前用户配置的所有BYOK。

#### 接口信息

* **Path**: `/byok/user/list`
* **Method**: `GET`
* **权限**: 主账号、RAM管理员、RAM成员、财务账号

#### 请求参数

无参数

#### 请求示例

```http
GET /byok/user/list
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
      "id": 46,
      "byok_provider_name": "Azure",
      "schema": "{\"deployments\":[{\"api_key\":\"YOUR_PROVIDER_API_KEY\",\"endpoint_url\":\"YOUR_AZURE_OPENAI_ENDPOINT\",\"model_id\":\"gpt-5\",\"model_slug\":\"openai/gpt-5\"}],\"model_api_name\":[\"openai/gpt-5\"]}",
      "model_api_name": [
        "openai/gpt-5"
      ],
      "is_enabled": true,
      "always_use_this_key": false,
      "schema_struct": "{\n  \"title\": \"Azure Configuration（Deployments）\",\n  \"type\": \"object\",\n  \"properties\": {\n    \"deployments\": {\n      \"type\": \"array\",\n      \"items\": {\n        \"type\": \"object\",\n        \"properties\": {\n          \"model_slug\": {\n            \"type\": \"string\",\n            \"title\": \"Model Slug\"\n          },\n          \"endpoint_url\": {\n            \"type\": \"string\",\n            \"title\": \"Endpoint URL\"\n          },\n          \"api_key\": {\n            \"type\": \"string\",\n            \"title\": \"API Key\"\n          },\n          \"model_id\": {\n            \"type\": \"string\",\n            \"title\": \"Model ID\"\n          }\n        },\n        \"required\": [\n          \"model_slug\",\n          \"endpoint_url\",\n          \"api_key\",\n          \"model_id\"\n        ]\n      },\n      \"minItems\": 1\n    }\n  },\n  \"required\": [\n    \"deployments\"\n  ]\n}",
      "model_mapping": [],
      "support_model": [
        {
          "ID": 106,
          "CreatedAt": "2025-07-22T10:54:15.445333+08:00",
          "UpdatedAt": "2025-11-20T17:18:45.519544+08:00",
          "type_id": 28,
          "name": "gpt-4.1",
          "model_info_id": 74,
          "description": "",
          "price": 0,
          "ratio": 1,
          "inputprice": 12500,
          "outputprice": 100000,
          "cached_price": 1250,
          "image_price": 0,
          "completion_ratio": 0,
          "other_price": "",
          "provider_price": 0,
          "provider_input_price": 12500,
          "provider_output_price": 100000,
          "provider_cached_price": 1250,
          "provider_image_price": 0,
          "provider_other_price": "",
          "provider_rpm": 10000,
          "provider_tpm": 1000000,
          "tpm_threshold": 80,
          "rpm_threshold": 80,
          "context": 0,
          "max_output": 0,
          "latency": 1089,
          "throughput": 14,
          "precision": "",
          "auto_route": true,
          "currency": "USD",
          "provider_filter": "",
          "country_code": "CN",
          "manual_priority": 0,
          "supported_apis": "/v1/chat/completions,/v1/messages",
          "model_info": {
            "ID": 74,
            "CreatedAt": "2025-02-20T15:54:58.348372+08:00",
            "UpdatedAt": "2025-06-04T18:23:43.977268+08:00",
            "company": "OpenAI",
            "company_id": 6,
            "company_info": {
              "ID": 0,
              "CreatedAt": "0001-01-01T00:00:00Z",
              "UpdatedAt": "0001-01-01T00:00:00Z",
              "icon64": "",
              "name": "",
              "description": "",
              "website": "",
              "model_infos": null
            },
            "model_name": "GPT-5-pro",
            "copy_name": "openai/gpt-5-pro",
            "desc": "",
            "offical_site": "",
            "open_source": "",
            "licence": "",
            "model_type_id": 1,
            "model_type": {
              "ID": 0,
              "CreatedAt": "0001-01-01T00:00:00Z",
              "UpdatedAt": "0001-01-01T00:00:00Z",
              "display_name": ""
            },
            "support_models": null,
            "supports_prompt_cache": false,
            "priority": 5,
            "currency": "CNY",
            "original_price": 0,
            "original_input_price": 50000,
            "original_output_price": 90000,
            "original_cached_price": 0,
            "original_image_price": 0,
            "original_other_price": "",
            "model_use_cases": null,
            "input_modalities": null,
            "output_modalities": null,
            "extend_info": {
              "ID": 0,
              "CreatedAt": "0001-01-01T00:00:00Z",
              "UpdatedAt": "0001-01-01T00:00:00Z",
              "model_info_id": 0,
              "input_schema": "",
              "output_schema": "",
              "price_schema": "",
              "usage": 0,
              "preview_url": "",
              "preview_video": ""
            }
          },
          "channel_types": {
            "ID": 0,
            "CreatedAt": "0001-01-01T00:00:00Z",
            "UpdatedAt": "0001-01-01T00:00:00Z",
            "name": "",
            "provider_name": "",
            "description": "",
            "label": "",
            "base_url": "",
            "channel": null,
            "support_models": null
          }
        }
      ]
    }
  ],
  "msg": "查询成功"
}
```

***

### 3. 创建 BYOK 配置

* **Path**: `/byok/create`
* **Method**: `POST`
* **权限**: 主账号、RAM管理员；受操作保护约束

#### 请求参数

| 参数                         | 要求         | 说明                                          |
| -------------------------- | ---------- | ------------------------------------------- |
| byok\_provider\_id         | 必填         | 提供商 ID                                      |
| keys                       | 必填，至少一项    | Key 条目数组                                    |
| keys\[].name               | 按配置填写      | Key 名称                                      |
| keys\[].schema             | 按供应商凭证定义填写 | 单个 Key 的凭证内容，格式由供应商 Schema 定义               |
| model\_mappings            | 必填         | 数组，每项包含 support\_model\_name 与 custom\_name |
| is\_enabled                | 按配置填写      | 是否启用配置                                      |
| always\_use\_this\_key     | 按配置填写      | 是否始终使用此自有 Key 配置                            |
| byok\_support\_model\_ids  | 可选         | 支持模型 ID 数组                                  |
| byok\_provider\_model\_ids | 可选         | 提供商模型 ID 数组                                 |

凭证放在每个 `keys[].schema` 中。创建时不传 `keys[].byok_detail_id` 或 `keys[].is_enabled`。供应商及凭证字段来自上方的供应商列表。

#### 请求示例

将供应商 ID、模型映射和凭证占位值替换为实际配置。`YOUR_PROVIDER_CREDENTIAL_SCHEMA` 表示按所选供应商 Schema 填写的完整凭证内容。

```http
POST /byok/create
Content-Type: application/json
X-Access-Key-Id: YOUR_ACCESS_KEY_ID
X-Timestamp: 2026-10-14T00:00:00Z
X-Signature: YOUR_SIGNATURE

{
  "byok_provider_id": 1,
  "keys": [
    {"name": "业务服务", "schema": "YOUR_PROVIDER_CREDENTIAL_SCHEMA"}
  ],
  "model_mappings": [
    {"support_model_name": "YOUR_SUPPORTED_MODEL", "custom_name": "YOUR_PROVIDER_MODEL"}
  ],
  "is_enabled": true,
  "always_use_this_key": false
}
```

#### 响应

读取管理接口响应的 `code`、`data` 和 `msg`，再通过 `/byok/user/list` 获取保存后的配置与 Key 标识。

### 4. 编辑 BYOK 配置

* **Path**: `/byok/edit`
* **Method**: `POST`
* **权限**: 主账号、RAM管理员；受操作保护约束

#### 请求参数

| 参数                         | 要求         | 说明                                          |
| -------------------------- | ---------- | ------------------------------------------- |
| byok\_provider\_id         | 必填         | 提供商 ID                                      |
| keys                       | 必填，至少一项    | 待编辑或新增的 Key 条目数组                            |
| keys\[].byok\_detail\_id   | 按操作填写      | 大于 0 表示编辑已有 Key；0 表示新增 Key                  |
| keys\[].name               | 按配置填写      | Key 名称                                      |
| keys\[].schema             | 按供应商凭证定义填写 | 单个 Key 的凭证内容                                |
| keys\[].is\_enabled        | 按配置填写      | 单个 Key 的启用状态                                |
| model\_mappings            | 必填         | 数组，每项包含 support\_model\_name 与 custom\_name |
| is\_enabled                | 按配置填写      | 整个配置的启用状态                                   |
| always\_use\_this\_key     | 按配置填写      | 是否始终使用此自有 Key 配置                            |
| byok\_support\_model\_ids  | 可选         | 支持模型 ID 数组                                  |
| byok\_provider\_model\_ids | 可选         | 提供商模型 ID 数组                                 |

#### 请求示例

```http
POST /byok/edit
Content-Type: application/json
X-Access-Key-Id: YOUR_ACCESS_KEY_ID
X-Timestamp: 2026-10-14T00:00:00Z
X-Signature: YOUR_SIGNATURE

{
  "byok_provider_id": 1,
  "keys": [
    {
      "byok_detail_id": 12,
      "name": "业务服务",
      "schema": "YOUR_PROVIDER_CREDENTIAL_SCHEMA",
      "is_enabled": true
    }
  ],
  "model_mappings": [
    {"support_model_name": "YOUR_SUPPORTED_MODEL", "custom_name": "YOUR_PROVIDER_MODEL"}
  ],
  "is_enabled": true,
  "always_use_this_key": false
}
```

保存后通过 `/byok/user/list` 检查配置及 Key 状态。

### 5. 删除单个 Key

* **Path**: `/byok/key/delete`
* **Method**: `POST`
* **权限**: 具备 BYOK 管理权限；受操作保护约束

| 参数               | 类型      | 必填 | 说明             |
| ---------------- | ------- | -- | -------------- |
| byok\_detail\_id | integer | 是  | 要删除的 Key 条目 ID |

```http
POST /byok/key/delete
Content-Type: application/json
X-Access-Key-Id: YOUR_ACCESS_KEY_ID
X-Timestamp: 2026-10-14T00:00:00Z
X-Signature: YOUR_SIGNATURE

{"byok_detail_id": 12}
```

该操作删除指定 Key。删除前检查依赖此 Key 的调用，并在删除后重新查询配置。

## 完整调用示例

### Python完整示例

安装 `requests`。将下面的导入语句和 `BYOKAPI` 类保存为 `byok_client.py`，可供项目管理示例复用。使用 `SSY_MANAGEMENT_BASE_URL` 配置管理地址；凭证使用 AccessKey/SecretKey，或传入 JWT。

```python
import requests
import hashlib
import hmac
import json
from datetime import datetime, timezone

class BYOKAPI:
    def __init__(self, base_url, access_key_id=None, secret_key=None, jwt=None):
        self.base_url = base_url.rstrip('/')
        self.access_key_id = access_key_id
        self.secret_key = secret_key
        self.jwt = jwt

    def _request(self, method, uri, body=None, params=None):
        from urllib.parse import urlsplit
        method = method.upper()
        url = self.base_url + '/' + uri.lstrip('/')
        body_bytes = json.dumps(body, ensure_ascii=False, separators=(',', ':')).encode('utf-8') if body is not None else b''
        headers = {"Content-Type": "application/json"}
        if self.jwt:
            headers["x-token"] = self.jwt
        else:
            if not self.access_key_id or not self.secret_key:
                raise ValueError("请提供 AccessKey/SecretKey 或 JWT")
            timestamp = datetime.now(timezone.utc).isoformat(timespec='seconds')
            body_hash = hashlib.sha256(body_bytes).hexdigest()
            path = urlsplit(url).path
            string_to_sign = f"{method}\n{path}\n{timestamp}\n{body_hash}"
            signature = hmac.new(self.secret_key.encode('utf-8'), string_to_sign.encode('utf-8'), hashlib.sha256).hexdigest()
            headers.update({"X-Access-Key-Id": self.access_key_id,
                            "X-Timestamp": timestamp, "X-Signature": signature})
        response = requests.request(method, url, params=params,
                                    data=body_bytes if body is not None else None,
                                    headers=headers, timeout=30)
        return response.json()

    def list_providers(self):
        return self._request("GET", "/byok/list")

    def list_user_byoks(self):
        return self._request("GET", "/byok/user/list")

    def create_byok(self, byok_provider_id, keys, model_mappings, is_enabled=True, always_use_this_key=False):
        return self._request("POST", "/byok/create", {
            "byok_provider_id": byok_provider_id, "keys": keys, "model_mappings": model_mappings,
            "is_enabled": is_enabled, "always_use_this_key": always_use_this_key
        })

    def edit_byok(self, byok_provider_id, keys, model_mappings, is_enabled=True, always_use_this_key=False):
        return self._request("POST", "/byok/edit", {
            "byok_provider_id": byok_provider_id, "keys": keys, "model_mappings": model_mappings,
            "is_enabled": is_enabled, "always_use_this_key": always_use_this_key
        })

    def delete_key(self, byok_detail_id):
        return self._request("POST", "/byok/key/delete", {"byok_detail_id": byok_detail_id})

if __name__ == "__main__":
    import os
    api = BYOKAPI(os.environ["SSY_MANAGEMENT_BASE_URL"],
                  os.environ.get("SSY_ACCESS_KEY_ID"), os.environ.get("SSY_SECRET_KEY"),
                  jwt=os.environ.get("SSY_JWT"))
    print(api.list_providers())
```

### Node.js完整示例

使用 Node.js 18 或以上版本，安装 `axios`，保存为 `.cjs` 文件。设置管理地址与凭证环境变量，底部示例只查询供应商。

```javascript
const crypto = require('crypto');
const axios = require('axios');

class BYOKAPI {
    constructor(baseUrl, accessKeyId = null, secretKey = null, jwt = null) {
        this.baseUrl = baseUrl.replace(/\/$/, '');
        this.accessKeyId = accessKeyId;
        this.secretKey = secretKey;
        this.jwt = jwt;
    }

    async request(method, uri, body = null, params = null) {
        method = method.toUpperCase();
        const url = new URL(this.baseUrl + '/' + uri.replace(/^\//, ''));
        if (params) for (const [key, value] of Object.entries(params)) {
            if (value != null) url.searchParams.set(key, String(value));
        }
        const bodyStr = body == null ? '' : JSON.stringify(body);
        const headers = { 'Content-Type': 'application/json' };
        if (this.jwt) {
            headers['x-token'] = this.jwt;
        } else {
            if (!this.accessKeyId || !this.secretKey) throw new Error('请提供 AccessKey/SecretKey 或 JWT');
            const timestamp = new Date().toISOString();
            const bodyHash = crypto.createHash('sha256').update(bodyStr, 'utf8').digest('hex');
            const stringToSign = [method, url.pathname, timestamp, bodyHash].join('\n');
            const signature = crypto.createHmac('sha256', this.secretKey).update(stringToSign, 'utf8').digest('hex');
            Object.assign(headers, { 'X-Access-Key-Id': this.accessKeyId,
                'X-Timestamp': timestamp, 'X-Signature': signature });
        }
        const response = await axios({ method, url: url.toString(), headers,
            data: body == null ? undefined : bodyStr, timeout: 30000 });
        return response.data;
    }

    listProviders() { return this.request('GET', '/byok/list'); }
    listUserByoks() { return this.request('GET', '/byok/user/list'); }
    createByok(byokProviderId, keys, modelMappings, isEnabled = true, alwaysUseThisKey = false) {
        return this.request('POST', '/byok/create', {
            byok_provider_id: byokProviderId, keys, model_mappings: modelMappings,
            is_enabled: isEnabled, always_use_this_key: alwaysUseThisKey
        });
    }
    editByok(byokProviderId, keys, modelMappings, isEnabled = true, alwaysUseThisKey = false) {
        return this.request('POST', '/byok/edit', {
            byok_provider_id: byokProviderId, keys, model_mappings: modelMappings,
            is_enabled: isEnabled, always_use_this_key: alwaysUseThisKey
        });
    }
    deleteKey(byokDetailId) {
        return this.request('POST', '/byok/key/delete', { byok_detail_id: byokDetailId });
    }
}

(async () => {
    const api = new BYOKAPI(process.env.SSY_MANAGEMENT_BASE_URL,
        process.env.SSY_ACCESS_KEY_ID, process.env.SSY_SECRET_KEY, process.env.SSY_JWT);
    console.log(await api.listProviders());
})().catch(console.error);
```

## 密钥轮换

先添加新 Key 并检查调用结果，再停用或删除旧 Key。编辑已有 Key 时传其 `byok_detail_id`；新增 Key 时使用 `0`。只删除某个 Key 时使用 `/byok/key/delete`。

## 错误处理

HTTP 状态码与响应 JSON 中的业务 `code` 分别处理；不能把 HTTP 400、401、403 直接当作业务码，也不能只凭 HTTP 200 判断操作成功。结合 `code`、`msg` 和 `data.reason` 处理异常。

* 认证失败：检查凭证状态、时间戳、签名 Path 与请求体字节。
* 参数或权限错误：检查本页参数、账号身份及项目授权范围。
* `JWT_REQUIRED`：该操作需要 JWT 登录态。
* `STEP_UP_REQUIRED`：按响应提供的验证方式完成敏感操作验证后重试。

公共响应说明见[认证与签名](api-ren-zheng-yu-qian-ming-ban-fa.md)。

## 安全建议

凭证通过环境变量或密钥管理服务注入，不写入仓库、页面代码和日志。更换 Key 后检查模型调用结果与日志；停用或删除前检查业务依赖。
