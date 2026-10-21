# 统计管理 api

> 总览、错误、请求、模型、费用、Token 和项目排名等统计接口。

## 概述

统计查询API提供企业网关的各类数据统计和分析功能，包括请求趋势、费用分析、模型使用情况、项目排行等。支持多维度的数据查询和可视化展示。

## 基础信息

* **Base URL**: `YOUR_GATEWAY_MANAGEMENT_BASE_URL`
* **认证方式**: 见下方认证与权限说明
* **请求格式**: JSON
* **响应格式**: JSON

## 认证与权限说明

管理地址使用 `YOUR_GATEWAY_MANAGEMENT_BASE_URL`，与模型调用 Base URL 分开配置。JWT 通过登录 Cookie 或 `x-token` 请求头传递；AccessKey 请求使用下文签名请求头。

开启操作保护后，受保护的写操作需要 JWT 和有效的敏感操作验证授权。收到 `JWT_REQUIRED` 时使用登录态；收到 `STEP_UP_REQUIRED` 时按返回的验证方式完成授权后重试。认证规则见[API 认证与签名方法](api-ren-zheng-yu-qian-ming-ban-fa.md)。

| 操作          | 主账号 | RAM管理员 | RAM成员-Manager | RAM成员-Viewer |
| ----------- | --- | ------ | ------------- | ------------ |
| 查看授权范围内统计数据 | ✅   | ✅      | ✅             | ✅            |

统计查询的数据范围受账号身份、项目授权和接口权限约束。`POST /statistics/enterprise/task/gatewaybill` 还支持项目 API Key 查询其授权范围内的数据。

## API接口列表

### 1. 获取Dashboard总览

获取企业网关的总体统计数据，包括请求总数、费用总额、Token使用量等关键指标。

#### 接口信息

* **Path**: `/statistics/overview`
* **Method**: `POST`
* **权限**: 已认证且具备相应数据访问权限的账号

#### 请求参数

| 参数名         | 类型      | 必填 | 说明                 | 示例 |
| ----------- | ------- | -- | ------------------ | -- |
| project\_id | integer | 否  | 项目ID，查询所有项目需要传空结构体 | 1  |

#### 请求示例

```http
POST /statistics/overview
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
  "data": {
    "last_7_days": {
      "total_requests": 5,
      "total_cost": 25146,
      "total_input_tokens": 35,
      "total_output_tokens": 2251
    },
    "week_over_week": {
      "requests_growth": -64.28571428571429,
      "cost_growth": -88.52891265076728
    },
    "current_month": {
      "total_cost": 643085,
      "cost_growth": 100,
      "total_requests_mtd": 26,
      "multimodal_requests_mtd": 0,
      "byok_requests_mtd": 0,
      "total_tokens_mtd": 7504,
      "project_count_mtd": 4,
      "ram_user_count_mtd": 2,
      "last_period_cost": 0
    }
  },
  "msg": "查询成功"
}
```

***

### 2. 获取错误统计

查询指定时间范围内的错误统计数据。

#### 接口信息

* **Path**: `/statistics/error`
* **Method**: `GET`
* **权限**: 已认证且具备相应数据访问权限的账号

#### 请求参数（Query参数）

| 参数名         | 类型      | 必填 | 说明   | 示例                   |
| ----------- | ------- | -- | ---- | -------------------- |
| start\_time | string  | 是  | 开始时间 | 2024-01-01T00:00:00Z |
| end\_time   | string  | 是  | 结束时间 | 2024-01-31T23:59:59Z |
| project\_id | integer | 否  | 项目ID | 1                    |

#### 请求示例

```http
GET /statistics/error
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx
```

#### 响应示例

