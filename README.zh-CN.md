# MyClaws

![MyClaws Demo](./demo.png)

> 把 AI Agent 部署到任意机器，用一台电脑或一部手机统一控制。

[English](./README.md) · [运行文档](./run_docs/README.md) · [架构文档](./dev_docs/project_knowledge/architecture.md) · [开发文档索引](./dev_docs/index.md) · [许可证草案](./LICENSE) · [商业授权草案](./COMMERCIAL_LICENSE.md) · [社区行为准则](./CODE_OF_CONDUCT.md) · [贡献指南](./CONTRIBUTING.md) · [安全披露](./SECURITY.md)

MyClaws 是一个面向高级用户的公开源码 AI Agent 平台，核心价值不是“在一台机器里跑一个 Agent”，而是把很多真实机器统一接入一个控制面。它不做人为的平台主机上限限制，不额外套沙箱，复杂任务可以直接交给最强模型，同时你仍然可以按自己的需要组合 Experts、工具、Skills 和 MCP。

如果你熟悉 OpenClaw 或其他终端优先的 Agent 工作流，MyClaws 最大的不同点很直接：它不是“单机 + 单终端会话”模型，而是“多机器统一控制 + 人类主导对话 + 专用客户端”模型。

## 与其他 Agent 产品的定位差异

> 以下对比基于 2026-05-21 查阅的官方产品页与官方文档整理。

| 产品 | 主要交互界面 | Agent 实际运行位置 | 是否以多机器远程控制为核心 | 执行方式 | 更适合谁 |
|---|---|---|---|---|---|
| **MyClaws** | 专用桌面端、移动端、Admin 网页入口 | 你自己的 Windows / macOS / Linux Claw 主机 | **是**，核心就是一套 UI 统一调度多台机器 | **默认无沙箱**，直接继承宿主机账号权限 | 需要统一操控多台真实机器的高级用户、运维和工程团队 |
| **OpenClaw** | 聊天应用、浏览器、语音 / 电话入口 | 自托管网关与其连接的工具 | 覆盖渠道很多，但核心不是主机舰队控制 | 自托管个人助手工作流 | 希望通过 Telegram、WhatsApp、浏览器、语音等多入口调用同一个助手的人 |
| **Claude Code** | 终端、IDE 扩展、桌面应用、Web | 本地开发环境加 Anthropic 提供的编码工作流 | 更强在编码协作，不是远程多主机控制面 | 带权限控制的编码 Agent 工作流 | 想把 Claude 深度嵌进日常开发工具链的开发者 |
| **Codex** | 与 ChatGPT 关联的编码 Agent、终端、云任务界面 | 本地与 Codex 管理的云环境 | 适合多任务编码，但不是自管主机控制中枢 | 偏托管的编码 Agent 执行模型 | 希望把 OpenAI 编码 Agent 与 ChatGPT、云执行环境联动的团队 |
| **OpenCode** | 终端、IDE、桌面端 | 主要是你当前打开的本地项目环境 | 本地 Agent 体验强，但不是多主机远程控制层 | 开源、本地优先的编码 Agent 工作流 | 想把开源编码 Agent 放进自己开发环境的开发者 |

真正的区别在于：MyClaws 不是只想做一个更顺手的 coding shell，而是要做 AI 工作的远程操作层。你可以接入很多台机器，给复杂任务挑最强模型，让人始终处在控制回路里，并且不被平台级沙箱和人为上限束住手脚。

## 概览

- `Admin` 负责身份认证、配置管理、文件中转和中心化记忆。
- `Claw` 运行在每台实际执行 Agent 和工具的主机上。
- `Client` 是适合长时任务的桌面控制中心。
- `Mobile` 让你离开工位后仍能继续跟进任务。

这种分层让你可以同时使用办公室 Mac、家里 Linux 主机和云端 GPU 机器，再从同一套 UI 中自由切换。

## 为什么是 MyClaws？

> 大多数编码 Agent 工具仍然绑定在单机、单终端上。MyClaws 走的是另一条路线。

