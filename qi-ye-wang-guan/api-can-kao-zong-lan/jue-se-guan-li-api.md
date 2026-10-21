# 角色管理 api

> 创建、查询、更新和删除项目角色及其权限与限额。

## 概述

角色管理API用于创建和管理项目角色，定义RAM用户在项目中的权限和资源限制。角色包含访问权限（manager/viewer/user/router\_editor）和API调用限额（RPM、TPM、Budget）。

## 基础信息

* **Base URL**: `YOUR_GATEWAY_MANAGEMENT_BASE_URL`
* **认证方式**: 见下方认证与权限说明
* **请求格式**: JSON
* **响应格式**: JSON

## 认证与权限说明

管理地址使用 `YOUR_GATEWAY_MANAGEMENT_BASE_URL`，与模型调用 Base URL 分开配置。JWT 通过登录 Cookie 或 `x-token` 请求头传递；AccessKey 请求使用下文签名请求头。

开启操作保护后，受保护的写操作需要 JWT 和有效的敏感操作验证授权。收到 `JWT_REQUIRED` 时使用登录态；收到 `STEP_UP_REQUIRED` 时按返回的验证方式完成授权后重试。认证规则见[API 认证与签名方法](/broken/pages/e8167923d9de15f636aa273e8250fa11b46d254a)。

| 操作     | 主账号 | RAM管理员 | RAM成员 |
| ------ | --- | ------ | ----- |
| 创建角色   | ✅   | ✅      | ❌     |
| 查看角色列表 | ✅   | ✅      | ✅     |
| 更新角色   | ✅   | ✅      | ❌     |
| 删除角色   | ✅   | ✅      | ❌     |

## 角色类型说明

### 权限类型（permissions）

| 类型             | 说明     | 可执行操作                  |
| -------------- | ------ | ---------------------- |
| manager        | 项目管理员  | 管理项目配置、路由、模型、成员        |
| viewer         | 项目查看者  | 查看项目信息，创建本人的项目 API Key |
| user           | 项目成员   | 受项目授权范围约束              |
| router\_editor | 路由编辑角色 | 受项目授权范围约束              |

### 限额类型

| 限额项          | 说明          | 单位                     |
| ------------ | ----------- | ---------------------- |
| rpm\_limit   | 每分钟请求数限制    | 次/分钟                   |
| tpm\_limit   | 每分钟Token数限制 | Token/分钟               |
| budget       | 请求中的整数预算    | 返回 Budget 为请求值 × 10000 |
| budget\_kind | 预算周期        | dayily/monthily        |

`budget` 与列表返回的 `Budget` 使用不同计量尺度：请求值 `100` 对应返回值 `1000000`。将列表值用于更新请求时，先除以 `10000`。`budget_kind` 按接口枚举填写 `dayily` 或 `monthily`。

## API接口列表

### 1. 创建角色

创建一个新的项目角色。

#### 接口信息

* **Path**: `/role/create`
* **Method**: `POST`
* **权限**: 主账号、RAM管理员

#### 请求参数

| 参数名          | 类型      | 必填 | 说明                                      | 示例        |
| ------------ | ------- | -- | --------------------------------------- | --------- |
| role\_name   | string  | 是  | 角色名称                                    | 开发团队角色    |
| desc         | string  | 否  | 角色描述                                    | 用于开发环境的角色 |
| permissions  | string  | 是  | 权限类型：manager/viewer/user/router\_editor | manager   |
| tpm\_limit   | integer | 否  | TPM限制                                   | 1000000   |
| rpm\_limit   | integer | 否  | RPM限制                                   | 100       |
| budget       | integer | 否  | 预算限制                                    | 1000      |
| budget\_kind | string  | 是  | 预算周期：dayily/monthily                    | monthily  |

#### 请求示例