```json
{
  "code": 0,
  "data": {
    "total_errors": 10,
    "gateway_errors": 4,
    "task_errors": 6,
    "statistics": [
      {
        "status_code": 503,
        "error_count": 8,
        "percentage": 80,
        "error_type": "service_unavailable",
        "error_category": "server_error",
        "is_retryable": true,
        "sample_error_msg": "Service Unavailable"
      },
      {
        "status_code": 401,
        "error_count": 1,
        "percentage": 10,
        "error_type": "authentication",
        "error_category": "provider_error",
        "is_retryable": false,
        "sample_error_msg": "Authentication Fails, Your api key: ****r\"]} is invalid"
      },
      {
        "status_code": 400,
        "error_count": 1,
        "percentage": 10,
        "error_type": "validation",
        "error_category": "client_error",
        "is_retryable": false,
        "sample_error_msg": "Request contains an invalid argument."
      }
    ],
    "top_errors_by_type": [],
    "top_errors_by_model": []
  },
  "msg": "查询成功"
}
```

***

### 3. 获取请求趋势

查询请求量的时间序列趋势数据。

#### 接口信息

* **Path**: `/statistics/trends/requests`
* **Method**: `POST`
* **权限**: 已认证且具备相应数据访问权限的账号

#### 请求参数

| 参数名         | 类型      | 必填 | 说明   | 示例                   |
| ----------- | ------- | -- | ---- | -------------------- |
| start\_time | string  | 是  | 开始时间 | 2024-01-01T00:00:00Z |
| end\_time   | string  | 是  | 结束时间 | 2024-01-31T23:59:59Z |
| project\_id | integer | 否  | 项目ID | 1                    |

#### 请求示例

```http
POST /statistics/trends/requests
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "end_time": "2025-12-18T15:59:59.059Z",
  "project_id": 35,
  "start_time": "2025-07-04T16:00:00.000Z"
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": {
    "daily_data": [
      {
        "date": "2025-12-17",
        "requests": 2,
        "byok_requests": 0,
        "non_byok_requests": 2,
        "cost": 20025
      },
      {
        "date": "2025-12-18",
        "requests": 12,
        "byok_requests": 0,
        "non_byok_requests": 12,
        "cost": 199187
      }
    ]
  },
  "msg": "查询成功"
}
```

***

### 4. 获取模型用量趋势

查询各模型使用量的时间序列趋势。

#### 接口信息

* **Path**: `/statistics/trends/model-usage`
* **Method**: `POST`
* **权限**: 已认证且具备相应数据访问权限的账号

#### 请求参数

| 参数名         | 类型      | 必填 | 说明   | 示例                   |
| ----------- | ------- | -- | ---- | -------------------- |
| start\_time | string  | 是  | 开始时间 | 2024-01-01T00:00:00Z |
| end\_time   | string  | 是  | 结束时间 | 2024-01-31T23:59:59Z |
| project\_id | integer | 否  | 项目ID | 1                    |

#### 请求示例

```http
POST /statistics/trends/model-usage
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "end_time": "2025-12-18T15:59:59.059Z",
  "project_id": 35,
  "start_time": "2025-07-04T16:00:00.000Z"
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": {
    "daily_data": [
      {
        "date": "2025-12-17",
        "models": [
          {
            "id": 9,
            "name": "qwen/qwen-vl-plus",
            "total_tokens": 127
          },
          {
            "id": 11,
            "name": "qwen/qvq-72b",
            "total_tokens": 67
          }
        ]
      },
      {
        "date": "2025-12-18",
        "models": [
          {
            "id": 9,
            "name": "qwen/qwen-vl-plus",
            "total_tokens": 1737
          },
          {
            "id": 9,
            "name": "qwen/qvq-72b",
            "total_tokens": 231
          },
          {
            "id": 11,
            "name": "qwen/qvq-72b",
            "total_tokens": 335
          },
          {
            "id": 49,
            "name": "deepseek/deepseek-v3",
            "total_tokens": 552
          }
        ]
      }
    ]
  },
  "msg": "查询成功"
}
```

***

### 5. 获取费用趋势

查询费用的时间序列趋势数据。

#### 接口信息

* **Path**: `/statistics/trends/cost`
* **Method**: `POST`
* **权限**: 已认证且具备相应数据访问权限的账号

#### 请求参数

