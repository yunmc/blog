# AI中转服务索引

> 文档类型：目录索引
> 最近更新：2026-05-06
> 用途：这是 `中转服务/三方中转站产品调研` 目录下 AI API 中转 / 统一网关竞品研究的正式入口页。平台调研优先维护独立文档，本页负责导航、分组和横向理解。
> 上级入口：[中转服务文档总索引](../中转服务文档总索引.md)

## 一、怎么读这套资料

建议按下面顺序阅读：

1. 先看本页，建立平台分类和整体心智。
2. 再看对应平台的 `全景解析`，理解单个平台的产品叙事。
3. 最后看专题文档，沉淀对低价、商业模式和产品化方向的判断。

早期长稿仍保留在 [AI中转服务调研](AI中转服务调研.md)，但后续新增内容优先维护本页和独立文档。

## 二、平台分组

### 2.1 治理型统一网关

- [OpenRouter_全景解析](OpenRouter_全景解析.md)：全球统一网关 benchmark，强在路由、隐私、企业治理。
- [ZenMux_全景解析](ZenMux_全景解析.md)：官方供给叙事、Auto routing、failover 与 compensation 结合的统一网关样本。
- [APIMart_全景解析](APIMart_全景解析.md)：更稳健的 API Hub 形态，强调官方通道、多模态和 SLA。

### 2.2 国内开发工具导向中转站

- [AICodeWith_全景解析](AICodeWith_全景解析.md)：统一额度池 + AI 编程工具接入。
- [Code101_全景解析](Code101_全景解析.md)：订阅制 AI API Gateway，主打 Claude Code / Cursor / Codex CLI 低改造接入。
- [哈基米AI_全景解析](哈基米AI_全景解析.md)：全协议统一接入 + 一键配置工具。
- [PPToken_全景解析](PPToken_全景解析.md)：充值 / 订阅 / 兑换 / 工具配置一体化。
- [BeefAPI_全景解析](BeefAPI_全景解析.md)：接入路径极短，首页就是 onboarding。
- [可可AI_全景解析](可可AI_全景解析.md)：OpenAI / Codex 导向，日卡 / 月卡 / 余额三段式售卖。

### 2.3 渠道池 / 大 SKU 运营平台

- [ChatFire_全景解析](ChatFire_全景解析.md)：国内大 SKU、多通道、多计费单位聚合样本。
- [OmniXAI_全景解析](OmniXAI_全景解析.md)：分组倍率和 CLI 文档都很强的 NewAPI 路线样本。
- [O3FAN_全景解析](O3FAN_全景解析.md)：300+ 模型 + 双协议兼容 + 内置工作台的国内平台化网关样本。

### 2.4 分销 / 转售导向平台

- [DeRouter_全景解析](DeRouter_全景解析.md)：Client Key、预算切片、利润空间做得很直接。
- [PPToken_全景解析](PPToken_全景解析.md)：返佣与会员体系也很重，但更偏零售与订阅。

### 2.5 低信息密度样本

- [CodeCMD_全景解析](CodeCMD_全景解析.md)：公开资料极少，适合观察最小化中转产品壳。
- [AICodeMirror_全景解析](AICodeMirror_全景解析.md)：典型 CC Mirror 心智样本。

## 三、平台总表

| 平台 | 类型 | 主要强项 | 典型用户 |
|------|------|----------|----------|
| OpenRouter | 治理型统一网关 | 路由、隐私、企业控制面 | 全球开发者、团队、企业 |
| ZenMux | 治理型统一网关 | 官方供给叙事、自动路由、赔付与可观测性 | 开发者、AI 产品团队 |
| APIMart | 稳健型 API Hub | 官方通道、多模态、SLA | 开发者、团队、企业 |
| ChatFire | 渠道池平台 | 大 SKU、多计费单位、运营能力 | 国内开发者、渠道运营 |
| OmniXAI | 国内统一网关 | 分组倍率、CLI 教程、NewAPI 封装 | 开发工具用户 |
| O3.FAN | 平台化统一网关 | 大模型聚合、双协议兼容、工作台与工具生态 | 开发者、团队、创作用户 |
| DeRouter | 分销型平台 | Client Key、转售、动态价格 | 开发者、reseller |
| AICodeWith | 编程 API 中转 | 统一额度池、工具接入 | AI 编程用户 |
| Code101 | 订阅制工具网关 | Claude Code / Cursor / Codex 接入、会员式套餐 | AI 编程用户 |
| 哈基米AI | 全协议中转 | OpenAI / Anthropic / Gemini 统一接入 | 个人开发者 |
| PPToken | 会员型网关 | 充值、订阅、兑换、工具配置 | 高频工具用户 |
| BeefAPI | 轻量接入网关 | onboarding、工具入口、一键命令 | Claude Code / Codex 用户 |
| 可可AI | Codex 中转站 | 日卡 / 月卡 / 余额、Codex 配置教程 | OpenAI / Codex 用户 |
| CodeCMD | 轻中转样本 | 最小产品壳信号 | 窄场景用户 |
| AICodeMirror | Mirror 样本 | 品牌命名与窄场景心智 | Claude Code 镜像用户 |

## 四、专题文档

- [中转平台低价原因揭秘](中转平台低价原因揭秘.md)：分析低价来源、透明度风险和识别方法。
- [AI中转平台商业模式分析](AI中转平台商业模式分析.md)：总结收入模型、成本结构、平台分层、壁垒和产品化方向。

## 五、当前最值得重点跟踪的样本

### 5.1 如果目标是做“统一模型网关产品”

- 首先看 [OpenRouter_全景解析](OpenRouter_全景解析.md)
- 其次看 [ZenMux_全景解析](ZenMux_全景解析.md)
- 再看 [APIMart_全景解析](APIMart_全景解析.md)

### 5.2 如果目标是做“国内多模型聚合面板 + 渠道池运营”

- 首先看 [ChatFire_全景解析](ChatFire_全景解析.md)
- 其次看 [OmniXAI_全景解析](OmniXAI_全景解析.md)
- 再看 [O3FAN_全景解析](O3FAN_全景解析.md)

### 5.3 如果目标是做“AI 编程工具导向的高转化入口”

- 首先看 [AICodeWith_全景解析](AICodeWith_全景解析.md)
- 再看 [Code101_全景解析](Code101_全景解析.md)
- 其次看 [PPToken_全景解析](PPToken_全景解析.md)
- 再看 [BeefAPI_全景解析](BeefAPI_全景解析.md)
- 再看 [可可AI_全景解析](可可AI_全景解析.md)

### 5.4 如果目标是做“分销 / reseller 友好型平台”

- 首先看 [DeRouter_全景解析](DeRouter_全景解析.md)

## 六、对我们自己的直接启发

1. **不要只卖代理地址**
   更高阶的方向是统一网关、统一计费、统一日志和统一路由。

2. **优先把 requested model / actual model / provider / 扣费做透明**
   透明度本身就是差异化。

3. **分清自己要学的是哪一类样本**
   OpenRouter 适合学治理，ChatFire 适合学运营，PPToken / BeefAPI 适合学转化路径。

## 七、后续补充建议

- 对 CodeCMD、AICodeMirror 做一次注册后复核。
- 对 BeefAPI 的文档页和价格页做手动访问补全。
- 对 ZenMux 的 docs 页和 compensation 细则做一次人工复核。
- 继续补更多国内 NewAPI / OneAPI 商业化样本，观察分组倍率和渠道池设计。