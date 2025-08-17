---
title: 【技术备忘录】基于PicGo+GitHub+Typora创建自己的MarkDown自动图床
date: 2022-08-12 22:14:05 +0800
description: 教你如何创建一个属于自己的基于PicGo+GitHub的Typora自动图床仓库
categories: [Technical, Memorandum]
tags: [memorandum, blog website mainteance, archive]     # TAG names should always be lowercase
toc: true
# comments: true
pin: false
math: true
mermaid: true
# media_subpath: /assets
published: true
sitemap: false
image:
---

## 前言

### 研究缘由（废话）

本人大学在校狗一枚，在经历过一次漫长的比赛之后，深深体会到了自动化代码版本控制的重要性，同时又想撰写博客把比赛经历记录下来备忘，于是在这个暑假决定学习一下现代开源项目的版本控制和文档撰写体系。由于我首选的博客站是CSDN，在看到MarkDown和富文本编辑器两个选项之后，首先开始学习MarkDown的语法（好吧可能就是每次下载code的时候都带一个README.md才想写md的owo）。由于MarkDown是一种纯文本标记语言，不能直接在文本中插入图片，图片的插入必须引用现有图床上的图片链接。想起之前一篇博文撰写中使用手动的方法，先向第三方图床上传图片，再复制图片链接地址，然后在博文撰写的时候手动插入图片链接地址才能完成一张图片的插入的繁琐过程，确实是过于麻烦了！于是乎，我上网搜索了一下“自动图床”的教程，照着做了一番，确实得到了一个好用的图床！从此我可以开启我的快乐博客之旅了！

### 什么是图床？为什么我们需要图床？

