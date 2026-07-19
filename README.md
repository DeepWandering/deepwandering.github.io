# deepwandering.github.io

Jia-Wei Xia 的个人学术主页。项目采用“手写静态主页 + Quarto 科研笔记”的轻量架构，由 GitHub Pages 直接发布仓库中的 HTML、CSS、图片和 Quarto 生成资源。

本文档是项目的总入口和维护手册。新的维护者或 AI agent 应先完整阅读本文，再根据需要阅读 [`notes/README.md`](notes/README.md) 中的笔记细则。

## 项目概览

| 项目 | 当前设定 |
| --- | --- |
| 线上域名 | <https://jiaweixia.cc.cd> |
| GitHub 仓库 | <https://github.com/DeepWandering/deepwandering.github.io> |
| 主分支 | `main` |
| 部署方式 | GitHub Pages 直接托管仓库中的静态文件 |
| 主页技术 | 原生 HTML + CSS，无 JavaScript 框架和包管理器 |
| 笔记技术 | Quarto QMD，渲染为同目录的 HTML |
| 内容语言 | 以中文为主，保留必要的英文术语 |
| 构建自动化 | 当前没有 GitHub Actions；生成后的 HTML 必须提交 |

## 功能与页面

| 页面 | 文件 | 作用 |
| --- | --- | --- |
| 主页 | `index.html` | 个人简介、头像、联系方式和研究兴趣 |
| 研究方向 | `research.html` | 展示主要研究方向 |
| 论文 | `publications.html` | 展示论文与学术成果 |
| 项目 | `projects.html` | 展示研究与开发项目 |
| 科研笔记 | `notes.html` | 按主题列出已发布笔记；目前由人工维护索引 |
| 笔记详情 | `notes/<category>/<note-slug>/index.html` | Quarto 或手工生成的独立笔记页面 |

全站共用 `style.css`。根页面直接包含主导航；Quarto 笔记通过 `_includes/nav-notes.html` 注入适用于深层路径的导航。

## 核心结构

```text
deepwandering.github.io/
├─ index.html                         # 主页
├─ research.html                      # 研究方向
├─ publications.html                  # 论文
├─ projects.html                      # 项目
├─ notes.html                         # 笔记总索引，需人工更新
├─ style.css                          # 根页面与 Quarto 笔记共用样式
├─ assets/
│  ├─ figures/                        # 站点级图片
│  └─ profile-placeholder.svg
├─ _includes/
│  ├─ nav-root.html                   # 根页面导航模板参考
│  └─ nav-notes.html                  # Quarto 笔记实际注入的导航
├─ notes/
│  ├─ _quarto.yml                     # Quarto 项目边界与正式 QMD 范围
│  ├─ _metadata.yml                   # 所有正式笔记的公共格式
│  ├─ _filters/sync-pagetitle.lua     # 同步正文标题与浏览器标签
│  ├─ .gitignore                      # Quarto 本地缓存忽略规则
│  ├─ README.md                       # 笔记目录的详细维护规范
│  ├─ _templates/note/index.qmd       # 新笔记模板
│  ├─ _shared/                        # 多篇笔记共用的网页资源
│  └─ <category>/<note-slug>/
│     ├─ index.qmd                    # 笔记源文件
│     ├─ index.html                   # 发布文件，由 Quarto 生成
│     ├─ index_files/                 # Quarto 生成的 CSS/JS/字体
│     ├─ assets/images/               # 页面直接使用的图片
│     ├─ attachments/                 # 本地附件，不上传
│     ├─ code/                        # 笔记相关代码
│     ├─ data/                        # 页面所需数据
│     └─ references/                  # 本篇笔记专用参考资料
├─ .gitignore                         # 全局本地附件忽略规则
├─ .gitattributes                     # 文本换行与二进制文件规则
├─ .nojekyll                          # 禁止 GitHub Pages 运行 Jekyll
└─ CNAME                              # 自定义域名
```

笔记目录的命名、资源归属和新增流程详见 [`notes/README.md`](notes/README.md)。

## 文件职责与事实来源

| 文件类型 | 是否手工编辑 | 是否提交 Git | 说明 |
| --- | --- | --- | --- |
| 根目录 `*.html` | 是 | 是 | 根页面没有构建步骤，HTML 本身就是源文件和发布文件 |
| `style.css` | 是 | 是 | 全站唯一主样式表 |
| 笔记 `index.qmd` | 是 | 是 | Quarto 笔记的内容事实来源 |
| 笔记 `index.html` | 否 | 是 | 修改 QMD 后重新渲染；不要长期手工维护生成内容 |
| 笔记 `index_files/` | 否 | 是 | GitHub Pages 运行页面所必需；应与 HTML 一起更新 |
| `assets/images/` | 是 | 是 | 页面显示所必需的图片 |
| `attachments/` | 是 | 否 | Word、PPT 等本地辅助资料，由 `.gitignore` 排除 |
| `.nb`、`.docx`、`.pptx`、`.xlsx` | 是 | 否 | 本地作者文件，不属于发布站点 |
| `.quarto/` | 否 | 否 | Quarto 缓存，可随时重新生成 |

