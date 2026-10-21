# api认证与签名办法

> 管理接口的公共请求头、签名算法、多语言实现、错误处理与安全建议。



## 概述

企业网关管理接口使用 AccessKey/SecretKey 签名或 JWT 登录态；部分项目查询也接受项目 API Key。凭证支持范围与账号权限分别校验。

* 管理地址：`YOUR_GATEWAY_MANAGEMENT_BASE_URL`，填写管理服务地址及接入前缀，不使用模型调用 Base URL 代替。
* JWT：通过登录 Cookie 或 `x-token` 请求头传递。
* AccessKey：发送 `X-Access-Key-Id`、`X-Timestamp`、`X-Signature`。

### 项目 API Key 支持的查询

| 方法   | 路径                                        |
| ---- | ----------------------------------------- |
| GET  | `/project/list`                           |
| POST | `/project/router/list`                    |
| POST | `/project/models/list`                    |
| POST | `/project/apikey/list`                    |
| POST | `/statistics/enterprise/task/gatewaybill` |

项目 API Key 访问受项目作用域、Key 状态、关联账号、企业及 Key 的 IP 白名单约束；它不提供其他管理接口的通用访问权限。

### 操作保护

开启操作保护后，受保护的敏感写操作需要 JWT 登录态和有效的验证授权。AccessKey 请求可能收到 `data.reason = JWT_REQUIRED`；JWT 缺少验证授权时收到 `data.reason = STEP_UP_REQUIRED`，响应同时提供验证范围和可用方式。按提示完成验证后重试。

关闭操作保护不改变接口自身的凭证与权限要求。安全验证、企业 AccessKey 管理、操作审计及使用报告使用 JWT 登录态。

## 获取AccessKey和SecretKey

### 创建AccessKey

联系系统管理员通过企业网关管理后台创建AccessKey，创建成功后会获得：

```json
{
  "access_key_id": "YOUR_ACCESS_KEY_ID",
  "secret_key": "YOUR_SECRET_KEY"
}
```

**⚠️ 重要提示**：

* `secret_key` 只在创建时显示一次，请务必妥善保存
* 丢失后无法找回，只能重新创建或轮换
* 建议定期轮换SecretKey以提高安全性

## 公共请求参数

所有使用AccessKey认证的API请求都需要在HTTP请求头中携带以下参数：

| 参数名             | 类型     | 必填 | 说明                     | 示例                        |
| --------------- | ------ | -- | ---------------------- | ------------------------- |
| X-Access-Key-Id | String | 是  | AccessKey ID           | YOUR\_ACCESS\_KEY\_ID     |
| X-Timestamp     | String | 是  | 请求时间戳（RFC3339格式）       | 2024-01-01T12:00:00+00:00 |
| X-Signature     | String | 是  | 请求签名（HMAC-SHA256，十六进制） | abc123...def456           |
| Content-Type    | String | 是  | 请求内容类型                 | application/json          |

### 时间戳格式说明

* **格式**：RFC3339标准格式 `YYYY-MM-DDTHH:mm:ss+HH:mm`
* **时区**：支持任意时区，建议使用UTC时间
* **有效期**：过去不超过 5 分钟，未来不超过 1 分钟
* **示例**：
  * UTC时间：`2024-01-01T12:00:00+00:00`
  * 北京时间：`2024-01-01T20:00:00+08:00`
  * 纽约时间：`2024-01-01T07:00:00-05:00`

## 签名算法

### 签名计算步骤

#### 第1步：计算请求体哈希

```
BodyHash = Hex(SHA256(RequestBody))
```

* 对实际发送的请求体字节进行 SHA256 哈希，再进行小写十六进制（Hex）编码
* 没有请求体时使用空字节数组计算哈希；`{}` 与空请求体是不同的字节

**示例**：

