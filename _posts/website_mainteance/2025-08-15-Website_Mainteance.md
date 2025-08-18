---
title: 【技术备忘录】如何维护自己的个人网站并编写博客
date: 2025-08-15 23:00:00 +0800
description: 我想将自己的个人博客作为一个电子笔记本，记录自己的学习经历。一个破破烂烂的开始总该是比一个通盘考虑的犹豫强些的吧。
categories: [Technical, Memorandum]
tags: [memorandum, blog website mainteance]     # TAG names should always be lowercase
toc: true
# comments: true
pin: false
math: true
mermaid: true
# media_subpath: /assets
published: true
sitemap: false
image:
    path: assets/img/covers/starry_sky.png
    alt: WallPaper Engine上的“星空”壁纸提取图。在选择桌面壁纸时，我一眼就相中了它。这把阴影倒错的椅子才是最有意思的地方。
---

## 搭建和部署网站

### 从Chirpy主题提供的Starter开始部署GitHub Pages

> 本博客站使用[GitHub Pages](https://pages.github.com/)部署+[Jeklly](https://jekyllrb.com/) [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy)主题。  

#### 部署网站

按照[Chirpy主题的官方开始文档](https://chirpy.cotes.page/posts/getting-started/)中的指导，初始化一个使用Chirpy主题的个人网站仓库。  
为了简单快速地搭建起我的个人网站，这里我采用的是[Option 1. Using the Starter (Recommended)](https://chirpy.cotes.page/posts/getting-started/#option-1-using-the-starter-recommended)和[Setting up Natively (Recommended for Unix-like OS)](https://chirpy.cotes.page/posts/getting-started/#setting-up-natively-recommended-for-unix-like-os)子节的方法。  

#### 本地预览 {#本地预览}

由于GitHub Pages的build & deploy流程有较高延迟，而且有每小时10次build & deploy，每月100GB出网流量的配额限制，建议实际部署之前先在本地预览，确认无误后再进行推送。  
若要在本地预览网站，则使用`git clone <repository_address>`命令将第1步中创建的个人网站仓库克隆到本地，然后`cd`到网站仓库目录下，使用以下命令：
```bash
bundle exec jekyll serve --port 4000
```
> 我的本地环境为：
> - OS: Ubuntu 24.04.2 LTS x86_64  
> - Kernel: 6.14.0-27-generic  
> - Shell: zsh 5.9  

随后，在`http://127.0.0.1:4000`中即可查看网站预览，也可以通过修改`port`参数修改部署的端口号，不加这个参数默认就是`4000`端口。使用`port`参数可以区分本地的多个网页实例，从而实现在本机上同时部署多个预览。

### 修改初始配置

> 目前使用的`_config.yml`[备份地址](https://github.com/fgfgfdg8/fgfgfdg8.github.io/blob/781e68afaf89e57fd754063094176ef04fd5ebd9/_config.yml)
{: .prompt-tip }

1. 参考[Chirpy 官方文档](https://chirpy.cotes.page/posts/getting-started/#usage)修改`_config.yml`文件中的主要配置项。  
注意修改`url`和`baseurl`，`url`是你的个人GitHub Pages的基本页面，而`baseurl`是在`url`基础上增加的仓库名，是只有在一个GitHub账户唯一的Pages域名配额下部署多个仓库页面的方法。二者关系如下：
```text
# 基础URL，对应GitHub账户下唯一的<username>.github.io的GitHub Pages主仓库。
# E.g. 'https://username.github.io', note that it does not end with a '/'.
url: "https://fgfgfdg8.github.io"
baseurl: ""
# 如果需要再部署一个名为demo的仓库中的页面，则必需设置baseurl为/demo（注意有“/”），这样https://fgfgfdg8.github.io/demo才能成功部署并被访问，否则会产生GitHub Actions报错，无法部署
url: "https://fgfgfdg8.github.io"
baseurl: "/demo"
```

2. 修改`_data/authors.yml`中的author信息：
```yaml
---
author: fgfgfdg8                     # for single entry
---
```


3. 配置`_data/contact.yml`中要显示的联系方式联系信息，这里增加了领英：
```yaml
- type: linkedin
  icon: 'fab fa-linkedin'   # icons powered by <https://fontawesome.com/>
  url:  'https://www.linkedin.com/in/fengyi-wu-4076aa319/'                  # Fill with your Linkedin homepage
```

### 修改头像及网页图标

参考[Chirpy官方教程](https://chirpy.cotes.page/posts/customize-the-favicon/)，在[Real Favicon Generator](https://realfavicongenerator.net/)网站上生成头像并替换资源文件目录即可进行更换。

### 复制一些基础模板缺失的资源文件

`chirpy-starter`的`assets`目录下缺少了`css`和`js`文件，因此需要复制`jekyll-theme-chirpy`的`assets`目录下的文件到`assets`中。  
这些文件有：
- `assets/js/data`
- `assets/404.html`
- `assets/feed.xml`
- `assets/robots.txt`
- `assets/css/jekyll-theme-chirpy.scss`
- `_data/media.yml`

### 修改`about.md`

修改`_tabs/about.md`中的内容，可以修改ABOUT子页面的内容，根据需要修改即可。


### 添加评论系统

我为我的博客网站添加了评论系统，评论系统采用了[giscus](https://giscus.app/)。  
根据giscus官网的设置内容配置好设置，可以在网页上得到一个配置内容的js块，将对应项目的配置写入`_config.yml`的`comments`子块中。 
> `comments`中的配置项一定要使用`"`双引号括起来才能正确生效。参照[这里](https://github.com/cotes2020/jekyll-theme-chirpy/discussions/1189)的内容。
{: .prompt-tip }
- 官网js配置块
```javascript
<script src="https://giscus.app/client.js"
        data-repo="fgfgfdg8/fgfgfdg8.github.io"
        data-repo-id="<repo-id>"
        data-category="General"
        data-category-id="<category-id>"
        data-mapping="pathname"
        data-strict="0"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="top"
        data-theme="dark"
        data-lang="en"
        crossorigin="anonymous"
        async>
</script>
```

- `_config.yml`中需要修改的代码
```yaml
comments:
  # Global switch for the post-comment system. Keeping it empty means disabled.
  provider: "giscus" # [disqus | utterances | giscus]
  # The provider options are as follows:
  disqus:
    shortname: # fill with the Disqus shortname. › https://help.disqus.com/en/articles/1717111-what-s-a-shortname
  # utterances settings › https://utteranc.es/
  utterances:
    repo: # <gh-username>/<repo>
    issue_term: # < url | pathname | title | ...>
  # Giscus options › https://giscus.app
  giscus:
    repo: "fgfgfdg8/fgfgfdg8.github.io" # <gh-username>/<repo>
    repo_id: "<repo-id>"
    category: "General"
    category_id: "<category-id>"
    mapping: "pathname" # optional, default to 'pathname'
    strict: "0" # optional, default to '0'
    input_position: "top" # optional, default to 'bottom'
    lang: "en" # optional, default to the value of `site.lang`
    reactions_enabled: "1" # optional, default to the value of `1`
```


### TODO: 配置jsdelivr CDN以加速国内访问


### 安装`jekyll_compose`插件以从命令行进行快速新帖Front Formatter生成

> 插件GitHub地址：[jekyll_compose](https://github.com/jekyll/jekyll-compose)

> [插件`README`文档](https://github.com/jekyll/jekyll-compose/blob/master/README.md)
{: .prompt-tip }

## 发布帖子

### 创建帖子

在`_posts`下以`YYYY-MM-DD-TITLE.md`的格式创建新帖子文件，并编写文件头（Front Matter）。也可以使用Jekyll Compose插件以指定格式快速创建新帖子。  

目前我使用的通用文件头如下：
根据需要修改每篇帖子的`title`、`date`、`description`（页首语）、`categories`、`tags`等内容即可。
```yaml
---
title: 【技术备忘录】如何维护自己的个人网站并编写博客
date: 2025-08-15 23:00:00 +0800
description: 我想将自己的个人博客作为一个电子笔记本，记录自己的学习经历。一个破破烂烂的开始总该是比一个通盘考虑的犹豫强些的吧。
categories: [Technical, Memorandum] # At most 2 elements
tags: [memorandum, blog website mainteance]     # TAG names should always be lowercase
toc: true
# comments: true
pin: false
math: true
mermaid: true
# media_subpath: /assets
published: true
sitemap: false # `false` if you don't want this post to be indexed by search engines
image:
    path: assets/img/covers/starry_sky.png
    alt: WallPaper Engine上的“星空”壁纸提取图。在选择桌面壁纸时，我一眼就相中了它。这把阴影倒错的椅子才是最有意思的地方。
---
```
> 注意：页首图（预览图）需要是1200*630分辨率的`jpg`或者`png`图像。不满足尺寸要求的图像会被自动缩放为横纵比`1.91:1`的图像。不满足的图像格式将导致网页编译不通过。
{: .prompt-tip }

### 编写帖子

可以参照Chirpy主题官方的教程[Writing a New Post](https://chirpy.cotes.page/posts/write-a-new-post/)来进行编写，帖子主体使用Markdown标记语言，对应参考模板可参考[这个帖子](../Markdown_Syntax_Template)。
有关Jekyll的Posts和Front Matter的官方说明文档：  
- [Jekyll documentation-Posts](https://jekyllrb.com/docs/posts/)
- [Jekyll documentation-Front Matter](https://jekyllrb.com/docs/front-matter/)

#### 交叉引用

> 本博客站在单篇博文内使用kramdown格式的交叉引用。为了实现灵活的滚动样式，本博客站增加了一个`custom-cross-reference.scss`并修改了`assets/css/jekyll-theme-chirpy.scss`文件使之生效。
{: .prompt-tip }

> 交叉引用语法示例可以在[这里](../Markdown_Syntax_Template/#交叉引用)查看
{: .prompt-tip }

- `custom-cross-reference.scss`:

```css
/* Custom styles for anchor offset */

:root {
  --site-header-height: 72px; /* adjust if your header size differs */
}

/* Ensure anchor targets are visible below fixed header */
.content h1[id],
.content h2[id],
.content h3[id],
.content h4[id],
.content h5[id],
.content h6[id] {
  scroll-margin-top: calc(var(--site-header-height) + 1rem);
}

/* Optional: make anchor icons more visible */
.content a.anchor {
  opacity: 0.6;
}

@media (max-width: 992px) {
  :root {
    --site-header-height: 56px;
  }
}
```

- `jekyll-theme-chirpy.scss`:  

```css  
---
---

/* prettier-ignore */
@use 'main
{%- if jekyll.environment == 'production' -%}
  .bundle
{%- endif -%}
';

/* append your custom style below */

@use 'custom-cross-reference';

```

### 发布帖子
1. 在本地编写完帖子;
2. 确定[本地预览](#本地预览)无误;
3. 使用`git add .`和`git commit`命令提交本地的修改;
4. 使用`git push`命令推送到远程仓库，等待GitHub Pages的CI/CD流程自动进行网页build和deploy。

> GitHub上的资源有效性校验会比本地严格很多，例如`http`URL是不被允许的、资源文件如果路径设置不对将会引发编译报错等等，这些需要根据对应网页仓库的GitHub Actions部署情况报错信息进行实际调整（如果部署失败，会有邮件提醒）。
{: .prompt-tip }

## 参考链接
1. [GitHub Pages documentation](https://docs.github.com/en/pages)
2. [Jekyll documentation](https://jekyllrb.com/docs/)
3. [Jekyll中文站](https://www.jekyll.com.cn/)
4. [Chirpy theme documentation](https://github.com/cotes2020/jekyll-theme-chirpy/wiki)
