---
title: 【技术备忘录】Markdown 语法模板
date: 2025-08-15 23:00:00 +0800
description: 这篇文章主要记录一些在网站blog编写与维护中常用的Markdown语法模板，方便以后直接复制使用。
categories: [Technical, Memorandum]
tags: [memorandum, blog website mainteance]     # TAG names should always be lowercase
toc: true
# comments: true
pin: true
math: true
mermaid: true
# media_subpath: /assets
published: true
sitemap: false
image:
  path: assets/img/covers/badlands_thunderstorms.png
  alt: 雷暴中的恶地国家公园。雷暴轰击的不只是土地，还有我龟裂的内心。
---

## Chirpy 主题的官方排版模板
> [Chirpy 官方排版展示](https://chirpy.cotes.page/posts/text-and-typography/)
> [Chirpy 官方排版示例源码](https://github.com/cotes2020/jekyll-theme-chirpy/blob/master/_posts/2019-08-08-text-and-typography.md)
{: .prompt-tip }

## 交叉引用

> 本博客站使用kramdown格式的交叉引用。
{: .prompt-tip }

下面给出几种常用的交叉引用写法（推荐使用 kramdown 的属性 ID）：

```markdown
# 标题示例 {#heading_example}

[跳到标题示例](#heading_example)
```

渲染后效果（页面内跳转）：

要被引用的段落或标题后加上 `{#id}` 即可将该元素赋予 id：

```markdown
这是一个段落，想让它被链接引用。 {#para_example}

[跳到这个段落](#para_example)
```

如果只想给行内的某个词或短语做锚点，直接使用 HTML 更兼容：

```html
<span id="inline_example">关键术语</span>

[跳到关键术语](#inline_example)
```

跨页面引用需要包含页面路径，注意是在最终部署的网站里的路径，所以需要采用针对当前博文路径的URL相对路径：

```markdown
[跳到另一篇文章的本地预览](../Website_Mainteance/#local_preview)
```

注意与最佳实践：
- 避免使用空的 `<a name="..."></a>`（这会触发 htmlproofer 的 “'a' tag is missing a reference” 报错）；用 `{#id}` 或 `<span id="...">` 替代。
- 尽量使用 ASCII 小写、连字符或下划线（例如 `local_preview`），避免中文或空格作为 id；这能提高兼容性并避免自动 slug 与手动 id 不一致的问题。
- 如果页面有固定头部，锚点跳转后可能被遮挡，建议使用 CSS 的 `scroll-margin-top` 为标题设置偏移（本仓库已示例实现）。

以上示例可以直接复制到文章中测试；本文件顶部的样例演示了代码块写法和渲染写法两种形式。

## LaTeX公式

本博客支持使用 MathJax / LaTeX 语法书写数学公式（请在文章 Front Matter 中启用 `math: true`）。下面列出常见写法和示例，方便复制使用。

1) 行内公式（与文本同行）

```markdown
这是行内公式示例：$E = mc^2$，或使用转义形式：\(E = mc^2\)。
```

这是行内公式示例：$E = mc^2$，或使用转义形式：\(E = mc^2\)。

注意：行内公式不能有上下空行；在列表项中若以 `$` 开头需要对第一个 `$` 进行转义：`\$...$$`（见下面的列表示例）。

2) 行间/独立公式（居中显示）

```markdown
$$
\int_{-\infty}^{\infty} e^{-x^2}\,dx = \sqrt{\pi}
$$
```

$$
\int_{-\infty}^{\infty} e^{-x^2}\,dx = \sqrt{\pi}
$$

3) 编号公式与引用

```markdown
$$
\begin{equation}
  f(x) = \sum_{n=0}^{\infty} a_n x^n
  \label{eq:power_series}
\end{equation}
$$

$$
\begin{equation}
  f(x) = \sum_{n=0}^{\infty} a_n x^n
  \label{eq:power_series}
\end{equation}
$$

可以在正文中用 `\eqref{eq:power_series}` 引用：见公式 \eqref{eq:power_series}。
```

4) 多行对齐（align 环境）

