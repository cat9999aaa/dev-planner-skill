# 🤖 dev-planner-skill

> **对话式 AI 开发规划师** —— 让任何人都能通过对话，把一个想法变成完整的项目。

---

## ⚡ 给 AI 编程工具的集成指南

> 本节是写给 AI 的。如果你是 Claude Code、Antigravity、Codex CLI、Gemini CLI 或其他 AI 编程助手，请阅读此节了解如何加载和使用这个 Skill。

### 这个 Skill 是什么

`dev-planner` 是一套对话式开发规划工作流，核心文件是 `SKILL.md`。加载后，你将获得以下能力：

- 通过多轮选项对话引导用户完善开发需求（零术语，小白友好）
- 自动生成三份企业级开发文档（`DEV_DOCUMENT.md` / `API 文档` / `风格指南`）
- 编排 Agent 子团队（后端 / 前端 / 测试 / 文档）并行开发
- 自动检测并调用环境中可用的 MCP 工具（GitHub / 数据库 / Docker / web_search）
- 全程文档驱动开发，节点测试门控，中文 Git 提交

---

### 📥 文件下载（所有平台通用）

```bash
# 下载核心 Skill 文件
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/SKILL.md -o SKILL.md

# 下载完整模板包（建议一并下载，AI 生成文档时会引用）
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/references/dev-doc-template.md -o dev-doc-template.md
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/references/api-doc-template.md -o api-doc-template.md
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/references/style-guide-template.md -o style-guide-template.md
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/references/plan-template.md -o plan-template.md

# 或者克隆整个仓库
git clone https://github.com/cat9999aaa/dev-planner-skill.git
```

---

### 🔧 各平台集成方式

---

#### Antigravity

Antigravity 有两种安装方式，根据使用范围选择：

**方式一：项目级安装（只对当前项目生效）**

```bash
# 在你的项目根目录下执行
mkdir -p .agent/skills/dev-planner

# 下载 SKILL.md 到技能目录
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/SKILL.md \
  -o .agent/skills/dev-planner/SKILL.md

# 下载模板文件（可选，提升文档生成质量）
mkdir -p .agent/skills/dev-planner/references
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/references/dev-doc-template.md \
  -o .agent/skills/dev-planner/references/dev-doc-template.md
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/references/api-doc-template.md \
  -o .agent/skills/dev-planner/references/api-doc-template.md
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/references/style-guide-template.md \
  -o .agent/skills/dev-planner/references/style-guide-template.md
```

最终目录结构：

```
你的项目/
└── .agent/
    └── skills/
        └── dev-planner/
            ├── SKILL.md          ← 核心文件
            └── references/       ← 模板文件（可选）
```

**方式二：全局安装（对所有项目生效）**

```bash
# Linux / macOS
mkdir -p ~/.antigravity/skills/dev-planner
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/SKILL.md \
  -o ~/.antigravity/skills/dev-planner/SKILL.md

# Windows（PowerShell）
New-Item -ItemType Directory -Force "$env:USERPROFILE\.antigravity\skills\dev-planner"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/SKILL.md" `
  -OutFile "$env:USERPROFILE\.antigravity\skills\dev-planner\SKILL.md"
```

或者，如果你已经把仓库克隆到本地，可以用 CLI 工具链接：

```bash
# 进入 skill 目录
cd dev-planner-skill/dev-planner

# 链接为全局 skill
ag skill link --global
```

---

#### Claude Code

Claude Code 通过 `CLAUDE.md` 文件加载上下文指令。

**项目级安装（推荐）：**

```bash
# 在项目根目录下载 SKILL.md，重命名为 CLAUDE.md
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/SKILL.md \
  -o CLAUDE.md

# 同时下载模板文件到 references/ 目录
mkdir -p references
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/references/dev-doc-template.md \
  -o references/dev-doc-template.md
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/references/api-doc-template.md \
  -o references/api-doc-template.md
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/references/style-guide-template.md \
  -o references/style-guide-template.md
```

**全局安装（对所有项目生效）：**

```bash
# Linux / macOS
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/SKILL.md \
  -o ~/.claude/CLAUDE.md

# Windows（PowerShell）
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/SKILL.md" `
  -OutFile "$env:USERPROFILE\.claude\CLAUDE.md"
```

Claude Code 启动后会自动读取 `CLAUDE.md`，当用户提到"帮我开发项目"等关键词时自动触发此 Skill。

---

#### OpenAI Codex CLI

Codex CLI 通过项目根目录的 `codex.md` 文件加载指令。

```bash
# 在项目根目录执行
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/SKILL.md \
  -o codex.md