```http
POST /role/create
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "role_name": "开发团队角色",
  "desc": "开发环境使用的管理员角色",
  "permissions": "manager",
  "tpm_limit": 1000000,
  "rpm_limit": 100,
  "budget": 5000,
  "budget_kind": "monthily"
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

先将[认证文档](/broken/pages/e8167923d9de15f636aa273e8250fa11b46d254a)的 Python 签名实现保存为 `signature_helper.py`，并安装 `requests`。本段依赖该文件。

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
    "role_name": "开发团队角色",
    "desc": "开发环境使用的管理员角色",
    "permissions": "manager",
    "tpm_limit": 1000000,
    "rpm_limit": 100,
    "budget": 5000,
    "budget_kind": "monthily"
}

## 计算签名
method = "POST"
uri = "/role/create"
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

### 2. 获取角色列表

查询角色列表。

#### 接口信息

* **Path**: `/role/list`
* **Method**: `GET`
* **权限**: 所有用户

#### 请求示例

```http
GET /role/list
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx
```

#### 响应示例

`data` 直接返回角色数组，不使用 `data.list` 或分页参数。字段大小写按响应读取。

```json
{
  "code": 0,
  "data": [
    {
      "ID": 1,
      "CreatedAt": "2026-10-14T00:00:00Z",
      "UpdatedAt": "2026-10-14T00:00:00Z",
      "UserId": 1,
      "Name": "开发成员",
      "Desc": "开发环境使用",
      "Auth": "user",
      "TpmLimit": 100000,
      "RpmLimit": 30,
      "Budget": 1000000,
      "BudgetKind": "monthily",
      "member_count": 2,
      "project_count": 1
    }
  ],
  "msg": "success"
}
```

***

### 3. 更新角色

更新现有角色的信息。请一并提交完整的目标角色配置；省略的限额字段可能按零值写入，不要只传发生变化的字段。

#### 接口信息

* **Path**: `/role/update`
* **Method**: `POST`
* **权限**: 主账号、RAM管理员

#### 请求参数

| 参数名          | 类型      | 必填 | 说明      | 示例      |
| ------------ | ------- | -- | ------- | ------- |
| role\_id     | integer | 是  | 角色ID    | 1       |
| role\_name   | string  | 是  | 新的角色名称  | 更新后的角色名 |
| desc         | string  | 否  | 新的角色描述  | 更新后的描述  |
| permissions  | string  | 是  | 新的权限类型  | viewer  |
| tpm\_limit   | integer | 否  | 新的TPM限制 | 2000000 |
| rpm\_limit   | integer | 否  | 新的RPM限制 | 200     |
| budget       | integer | 否  | 新的预算限制  | 10000   |
| budget\_kind | string  | 是  | 新的预算周期  | dayily  |

#### 请求示例

```http
POST /role/update
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "role_id": 1,
  "role_name": "开发成员",
  "permissions": "user",
  "budget_kind": "monthily",
  "desc": "开发环境使用",
  "tpm_limit": 100000,
  "rpm_limit": 30,
  "budget": 100
}
```

#### 响应示例

```json
{
  "code": 0,
  "data": {},
  "msg": "更新成功"
}
```

***

### 4. 删除角色

删除指定的角色。

#### 接口信息

* **Path**: `/role/delete`
* **Method**: `POST`
* **权限**: 主账号、RAM管理员

#### 请求参数

| 参数名      | 类型      | 必填 | 说明   | 示例 |
| -------- | ------- | -- | ---- | -- |
| role\_id | integer | 是  | 角色ID | 1  |

#### 请求示例

```http
POST /role/delete
Content-Type: application/json
X-Access-Key-Id: AK_xxx
X-Timestamp: 2024-01-01T12:00:00+00:00
X-Signature: xxx

{
  "role_id": 1
}
```

#### 响应示例

**成功响应**:

```json
{
  "code": 0,
  "msg": "删除成功"
}
```

操作失败时读取响应的 `code`、`msg` 和 `data.reason`，处理方式见本页「错误处理」。

***

## 完整调用示例

### Python完整示例

安装 `requests`，设置 `SSY_MANAGEMENT_BASE_URL` 和 AccessKey/SecretKey 环境变量，或设置 `SSY_JWT`。

```python
import requests
import hashlib
import hmac
import json
from datetime import datetime, timezone

