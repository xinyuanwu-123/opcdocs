# 获取APIKey详情

**GET** `/v1/key`

## 请求示例代码

```bash
curl --location 'https://router.shengsuanyun.com/api/v1/key'
```

## 返回响应

#### 🟢 200 成功

`application/json`

```json
{
  "success": true,
  "data": {
    "name": "string",
    "desc": "string",
    "is_banned": true,
    "is_expired": true,
    "max_quota": 0,
    "consumed_amount": 0,
    "supported_models": "string",
    "expires_at": "string"
  },
  "error": {
    "message": "string",
    "type": "string",
    "code": "string"
  }
}
```
