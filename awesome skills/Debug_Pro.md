# Debug Pro

**地址**: [https://clawhub.ai/cmanfre7/debug-pro](https://clawhub.ai/cmanfre7/debug-pro)  
**作者**: cmanfre7  
**License**: MIT-0

## 简介
这是一个系统化的调试指南（Instruction-only 技能）。它的核心目的是为 AI Agent 提供一套标准的 Debug 方法论和多语言排错指令，帮助 Agent 更系统、高效地为你解决代码 bug。

## 核心内容
1. **7步调试协议 (The 7-Step Debugging Protocol)**：
   - 1. **重现 (Reproduce)**：确保稳定复现。
   - 2. **隔离 (Isolate)**：缩小问题范围。
   - 3. **假设 (Hypothesize)**：提出可测试的推测。
   - 4. **介入 (Instrument)**：添加日志或断点。
   - 5. **验证 (Verify)**：确认根本原因。
   - 6. **修复 (Fix)**：应用最小正确修复。
   - 7. **回归测试 (Regression Test)**：编写测试以防再次发生。

2. **特定语言调试指南**：
   内置 JavaScript/Node.js, Python, Swift, CSS, 网络(Network)及 Git Bisect 的快捷调试命令和小抄。

3. **常见错误模式 (Common Error Patterns)**：
   针对 `Cannot read property of undefined`、`ENOENT`、`CORS error` 等经典高频报错提供检查清单和修复思路。

4. **服务器和进程级诊断**：
   包含了 `lsof`, `ps`, `fswatch`, `top` 等高阶系统命令的快速使用。

## 适用场景
为 AI 安装该技能后，它在排查 bug 时将遵循严谨的排错逻辑，而不是盲目尝试。相当于给 AI 装备了一份高级研发工程师的 Debug 备忘录。