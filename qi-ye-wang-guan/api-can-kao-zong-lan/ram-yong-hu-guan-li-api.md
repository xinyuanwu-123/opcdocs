# ram 用户管理 api

> 创建、查询、编辑、重置密码和删除企业 RAM 用户。

## 概述

RAM（Resource Access Management）用户管理API允许主账号创建和管理子账号，实现企业内部的权限分级管理。

## 基础信息

* **Base URL**: `YOUR_GATEWAY_MANAGEMENT_BASE_URL`
* **认证方式**: 见下方认证与权限说明
* **请求格式**: JSON
* **响应格式**: JSON

## 认证与权限说明

管理地址使用 `YOUR_GATEWAY_MANAGEMENT_BASE_URL`，与模型调用 Base URL 分开配置。JWT 通过登录 Cookie 或 `x-token` 请求头传递；AccessKey 请求使用下文签名请求头。

开启操作保护后，受保护的写操作需要 JWT 和有效的敏感操作验证授权。收到 `JWT_REQUIRED` 时使用登录态；收到 `STEP_UP_REQUIRED` 时按返回的验证方式完成授权后重试。认证规则见[API 认证与签名方法](api-ren-zheng-yu-qian-ming-ban-fa.md)。

主账号管理 RAM 账号。列表查询也支持 RAM 管理员；详情查询中，主账号指定 `ram_user_id`，子账号读取自身详情。`GET /ram/user/info` 要求管理员权限及 RAM 子账号身份。

## API接口列表

### 1. 创建RAM用户

创建一个新的RAM子账号。

#### 接口信息

* **Path**: `/ram/user/create`
* **Method**: `POST`
* **权限**: 仅主账号

#### 请求参数

| 参数名       | 类型     | 必填 | 说明                             | 示例                     |
| --------- | ------ | -- | ------------------------------ | ---------------------- |
| name      | string | 是  | RAM用户名称                        | test-ram-user          |
| password  | string | 是  | 登录密码，至少8位                      | YOUR\_SECURE\_PASSWORD |
| phone     | string | 是  | 有效的 11 位手机号                    | 13800000000            |
| authroity | string | 是  | 企业权限：admin/member/finance      | member                 |
| projects  | array  | 否  | 项目分配，每项包含 project\_id、role\_id | 见下方示例                  |

**authroity 取值**（请求字段按此拼写）：

* `admin`: RAM管理员，可以查看所有项目，管理BYOK和角色
* `member`: RAM成员，只能访问被授权的项目
* `finance`: 财务账号

#### 请求示例

```http
POST /ram/user/create
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "name": "dev-team-user",
  "password": "YOUR_SECURE_PASSWORD",
  "phone": "13800000000",
  "authroity": "member",
  "projects": [{"project_id": 1, "role_id": 2}]
}
```

#### 响应示例

**成功响应** (200 OK):

```json
{
  "code": 0,
  "data": {},
  "msg": "创建成功"
}
```

操作失败时读取响应的 `code`、`msg` 和 `data.reason`，处理方式见本页「错误处理」。

#### Python调用示例

先将[认证文档](api-ren-zheng-yu-qian-ming-ban-fa.md)的 Python 签名实现保存为 `signature_helper.py`，并安装 `requests`。本段依赖该文件。

```python
import requests
import json
from signature_helper import calculate_signature
from urllib.parse import urlsplit

## 配置
base_url = "YOUR_GATEWAY_MANAGEMENT_BASE_URL"
access_key_id = "AK_xxx"
secret_key = "SK_xxx"

## 请求参数
body = {
    "name": "dev-team-user",
    "password": "YOUR_SECURE_PASSWORD",
    "phone": "13800000000",
    "authroity": "member",
    "projects": [{"project_id": 1, "role_id": 2}]
}

## 计算签名
method = "POST"
uri = "/ram/user/create"
url = base_url.rstrip("/") + uri
body_str = json.dumps(body, separators=(",", ":"))
timestamp, signature = calculate_signature(method, urlsplit(url).path, body_str, secret_key)

## 发送请求
response = requests.post(
    url,
    data=body_str.encode('utf-8'),
    timeout=30,
    headers={
        "Content-Type": "application/json",
        "X-Access-Key-Id": access_key_id,
        "X-Timestamp": timestamp,
        "X-Signature": signature
    }
)

print(response.json())
```