| 参数名         | 类型      | 必填 | 说明   | 示例                   |
| ----------- | ------- | -- | ---- | -------------------- |
| start\_time | string  | 是  | 开始时间 | 2024-01-01T00:00:00Z |
| end\_time   | string  | 是  | 结束时间 | 2024-01-31T23:59:59Z |
| project\_id | integer | 否  | 项目ID | 1                    |

#### 请求示例

```http
POST /statistics/trends/cost
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "end_time": "2025-12-18T15:59:59.059Z",
  "project_id": 35,
  "start_time": "2025-07-04T16:00:00.000Z"
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": {
    "daily_data": [
      {
        "date": "2025-12-17",
        "requests": 2,
        "byok_requests": 0,
        "non_byok_requests": 2,
        "cost": 20025
      },
      {
        "date": "2025-12-18",
        "requests": 12,
        "byok_requests": 0,
        "non_byok_requests": 12,
        "cost": 199187
      }
    ]
  },
  "msg": "查询成功"
}
```

***

### 6. 获取Token趋势

查询Token使用量的时间序列趋势。

#### 接口信息

* **Path**: `/statistics/trends/tokens`
* **Method**: `POST`
* **权限**: 已认证且具备相应数据访问权限的账号

#### 请求参数

| 参数名         | 类型      | 必填 | 说明   | 示例                   |
| ----------- | ------- | -- | ---- | -------------------- |
| start\_time | string  | 是  | 开始时间 | 2024-01-01T00:00:00Z |
| end\_time   | string  | 是  | 结束时间 | 2024-01-31T23:59:59Z |
| project\_id | integer | 否  | 项目ID | 1                    |

#### 请求示例

```http
POST /statistics/trends/tokens
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "start_time": "2024-01-01T00:00:00Z",
  "end_time": "2024-01-31T23:59:59Z",
  "project_id": 1
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": {
    "daily_data": [
      {
        "date": "2025-12-17",
        "models": [
          {
            "id": 9,
            "name": "qwen/qwen-vl-plus",
            "input_tokens": 14,
            "output_tokens": 113,
            "byok_input_tokens": 0,
            "byok_output_tokens": 0,
            "non_byok_input_tokens": 14,
            "non_byok_output_tokens": 113
          },
          {
            "id": 11,
            "name": "qwen/qvq-72b",
            "input_tokens": 42,
            "output_tokens": 25,
            "byok_input_tokens": 0,
            "byok_output_tokens": 0,
            "non_byok_input_tokens": 42,
            "non_byok_output_tokens": 25
          }
        ]
      },
      {
        "date": "2025-12-18",
        "models": [
          {
            "id": 9,
            "name": "qwen/qwen-vl-plus",
            "input_tokens": 26,
            "output_tokens": 1711,
            "byok_input_tokens": 0,
            "byok_output_tokens": 0,
            "non_byok_input_tokens": 26,
            "non_byok_output_tokens": 1711
          },
          {
            "id": 9,
            "name": "qwen/qvq-72b",
            "input_tokens": 28,
            "output_tokens": 203,
            "byok_input_tokens": 0,
            "byok_output_tokens": 0,
            "non_byok_input_tokens": 28,
            "non_byok_output_tokens": 203
          },
          {
            "id": 11,
            "name": "qwen/qvq-72b",
            "input_tokens": 210,
            "output_tokens": 125,
            "byok_input_tokens": 0,
            "byok_output_tokens": 0,
            "non_byok_input_tokens": 210,
            "non_byok_output_tokens": 125
          },
          {
            "id": 49,
            "name": "deepseek/deepseek-v3",
            "input_tokens": 18,
            "output_tokens": 534,
            "byok_input_tokens": 0,
            "byok_output_tokens": 0,
            "non_byok_input_tokens": 18,
            "non_byok_output_tokens": 534
          }
        ]
      }
    ]
  },
  "msg": "查询成功"
}
```

***

### 7. 获取项目排行

查询项目使用量排行榜。

#### 接口信息

* **Path**: `/statistics/project-ranking`
* **Method**: `POST`
* **权限**: 已认证且具备相应数据访问权限的账号

#### 请求参数

