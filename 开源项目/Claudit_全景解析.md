# Claudit 全景解析

**GitHub Repository**: [quickstop/plugins/claudit](https://github.com/acostanzo/quickstop/tree/main/plugins/claudit)

`claudit` 是一个为 **Claude Code** 提供的审计与优化插件。它的主要作用是**全面检查和评估你的 Claude Code 配置是否标准、高效**。它就像是 Claude Code 的“体检仪”和“优化师”。

## 核心功能与特点

1. **自动扫描配置**
   它会自动查找你所有的 Claude 配置文件（包括全局的 `CLAUDE.md`、项目目录下的规则、`.claude/settings.json`、MCP 服务器、插件配置等）。

2. **检测过度设计 (Over-engineering)**
   它会发现你配置里哪些部分过于臃肿或多余（比如冗长的提示词或滥用的 MCP 工具），因为过分复杂的配置反而会降低 Claude 的运行效率。

3. **结合官方文档打分**
   它会在后台自动检索 Anthropic 的最新官方文档，然后根据最佳实践给你的配置打分（从 A+ 到 F），并细分为过度设计、配置质量、安全状况、上下文效率等 6 个维度。

4. **一键修复与提 PR**
   它不仅能指出问题，还能为你提供修改建议。如果你在 Git 仓库中，它甚至能自动生成修复代码并提交带有详细教学注释的 Pull Request，教你或你的团队如何更好地使用 Claude Code。

5. **智能上下文感知**
   如果你在代码仓库内运行，它会同时审计项目配置和全局配置的冲突；如果在外部运行，它则只专注于全局设置。

## 适用场景

当你在系统中重度使用 Claude Code，配置了大量规则 (`CLAUDE.md`) 或 MCP 服务器，感觉响应变慢或效果不佳甚至产生上下文资源浪费时，可以通过它运行 `/claudit` 命令来一键排查、精简和优化配置。
