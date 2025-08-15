---
title: 【技术备忘录】如何维护自己的个人网站并编写博客
date: 2025-08-15 23:00:00 +0800
description: 我想将自己的个人博客作为一个电子笔记本，记录自己的学习经历。一个破破烂烂的开始比一个通盘考虑的犹豫更有价值！
categories: [Technical, Memorandum]
tags: [memorandum, blog website mainteance]     # TAG names should always be lowercase
toc: true
# comments: true
pin: true
math: true
mermaid: true
# media_subpath: /assets
image:
    path: assets/img/covers/starry_sky.png
    alt: WallPaper Engine上的“星空”壁纸提取图。在选择桌面壁纸时，我一眼就相中了它。这把阴影倒错的椅子才是最有意思的地方。
---
# 搭建和部署网站
## 从Chirpy主题提供的Starter开始部署GitHub Pages
> 本博客站使用[GitHub Pages](https://pages.github.com/)部署+[Jeklly](https://jekyllrb.com/) [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy)主题。  

1. 按照[Chirpy主题的官方开始文档](https://chirpy.cotes.page/posts/getting-started/)中的指导，初始化一个使用Chirpy主题的个人网站仓库。  
为了简单快速地搭建起我的个人网站，这里我采用的是[Option 1. Using the Starter (Recommended)](https://chirpy.cotes.page/posts/getting-started/#option-1-using-the-starter-recommended)和[Setting up Natively (Recommended for Unix-like OS)](https://chirpy.cotes.page/posts/getting-started/#setting-up-natively-recommended-for-unix-like-os)子节的方法。  

2. 若要在本地预览网站，则使用`git clone <repository_address>`命令将第1步中创建的个人网站仓库克隆到本地，然后`cd`到网站仓库目录下，使用以下命令：
```bash
bundle exec jekyll serve
```
> 我的本地环境为：
> - OS: Ubuntu 24.04.2 LTS x86_64  
> - Kernel: 6.14.0-27-generic  
> - Shell: zsh 5.9  

随后，在`http://127.0.0.1:4000`中即可查看网站预览。

## 修改初始配置
参考[Chirpy 官方文档](https://chirpy.cotes.page/posts/getting-started/#usage)修改`_config.yml`文件中的主要配置项。。

## 复制一些基础模板缺失的资源文件
`chirpy-starter`的`assets`目录下缺少了`css`和`js`文件，因此需要复制`jekyll-theme-chirpy`的`assets`目录下的文件到`assets`中。


## 添加评论系统
我为我的blog网站添加了评论系统，评论系统采用了[giscus](https://giscus.app/)。  
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


## TODO: 配置jsdelivr CDN以加速国内访问


# 编写帖子
[Writing a New Post](https://chirpy.cotes.page/posts/write-a-new-post/#mathematics)


# 参考链接
1. [GitHub Pages documentation](https://docs.github.com/en/pages)
2. [Jekyll documentation](https://jekyllrb.com/docs/)
3. [Chirpy theme documentation](https://github.com/cotes2020/jekyll-theme-chirpy/wiki)
4. [Markdown Origianl Syntax](https://daringfireball.net/projects/markdown/syntax)
5. [Markdown Cheatsheet](https://www.markdownguide.org/cheat-sheet/)
6. [Markdown Syntax from MarkText](https://github.com/marktext/marktext/blob/master/docs/MARKDOWN_SYNTAX.md)
7. [Markdown 中文教程](https://markdown.com.cn/)