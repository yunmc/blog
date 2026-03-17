# AI 编程 API 中转服务调研

> 调研日期：2026-03-13 ~ 2026-03-14
> 调研对象：AICodeWith、CodeCMD、AICodeMirror 及同类平台

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

| 套餐 | 价格（CNY） | 目标���户 |
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
| 信息丰富度 | ★★☆☆��（公开信息较少） |

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

## 三、平台横向对比

| 维度 | AICodeWith | CodeCMD | AICodeMirror |
|------|-----------|---------|-------------|
| 文档完善度 | ★★★★★ | ★★☆☆☆ | ★★☆☆☆ |
| 公开信息量 | 丰富 | 较少 | 较少 |
| 多模型支持 | Claude + Codex + Gemini | 待确认 | 待确认 |
| 统一额度池 | ✅ | 待确认 | 待确认 |
| OpenCode 插件 | ✅ 有专用插件 | 待确认 | 待确认 |
| 入门价格 | ¥399 | 待确认 | 待确认 |
| 积分永不过期 | ✅ | 待确认 | 待确认 |
| 中文文档 | ✅ 完善 | 待确认 | 待确认 |

---

## 四、更多同类竞品

| 平台 | 网址 | 特点 |
|------|------|------|
| 仟里码 | 1kcode.cn | Claude Code 国内使用方案 |
| LumeCoder | lumecoder.com | CC Mirror，透明计费 |
| AICodeEditor | aicodeditor.com | 面向企业，强调合规 |
| ClaudeCodeAPI | claudecodeapi.com | ¥1.2=$1 积分，月套餐 $99 起 |

---

## 五、商业模式分析（通用）

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

## 六、总结

这类平台本质都是 **API 中间商**，不提供自有模型能力，核心价值：
1. 解决国内网络访问问题
2. 解决海外支付问题
3. 提供中文化体验
4. 部分平台提供多模型统一管理

**选择建议：**
- 如果已有海外支付和网络条件 → 直接用官方 API 最可靠
- 如果需要中转 → 优先选文档完善、社区口碑好的平台
- 三者中 AICodeWith 公开信息最丰富，CodeCMD 和 AICodeMirror 需实际注册体验后评估
