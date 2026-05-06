# AI 编程 API 中转服务调研

> 文档类型：归档长稿
> 最近更新：2026-05-06
> 用途：保留早期合并调研稿；正式入口请优先查看 [AI中转服务索引](AI中转服务索引.md)，新增平台与专题分析优先维护独立文档。
> 原始调研区间：2026-03-13 ~ 2026-03-14，2026-04-19 补充 OpenRouter，2026-04-21 补充 ChatFire
> 调研对象：AICodeWith、CodeCMD、AICodeMirror、哈基米 AI、OpenRouter、ChatFire 及同类平台

已拆分文档包括：OpenRouter、ChatFire、DeRouter、PPToken、APIMart、OmniXAI、AICodeWith、CodeCMD、AICodeMirror、哈基米AI、BeefAPI，以及两个专题文档。

## 一、行业背景

由于网络和支付限制，国内开发者无法直接使用 Anthropic Claude API / OpenAI Codex API 等海外 AI 编程服务。催生了一批 **API 中转/镜像平台**，通过反向代理 + 国内加速 + 人民币支付，为国内开发者提供接入通道。

核心模式：

```
国内用户 → 中转平台（国内加速节点）→ 海外服务器 → Anthropic / OpenAI 官方 API
```

---

## 二、平台详细分析

### 2.1 AICodeWith

| 项目 | 信息 |
|------|------|
| 网址 | https://aicodewith.com |
| 文档站 | https://docs.aicodewith.com |
| 定位 | AI 编程 API 聚合中转平台 |
| 信息丰富度 | ★★★★★（文档完善，社区讨论多） |

**核心功能：**
- API 中转：提供 Claude API 国内镜像端点，CDN 加速，声称延迟 < 50ms，99.9% 在线率
- 统一额度池（核心卖点）：一个 API Key 可跨 Claude、Codex 及所有未来模型使用
- 多工具支持：Claude Code、OpenAI Codex CLI、OpenCode
- 全系列模型：Claude Sonnet / Opus / Haiku
- OpenCode 插件：提供 `opencode-aicodewith-auth` 认证插件，支持多模型路由

**定价套餐：**

| 套餐 | 价格（CNY） | 目标用户 |
|------|------------|---------|
| 入门版 | ¥399 | 轻度用户 / 个人项目 |
| 专业版 | ¥699 | 中度用户 / 专业开发者 |
| 旗舰版 | ¥1799 | 重度用户 / 企业级 |

**计费特点：**
- 积分永不过期，按量付费
- 跨模型通用（Claude / Codex 共享额度）
- 人民币支付，无需虚拟信用卡
- 声称通过渠道折扣，价格远低于官方

**Dashboard 功能：**
1. 欢迎页 — 新手引导和快速开始
2. API Key 管理 — 创建、查看、删除密钥
3. 用量统计 — 积分消耗、调用次数、模型分布
4. 套餐购买 — 充值积分
5. 配置教程 — 各工具的接入指南
6. 调用记录 — 历史请求日志

**接入方式：**

```bash
# Claude Code 接入
export ANTHROPIC_BASE_URL="https://[中转地址]"
export ANTHROPIC_API_KEY="your-api-key"
```

OpenCode 通过插件认证，按模型前缀路由：
- `claude-*` → Anthropic API
- `gpt-*/codex-*` → OpenAI API
- `gemini-*` → Google API

---

### 2.2 CodeCMD

| 项目 | 信息 |
|------|------|
| 网址 | https://codecmd.com |
| Dashboard | https://codecmd.com/dashboard |
| 定位 | Claude Code API 中转服务 |
| 信息丰富度 | ★★☆☆☆（公开信息较少） |

**已知信息：**
- 提供 Claude Code API 中转服务
- 有 Dashboard 控制台（/dashboard 路径），说明具备用户管理和用量统计功能
- 属于较新或较小的中转平台，网上讨论和评测较少
- 具体定价、套餐、功能细节未能从公开渠道获取

