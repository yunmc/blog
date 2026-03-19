# Self-Improving Agent

**地址**: [https://clawhub.ai/xiucheng/xiucheng-self-improving-agent](https://clawhub.ai/xiucheng/xiucheng-self-improving-agent)  
**作者**: xiucheng  
**License**: MIT-0

## 简介
为 AI Agent 提供自我反思和持续优化能力的技能（Skill）。它的核心功能是在每次对话后自动分析对话质量，识别可改进的地方，并持续优化其回复策略。所有操作均在本地完成，通过读写日志文件来实现，安全无害。

## 核心特性 (Features)
1. **质量分析 (Quality Analysis)**：评估对话的有效性。
2. **改进追踪 (Improvement Tracking)**：找出需要提升和改进的方向。
3. **学习日志 (Learning Log)**：将获得的洞察和经验教训记录到本地 `improvement_log.md` 中。
4. **生成报告 (Weekly Reports)**：可自动生成每周的改进总结报告。
5. **策略优化 (Strategy Optimization)**：随着时间的推移不断调整和适应，提供更好的回复模式。

## 适用场景
适合希望通过“复盘”机制，让 AI 每天与自己的对话质量越变越好、越来越符合个人偏好的用户。推荐结合 `memory-manager` 以及自定义的 `SOUL.md`（人格设定文件）一起使用。