| 参数名         | 类型      | 必填 | 说明   | 示例                   |
| ----------- | ------- | -- | ---- | -------------------- |
| start\_time | string  | 是  | 开始时间 | 2024-01-01T00:00:00Z |
| end\_time   | string  | 是  | 结束时间 | 2024-01-31T23:59:59Z |
| project\_id | integer | 否  | 项目ID | 1                    |

#### 请求示例

```http
POST /statistics/project-ranking
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "start_time": "2024-01-01T00:00:00Z",
  "end_time": "2024-01-31T23:59:59Z",
  "project_id": 35
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": {
    "sort_by": "cost",
    "rankings": [
      {
        "rank": 1,
        "project_id": 3,
        "project_name": "虚构示例项目 A",
        "requests": 45230,
        "cost": 582.45,
        "tokens": 8950000,
        "success_rate": 99.2
      },
      {
        "rank": 2,
        "project_id": 1,
        "project_name": "虚构示例项目 B",
        "requests": 38450,
        "cost": 465.80,
        "tokens": 7320000,
        "success_rate": 98.5
      },
      {
        "rank": 3,
        "project_id": 5,
        "project_name": "虚构示例项目 C",
        "requests": 28900,
        "cost": 312.20,
        "tokens": 5480000,
        "success_rate": 97.8
      }
    ]
  },
  "msg": "success"
}
```

***

### 8. 获取模型维度统计

按模型维度统计使用情况。

#### 接口信息

* **Path**: `/statistics/model-breakdown`
* **Method**: `POST`
* **权限**: 已认证且具备相应数据访问权限的账号

#### 请求参数

| 参数名         | 类型      | 必填 | 说明   | 示例                   |
| ----------- | ------- | -- | ---- | -------------------- |
| start\_time | string  | 是  | 开始时间 | 2024-01-01T00:00:00Z |
| end\_time   | string  | 是  | 结束时间 | 2024-01-31T23:59:59Z |
| project\_id | integer | 否  | 项目ID | 1                    |

#### 请求示例

```http
POST /statistics/model-breakdown
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "start_time": "2024-01-01T00:00:00Z",
  "end_time": "2024-01-31T23:59:59Z",
  "project_id": 1
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": {
    "models": [
      {
        "support_model_id": 11,
        "model_name": "qwen/qvq-72b",
        "provider_name": "",
        "total_requests": 6,
        "total_cost": 92664,
        "total_input_tokens": 252,
        "total_output_tokens": 150,
        "avg_latency": 172,
        "success_rate": 100
      },
      {
        "support_model_id": 49,
        "model_name": "deepseek/deepseek-v3",
        "provider_name": "",
        "total_requests": 3,
        "total_cost": 47388,
        "total_input_tokens": 18,
        "total_output_tokens": 534,
        "avg_latency": 108,
        "success_rate": 100
      },
      {
        "support_model_id": 9,
        "model_name": "qwen/qwen-vl-plus",
        "provider_name": "",
        "total_requests": 3,
        "total_cost": 70883,
        "total_input_tokens": 40,
        "total_output_tokens": 1824,
        "avg_latency": 50,
        "success_rate": 100
      },
      {
        "support_model_id": 9,
        "model_name": "qwen/qvq-72b",
        "provider_name": "",
        "total_requests": 2,
        "total_cost": 8277,
        "total_input_tokens": 28,
        "total_output_tokens": 203,
        "avg_latency": 33,
        "success_rate": 100
      }
    ]
  },
  "msg": "查询成功"
}
```

***

### 9. 获取RAM用户审计

查询RAM用户的操作审计日志。

#### 接口信息

* **Path**: `/statistics/ram-user-audit`
* **Method**: `POST`
* **权限**: 已认证且具备相应数据访问权限的账号

#### 请求参数

| 参数名         | 类型      | 必填 | 说明   | 示例                   |
| ----------- | ------- | -- | ---- | -------------------- |
| start\_time | string  | 是  | 开始时间 | 2024-01-01T00:00:00Z |
| end\_time   | string  | 是  | 结束时间 | 2024-01-31T23:59:59Z |
| project\_id | integer | 否  | 项目ID | 1                    |