**待确认（需注册体验）：**
- [ ] 具体定价套餐
- [ ] 支持的模型范围（是否支持 Codex / OpenCode）
- [ ] 计费方式（按量 / 订阅）
- [ ] 用户评价和稳定性
- [ ] 是否有中文文档

---

### 2.3 AICodeMirror

| 项目 | 信息 |
|------|------|
| 网址 | https://www.aicodemirror.com |
| 定位 | Claude Code 镜像服务 |
| 信息丰富度 | ★★☆☆☆（公开信息较少） |

**已知信息：**
- 从域名推断，定位为 "AI Code Mirror"（AI 代码镜像）
- 属于 Claude Code 镜像/中转类服务
- 网上讨论和评测较少，属于较新平台

**同类参考 — "CC Mirror" 生态：**

"CC Mirror"（Claude Code Mirror）是国内社区对此类中转服务的统称。类似平台还有：
- LumeCoder（lumecoder.com）— 声称提供稳定的 Claude Code 镜像访问，透明计费
- AICodeEditor（aicodeditor.com）— 面向开发团队，强调安全合规
- ClaudeCodeAPI（claudecodeapi.com）— ¥1.2 = $1 USD 积分兑换，月套餐 $99 起

**待确认（需注册体验）：**
- [ ] 具体定价套餐
- [ ] 支持的模型和工具
- [ ] 与其他平台的差异化
- [ ] 用户评价和稳定性

---

### 2.4 哈基米 AI (aipro.love)

| 项目 | 信息 |
|------|------|
| 网址 | https://vip.aipro.love |
| 文档站 | https://docs.aipro.love |
| 定位 | OpenAI/Anthropic/Gemini 全协议 AI API 中转服务 |
| 信息丰富度 | ★★★★☆（文档完善，提供一键接入工具） |

**已知信息：**
- API 中转：同时兼容 OpenAI、Anthropic、Gemini 三种协议，一个 Key 通用。
- 全系列模型：支持 GPT-5/5.4/Codex、Claude 4.6（支持 1M 上下文及 Thinking 推理）、Gemini 3.1 系列。
- 配套工具：提供专属 "哈基米一键配置工具（Switch）"，方便 Windows/macOS/Linux 用户快速接入。
- 计费与验证：拥有时间限制套餐（日卡/周卡）以及普通调用，提供免费查询可用模型接口（不耗额度）。

**接入方式：**
- **OpenAI 协议**: `https://vip.aipro.love/v1`
- **Anthropic 协议**: `https://vip.aipro.love`（工具自动拼接）
- **Gemini 协议**: `https://vip.aipro.love`（工具自动拼接 `/v1beta`）

---

### 2.5 OpenRouter

| 项目 | 信息 |
|------|------|
| 网址 | https://openrouter.ai |
| 文档站 | https://openrouter.ai/docs |
| 定位 | 全球化 LLM 聚合网关 / Unified Interface for LLMs |
| 信息丰富度 | ★★★★★（产品、文档、计费、路由和企业能力公开极其充分） |

**已知信息：**
- 一个 API 和一个账单体系接入 300+ 模型、60+ Provider；官网 `Providers` 页面当前展示 67 家 provider。
- 对外强调 "One API, One Bill, Every AI Provider"，更像模型网关 / 控制平面，而不只是单一 API 中转。
- 兼容 OpenAI 风格接口，也提供自己的 SDK、Request Builder、模型页、Provider 页、状态页和 Apps / Rankings 生态页。
- Credits 可跨模型与 provider 使用，支持 Free / Pay-as-you-go / Enterprise 分层。

**核心能力：**
- 自动路由与故障切换：默认按价格 + 最近 30 秒稳定性做负载均衡，失败后自动 fallback 到其他 provider。
- 精细 provider 控制：支持 `sort`、`order`、`only`、`ignore`、`allowFallbacks`、`maxPrice`、`requireParameters` 等按请求级别的路由策略。
- 性能优先选路：支持按 `price` / `throughput` / `latency` 排序，也支持基于 `p50` / `p90` / `p99` 的吞吐和时延偏好。
- 数据策略控制：支持 `dataCollection: "deny"`、`zdr: true`、账户级 privacy settings，并公开展示各 provider 的 retention / training policy。
- 企业能力：支持统一报告、Key 级预算控制、SSO、发票、SLA、EU 区域内路由、BYOK / Bring your own capacity。

