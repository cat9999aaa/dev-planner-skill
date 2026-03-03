# {项目名}_API.md 接口文档模板

---

````markdown
# 🔌 {项目名称} 接口文档

> **版本**：v1.0 | **基础路径**：`/api/v1` | **更新时间**：{日期}

---

## 一、接口规范

### 基础信息

| 字段 | 内容 |
|------|------|
| 基础 URL | `http://localhost:{PORT}/api/v1` |
| 生产 URL | `https://{域名}/api/v1` |
| 认证方式 | Bearer Token（JWT）|
| 请求格式 | `application/json` |
| 响应格式 | `application/json; charset=utf-8` |
| 时间格式 | ISO 8601（`2024-01-15T10:30:00Z`）|

### 认证方式

需要登录的接口，请求头中携带：
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

Token 获取方式：调用 `POST /auth/login` 接口。

### 统一响应格式

```json
{
  "code": 200,
  "message": "success",
  "data": {},
  "timestamp": "2024-01-15T10:30:00Z"
}
```

**分页响应格式：**
```json
{
  "code": 200,
  "message": "success",
  "data": {
    "list": [],
    "total": 100,
    "page": 1,
    "pageSize": 20,
    "totalPages": 5
  }
}
```

### 通用错误码

| code | HTTP | 含义 | 常见原因 |
|------|------|------|----------|
| 200 | 200 | 成功 | — |
| 400 | 400 | 参数错误 | 缺少必填字段/格式错误 |
| 401 | 401 | 未授权 | Token 缺失/过期/无效 |
| 403 | 403 | 无权限 | 用户角色不够 |
| 404 | 404 | 不存在 | 资源 ID 不存在 |
| 409 | 409 | 冲突 | 用户名/邮箱已存在 |
| 422 | 422 | 验证失败 | 数据格式/业务规则不符 |
| 429 | 429 | 频率限制 | 请求太频繁 |
| 500 | 500 | 服务器错误 | 内部异常 |

---

## 二、接口清单

### 📋 接口目录

| 模块 | 方法 | 路径 | 说明 | 认证 |
|------|------|------|------|------|
| 认证 | POST | /auth/register | 用户注册 | ❌ |
| 认证 | POST | /auth/login | 用户登录 | ❌ |
| 认证 | GET  | /auth/me | 获取当前用户 | ✅ |
| 认证 | POST | /auth/logout | 退出登录 | ✅ |
| 认证 | POST | /auth/refresh | 刷新 Token | ✅ |
| {模块} | GET | /{resource} | 获取列表 | ✅ |
| {模块} | POST | /{resource} | 创建 | ✅ |
| {模块} | GET  | /{resource}/:id | 获取单条 | ✅ |
| {模块} | PUT  | /{resource}/:id | 更新 | ✅ |
| {模块} | DELETE | /{resource}/:id | 删除 | ✅ |

---

### 🔐 认证模块

---

#### POST /auth/register — 用户注册

**描述**：创建新用户账号，成功后返回 Token。

**请求参数：**
```json
{
  "username": "johndoe",       // string, 必填, 3-50字符, 只允许字母数字下划线
  "email": "john@example.com", // string, 必填, 合法邮箱格式
  "password": "Abc123456",     // string, 必填, 8-50字符, 必须包含大小写字母和数字
  "confirmPassword": "Abc123456" // string, 必填, 必须与 password 一致
}
```

**成功响应（201）：**
```json
{
  "code": 200,
  "message": "注册成功",
  "data": {
    "token": "eyJhbGci...",
    "tokenType": "Bearer",
    "expiresIn": 604800,
    "user": {
      "id": 1,
      "username": "johndoe",
      "email": "john@example.com",
      "role": "user",
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}
```

**错误响应：**
```json
// 409 - 用户名/邮箱已存在
{ "code": 409, "message": "该邮箱已被注册" }

// 400 - 参数格式错误
{
  "code": 400,
  "message": "参数验证失败",
  "errors": [
    { "field": "password", "message": "密码必须包含大写字母" }
  ]
}
```

---

#### POST /auth/login — 用户登录

**请求参数：**
```json
{
  "email": "john@example.com",   // string, 必填
  "password": "Abc123456",       // string, 必填
  "rememberMe": false            // boolean, 可选, true=30天有效期
}
```

**成功响应（200）：**
```json
{
  "code": 200,
  "message": "登录成功",
  "data": {
    "token": "eyJhbGci...",
    "expiresIn": 604800,
    "user": { "id": 1, "username": "johndoe", "email": "...", "role": "user" }
  }
}
```

---

#### GET /auth/me — 获取当前用户信息

**认证**：需要 Bearer Token

**成功响应（200）：**
```json
{
  "code": 200,
  "data": {
    "id": 1,
    "username": "johndoe",
    "email": "john@example.com",
    "role": "user",
    "avatarUrl": null,
    "lastLoginAt": "2024-01-15T10:30:00Z",
    "createdAt": "2024-01-01T00:00:00Z"
  }
}
```

---

### 📦 {业务模块名称}

---

#### GET /{resource} — 获取列表

**认证**：需要 Bearer Token

**Query 参数：**
```
page        integer  可选  页码，默认 1
pageSize    integer  可选  每页数量，默认 20，最大 100
keyword     string   可选  搜索关键词，模糊匹配 name/title 字段
status      integer  可选  状态筛选，0=禁用 1=启用
sortBy      string   可选  排序字段，默认 createdAt
sortOrder   string   可选  排序方向，asc/desc，默认 desc
```

**成功响应（200）：**
```json
{
  "code": 200,
  "data": {
    "list": [
      { "id": 1, "name": "示例", "status": 1, "createdAt": "..." }
    ],
    "total": 50,
    "page": 1,
    "pageSize": 20,
    "totalPages": 3
  }
}
```

---

#### POST /{resource} — 创建

**认证**：需要 Bearer Token（需要 admin 角色）

**请求参数：**
```json
{
  "name": "名称",          // string, 必填, 1-100字符
  "description": "描述",   // string, 可选
  "status": 1             // integer, 可选, 0=禁用 1=启用，默认 1
}
```

**成功响应（201）：**
```json
{
  "code": 200,
  "message": "创建成功",
  "data": { "id": 1, "name": "名称", "status": 1, "createdAt": "..." }
}
```

---

## 三、数据字典

### 用户角色（role）
| 值 | 说明 | 可访问资源 |
|----|------|-----------|
| admin | 管理员 | 全部接口 |
| user | 普通用户 | 自己的数据 |

### 状态（status）
| 值 | 说明 |
|----|------|
| 0 | 禁用/不可用 |
| 1 | 正常/启用 |
| 2 | 待审核 |

---

## 四、接口调用示例（cURL）

```bash
# 注册
curl -X POST http://localhost:3000/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{"username":"test","email":"test@example.com","password":"Abc123456","confirmPassword":"Abc123456"}'

# 登录
curl -X POST http://localhost:3000/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"Abc123456"}'

# 获取用户信息（替换 TOKEN 为实际 Token）
curl http://localhost:3000/api/v1/auth/me \
  -H "Authorization: Bearer TOKEN"
```
````