| | MyClaws | 典型终端型 Agent |
|---|---|---|
| **主机控制** | 多机器统一管理，一个面板管全局 | 通常一次只盯一台机器或一个 shell |
| **安装与切换** | 主机注册一次即可，之后可从桌面端或手机端切换 | 更依赖手动切机器、SSH 跳转或逐台配置 |
| **对话模式** | 人类控制每一轮，历史更容易回看 | 更偏“一次跑到底”或终端滚屏回查 |
| **客户端体验** | 专用桌面端与移动端，支持文件传输、子 Agent 控制、Todo 面板 | 以终端或浏览器为主 |
| **专家体系** | 可自定义 Expert，绑定 prompt、工具、skills、MCP 与工作模式 | 人设和配置通常更轻 |
| **调试方式** | Bernard 元 Agent 协助理解和调优 Agent 行为 | 更多依赖人工读日志和猜 prompt |
| **记忆系统** | 按用户、Claw 主机、项目分层的中心化记忆 | 更偏本地、扁平化记忆 |
| **可观测性** | 实时显示上下文、token、工具调用和压缩事件 | 运行时上下文透明度较低 |
| **并行能力** | 多任务并发，单任务子 Agent 数量不设平台硬上限 | 更偏单会话导向 |
| **执行模型** | 本地优先、自主管理、无沙箱的高级用户模型 | 更常见为沙箱或强约束模型 |

## 核心亮点

- **多机器，一个控制面板**：想部署多少台 Claw 都可以，并且无需改变工作方式就能在它们之间切换。
- **默认 Human-in-the-Loop**：所有对话都按轮次推进、可回看，你掌控节奏，而不是把一切交给自动驾驶。
- **专用桌面端与移动端**：不是终端壳子，而是为文件传输、子 Agent 控制、Todo 查看、通知跟进而设计的界面。
- **自定义 Experts**：可以为不同专家定义独立的 prompt、tools、skills、MCP 集成与工作习惯。
- **Bernard 辅助调试**：内置 Meta Agent 帮你理解另一个 Agent 正在做什么，以及为什么这么做。
- **中心化分层记忆**：知识按用户、环境、项目沉淀，跨机器切换时上下文不丢。
- **Dream 风格记忆整理**：周期性把原始记忆压缩、归并成更结构化的长期知识。
- **实时上下文观察器**：任务运行时直接看到 token 消耗、工具调用和压缩事件。
- **并发多任务**：多个会话可同时运行；单任务的子 Agent 扩展主要受模型额度和主机资源限制，而不是平台硬限制。

## 架构

```text
┌─────────┐  HTTPS REST  ┌──────────┐  WebSocket  ┌─────────┐
│  Admin  │◄────────────►│  Client  │◄───────────►│  Claw   │
│ Fastify │              │Electron  │              │ Fastify │
└────┬────┘              └──────────┘              └────┬────┘
     │ A↔C: 鉴权、配置、Skills、MCP、文件、记忆      │
     └──────────────────────────────────────────────────┘
          ▲ HTTPS REST + WebSocket
          │
   ┌──────┴───────┐
   │    Mobile    │
   │ Expo / RN    │
   └──────────────┘
```

| 组件 | 角色 |
|---|---|
| `Admin` | 认证、配置、文件、中心化记忆、下载与协调服务 |
| `Claw` | Agent 运行时、工具执行、WebSocket 会话、本地持久化 |
| `Client` | 桌面控制中心，负责对话、主机切换和丰富任务 UI |
| `Mobile` | 当前公开发布中面向 Android 客户端的跟进、通知与人工介入入口 |

## 典型使用场景

- **一个人，多台机器**：笔记本一台、家里服务器一台、云主机一台，按任务类型切到最合适的环境。
- **长时工程对话**：把调试、架构审查、代码编辑做成“人类引导式对话”，而不是一次无人值守跑到底。
- **移动端跟进**：在桌面端启动任务，外出时用手机回复 Agent 问题，回来后继续接着完整历史处理。

## 截图与演示

当前公开版直接使用本文顶部这张 demo 图作为展示图，不再保留占位式截图说明。后续如需补充动图或分场景截图，再按发布素材节奏单独增加。

## 快速开始

### 直接安装发布版

