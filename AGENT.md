# AGENT.md

本文件面向在仓库中工作的 AI Agent /
自动化工具，说明本项目的结构、约定与改动准则。 面向人类读者的简介见
`README.md`。

## 项目概览

- 类型：个人静态博客（Hugo 站点），中文内容为主。
- 站点地址：<https://blog.best-fiber.shop/>（`hugo.toml` 的 `baseURL`）。
- 自定义域名：`static/CNAME`。
- 部署：GitHub Pages，通过 GitHub Actions 自动构建发布（`master` 分支 push
  触发）。
- 作者：`oldsheep113`，内容涉及化纤/原液着色技术笔记、生活日志与随笔。

## 技术栈

| 项目          | 说明                                                                      |
| ------------- | ------------------------------------------------------------------------- |
| 静态生成器    | Hugo，**0.123.7 extended**（CI 中固定，见 `.github/workflows/hugo.yml`）  |
| 主题          | Hyde（Poole/Hyde 系列），**已内联进本仓库，没有 `themes/` 目录**          |
| Markdown 数学 | 本地 KaTeX（`static/katex/`），客户端 `auto-render` 渲染                  |
| 字体/图标     | `static/fonts/`（Selawik）、内联 SVG 图标（`layouts/partials/icon.html`） |

> ⚠️ `hugo.toml` 中的 `params.theme = "theme-base-09"` 不是 Hugo
> 主题引用，它会被写入 `<body class="...">`（见
> `layouts/_default/baseof.html`），用于切换 Hyde 的配色变体。
> 不要把它改名为主题配置，也不要新增 `themesDir`。

## 目录结构

```
hugo.toml                 站点配置：baseURL、菜单（menu.main）、站点参数
archetypes/default.md     新建文章的 front matter 模板
content/
  about.md                关于页
  links.md                友链页（front matter 里 layout: links）
  post/                   随笔/杂项文章
    _index.md
    first.md
  notes/                  技术笔记
    _index.md
    fibre/                化纤相关笔记（含 html 附件，如 spec-change-process-window-calculator.html）
  diary/                  生活日志，文件名即日期，如 20261002.md
    _index.md
data/links.yaml           友链数据（列表页渲染用）
layouts/
  404.html
  index.html              首页：聚合 post / notes / diary 三个 section
  _default/
    baseof.html           骨架：sidebar + main block + footer block
    single.html           文章页
    list.html             section 列表页
    standalone.html       仅输出 .Content（无站点外壳）
    links.html            友链页专用布局
  partials/
    head.html             <head>：CSS、RSS、KaTeX 加载与渲染脚本
    sidebar.html          侧边栏导航（按 menu.main 生成）
    icon.html             按菜单 identifier 输出内联 SVG 图标
static/
  CNAME  css/  fonts/  images/diary/  katex/
```

## 内容与写作约定

- 语言为 `zh-cn`；正文以中文为主，模板中日期格式为 `2006年1月2日`。
- Front matter 使用 YAML（`---` 包裹）。基本字段：

  ```yaml
  ---
  title: 标题
  date: 2026-10-02
  ---
  ```

- 可选字段：`slug`、`aliases`（旧链接重定向）、`layout`（例如
  `links`）、`draft`、`tags`、`categories`。
- 摘要分隔符使用 `<!--more-->`，超出的内容才会生成 “Read More…” 链接。
- 文件名约定：
  - `content/diary/` 下用 `YYYYMMDD.md`（如 `20261002.md`）。
  - `content/notes/` 下按主题分目录（现有 `fibre/`），中文文件名允许。
- 新增文章建议用 `hugo new`（会套用 `archetypes/default.md`），例如
  `hugo new content/diary/20261020.md`。新建后记得把 `draft: true` 改为 `false`
  或删除。
- 图片放 `static/images/<section>/`，引用时用绝对路径，如
  `/images/diary/20261003.jpg`。