***

### 2. 获取RAM用户列表

按账号名称查询 RAM 用户列表；请求只接收 `account_name`，不使用分页参数。

#### 接口信息

* **Path**: `/ram/user/list`
* **Method**: `POST`
* **权限**: 主账号、RAM管理员

#### 请求参数

| 参数名           | 类型     | 必填 | 说明           | 示例    |
| ------------- | ------ | -- | ------------ | ----- |
| account\_name | string | 否  | 按用户名筛选（模糊搜索） | xxxxx |

#### 请求示例

```http
POST /ram/user/list
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "account_name": "xxxxxx"
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": [
    {
      "id": 28,
      "user_id": 4,
      "account_id": 7468125380,
      "name": "string",
      "status": 1,
      "authority": "string"
    },
    {
      "id": 29,
      "user_id": 4,
      "account_id": 1849937,
      "name": "string",
      "status": 1,
      "authority": "string"
    }
  ],
  "msg": "获取子用户列表成功"
}
```

***

### 3. 获取RAM用户详情

主账号通过 `ram_user_id` 查询指定 RAM 用户；RAM 子账号使用当前身份读取自身详情。

#### 接口信息

* **Path**: `/ram/user/detail`
* **Method**: `POST`
* **权限**: 主账号查询指定用户；RAM 子账号查询自身

#### 请求参数

| 参数名           | 类型      | 必填    | 说明                  | 示例 |
| ------------- | ------- | ----- | ------------------- | -- |
| ram\_user\_id | integer | 主账号必填 | RAM 用户 ID；子账号读取自身身份 | 16 |

#### 请求示例

```http
POST /ram/user/detail
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "ram_user_id": 16
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": {
    "id": 16,
    "user_id": 4,
    "account_id": 4668925877,
    "name": "demo-user",
    "status": 1,
    "authority": "member",
    "ram_user_project_roles": [
      {
        "ID": 56,
        "CreatedAt": "2025-12-03T17:55:43.613148+08:00",
        "UpdatedAt": "2025-12-03T17:55:43.613148+08:00",
        "RamUserID": 16,
        "EnterpriseGatewayProjectID": 5,
        "RoleID": 11,
        "Role": {
          "ID": 11,
          "CreatedAt": "2025-11-21T14:46:12.691133+08:00",
          "UpdatedAt": "2025-12-17T11:06:05.500465+08:00",
          "UserId": 4,
          "Name": "有限制管理员",
          "Desc": "",
          "Auth": "manager",
          "TpmLimit": 10,
          "RpmLimit": 1000,
          "Budget": 20,
          "BudgetKind": "daily"
        }
      },
      {
        "ID": 91,
        "CreatedAt": "2025-12-19T14:09:36.398269+08:00",
        "UpdatedAt": "2025-12-19T14:09:36.398269+08:00",
        "RamUserID": 16,
        "EnterpriseGatewayProjectID": 36,
        "RoleID": 11,
        "Role": {
          "ID": 11,
          "CreatedAt": "2025-11-21T14:46:12.691133+08:00",
          "UpdatedAt": "2025-12-17T11:06:05.500465+08:00",
          "UserId": 4,
          "Name": "有限制管理员",
          "Desc": "",
          "Auth": "manager",
          "TpmLimit": 10,
          "RpmLimit": 1000,
          "Budget": 20,
          "BudgetKind": "daily"
        }
      },
      {
        "ID": 97,
        "CreatedAt": "2025-12-19T15:44:58.443568+08:00",
        "UpdatedAt": "2025-12-19T15:44:58.443568+08:00",
        "RamUserID": 16,
        "EnterpriseGatewayProjectID": 35,
        "RoleID": 5,
        "Role": {
          "ID": 5,
          "CreatedAt": "2025-11-20T16:11:20.112738+08:00",
          "UpdatedAt": "2025-12-26T10:14:30.316569+08:00",
          "UserId": 4,
          "Name": "无限制管理员",
          "Desc": "",
          "Auth": "manager",
          "TpmLimit": 20000,
          "RpmLimit": 1000,
          "Budget": 0,
          "BudgetKind": "unlimited"
        }
      }
    ]
  },
  "msg": "获取子用户详情成功"
}
```