例外：`notes/mechanics/periodic-thin-film-buckling/index.html` 目前只有 HTML、没有对应 QMD，因此修改该笔记前应先决定是否补建源文件。

## Quarto 关键设定

### 配置继承链

1. `notes/_quarto.yml` 将 `notes/` 定义为 Quarto 项目。
2. `metadata-files` 显式加载 `notes/_metadata.yml`。
3. 单篇 `index.qmd` 的 YAML 可以覆盖公共配置，但应只覆盖确有必要的字段。

`notes/_quarto.yml` 批量包含 `**/*.qmd`，并排除 `_templates/**`，因此模板不会被当作正式页面渲染。

`notes/_metadata.yml` 当前统一设定：

- `lang: zh-CN`；
- `_filters/sync-pagetitle.lua` 将每篇 QMD 的 `title` 同步到浏览器标签页；
- Cosmo 主题；
- 正文目录位于 body，深度为 3；
- 不显示章节编号；
- 加载站点根路径 `/style.css`；
- 在正文前注入 `../_includes/nav-notes.html`；
- `execute.eval: false`，默认不执行代码块。

不要在单篇 QMD 中无意间定义完整的 `format:`，否则可能覆盖公共 HTML 格式。需要执行代码的笔记必须在自身 YAML 中显式覆盖 `execute.eval`，并在提交前确认结果可复现。

### URL 与路径约定

- 分类、笔记目录和资源文件名优先使用小写英文及连字符。
- 中文标题写在 QMD 的 `title` 中，不写入 URL。
- 笔记链接指向目录，例如 `notes/semiconductor-devices/exercise-collection/`。
- 深层笔记导航和站点 CSS 使用 `/` 开头的站点根绝对路径。最终验证必须通过 HTTP 服务完成，不能只双击本地 HTML。
- `notes/the-Laudau-level-under_magnetic-field-Graphene.html` 和 `notes/buckling-note-01.html` 是旧网址重定向页，应保留以兼容历史链接。

## 本地维护流程

### 环境要求

- Git；
- Quarto CLI；
- 一个本地静态 HTTP 服务，用于最终检查根绝对路径。

项目没有 Node.js、Python 包或其他依赖安装步骤。Python 仅可选用于启动本地静态服务器。

### 修改根页面

1. 修改对应的根目录 HTML。
2. 如涉及全站视觉规则，修改 `style.css`。
3. 保持所有根页面导航链接一致；导航参考位于 `_includes/nav-root.html`。
4. 通过本地 HTTP 服务检查桌面与窄屏布局。

### 新建笔记

1. 选择一个稳定的主分类目录。
2. 将 `notes/_templates/note/` 复制为 `notes/<category>/<note-slug>/`。
3. 在 `index.qmd` 中设置 `title`、`author`、`date`、`categories` 和 `tags`。
4. 页面图片放入本笔记的 `assets/images/`。
5. Word、PPT 等本地资料放入 `attachments/`，不要提交。
6. 渲染 QMD。
7. 在 `notes.html` 中新增目录链接和展示标题。
8. 检查生成页面、链接与 Git 状态。

单篇渲染命令（从仓库根目录执行）：

```powershell
quarto render notes/<category>/<note-slug>/index.qmd
```

例如：

```powershell
quarto render notes/semiconductor-devices/exercise-collection/index.qmd
```

需要重建所有笔记时，逐文件渲染：

```powershell
Get-ChildItem notes -Recurse -Filter index.qmd |
  Where-Object { $_.FullName -notmatch '[\\/]_templates[\\/]' } |
  ForEach-Object { quarto render $_.FullName }
```

当前 Quarto 1.9.38 在项目中存在多个同名 `index.qmd` 时，直接执行 `quarto render notes` 可能发生输出清理冲突。提交前使用上述逐文件命令，并确认每篇 `index.html` 都存在。

实时编辑可以使用：

```powershell
quarto preview notes/<category>/<note-slug>/index.qmd
```

`preview` 用于编辑反馈；提交前仍应执行一次 `render`。修改全局 `_quarto.yml`、`_metadata.yml`、导航或样式后，应重新渲染所有受影响笔记。

### 本地预览完整站点

从仓库根目录启动静态服务器，例如：

```powershell
python -m http.server 8000
```

然后访问 `http://127.0.0.1:8000/`。不要以 `file://` 方式作为最终验收，因为 `/style.css` 和根导航依赖站点根路径。

## 提交前验收清单

- 根页面和新增笔记入口可访问。
- Quarto 页面包含主导航、`/style.css` 和正文目录。
- 浏览器 `<title>` 等于 QMD 中的 `title`，而不是 `index`。
- QMD、HTML、`index_files/` 和页面图片保持同步。
- `notes.html` 使用 `/` 作为 URL 分隔符，且链接到笔记目录。
- 没有误提交 `attachments/`、Office 文件、Notebook 或 `.quarto/`。
- 没有遗留的无引用生成文件。
- `git diff --check` 通过。
- `git status --short` 中的每个文件都能解释其用途。