- 数学公式：行内 `$...$`，块级 `$$...$$`（`head.html` 中的 KaTeX `auto-render`
  分隔符配置）。 含下标 `_` 的公式建议放在 `$$...$$` 块中，避免被 Markdown
  当作强调处理。
- 友链：在 `data/links.yaml` 追加条目（`name`、`url`，可选
  `description`、`avatar`）， 无需改动模板。

## 模板与主题约定

- 所有页面都经由 `layouts/_default/baseof.html`，需定义 `{{ define "main" }}`；
  纯内容页可用 `standalone.html` 或直接覆盖 `main` block。
- 首页由 `layouts/index.html` 硬编码聚合 `post`、`notes`、`diary` 三个 section；
  新增顶级 section 时需同步修改此文件。
- 侧边栏导航完全由 `hugo.toml` 的 `[[menu.main]]` 生成：
  - URL 以 `http` 开头 → 归入“外部链接”分组，新窗口打开；
  - 其他 → 归入站内导航，并按 `hasPrefix` 计算 `active` 高亮。
- 导航图标由 `layouts/partials/icon.html` 按 `identifier`
  匹配（`home`/`notes`/`diary`/
  `links`/`about`/`github`/`bilibili`）。**新增菜单项必须同时在该文件中补充 SVG
  分支**， 否则图标为空。
- 主题文件（`static/css/hyde.css`、`poole.css`、`custom.css` 等）属于上游 Hyde
  主题内容， 站点级样式调整优先写入 `static/css/custom.css`，减少与上游差异。
- 站点 UI 文案（“Read More…”、404
  文案、页脚版权）为英文/固定文本，改动时注意同步所有布局。

## 构建与部署

本地预览（含草稿）：

```powershell
hugo server -D
```

生产构建（与 CI 一致）：

```powershell
hugo --gc
```

- 产物目录 `public/`（已被 `.gitignore` 忽略，提交前确认没有误加）。
- 不要手动提交 `resources/`、`.hugo_build.lock`。
- 部署流程：push 到 `master` → Actions 构建（Hugo 0.123.7 extended）→ 上传
  `public/` → `actions/deploy-pages` 发布。
- 本地存在若干辅助脚本（`本地预览.cmd`、`发布到网站.cmd`、`scripts/`），已在
  `.gitignore` 中排除，不要把它们加入版本库。
- 若需更改 Hugo 版本，必须同时更新 `hugo.toml` 之外的 CI 环境变量 `HUGO_VERSION`
  与 `peaceiris/actions-hugo` 的 `hugo-version`（`.github/workflows/hugo.yml`
  中两处）。

## 修改准则（Agent 必读）

1. **先确认改动是否影响构建**：任何模板改动都应在本地跑一次
   `hugo --gc`，确认无模板报错。
2. 保持 front matter 为 YAML，不要把 4 空格缩进改成 tab。
3. 不要引入 npm/bundler 等构建管线；本项目是纯 Hugo + 静态资源。
4. 不要重命名或移动 `static/` 下的既有资源路径，已发布文章可能引用它们。
5. 删除/重命名文章时，为保持旧链接可用，应在 front matter 的 `aliases`
   中加回原路径 （参见 `content/notes/fibre/色母粒加入百分比换算原理.md`）。
6. 与主题无关的业务内容改在 `content/`，样式改在 `custom.css`，结构改在
   `layouts/`。
7. 内容写作不要改动作者的原始表述与语气；仅做格式或结构调整时保持语义不变。
8. 提交信息与注释保持简洁，中文可用。

## 验证清单

改动后逐项确认：

- [ ] `hugo --gc` 成功，无 template error / 警告。
- [ ] 首页仍聚合 `post`、`notes`、`diary` 的文章。
- [ ] 侧边栏站内/站外分组与高亮正确，图标均能显示。
- [ ] 含公式的文章（如 `content/post/first.md`）渲染正常。
- [ ] 友链页从 `data/links.yaml` 正确读取。
- [ ] `robots`/RSS、`static/CNAME` 未被破坏。
- [ ] 未把 `public/`、`resources/`、本地脚本提交进仓库。
