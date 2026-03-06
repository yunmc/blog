# Project AIRI (moeru-ai/airi)

**仓库地址:** [https://github.com/moeru-ai/airi](https://github.com/moeru-ai/airi)  
**类别:** AI / 虚拟主播 (VTuber) / 桌面数字伴侣

## 简介
这是一个开源的 AI 虚拟主播与数字伴侣构建框架，核心目标是复刻知名 AI 主播 "Neuro-sama" 的体验。提供了一整套脚手架，支持跨平台运行（Web纯网页版、桌面“电子宠物”版、移动端）。

## 1. 核心运行架构 (Architecture)
整个项目是一个基于 **TypeScript + Vue 3** （前端与主逻辑），并结合了 **Electron**（桌面端封装）和 **Rust/WebAssembly**（高性能模块）构建的 Monorepo 工程。
- **Web First 原则:** 核心逻辑跑在浏览器/Webview 引擎之上。利用现代浏览器的 `WebGPU`、`Web Workers` 和 `WebAssembly(WASM)` 处理高并发推理、多线程任务和图像计算。
- **分级运行模式 (Stages):**
  - **Stage Web:** 纯网页浏览器运行版（轻量级体验）。
  - **Stage Tamagotchi:** “电子宠物”桌面版，采用 Electron/Vue 架构，支持解锁浏览器受限能力（如复杂的本地文件系统访问、全局透明窗口、系统底层服务）。
  - **Stage Pocket:** 移动端版本（基于 Capacitor 构建 iOS/Android 端）。

## 2. 核心技术机制 (生物器官隐喻)
- **大脑 (Brain - LLM & 记忆):** 
  - **多模型适配:** 采用自研的库（如 xsai）动态对接几乎市面上所有主流 LLM API。
  - **本地推理:** 利用 `WebGPU` 和开源库（如 Transformers.js）在显卡上直接跑小参数模型，实现离线大脑。
  - **本地记忆:** 利用基于 WebAssembly 的本地数据库 (DuckDB WASM / pglite) 结合 Drizzle ORM，构建纯本端的 RAG (检索增强生成) 向量记忆系统，实现长短期记忆。
- **听觉 (Ears - VAD & STT):** 监听系统麦克风或 Discord 音频流；客户端进行 VAD 人声检测，结合 Whisper 及其端侧模型将语音转文本。
- **嘴巴 (Mouth - TTS):** 大模型返回文本时触发文本转语音引擎（如 ElevenLabs, 本地部署的 VITS 等）。
- **身体 (Body - 渲染与动画):** 利用 `Three.js` (处理 3D) 和 `Live2D SDK` 驱动。基于 TTS 的音频频域进行基于音频的嘴型同步（Lip Sync），并通过代码注入程序化的待机动画（自动眨眼、视线跟随）。

## 3. 全链路 Agent 指令执行流 (总结)
AIRI 的本质是一个打通感官的 Agent 平台。其工作流大致为：
接收输入（麦克风/游戏事件/聊天文本） $\rightarrow$ VAD+STT 转成 Prompt $\rightarrow$ 交给搭载记忆数据库的 LLM 规划 $\rightarrow$ LLM 生成带有特殊 Tag 的动作流（说话内容 + 游戏指令） $\rightarrow$ 客户端分发执行（触发 TTS 发声、驱动 Live2D 嘴型、发送网络协议控制游戏角色）。

## 4. 游戏实现原理 (Minecraft / Factorio)
并非基于电脑画面的截图视觉判断，而是基于**数据驱动与底层 API**：
- 把复杂的游戏抽象成“带有海量状态数据的策略环境”。
- 通过机器人操作框架（如 Mineflayer）或远程服务端协议（如 Factorio RCON）读取游戏后台的实体坐标和参数。
- 转化为文本输入给 AI，AI 推理后输出动作大纲，底层再将其解析为游戏内的指令或通信包。

## 5. 美术与 UI/UX 环节
这套框架只负责“驱动”，**皮套和软件交互仍极度依赖人类设计师**：
- **美术资产:** 画师绘制极为精细的图层拆分图 $\rightarrow$ Live2D 建模师进行网格与骨骼绑定 $\rightarrow$ 输出模型文件供 AIRI 读取。
- **UI/UX 设计:** 跨平台客户端的应用界面设计、桌面悬浮人偶的触摸交互反馈、以及直播间的各类视觉包装面板（弹幕框、游戏遮罩等）。

## 6. 同类开源竞品参考 (Similar Projects)
除了 AIRI 之外，开源社区还有其它尝试实现类似逻辑的优秀项目，可作为横向对比研究：
- **[kimjammer/Neuro](https://github.com/kimjammer/Neuro):** 7天内原汁原味的 Neuro-Sama 基础复刻。
- **[semperai/amica](https://github.com/semperai/amica/):** 侧重于 VRM (3D) 模型与 WebXR 体验。
- **[elizaOS/eliza](https://github.com/elizaOS/eliza):** 极佳的通用 Agent 框架，侧重于系统和 API 级别的深度集成，代码工程化水平很高。
- **[ardha27/AI-Waifu-Vtuber](https://github.com/ardha27/AI-Waifu-Vtuber):** 偏向于与 Twitch/YouTube 直播接口深度集成的实战打法。
