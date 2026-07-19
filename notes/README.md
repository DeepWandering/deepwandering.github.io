# 笔记目录维护规范

笔记采用“分类目录 / 单篇笔记目录”的组织方式。每篇笔记都是一个独立单元，网页入口统一命名为 `index.qmd`，生成页面统一命名为 `index.html`。

```text
notes/
├─ <category>/
│  └─ <note-slug>/
│     ├─ index.qmd
│     ├─ index.html
│     ├─ index_files/       # Quarto 生成的网页依赖
│     ├─ assets/images/     # 页面直接使用的图片
│     ├─ attachments/       # 本地附件，不上传仓库
│     ├─ code/              # 笔记相关代码
│     ├─ data/              # 页面需要的数据
│     └─ references/        # 本篇笔记专用参考资料
├─ _shared/                 # 多篇笔记共同使用的网页资源
├─ _templates/note/         # 新笔记模板
├─ _filters/                # Quarto/Pandoc 公共过滤器
├─ _quarto.yml              # Quarto 项目与正式 QMD 范围
└─ _metadata.yml            # Quarto 公共配置
```

## 命名规则

- 分类和笔记目录使用小写英文及连字符，例如 `condensed-matter/graphene-landau-levels`。
- 中文标题写在 `index.qmd` 的 YAML 元数据中，不写入路径。
- 一篇笔记只存放在一个主分类中；跨领域信息使用 `categories` 或 `tags` 表示。

## 文件归属

- 网页正文和构建所需资源可以提交仓库。
- Word、PowerPoint 等辅助资料统一放入对应笔记的 `attachments/`，该目录已被 Git 忽略。
- Mathematica Notebook 等仅供本地维护的文件放入 `code/`，其中 `.nb` 文件已被 Git 忽略。
- 不要手工编辑 `index_files/`，其中内容由 Quarto 生成。

## 新建笔记

复制 `_templates/note/` 到对应分类下，并将新目录命名为稳定的英文 slug。仅在实际需要时创建 `assets/`、`attachments/`、`code/`、`data/` 或 `references/`，避免保留空目录。

### 自动继承公共格式

`notes/_quarto.yml` 将 `notes/` 定义为一个 Quarto 项目，并通过 `metadata-files` 显式加载 `_metadata.yml`。放在 `notes/` 内的正式笔记会自动继承其中的公共配置，包括：

- 中文语言和 Cosmo 主题；
- 主站 `/style.css`；
- 主站导航栏；
- 浏览器标签页标题（`_filters/sync-pagetitle.lua` 自动读取笔记的 `title`）；
- 正文目录、目录深度和代码执行设置。

新笔记只需在自己的 `index.qmd` 中定义标题、作者、日期、分类和标签。不要重复填写公共格式，也不要在笔记中重新定义完整的 `format`，除非确实需要覆盖默认格式。

建议从仓库根目录渲染单篇笔记：

```powershell
quarto render notes/<category>/<note-slug>/index.qmd
```

例如：

```powershell
quarto render notes/semiconductor-devices/exercise-collection/index.qmd
```

生成的 `index.html` 和 `index_files/` 位于该笔记的 `index.qmd` 旁边。

需要重建全部正式笔记时，应逐文件渲染：

```powershell
Get-ChildItem notes -Recurse -Filter index.qmd |
  Where-Object { $_.FullName -notmatch '[\\/]_templates[\\/]' } |
  ForEach-Object { quarto render $_.FullName }
```

`_templates/` 已被排除。当前 Quarto 1.9.38 对多个同名 `index.qmd` 直接执行 `quarto render notes` 时可能发生输出清理冲突，因此不将该命令作为发布流程。

以下情况不会按预期继承公共配置：

- QMD 文件位于 `notes/` 项目之外；
- 子目录中的 `_metadata.yml` 覆盖了公共选项；
- 笔记自身的 YAML 覆盖了公共选项，尤其是完整的 `format` 配置。