```javascript
// 请求体
const body = '{"name":"test-user","password":"YOUR_SECURE_PASSWORD"}'

// SHA256哈希后十六进制编码
const bodyHash = "013c1a46b7c341e46758ca835e0bc2636b3fcd2976f9df2ac0f5e45519b51f69"
```

#### 第2步：构造待签名字符串

```
StringToSign = HTTP方法 + "\n" +
               请求Path + "\n" +
               时间戳 + "\n" +
               请求体哈希
```

**示例**：

```
POST
/ram/user/create
2024-01-01T12:00:00+00:00
013c1a46b7c341e46758ca835e0bc2636b3fcd2976f9df2ac0f5e45519b51f69
```

#### 第3步：计算HMAC-SHA256签名

```
Signature = Hex(HMAC-SHA256(SecretKey, StringToSign))
```

* 使用SecretKey对StringToSign进行HMAC-SHA256签名
* 将签名结果进行小写十六进制（Hex）编码

### 完整示例

假设我们要调用创建RAM用户的API：

**请求参数**：

* Method: `POST`
* URI: `/ram/user/create`
* Timestamp: `2024-01-01T12:00:00+00:00`
* SecretKey: `YOUR_SECRET_KEY`
* Body: `{"name":"test-user","password":"YOUR_SECURE_PASSWORD","authroity":"member","phone":"13800000000"}`

**计算过程**：

```javascript
const { createHash, createHmac } = require('node:crypto');
const body = '{"name":"test-user","password":"YOUR_SECURE_PASSWORD","authroity":"member","phone":"13800000000"}';
const bodyHash = createHash('sha256').update(body, 'utf8').digest('hex');
const stringToSign = ['POST', '/ram/user/create', '2024-01-01T12:00:00+00:00', bodyHash].join('\n');
const signature = createHmac('sha256', 'YOUR_SECRET_KEY').update(stringToSign, 'utf8').digest('hex');
console.log({ bodyHash, signature });
```

签名中的 Path 不包含域名、Query 或片段。例如 `/role/list?keyword=demo` 的签名 Path 为 `/role/list`。地址带接入前缀时，Path 包含服务接收到的前缀。请求体只序列化一次，签名与发送使用完全相同的字节。

## 多语言签名实现

### Python

将下方代码保存为 `signature_helper.py`；实际发送请求时，使用同一份序列化后的字节作为请求体，不要再由 HTTP 库重新序列化。

```python
import hashlib
import hmac
from datetime import datetime, timezone

def calculate_signature(method, uri, body, secret_key):
    """
    计算API请求签名

    Args:
        method: HTTP方法，如 "POST"
        uri: 请求URI，如 "/ram/user/create"
        body: 请求体（字符串或字典）
        secret_key: SecretKey

    Returns:
        (timestamp, signature) 元组
    """
    # 1. 计算Body哈希（十六进制）
    if isinstance(body, dict):
        import json
        body_str = json.dumps(body, separators=(',', ':'))
    else:
        body_str = body if body else ''

    body_bytes = body_str.encode('utf-8')
    body_hash = hashlib.sha256(body_bytes).hexdigest()

    # 2. 生成时间戳
    timestamp = datetime.now(timezone.utc).strftime('%Y-%m-%dT%H:%M:%S%z')
    # 格式化时区部分 (+0000 -> +00:00)
    timestamp = timestamp[:-2] + ':' + timestamp[-2:]

    # 3. 构造待签名字符串
    string_to_sign = f"{method}\n{uri}\n{timestamp}\n{body_hash}"

    # 4. 计算HMAC-SHA256签名（十六进制）
    signature = hmac.new(
        secret_key.encode('utf-8'),
        string_to_sign.encode('utf-8'),
        hashlib.sha256
    ).hexdigest()

    return timestamp, signature

## 使用示例
method = "POST"
uri = "/ram/user/create"
body = {"name": "test-user", "password": "YOUR_SECURE_PASSWORD", "authroity": "member", "phone": "13800000000"}
secret_key = "YOUR_SECRET_KEY"

timestamp, signature = calculate_signature(method, uri, body, secret_key)

print(f"Timestamp: {timestamp}")
print(f"Signature: {signature}")
```

