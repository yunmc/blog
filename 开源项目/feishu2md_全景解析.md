# feishu2md 全景解析

## 1. 项目基础信息
- **项目名称**：feishu2md
- **GitHub 地址**：[Wsine/feishu2md](https://github.com/Wsine/feishu2md)
- **开发语言**：Go
- **项目定位**：一键将飞书（Feishu/Larksuite）文档下载并转换为 Markdown 格式的工具。

## 2. 核心功能与应用场景
解决飞书内容闭环、不易导出至其他平台的问题。非常适合创作者和开发者将自己在飞书知识库中的文章，批量导出到个人博客（如 Hexo、Hugo、VuePress）、GitHub 或本地知识管理软件（如 Obsidian）中。

**主要特性：**
- **多途径运行**：支持命令行（CLI）直接执行，也提供了 Docker 版本。
- **素材与图片本地化**：
  - 文档中的图片及附件会自动被下载。
  - **存储形式**：默认保存在与 Markdown 同级的 `static/` 文件夹下（文件名为飞书的图片 token），可通过 `config.json` 将 `image_dir` 自定义为 `assets` 或 `images` 等其它名称。
  - **语法替换**：转换后的 Markdown 内部会自动使用**本地相对路径引用**（例如 `![img](static/img-v3-xxxxx.png)`），实现完美的本地离线预览。
  - **灵活配置**：如果不想慢吞吞下载图片，可通过开启 `skip_img_download` 仅极速转换文本文档。
- **规模化导出**：
  - 支持单篇文档 URL 下载。
  - 支持 `batch` 命令批量下载某飞书文件夹内的所有文档。
  - 支持 `wiki` 命令批量下载指定的整个知识库文档。

## 3. 使用前置条件（API 鉴权）
由于飞书严格的数据安全策略，使用者无法直接用账号密码拉取，需要：
1. 登录飞书开发者后台，创建一个“企业自建应用（个人使用即可，随意填个名字）”。
2. 开通核心的读权限：包含“查看新版文档”、“下载云文档中的图片和附件”、“查看和管理云空间所有文件”、“查看知识库”等。
3. 提取应用的 `App ID` 和 `App Secret` 获取凭证。
4. 使用 `feishu2md config --appId <your_id> --appSecret <your_secret>` 进行本地初始化配置。

## 4. 项目状态
值得注意的是，原作者因个人习惯变更已较少使用飞书，因此项目目前处于**转交开源社区维护**的状态，接受并欢迎合并有能力的开发者的 PR。

## 5. 原理与技术实现细节
整个项目的核心实现非常清晰，本质上是一个**基于飞书开放平台 OpenAPI 的数据提取与格式抹平流水线**。以下是其关键的技术构成及运转流程：

### 5.1 整体技术栈
- **语言**：Go 1.21+ (体现了跨平台跨架构分发 CLI 的优势)
- **API 交互**：使用了开源社区主流的 `github.com/chyroc/lark` 飞书 / Larksuite Go SDK。
- **渲染与 CLI**：CLI 层使用 `urfave/cli`；表格与排版的辅助使用了 `olekukonko/tablewriter`；而提供 Web 界面的 Docker 镜像容器则是包装了一个极简的 `gin-gonic/gin` 服务。

### 5.2 核心运转流程 (Pipeline)
从输入一条文档 URL，到最后输出 Markdown 文件，`feishu2md` 的代码流流转如下：

1. **统一的 URL 解析**：
   CLI 或 Web 接受用户输入的文档/文件夹/Wiki 链接。代码内部对 URL 解析出具体的 `Token`（飞书内部标识资源的唯一 ID，如 `docxtoken`）。
2. **鉴权与客户端初始化**：
   使用本地 `config.json` 里的 App ID/Secret 构建具备访问权限的 `core.Client`（`lark.New(...)`），并在发送请求时使用限流中间件防止被飞书 API 熔断。
3. **获取树状节点数据（AST 遍历）**：
   飞书新版文档（Docx）的数据结构本质上是一棵包含不同 `Block` 类型的 DOM 树。通过循环调用 `GetDocxBlockListOfDocumentReq`，将分页获取到的所有 Block 平铺并建立映射（`blockMap`）。
4. **解析与 Markdown 映射 (`parser.go`)**：
   对于不同种类的 `Block` 进行 DFS 递归 / Switch-Case 判别映射：
   - `Text`：提取文本及样式标记（加粗、斜体等转换为 Markdown 语法）。
   - `Code`：有一张映射表把类似 `lark.DocxCodeLanguageBash` 转化为 `bash` Markdown block 标识。
   - `Image` / `File`：如果是图片/附件节点，直接提取其 `FileToken` 压入下载队列。
   - `Table`：映射并渲染为原生的 Markdown 表格。
   - `Quote` / `Toggle`：利用原生的 HTML tag 或 Markdown 嵌套符号平替。
5. **并发 / 批量素材拉取**：
   在文档块基本解析拼装完毕后，如果不是仅解析纯文本，根据搜集到的所有 `imgToken` 等，并发调用云空间（Drive）素材拉取接口 `DownloadDriveMedia` 保存为本地实际格式的文件（放入 `static/` ）。
6. **最终落盘**：
   将全部拼接好的 Markdown 字符串（引用图片处更换成相对路径）落盘（利用 `WriteFile`）到用户的指定或默认目录下。对于 Batch 批量模式，实际上就是先由文件夹/Wiki 获取文件 Token 清单，再循环调用这一核心流水线。