***

### 4. 获取当前RAM用户信息

获取当前 RAM 管理员的信息，需要认证身份中包含 RAM 子账号 ID。

#### 接口信息

* **Path**: `/ram/user/info`
* **Method**: `GET`
* **权限**: RAM 管理员，认证身份须包含 RAM 子账号 ID

#### 请求参数

无需请求体。

#### 请求示例

```http
GET /ram/user/info
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx
```

#### 响应示例

```json
{
  "code": 0,
  "data": {
    "user_id": 1,
    "account_type": "sub",
    "ram_user_id": 1001,
    "ram_username": "dev-team-user",
    "authority": "admin",
    "projects": [
      {
        "project_id": 1,
        "project_name": "虚构示例项目 A",
        "role": "viewer"
      }
    ]
  },
  "msg": "success"
}
```

***

### 5. 修改RAM用户

更新RAM用户的信息。

#### 接口信息

* **Path**: `/ram/user/update`
* **Method**: `POST`
* **权限**: 仅主账号

#### 请求参数

| 参数名           | 类型      | 必填 | 说明       | 示例                   |
| ------------- | ------- | -- | -------- | -------------------- |
| ram\_user\_id | integer | 是  | RAM用户的ID | 12                   |
| name          | string  | 否  | 新的用户名    | new-ram-user         |
| authroity     | string  | 否  | 企业权限     | admin/member/finance |
| status        | integer | 否  | 账号状态     | 1                    |

#### 请求示例

```http
POST /ram/user/update
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "authroity": "member",
  "name": "string",
  "ram_user_id": 0
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": {},
  "msg": "更新子用户成功"
}
```

***

### 6. 删除RAM用户

删除指定的RAM用户。

#### 接口信息

* **Path**: `/ram/user/delete`
* **Method**: `POST`
* **权限**: 仅主账号

#### 请求参数

| 参数名           | 类型      | 必填 | 说明       | 示例 |
| ------------- | ------- | -- | -------- | -- |
| ram\_user\_id | integer | 是  | RAM用户的ID | 16 |

#### 请求示例

```http
POST /ram/user/delete
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "ram_user_id": 16
}
```

#### 响应示例

```json
{
  "code": 0,
  "msg": "删除成功"
}
```

***

### 7. 重置RAM用户密码

由主账号重置 RAM 用户密码。请求字段为 `ram_user_id`、`password`；开启操作保护时，先完成敏感操作验证，验证码不放在此请求体中。

#### 接口信息

* **Path**: `/ram/user/reset_password`
* **Method**: `POST`
* **权限**: 仅主账号

#### 请求参数

| 参数名           | 类型      | 必填 | 说明       | 示例                          |
| ------------- | ------- | -- | -------- | --------------------------- |
| ram\_user\_id | integer | 是  | RAM用户的ID | 16                          |
| password      | string  | 是  | 新密码，至少8位 | YOUR\_NEW\_SECURE\_PASSWORD |

#### 请求示例

```http
POST /ram/user/reset_password
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "ram_user_id": 100001,
  "password": "YOUR_NEW_SECURE_PASSWORD"
}
```

#### 响应示例

```json
{
  "code": 0,
  "msg": "密码重置成功"
}
```

***