#### 请求示例

```http
POST /statistics/ram-user-audit
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "end_time": "2025-12-18T15:59:59.059Z",
  "project_id": 35,
  "start_time": "2025-07-04T16:00:00.000Z"
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": {
    "users": [
      {
        "ram_user_id": 16,
        "ram_user_name": "lcw",
        "total_requests": 9,
        "total_cost": 152855,
        "total_input_tokens": 156,
        "total_output_tokens": 2498,
        "total_tokens": 2654,
        "token_percentage": 87.04493276484094
      },
      {
        "ram_user_id": 0,
        "ram_user_name": "用户-0",
        "total_requests": 5,
        "total_cost": 66357,
        "total_input_tokens": 182,
        "total_output_tokens": 213,
        "total_tokens": 395,
        "token_percentage": 12.955067235159067
      }
    ],
    "grand_total_tokens": 3049
  },
  "msg": "查询成功"
}
```

***

## 完整调用示例

### Python完整示例

```python
import requests
import hashlib
import hmac
import json
from datetime import datetime, timezone, timedelta

class StatisticsAPI:
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

    def get_overview(self, start_time, end_time, project_id=None):
        """获取Dashboard总览"""
        body = {
            "start_time": start_time,
            "end_time": end_time
        }
        if project_id:
            body["project_id"] = project_id
        return self._request("POST", "/statistics/overview", body)

    def get_error_statistics(self, start_time, end_time, project_id=None):
        """获取错误统计"""
        params = {
            "start_time": start_time,
            "end_time": end_time
        }
        if project_id:
            params["project_id"] = project_id
        return self._request("GET", "/statistics/error", params=params)

    def get_request_trend(self, start_time, end_time, project_id=None, granularity="day"):
        """获取请求趋势"""
        body = {
            "start_time": start_time,
            "end_time": end_time,
            "granularity": granularity
        }
        if project_id:
            body["project_id"] = project_id
        return self._request("POST", "/statistics/trends/requests", body)

    def get_model_usage_trend(self, start_time, end_time, project_id=None,
                             granularity="day", models=None):
        """获取模型用量趋势"""
        body = {
            "start_time": start_time,
            "end_time": end_time,
            "granularity": granularity
        }
        if project_id:
            body["project_id"] = project_id
        if models:
            body["models"] = models
        return self._request("POST", "/statistics/trends/model-usage", body)

    def get_cost_trend(self, start_time, end_time, project_id=None, granularity="day"):
        """获取费用趋势"""
        body = {
            "start_time": start_time,
            "end_time": end_time,
            "granularity": granularity
        }
        if project_id:
            body["project_id"] = project_id
        return self._request("POST", "/statistics/trends/cost", body)

    def get_token_trend(self, start_time, end_time, project_id=None, granularity="day"):
        """获取Token趋势"""
        body = {
            "start_time": start_time,
            "end_time": end_time,
            "granularity": granularity
        }
        if project_id:
            body["project_id"] = project_id
        return self._request("POST", "/statistics/trends/tokens", body)

    def get_project_ranking(self, start_time, end_time, sort_by="requests", limit=10):
        """获取项目排行"""
        return self._request("POST", "/statistics/project-ranking", {
            "start_time": start_time,
            "end_time": end_time,
            "sort_by": sort_by,
            "limit": limit
        })

    def get_model_breakdown(self, start_time, end_time, project_id=None):
        """获取模型维度统计"""
        body = {
            "start_time": start_time,
            "end_time": end_time
        }
        if project_id:
            body["project_id"] = project_id
        return self._request("POST", "/statistics/model-breakdown", body)

    def get_ram_user_audit(self, start_time, end_time, ram_user_id=None,
                          project_id=1):
        """获取RAM用户审计"""
        body = {
            "start_time": start_time,
            "end_time": end_time,
            "project_id": project_id

        }
        if ram_user_id:
            body["ram_user_id"] = ram_user_id
        return self._request("POST", "/statistics/ram-user-audit", body)


## 使用示例
if __name__ == "__main__":
    api = StatisticsAPI(
        base_url="YOUR_GATEWAY_MANAGEMENT_BASE_URL",
        access_key_id="AK_xxx",
        secret_key="SK_xxx"
    )

    # 定义时间范围（最近30天）
    end_time = datetime.now(timezone.utc)
    start_time = end_time - timedelta(days=30)

    start_str = start_time.strftime('%Y-%m-%dT%H:%M:%SZ')
    end_str = end_time.strftime('%Y-%m-%dT%H:%M:%SZ')

    # 1. 获取Dashboard总览
    overview = api.get_overview(start_str, end_str, project_id=1)
    print("Dashboard总览:", json.dumps(overview, indent=2))

    # 2. 获取错误统计
    errors = api.get_error_statistics(start_str, end_str, project_id=1)
    print("\n错误统计:", json.dumps(errors, indent=2))

    # 3. 获取请求趋势（按天）
    request_trend = api.get_request_trend(start_str, end_str, project_id=1, granularity="day")
    print("\n请求趋势:", json.dumps(request_trend, indent=2))

    # 4. 获取模型用量趋势
    model_trend = api.get_model_usage_trend(
        start_str, end_str,
        project_id=1,
        models=["gpt-4", "gpt-3.5-turbo"]
    )
    print("\n模型用量趋势:", json.dumps(model_trend, indent=2))

    # 5. 获取费用趋势
    cost_trend = api.get_cost_trend(start_str, end_str, project_id=1)
    print("\n费用趋势:", json.dumps(cost_trend, indent=2))

    # 6. 获取Token趋势
    token_trend = api.get_token_trend(start_str, end_str, project_id=1)
    print("\n Token趋势:", json.dumps(token_trend, indent=2))

    # 7. 获取项目排行（按费用排序）
    project_ranking = api.get_project_ranking(start_str, end_str, sort_by="cost", limit=5)
    print("\n项目排行:", json.dumps(project_ranking, indent=2))

    # 8. 获取模型维度统计
    model_breakdown = api.get_model_breakdown(start_str, end_str, project_id=1)
    print("\n模型维度统计:", json.dumps(model_breakdown, indent=2))

    # 9. 获取RAM用户审计
    audit = api.get_ram_user_audit(start_str, end_str, ram_user_id=1001, project_id=25)
    print("\nRAM用户审计:", json.dumps(audit, indent=2))
```

