# Oldsheep 博客

<https://oldsheep113.github.io/>

这是博客的**源文件**仓库。网页由 Hugo 根据 `content/` 里的 Markdown 自动生成，
不需要手动编辑 HTML。

## 两个脚本

| 文件 | 用途 |
| --- | --- |
| `本地预览.cmd` | 在本机起预览服务，地址 <http://localhost:1313/>，改完自动刷新 |
| `发布到网站.cmd` | 提交并推送到 GitHub，1-2 分钟后网站自动更新 |

文章和图片用你自己的 Markdown 编辑器写，写完放到 `content/` 下就行。
这两个脚本只负责"预览"和"同步"。

## 目录说明

| 目录 | 作用 |
| --- | --- |
| `content/post/` | 技术文章 |
| `content/notes/` | 日志、笔记 |
| `content/about.md` | 关于页 |
| `layouts/` | 页面模板（决定网页长什么样） |
| `static/css/` | 样式 |
| `static/katex/` | 数学公式渲染库（本地托管） |
| `static/images/` | 文章插图 |
| `hugo.toml` | 站点配置（标题、菜单、域名） |
| `tools/` | 本机用的 Hugo 程序，不进版本库 |
| `public/` | 构建产物，自动生成，**不要手改、不会提交** |

## 写一篇新文章

技术文章放 `content/post/`，日志笔记放 `content/notes/`，文件名随便取，
用你习惯的 Markdown 编辑器新建即可。开头必须有这一段（front matter）：

```markdown
---
title: 文章标题
date: 2026-10-01T20:00:00+08:00
tags: ["化纤", "备忘"]
categories: ["技术"]
---

正文从这里开始，用 Markdown 写。

<!--more-->

这一行之后的内容不会出现在首页摘要里。
```

- `date` 决定排序和显示日期，格式 `年-月-日T时:分:秒+08:00`
- `<!--more-->` 之前的内容会作为首页摘要，可省略
- 草稿加上 `draft: true`，写好后删掉这行才会发布

公式用 KaTeX 语法，行内用 `$...$`，独立成行用 `$$...$$`：

```markdown
$$\frac{A}{V}\propto\frac{1}{d}$$
```

## 插入图片

图片一律放在 `static/images/` 下，子目录也可以（例如 `static/images/2026/`）。
正文里用根路径引用：

```markdown
![说明文字](/images/图片名.png)
```

也就是：文件放 `static/images/foo.png`，Markdown 里就写 `/images/foo.png`。
以 `/` 开头表示从网站根目录算起，所以不管这篇文章在第几层目录都能用。

图片文件会跟着文章一起提交，所以记得最后双击一次 `发布到网站.cmd`。

## 本地预览

**双击 `本地预览.cmd`**，然后用浏览器打开 <http://localhost:1313/>。
改完文章保存后页面会自动刷新；想停止就关掉那个黑窗口。

脚本会在这些位置找 Hugo：系统 PATH 里的 `hugo`、项目内的 `tools\hugo\hugo.exe`。
都找不到时会提示你。

命令行方式等价于：

```
tools\hugo\hugo.exe server
```

## 发布

**双击 `发布到网站.cmd`** 就行，它会：

1. 列出你改了哪些文件
2. 问你要一句更新说明，随便写，或者直接回车用默认的「更新博客」
3. 自动提交并推送到 GitHub

推送后约一到两分钟，访问 <https://oldsheep113.github.io/> 就能看到更新。
构建进度可以在仓库的 **Actions** 标签页查看。

如果你更习惯敲命令，下面三条是等价的：

```
git add -A
git commit -m "新增一篇笔记"
git push
```

**注意：不要手动修改 `public/` 或仓库里生成出来的 HTML 文件**，
它们每次构建都会被覆盖。要改样式就改 `layouts/` 和 `static/css/`。

## 部署方式

本站用 GitHub Actions 构建并发布。仓库 **Settings → Pages → Source** 需要设置为
**GitHub Actions**（而不是某个分支）。