# 下载模板文件
mkdir -p references
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/references/dev-doc-template.md \
  -o references/dev-doc-template.md
# （其余模板同上，按需下载）
```

Codex CLI 启动时会自动读取当前目录的 `codex.md`，无需额外配置。

---

#### Google Gemini CLI

Gemini CLI 通过项目根目录的 `GEMINI.md` 文件加载指令（类似 Claude Code 的 `CLAUDE.md`）。

**项目级安装：**

```bash
# 在项目根目录执行
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/SKILL.md \
  -o GEMINI.md
```

**全局安装：**

```bash
# Linux / macOS
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/SKILL.md \
  -o ~/.gemini/GEMINI.md
```

---

#### Cursor

Cursor 通过 `.cursorrules` 文件（或新版 `.cursor/rules/` 目录）加载项目级规则。

```bash
# 方式一：使用 .cursorrules（旧版兼容）
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/SKILL.md \
  -o .cursorrules

# 方式二：使用新版规则目录（Cursor 0.43+）
mkdir -p .cursor/rules
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/SKILL.md \
  -o .cursor/rules/dev-planner.mdc
```

---

#### Aider

Aider 通过 `--system` 参数或配置文件加载自定义指令。

```bash
# 先下载 SKILL.md
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/SKILL.md \
  -o .aider-skill.md

# 启动时注入
aider --system "$(cat .aider-skill.md)"
```

或写入 Aider 配置文件 `.aider.conf.yml`：

```yaml
# .aider.conf.yml
system: |
  # 在此粘贴 SKILL.md 的完整内容
```

---

#### Continue.dev（VS Code / JetBrains 插件）

在 Continue 配置文件 `~/.continue/config.json` 中添加系统提示：

```bash
# 先下载 SKILL.md 内容
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/SKILL.md \
  -o /tmp/dev-planner-skill.md
```

然后编辑 `~/.continue/config.json`：

```json
{
  "models": [
    {
      "title": "Claude with Dev Planner",
      "provider": "anthropic",
      "model": "claude-opus-4-6",
      "systemMessage": "<粘贴 SKILL.md 的完整内容>"
    }
  ]
}
```

---

#### 通用方案（适用于任何支持系统提示词的 AI 工具）

如果你使用的工具不在上面列表中，只要它支持自定义系统提示词（System Prompt），都可以用这个方法：

```bash
# 获取 SKILL.md 内容，复制到剪贴板
curl -L https://raw.githubusercontent.com/cat9999aaa/dev-planner-skill/main/SKILL.md
```

将输出内容粘贴到你的工具的「系统提示词」、「自定义指令」或「角色设定」区域即可。

---

### 📁 文件结构说明

```
dev-planner/
├── SKILL.md                          # 核心 Skill 文件，加载此文件即可激活全部能力
└── references/
    ├── dev-doc-template.md           # DEV_DOCUMENT.md 超详细模板（含架构图/ER图/流程图）
    ├── api-doc-template.md           # API 接口文档模板（含请求响应示例/错误码）
    ├── style-guide-template.md       # 代码规范+设计系统模板
    └── plan-template.md              # 规划方案输出模板
```

**最小化集成**：只需 `SKILL.md` 即可运行，`references/` 中的模板用于提升文档质量，建议一并下载。

---

### 🚦 触发关键词

以下任意词语会触发 dev-planner Skill：

```
"帮我开发..."     "我想做一个..."    "我有个想法..."
"做个网站"        "做个APP"          "做个脚本"
"做个机器人"      "做个系统"         "plan模式"
"对话式开发"      "生成开发文档"     "帮我规划项目"
```

即使描述非常模糊（如"我想做个东西"），也应触发此 Skill 进入对话规划模式。

---

### 🔄 工作流概览（供 AI 理解）

```
用户描述想法
    │
    ▼
Phase 1  PLAN MODE
  8步选项对话 → 收集：项目类型/规模/技术栈/功能/UI/部署
    │ 用户确认规划
    ▼
Phase 2  DOC GEN
  生成三份文档：DEV_DOCUMENT.md + API文档 + 风格指南
    │ 用户确认文档
    ▼
Phase 3  MCP SETUP
  检测可用工具：GitHub MCP / 数据库 MCP / Docker MCP / web_search
    │
    ▼
Phase 4  TEAM DEV
  编排 Agent 团队并行开发：
  后端Agent + 前端Agent + 测试Agent + 文档Agent
  每节点：开发 → MCP辅助 → 测试 → 提交 → 文档同步
    │ 全部节点完成
    ▼
