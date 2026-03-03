# DEV_DOCUMENT.md 超详细模板

将此模板中所有 {占位符} 替换为实际内容后生成文档。

---

````markdown
# 📚 {项目名称} 开发文档

> **文档版本**：v1.0 | **最后更新**：{日期} | **当前进度**：节点 {X}/{总N}

---

## 一、项目概览

| 字段 | 内容 |
|------|------|
| 项目名称 | {项目名称} |
| 项目描述 | {一句话描述} |
| 开发类型 | {前端/后端/全栈} |
| 开始时间 | {时间} |
| 当前状态 | 🟡 开发中 / 🟢 已完成 |
| 仓库地址 | {GitHub/GitLab URL} |
| 线上地址 | {部署 URL，开发阶段填 N/A} |

---

## 二、系统架构

### 2.1 整体架构图

```
┌──────────────────────────────────────────────┐
│                   用户浏览器                   │
│              React / Vue 前端应用              │
└──────────────────┬───────────────────────────┘
                   │ HTTPS
┌──────────────────▼───────────────────────────┐
│                  反向代理                      │
│              Nginx / Caddy                    │
└──────┬────────────────────┬──────────────────┘
       │                    │
┌──────▼──────┐    ┌────────▼────────┐
│  前端静态    │    │   后端 API 服务  │
│  dist/build │    │  Node/Python    │
└─────────────┘    └────────┬────────┘
                            │
              ┌─────────────┴─────────────┐
              │                           │
      ┌───────▼────────┐      ┌──────────▼────────┐
      │  主数据库       │      │   缓存/队列        │
      │  PostgreSQL    │      │   Redis（如有）    │
      └────────────────┘      └───────────────────┘
```

### 2.2 技术栈全景

| 层级 | 技术 | 版本 | 用途 |
|------|------|------|------|
| 前端框架 | {React/Vue} | {版本} | 用户界面 |
| 前端语言 | TypeScript | {版本} | 类型安全 |
| 前端样式 | Tailwind CSS | {版本} | 样式系统 |
| 前端构建 | Vite | {版本} | 打包工具 |
| 后端语言 | {Node.js/Python} | {版本} | 服务端运行 |
| 后端框架 | {Express/FastAPI} | {版本} | HTTP 服务 |
| ORM | {Prisma/SQLAlchemy} | {版本} | 数据库操作 |
| 数据库 | {PostgreSQL/MySQL} | {版本} | 数据存储 |
| 缓存 | Redis | {版本} | 缓存/会话 |
| 认证 | JWT | — | 用户鉴权 |
| 部署 | Docker + {平台} | — | 容器化部署 |

---

## 三、数据库设计

### 3.1 ER 关系图

```
users ──────────────< orders
  │                     │
  │                  products
  │
  └────────────────< user_roles >──── roles
```

### 3.2 数据表详细设计

#### 表：users（用户表）

| 字段名 | 类型 | 约束 | 默认值 | 说明 |
|--------|------|------|--------|------|
| id | BIGINT | PK, AUTO_INCREMENT | — | 用户 ID |
| username | VARCHAR(50) | NOT NULL, UNIQUE | — | 用户名 |
| email | VARCHAR(255) | NOT NULL, UNIQUE | — | 邮箱 |
| password_hash | VARCHAR(255) | NOT NULL | — | bcrypt 哈希密码 |
| avatar_url | VARCHAR(500) | NULL | NULL | 头像 URL |
| role | ENUM('admin','user') | NOT NULL | 'user' | 用户角色 |
| status | TINYINT | NOT NULL | 1 | 0=禁用 1=正常 |
| last_login_at | TIMESTAMP | NULL | NULL | 最后登录时间 |
| created_at | TIMESTAMP | NOT NULL | CURRENT | 创建时间 |
| updated_at | TIMESTAMP | NOT NULL | CURRENT | 更新时间 |

**索引：**
- `idx_email` ON (email) — 登录查询
- `idx_username` ON (username) — 用户名查询
- `idx_status` ON (status) — 状态筛选

#### 表：{业务表名}（根据项目实际增补）

{同上格式，列出所有表}

### 3.3 数据库初始化 SQL