class RoleAPI:
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

    def create_role(self, role_name, permissions, budget_kind, tpm_limit=0, rpm_limit=0, budget=0, desc=""):
        return self._request("POST", "/role/create", {
            "role_name": role_name, "permissions": permissions, "budget_kind": budget_kind,
            "tpm_limit": tpm_limit, "rpm_limit": rpm_limit, "budget": budget, "desc": desc
        })

    def list_roles(self):
        return self._request("GET", "/role/list")

    def update_role(self, role_id, role_name, permissions, budget_kind, tpm_limit, rpm_limit, budget, desc=""):
        return self._request("POST", "/role/update", {
            "role_id": role_id, "role_name": role_name, "permissions": permissions,
            "budget_kind": budget_kind, "tpm_limit": tpm_limit, "rpm_limit": rpm_limit,
            "budget": budget, "desc": desc
        })

    def delete_role(self, role_id):
        return self._request("POST", "/role/delete", {"role_id": role_id})


## 使用示例
if __name__ == "__main__":
    import os
    api = RoleAPI(os.environ["SSY_MANAGEMENT_BASE_URL"],
                  os.environ.get("SSY_ACCESS_KEY_ID"), os.environ.get("SSY_SECRET_KEY"),
                  jwt=os.environ.get("SSY_JWT"))
    print(api.list_roles())

```

### Node.js完整示例

使用 Node.js 18 或以上版本，先执行 `npm install axios`，将代码保存为 `.cjs` 文件。设置管理地址与凭证环境变量；底部示例查询角色列表。

```javascript
const crypto = require('crypto');
const axios = require('axios');

class RoleAPI {
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

    async createRole(roleName, permissions, budgetKind, tpmLimit = 0, rpmLimit = 0, budget = 0, desc = '') {
        return this.request('POST', '/role/create', {
            role_name: roleName, permissions, budget_kind: budgetKind,
            tpm_limit: tpmLimit, rpm_limit: rpmLimit, budget, desc
        });
    }

    async listRoles() {
        return this.request('GET', '/role/list');
    }

    async updateRole(roleId, config) {
        for (const field of ['role_name', 'permissions', 'budget_kind', 'tpm_limit', 'rpm_limit', 'budget']) {
            if (config[field] == null) throw new Error(`请提供完整目标配置：${field}`);
        }
        return this.request('POST', '/role/update', { ...config, role_id: roleId });
    }

    async deleteRole(roleId) {
        return this.request('POST', '/role/delete', { role_id: roleId });
    }
}

// 使用示例
(async () => {
    const api = new RoleAPI(process.env.SSY_MANAGEMENT_BASE_URL,
        process.env.SSY_ACCESS_KEY_ID, process.env.SSY_SECRET_KEY, process.env.SSY_JWT);
    console.log(await api.listRoles());
})().catch(console.error);

```

***

## 更新角色限额示例

以下代码复用前文的 `api` 实例。先读取角色，保留其名称、权限、预算周期及仍需保留的限额，再提交完整目标配置。

```python
api.update_role(
    role_id=1, role_name="开发成员", permissions="user", budget_kind="monthily",
    tpm_limit=100000, rpm_limit=30, budget=100, desc="开发环境使用"
)
```

## 错误处理

HTTP 状态码与响应 JSON 中的业务 `code` 分别处理；不能把 HTTP 400、401、403 直接当作业务码，也不能只凭 HTTP 200 判断操作成功。结合 `code`、`msg` 和 `data.reason` 处理异常。

* 认证失败：检查凭证状态、时间戳、签名 Path 与请求体字节。
* 参数或权限错误：检查本页参数、账号身份及项目授权范围。
* `JWT_REQUIRED`：该操作需要 JWT 登录态。
* `STEP_UP_REQUIRED`：按响应提供的验证方式完成敏感操作验证后重试。

公共响应说明见[认证与签名](api-ren-zheng-yu-qian-ming-ban-fa.md)。

## 注意事项

1. **角色名称唯一性**: 角色名称在主账号下必须唯一
2. **删除限制**: 正在被RAM用户使用的角色无法删除
3. **权限粒度**: 角色权限与成员所在项目共同限定操作范围
4. **限额设置**: 建议根据实际需求合理设置RPM、TPM和Budget
5. **预算周期**: `dayily` 表示日预算周期，`monthily` 表示月预算周期；这是限额周期，不是套餐计费周期



## 示例运行范围

完整客户端示例需要 Python 3 与 requests；JavaScript 客户端需要 Node.js 与 axios。后续业务片段复用前文创建的客户端实例，不是独立脚本。执行创建、修改或删除前，仅使用获准的测试环境及测试数据。
