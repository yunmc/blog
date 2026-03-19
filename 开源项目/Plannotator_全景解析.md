# Plannotator (for Pi) 全景解析

## 项目简介

**Plannotator for Pi** (`apps/pi-extension`) 是专门为 **[Pi 编程助手 (Pi coding agent)](https://github.com/mariozechner/pi)** 开发的一个扩展插件。

它的核心功能是为 AI 编程助手引入**“基于文件的计划模式（Plan mode）”**以及配套的**可视化浏览器 UI**，让用户可以更安全、直观地审核和指导 AI 的工作。

简单来说，这是一个**让你可以先让 AI 写好“施工计划书”，等你可视化审查并批准后，再让 AI 真正动手的安全管控扩展工具**。

## 核心功能与特点

### 1. 计划模式 (Plan Mode)
- 限制 AI 的破坏性操作（例如限制不能随意修改代码，只能写入计划文件）。
- AI 会先检索代码库，并以 Markdown 待办列表（Checklists）的形式写下一份操作计划（Plan）。

### 2. 可视化浏览器 UI 审核
- AI 完成计划输出后，会在浏览器中打开 Plannotator UI 界面。
- 用户可以在 UI 中可视化地审查计划，并做出决定：
  - **Approve (直接批准)**：同意计划，让 AI 开始执行。
  - **Deny with annotations (拒绝并批注)**：发送结构化反馈退回给 AI 重新修改计划。
  - **Approve with notes (带备注批准)**：同意计划，但包含额外的实现指导。

### 3. 安全执行与进度追踪
- 当用户批准计划后，AI 会进入“执行模式”，获取完整的工具权限（读写文件、执行 Bash 等）开始写代码。
- 执行期间，AI 会通过 `[DONE:n]` 标记已完成的步骤。
- 终端状态栏会实时显示进度，包含一个可视化的待办事项清单小部件。

### 4. 代码审查与 Markdown 批注
- **代码审查 (Code review)**：提供可视化代码审查工具，允许对当前 Git 变更（未提交、已暂存、上一次提交、分支差异等）进行详细的代码单行批注，并将反馈发送给 AI。
- **文档审查 (Markdown annotation)**：支持打开任意的 Markdown 文件（如开发文档或需求规格等）进行可视化批注，并与 AI 一起讨论。

## 核心工作流状态机

扩展插件在底层管理着一个状态机：
**idle (空闲)** → **planning (规划中)** → **executing (执行中)** → **idle (空闲)**。

- 在**规划中 (planning)**：AI 被系统提示词限制，虽然能用终端 bash 命令和其它工具探查信息，但不能执行破坏性指令，只能编辑当前计划文件。
- 在**执行中 (executing)**：AI 拥有全部修改、读取、写入和终端执行权限。每执行一轮会重新读取磁盘上的 Plan 文件保持状态同步。

# Pi、Plannotator、Claude Code、Codex CLI 对比

## 一句话概括

- **Pi**：一个可扩展的终端 AI 编程代理底座
- **Plannotator**：装在 Pi 上的“先计划、再审批、再执行”扩展
- **Claude Code**：Anthropic 官方的终端 AI 编程代理
- **Codex CLI**：OpenAI 的终端编程代理 / CLI 工具

---

## 它们分别是什么

### Pi
Pi 可以理解为一个运行在终端里的 AI 编程代理框架。  
它负责接收任务，并调用读文件、写文件、编辑代码、执行命令等能力来完成开发工作。

它更像一个“底座”：
- 核心能力简洁
- 支持扩展
- 适合按自己的工作流去定制

### Plannotator
Plannotator 不是独立的 coding agent，而是 **Pi 的扩展**。  
它给 Pi 增加了一套更可控的流程：

1. AI 先生成计划
2. 用户审阅计划
3. 用户批准或提出修改意见
4. AI 再根据批准后的计划执行代码修改

所以，Plannotator 的重点不是“替代 Pi”，而是“让 Pi 的执行过程更透明、更可控”。

### Claude Code
Claude Code 是 Anthropic 官方提供的终端 AI 编程工具。  
它本身就是一个完整产品，不依赖 Pi。

特点通常是：
- 开箱即用
- 面向终端开发场景
- 不需要自己先搭一个代理底座

### Codex CLI
Codex CLI 是 OpenAI 提供的终端编程代理 / 命令行工具。  
它同样是一个独立产品，不依赖 Pi。

它和 Claude Code 类似，属于“直接可用的整机”，而不是“先搭底座再加扩展”的路线。

---

## 它们之间的关系

最容易理解的方式是：

- **Pi**：底座
- **Plannotator**：Pi 的计划审批插件
- **Claude Code**：独立成品
- **Codex CLI**：独立成品

也就是说：

- **Pi + Plannotator** 是一套“可扩展底座 + 审批插件”的组合
- **Claude Code / Codex CLI** 是直接拿来用的完整工具

---

## 对比表

| 名称 | 本质 | 主要用途 | 是否依赖 Pi | 适合谁 |
|---|---|---|---|---|
| **Pi** | 编程代理框架 / CLI | 读写代码、跑命令、接扩展 | 否 | 想自己定制工作流的人 |
| **Plannotator** | Pi 的扩展 | 先计划、审阅批准、再执行 | 是 | 想让 AI 改代码前先给方案的人 |
| **Claude Code** | 独立终端 AI 编程工具 | 直接在终端完成开发任务 | 否 | 想快速上手、少折腾的人 |
| **Codex CLI** | 独立终端 AI 编程工具 | 直接在终端完成开发任务 | 否 | 想直接使用成品工具的人 |

---

## 更通俗的类比

可以把它们类比成：

- **Pi** = 一台可以加模块的“开发机器人主机”
- **Plannotator** = 装在这台主机上的“审批面板”
- **Claude Code** = 一台已经组装完成的整机
- **Codex CLI** = 另一台已经组装完成的整机

这个类比下就很清楚：

- Pi 负责“能干活”
- Plannotator 负责“干活前先报计划、给你审批”
- Claude Code 和 Codex CLI 负责“直接开工”

---

## 什么时候选哪个

### 适合选 Pi 的情况
如果你希望：
- 工作流可定制
- 能自己装扩展
- 更关注可扩展性和灵活性

那么 Pi 更合适。

### 适合选 Pi + Plannotator 的情况
如果你希望：
- AI 不要直接改代码
- 先给你实施计划
- 你确认后再执行
- 团队协作里更强调可控性和审阅流程

那么 Pi + Plannotator 更合适。

### 适合选 Claude Code 或 Codex CLI 的情况
如果你希望：
- 工具尽快上手
- 少配置、少拼装
- 直接进入开发流程

那么 Claude Code 或 Codex CLI 更合适。

---

## 总结

这几个工具不是同一层级的东西：

- **Pi** 是底座
- **Plannotator** 是 Pi 的扩展
- **Claude Code** 和 **Codex CLI** 是独立的完整工具

所以，准确地说：

- **Plannotator 不是 Pi 的替代品**
- **Plannotator 是 Pi 的增强组件**
- **Claude Code / Codex CLI 则是另一条“直接提供完整体验”的路线**

如果只记一句话，可以记成：

> **Pi 是底座，Plannotator 是 Pi 的计划审批插件，Claude Code 和 Codex CLI 是独立成品。**