```sql
-- 创建数据库
CREATE DATABASE {db_name} CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- users 表
CREATE TABLE users (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  username VARCHAR(50) NOT NULL UNIQUE,
  email VARCHAR(255) NOT NULL UNIQUE,
  password_hash VARCHAR(255) NOT NULL,
  role ENUM('admin', 'user') NOT NULL DEFAULT 'user',
  status TINYINT NOT NULL DEFAULT 1,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  INDEX idx_email (email),
  INDEX idx_status (status)
);

-- {其他表，以此类推}
```

---

## 四、核心业务流程

### 4.1 用户注册流程

```
用户填写表单
    │
    ▼
前端校验（格式/长度）
    │ 失败 → 显示错误提示
    ▼ 通过
POST /auth/register
    │
    ▼
后端校验
├── 用户名/邮箱是否已存在？ → 是 → 返回 409 错误
└── 格式是否合法？ → 否 → 返回 400 错误
    │ 通过
    ▼
bcrypt 加密密码
    │
    ▼
写入 users 表
    │
    ▼
生成 JWT Token
    │
    ▼
返回 { token, user } → 前端存储 Token → 跳转主页
```

### 4.2 {其他核心流程}

{以同样格式描述其他重要流程}

---

## 五、API 时序图

### 5.1 用户登录时序

```
浏览器        前端应用       API服务器      数据库
  │               │               │            │
  │──点击登录────▶│               │            │
  │               │──POST /login─▶│            │
  │               │               │──查询用户──▶│
  │               │               │◀──返回数据──│
  │               │               │──校验密码   │
  │               │               │──生成Token  │
  │               │◀──200+Token───│            │
  │               │──存储Token    │            │
  │◀──跳转主页────│               │            │
```

---

## 六、环境配置

### 6.1 环境要求

| 软件 | 最低版本 | 推荐版本 | 说明 |
|------|----------|----------|------|
| Node.js | 18.0.0 | 20.x LTS | 后端运行环境 |
| npm | 9.0.0 | 10.x | 包管理器 |
| PostgreSQL | 14.0 | 16.x | 主数据库 |
| Redis | 6.0 | 7.x | 缓存（如有） |
| Docker | 24.0 | 最新 | 容器化（可选） |

### 6.2 环境变量完整说明

```bash
# ================================
# 服务配置
# ================================
PORT=3000                          # 服务监听端口（必填）
NODE_ENV=development               # 环境：development / production（必填）

# ================================
# 数据库配置
# ================================
DATABASE_URL="postgresql://用户名:密码@localhost:5432/数据库名"
# 格式：postgresql://USER:PASSWORD@HOST:PORT/DBNAME
# 示例：postgresql://admin:123456@localhost:5432/myapp
DB_POOL_MIN=2                      # 连接池最小连接数
DB_POOL_MAX=10                     # 连接池最大连接数

# ================================
# JWT 认证配置
# ================================
JWT_SECRET=your-super-secret-key   # JWT 签名密钥（必须是随机长字符串！）
JWT_EXPIRES_IN=7d                  # Token 有效期（7d = 7天）
# 生成随机密钥命令：node -e "console.log(require('crypto').randomBytes(64).toString('hex'))"

# ================================
# Redis 配置（如有）
# ================================
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=                    # 无密码则留空

# ================================
# 文件上传配置（如有）
# ================================
UPLOAD_DIR=./uploads               # 本地上传目录
MAX_FILE_SIZE=10                   # 最大文件大小（MB）
ALLOWED_FILE_TYPES=jpg,png,gif,pdf # 允许的文件类型

# ================================
# 邮件配置（如有）
# ================================
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your@email.com
SMTP_PASS=your-app-password
MAIL_FROM=noreply@yourapp.com

# ================================
# 第三方服务（按需填写）
# ================================
OPENAI_API_KEY=sk-...              # OpenAI API Key
WECHAT_APP_ID=                     # 微信 AppID
WECHAT_APP_SECRET=                 # 微信 AppSecret
ALIPAY_APP_ID=                     # 支付宝 AppID
```

### 6.3 本地开发启动步骤

```bash
# 1. 克隆项目
git clone {仓库地址}
cd {项目名}

# 2. 安装后端依赖
cd backend
npm install

# 3. 配置环境变量
cp .env.example .env
# 用编辑器打开 .env 文件，填写上面的配置

# 4. 初始化数据库
npm run db:migrate    # 执行数据库迁移
npm run db:seed       # （可选）插入测试数据

# 5. 启动后端
npm run dev           # 启动在 http://localhost:3000

# 6. 安装前端依赖（新开一个终端）
cd ../frontend
npm install

# 7. 启动前端
npm run dev           # 启动在 http://localhost:5173

# 8. 验证成功标志
# 前端：浏览器打开 http://localhost:5173，看到页面
# 后端：http://localhost:3000/health 返回 {"status":"ok"}
```

