# 实战方案：利用 SVG 源码 + 截图让大模型精准还原 Figma 设计稿

在不升级 Figma 付费版（无法使用 Dev Mode），且不依赖第三方收费插件的情况下，如何将整页的设计精准喂给 AI 进行“像素级”还原？
目前最极客且高效的“Vibe Coding”最佳实践是：**视觉截图 + SVG 源码投喂法**。

## 1. 核心原理
大模型（如 Claude 3.5 Sonnet）的视觉能力极强，能轻松理解整体排版和组件结构，但直接看图容易“猜错”具体的十六进制色值、间距、复杂阴影和精确的圆角大小。

Figma 复制出来的 SVG 本质上是一段包含了**绝对精确的元数据（Metadata）**的 XML 代码，AI 解析它如同看字典：
- **圆角极其精准**：如 `<rect rx="16">` 代表 16px 的 `border-radius`。
- **确切的色值与透明度**：如 `fill="#18191B" fill-opacity="0.4"` 或复杂的 `<linearGradient>` 渐变色。
- **高级阴影与滤镜**：`<filter>` 标签内的 `dx/dy/stdDeviation` 可以被 AI 完美反推出 `box-shadow` 的参数。

**唯一局限（为何需要配图）：** 
Figma 导出的 SVG 通常会将文本轮廓化（变为 `<path d="...">`）。意味着 AI 虽然能从 SVG 中看出由于文字形成的多大色块，但无法解析出“里面写了什么字”。因此，**“SVG 当数值字典，截图当文案排版指南”** 是不可分割的组合技。

## 2. 真实场景痛点与极速操作步骤
在实际开发中，我们通常会同时重构一条业务流里的前前后后多个页面，逐个复制 SVG 太过繁琐。你可以**直接全选**它们一并搞定，让 AI 自己去做元素匹配与梳理。

步骤如下：
1. 在 Figma 中，按住 `Shift` 键，**同时选中**需要让 AI 还原的多个页面 Frame（画板）。
2. **无需逐个操作**，直接**右键**，在菜单中选择 **`Copy/Paste as` -> `Copy as SVG`**。（这时所有画板会合并成一段巨大的 `<svg>` 代码被复制在剪贴板）。
3. 截取这些 Frame 对应的**多张高清图片**。
4. 将“多张截图”与“合并后的这一整段超大 SVG 代码”一起打包扔给大模型（如 Cursor / Claude 3.5 Sonnet），并带上以下“免匹配”Prompt 模板。

## 3. 神级批量处理提示词模板 (无须人工一一对应版)

把下面的 Prompt 连同多张图片以及那一大坨混合 SVG 代码一起发送：

```text
我为你提供了【多张 UI 设计图的截图】以及它们对应的【合并后的 Figma 原始 SVG 源码】。
（注：这段包含所有页面的完整 SVG 源码中，每个顶面页面画板通常会包裹在一个带 id 或特定坐标的 `<g>` 标签中）。

请你作为架构级的移动端工程师，使用 [Flutter (Dart)] 帮我将它们完整重构为代码体系。

要求原则：
1. 【自动映射与模式识别】：你必须自己观察提供的多张截图，然后去我提供的那一大段 SVG 源码中，通过宽高 (`width/height`)、坐标 (`x/y`) 的分组结构，**自行把源码数据和截图特征对应起来**。
2. 【内容与结构（看图）】：排版结构、具体的文本内容及其所处的位置关系，请你通过“视觉截图”来整体识别并还原。
3. 【高保真数值（看源码）】：涉及到绝对精准的色值代码（HEX/RGBA）、圆角大小（Border Radius）、渐变参数、阴影参数以及核心元素的绝对尺寸，请你务必从定位到的“SVG 源码节点”中读取分析，绝对禁止依据图片凭空猜测标注！
4. 【响应式重构】：请观察 SVG 里的 `<rect>` 与图层的嵌套解析出元素树，构建符合现代 Flutter 理念的布局（使用 Column, Row, Stack, Expanded 等），禁止使用绝对坐标 (`Positioned` 写死全部坐标) 写死页面形态。
5. 【极致的组件化提取】：这几张设计图大概率属于同一个业务模块。请**强烈关注**它们之间可以复用的“原子和分子设计”（例如：统一样式的按钮池、共用的列表卡片、底部 Tab 栏等），在写页面代码之前，首先为我抽离并单独建立一个干净无冗余的公共 Widget 组件 / UI库，然后再使用这些复用组件去拼装各个页面。
```
## 4. 核心痛点：SVG 太大怎么办？

从 Figma 批量 Copy 出来的 SVG 动辄几十 KB 甚至几百 KB，直接贴进对话窗口会：
- 吃掉大量 Token 预算（一段 200KB 的 SVG ≈ 5~8 万 Token）
- 挤压 AI 的"思考空间"，导致输出质量下降甚至被截断
- 包含大量对代码还原**毫无价值**的冗余数据

### 4.1 理解"大"在哪 — 体积构成分析

Figma SVG 的体积大户，按占比排序：

| 元素 | 典型占比 | 对还原的价值 |
|---|---|---|
| `<path d="...">` 文本轮廓 | **40~60%** | ❌ 零价值（文字内容靠截图识别） |
| `<image href="data:image/png;base64,...">` 内嵌位图 | **10~30%** | ❌ 零价值（图片资源应单独切图） |
| `<clipPath>` / `<mask>` 裁剪遮罩 | 5~15% | ⚠️ 低价值（告诉 AI 用 `ClipRRect` 即可） |
| `<rect>` / `<circle>` / 简单 `<path>` | 5~15% | ✅ **核心价值**（布局结构、尺寸、圆角） |
| `<linearGradient>` / `<radialGradient>` | 2~5% | ✅ **高价值**（精确渐变参数） |
| `<filter>` (阴影/模糊) | 1~3% | ✅ **高价值**（精确阴影参数） |
| Figma 私有属性 / 无用 metadata | 2~5% | ❌ 零价值 |

