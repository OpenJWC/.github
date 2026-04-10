# OpenJWC API文档

本文档详细描述OpenJWC系统的所有API接口，包括请求格式、响应格式、参数说明等信息。

## 目录

- [API概述](#api概述)
- [认证机制](#认证机制)
- [通用规范](#通用规范)
- [客户端API](#客户端api)
  - [通知相关](#通知相关)
  - [AI对话](#ai对话)
  - [设备管理](#设备管理)
  - [每日一言](#每日一言)
  - [资讯投稿](#资讯投稿)
  - [语义搜索](#语义搜索)
  - [设备注册](#设备注册)
- [管理员API](#管理员api)
  - [管理员认证](#管理员认证)
  - [API密钥管理](#api密钥管理)
  - [系统设置](#系统设置)
  - [系统监控](#系统监控)
  - [日志管理](#日志管理)
  - [通知管理](#通知管理)
  - [投稿管理](#投稿管理)

## API概述

### 基础信息

- **Base URL**: `http://your-server-ip:8000`
- **API版本**: `v1`
- **数据格式**: `JSON`
- **字符编码**: `UTF-8`

### API端点结构

```
http://your-server-ip:8000/api/v1/{module}/{resource}
```

**模块说明：**

- `client`: 客户端相关API
- `admin`: 管理员相关API

## 认证机制

### API密钥认证

客户端使用API密钥进行认证。

**请求头：**

```http
X-API-Key: your-api-key-here
X-Device-ID: your-device-id
```

### JWT认证

管理员使用JWT进行身份认证。

**请求头：**

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

## 通用规范

### 响应格式

所有接口统一返回以下格式：

```json
{
  "msg": "操作结果描述",
  "data": {
    // 响应数据
  }
}
```

### 分页参数

大多数列表接口支持分页：

| 参数 | 类型 | 说明 |
|------|------|------|
| page | int | 页码，从1开始 |
| size | int | 每页数量，最大不超过50条 |

## 客户端API

### 通知相关

#### 获取通知列表

**接口地址：** `GET /api/v1/client/notices`

**查询参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| label | string | 否 | 标签筛选 |
| page | int | 否 | 页码，默认1 |
| size | int | 否 | 每页数量，最大50，默认20 |

**请求示例：**

```http
GET /api/v1/client/notices?label=exam&page=1&size=20
```

**响应示例：**

```json
{
  "msg": "获取成功",
  "data": {
    "total_returned": 100,
    "total_label": ["exam", "notice", "info"],
    "notices": [
      {
        "id": "1",
        "label": "exam",
        "title": "关于期末考试安排的通知",
        "date": "2024-01-15",
        "detail_url": "https://jwc.example.com/notice/123",
        "is_page": false
      }
    ]
  }
}
```

#### 获取标签列表

**接口地址：** `GET /api/v1/client/notices/labels`

**响应示例：**

```json
{
  "msg": "获取成功",
  "data": {
    "labels": ["exam", "notice", "info"]
  }
}
```

### AI对话

#### AI对话

**接口地址：** `POST /api/v1/client/chat`

**请求参数：**

```json
{
  "notice_ids": ["1", "2"],
  "user_query": "下周一有哪些考试？",
  "stream": false,
  "history": [
    {
      "role": "user",
      "content": "之前的对话内容"
    },
    {
      "role": "assistant",
      "content": "之前的回复内容"
    }
  ]
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| notice_ids | List[string] | 否 | 关联的通知ID列表 |
| user_query | string | 是 | 用户查询内容 |
| stream | bool | 否 | 是否使用流式输出，默认false |
| history | List[Message] | 否 | 对话历史 |

**非流式响应示例：**

```json
{
  "status": "success",
  "reply": "根据教务信息，下周一（1月22日）有以下考试安排：..."
}
```

**流式响应：**

- 返回类型：`text/event-stream`
- 内容为流式文本输出

### 设备管理

#### 获取设备信息

**接口地址：** `GET /api/v1/client/device`

**响应示例：**

```json
{
  "msg": "获取成功",
  "data": {
    "max_devices": 3,
    "bound_devices": 2,
    "device_list": [
      {
        "device_id": "device_123",
        "device_name": "My Phone",
        "bind_time": "2024-01-15T10:30:00Z"
      }
    ]
  }
}
```

#### 解绑设备

**接口地址：** `POST /api/v1/client/device/unbind`

**响应示例：**

```json
{
  "detail": "解绑成功，名额已释放。"
}
```

### 每日一言

#### 获取每日一言

**接口地址：** `GET /api/v1/client/motto`

**响应示例：**

```json
{
  "msg": "每日一言获取成功",
  "data": {
    "text": "今日格言内容",
    "author": "作者姓名"
  }
}
```

### 资讯投稿

#### 提交流程

**接口地址：** `POST /api/v1/client/submissions`

**请求参数：**

```json
{
  "content": {
    "attachment_urls": ["https://example.com/file.pdf"],
    "text": "投稿内容正文"
  },
  "date": "2024-01-15",
  "detail_url": "https://example.com/detail",
  "is_page": false,
  "label": "notice",
  "title": "投稿标题"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| content | SubmissionContent | 是 | 投稿内容 |
| date | string | 是 | 发布日期 |
| detail_url | string | 否 | 详情URL |
| is_page | bool | 是 | 是否为页面 |
| label | string | 是 | 标签 |
| title | string | 是 | 标题 |

**响应示例：**

```json
{
  "msg": "提交成功",
  "data": {}
}
```

#### 获取我的投稿

**接口地址：** `GET /api/v1/client/submissions/my`

**响应示例：**

```json
{
  "msg": "提交成功",
  "data": {
    "total": 5,
    "notices": [
      {
        "id": "1",
        "title": "投稿标题",
        "status": "pending",
        "submit_time": "2024-01-15T10:30:00Z"
      }
    ]
  }
}
```

### 语义搜索

#### 语义搜索

**接口地址：** `POST /api/v1/client/notices/search`

**请求参数：**

```json
{
  "query": "考试时间",
  "top_k": 5,
  "min_similarity": 0.7
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| query | string | 是 | 搜索查询文本 |
| top_k | int | 否 | 返回结果数量上限，默认5 |
| min_similarity | float | 否 | 最低相似度阈值（0-1） |

**响应示例：**

```json
{
  "msg": "搜索成功",
  "data": {
    "total_found": 3,
    "results": [
      {
        "id": "1",
        "label": "exam",
        "title": "关于期末考试安排的通知",
        "date": "2024-01-15",
        "detail_url": "https://jwc.example.com/notice/123",
        "is_page": false,
        "similarity_score": 0.92,
        "distance": 0.08
      }
    ]
  }
}
```

### 设备注册

#### 注册设备

**接口地址：** `POST /api/v1/client/register`

**响应示例：**

```json
{
  "msg": "设备注册成功",
  "data": {}
}
```

## 管理员API

### 管理员认证

#### 管理员登录

**接口地址：** `POST /api/v1/admin/auth/login`

**请求格式：** `application/x-www-form-urlencoded`

**请求参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| username | string | 是 | 管理员用户名 |
| password | string | 是 | 管理员密码 |

**请求示例：**

```http
POST /api/v1/admin/auth/login
Content-Type: application/x-www-form-urlencoded

username=admin&password=admin123
```

**响应示例：**

```json
{
  "msg": "登录成功",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expires_in": 300
  }
}
```

### API密钥管理

#### 创建API密钥

**接口地址：** `POST /api/v1/admin/apikeys`

**请求参数：**

```json
{
  "owner_name": "张三",
  "max_devices": 3
}
```

**响应示例：**

```json
{
  "msg": "Apikey创建成功。",
  "data": {
    "new_key": "sk-xxxxxxxxxxxxxxxx"
  }
}
```

#### 获取API密钥列表

**接口地址：** `GET /api/v1/admin/apikeys`

**查询参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| page | int | 否 | 页码，默认1 |
| size | int | 否 | 每页数量，最大50，默认20 |
| keyword | string | 否 | 按owner_name搜索 |

**响应示例：**

```json
{
  "msg": "请求成功",
  "data": [
    {
      "id": 1,
      "key_string": "sk-xxxxxx",
      "owner_name": "张三",
      "is_active": true,
      "max_devices": 3,
      "bound_devices": 2,
      "created_at": "2024-01-15T10:30:00Z"
    }
  ]
}
```

#### 启停API密钥

**接口地址：** `PUT /api/v1/admin/apikeys/{key_id}/status`

**路径参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| key_id | int | 是 | API密钥ID |

**请求参数：**

```json
{
  "is_active": true
}
```

**响应示例：**

```json
{
  "msg": "1的启用状态调整为true",
  "data": {}
}
```

#### 删除API密钥

**接口地址：** `DELETE /api/v1/admin/apikeys/{key_id}`

**响应示例：**

```json
{
  "msg": "APIkey删除成功。",
  "data": {}
}
```

### 系统设置

#### 获取系统设置

**接口地址：** `GET /api/v1/admin/settings`

**响应示例：**

```json
{
  "msg": "请求成功",
  "data": {
    "deepseek_api_key": "sk-xxxxxx",
    "zhipu_api_key": "xxxxxx",
    "crawler_interval_minutes": "480",
    "crawler_days_gap": "200",
    "search_max_day_diff": "60",
    "prompt_debug": "false",
    "notices_auth": "false",
    "submission_max_length": "10000",
    "system_prompt": "你的系统提示词"
  }
}
```

#### 修改系统设置

**接口地址：** `PUT /api/v1/admin/settings`

**请求参数：**

```json
{
  "settings": [
    {
      "key": "deepseek_api_key",
      "value": "new-api-key"
    }
  ]
}
```

**响应示例：**

```json
{
  "msg": "修改成功，deepseek_api_key被修改为new-api-key",
  "data": {}
}
```

#### 重置系统设置

**接口地址：** `PUT /api/v1/admin/settings/reset`

**请求参数：**

```json
["deepseek_api_key", "crawler_interval_minutes"]
```

**响应示例：**

```json
{
  "msg": "修改成功",
  "data": {}
}
```

#### 手动刷新每日一言

**接口地址：** `PUT /api/v1/admin/settings/motto`

**响应示例：**

```json
{
  "msg": "每日一言手动刷新成功",
  "data": {}
}
```

#### 手动执行爬虫

**接口地址：** `PUT /api/v1/admin/settings/crawler`

**响应示例：**

```json
{
  "msg": "手动爬虫成功",
  "data": {}
}
```

#### 修改管理员密码

**接口地址：** `PUT /api/v1/admin/settings/password`

**请求参数：**

```json
{
  "old_password": "oldpassword",
  "new_password": "newpassword"
}
```

**响应示例：**

```json
{
  "msg": "修改成功",
  "data": {}
}
```

### 系统监控

#### 获取系统统计

**接口地址：** `GET /api/v1/admin/monitor/stats`

**响应示例：**

```json
{
  "msg": "请求成功",
  "data": {
    "total_api_calls": 125680,
    "active_keys_count": 15,
    "total_notices": 3520
  }
}
```

#### 获取系统信息

**接口地址：** `GET /api/v1/admin/monitor/sysinfo`

**响应示例：**

```json
{
  "msg": "success",
  "data": {
    "cpu_percent": "45.2%",
    "ram_total_mb": "16384.00 MB",
    "ram_used_mb": "1024.00 MB",
    "uptime_seconds": "86400s"
  }
}
```

### 日志管理

#### 获取日志列表

**接口地址：** `GET /api/v1/admin/logs`

**查询参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| level | string | 否 | 日志级别 |
| size | int | 否 | 返回条目数，默认20 |
| page | int | 否 | 页码，默认1 |
| module | string | 否 | 来源模块 |
| keyword | string | 否 | 模糊搜索 |

**响应示例：**

```json
{
  "msg": "获取日志成功",
  "data": {
    "total": 100,
    "logs": [
      {
        "timestamp": "2024-01-15T10:30:00Z",
        "level": "INFO",
        "message": "User login successful",
        "module": "auth",
        "request_id": "req_123"
      }
    ]
  }
}
```

#### 获取日志模块

**接口地址：** `GET /api/v1/admin/logs/modules`

**响应示例：**

```json
{
  "msg": "获取日志来源模块成功",
  "data": {
    "modules": ["auth", "chat", "admin", "monitor"]
  }
}
```

### 通知管理

#### 获取待审核资讯

**接口地址：** `GET /api/v1/admin/submissions`

**查询参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| status | string | 否 | 指定状态 |
| page | int | 否 | 页码，默认1 |
| size | int | 否 | 每页数量，默认20 |

**响应示例：**

```json
{
  "msg": "获取成功",
  "data": {
    "total": 10,
    "notices": [
      {
        "id": "1",
        "title": "投稿标题",
        "status": "pending",
        "submit_time": "2024-01-15T10:30:00Z"
      }
    ]
  }
}
```

#### 获取投稿详情

**接口地址：** `GET /api/v1/admin/submissions/{id}`

**路径参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | string | 是 | 投稿ID |

**响应示例：**

```json
{
  "msg": "获取成功",
  "data": {
    "id": "1",
    "title": "投稿标题",
    "content": "投稿内容",
    "status": "pending",
    "submitter": "张三",
    "submit_time": "2024-01-15T10:30:00Z"
  }
}
```

#### 审核投稿

**接口地址：** `POST /api/v1/admin/submissions/{id}/review`

**路径参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | string | 是 | 投稿ID |

**请求参数：**

```json
{
  "action": "approve",
  "review": "审核通过"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| action | string | 是 | 操作类型 |
| review | string | 是 | 审核意见 |

**响应示例：**

```json
{
  "msg": "修改成功",
  "data": {}
}
```

## 错误处理

### HTTP状态码

| 状态码 | 说明 |
|--------|------|
| 200 | 请求成功 |
| 401 | 未授权 |
| 403 | 禁止访问 |
| 404 | 资源不存在 |
| 422 | 请求参数错误 |
| 500 | 服务器内部错误 |

### 错误响应格式

```json
{
  "msg": "错误描述",
  "data": null
}
```

---

**OpenJWC API文档 - 完整的接口定义和使用指南**