---

## 七、第三方服务配置

### 7.1 {服务名称}（如有）

**申请地址**：{URL}  
**需要的信息**：{AppID, AppSecret 等}  
**配置步骤**：
1. {步骤1}
2. {步骤2}
3. 将 AppID 填入 .env 的 `XXX_APP_ID` 字段

---

## 八、错误码对照表

| 错误码 | HTTP 状态 | 含义 | 前端处理建议 |
|--------|----------|------|-------------|
| 200 | 200 | 成功 | 正常处理数据 |
| 400 | 400 | 请求参数错误 | 显示具体错误字段提示 |
| 401 | 401 | 未登录/Token 失效 | 跳转登录页，清除本地 Token |
| 403 | 403 | 无权限 | 显示"无权限"提示 |
| 404 | 404 | 资源不存在 | 显示 404 页面 |
| 409 | 409 | 数据冲突（如用户名已存在） | 显示冲突字段提示 |
| 422 | 422 | 数据验证失败 | 逐字段显示错误 |
| 429 | 429 | 请求频率超限 | 显示"请求太频繁，请稍后再试" |
| 500 | 500 | 服务器内部错误 | 显示通用错误提示，上报错误日志 |

---

## 九、安全检查清单

```
【认证安全】
✅/⬜ 密码使用 bcrypt 加密（cost ≥ 10）
✅/⬜ JWT 密钥足够随机（≥ 64 字节）
✅/⬜ Token 有过期时间
✅/⬜ 刷新 Token 机制（如有）

【输入安全】
✅/⬜ 所有用户输入有服务端校验
✅/⬜ SQL 使用参数化查询（无拼接 SQL）
✅/⬜ XSS 防护（富文本需 DOMPurify）
✅/⬜ 文件上传限制类型和大小

【API 安全】
✅/⬜ 敏感接口有权限校验
✅/⬜ 接口有频率限制（rate limiting）
✅/⬜ 不在响应中返回密码字段
✅/⬜ CORS 配置正确

【数据安全】
✅/⬜ 敏感数据不写入日志
✅/⬜ 生产环境关闭调试模式
✅/⬜ 环境变量不提交 Git
```

---

## 十、模块开发规划

### 【第一阶段：基础架构】
- [ ] 节点 1：{节点名}
- [ ] 节点 2：{节点名}
- [ ] 节点 3：{节点名}

### 【第二阶段：核心功能】
- [ ] 节点 4：{节点名}
- [ ] 节点 5：{节点名}

### 【第三阶段：业务模块】
- [ ] 节点 6：{节点名}
...

---

## 十一、开发节点记录

（每个节点完成后追加）

### 节点 1：{节点名}

| 字段 | 内容 |
|------|------|
| 完成时间 | {时间} |
| 负责 Agent | {后端/前端/全体} |
| Git 提交 | [{模块}] {描述} |
| 提交哈希 | {hash} |
| 测试状态 | ✅ 全部通过 |

**完成内容：**
- {具体内容}

**交付文件：**
```
{文件路径列表}
```

**测试报告：**
- ✅ 语法检查 — 通过
- ✅ {测试项} — 通过（{N} 个用例）

**遇到的问题：**
- {问题描述 / 无}

**解决方案：**
- {方案 / 无}

**MCP 调用记录：**
- GitHub MCP: commit {hash}
- {其他 MCP 调用}

---

## 十二、Git 提交历史

| 时间 | 提交信息 | 节点 | 负责 | 测试 |
|------|----------|------|------|------|
| {时间} | {提交信息} | 节点 1 | 后端 Agent | ✅ |

---

## 十三、问题与解决方案汇总

### 已解决

| # | 问题描述 | 解决方案 | 影响节点 |
|---|----------|----------|----------|
| 1 | {问题} | {方案} | 节点 X |

### 待解决

| # | 问题描述 | 临时方案 | 计划解决 |
|---|----------|----------|----------|
| 1 | {问题} | {方案} | 节点 X |
````
