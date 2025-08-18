---
title: 【技术备忘录】Ubuntu下网络代理配置踩坑记录
date: 2025-08-16 00:00:00 +0800
description: 由于国内网络环境的特殊性，对经常需要使用国际互联网的程序员非常不友好，所以需要使用代理工具。Linux下使用代理的配置方法又较为特殊，坑点很多，我的技术水平和记忆力又有限，必须记录下来以备不时之需。
categories: [Technical, Memorandum]
tags: [memorandum, coding]     # TAG names should always be lowercase
toc: true
# comments: true
pin: false
math: true
mermaid: true
# media_subpath: /assets
published: true
sitemap: false
image:
    path: assets/img/covers/longs_peak.png
    alt: 落基山国家公园的朗斯峰，科罗拉多州。
---

> 使用代理本身就不应该是一件需要花大精力重点研究的事情，这纯粹是人为因素为编程初学者们造成的不必要的门槛。在这件事上更应该追求短平快的效率。
{: .prompt-tip }  

Ubuntu下的代理配置比起Windows下的代理配置要麻烦很多，因为Linux的网络服务结构跟Windows完全不一样。  
按照网上的说法，Linux下不支持类似Windows的全局代理功能，这给使用统一的代理软件造成了不小的麻烦。但是我又是一个Windows和Linux双系统用户，且PC终端设备很多，如果每个设备都需要一套单独的配置方法或本地配置文件，实在是难以维护，而且数据同步也是一个不小的麻烦。  
所以，我一直在尝试找一个能够将多个平台统一起来的代理配置方法，以最小化配置成本，减少复刻的复杂度。  