***

## 使用场景

### 场景1: 月度报告生成

生成完整的月度使用报告：

```python
## 获取上个月的统计数据
end_time = datetime(2024, 2, 1, 0, 0, 0, tzinfo=timezone.utc)
start_time = datetime(2024, 1, 1, 0, 0, 0, tzinfo=timezone.utc)

start_str = start_time.strftime('%Y-%m-%dT%H:%M:%SZ')
end_str = end_time.strftime('%Y-%m-%dT%H:%M:%SZ')

## 收集所有统计数据
report = {
    "overview": api.get_overview(start_str, end_str),
    "request_trend": api.get_request_trend(start_str, end_str, granularity="day"),
    "cost_trend": api.get_cost_trend(start_str, end_str, granularity="day"),
    "model_breakdown": api.get_model_breakdown(start_str, end_str),
    "project_ranking": api.get_project_ranking(start_str, end_str, sort_by="cost", limit=10)
}

## 生成PDF或Excel报告
generate_monthly_report(report)
```

### 场景2: 实时监控告警

实时监控错误率和成本异常：

```python
import time

def monitor_statistics():
    # 获取最近1小时的数据
    end_time = datetime.now(timezone.utc)
    start_time = end_time - timedelta(hours=1)

    start_str = start_time.strftime('%Y-%m-%dT%H:%M:%SZ')
    end_str = end_time.strftime('%Y-%m-%dT%H:%M:%SZ')

    # 获取错误统计
    errors = api.get_error_statistics(start_str, end_str)
    error_rate = errors['data']['error_rate']

    # 告警阈值检查
    if error_rate > 5.0:
        send_alert(f"错误率异常: {error_rate}%")

    # 获取费用趋势
    cost = api.get_cost_trend(start_str, end_str, granularity="hour")
    hourly_cost = cost['data']['series'][-1]['total_cost']

    # 费用告警
    if hourly_cost > 100:
        send_alert(f"小时费用异常: ${hourly_cost}")

## 每5分钟检查一次
while True:
    monitor_statistics()
    time.sleep(300)
```