**图床**，顾名思义，就是“**图片的温床**”，以下定义来自[度娘百科](https://baike.baidu.com/item/%E5%9B%BE%E5%BA%8A/10721348?fr=aladdin)：

> 图床就是专门用来存放图片，同时允许你把图片对外连接的网上空间。

由于图片的上传下载会产生巨大的流量，各大博客网站的服务器又基本上托管在机房，机房网络是按照流量计费的，专线宽带的那个价格嘛只能说懂的都懂，所以很多博客站或者论坛图片就被ban了……再加上大家都喜欢使用和HTML有共通之处的MarkDown纯文本来进行博客的撰写，博客不可能没有插入图片的需求，纯文本怎么插入图片呢？然后我们伟大的MarkDown发明者就想到了“图片链接”的形式（好吧应该是借鉴的HTML）来插入图片，所以我们就需要**将自己的图片转化为可以在线访问的图片链接，才能保证我们的图片在博客网站上正常显示**。

### 常用的图床有哪些？我该如何选择适合我的图床？

根据现版本PicGo的支持列表，可以看到常用的图床有**腾讯云COS、阿里云OSS、SM.MS、Imgur、七牛云、又拍云**等等，这些图床有的在国内有的在国外、有的免费有的收费、的有上传容量限制有的没有（好吧其实在这里面我也没用过除了GitHub以外的图床，没有资格评论它们的好坏qwq）。一般而言国内的图床访问更加稳定快速，但是往往有容量限制或者需要收费（国内基本都是这样的，之前被大家当做图床的的Gitee做了防盗链处理之后就不能再被大家快乐白嫖当图床了，估计是流量太贵了顶不住了>_<）。我特地上阿里云OSS页面看过，这是一种后付费对象存储服务，通过国内CDN实现内容分发，按照流量计费，要是博客访问量不是很大，而且图片不大的话，对于个人用户而言它的价格也是很合适的（但是对于我这种白嫖党来说肯定不行了hhh），图方便快速可以考虑一下它或者是腾讯云。由于CSDN自带GitHub加速，而且GitHub相比其他图床没那么多限制，只需要自己挂个加速器就好了，作为究极白嫖党当然是选择GitHub啦！（误

## 工具与材料

- Windows 电脑一台
- [Typora](https://typoraio.cn/)（演示版本1.3.8，有条件请支持正版）
- [PicGo](https://github.com/Molunerfinn/PicGo/releases)（演示版本2.3.0）
- [Watt Toolkit](https://steampp.net/)（演示版本2.8.2 x64，用于GitHub加速）（以前叫Steam++，现在改名了但没完全改，现在还可以在很多地方看见Steam++的名称xwx）

## 操作步骤

1. 下载以上工具并且安装（安装过程就不赘述了，有不会的请问度娘，这仨都挺好装的owo）。

2. 启动Watt Toolkit，按照下图步骤选择启动GitHub加速。

   ![image-20220812213633494](https://raw.githubusercontent.com/fgfgfdg8/ImageStage/main/img/202208122136693.png)

   **注意：上方的“加速模式”一定要选择“系统代理模式”，否则在加速情况下很可能出现图床的上传或者下载只有一个好用的情况！**

   > tips：系统代理模式有一个弊端就是他会修改你的系统设置，打开你的本地代理，在没有正确关闭Watt Toolkit加速的时候，这个系统设置中的本地代理可能会导致你无法正常上网！这时就需要到设置-网络和Internet-代理中去把这个代理关掉！
   >
   > ![image-20220812214021346](https://raw.githubusercontent.com/fgfgfdg8/ImageStage/main/img/202208122140441.png)

3. 这时你应该可以顺利打开[全球最大同性交友网站](https://github.com/)了，在上面登录你的账号（没有账号就注册一个，怎么注册就请问度娘叭）。

4. 首先需要生成一个属于你自己的访问令牌。进入主页，鼠标挪到右上方你的头像上，在弹出的下拉列表中选择“Settings”。

   ![image-20220812214303102](https://raw.githubusercontent.com/fgfgfdg8/ImageStage/main/img/202208122143201.png)

5. 把页面拖下来选择“Developer Settings”：

   ![image-20220812214337288](https://raw.githubusercontent.com/fgfgfdg8/ImageStage/main/img/202208122143368.png)

6. 选择左侧的“Personal access token”，选择右上角的“Generate new token ”，

   ![image-20220812214523960](https://raw.githubusercontent.com/fgfgfdg8/ImageStage/main/img/202208122145043.png)

   然后按照下图输入信息：

   ![image-20220812214830762](https://raw.githubusercontent.com/fgfgfdg8/ImageStage/main/img/202508171116136.png)

   最后拖到页面底下，点击绿色的“Generate token”即可完成个人访问令牌的创建！

   ![image-20220812214945822](https://raw.githubusercontent.com/fgfgfdg8/ImageStage/main/img/202208122149899.png)

   创建完毕后，你就会得到一串字符，这就是你的个人访问令牌，一定要记得保存好它，因为GitHub不会第二次再给你看这串令牌！

   ![image-20220812215059548](https://raw.githubusercontent.com/fgfgfdg8/ImageStage/main/img/202208122150627.png)

7. 接下来进行GitHub仓库的配置。回到主页（好吧其实不回也行xwx），鼠标放到右上角的”+“上，在弹出的下拉列表中选择”New repository“：

   ![image-20220812215257987](https://raw.githubusercontent.com/fgfgfdg8/ImageStage/main/img/202208122152083.png)

   在新页面中按照下图输入信息：

   ![image-20220812215540153](https://raw.githubusercontent.com/fgfgfdg8/ImageStage/main/img/202208122155241.png)

   **注意：一定要选择“Public”，否则PicGo无法正常上传下载！**

   其他信息保持默认即可。填写完毕信息后，拖到页面最底下，点击绿色的“Create repository”按钮，即可完成图床仓库的创建。

   ![image-20220812215651943](https://raw.githubusercontent.com/fgfgfdg8/ImageStage/main/img/202208122156027.png)

   之后进入如下图页面即为创建成功！

   ![image-20220812215748628](https://raw.githubusercontent.com/fgfgfdg8/ImageStage/main/img/202208122157709.png)

8. 打开PicGo，按照下图进行图床设置（我这里采用的是已有的“ImageStorage”仓库，之前创建的例子中用的是“test”仓库），其中Token就是之前申请的Personal access token，之后点击“确定”保存；

   ![image-20220812220001730](https://raw.githubusercontent.com/fgfgfdg8/ImageStage/main/img/202208122200783.png)

   **注意：分支名一定要写“`main`”，否则PicGo无法正常上传！**

9. 打开Typora，按<kbd>Ctrl</kbd>+<kbd>,</kbd>或在“文件”菜单下打开偏好设置，按照下图进行设置：

   ![image-20220812220721769](https://raw.githubusercontent.com/fgfgfdg8/ImageStage/main/img/202208122207838.png)

10. OK！大功告成！在你的Typora中插入一张图片试试吧！图片在上传后，PicGo会在右下角弹出一个通知提示，提示图片上传已成功。当然，也可以在Typora中右键想要上传的图片，选择“上传图片”手动上传，或者是使用“格式-图像-上传所有本地图片”来上传MarkDown中所有的本地图片！

## 总结

经过以上的步骤，你就得到了一个好用的GitHub图床啦！Now enjoy it！