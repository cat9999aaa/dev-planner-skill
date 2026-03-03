# {项目名}_STYLE.md 风格/规范指南模板

---

````markdown
# 📐 {项目名称} 风格指南

> **版本**：v1.0 | **适用团队**：全体开发 Agent | **更新时间**：{日期}

---

## 一、项目目录结构

```
{项目名}/
├── frontend/                    # 前端项目
│   ├── public/                  # 静态资源（不经打包）
│   ├── src/
│   │   ├── assets/             # 图片、字体等静态资源
│   │   ├── components/         # 公共组件（≥2处使用）
│   │   │   ├── ui/             # 基础 UI 组件（Button, Input等）
│   │   │   └── business/       # 业务组件
│   │   ├── pages/              # 页面组件（路由级别）
│   │   ├── layouts/            # 布局组件
│   │   ├── hooks/              # 自定义 React/Vue Hooks
│   │   ├── stores/             # 状态管理（Zustand/Pinia）
│   │   ├── services/           # API 请求封装
│   │   │   ├── api.ts          # axios 实例配置
│   │   │   └── {module}.ts     # 各模块接口
│   │   ├── utils/              # 工具函数
│   │   ├── types/              # TypeScript 类型定义
│   │   ├── constants/          # 常量
│   │   ├── router/             # 路由配置
│   │   └── styles/             # 全局样式
│   ├── .env.example
│   ├── package.json
│   └── vite.config.ts
│
├── backend/                     # 后端项目
│   ├── src/
│   │   ├── routes/             # 路由注册
│   │   ├── controllers/        # 控制器（处理请求/响应）
│   │   ├── services/           # 业务逻辑层
│   │   ├── models/             # 数据模型/ORM
│   │   ├── middleware/         # 中间件（认证、日志、限流等）
│   │   ├── utils/              # 工具函数
│   │   ├── validators/         # 请求参数校验
│   │   ├── types/              # TypeScript 类型
│   │   └── config/             # 配置文件
│   ├── prisma/                 # Prisma Schema（如使用）
│   │   ├── schema.prisma
│   │   └── migrations/
│   ├── tests/                  # 测试文件
│   │   ├── unit/
│   │   └── integration/
│   ├── .env.example
│   └── package.json
│
├── docs/                        # 文档目录
│   ├── DEV_DOCUMENT.md
│   ├── {项目名}_API.md
│   └── {项目名}_STYLE.md
│
├── docker/                      # Docker 配置
│   ├── Dockerfile.frontend
│   ├── Dockerfile.backend
│   └── docker-compose.yml
│
└── README.md
```

---

## 二、命名规范

### 文件命名

| 文件类型 | 规范 | 示例 |
|----------|------|------|
| React 组件 | PascalCase | `UserCard.tsx` |
| Vue 组件 | PascalCase | `UserCard.vue` |
| 工具函数 | camelCase | `formatDate.ts` |
| 常量文件 | UPPER_SNAKE_CASE | `API_CONSTANTS.ts` |
| 页面组件 | PascalCase + Page | `UserProfilePage.tsx` |
| 样式文件 | 同组件名 | `UserCard.module.css` |
| 测试文件 | 同源文件 + `.test` | `UserCard.test.tsx` |
| API 服务 | camelCase | `userService.ts` |

### 变量/函数命名

| 类型 | 规范 | 示例 |
|------|------|------|
| 普通变量 | camelCase | `userName`, `isLoading` |
| 常量 | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| 函数 | camelCase 动词开头 | `getUserInfo()`, `handleSubmit()` |
| React 组件 | PascalCase | `UserCard`, `LoginForm` |
| 自定义 Hook | use 前缀 | `useAuth()`, `useUserList()` |
| 事件处理函数 | handle 前缀 | `handleClick`, `handleFormSubmit` |
| 布尔变量 | is/has/can 前缀 | `isVisible`, `hasPermission` |
| 类型/接口 | PascalCase + 类型后缀 | `UserType`, `ApiResponse` |

### 数据库命名

| 类型 | 规范 | 示例 |
|------|------|------|
| 表名 | snake_case 复数 | `users`, `order_items` |
| 字段名 | snake_case | `created_at`, `user_id` |
| 索引名 | idx\_{表名}\_{字段} | `idx_users_email` |
| 外键名 | fk\_{表名}\_{关联表} | `fk_orders_users` |

---

## 三、代码规范

### TypeScript / JavaScript

```typescript
// ✅ 好的写法
const getUserById = async (id: number): Promise<User | null> => {
  try {
    const user = await prisma.user.findUnique({ where: { id } })
    return user
  } catch (error) {
    logger.error('[UserService] 查询用户失败:', error)
    throw new AppError('查询用户失败', 500)
  }
}

// ❌ 避免的写法
const getUser = (id) => {  // 缺少类型
  return db.query("SELECT * FROM users WHERE id = " + id)  // SQL 注入风险！
}
```

**规则清单：**
- 所有函数/变量必须有类型注解（TypeScript 项目）
- 禁止使用 `any`，用 `unknown` 代替
- 异步函数必须 try/catch
- 禁止 SQL 字符串拼接，必须参数化查询
- 函数长度不超过 50 行，超过则拆分
- 文件长度不超过 300 行，超过则拆分模块

### Python（如使用）

```python
# ✅ 好的写法
async def get_user_by_id(user_id: int) -> Optional[User]:
    """根据 ID 获取用户。"""
    try:
        user = await User.get_or_none(id=user_id)
        return user
    except Exception as e:
        logger.error(f"[UserService] 查询用户失败: {e}")
        raise AppException("查询用户失败", status_code=500)
```