***

### JavaScript / Node.js

使用 Node.js 18 或以上版本，将代码保存为 `.cjs` 文件运行；仅使用内置 `crypto`，无需安装额外依赖。无请求体时传 `null` 或空字符串，空 JSON 对象则传 `{}`。

```javascript
const crypto = require('crypto');

/**
 * 计算API请求签名
 * @param {string} method - HTTP方法
 * @param {string} uri - 请求URI
 * @param {object|string} body - 请求体
 * @param {string} secretKey - SecretKey
 * @returns {{timestamp: string, signature: string}}
 */
function calculateSignature(method, uri, body, secretKey) {
    // 1. 计算Body哈希（十六进制）
    const bodyStr = body == null ? '' : typeof body === 'object'
        ? JSON.stringify(body)
        : (body || '');
    const bodyHash = crypto
        .createHash('sha256')
        .update(bodyStr)
        .digest('hex');

    // 2. 生成时间戳
    const timestamp = new Date().toISOString().replace(/\.\d{3}Z$/, '+00:00');

    // 3. 构造待签名字符串
    const stringToSign = `${method}\n${uri}\n${timestamp}\n${bodyHash}`;

    // 4. 计算HMAC-SHA256签名（十六进制）
    const signature = crypto
        .createHmac('sha256', secretKey)
        .update(stringToSign)
        .digest('hex');

    return { timestamp, signature };
}

// 使用示例
const method = 'POST';
const uri = '/ram/user/create';
const body = {
    name: 'test-user',
    password: 'YOUR_SECURE_PASSWORD',
    authroity: 'member',
    phone: '13800000000'
};
const secretKey = 'YOUR_SECRET_KEY';

const { timestamp, signature } = calculateSignature(method, uri, body, secretKey);

console.log(`Timestamp: ${timestamp}`);
console.log(`Signature: ${signature}`);
```

***

### Go

```go
package main

import (
    "crypto/hmac"
    "crypto/sha256"
    "encoding/hex"
    "encoding/json"
    "fmt"
    "time"
)

// CalculateSignature 计算API请求签名
func CalculateSignature(method, uri string, body interface{}, secretKey string) (timestamp, signature string, err error) {
    // 1. 计算Body哈希（十六进制）
    var bodyBytes []byte
    if body != nil {
        bodyBytes, err = json.Marshal(body)
        if err != nil {
            return "", "", fmt.Errorf("序列化请求体失败: %w", err)
        }
    }

    hash := sha256.New()
    hash.Write(bodyBytes)
    bodyHash := hex.EncodeToString(hash.Sum(nil))

    // 2. 生成时间戳
    timestamp = time.Now().UTC().Format("2006-01-02T15:04:05-07:00")

    // 3. 构造待签名字符串
    stringToSign := fmt.Sprintf("%s\n%s\n%s\n%s", method, uri, timestamp, bodyHash)

    // 4. 计算HMAC-SHA256签名（十六进制）
    mac := hmac.New(sha256.New, []byte(secretKey))
    mac.Write([]byte(stringToSign))
    signature = hex.EncodeToString(mac.Sum(nil))

    return timestamp, signature, nil
}

// 使用示例
func main() {
    method := "POST"
    uri := "/ram/user/create"
    body := map[string]interface{}{
        "name":      "test-user",
        "password":  "YOUR_SECURE_PASSWORD",
        "authroity": "member", "phone": "13800000000",
    }
    secretKey := "YOUR_SECRET_KEY"

    timestamp, signature, err := CalculateSignature(method, uri, body, secretKey)
    if err != nil {
        panic(err)
    }

    fmt.Printf("Timestamp: %s\n", timestamp)
    fmt.Printf("Signature: %s\n", signature)
}
```