## Clash Verge Rev
截至目前，能跨Windows，Linux平台部署的统一代理软件里比较优秀的应该只有[Clash Verge Rev](https://github.com/clash-verge-rev/clash-verge-rev)了。

我作为用户最直观感受到的优缺点：  
优点：
- 现代化的GUI界面，包含了测速、出口IP查询、流量统计等功能，功能强大。
- 简单易上手，只需要配置订阅链接、打开代理开关就行。
- [文档](https://clash-verge-rev.org/guide/quickstart.html)较人性化，对新手较为友好。

缺点：
- 官方说不能部署在没有GUI的设备上，Linux服务器不行
- TUN模式下接管流量有可能导致系统网络不稳定，尤其是节点网络质量不好的时候。
- 不支持系统代理+`proxychains`等命令行代理工具联用，只能使用TUN模式。
- Clash兼容节点自建较为复杂，没有成熟的一键配置脚本。


> 在目前的简单配置下，Clash Verge Rev不支持与`proxychains`等命令行代理工具共同配合使用，只能使用TUN模式，代理命令行流量。
{: .prompt-info }  
> TODO：使用TUN模式有概率导致系统DNS不可用，网上暂时搜不到类似问题的处理方式，官方文档和issue中暂时没有对口解决方案。Clash Verge Rev官方目前不打算修改[系统代理模式下自动设置命令行环境变量的问题](https://github.com/clash-verge-rev/clash-verge-rev/issues/3725)，而是要求用户自己配置代理作用范围。并不是每个人对Linux都有这么深入的技术理解，但是毕竟技不如人，挨打立正吧。
{: .prompt-info }

### 安装

1. 到[Release](https://github.com/clash-verge-rev/clash-verge-rev/releases)页面下载对应的`.deb`安装包；
2. 使用`apt`进行安装（这里安装的是V2.3.2版本）；
```bash
sudo apt install ./Clash.Verge_2.3.2_amd64.deb
```
3. 安装完毕后，会在应用列表中看到`Clash Verge`图标已经出现，点击运行即可。

### 简单使用
[官方参考文档](https://www.clashverge.dev/guide/quickstart.html)  

Clash Verge主要有2种模式，一种是系统代理模式，另一种是TUN模式。
- **系统代理模式**：只修改系统设置中的代理配置，适用于大多数浏览器访问网页的代理。
- **TUN模式（虚拟网卡模式）**：创建一张虚拟网卡，作为专用Tunnel接管计算机上的所有流量，从而实现对命令行等应用程序流量的接管，实现系统级全局Clash核心流量代理。  

> 切换TUN模式会导致机器出现瞬间断网的情况，这在服务器远程连接配置场景下有时候是很致命的（例如进行大文件下载或者`git clone`操作的时候，切换节点导致下载中断），甚至有可能导致物理机断网，在无法物理访问服务器的时候，慎用。
{: .prompt-warning }

### 有关启用TUN模式后系统DNS解析异常的问题
这种情况下需要在修改UI界面->订阅->全局扩展覆写配置中的`dns`字段，以恢复使用系统DNS，而不是订阅配置文件中的替代DNS，如果没有则新建该字段，配置如下：
```yaml
dns:
  enable: false
  use-system-hosts: true
```
[参考issue](https://github.com/clash-verge-rev/clash-verge-rev/issues/2615)

### WebUI
WebUI适用于无法访问目标机器GUI、只能通过SSH访问的情况，提供了常用的配置功能（包括启用或关闭TUN模式、更换节点、节点测速等），但是不支持像在桌面环境下一样切换订阅文件。  
目前Clash Verge Rev自带3种WebUI，其链接分别如下：
```text
https://metacubex.github.io/metacubexd/#/setup?http=true&hostname=%host&port=%port&secret=%secret

https://yacd.metacubex.one/?hostname=%host&port=%port&secret=%secret

https://board.zash.run.place/#/setup?http=true&hostname=%host&port=%port&secret=%secret
```
将其中的`%host`和`%port`分别替换为自己机器上Clash Verge Rev的对应值即可，默认为`127.0.0.1`和`9997`。  

> 访问WebUI之前，需要提前做好SSH端口映射，可以使用VSCode等IDE自带的端口映射功能，或者使用ssh命令：

```bash
ssh -L 9997:127.0.0.1:9997 user@remote_host
```

其中，`user`为远程主机用户名，`remote_host`为远程主机地址。这样即可将远程主机的9997端口映射到本地的9997端口，随后利用上述链接即可在浏览器中访问WebUI。
{: .prompt-tip }  

### 免费Clash订阅获取TG频道（临时用）
在缺少订阅链接的时候，可以到TG频道[订阅分享中心](https://t.me/dingyue_Center)临时获取一个别人投稿的临期订阅救急（但是前提是还是能够科学上网）。  


## V2RayA
[V2RayA](https://github.com/v2rayA/v2rayA)是一个易用而强大的，跨平台的 V2Ray 客户端（官方介绍）。

我作为用户最直观感受到的优缺点：  
优点：
- 支持没有GUI界面的Linux服务器。
- 支持与[233Boy V2Ray一键配置脚本](https://233boy.com/v2ray/v2ray-script/)配合实现低配置成本自建节点（网上很少有有效的Clash自建节点并导出订阅的教程）。
- 支持多节点负载均衡。
- 支持与`proxychains`命令行代理工具配合使用。

缺点：
- 配置较繁琐，配置项名称和内容都缺乏直观性。
- [文档](https://v2raya.org/docs/prologue/introduction/)不全面，尤其缺乏对配置页面关键配置项含义的说明（`用户文档->手册`栏有挺多TODO），对新手而言不算很友好。
- 在启动或停止V2RayA透明代理服务时，整机所有应用的网络均会中断，断网时长不确定。
- 在V2RayA透明代理运行时，添加或删除分流节点时响应缓慢，而且不支持同时处理多个请求，只能一个个等。
- 不支持某些应用的代理（例如[Zoom Workplace](https://www.zoom.com/zh-cn/products/collaboration-tools/)），即使在应用中专门设置代理配置之后也无法正确登录。

> TODO：挨个查阅issue & 阅读源码也许是一个理解配置的方式，但是太费力了，我认为就为了使用一个代理工具这么做不值得。
{: .prompt-info }

### 自建节点服务端配置
具体可以参照[233Boy V2Ray一键配置脚本教程](https://233boy.com/v2ray/v2ray-script/)，非常简单。
> 需要购买一台具有公网IP的、中国大陆地区以外的VPS用于代理流量转发，要配置为Linux实例。
{: .prompt-tip }  
#### 安装
```bash
bash <(wget -qO- -o- https://git.io/v2ray.sh)
```
#### 配置
```bash
v2ray
```
然后根据提示进行配置，即可获取V2RayA中可使用的订阅链接。

### 客户端安装与配置
#### 安装
[V2RayA官方安装教程文档](https://v2raya.org/docs/prologue/installation/debian/)
将使用的命令总结如下：
```bash
wget -qO - https://apt.v2raya.org/key/public-key.asc | sudo tee /etc/apt/keyrings/v2raya.asc
echo "deb [signed-by=/etc/apt/keyrings/v2raya.asc] https://apt.v2raya.org/ v2raya main" | sudo tee /etc/apt/sources.list.d/v2raya.list
sudo apt update
sudo apt install v2raya v2ray ## 也可以使用 xray 包
sudo systemctl start v2raya.service
sudo systemctl enable v2raya.service
```

#### 配置

> 开启/关闭透明代理服务会导致机器出现瞬间断网的情况，这在服务器远程连接配置场景下有时候是很致命的（例如进行大文件下载或者`git clone`操作的时候，切换节点导致下载中断），甚至有可能导致物理机断网，在无法物理访问服务器的时候，慎用。
{: .prompt-warning }

1. 安装完毕后，默认会在[http://localhost:2017](http://localhost:2017)启动WebUI；
2. 登录WebUI，在WebUI创建用户名及密码
> 注意：如果使用SSH方式访问远程机器，则需要跟上面访问Clash Verge Rev的方式一样一样进行端口转发。
{: .prompt-tip }  
3. 导入自建节点链接或订阅地址，然后选择节点；
4. 点击右上角“设置”配置规则：
<img src="assets/img/env_config/proxy_setup/1.png" alt="img" width="67%" />  
<img src="assets/img/env_config/proxy_setup/2.png" alt="img" width="67%" />  
<img src="assets/img/env_config/proxy_setup/3.png" alt="img" width="67%" />  

5. 最后点击左上角的粉红色“就绪”按钮启动服务即可启用透明代理。

## Clash for Linux (已停更)
Clash for Linux已经停更了，所以它已经没有文档支持了，但是在Linux下如果使用`proxychains`等命令行代理工具的话，其还是一个较好的选择。  

[我的Clash for Linux配置修改版](https://github.com/fgfgfdg8/clash-for-linux)

### 安装与配置方法

#### 下载项目

```bash
$ git clone https://github.com/Elegycloud/clash-for-linux-backup.git
```

进入到项目目录，编辑`.env`文件，修改变量`CLASH_URL`的值。

```bash
$ cd clash-for-linux
$ vim .env
```

> **注意：** `.env` 文件中的变量 `CLASH_SECRET` 为自定义 Clash Secret，值为空时，脚本将自动生成随机字符串。

<br>

#### 启动程序

直接运行脚本文件`start.sh`

- 进入项目目录

```bash
$ cd clash-for-linux
```

- 运行启动脚本

```bash
$ sudo bash start.sh

正在检测订阅地址...
Clash订阅地址可访问！                                      [  OK  ]

正在下载Clash配置文件...
配置文件config.yaml下载成功！                              [  OK  ]

正在启动Clash服务...
服务启动成功！                                             [  OK  ]

Clash Dashboard 访问地址：http://<ip>:9090/ui
Secret：xxxxxxxxxxxxx

请执行以下命令加载环境变量: source /etc/profile.d/clash.sh

请执行以下命令开启系统代理: proxy_on

若要临时关闭系统代理，请执行: proxy_off

```

```bash
$ source /etc/profile.d/clash.sh
$ proxy_on
```

- 检查服务端口

```bash
$ netstat -tln | grep -E '9090|789.'
tcp        0      0 127.0.0.1:9090          0.0.0.0:*               LISTEN     
tcp6       0      0 :::7890                 :::*                    LISTEN     
tcp6       0      0 :::7891                 :::*                    LISTEN     
tcp6       0      0 :::7892                 :::*                    LISTEN
```

- 检查环境变量

```bash
$ env | grep -E 'http_proxy|https_proxy'
http_proxy=http://127.0.0.1:7890
https_proxy=http://127.0.0.1:7890
```

以上步鄹如果正常，说明服务clash程序启动成功，现在就可以体验高速下载github资源了。

<br>

#### 重启程序

如果需要对Clash配置进行修改，请修改 `conf/config.yaml` 文件。然后运行 `restart.sh` 脚本进行重启。

> **注意：**
> 重启脚本 `restart.sh` 不会更新订阅信息。

<br>

#### 停止程序

- 进入项目目录

```bash
$ cd clash-for-linux
```

- 关闭服务

```bash
$ sudo bash shutdown.sh
```

服务关闭成功，请执行以下命令关闭系统代理：proxy_off

```bash
$ proxy_off
```

然后检查程序端口、进程以及环境变量`http_proxy|https_proxy`，若都没则说明服务正常关闭。


<br>

#### Clash Dashboard

- 访问 Clash Dashboard

通过浏览器访问 `start.sh` 执行成功后输出的地址，例如：http://192.168.0.1:9090/ui

- 登录管理界面

在`API Base URL`一栏中输入：http://\<ip\>:9090 ，在`Secret(optional)`一栏中输入启动成功后输出的Secret。

点击Add并选择刚刚输入的管理界面地址，之后便可在浏览器上进行一些配置。

- 更多教程

此 Clash Dashboard 使用的是[yacd](https://github.com/haishanh/yacd)项目，详细使用方法请移步到yacd上查询。


<br>

#### 终端界面选择代理节点

部分用户无法通过浏览器使用 Clash Dashboard 进行节点选择、代理模式修改等操作，为了方便用户可以在Linux终端进行操作，下面提供了一个功能简单的脚本以便用户可以临时通过终端界面进行配置。

脚本存放位置：`scripts/clash_proxy-selector.sh`

> **注意：**
>
> 使用脚本前，需要修改脚本中的 **Secret** 变量值为上述启动脚本输出的值，或者与 `.env` 文件中定义的 **CLASH_SECRET** 变量值保持一致。


<br>

#### 配置为服务
将以下的`clash.service`放到`/etc/systemd/system`下，然后执行`sudo systemctl enable calsh`和`sudo systemctl start clash`即可将Clash for Linux配置为开机自启的服务，但是一定要确保`.env`中的订阅链接是有效的，否则有可能导致服务器重启的时候卡在Clash服务启动这一步。
```text
[Unit]
Description=Clash Core
After=network.target
Wants=network.target

[Service]
Type=forking
WorkingDirectory=/home/wfy/clash-for-linux/
ExecStart=/home/wfy/clash-for-linux/start.sh
ExecStop=/home/wfy/clash-for-linux/shutdown.sh
ExecStartPost=
ExecStopPost=
PrivateTmp=false
Restart=on-failure
RestartSec=10
User=root
Group=root

[Install]
WantedBy=multi-user.target
```