- Client 下载： [GitHub Releases](https://github.com/weidwonder/myclaws-releases/releases)（`Android / Windows / macOS`）
- 远端 Agent 后端一键安装：

```bash
curl -fsSL https://myclaws.ai/install/claw.sh | bash
```

```powershell
irm https://myclaws.ai/install/claw.ps1 | iex
```

### 前置要求

- `Node.js >= 20`
- `pnpm >= 9`

### 安装

```bash
pnpm install
```

### 配置

从已经脱敏的根目录示例开始：

```bash
cp .env.example .env
```

`.env.example` 中最关键的变量：

| 变量 | 作用 | 示例 |
|---|---|---|
| `ADMIN_API_URL` | 本地工具与部署辅助所使用的 Admin 入口 | `https://central.myclaws.ai` 或你自己的 Admin 地址 |
| `ADMIN_DEPLOY_SSH_USER` | 远程安装 / 更新流程使用的 SSH 用户 | `deploy` |
| `ADMIN_DEPLOY_TARGETS` | 命名的部署目标列表 | `office:192.0.2.10,cloud:198.51.100.20` |
| `CLIENT_WEB_PORT` | 桌面端 Web 开发端口 | `5173` |
| `CLIENT_ELECTRON_DEV_PORT` | Electron 开发端口 | `5179` |

客户端品牌相关可选变量见 `./packages/client/.env.example`。

### 启动开发栈

```bash
# 终端 1
pnpm dev:admin

# 终端 2
pnpm dev:claw

# 终端 3
pnpm dev:client
```

移动端开发：

```bash
pnpm --filter @myclaws/mobile dev
```

如果你修改了共享类型，先构建：

```bash
pnpm --filter @myclaws/shared build
```

Admin 启动后，如需初始化示例 Agent：

```bash
pnpm seed:agents
```

## 构建与校验

```bash
pnpm build
pnpm verify:unit
```

更多运行与部署说明见 `./run_docs/README.md`。

## 安全模型与隐私

> 警告：MyClaws **不会**在 Agent 外额外套一层沙箱。

MyClaws 的设计是本地优先、自主管理。Claw 会直接以启动它的操作系统账号权限运行。这意味着你对该主机上的文件、工具和环境拥有完整控制权，同时也必须承担对应风险。

- 视情况使用独立低权限账号、隔离主机、容器或虚拟机。
- 把密钥放在环境变量或密钥管理器里，而不是 prompt 或聊天历史里。
- 启用自定义 Expert、skills、MCP server 或远端主机前先做审查。
- 把每一台接入的 Claw 主机都视为高权限执行环境。
- 个人、工作、生产环境尽量使用分离的凭据与存储边界。

这种模型更适合愿意自行管理运行风险的工程师和高级用户。

## 文档导航

- `./run_docs/README.md` — 公开运维与部署文档索引
- `./run_docs/claw-install-guide.md` — Linux / macOS / Windows 的 Claw 安装指南
- `./run_docs/admin-server-ops.md` — 面向公开部署的 Admin 运维模板
- `./dev_docs/project_knowledge/architecture.md` — 更深入的系统架构说明
- `./dev_docs/project_knowledge/project-context.md` — 项目上下文与设计原则
- `./dev_docs/index.md` — 完整开发文档索引

## 授权与商业使用说明

MyClaws 当前按 **public-source / source-available（公开源码 / 源码可见）** 方向发布。除非最终的 `LICENSE` 文件明确写成别的条款，否则**不要**默认把它理解为 OSI 定义下的开源软件。

当前授权方向如下：

- 个人、非商业使用计划为免费。
- 商业使用需要单独付费并取得书面授权。
- 二次开发后的再分发、商业托管或商业化衍生使用，同样需要单独付费授权，除非最终许可证文本另有说明。

当前公开草案见 `./LICENSE` 与 `./COMMERCIAL_LICENSE.md`。以上内容仅用于说明方向，不能替代最终许可证文本、单独签署的商业协议，也不构成法律意见。请在商业使用或衍生分发前让你的法律顾问审阅正式条款。

## 路线图

- 发布脱敏后的截图、简短演示和首次上手说明
- 完成法律复核，并落定公开发布所需的商业授权流程细节
- 完善自托管与多机器部署模板
- 增强可观测性、记忆工具和跨设备工作流
- 持续打磨公开发布自动化与文档体验

## 贡献

公开协作指南仍在整理中。现阶段建议：

- 先阅读 `./CONTRIBUTING.md`
- 较大功能改动先提 issue 沟通
- 提交 PR 时确保不包含 secrets、内部 IP 和私有运维细节

## 安全披露

如果你发现漏洞，请优先遵循 `./SECURITY.md` 里的私下披露流程。在正式披露流程建立前，不要把利用细节直接发到公开 issue。

## 致谢

MyClaws 站在整个 Agent 与开发工具生态之上，吸收了终端型编码 Agent、Electron、Expo、Vue、React Native、Fastify 以及现代 LLM 工具编排工作流中的许多经验与启发。