**结论：超过一半的体积是"文本轮廓路径"和"内嵌位图"，而它们对 AI 还原代码完全没用。**

### 4.2 方案一：SVGO 自动化瘦身（推荐）

[SVGO](https://github.com/svg/svgo) 是业界标准的 SVG 优化器，可一键砍掉 30~70% 体积。

**安装：**
```bash
npm install -g svgo
```

**使用（先把剪贴板 SVG 存为文件）：**
```bash
# 基础优化 — 通常能瘦 30~50%
svgo input.svg -o output.svg

# 激进优化 — 针对投喂 AI 的场景，进一步精简
svgo input.svg -o output.svg --config svgo.config.mjs
```

**推荐的 `svgo.config.mjs` 配置（针对 AI 投喂场景）：**
```js
export default {
  plugins: [
    'preset-default',        // 默认优化集（合并路径、移除空属性等）
    'removeXMLNS',           // 移除 xmlns 声明
    'removeDimensions',      // 用 viewBox 替代 width/height
    'removeOffCanvasPaths',  // 移除画布外不可见元素
    {
      name: 'removeAttrs',
      params: {
        attrs: ['data-*', 'class', 'style'] // 移除 Figma 私有属性
      }
    }
  ]
}
```

### 4.3 方案二：手动删除文本路径（效果最猛）

文本轮廓是体积的最大杀手。因为我们已经有截图来告诉 AI "文字写的什么"，所以这些 `<path>` 完全可以删掉，用占位注释替代。

**识别文本路径的特征：**
- 通常被包裹在一个 `<g>` 标签中，且该 `<g>` 内有大量密集的短 `<path>` 元素
- `<path d="...">` 的 `d` 属性极长（几百到几千字符），且充满 `C`（三次贝塞尔曲线）指令
- 同一组内多个 `<path>` 的 `fill` 颜色一致（通常是黑色或白色系的文本颜色）

**替换策略：**
将文本路径区域替换为一行注释，保留关键信息：
```xml
<!-- 原先是一段文字区域，位于 x="24" y="120"，fill="#333333"，具体文案请看截图 -->
```

**自动化脚本（Node.js）：**
```js
// strip-text-paths.mjs
// 用法: node strip-text-paths.mjs input.svg > output.svg
import { readFileSync } from 'fs';

const svg = readFileSync(process.argv[2], 'utf-8');

// 匹配超长 path（d 属性 > 500 字符 且包含大量曲线指令的大概率是文本轮廓）
const cleaned = svg.replace(
  /<path[^>]*\bd="([^"]{500,})"[^>]*\/?\s*>/g,
  (match, d) => {
    const curveCount = (d.match(/[CcSs]/g) || []).length;
    if (curveCount > 20) {
      // 提取 fill 和位置信息作为注释
      const fill = match.match(/fill="([^"]*)"/)?.[1] || 'unknown';
      return `<!-- text-outline removed, fill="${fill}", see screenshot -->`;
    }
    return match; // 保留非文本的正常路径
  }
);

process.stdout.write(cleaned);
```

### 4.4 方案三：移除内嵌位图（Base64 图片）

Figma 会把图片资源直接以 Base64 编码嵌入 SVG，一张小图就可能占几十 KB。

```bash
# 快速查看 SVG 里有没有内嵌图片
grep -c "data:image" input.svg
```

**处理方式：** 直接替换为占位标记
```xml
<!-- 原先此处是一张内嵌图片，宽200高150，请用 Image/AssetImage 占位 -->
```

**一行 sed 搞定（Git Bash / Mac / Linux）：**
```bash
sed -E 's|<image[^>]*href="data:image[^"]*"[^>]*/>|<!-- embedded-image removed, see screenshot -->|g' input.svg > output.svg
```

### 4.5 方案四：分页投喂（大杀器）

当优化后的 SVG 依然很大时，**不要硬塞一条消息**，改为分步投喂：

**第一轮对话 — 建立全局认知：**
```text
我有 5 张 UI 设计截图（见附件），它们属于同一个业务流程。
请你先观察截图，识别出：
1. 所有页面共用的组件（导航栏、底部Tab、通用按钮/卡片样式等）
2. 各页面的整体布局结构
3. 你需要哪些精确数值（色值、圆角、间距等）

先不要写代码，只输出你的「结构分析报告」。
```

**第二轮起 — 逐页投喂 SVG：**
```text
这是第 1 张页面（登录页）的 SVG 源码，请结合之前的截图分析，
提取所有精确数值，然后输出该页面的完整代码。

[粘贴该页面的优化后 SVG]
```

> **核心思路：** 截图建立全局认知（Token 消耗小），SVG 按需精确查数值（按页拆分后每段可控在 1~3 万 Token）。

### 4.6 方案五：只复制关键组件的 SVG

很多时候你并不需要整页的 SVG。真正需要 SVG 精确数值的场景往往是：
- 带渐变/阴影的复杂卡片
- 自定义形状的按钮
- 非标准的图标组
- 复杂的背景装饰

**操作：** 在 Figma 中只选中这些"难搞的组件"单独 Copy as SVG，其余简单布局让 AI 看截图自行判断即可。这样一段 SVG 通常只有几 KB。

### 4.7 优化效果对照

| 优化手段 | 体积缩减 | 信息损失 | 操作成本 |
|---|---|---|---|
| SVGO 默认优化 | 30~50% | 无 | 一条命令 |
| + 删除文本轮廓 | 再减 40~60% | 无（靠截图补） | 脚本一键 |
| + 删除内嵌位图 | 再减 10~30% | 无（单独切图补） | 一行 sed |
| **综合效果** | **原始体积的 10~25%** | **无实质损失** | **< 1 分钟** |

> 举例：一个 5 页面合并的 300KB SVG，经过上述三步处理后通常可压缩到 **30~75KB**（约 1~2 万 Token），完全在大模型的舒适区内。

### 4.8 终极工作流（推荐）

```
Figma 批量选中 → Copy as SVG → 存为 raw.svg
                                      ↓
                              svgo raw.svg -o opt.svg
                                      ↓
                        node strip-text-paths.mjs opt.svg > clean.svg
                                      ↓
                           sed 移除 base64 图片 → final.svg
                                      ↓
                  截图 + final.svg → 分页或一次性投喂大模型
```

将这套流程封装成一个 shell 脚本，就是可复用的一键工具：

```bash
#!/bin/bash
# figma-svg-clean.sh — Figma SVG 瘦身一键脚本
# 用法: ./figma-svg-clean.sh input.svg

INPUT="$1"
BASENAME="${INPUT%.*}"

echo "📐 Step 1: SVGO 基础优化..."
svgo "$INPUT" -o "${BASENAME}_opt.svg" --quiet

echo "✂️  Step 2: 移除文本轮廓路径..."
node strip-text-paths.mjs "${BASENAME}_opt.svg" > "${BASENAME}_notext.svg"

echo "🖼️  Step 3: 移除内嵌 Base64 图片..."
sed -E 's|<image[^>]*href="data:image[^"]*"[^>]*/>|<!-- embedded-image removed -->|g' \
  "${BASENAME}_notext.svg" > "${BASENAME}_final.svg"

# 对比结果
ORIG=$(wc -c < "$INPUT")
FINAL=$(wc -c < "${BASENAME}_final.svg")
RATIO=$((FINAL * 100 / ORIG))
echo ""
echo "✅ 完成！ $ORIG bytes → $FINAL bytes (保留 ${RATIO}%)"
echo "📄 输出文件: ${BASENAME}_final.svg"
```

## 5. 换个思路：不投喂 SVG 的替代方案

SVG 本质上是"用 XML 描述矢量图形"——这对人类来说是一种糟糕的中间格式，信息密度低、冗余多。如果换个角度，**跳过 SVG 这层**，直接拿到 Figma 的结构化设计数据，效率会高得多。

### 5.1 方案 A：Figma REST API 提取 JSON 节点树（最推荐）

Figma 提供免费的 REST API（只要有个人 Access Token 就行），可以直接拿到**设计文件的完整节点树**，是一段结构化 JSON，信息密度远高于 SVG。

> ⚠️ **免费用户限流警告（2025.11.17 起生效的新限制）**
>
> Figma API 的限流取决于三个因素：**座位类型** × **端点等级(Tier)** × **资源所在计划**。
>
> 我们用的 `GET /v1/files/:key` 和 `GET /v1/files/:key/nodes` 都属于 **Tier 1**（最严格的一档），`GET /v1/images/:key` 也是 Tier 1。具体限额如下：
>
> | 端点等级 | View / Collab 座位 | Dev / Full 座位（Starter） | Dev / Full（Pro） | Dev / Full（Enterprise） |
> |---|---|---|---|---|
> | **Tier 1**（GET file / nodes / images） | **每月最多 6 次** | 10 次/分钟 | 15 次/分钟 | 20 次/分钟 |
> | Tier 2（GET image fills 等） | 5 次/分钟 | 25 次/分钟 | 50 次/分钟 | 100 次/分钟 |
> | Tier 3（GET file metadata 等） | 10 次/分钟 | 50 次/分钟 | 100 次/分钟 | 150 次/分钟 |
>
> **关键坑点：**
> - 免费（Starter）计划的 View/Collab 座位用户，Tier 1 端点**每月只能请求 6 次**，而且这是上限，高峰期可能更少。
> - 即使你在别的组织有 Full 座位，只要请求的**文件处于 Starter 计划**下，就按 Starter 限额算。
> - Personal Access Token 是账号级的，团队多人共用同一个 Token 会共享限额。
> - 触发限流后会返回 `429` 状态码，响应头里有 `Retry-After`（秒数）告诉你多久后重试。
>
> **应对策略：**
> 1. **一次拉够**：尽量选中最顶层的父节点（如整个 Page），一次请求拿全部子节点，别一个 Frame 一个 Frame 地拉。
> 2. **本地缓存**：拉下来的 JSON 存为文件，后续反复投喂 AI 时直接读本地文件，不要重复请求 API。
> 3. **升级座位**：如果你在团队里有 Dev 或 Full 座位（哪怕是 Starter 计划），Tier 1 直接变成 10 次/分钟，基本够用。
> 4. **429 自动重试**：脚本里加上 `Retry-After` 处理逻辑（下文有示例）。

---

#### Step 0：获取 Personal Access Token

1. 打开 [figma.com](https://figma.com)，登录你的账号
2. 点击左上角头像 → **Settings**
3. 滚动到 **Personal access tokens** 区域
4. 输入一个描述（如 `design-export`），点击 **Generate token**
5. **立刻复制保存**（只显示一次），类似 `figd_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

> ⚠️ 这个 Token 能访问你账号下的所有文件，不要提交到 Git 或分享给别人。

---

#### Step 1：从 Figma URL 中提取 FILE_KEY 和 NODE_ID

打开你的 Figma 设计文件，看浏览器地址栏：

```
https://www.figma.com/design/ABC123xyz/My-App-Design?node-id=456-789
                              ^^^^^^^^^^^                ^^^^^^^^
                              这是 FILE_KEY              这是 NODE_ID
```

**三种拿 NODE_ID 的方式：**
- **方式一：** 直接看 URL 里的 `?node-id=456-789`（选中 Frame 时 URL 会自动变）
- **方式二：** 选中目标 Frame → 右键 → **Copy/Paste as** → **Copy link** → 链接里有 `node-id`
- **方式三：** 选中 Frame → 右侧面板最底部可以看到 Node ID

> 💡 **如果你要导出多个页面**，可以选中它们的**共同父级**（比如整个 Page），只需要一个 NODE_ID 就全包含了。

---

#### Step 2：调用 API 拉取数据

**方法一：命令行 curl（最直接）**

```bash
# 替换三个变量
TOKEN="figd_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
FILE_KEY="ABC123xyz"
NODE_ID="456-789"

# 拉取指定节点的完整属性树
curl -s -H "X-Figma-Token: $TOKEN" \
  "https://api.figma.com/v1/files/$FILE_KEY/nodes?ids=$NODE_ID" \
  | python -m json.tool > design.json

# 查看体积
ls -lh design.json
```

**方法二：浏览器直接访问（零门槛试水）**

安装一个浏览器插件（如 [ModHeader](https://modheader.com/)），添加请求头：
```
X-Figma-Token: figd_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```
然后直接在浏览器地址栏访问：
```
https://api.figma.com/v1/files/ABC123xyz/nodes?ids=456-789
```
浏览器会直接显示 JSON 结果，你可以 Ctrl+A 全选复制。

**方法三：Node.js 脚本（可扩展、可自动化）**

```js
// figma-export.mjs
// 用法: FIGMA_TOKEN=figd_xxx node figma-export.mjs
import { writeFileSync } from 'fs';

const TOKEN    = process.env.FIGMA_TOKEN;
const FILE_KEY = 'ABC123xyz';
const NODE_IDS = ['456-789', '456-790', '456-791']; // 可以一次拉多个节点

// 429 自动重试（配合 Retry-After 响应头）
async function fetchWithRetry(url, opts, maxRetries = 5) {
  for (let attempt = 0; attempt <= maxRetries; attempt++) {
    const resp = await fetch(url, opts);
    if (resp.status !== 429) return resp;

    const retryAfter = Number(resp.headers.get('Retry-After')) || 5;
    const plan = resp.headers.get('X-Figma-Plan-Tier') || 'unknown';
    const limitType = resp.headers.get('X-Figma-Rate-Limit-Type') || 'unknown';
    console.warn(`⚠️ 被限流了 (plan=${plan}, type=${limitType})，${retryAfter}s 后重试 (${attempt + 1}/${maxRetries})...`);

    if (attempt === maxRetries) {
      console.error(`❌ 重试 ${maxRetries} 次后仍被限流。如果你是免费 Starter 的 View/Collab 座位，Tier 1 端点每月只有 6 次额度。`);
      process.exit(1);
    }
    await new Promise(r => setTimeout(r, retryAfter * 1000));
  }
}

async function fetchNodes() {
  const ids = NODE_IDS.join(',');
  const resp = await fetchWithRetry(
    `https://api.figma.com/v1/files/${FILE_KEY}/nodes?ids=${ids}`,
    { headers: { 'X-Figma-Token': TOKEN } }
  );

  if (!resp.ok) {
    console.error(`API 错误: ${resp.status} ${resp.statusText}`);
    const text = await resp.text();
    console.error(text);
    process.exit(1);
  }

  const data = await resp.json();
  writeFileSync('design.json', JSON.stringify(data, null, 2));
  console.log(`✅ 已保存 design.json (${(JSON.stringify(data).length / 1024).toFixed(1)} KB)`);
}

fetchNodes();
```

---

#### Step 3：JSON 太大？精简瘦身

Figma API 返回的 JSON 虽然比 SVG 小很多，但复杂页面仍然可能有几十 KB。可以用脚本只保留 AI 需要的字段：

```js
// figma-slim.mjs
// 用法: node figma-slim.mjs design.json > slim.json
import { readFileSync } from 'fs';

const raw = JSON.parse(readFileSync(process.argv[2], 'utf-8'));

// AI 真正需要的属性白名单
const KEEP_KEYS = new Set([
  'name', 'type', 'characters',                          // 身份 & 文本
  'absoluteBoundingBox', 'size',                          // 尺寸位置
  'fills', 'strokes', 'strokeWeight',                     // 填充描边
  'cornerRadius', 'rectangleCornerRadii',                 // 圆角
  'effects',                                              // 阴影模糊
  'style', 'fontSize', 'fontWeight', 'fontFamily',        // 字体
  'lineHeightPx', 'letterSpacing', 'textAlignHorizontal', // 排版
  'layoutMode', 'itemSpacing', 'paddingLeft',             // Auto Layout
  'paddingRight', 'paddingTop', 'paddingBottom',
  'primaryAxisAlignItems', 'counterAxisAlignItems',
  'children',                                             // 子节点
  'opacity', 'visible', 'blendMode',                      // 可见性
  'constraints', 'layoutAlign', 'layoutGrow',             // 约束
]);

function slim(node) {
  if (!node || typeof node !== 'object') return node;
  if (Array.isArray(node)) return node.map(slim).filter(Boolean);

  const result = {};
  for (const [key, value] of Object.entries(node)) {
    if (KEEP_KEYS.has(key)) {
      result[key] = key === 'children' ? value.map(slim).filter(Boolean) : slim(value);
    }
  }
  // 跳过不可见节点
  if (result.visible === false) return null;
  return Object.keys(result).length ? result : null;
}

// Figma API 的 nodes 响应: { nodes: { "id": { document: {...} } } }
const nodes = raw.nodes || raw;
const output = {};

for (const [id, val] of Object.entries(nodes)) {
  if (val?.document) {
    output[id] = slim(val.document);
  } else {
    output[id] = slim(val);
  }
}

process.stdout.write(JSON.stringify(output, null, 2));
```

```bash
node figma-slim.mjs design.json > slim.json

# 对比效果
echo "原始: $(wc -c < design.json) bytes"
echo "精简: $(wc -c < slim.json) bytes"
```

> 一般能再压缩 50~70%，复杂页面从 80KB → 20KB 左右。

---

#### Step 4：投喂给大模型

**返回的 JSON 结构示意（精简后）：**
```json
{
  "name": "LoginPage",
  "type": "FRAME",
  "absoluteBoundingBox": { "x": 0, "y": 0, "width": 375, "height": 812 },
  "layoutMode": "VERTICAL",
  "itemSpacing": 16,
  "paddingTop": 60, "paddingLeft": 24, "paddingRight": 24,
  "children": [
    {
      "name": "HeaderCard",
      "type": "RECTANGLE",
      "cornerRadius": 16,
      "fills": [{
        "type": "GRADIENT_LINEAR",
        "gradientStops": [
          { "color": { "r": 0.2, "g": 0.4, "b": 1, "a": 1 }, "position": 0 },
          { "color": { "r": 0.6, "g": 0.2, "b": 0.9, "a": 1 }, "position": 1 }
        ]
      }],
      "effects": [{
        "type": "DROP_SHADOW",
        "offset": { "x": 0, "y": 4 },
        "radius": 12,
        "color": { "r": 0, "g": 0, "b": 0, "a": 0.15 }
      }]
    },
    {
      "name": "WelcomeText",
      "type": "TEXT",
      "characters": "欢迎登录",
      "style": { "fontSize": 24, "fontWeight": 600, "lineHeightPx": 32 },
      "fills": [{ "type": "SOLID", "color": { "r": 0.1, "g": 0.1, "b": 0.1, "a": 1 } }]
    }
  ]
}
```

**对比 SVG 的核心优势：**

| 维度 | SVG | Figma JSON |
|---|---|---|
| 文本内容 | ❌ 轮廓化丢失，需要截图补 | ✅ `characters` 字段直接包含 |
| 字体信息 | ❌ 不包含 | ✅ `fontSize`、`fontWeight`、`fontFamily` |
| 体积 | 很大（XML + 贝塞尔路径） | **小 5~20 倍**（纯结构化数据） |
| 布局语义 | ❌ 全是绝对坐标 | ✅ Auto Layout（`layoutMode`, `itemSpacing`） |
| 组件实例 | ❌ 展平看不出来 | ✅ `type: "INSTANCE"` + `componentId` |

> **关键点：** JSON 天然包含文字和 Auto Layout，SVG 方案要"截图+源码"两份材料，JSON 方案**一份文件全搞定**。

**完整 Prompt 模板：**
```text
以下是通过 Figma API 提取的设计稿 JSON 节点树（已精简，只保留布局和样式字段）。
请你解析其中的布局层级、样式数值和文本内容，用 Flutter (Dart) 还原为完整的页面和组件代码。

解析规则：
1. 布局映射：layoutMode="VERTICAL" → Column，"HORIZONTAL" → Row
   - itemSpacing → SizedBox / MainAxisAlignment.spaceBetween
   - paddingLeft/Right/Top/Bottom → EdgeInsets
   - primaryAxisAlignItems → MainAxisAlignment
   - counterAxisAlignItems → CrossAxisAlignment
2. 颜色值是 0~1 浮点数，转换公式：Color.fromRGBO(r*255, g*255, b*255, a)
3. effects 里的 DROP_SHADOW → BoxShadow(offset, blurRadius, color)
4. cornerRadius → BorderRadius.circular()
5. fills 的 type: "GRADIENT_LINEAR" → LinearGradient
6. 节点 name 字段用做 Widget 的语义命名参考
7. type: "TEXT" 的 characters 字段就是显示的文字内容
8. 请拆分公共组件，不要把所有代码写在一个文件里

[粘贴 slim.json]
```

---

#### Step 5（可选）：同时导出图片资源

Figma API 还可以直接导出节点为 PNG/SVG 图片，用于 Flutter 的 `assets/`：

```js
// figma-export-images.mjs
// 用法: FIGMA_TOKEN=figd_xxx node figma-export-images.mjs
import { writeFileSync, mkdirSync } from 'fs';

const TOKEN    = process.env.FIGMA_TOKEN;
const FILE_KEY = 'ABC123xyz';

// 需要导出为图片的节点 ID（图标、插画等）
const IMAGE_NODES = ['100-1', '100-2', '100-3'];

async function exportImages() {
  mkdirSync('assets', { recursive: true });
  const ids = IMAGE_NODES.join(',');
  // scale=2 导出 2x 图，format 可选 png/svg/jpg/pdf
  const resp = await fetch(
    `https://api.figma.com/v1/images/${FILE_KEY}?ids=${ids}&scale=2&format=png`,
    { headers: { 'X-Figma-Token': TOKEN } }
  );
  const { images } = await resp.json();

  for (const [nodeId, url] of Object.entries(images)) {
    if (!url) { console.warn(`⚠️ ${nodeId} 无法导出`); continue; }
    const imgResp = await fetch(url);
    const buffer = Buffer.from(await imgResp.arrayBuffer());
    const filename = `assets/asset_${nodeId.replace(':', '-')}.png`;
    writeFileSync(filename, buffer);
    console.log(`✅ ${filename} (${(buffer.length / 1024).toFixed(1)} KB)`);
  }
}

exportImages();
```

> 这样连"去 Figma 里手动导出切图"这一步都省了。

---

#### 完整工作流总结

```
Figma 文件 URL → 提取 FILE_KEY + NODE_ID
                          ↓
          figma-export.mjs → design.json（原始节点树）
                          ↓
          figma-slim.mjs → slim.json（精简版，~20KB）
                          ↓
          figma-export-images.mjs → assets/*.png（图片资源）
                          ↓
        slim.json + 截图（可选） + Prompt 模板 → 投喂大模型
                          ↓
                    AI 输出 Flutter 代码
```

> 整条链路**不需要 Figma 付费版**，不需要 Dev Mode，不需要第三方插件，只用免费的 Personal Access Token + 几个脚本就能搞定。

---

如果不想搞 API Token，还可以写脚本把 SVG 自动转换成一种**极度精简的自定义 DSL**，只保留 AI 需要的信息：

**转换脚本（Node.js）：**
```js
// svg-to-dsl.mjs
// 用法: node svg-to-dsl.mjs input.svg > design.dsl
import { readFileSync } from 'fs';
import { JSDOM } from 'jsdom';

const svg = readFileSync(process.argv[2], 'utf-8');
const doc = new JSDOM(svg, { contentType: 'image/svg+xml' }).window.document;

function extract(el, depth = 0) {
  const indent = '  '.repeat(depth);
  const tag = el.tagName;
  const lines = [];

  if (tag === 'rect') {
    const w = el.getAttribute('width');
    const h = el.getAttribute('height');
    const rx = el.getAttribute('rx');
    const fill = el.getAttribute('fill');
    const opacity = el.getAttribute('fill-opacity');
    lines.push(`${indent}RECT ${w}×${h}${rx ? ` r=${rx}` : ''} ${fill || ''}${opacity ? ` @${opacity}` : ''}`);
  } else if (tag === 'circle') {
    const r = el.getAttribute('r');
    const fill = el.getAttribute('fill');
    lines.push(`${indent}CIRCLE r=${r} ${fill || ''}`);
  } else if (tag === 'linearGradient') {
    const stops = [...el.querySelectorAll('stop')]
      .map(s => `${s.getAttribute('stop-color')}@${s.getAttribute('offset')}`)
      .join(' → ');
    lines.push(`${indent}GRADIENT linear: ${stops}`);
  } else if (tag === 'filter') {
    for (const fe of el.children) {
      if (fe.tagName === 'feDropShadow' || fe.tagName === 'feGaussianBlur') {
        const dx = fe.getAttribute('dx') || '0';
        const dy = fe.getAttribute('dy') || '0';
        const blur = fe.getAttribute('stdDeviation');
        const color = fe.getAttribute('flood-color') || '';
        lines.push(`${indent}SHADOW dx=${dx} dy=${dy} blur=${blur} ${color}`);
      }
    }
  } else if (tag === 'g' || tag === 'svg') {
    const id = el.getAttribute('id');
    if (tag === 'g') lines.push(`${indent}GROUP${id ? ` #${id}` : ''}`);
    for (const child of el.children) {
      lines.push(...extract(child, depth + (tag === 'g' ? 1 : 0)));
    }
  }
  // 跳过 <path>（文本轮廓）和 <image>（内嵌位图）
  return lines;
}

const root = doc.querySelector('svg');
const result = extract(root);
console.log(result.join('\n'));
```

**输出的 DSL 样子（同一段 SVG 可以从 100KB 压缩到 ~2KB）：**
```
GROUP #LoginPage
  RECT 375×812 #FFFFFF
  GROUP #HeaderCard
    RECT 327×200 r=16 #2E5BFF
    GRADIENT linear: #2E66FF@0 → #9933EA@1
    SHADOW dx=0 dy=4 blur=12 rgba(0,0,0,0.15)
  GROUP #InputArea
    RECT 327×48 r=8 #F5F5F5
    RECT 327×48 r=8 #F5F5F5
  GROUP #LoginButton
    RECT 327×48 r=24 #2E66FF
```

> **超级紧凑**，AI 一眼就能理解布局层级和数值，配合截图看文字和排版，Token 消耗可以降到**原始 SVG 的 1~3%**。

### 5.3 方案 C：Figma 免费社区插件导出 Design Token

一些免费的 Figma 社区插件可以直接导出结构化数据，不需要写代码：

| 插件 | 导出格式 | 特点 |
|---|---|---|
| **[Design Tokens](https://www.figma.com/community/plugin/888356646278934516)** | JSON | 导出颜色/字体/间距等 Token |
| **[Inspect - Export to JSON](https://www.figma.com/community/plugin/1286851735498498972)** | JSON | 导出选中图层的完整属性 |
| **[html.to" target](https://www.figma.com/community/plugin/940015253498498503)** | HTML/CSS | 直接转为 HTML，可以把 HTML 喂给 AI 转 Flutter |

**工作流：** 用插件导出 JSON/HTML → 投喂 AI → 让 AI 转换为目标框架代码

### 5.4 方案 D：截图 + 局部标注（最轻量）

对于简单页面，其实根本不需要 SVG。现代大模型的视觉能力已经很强，只需：

1. **一张高清截图**
2. **手动标注 3~5 个 AI 容易猜错的关键数值**

```text
请根据截图还原这个页面的 Flutter 代码。

以下是不要猜、必须使用的精确数值：
- 主色调: #2E66FF
- 卡片圆角: 16px
- 卡片阴影: 0 4px 12px rgba(0,0,0,0.15)
- 渐变: #2E66FF → #9933EA (从左到右)
- 主按钮高度: 48px, 圆角: 24px
```

> **适用场景：** 页面不复杂、设计风格统一、你对核心数值心里有数。
> **优势：** 零工具链、零成本、Token 消耗最低。

### 5.5 方案 E：终极白嫖——手写一个本地 Figma 插件（无任何限流）

如果你既想用 **JSON 结构化数据**投喂 AI 实现 100% 精准的样式还原，又因为白嫖 Figma 账被“每月 6 次”限流恶心到了，最佳的终极“后门”方案是：**利用 Figma Plugin API 机制手写一个本地插件**。

Figma 本地插件运行在当前被打开的客户端画布中，直接读取内存中的图层节点树，提取数据**并复制进剪贴板**。
它具有绝对优势：
- **完全离线、绝对没有网络请求**（也就**肯定没有限流**）。
- **过滤掉一切不必要的元数据废话**：比起通过 REST API 会带出来几十上百 KB 的环境属性对象（比如上万字长的无用 SVG `<path>` 或者组件映射关系 `components/styles` 表），你可以把代码逻辑写成只保留大模型翻译 UI 时真正需要的：宽高、颜色、边框、描边粗细、文字对齐、圆角等，使 AI 阅读时 Token 极省，也几乎杜绝“幻觉截断”。
- **坐标系对前端更友好**：REST API 默认提取的是在整个深空画布里的“绝对坐标 (`absoluteBoundingBox`)”，容易误导大模型强行使用类似 `Positioned` 或 `absolute` 去死堆；而插件机制中，可以直接拿到每一个 Node 相对于容器里的**相对坐标 (`x`, `y`)** 以及**真正的响应式 Flex 布局参数**（`layoutMode`, `layoutSizingHorizontal`，`layoutGrow` 等），更容易协助模型搭建诸如 `Row`、`Column` 或者 `Expanded` 这样地道的响应式代码体系。

#### 步骤一：创建私有插件环境

> **前提说明：因为 Figma 禁止在“仅查看(View Only)”模式下运行任何插件（由于缺少画布的交互操作权限），如果遇到右侧/下方菜单提醒 `Ask to edit` 或右键菜单死活打不开 Plugins 选项时，请先点击屏幕上方顶部标题栏旁边的下拉小箭头，将设计原稿复制（Duplicate to your drafts）。然后在属于自己的草稿内，你就拥有了管理员编辑权限。**

1. 在你有编辑权限的 Figma 桌面端：`右键任意空白处` -> `Plugins` -> `Development` -> `New Plugin...`
2. 选择 `Figma design` -> 随便起个名字（比如 `Export-Node-to-JSON`） -> 选择 `Default (Run once)`。
3. 它会生成一个小文件夹（如 `manifest.json`, `code.ts` 和 `ui.html`）。

#### 步骤二：替换核心提取逻辑（供 AI 使用定制版）

打开上面生成的插件所在文件夹，我们分别编写这 3 个微型文件：

**1. manifest.json** (声明插件的形态)
```json
{
  "name": "Export Node to Slim JSON",
  "id": "12345678",
  "api": "1.0.0",
  "main": "code.js",
  "ui": "ui.html",
  "editorType": ["figma"],
  "networkAccess": {
    "allowedDomains": ["none"],
    "reasoning": "完全本地内存提取"
  }
}
```

**2. code.js** (或者 `code.ts` — 核心提取大脑！)
这是所有逻辑地狱里的神之一手。为了要求 AI 做到 100% 无死角零猜测样式还原，我们务必提取出以下这些“绝对不能不给的数值”：边框粗细、混合圆角阵列、响应式对齐策略与字体的全部详情。
```javascript
figma.showUI(__html__, { visible: false }); // 借用隐藏的 UI 层执行系统拷贝 API

function extractNodeData(node) {
  // 1. 基础身份与相对坐标（弃用坑爹的绝对坐标）
  const data = { id: node.id, name: node.name, type: node.type };
  if ('width' in node && 'height' in node) { data.width = node.width; data.height = node.height; }
  if ('x' in node && 'y' in node) { data.x = node.x; data.y = node.y; }
  if ('rotation' in node && node.rotation !== 0) { data.rotation = node.rotation; }

  // 2. 布局系统 (Auto Layout 与响应式伸缩特性)
  if ('layoutMode' in node && node.layoutMode !== 'NONE') {
    data.layoutMode = node.layoutMode; // VERTICAL | HORIZONTAL
    data.primaryAxisAlignItems = node.primaryAxisAlignItems;
    data.counterAxisAlignItems = node.counterAxisAlignItems;
    data.itemSpacing = node.itemSpacing;
    data.paddingTop = node.paddingTop;
    data.paddingBottom = node.paddingBottom;
    data.paddingLeft = node.paddingLeft;
    data.paddingRight = node.paddingRight;
    // 决定它是 Hug contents (包裹容器) 还是 Fill container (撑满Expanded)
    data.layoutSizingHorizontal = node.layoutSizingHorizontal; 
    data.layoutSizingVertical = node.layoutSizingVertical;
  } else if ('constraints' in node) {
    data.constraints = node.constraints; // 散养绝对定位的吸附规则 (例如停靠右上角)
  }
  if ('layoutGrow' in node && node.layoutGrow !== 0) data.layoutGrow = node.layoutGrow; // flex-grow
  if ('layoutAlign' in node && node.layoutAlign !== 'INHERIT') data.layoutAlign = node.layoutAlign; // align-self

  // 3. 填充、描边与阴影样式
  if ('fills' in node && node.fills.length > 0) data.fills = node.fills;
  if ('strokes' in node && node.strokes.length > 0) {
    data.strokes = node.strokes;
    data.strokeWeight = node.strokeWeight; // 缺少线条粗细AI会瞎猜1px
    data.strokeAlign = node.strokeAlign;   // 线条走内描边或外描边
  }
  if ('effects' in node && node.effects.length > 0) data.effects = node.effects; // 提取 BoxShadow 参数
  if ('opacity' in node && node.opacity !== 1) data.opacity = node.opacity;

  // 4. 圆角计算（包含坑爹的上下左右分别混用不一样的圆角）
  if ('cornerRadius' in node) {
    if (node.cornerRadius !== figma.mixed && node.cornerRadius > 0) {
      data.cornerRadius = node.cornerRadius; 
    } else if (node.cornerRadius === figma.mixed) {
      data.cornerRadii = [node.topLeftRadius, node.topRightRadius, node.bottomRightRadius, node.bottomLeftRadius];
    }
  }

  // 5. 文本渲染专属字段 (不能少文字的对齐方式)
  if (node.type === 'TEXT') {
    data.characters = node.characters; 
    data.fontSize = node.fontSize;
    data.fontWeight = node.fontWeight;
    data.fontName = node.fontName;
    data.lineHeight = node.lineHeight;
    data.letterSpacing = node.letterSpacing;
    data.textAlignHorizontal = node.textAlignHorizontal; // 文本水平居中/左/右
    data.textAlignVertical = node.textAlignVertical;
    if (node.textDecoration && node.textDecoration !== 'NONE') data.textDecoration = node.textDecoration;
  }

  // 6. 递归提取可见的结构层树
  if ('children' in node) {
    const visibleChildren = node.children.filter(child => child.visible !== false);
    if (visibleChildren.length > 0) data.children = visibleChildren.map(extractNodeData);
  }
  return data;
}

const selection = figma.currentPage.selection;
if (selection.length > 0) {
  const result = selection.map(extractNodeData);
  figma.ui.postMessage({ type: 'copy', data: JSON.stringify(result, null, 2) });
} else {
  figma.closePlugin("❌ 错误：请先在画布中框选要提取元素的画板/Frame。");
}

figma.ui.onmessage = (msg) => { 
  if (msg.type === 'close') figma.closePlugin("✅ 超精简 JSON 已安全抵达系统剪贴板！速发给 AI 吧！"); 
};
```

**3. ui.html** (代替沙盒借手用你的电脑粘贴板把信息吐出来：
```html
<script>
  window.onmessage = async (event) => {
    const msg = event.data.pluginMessage;
    if (msg.type === 'copy') {
      const textarea = document.createElement('textarea');
      textarea.value = msg.data;
      document.body.appendChild(textarea);
      textarea.select();
      document.execCommand('copy');
      textarea.remove();
      parent.postMessage({ pluginMessage: { type: 'close' } }, '*');
    }
  };
</script>
```

#### 步骤三：工作流起飞

把这三个保存完毕后，任何一次你想还原一个牛逼的、极其复杂的页面设计逻辑：

1. **选中你要投喂的页面大框（甚至多个 Frame 同时选）。**
2. **右键** -> `Plugins` -> `Development` 里找你那个 `Export-Node-to-JSON`，点击它执行一次。（滴一声提示，此时毫无信息干扰的完美大段 JSON 就安静地躺在你的剪贴板缓存里了）。
3. **打开大模型对话框（Claude/Cursor 等）输入快捷键** `Ctrl + V`！
4. 附上这些页面的视觉排版全局截图。
5. 并且丢出指令给大模型：
   > *"你是一个顶级组件化工程师体系。这是一份界面排版参考截图，以及它配套的极其纯净的 Figma 节点参数表（已剥离了多余的无用元数据）。"*
   > *"要求：所有涉及到排版颜色、具体边距、透明度、混合阴影、字体尺寸要求及 Flex 延展与否 (`layoutSizingHorizontal` 等对应 Expanded/Center 等) 的数据，必须100%根据这段代码字典里严丝合缝翻译到 `[你的目标编写框架/例如Flutter]` 里，不要看着图片去瞎猜 `Container` 的宽度盲堆叠！"*

---

### 5.6 各方案对比总结

| 方案 | Token 消耗 | 信息完整度 | 操作门槛 | 真实可用性 / 流量限制 |
|---|---|---|---|---|
| **原始 SVG 直投** | ❌ 极大 | ⚠️ 缺文本 | 低 | 随手一拔即贴，但占用恐怖算力 |
| **瘦身后 SVG + 截图** | 🟡 中等 | ✅ 完整 | 中（需终端脚本跑一下） | **全开源脱机推荐，无限制。** |
| **Figma API REST 大网提取** | ✅ 娇小 | ✅ 最完整 | 高（需调参去重并设 Token） | **🚫 致命痛点：免费用户每月只有 6 次！** |
| **本地 Plugin API 榨汁手搓法** | ✅ 极其袖珍且直接包含组件思维 | ✅ **为前端渲染最针对量身定制** | 入门后极低 | **🔥 真正的王者极客神技：无需发网路链接，直接在内存里脱水榨取，要多少次取多少次，纯度最高。** |