### 8. 修改RAM用户密码

修改 RAM 用户密码，请求字段为 `ram_user_id`、`new_password`。

#### 接口信息

* **Path**: `/ram/user/update_password`
* **Method**: `POST`
* **权限**: 仅主账号

#### 请求参数

| 参数名           | 类型      | 必填 | 说明         | 示例                          |
| ------------- | ------- | -- | ---------- | --------------------------- |
| ram\_user\_id | integer | 是  | RAM用户的账号ID | 100001                      |
| new\_password | string  | 是  | 新密码，至少8位   | YOUR\_NEW\_SECURE\_PASSWORD |

#### 请求示例

```http
POST /ram/user/update_password
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "ram_user_id": 15,
  "new_password": "YOUR_NEW_SECURE_PASSWORD"
}
```

#### 响应示例

```json
{
  "code": 0,
  "msg": "更新子用户密码成功"
}
```

***

## 完整调用示例

### Python完整示例

安装 `requests`，设置 `SSY_MANAGEMENT_BASE_URL`，并设置 `SSY_ACCESS_KEY_ID` 与 `SSY_SECRET_KEY`，或设置 `SSY_JWT`。客户端传入 JWT 时使用 `x-token`。

```python
import requests
import hashlib
import hmac
import json
from datetime import datetime, timezone

class RAMUserAPI:
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

    def create_ram_user(self, name, password, phone, authroity, projects=None):
        body = {"name": name, "password": password, "phone": phone, "authroity": authroity}
        if projects is not None:
            body["projects"] = projects
        return self._request("POST", "/ram/user/create", body)

    def list_ram_users(self, account_name=None):
        body = {} if account_name is None else {"account_name": account_name}
        return self._request("POST", "/ram/user/list", body)

    def get_ram_user_detail(self, ram_user_id=None):
        body = {} if ram_user_id is None else {"ram_user_id": ram_user_id}
        return self._request("POST", "/ram/user/detail", body)

    def get_current_user_info(self):
        return self._request("GET", "/ram/user/info")

    def update_ram_user(self, ram_user_id, name=None, authroity=None, status=None):
        body = {"ram_user_id": ram_user_id}
        for key, value in {"name": name, "authroity": authroity, "status": status}.items():
            if value is not None:
                body[key] = value
        return self._request("POST", "/ram/user/update", body)

    def delete_ram_user(self, ram_user_id):
        return self._request("POST", "/ram/user/delete", {"ram_user_id": ram_user_id})

    def reset_password(self, ram_user_id, password):
        return self._request("POST", "/ram/user/reset_password", {"ram_user_id": ram_user_id, "password": password})

    def update_password(self, ram_user_id, new_password):
        return self._request("POST", "/ram/user/update_password", {"ram_user_id": ram_user_id, "new_password": new_password})


## 使用示例：先通过只读查询检查连接，写操作按上方方法传参。
if __name__ == "__main__":
    import os
    api = RAMUserAPI(
        base_url=os.environ["SSY_MANAGEMENT_BASE_URL"],
        access_key_id=os.environ.get("SSY_ACCESS_KEY_ID"),
        secret_key=os.environ.get("SSY_SECRET_KEY"),
        jwt=os.environ.get("SSY_JWT")
    )
    print(api.list_ram_users(account_name="demo-user"))

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

1. **用户名唯一性**: RAM用户名在主账号下必须唯一
2. **密码强度**: 密码至少8位，建议包含大小写字母、数字和特殊字符
3. **权限继承**: RAM用户的权限不能超过创建它的主账号
4. **状态管理**: 禁用RAM用户后，其AccessKey将无法使用
5. **删除影响**: 删除RAM用户会同时删除其所有AccessKey和项目角色绑定



## 示例运行范围

完整客户端示例需要 Python 3 与 requests；后续业务片段复用前文创建的客户端实例，不是独立脚本。执行创建、修改或删除前，仅使用获准的测试环境及测试数据。
