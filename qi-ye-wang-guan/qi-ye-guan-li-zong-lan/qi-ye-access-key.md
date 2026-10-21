# 企业access key

> 创建和轮换企业级管理接口凭证，设置有效期并管理启用状态。

## 适用场景与边界

企业 AccessKey 用于企业级管理接口和自动化治理，不用于聊天补全、向量化等项目模型调用。 例如企业想用内部服务批量维护账号、项目或读取统计数据时，使用企业管理接口凭证；只是让应用调用模型，请使用[项目 API Key](../xiang-mu-gong-zuo-qu/xiang-mu-api-key.md)。

## 创建企业 AccessKey

1. 进入企业级「企业 AccessKey」页面，点击「创建 AccessKey」。
2. 填写名称和描述，并设置过期时间；可选择快捷期限、自定义天数或不限制。
3. 确认创建后复制 AccessKey ID 与 SecretKey。
4. 将 SecretKey 保存到安全的密钥管理服务，并记录用途与过期时间。

企业管理凭证与项目模型调用凭证分开维护。

> **影响与边界**：SecretKey 只在创建或轮换结果中展示一次，之后不再回显。

## 用于管理接口签名

请求携带 AccessKey ID、时间戳和签名。公共请求头与签名规则见[API 认证与签名方法](../api-can-kao-zong-lan/api-ren-zheng-yu-qian-ming-ban-fa.md)。企业 AccessKey 与模型调用使用的[项目 API Key](../xiang-mu-gong-zuo-qu/xiang-mu-api-key.md)用途不同。

## 核对有效期与状态

在列表的「更多 → 查看详情」中核对创建者、创建时间、过期时间、最后使用时间和启用状态。业务上线前确认凭证期限覆盖使用计划，并提前安排轮换。

## 轮换与停用

轮换时先让调用方接入新 SecretKey，再撤销旧凭证。停用或删除前确认自动化任务、服务和脚本已完成迁移。