### 场景3: 项目性能对比

对比不同项目的性能表现：

```python
def compare_projects(project_ids, start_time, end_time):
    comparison = []

    for project_id in project_ids:
        overview = api.get_overview(start_time, end_time, project_id=project_id)
        data = overview['data']

        comparison.append({
            "project_id": project_id,
            "total_requests": data['total_requests'],
            "total_cost": data['total_cost'],
            "success_rate": data['success_rate'],
            "avg_latency": data['avg_latency']
        })

    # 排序和分析
    comparison.sort(key=lambda x: x['total_cost'], reverse=True)
    return comparison

## 对比前5个项目
projects = compare_projects([1, 2, 3, 4, 5], start_str, end_str)
```

### 场景4: 成本优化分析

分析成本构成并提供优化建议：

```python
def analyze_cost_optimization(start_time, end_time, project_id):
    # 获取模型维度统计
    model_breakdown = api.get_model_breakdown(start_time, end_time, project_id)

    suggestions = []

    for model in model_breakdown['data']['models']:
        # 计算平均每请求成本
        cost_per_request = model['cost'] / model['requests']

        # 如果使用gpt-4但平均token数很小，建议使用gpt-3.5-turbo
        if model['model'] == 'gpt-4':
            avg_tokens = (model['input_tokens'] + model['output_tokens']) / model['requests']
            if avg_tokens < 500:
                suggestions.append({
                    "model": "gpt-4",
                    "issue": "平均token数较少但使用昂贵模型",
                    "suggestion": "考虑对简单任务使用gpt-3.5-turbo",
                    "potential_savings": model['cost'] * 0.6  # 估算节省60%成本
                })

    return suggestions

## 获取优化建议
suggestions = analyze_cost_optimization(start_str, end_str, project_id=1)
```

***

## 错误处理

HTTP 状态码与响应 JSON 中的业务 `code` 分别处理；不能把 HTTP 400、401、403 直接当作业务码，也不能只凭 HTTP 200 判断操作成功。结合 `code`、`msg` 和 `data.reason` 处理异常。

* 认证失败：检查凭证状态、时间戳、签名 Path 与请求体字节。
* 参数或权限错误：检查本页参数、账号身份及项目授权范围。
* `JWT_REQUIRED`：该操作需要 JWT 登录态。
* `STEP_UP_REQUIRED`：按响应提供的验证方式完成敏感操作验证后重试。

公共响应说明见[认证与签名](/broken/pages/e8167923d9de15f636aa273e8250fa11b46d254a#%E5%93%8D%E5%BA%94%E4%B8%8E%E9%94%99%E8%AF%AF%E5%A4%84%E7%90%86)。

## 注意事项

1. **时间范围**: 建议查询时间范围不超过90天，长时间范围查询可能较慢
2. **时间粒度**:
   * `hour`: 适合查询1-7天的数据
   * `day`: 适合查询7-90天的数据
   * `week`: 适合查询1-12个月的数据
   * `month`: 适合查询1年以上的数据
3. **数据延迟**: 统计数据可能有5-10分钟的延迟
4. **权限范围**:
   * 主账号和RAM管理员可查看所有项目数据
   * RAM成员只能查看有权限的项目数据
5. **时间格式**: 所有时间参数必须使用ISO 8601格式（UTC时区）



## 示例运行范围

完整客户端示例需要 Python 3 与 requests；JavaScript 客户端需要 Node.js 与 axios。后续业务片段复用前文创建的客户端实例，不是独立脚本。执行创建、修改或删除前，仅使用获准的测试环境及测试数据。