Phase 5  DELIVERY
  最终报告 + 技术手册 + 用户手册 + 手把手部署教程
```

---

### ⚠️ 重要约束（AI 必须遵守）

1. **文档优先**：所有开发严格遵循生成的三份文档，不得擅自更改技术栈
2. **测试门控**：每节点测试不通过，不得提交，不得跳过
3. **MCP 优先**：有 MCP 工具可用时优先调用，不要手动模拟
4. **中文提交**：所有 Git 提交信息必须使用中文
5. **文档同步**：每节点完成后立即更新三份文档

---

---

## 👋 给人看的介绍

### 这个项目是什么

dev-planner-skill 是一个 **AI 编程 Skill（技能包）**，专门解决一个问题：

> **我有个想法，但不懂怎么开发，也不知道从哪开始。**

装了这个 Skill 的 AI，会变成一个有耐心的开发团队负责人。它不会上来就让你填技术栈、写需求文档——它会像朋友一样问你"你想做什么"，然后通过一系列选择题，一步步帮你把脑子里那个模糊的想法，变成一份清清楚楚的开发计划。

计划确认后，AI 会自动生成三份企业级文档，然后召唤一支虚拟的 Agent 开发团队（后端/前端/测试/文档），并行把项目做出来，全程不需要你懂任何技术。

---

### 为什么做这个

做这个项目源于一个真实的观察：

现在的 AI 编程工具越来越强，但**门槛反而越来越高**。你要懂怎么写 Prompt，要知道选什么技术栈，要理解什么是 API……对于真正的"小白"来说，这些工具好像和他们没什么关系。

另一个问题是，即使是有经验的开发者，用 AI 开发的过程也经常是混乱的：AI 今天改了这里，明天忘了那里，代码越来越乱，文档从来没有，到最后自己都不知道项目是什么状态。

dev-planner 想解决这两个问题：

1. **让小白用得起来**：对话全程选择题，不需要懂任何技术术语
2. **让开发过程有秩序**：文档驱动，测试门控，每一步都有记录，项目状态随时清晰

---

### 怎么用

#### 如果你用的是 Claude Code

```bash
# 安装
claude skill install https://github.com/cat9999aaa/dev-planner-skill/releases/latest/download/dev-planner.skill

# 然后直接说话就行，比如：
# "我想做一个记账 App"
# "帮我开发一个自动发微博的脚本"
# "我要做个公司内部的请假审批系统"
```

#### 如果你用的是其他 AI 工具

1. 打开 https://github.com/cat9999aaa/dev-planner-skill
2. 下载 `dev-planner/SKILL.md`
3. 把里面的内容复制粘贴到你用的 AI 工具的"自定义系统提示词"区域
4. 开始对话

#### 对话示例

```
你：我想做一个帮我管理读书笔记的网站

AI：👋 你好！根据你的描述，这个项目属于：
   🌐 A. 网站/Web 应用
   📱 B. 手机 APP
   ...
   （你选 A）

AI：这个项目大概多大？
   🌱 A. 小工具，就我自己用
   ...
   （你选 A）

   ... 几轮选择后 ...

AI：规划完成！共 12 个开发节点，预计使用 Vue3 + Node.js + SQLite。
   回复"确认"开始生成开发文档。

你：确认

AI：三份文档生成完毕。回复"开始开发"启动 Agent 团队。

你：开始开发

AI：[后端 Agent] 数据库设计完成 ✅
    [前端 Agent] 项目脚手架完成 ✅
    [后端 Agent] 笔记 CRUD 接口完成 ✅
    ...（全自动，直到完成）
```

---

### 文档结构

| 文件                                             | 说明                                     |
| ------------------------------------------------ | ---------------------------------------- |
| `dev-planner/SKILL.md`                           | 核心 Skill，定义了完整的工作流程和规范   |
| `dev-planner/references/dev-doc-template.md`     | 开发文档模板（含架构图/ER图/环境配置等） |
| `dev-planner/references/api-doc-template.md`     | API 接口文档模板                         |
| `dev-planner/references/style-guide-template.md` | 代码规范+设计系统模板                    |
| `dev-planner/references/plan-template.md`        | 规划方案输出模板                         |
| `dev-planner.skill`                              | 打包后的 Skill 文件（可直接安装）        |

---

### 贡献与反馈

有想法、发现问题、或者希望支持更多平台，欢迎：

- 提 [Issue](https://github.com/cat9999aaa/dev-planner-skill/issues)
- 提 Pull Request
- 或者直接在 Issue 里描述你用这个 Skill 做了什么，哪里不够用

---

### License

MIT License — 随便用，随便改，改好了记得回来分享。