```markdown
$$
\begin{aligned}
  (a+b)^2 &= a^2 + 2ab + b^2 \\
          &= a^2 + b^2 + 2ab
\end{aligned}
$$
```

$$
\begin{aligned}
  (a+b)^2 &= a^2 + 2ab + b^2 \\
          &= a^2 + b^2 + 2ab
\end{aligned}
$$

5) 分段函数（cases）

```markdown
$$
f(x)=\begin{cases}
  x^2, & x\ge 0 \\
  -x,  & x<0
\end{cases}
$$
```

$$
f(x)=\begin{cases}
  x^2, & x\ge 0 \\
  -x,  & x<0
\end{cases}
$$

6) 矩阵（matrix / pmatrix）

```markdown
$$
A = \begin{pmatrix}
  a & b \\
  c & d
\end{pmatrix}
$$
```

$$
A = \begin{pmatrix}
  a & b \\
  c & d
\end{pmatrix}
$$

7) 常见运算符、导数、积分、求和、极限等

```markdown
导数：\(\frac{d}{dx} e^{x} = e^{x}\)

积分：$$\int_0^1 x^2 \, dx = \frac{1}{3}$$

求和：$$\sum_{k=1}^{n} k = \frac{n(n+1)}{2}$$

极限：$$\lim_{x\to 0} \frac{\sin x}{x} = 1$$
```

导数：\(\frac{d}{dx} e^{x} = e^{x}\)

积分：$$\int_0^1 x^2 \, dx = \frac{1}{3}$$

求和：$$\sum_{k=1}^{n} k = \frac{n(n+1)}{2}$$

极限：$$\lim_{x\to 0} \frac{\sin x}{x} = 1$$

8) 在列表中使用公式（注意第一个 `$` 需转义）

```markdown
1. \$$E = mc^2$$ — 这样可以在项目符号中正确渲染行间公式。
2. \$\(a+b=c\)$ — 行内公式同理需要转义第一个美元符号。
```

1. \$$E = mc^2$$ — 这样可以在项目符号中正确渲染行间公式。
2. \$\(a+b=c\)$ — 行内公式同理需要转义第一个美元符号。

9) 调整显示与排版的小贴士

- display 公式前后必须保留空行（否则会被当成普通段落）。
- 使用 `\displaystyle` 可以在行内或某些环境中强制较大的显示样式，例如：`$\displaystyle\sum_{n=0}^\infty x^n$`。
- 常用数学函数请使用 `\sin, \cos, \ln, \exp` 等命令以获得正确字体与间距。

10) MathJax / Chirpy 相关

- 本仓库的主题已包含 MathJax 配置（参考 `assets/js/data/mathjax.js`），并要求在文章 Front Matter 设置 `math: true` 才会启用。若公式不渲染，请确认构建时没有被 minifier 或转义错误影响。
- 如果需要自动编号并引用公式，使用 `\begin{equation}...\label{...}\end{equation}` 并用 `\eqref{...}` 引用。

> 测试建议：在本地运行 `bundle exec jekyll serve`（或 `./tools/run.sh`），打开对应页面检查公式渲染并在浏览器控制台查找 MathJax 的错误信息。
{: .prompt-tip }

## MarkText提供的Markdown语法参考文档
> [MARKDOWN_SYNTAX.md](https://github.com/marktext/marktext/blob/master/docs/MARKDOWN_SYNTAX.md)
{: .prompt-tip }

> [本站备份链接](../../assets/archive/MARKDOWN_SYNTAX.md)
{: .prompt-tip }

## 其它参考链接
1. [Markdown 中文教程](https://markdown.com.cn/)
2. [Markdown Cheatsheet](https://www.markdownguide.org/cheat-sheet/)
3. [Markdown Origianl Syntax](https://daringfireball.net/projects/markdown/syntax)
4. [Emoji Cheat-Sheet](https://www.webfx.com/tools/emoji-cheat-sheet/)
5. [GitHub文档 Markdown基本格式语法](https://docs.github.com/zh/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
6. [GitHub Flavored Markdown Spec](https://github.github.com/gfm/)