建议执行：

```powershell
git diff --check
git status --short
git status --short --ignored
```

如果不确定某个附件是否被忽略：

```powershell
git check-ignore -v <path>
```

## Git 提交说明规范

项目采用接近 Conventional Commits 的格式：

```text
<type>(<scope>): <summary>

<可选正文：解释背景、关键改动和迁移影响>

<可选页脚：BREAKING CHANGE、关联问题等>
```

### Type

| Type | 用途 |
| --- | --- |
| `feat` | 新增站点功能或用户可见能力 |
| `fix` | 修复页面、链接、样式或构建问题 |
| `content` | 新增或实质更新科研内容、笔记、论文、项目 |
| `refactor` | 调整结构但不改变主要内容含义 |
| `docs` | 仅修改维护文档或说明 |
| `style` | 纯视觉或格式调整，不改变内容语义 |
| `build` | Quarto、GitHub Pages、生成流程或依赖配置 |
| `chore` | 忽略规则、仓库维护和其他杂项 |

### Scope

优先使用稳定范围：`site`、`notes`、`quarto`、`assets`、`nav`、`readme`。范围可省略，但不要使用临时文件名作为范围。

### Summary 与正文

- Summary 使用简洁英文祈使句，说明结果，不写过程；建议不超过 72 个字符。
- 一个提交只表达一个可回滚的逻辑变更。
- 大幅目录迁移、URL 变化或生成策略变化必须写正文。
- 正文优先说明“为什么改”“核心变化”“如何验证”。
- 破坏兼容性的变更使用 `BREAKING CHANGE:` 页脚。

示例：

```text
refactor(notes): isolate notes by category and topic
content(notes): add semiconductor exercise collection
fix(quarto): inherit page title and shared navigation
docs(readme): document maintenance and release workflow
chore(assets): ignore local authoring attachments
```

较大提交正文示例：

```text
refactor(notes): reorganize notes for long-term maintenance

- isolate each note and its web assets
- add shared Quarto metadata and templates
- preserve legacy URLs with redirects

Validation:
- render each affected `index.qmd`
- git diff --check
```

## 发布到 GitHub Pages

当前没有服务器端或 GitHub Actions 构建。发布链路是：

```text
QMD/HTML/CSS 修改
  -> 本地 Quarto render
  -> 本地 HTTP 验证
  -> 提交源文件与生成文件
  -> push origin main
  -> GitHub Pages 发布静态文件
```

标准命令：

```powershell
git add <明确的文件或目录>
git commit -m "<type>(<scope>): <summary>"
git push origin main
```

避免未经检查直接使用 `git add .`。提交前先确认远程分支没有新提交；如有并发更新，应先获取并整合远程变化。

`.nojekyll` 必须保留，否则 GitHub Pages 可能忽略以下划线开头的 Quarto 或站点资源。`CNAME` 必须保持为 `jiaweixia.cc.cd`，除非明确迁移域名。

## AI agent 操作约束

接手任务时按以下顺序工作：

1. 阅读本 README；涉及笔记时继续阅读 `notes/README.md`。
2. 执行 `git status --short`，把现有未提交修改视为用户工作，不覆盖或回退。
3. 明确文件事实来源：根 HTML 可手改；Quarto HTML 应由 QMD 生成。
4. 修改 QMD 后必须渲染并检查生成物；不要只提交 QMD。
5. 不得强行跟踪被忽略的 Office、Notebook、附件或缓存。
6. 不要修改 `CNAME`、删除 `.nojekyll`、改写公共导航或改变 URL，除非任务明确要求。
7. 不要删除旧网址重定向页，除非已经确认没有兼容需求。
8. 提交或推送前报告并验证所有拟提交文件。

## 已知限制

- `notes.html` 目前不是自动列表，新笔记需要人工登记。
- GitHub Pages 不会自动将 QMD 转换为 HTML，生成文件必须提交。
- 屈曲笔记目前没有 QMD 源文件。
- 屈曲页面引用的 `/assets/figures/buckling-profile.png` 当前缺失。
- Quarto 1.9.38 对多个同名 `index.qmd` 的项目级批量渲染可能发生输出清理冲突；使用逐文件渲染。
- Quarto 1.9.38 渲染 `zh-CN` 时可能提示缺少部分翻译文件，但不影响中文正文、导航和样式生成。

## 换行约定

`.gitattributes` 将 HTML、CSS、JavaScript、QMD、Markdown、YAML、CNAME 和 SVG 统一为 LF，并将常见图片和字体标记为二进制。若历史文件出现换行噪声，可在确认影响范围后执行：

```powershell
git add .gitattributes
git add --renormalize .
git status
```

不要在未检查差异时直接提交一次全仓库换行归一化。