***

### Java

```java
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.nio.charset.StandardCharsets;
import java.security.MessageDigest;
import java.time.ZonedDateTime;
import java.time.format.DateTimeFormatter;

public class SignatureCalculator {

    /**
     * 计算API请求签名
     * @param method HTTP方法
     * @param uri 请求URI
     * @param body 请求体JSON字符串
     * @param secretKey SecretKey
     * @return [timestamp, signature]
     */
    public static String[] calculateSignature(String method, String uri,
                                              String body, String secretKey) throws Exception {
        // 1. 计算Body哈希（十六进制）
        MessageDigest digest = MessageDigest.getInstance("SHA-256");
        byte[] bodyBytes = (body != null ? body : "").getBytes(StandardCharsets.UTF_8);
        byte[] hashBytes = digest.digest(bodyBytes);
        String bodyHash = bytesToHex(hashBytes);

        // 2. 生成时间戳
        String timestamp = ZonedDateTime.now()
            .format(DateTimeFormatter.ofPattern("yyyy-MM-dd'T'HH:mm:ssXXX"));

        // 3. 构造待签名字符串
        String stringToSign = String.format("%s\n%s\n%s\n%s",
            method, uri, timestamp, bodyHash);

        // 4. 计算HMAC-SHA256签名（十六进制）
        Mac mac = Mac.getInstance("HmacSHA256");
        SecretKeySpec secretKeySpec = new SecretKeySpec(
            secretKey.getBytes(StandardCharsets.UTF_8), "HmacSHA256");
        mac.init(secretKeySpec);
        byte[] signatureBytes = mac.doFinal(stringToSign.getBytes(StandardCharsets.UTF_8));
        String signature = bytesToHex(signatureBytes);

        return new String[]{timestamp, signature};
    }

    /**
     * 字节数组转十六进制字符串
     */
    private static String bytesToHex(byte[] bytes) {
        StringBuilder result = new StringBuilder();
        for (byte b : bytes) {
            result.append(String.format("%02x", b));
        }
        return result.toString();
    }

    // 使用示例
    public static void main(String[] args) throws Exception {
        String method = "POST";
        String uri = "/ram/user/create";
        String body = "{\"name\":\"test-user\",\"password\":\"YOUR_SECURE_PASSWORD\",\"authroity\":\"member\",\"phone\":\"13800000000\"}";
        String secretKey = "YOUR_SECRET_KEY";

        String[] result = calculateSignature(method, uri, body, secretKey);

        System.out.println("Timestamp: " + result[0]);
        System.out.println("Signature: " + result[1]);
    }
}
```

***

### PHP

```php
<?php

/**
 * 计算API请求签名
 * @param string $method HTTP方法
 * @param string $uri 请求URI
 * @param array|string $body 请求体
 * @param string $secretKey SecretKey
 * @return array [timestamp, signature]
 */
function calculateSignature($method, $uri, $body, $secretKey) {
    // 1. 计算Body哈希（十六进制）
    if (is_array($body)) {
        $bodyStr = json_encode($body, JSON_UNESCAPED_SLASHES | JSON_UNESCAPED_UNICODE);
    } else {
        $bodyStr = $body ?: '';
    }
    $bodyHash = hash('sha256', $bodyStr);

    // 2. 生成时间戳
    $timestamp = gmdate('Y-m-d\TH:i:sP');

    // 3. 构造待签名字符串
    $stringToSign = "$method\n$uri\n$timestamp\n$bodyHash";

    // 4. 计算HMAC-SHA256签名（十六进制）
    $signature = hash_hmac('sha256', $stringToSign, $secretKey);

    return [$timestamp, $signature];
}

// 使用示例
$method = 'POST';
$uri = '/ram/user/create';
$body = [
    'name' => 'test-user',
    'password' => 'YOUR_SECURE_PASSWORD',
    'authroity' => 'member',
    'phone' => '13800000000'
];
$secretKey = 'YOUR_SECRET_KEY';

list($timestamp, $signature) = calculateSignature($method, $uri, $body, $secretKey);

echo "Timestamp: $timestamp\n";
echo "Signature: $signature\n";
?>
```