**定价与计费特点：**
- Free：25+ 免费模型、4 个免费 provider、50 请求/天。
- Pay-as-you-go：300+ 模型、60+ provider、无最低消费，平台费 5.5%。
- BYOK：Pay-as-you-go 下每月 1M 请求免费，之后 5% fee；Enterprise 提供更高免费额度和定制方案。
- Enterprise：支持 bulk discount、对公 invoicing、专属限额与合规能力。

**对我们最值得看的点：**
- 它把“路由策略”本身做成了产品能力，而不是只在后端偷偷切 provider。
- 它把“隐私 / 数据保留 / 是否训练”做成了可见、可配置的控制面板。
- 它同时服务个人开发者与企业采购，产品壳明显比普通中转站更厚。
- 详细拆解见 [OpenRouter_全景解析](OpenRouter_全景解析.md)。

---

### 2.6 ChatFire / ChatfireAPI

| 项目 | 信息 |
|------|------|
| 网址 | https://api.chatfire.cn / https://chatfire.cn |
| 文档站 | https://api.chatfire.cn/doc / https://docs.newapi.pro/ |
| 定位 | 国内多模型 API 聚合中转 / UnifiedLLM API Gateway |
| 信息丰富度 | ★★★★☆（价格页和营销页信息很多，但 About / Doc 内容不够统一） |

**已知信息：**
- 同时存在两套公开入口：`api.chatfire.cn` 偏营销站 + 价格展示站，`chatfire.cn` 偏 NewAPI 风格控制台与模型市场。
- 首页直接强调 OpenAI 兼容，只需替换 Base URL，可接 `/v1/chat/completions`、`/v1/responses`、`/v1/messages`、`/v1beta/models`、`/v1/embeddings`、`/v1/rerank` 以及图片、音频等接口。
- `api.chatfire.cn/pricing` 当前公开 855 个模型 / SKU，覆盖 OpenAI、Anthropic、Gemini、Moonshot、DeepSeek、Qwen、xAI、Vertex，以及大量图像、视频、OCR、搜索、TTS、Midjourney、Kling 等能力。
- 站点公开宣称按量付费、余额不过期、兼容多数开源聊天应用、支持对公与发票、支持模型定制化服务。

