# ZenMux PAYG 解析

> 文档类型：平台补充解析
> 最近更新：2026-05-07
> 研究对象：https://zenmux.ai/pricing / https://zenmux.ai/docs/guide/observability/pricing.html
> 关联平台：[ZenMux_全景解析](ZenMux_全景解析.md)
> 配对阅读：[ZenMux_Builder_Plan订阅解析](ZenMux_Builder_Plan订阅解析.md)
> 核心判断：ZenMux 的 PAYG 不是简单“按量扣 token”的备用付款方式，而是它面向生产环境、商业应用和高并发场景的正式交付层，核心卖点是无限扩展、精细计费、生产级稳定性与可观测性。

## 一、一句话定位

ZenMux PAYG 是一个 **面向生产与商业场景的按量付费统一网关层**。

它卖的不是固定月费套餐，而是：

- 按实际使用计费
- 更高稳定性和并发能力
- 更细粒度的成本控制
- 更适合正式上线业务的履约形态

如果说 Builder Plan 是开发测试入口，那么 PAYG 就是 ZenMux 的生产交付层。

## 二、官网公开出来的核心信号

ZenMux 的 Pricing 总览页对 PAYG 的定位非常明确：

- Billing model：Pay what you use
- No Rate Limit
- high concurrency support
- Production-grade stability
- Token-level precise billing
- AI Insurance compensation
- Ideal for production environments, commercial products, enterprise applications

这说明它的 PAYG 并不是“订阅不够用了再补一点”的逻辑，而是单独面向生产场景设计的正式方案。

## 三、它和 Builder Plan 的差异在哪里

ZenMux 自己在价格页上把两者对比得很清楚：

- Subscription：固定月费，适合个人开发、学习、Vibe Coding
- PAYG：按实际用量计费，适合生产和商业产品
- Subscription：10-15 RPM，存在窗口配额和周限制
- PAYG：Unlimited rate limit，支持更高并发
- Subscription：生产使用被禁止
- PAYG：官方明确推荐用于生产

因此，两者不是高低配关系，而是 **开发层 / 生产层** 的分层关系。

## 四、PAYG 的商业模式怎么理解

### 4.1 按用量扣费，但强调“精细计费”

ZenMux 并不只是说“按 token 计费”，而是把计费透明度做得比较前台。

从 Billing Transparency 文档可以看到，它会区分多种 billing items：

- `prompt`
- `completion`
- `image`
- `request`
- `web_search`
- `input_cache_read`
- `input_cache_write`
- `internal_reasoning`

这意味着它卖的不是粗颗粒度套餐，而是一个更接近基础设施的 **细项计费体系**。

### 4.2 价格按模型和 provider 变化

ZenMux 文档明确说明：

- 不同模型价格不同
- 同一模型在不同 provider 下价格也可能不同
- 模型详情页会展示各 provider 的价格标准
- 阶梯计价模型会按 tier 展示

这和典型统一网关的逻辑一致：

**平台通过 provider 聚合与路由承接复杂性，用户按最终消耗买结果。**

### 4.3 PAYG 更像正式履约层

价格页还给了几个明显的生产信号：

- unlimited scaling
- precise billing
- production-grade stability
- commercial products recommended

再结合文档侧的日志、成本统计、请求明细，可以更准确地判断：

ZenMux 想把 PAYG 做成一个可以承载正式业务的统一 API 层，而不只是内部测试额度池。

## 五、可观测性为什么重要

PAYG 能否成立，核心不只是能扣费，而是扣费要可解释。

ZenMux 文档里提供了几类关键可观测能力：

- 模型详情页查看价格
- Log Details 查看单次调用的成本细项
- Cost Statistics 查看当日、本月等统计成本
- Usage / Logs / Cost 分页查看使用情况

这意味着它在卖一种更高阶的东西：

**不是只把请求转发出去，而是把调用、计费、排查和复盘一起产品化。**

## 六、PAYG 为什么更适合生产

从公开信息看，ZenMux 认为生产环境需要的是：

- 无明显速率限制
- 高并发支撑
- 更稳定的服务等级
- 更细的成本追踪
- 商业场景可持续扩容

这些都不是订阅层最擅长的。订阅层更适合让用户先形成使用习惯，但一旦进入正式业务，平台需要把资源调度和成本波动交给 PAYG。

## 七、对我们的直接启发

1. **按量层要和订阅层目标完全不同**
   订阅卖低门槛，PAYG 卖生产交付，不要混成一套模糊规则。

2. **计费透明本身就是产品能力**
   如果用户看不清 prompt、completion、cache、request 等成本构成，生产层很难建立信任。

3. **模型价和 provider 价差要前台化**
   这会直接影响用户对平台是否“偷换模型”或“黑箱加价”的判断。

4. **日志、成本和用量页必须和扣费体系联动**
   否则 PAYG 只剩“按量收费”，但没有“按量可解释”。

## 八、一句话结论

ZenMux 的 PAYG 是它面向生产、商业化和高并发场景的正式统一网关交付层。它的关键不是“按量收费”本身，而是把高并发、生产稳定性、细项计费和调用可观测性一起打包成可上线的产品能力。