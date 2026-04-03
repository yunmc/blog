# BMAD 框架全景解析

## 1. 项目简介

**BMAD** (Breakthrough Method for Agile Ai Driven Development，敏捷 AI 驱动开发的突破性方法) 是在当前 AI 辅助编程领域（Vibe Coding）兴起的一套**结构化的研发方法论和工程框架**。它旨在解决大模型在复杂软件项目中容易丢失上下文、产生幻觉和系统架构偏移的核心痛点。

BMAD 不再依赖开发者直接与 AI 进行无序的“闲聊式”写代码，而是强调**文档驱动 (Document-Driven)** 和 **多 Agent 协同 (Multi-Agent Orchestration)** 的工作流。通常通过生成标准化的 Markdown 配置（如架构决策 ADR、前置设计方案、开发 Plan）作为 AI 理解业务意图的第一入口。

## 2. 核心特点
- **静态化架构对齐**：通过预先设定的指导文档引导大语言模型的输出环境，限制代码的边界条件。
- **角色化编排**：利用 Boss (Planner) 和 Worker (Coder) 等角色的分工，避免单个 Agent 负担过多上下文。
- **生态与工具链**：目前开源社区发展出了一系列围绕 BMAD 的技能包（Skills）和工作流管道，能直接接入诸如 Claude Code、Cursor 等原生工具中。流管道，能直接接入诸如 Claude Code、Cursor 等原生工具中。
