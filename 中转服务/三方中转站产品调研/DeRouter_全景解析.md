# DeRouter 全景解析

> 文档类型：平台全景解析
> 最近更新：2026-05-06
> 研究对象：https://derouter.ai / https://derouter.ai/docs
> 核心判断：DeRouter 不是通用多模型市场型网关，而是一个以 Claude / GPT 官方兼容接入、动态低价和分销体系为核心卖点的 API 中转平台。

## 一、一句话定位

DeRouter 更像一个 **全规格模型中转 + 分销型 API 平台**。

它强调的不是模型广度，而是三件事：

- 不受地区与封禁限制地访问 Claude、GPT、Codex 等模型
- 用 OpenAI / Anthropic 兼容方式直接替换官方接口
- 通过 Client Key、Reseller、Referral 做下游分销

## 二、官网公开出来的关键信息

- 官网主标语：Access Frontier AI Models at a Fraction of the Cost。
- 核心卖点：No restrictions、Full-spec models、All models one account、Standard API compatible。
- 注册门槛：仅邮箱 + 验证码，无信用卡、无 KYC。
- 充值门槛：最低 $10。
- 公开定价对象主要是 Claude Opus / Sonnet / Haiku 和 GPT-5.x / Codex。
- 首页明确写了价格系数机制：实际价格会随网络负载在 0.8x 到 1.5x 间波动，5 分钟刷新一次。

## 三、最重要的产品特征

### 3.1 兼容层非常直接

DeRouter 的接入方式非常明确：

- OpenAI 兼容代理基址：`https://api.derouter.ai/openai/v1`
- 管理 API 基址：`https://cf-api.derouter.ai`

文档给出的 Quick Start 是标准 OpenAI SDK 写法，只要改 `base_url` 和 `api_key` 即可。

这意味着它卖的首先是 **迁移成本极低**。

### 3.2 它更像“模型 access layer”而不是“模型 marketplace”

从官网公开内容看，DeRouter 没有像 OpenRouter 那样突出几百个模型、几十个 provider、复杂 routing policy。

它强调的是：

- Claude / GPT 官方全规格模型
- 上下文、能力、输出“不裁剪”
- 标准 API 兼容
- 更低价格

所以它的心智更接近：

**帮用户稳定、便宜地拿到前沿闭源模型能力。**

### 3.3 动态定价机制很特别

DeRouter 的公开价格页并不是固定价格，而是：

- 先给出平均价（1.0x coefficient）
- 再给出当前实时价格
- 实时价格随网络负载在 `0.8x` 到 `1.5x` 之间波动

官方写法是：

- 大多数时间系数在 `0.8x` 到 `1.0x`
- 晚高峰通常接近 `1.2x`

这说明它的价格系统不是简单静态价目表，而是 **负载联动型动态定价**。

### 3.4 分销能力是核心商业设计

DeRouter 最值得记住的一点，是它把分销体系做得很完整。

文档明确区分两类 Key：

- `Account Key`：可调用管理接口 + OpenAI 兼容代理
- `Client Key`：自助查询自身余额 / 日志 + OpenAI 兼容代理

支持的管理能力包括：

- 查看主账户余额
- 创建 / 更新 / 删除 Client Key
- 给 Client Key 分配预算
- 查询所有 Key 的 usage logs

这意味着它不是只卖“终端调用”，而是直接把 **下游转售** 做成了第一类能力。

### 3.5 Reseller 模型非常明确

Reseller 文档给出了很完整的示例流程：

1. 账户先充值，例如 $200
2. 给下游客户创建 Client Key
3. 自己设置客户看到的预算与自己实际成本
4. 差额自动形成利润
5. 客户通过 `apikey.cloud` 自助查看余额和用量

几个关键点：

- 利润率在创建时锁定
- 删除 Client Key 时，未消耗成本部分回退给主账户
- 目前默认 60 RPM、5 并发
- 支持内部无加价分发

这是一种非常典型的 **预付费额度分销平台** 设计。

### 3.6 Referral 体系也做得很重

除了主动分销，DeRouter 还有被动推广体系：

- Referral commission 5% 到 30%
- 依据累计被推荐用户充值额分层
- 可自定义 referral code
- 可把一部分返佣再返给用户
- 佣金直接打回平台余额，无最低提现门槛

所以它的增长设计分成两层：

- 主动卖 Key：Reseller
- 被动拉新：Referral

## 四、价格与商业信号

公开价格示例显示：

- Claude Sonnet 4.6 平均价明显低于官方标价
- GPT-5.4、GPT-5.3 Codex、GPT-5.5 也显著低于官方标价
- 官方页面直接写出相对 Anthropic / OpenAI 官方价的折扣比例

这意味着它的产品故事就是：

**官方兼容、低门槛接入、价格远低于官方。**

但这也意味着，用户会天然进一步追问它的供给来源与稳定性边界。

## 五、它和 OpenRouter / ChatFire 的差异

| 维度 | DeRouter | OpenRouter | ChatFire |
|------|----------|------------|----------|
| 核心卖点 | 前沿闭源模型低价接入 + 分销 | 路由控制面 + 企业治理 | 多渠道大 SKU 聚合运营 |
| 平台气质 | 轻网关、强分销 | 强基础设施、强治理 | 强运营、强渠道池 |
| 模型范围 | 以 Claude / GPT / Codex 为核心 | 广泛 | 极广 |
| 路由控制 | 几乎不强调 | 强 | 中等 |
| 分销设计 | 很强 | 弱于 DeRouter | 未明显突出 |
| 用户类型 | 终端开发者 + 转售商 | 开发者 + 企业 | 开发者 + 企业 + 渠道运营 |

## 六、值得我们借鉴的点

1. **把下游转售做成一等能力**
   Client Key、预算切片、成本与利润锁定，这些都很适合做成正式产品。

2. **管理 API 简洁直接**
   余额、子 Key、日志，这三个面就足以撑起一个轻量分销平台。

3. **价格机制有产品感**
   动态 coefficient 把“价格波动”显式化，而不是黑盒调价。

## 七、需要警惕的地方

1. **低价叙事很激进**
   当平台长期主打“官方几折甚至更低”时，供给可持续性会成为第一质疑点。

2. **模型面相对收敛**
   相比 OpenRouter / ChatFire 这类平台，DeRouter 更像“少数高价值模型 access layer”，不适合直接当作通用网关 benchmark。

3. **平台护城河更偏商务与分销**
   如果不具备稳定供给和分销渠道，单抄产品壳的意义有限。

## 八、一句话结论

DeRouter 不是最值得学习“多模型治理”的平台，但非常值得学习 **轻量中转平台如何把 Key 分销、预算切片和利润计算产品化**。