### 注释规范

```typescript
// 单行注释：描述「为什么」而非「是什么」
// 用 setTimeout 是因为动画需要在 DOM 更新后执行
setTimeout(() => animate(), 0)

/**
 * 发送验证码邮件
 * @param email - 目标邮箱地址
 * @param code - 6位验证码
 * @returns 发送是否成功
 * @throws {EmailError} 当邮件服务不可用时
 */
async function sendVerifyEmail(email: string, code: string): Promise<boolean> {}

// TODO: 后续优化为批量发送，减少数据库查询
// FIXME: 在 Safari 上有兼容性问题，待排查
```

---

## 四、API 开发规范

### 统一响应封装

```typescript
// 后端统一用此函数返回
const success = (data: any, message = 'success') => ({
  code: 200, message, data, timestamp: new Date().toISOString()
})

const fail = (message: string, code = 400) => ({
  code, message, data: null, timestamp: new Date().toISOString()
})
```

### 接口层级规范

```
Controller  ─── 只处理请求/响应，不含业务逻辑
    │
Service     ─── 业务逻辑，调用多个 Model
    │
Model/ORM   ─── 数据库操作，只做 CRUD
```

---

## 五、Git 提交规范

### 格式

```
[{模块}] {动词}{内容}
```

### 模块标签

| 标签 | 用途 |
|------|------|
| `[初始化]` | 项目搭建 |
| `[用户]` | 用户相关功能 |
| `[前端]` | 前端页面/组件 |
| `[后端]` | 后端接口/逻辑 |
| `[数据库]` | 数据库变更 |
| `[修复]` | Bug 修复 |
| `[优化]` | 性能/体验优化 |
| `[文档]` | 文档更新 |
| `[测试]` | 测试相关 |
| `[部署]` | 部署配置 |

### 示例

```
✅ [初始化] 项目脚手架搭建，配置 TypeScript + ESLint
✅ [用户] 注册登录接口开发，包含 JWT 认证
✅ [前端] 首页布局实现，响应式适配移动端
✅ [数据库] 添加 orders 表，建立与 users 的关联
✅ [修复] 修复登录后 Token 刷新不更新的问题
❌ fix bug         （太模糊）
❌ update code     （无意义）
```

---

## 六、设计规范（前端项目）

### 色彩系统

```css
:root {
  /* 主色 */
  --color-primary:        #3b82f6;   /* 蓝色，主操作 */
  --color-primary-hover:  #2563eb;   /* 主色 hover */
  --color-primary-light:  #eff6ff;   /* 主色浅背景 */

  /* 语义色 */
  --color-success:        #22c55e;
  --color-warning:        #f59e0b;
  --color-danger:         #ef4444;
  --color-info:           #06b6d4;

  /* 文字 */
  --color-text-primary:   #111827;   /* 标题/正文 */
  --color-text-secondary: #6b7280;   /* 辅助文字 */
  --color-text-disabled:  #d1d5db;   /* 禁用文字 */

  /* 背景 */
  --color-bg:             #ffffff;
  --color-bg-secondary:   #f9fafb;
  --color-bg-tertiary:    #f3f4f6;

  /* 边框 */
  --color-border:         #e5e7eb;
  --color-border-focus:   #3b82f6;
}
```

### 字体规范

```css
/* 字体族 */
font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'PingFang SC',
             'Microsoft YaHei', sans-serif;

/* 字号层级 */
--text-xs:   12px;   /* 标签、说明文字 */
--text-sm:   14px;   /* 正文 */
--text-base: 16px;   /* 大正文 */
--text-lg:   18px;   /* 小标题 */
--text-xl:   20px;   /* 标题 */
--text-2xl:  24px;   /* 页面标题 */
--text-3xl:  30px;   /* 大标题 */
```

### 间距规范

| 变量 | 值 | 用途 |
|------|-----|------|
| --space-1 | 4px | 图标与文字间距 |
| --space-2 | 8px | 组件内间距 |
| --space-3 | 12px | 表单项间距 |
| --space-4 | 16px | 卡片内边距 |
| --space-6 | 24px | 区块间距 |
| --space-8 | 32px | 大区块间距 |

### 组件规范

| 组件 | 默认高度 | 圆角 |
|------|----------|------|
| 按钮（小） | 32px | 6px |
| 按钮（默认） | 40px | 8px |
| 按钮（大） | 48px | 8px |
| 输入框 | 40px | 8px |
| 卡片 | 自适应 | 12px |
| 模态框 | 自适应 | 16px |

### 响应式断点

```css
/* 移动端优先 */
/* sm: */  @media (min-width: 640px) {}
/* md: */  @media (min-width: 768px) {}
/* lg: */  @media (min-width: 1024px) {}
/* xl: */  @media (min-width: 1280px) {}
/* 2xl: */ @media (min-width: 1536px) {}
```

---

## 七、测试规范

### 测试覆盖要求

| 层级 | 覆盖率目标 | 重点 |
|------|-----------|------|
| 工具函数 | ≥ 90% | 边界值、异常情况 |
| Service 层 | ≥ 80% | 业务逻辑分支 |
| Controller 层 | ≥ 70% | 正常/异常响应 |
| 前端组件 | ≥ 60% | 用户交互、状态变化 |

### 测试文件结构

```typescript
describe('UserService', () => {
  describe('register()', () => {
    it('正常注册应返回用户信息和 Token', async () => {})
    it('邮箱已存在应抛出 409 错误', async () => {})
    it('密码不符合规则应抛出 400 错误', async () => {})
  })
})
```
````