***

## 完整请求示例

### cURL

```bash
#!/bin/bash

## 配置
ACCESS_KEY_ID="YOUR_ACCESS_KEY_ID"
SECRET_KEY="YOUR_SECRET_KEY"
BASE_URL="YOUR_GATEWAY_MANAGEMENT_BASE_URL"
METHOD="POST"
URI="/ram/user/create"
BODY='{"name":"test-user","password":"YOUR_SECURE_PASSWORD","authroity":"member","phone":"13800000000"}'

## 1. 使用实际请求 URL 的 Path（含接入前缀，不含 Query）
REQUEST_URL="${BASE_URL%/}${URI}"
PATH_AFTER_SCHEME="${REQUEST_URL#*://}"
SIGNING_PATH="/${PATH_AFTER_SCHEME#*/}"
SIGNING_PATH="${SIGNING_PATH%%\?*}"
BODY_HASH=$(printf '%s' "$BODY" | openssl dgst -sha256 -hex | awk '{print $2}')

## 2. 生成时间戳
TIMESTAMP=$(date -u +"%Y-%m-%dT%H:%M:%S+00:00")

## 3. 构造待签名字符串
STRING_TO_SIGN=$(printf '%s\n%s\n%s\n%s' "$METHOD" "$SIGNING_PATH" "$TIMESTAMP" "$BODY_HASH")

## 4. 计算签名
SIGNATURE=$(printf '%s' "$STRING_TO_SIGN" | openssl dgst -sha256 -hmac "$SECRET_KEY" -hex | awk '{print $2}')

## 5. 发送请求
curl -X POST "$REQUEST_URL" \
  -H "Content-Type: application/json" \
  -H "X-Access-Key-Id: ${ACCESS_KEY_ID}" \
  -H "X-Timestamp: ${TIMESTAMP}" \
  -H "X-Signature: ${SIGNATURE}" \
  --data-binary "$BODY"
```

***

### Postman Pre-request Script

在 Postman 中，将下方代码放入 Pre-request Script，使用无请求体或 Body → raw → JSON。脚本解析变量后仅签 Path，查询参数照常发送；表单和文件上传不适用。

```javascript
// 配置
const accessKeyId = pm.environment.get("ACCESS_KEY_ID");
const secretKey = pm.environment.get("SECRET_KEY");

// 获取请求信息
const method = pm.request.method;
// 只签请求路径，查询参数不参与签名。
const resolvedUrl = pm.variables.replaceIn(pm.request.url.toString());
const uri = resolvedUrl.replace(/^https?:\/\/[^/]+/, '').split(/[?#]/)[0] || '/';
const body = pm.request.body?.raw ? pm.variables.replaceIn(pm.request.body.raw) : '';
if (pm.request.body?.mode === 'raw') pm.request.body.update({ mode: 'raw', raw: body });

// 1. 计算Body哈希
const bodyHash = CryptoJS.SHA256(body).toString(CryptoJS.enc.Hex);

// 2. 生成时间戳
const timestamp = new Date().toISOString().replace(/\.\d{3}Z$/, '+00:00');

// 3. 构造待签名字符串
const stringToSign = `${method}\n${uri}\n${timestamp}\n${bodyHash}`;

// 4. 计算签名
const signature = CryptoJS.HmacSHA256(stringToSign, secretKey).toString(CryptoJS.enc.Hex);

// 5. 设置请求头
pm.request.headers.upsert({
    key: 'X-Access-Key-Id',
    value: accessKeyId
});
pm.request.headers.upsert({
    key: 'X-Timestamp',
    value: timestamp
});
pm.request.headers.upsert({
    key: 'X-Signature',
    value: signature
});

console.log('Timestamp:', timestamp);
console.log('Signature:', signature);
```

