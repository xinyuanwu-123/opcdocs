# 查询任务状态

**GET** `/v1/tasks/generations/{request_id}`

根据请求ID查询视频生成任务的状态和结果。

**任务状态说明：**

* `SUBMITTING`：提交中，任务正在提交到系统
* `PENDING`：待处理，任务已接收但尚未开始处理
* `SUBMITTED`：已提交，任务已成功提交到处理队列
* `QUEUED`：排队中，任务在队列中等待资源分配
* `IN_PROGRESS`：执行中，任务正在处理中
* `COMPLETED`：已完成，任务成功完成并生成结果
* `FAILED`：失败，任务处理失败
* `CANCELLED`：已取消，任务被用户或系统取消
* `TIMEOUT`：超时，任务处理超时
* `UNKNOWN`：未知状态，无法确定任务当前状态

## 请求参数

### Authorization

Bearer Token

在 Header 添加参数 `Authorization`，其值为在 Bearer 之后拼接 Token。

示例：

`Authorization: Bearer ********************`

### Path 参数

| 参数名         | 类型     | 说明   | 必需 | 示例                              |
| ----------- | ------ | ---- | -- | ------------------------------- |
| request\_id | string | 请求ID | 是  | 20250829155118560120000jnBOAS1q |

## 请求示例代码

```bash
curl --location 'https://router.shengsuanyun.com/api/v1/tasks/generations/20250829155118560120000jnBOAS1q' \
--header 'Authorization: Bearer <token>'
```

## 返回响应

### 🟢 200 成功

`application/json`

查询成功

| 字段      | 类型                   | 说明           | 必填 |
| ------- | -------------------- | ------------ | -- |
| code    | string               |              | 否  |
| message | string               |              | 否  |
| data    | object(异步Task响应Data) | 异步Task响应Data | 否  |

#### 异步Task响应Data

| 字段           | 类型                     | 说明                          | 必填 |
| ------------ | ---------------------- | --------------------------- | -- |
| request\_id  | string                 | 请求的唯一标识符                    | 否  |
| task\_id     | string                 | 任务 ID，系统内部生成的任务标识符          | 否  |
| action       | enum                   | 任务类型                        | 否  |
| status       | enum                   | 任务状态，详见任务状态说明               | 否  |
| fail\_reason | string                 | 失败原因，任务成功时为空                | 否  |
| submit\_time | integer                | 任务提交时间戳（Unix 时间戳，秒）         | 否  |
| start\_time  | integer                | 任务开始处理时间戳（Unix 时间戳，秒）       | 否  |
| finish\_time | integer                | 任务完成时间戳（Unix 时间戳，秒），未完成时为 0 | 否  |
| progress     | string                 | 进度百分比                       | 否  |
| data         | object(异步Task响应Result) | 任务结果数据对象                    | 否  |

#### 异步Task响应Result

任务结果数据对象

#### 示例

任务处理中 / 任务成功完成 / 任务失败

```json
{
    "code": "success",
    "message": "",
    "data": {
        "request_id": "20250829155118560120000jnBOAS1q",
        "task_id": "68b15bf74887835d7cf6e20c",
        "action": "VIDEO_GENERATION",
        "status": "IN_PROGRESS",
        "fail_reason": "",
        "submit_time": 1756453878,
        "start_time": 1756453879,
        "finish_time": 0,
        "progress": "45%",
        "data": {}
    }
}
```

### 🟠 400 请求有误

### 🟠 401 未认证

### 🟠 404 未找到

### 🔴 500 服务器内部错误
