# Garden Skills

**地址**: [https://github.com/ConardLi/garden-skills](https://github.com/ConardLi/garden-skills)  
**作者**: ConardLi  
**License**: MIT

## 简介
这是一个面向 AI Agent 的开源 Skill 集合仓库。它不是单一功能插件，而是一组可安装的 `SKILL.md` 技能包，适用于 Claude Code、Cursor、Codex 等支持 Skill 机制的代理环境。

## 核心内容
1. **Skill 仓库，而不是单个 Skill**：
   提供多个可独立安装的 Agent Skill，用户可以整仓安装，也可以按需只装某一个技能。

2. **当前包含 4 个代表性 Skill**：
   - `web-video-presentation`：把文章、口播稿、课程内容做成适合录屏的视频演示网页。
   - `web-design-engineer`：提升 AI 生成网页/UI 时的设计质量和工程约束。
   - `gpt-image-2`：围绕 GPT Image 2 的出图、修图和提示词工程工作流。
   - `kb-retriever`：从本地知识库中检索 Markdown、PDF、Excel 等内容，并基于证据回答。

3. **安装方式完整**：
   支持 `npx skills add`、Claude Code 插件市场、GitHub Releases `.zip`、手动拷贝和 Git Submodule 等多种接入方式。

4. **强调可移植的 Skill 标准**：
   以 `SKILL.md` 为核心，配合 `references/`、`scripts/`、`assets/` 等目录组织 Agent 能力，兼容多个支持 Skill 规范的 Agent。

## 常用方式
```bash
npx skills add ConardLi/garden-skills
```

如果只安装其中一个 Skill：

```bash
npx skills add ConardLi/garden-skills -s kb-retriever
```

## 适用场景
适合希望给 AI Agent 增加“专项能力模块”的用户。与其反复手写提示词，不如把成熟的方法论、工作流和参考材料打包成 Skill，让 Agent 在特定任务里按需加载，例如网页设计、演示录屏、图像生成和本地知识库检索。