**环境变量设置**：

* `ACCESS_KEY_ID`: 你的AccessKey ID
* `SECRET_KEY`: 你的SecretKey

***

## 签名验证调试

如果遇到签名验证失败，可以按以下步骤排查：

### 1. 检查时间戳

* 确保服务器时间准确（使用NTP同步）
* 时间戳格式必须符合RFC3339标准
* 请求时间戳允许过去不超过 5 分钟，未来不超过 1 分钟

```python
## 正确的时间戳格式
"2024-01-01T12:00:00+00:00"  # ✅ 正确
"2024-01-01T12:00:00Z"        # ✅ 正确（UTC）
"2024-01-01 12:00:00"         # ❌ 错误（格式不对）
```

### 2. 检查Body哈希

* 确保签名与发送使用相同请求体字节，包括空格、换行和 Unicode 编码
* 空Body应该使用空字符串计算哈希

```python
## 调试Body哈希
import hashlib

body = '{"name":"test-user","password":"YOUR_SECURE_PASSWORD"}'
body_hash = hashlib.sha256(body.encode('utf-8')).hexdigest()
print(f"Body Hash: {body_hash}")
```

### 3. 检查StringToSign

* 确保各部分用`\n`换行符连接
* URI 只使用服务接收到的 URL Path，不包含查询参数；Query 仍随请求发送

```python
## 调试StringToSign
string_to_sign = f"{method}\n{uri}\n{timestamp}\n{body_hash}"
print("StringToSign:")
print(repr(string_to_sign))  # 使用repr可以看到\n字符
```

### 4. 检查签名计算

* 确保SecretKey正确（不包含多余空格）
* 确保使用十六进制编码而不是base64

```python
## 调试签名
import hmac
import hashlib

signature = hmac.new(
    secret_key.encode('utf-8'),
    string_to_sign.encode('utf-8'),
    hashlib.sha256
).hexdigest()  # 注意：使用hexdigest()而不是digest()

print(f"Signature: {signature}")
print(f"Signature Length: {len(signature)}")  # 应该是64个字符
```

***

## 响应与错误处理

管理接口响应采用 `code`、`data`、`msg`。HTTP 状态与 JSON 业务码是两层信息；请同时读取，不要将 HTTP 400、401、403 直接当成业务码。

| 响应字段          | 值                  | 处理方式                         |
| ------------- | ------------------ | ---------------------------- |
| `code`        | `0`                | 操作成功，读取 `data`               |
| `code`        | `20001`            | 未登录，重新获取有效登录态或检查认证凭证         |
| `code`        | `40000` / `50000`  | 部分接口使用的业务错误码，结合 `msg` 处理具体原因 |
| `data.reason` | `JWT_REQUIRED`     | 使用 JWT 登录态完成该操作              |
| `data.reason` | `STEP_UP_REQUIRED` | 根据响应中的验证范围与方式完成验证后重试         |

签名失败时检查 AccessKey 状态、时间戳、请求 Path 及请求体字节；权限失败时检查账号身份与项目授权。AI 模型接口使用各自协议的错误响应。

***

## 安全建议

1. **保护SecretKey**：
   * 不要在代码中硬编码SecretKey
   * 使用环境变量或密钥管理服务
   * 不要将SecretKey提交到版本控制系统
2. **定期轮换**：
   * 建议每3-6个月轮换一次SecretKey
   * 发现泄露立即禁用并创建新的
3. **最小权限原则**：
   * 为不同用途创建不同的AccessKey
   * 不再使用的AccessKey及时禁用
4. **HTTPS传输**：
   * 生产环境必须使用HTTPS
   * 避免在不安全的网络环境下传输
5. **日志脱敏**：
   * 日志中不要记录完整的SecretKey
   * 签名可以记录用于调试