**产品特征：**
- 更像“大 SKU 渠道池 + 统一计费面板”，而不是只卖某一家模型的镜像入口。
- 渠道分层信息外露较多：价格页可见 Tier、渠道分组、token group、按次计费、默认通道 / 企业通道等运营痕迹。
- `chatfire.cn/about` 公开显示其现代控制台基于 [QuantumNous/new-api](https://github.com/QuantumNous/new-api)，并注明 Based on One API v0.5.4，说明它很可能是基于开源聚合网关做了较重运营层封装。
- 详细拆解见 [ChatFire_全景解析](ChatFire_全景解析.md)。

**值得注意：**
- 首页宣称“提供公开日志”，但 `https://api.chatfire.cn/uptime` 当前返回 404，监控透明度需要人工复核。
- `api.chatfire.cn` 与 `chatfire.cn` 两套前台形态差异较大，可能是旧站与新站并存，也可能是不同业务入口，需要后续手动注册验证。

---

## 三、平台横向对比

| 维度 | AICodeWith | CodeCMD | AICodeMirror | 哈基米 AI | OpenRouter | ChatFire |
|------|-----------|---------|-------------|----------|------------|----------|
| 定位 | 国内 AI 编程聚合中转 | Claude Code 中转 | Claude Code 镜像 | 全协议 AI API 中转 | 全球 LLM 聚合网关 | 国内多模型聚合网关 |
| 文档完善度 | ★★★★★ | ★★☆☆☆ | ★★☆☆☆ | ★★★★☆ | ★★★★★ | ★★★☆☆ |
| 公开信息量 | 丰富 | 较少 | 较少 | 丰富 | 极丰富 | 丰富 |
| 多模型支持 | Claude + Codex + Gemini | 待确认 | 待确认 | GPT-5 + Claude 4.6 + Gemini 3.1 | 300+ models | 855 公开模型 / SKU |
| 统一额度 / 账单 | ✅ | 待确认 | 待确认 | ✅ | ✅ credits + unified billing | ✅ |
| 路由控制能力 | 基础模型前缀路由 | 待确认 | 待确认 | 协议兼容为主 | ★★★★★ 请求级 provider routing | 中等，偏后台渠道分组 |
| 数据策略 / 隐私 | 未突出 | 待确认 | 待确认 | 未突出 | ★★★★★ ZDR / data policy / EU routing | 未突出 |
| 企业能力 | 一般 | 待确认 | 待确认 | 一般 | SSO / SLA / invoicing / reporting | 对公 / 发票 / 定制服务 |
| 入门方式 | ¥399 套餐 | 待确认 | 待确认 | 日卡 / 周卡 / 按量 | Free + Pay-as-you-go | 按量付费 |
| 中文文档 | ✅ 完善 | 待确认 | 待确认 | ✅ 完善 | ❌ 以英文为主 | ✅ |

---

## 四、更多同类竞品

| 平台 | 网址 | 特点 |
|------|------|------|
| OpenRouter | openrouter.ai | 海外模型聚合网关，强在 provider routing、BYOK、ZDR、企业能力 |
| ChatFire | api.chatfire.cn / chatfire.cn | 国内多模型大 SKU 网关，OpenAI 兼容强，明显带 NewAPI / OneAPI 运营中台特征 |
| 仟里码 | 1kcode.cn | Claude Code 国内使用方案 |
| LumeCoder | lumecoder.com | CC Mirror，透明计费 |
| AICodeEditor | aicodeditor.com | 面向企业，强调合规 |
| ClaudeCodeAPI | claudecodeapi.com | ¥1.2 = $1 积分，月套餐 $99 起 |

---

## 五、OpenRouter 给我们的启发

1. **中转站不应该只卖“代理地址”**
	更高阶的做法，是把多模型、多 provider、多协议、多账单、多策略统一成一个“控制面”。

2. **路由透明要产品化**
	不只是后台有路由逻辑，而是要让用户看见：请求模型、实际 provider、实际模型、fallback 原因、扣费明细。

3. **隐私与数据策略要前置**
	`只走 ZDR`、`禁止训练`、`限定 provider`、`限定地区` 这类能力，不应该是企业定制口头承诺，而应该是产品参数和后台配置项。

4. **企业能力是第二增长曲线**
	SSO、统一报告、预算控制、发票和 SLA，不是锦上添花，而是从开发者工具升级到企业网关的关键分水岭。

5. **公开透明本身就是护城河**
	模型页、Provider 页、排名页、状态页、文档细节，这些都会提高信任度，也会减少“黑盒中转站”的不确定感。

---

## 六、中转平台低价原因揭秘

搞中转服务（API 代理 / 镜像）之所以能提供远低于官方的“破盘价”“白菜价”，背后通常隐藏着一套复杂的“供应链”和运营逻辑。概括来说，低价来源主要分为正规渠道的成本差异、灰产 / 黑产的零成本倒卖，以及技术层面的“降本增效”（掺水）。

### 6.1 灰产与黑产（零 / 极低成本）

- **满月号 / 试用金白嫖（薅羊毛）**：利用自动化脚本批量注册新账号，获取官方（如 OpenAI、Anthropic 等）的新手免费额度。额度用完即封号再换，成本仅限于接码平台和动态 IP。
- **黑卡 / 盗刷（Carding）**：使用被盗的信用卡或虚拟信用卡批量绑定开发者账号，疯狂调用 API，直到被风控或拒付。期间产生的 Token 几乎是零成本。
- **买卖盗号的 API Key**：黑客通过 GitHub 泄露、木马等手段盗取企业或个人的真实 API Key，低价批发给中转服务商。

### 6.2 技术层面的“降本增效”（掺水与造假）

这是部分低价中转平台最常见的盈利手段：

- **模型造假（挂羊头卖狗肉）**：用户请求最贵的模型（如 `gpt-4` 或 `claude-3-opus`），网关后台却路由到极便宜的模型（如 `gpt-3.5` 或 `claude-3-haiku`），甚至是免费开源模型，借此赚取几十倍差价。
- **缓存命中（Semantic Caching）**：短时间内有多位用户问类似问题时，中转网关直接返回 Redis 里的历史响应，并未真正请求官方 API。
- **系统级 Prompt 截断**：偷偷删减上下文历史或过长的 System Prompt，减少向官方发送的 Input Tokens。

### 6.3 企业级批量折扣与逆向工程

- **企业大客户折扣**：大型中转商跟官方或 Azure 签下大客户协议获取批发折扣，转卖给散户赚取差价。这种降价空间通常有限，但最稳。
- **逆向网页版（Token 转换）**：官方网页版（如 ChatGPT Plus 或 Claude Pro）通常包含大量额度。技术团队通过逆向网页版协议包装成标准 API 格式，只需花 20 刀买个账号，就能高并发产出极大价值的 API Token，边际成本极低。

### 6.4 商业模式考量（烧钱引流）

- **庞氏骗局 / 资金盘模式**：前期亏本甩卖（“API 刺客”）吸引大量充值，资金池攒够后直接关站跑路（“灵车”）。
- **引流其他业务**：通过低价 API 中转作为 Loss Leader（亏损引流品），吸引开发者后推销云服务器、海外虚拟卡等高利润服务。

### 小结

如果遇到价格在官方正常指导价 5 折以下的 API（且未明确声明是逆向渠道），大概率存在掺水（换模型）、逆向网页版不稳定，或者是在消耗黑卡额度（随时可能跑路）。生产环境过度追求“极低价格”，往往会带来极高的业务中断风险和输出质量降级。

---

## 七、商业模式分析（通用）

### 收入模型

```
用户充值（人民币）→ 平台 → 以批量/渠道价调用官方 API（美元）→ 赚取差价
```

### 成本结构
- API 调用成本（最大头）
- 海外服务器 / CDN 加速
- 运维和客服人力
- 支付通道手续费

### 竞争壁垒
- **较低**：技术门槛不高，核心就是反向代理 + 计费
- 主要靠用户体验、价格、稳定性和先发优势

### 风险

| 风险 | 说明 |
|------|------|
| 官方封禁 | Anthropic/OpenAI 可能禁止第三方转售，封禁 API Key |
| 合规风险 | AI 内容审核、数据出境、经营许可 |
| 汇率波动 | 人民币收入 vs 美元支出 |
| 竞争激烈 | 门槛低，同质化严重，价格战 |
| 上游涨价 | 官方 API 调价直接影响利润 |

---

## 八、总结

这类平台可以粗分为两类：

1. **国内编程 API 中转 / 镜像平台**：重点解决访问、支付、中文支持和工具接入问题。
2. **全球模型聚合网关**：以 OpenRouter 为代表，重点解决 vendor lock-in、路由策略、统一账单、隐私治理和企业级可观测问题。

它们共同的核心价值包括：

1. 解决多上游接入复杂度
2. 解决支付、额度和账单碎片化问题
3. 提供统一文档、统一 Key 和统一控制台
4. 在更成熟的平台上，进一步提供路由、治理、日志和企业能力

**选择建议：**
- 如果已有海外支付和网络条件，且只需要单一模型 → 直接用官方 API 最可靠。
- 如果目标用户是国内个人开发者 → AICodeWith、哈基米 AI 这类产品更直接可比。
- 如果目标是做“国内多模型聚合面板 + 大量渠道 / SKU 运营” → ChatFire 值得重点观察。
- 如果目标是做“统一模型网关产品”而不是“代理地址” → OpenRouter 是更值得重点拆解的 benchmark。
- 我们自己的中转站，不应该只学“便宜”，更应该学 OpenRouter 的透明、控制和产品化能力。
