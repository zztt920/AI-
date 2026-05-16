[AI游戏系统原型设计器 - 项目文档.md](https://github.com/user-attachments/files/27859002/AI.-.md)# AI 辅助游戏系统原型设计器

> 基于 GPT 大模型的游戏系统设计文档（FD）自动生成工具，输入玩法关键词即可输出包含核心逻辑、交互流程、数值框架、流程图、UI原型和逻辑校验的完整设计文档。

---

## 目录

1. [项目概述](#1-项目概述)
2. [核心功能](#2-核心功能)
3. [技术架构](#3-技术架构)
4. [安装与运行](#4-安装与运行)
5. [项目结构](#5-项目结构)
6. [数据库模型](#6-数据库模型)
7. [API 接口](#7-api-接口)
8. [使用指南](#8-使用指南)
9. [Mock 模式与 AI 模式](#9-mock-模式与-ai-模式)
10. [部署说明](#10-部署说明)

---

## 1. 项目概述

### 1.1 背景

传统游戏系统策划工作中，撰写一份完整的功能设计文档（FD）平均需要 3-5 天。过程中需要反复与程序、美术、测试沟通确认，逻辑漏洞往往在多人评审后才能发现，验证周期长。

本项目旨在通过 AI 技术实现系统设计文档的自动化生成，将设计效率提升 60% 以上，同时通过内置的逻辑校验引擎在文档生成阶段就发现潜在问题。

### 1.2 设计目标

- **效率提升**：将系统设计文档撰写时间从 3-5 天缩短至 5-10 分钟
- **质量保证**：AI 自动校验系统间冲突、数值失衡、边界情况缺失
- **可视化输出**：自动生成 Mermaid 流程图和 UI 线框原型
- **标准化格式**：统一输出行业标准的 Markdown 功能设计文档

---

## 2. 核心功能

### 2.1 功能总览

| 功能模块 | 说明 | 输出格式 |
|---------|------|---------|
| AI 需求解析 | 分析游戏类型和玩法关键词，提取核心机制 | - |
| 核心逻辑文档 | 系统概述、核心循环、模块设计、数据结构 | Markdown |
| 交互流程文档 | 操作流程、响应逻辑、界面跳转、异常处理 | Markdown |
| 数值框架文档 | 成长曲线、经济系统、战斗公式、概率设计 | Markdown |
| 流程图生成 | 基于 Mermaid 语法的系统流程图 | SVG 渲染 |
| UI 原型生成 | 8-12 个核心界面的线框图和元素清单 | JSON + 可视化 |
| 逻辑校验引擎 | 系统冲突检测、数值平衡检查、边界情况评估 | Markdown 报告 |
| 历史记录管理 | 所有生成原型自动持久化，支持查看和删除 | - |

### 2.2 6 步 AI 生成流水线

```
[需求输入] → [核心逻辑生成] → [交互流程生成] → [数值框架生成]
                ↓                    ↓                    ↓
         [保存数据库]          [保存数据库]          [保存数据库]
                ↓                    ↓                    ↓
         [流程图生成] → [UI 原型生成] → [逻辑校验] → [完成]
```

每一步生成结果实时保存到数据库，确保即使后续步骤失败，已有成果也不会丢失。

---

## 3. 技术架构

### 3.1 技术栈

| 层级 | 技术选型 | 版本 |
|------|---------|------|
| **前端框架** | React + TypeScript | React 19, TS 5.x |
| **构建工具** | Vite | 7.x |
| **样式方案** | Tailwind CSS + shadcn/ui | Tailwind 3.4 |
| **路由方案** | react-router | v7 |
| **后端框架** | Hono + tRPC | tRPC 11.x |
| **数据库** | MySQL + Drizzle ORM | MySQL 8.x |
| **AI 引擎** | Kimi API (GPT-4 级别) | kimi-latest |
| **流程图渲染** | Mermaid.js | 11.x |
| **Markdown 渲染** | react-markdown + remark-gfm | - |

### 3.2 架构图

```
┌─────────────────────────────────────────────────────┐
│                    前端 (Frontend)                    │
│  ┌──────────┐  ┌──────────────┐  ┌──────────────┐   │
│  │ Home     │  │ Prototype    │  │ History      │   │
│  │ 需求输入 │  │ Detail 详情  │  │ 历史记录     │   │
│  └──────────┘  └──────────────┘  └──────────────┘   │
│                                                      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────┐  │
│  │ Flowchart    │  │ UIPrototype  │  │ Markdown │  │
│  │ Viewer 流程图 │  │ Viewer UI原型 │  │ Renderer │  │
│  └──────────────┘  └──────────────┘  └──────────┘  │
│                                                      │
│  tRPC Client ←─────────→ tRPC Server (Hono)        │
└─────────────────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────┐
│                    后端 (Backend)                     │
│                                                      │
│  ┌──────────┐  ┌──────────┐  ┌──────────────────┐  │
│  │ prototype │  │ ai       │  │ validator        │  │
│  │ Router   │  │ Router   │  │ Router           │  │
│  │ 原型 CRUD │  │ AI 生成  │  │ 逻辑校验         │  │
│  └──────────┘  └──────────┘  └──────────────────┘  │
│                                                      │
│  ┌──────────┐  ┌──────────┐  ┌──────────────────┐  │
│  │ Drizzle  │  │ MySQL    │  │ Kimi API         │  │
│  │ ORM      │  │ Database │  │ (AI 引擎)        │  │
│  └──────────┘  └──────────┘  └──────────────────┘  │
└─────────────────────────────────────────────────────┘
```

---

## 4. 安装与运行

### 4.1 环境要求

- Node.js 20+
- MySQL 8.x（或兼容的 PlanetScale 服务）
- Kimi API Key（可选，无 Key 时使用 Mock 模式）

### 4.2 安装步骤

```bash
# 1. 解压项目
unzip AI游戏系统原型设计器.zip
cd app

# 2. 安装依赖
npm install

# 3. 配置环境变量
# 编辑 .env 文件，确保 DATABASE_URL 正确
# 如需启用 AI 功能，添加 KIMI_API_KEY

# 4. 初始化数据库
npm run db:push

# 5. 启动开发服务器
npm run dev

# 6. 打开浏览器访问
# http://localhost:3000
```

### 4.3 常用命令

| 命令 | 说明 |
|------|------|
| `npm run dev` | 启动开发服务器（端口 3000） |
| `npm run build` | 构建生产版本 |
| `npm run start` | 启动生产服务器 |
| `npm run check` | TypeScript 类型检查 |
| `npm run db:push` | 同步数据库 Schema |
| `npm run db:generate` | 生成数据库迁移文件 |
| `npm run db:migrate` | 执行数据库迁移 |

---

## 5. 项目结构

```
├── api/                          # 后端代码
│   ├── boot.ts                   # Hono 服务启动入口
│   ├── context.ts                # tRPC 上下文
│   ├── middleware.ts             # tRPC 中间件定义
│   ├── router.ts                 # 路由注册中心
│   ├── lib/                      # 工具库
│   │   ├── env.ts                # 环境变量管理
│   │   ├── http.ts               # HTTP 工具
│   │   └── vite.ts               # Vite 集成
│   ├── queries/
│   │   └── connection.ts         # Drizzle 数据库连接
│   └── routers/                  # tRPC 路由
│       ├── ai.ts                 # AI 生成引擎（核心）
│       ├── prototype.ts          # 原型 CRUD 操作
│       └── validator.ts          # 逻辑校验引擎
│
├── contracts/                    # 前后端共享类型
│   ├── types.ts                  # 业务类型定义
│   └── errors.ts                 # 错误类型定义
│
├── db/                           # 数据库
│   ├── schema.ts                 # 表结构定义
│   ├── relations.ts              # 表关系定义
│   ├── seed.ts                   # 种子数据脚本
│   └── migrations/               # 迁移文件目录
│
├── src/                          # 前端代码
│   ├── main.tsx                  # React 入口
│   ├── App.tsx                   # 根路由组件
│   ├── index.css                 # 全局样式
│   ├── pages/                    # 页面组件
│   │   ├── Home.tsx              # 首页（需求输入）
│   │   ├── PrototypeDetail.tsx   # 原型详情页
│   │   └── History.tsx           # 历史记录页
│   ├── components/               # 业务组件
│   │   ├── FlowchartViewer.tsx   # Mermaid 流程图渲染
│   │   └── UIPrototypeViewer.tsx # UI 原型可视化
│   ├── components/ui/            # shadcn/ui 组件库
│   ├── providers/
│   │   └── trpc.tsx              # tRPC 客户端配置
│   ├── hooks/                    # 自定义 Hooks
│   ├── lib/                      # 前端工具库
│   └── types/                    # 前端类型定义
│
├── .env                          # 环境变量（请勿提交到 Git）
├── .env.example                  # 环境变量示例
├── drizzle.config.ts             # Drizzle 配置
├── vite.config.ts                # Vite 配置
├── tailwind.config.js            # Tailwind 配置
├── tsconfig.json                 # TypeScript 配置
└── package.json                  # 依赖管理
```

---

## 6. 数据库模型

### 6.1 prototypes 表

存储所有生成的游戏系统原型数据。

| 字段 | 类型 | 说明 |
|------|------|------|
| id | serial PK | 自增主键 |
| title | varchar(255) | 系统名称/主题 |
| gameType | varchar(100) | 游戏类型（RPG/ACT/SLG等） |
| keywords | varchar(500) | 核心玩法关键词 |
| status | enum | 状态：generating/completed/error |
| coreLogic | text | 核心逻辑文档（Markdown） |
| interactionFlow | text | 交互流程文档（Markdown） |
| numericalFramework | text | 数值框架文档（Markdown） |
| validationResult | text | 逻辑校验报告（Markdown） |
| flowchartMermaid | text | Mermaid 流程图代码 |
| documentMarkdown | text | 完整文档（Markdown） |
| uiPrototype | json | UI 原型数据（JSON） |
| createdAt | timestamp | 创建时间 |
| updatedAt | timestamp | 更新时间 |

### 6.2 状态流转

```
[创建记录] → status: generating
                ↓
         [AI 生成中]
                ↓
    ┌─────[成功]─────┐    ┌─────[失败]─────┐
    ↓                ↓    ↓                ↓
 status:       status:  status:       status:
 completed    completed   error          error
                ↓                          ↓
         [查看详情]                  [重试生成]
```

---

## 7. API 接口

### 7.1 prototype 路由

| 接口 | 类型 | 输入 | 输出 | 说明 |
|------|------|------|------|------|
| `prototype.list` | query | - | Prototype[] | 获取所有原型列表（按时间倒序） |
| `prototype.get` | query | `{ id: number }` | Prototype | null | 根据 ID 获取单个原型 |
| `prototype.create` | mutation | `{ title, gameType, keywords }` | `{ id }` | 创建新原型记录 |
| `prototype.update` | mutation | `{ id, ...fields }` | `{ success }` | 更新原型字段 |
| `prototype.delete` | mutation | `{ id: number }` | `{ success }` | 删除原型 |

### 7.2 ai 路由

| 接口 | 类型 | 输入 | 输出 | 说明 |
|------|------|------|------|------|
| `ai.generatePrototype` | mutation | `{ id, title, gameType, keywords, requirements? }` | `{ success, coreLogic, ... }` | 6 步 AI 生成流水线 |

**生成流程**：
1. 生成核心逻辑文档
2. 生成交互流程文档
3. 生成数值框架文档
4. 生成 Mermaid 流程图
5. 生成 UI 原型（JSON）
6. 生成逻辑校验报告

### 7.3 validator 路由

| 接口 | 类型 | 输入 | 输出 | 说明 |
|------|------|------|------|------|
| `validator.validateSystem` | mutation | `{ coreLogic, modules[], numericalFramework? }` | `{ overall, issues[], metrics }` | 系统逻辑校验 |

**校验维度**：
- 模块依赖完整性（输入是否有产出、产出是否有消费）
- 数值框架完整性（成长曲线、经济系统、平衡性）
- 核心逻辑完整性（异常处理、状态机、数据流）
- 模块数量合理性（<3 或 >15 个模块时报警）

---

## 8. 使用指南

### 8.1 生成游戏系统原型

1. **进入首页**（`/`）
2. **填写基本信息**：
   - 系统名称/主题：例如「武侠角色养成系统」
   - 游戏类型：从标签中选择（RPG/ACT/SLG 等）
   - 核心玩法关键词：例如「经脉系统、装备强化、技能树、门派选择」
3. **补充需求描述**（可选）：详细描述特殊需求、目标玩家、关联系统等
4. **点击「生成游戏系统原型」按钮**
5. **等待 AI 生成**（约 10-30 秒，Mock 模式约 1-2 秒）
6. **查看结果**：自动跳转到详情页，展示 6 大模块的完整输出

### 8.2 查看原型详情

详情页包含 6 个标签页：

| 标签页 | 内容 |
|--------|------|
| 核心逻辑 | 系统概述、核心循环、模块设计、数据结构、接口定义、边界处理 |
| 交互流程 | 操作流程、响应逻辑、界面跳转、异常处理、并发处理 |
| 数值框架 | 成长曲线、经济系统、战斗公式、概率设计、平衡校验 |
| 流程图 | Mermaid 流程图可视化渲染 |
| UI 原型 | 界面列表 + 线框图 + 元素详情 |
| 逻辑校验 | 校验概述、问题清单、数值检查、边界评估、优化建议 |

### 8.3 使用快捷示例

首页底部提供 4 个快捷示例，点击即可自动填充：

- **武侠角色养成系统**（RPG）：经脉系统、装备强化、技能树、门派选择
- **Roguelike 卡牌战斗**（Roguelike）：卡牌构筑、遗物系统、随机事件、连击机制
- **多人在线竞技场**（ACT）：匹配系统、排行榜、段位晋升、赛季奖励
- **模拟经营餐厅**（模拟经营）：菜谱研发、员工管理、顾客满意度、装修系统

---

## 9. Mock 模式与 AI 模式

### 9.1 Mock 模式（默认）

当没有配置 `KIMI_API_KEY` 时，系统自动使用 Mock 模式：

- 使用预置的专业游戏设计模板
- 模板参数根据用户输入的标题、游戏类型、关键词动态填充
- 包含完整的 6 大文档模块
- 适合演示、测试和本地开发

### 9.2 AI 模式

配置 `KIMI_API_KEY` 后启用真实 AI 生成：

```bash
# .env
KIMI_API_KEY=sk-xxxxxxxxxxxxxxxx
```

- 调用 Kimi API（GPT-4 级别模型）
- 根据用户输入实时生成定制化内容
- 生成质量更高，内容更贴合实际需求
- 获取 API Key：[Kimi 开放平台](https://platform.moonshot.cn/)

### 9.3 模式对比

| 特性 | Mock 模式 | AI 模式 |
|------|----------|---------|
| 需要 API Key | 否 | 是 |
| 生成速度 | 1-2 秒 | 10-30 秒 |
| 内容质量 | 标准模板 | 定制化生成 |
| 适用场景 | 演示/开发/测试 | 生产环境 |
| 成本 | 免费 | 按 API 调用计费 |

---

## 10. 部署说明

### 10.1 构建生产版本

```bash
npm run build
```

输出目录：`dist/`
- `dist/public/`：前端静态文件
- `dist/boot.js`：后端服务入口

### 10.2 生产环境启动

```bash
npm run start
```

服务监听端口：3000

### 10.3 环境变量

| 变量名 | 必填 | 说明 |
|--------|------|------|
| `DATABASE_URL` | 是 | MySQL 连接地址 |
| `APP_ID` | 是 | 应用 ID |
| `APP_SECRET` | 是 | 应用密钥 |
| `KIMI_API_KEY` | 否 | Kimi API Key（启用 AI 模式） |
| `NODE_ENV` | 否 | production 或 development |

---
