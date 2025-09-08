---
title: 【技术备忘录】Ubuntu下网络代理配置踩坑记录
date: 2025-08-16 00:00:00 +0800
description: 由于国内网络环境的特殊性，对经常需要使用国际互联网的程序员非常不友好，所以需要使用代理工具。Linux下使用代理的配置方法又较为特殊，坑点很多，我作为小白的技术水平和记忆力又有限，只能记录下来以备不时之需。
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
- 官方说不能部署在没有GUI的设备上，因此Linux服务器就没法用这个客户端了，
- TUN模式下接管流量有可能导致系统网络不稳定，尤其是节点网络质量不好的时候。
- （待定）默认配置下，不支持系统代理+`proxychains`等命令行代理工具联用，有可能是防火墙设置问题，只能使用TUN模式作替代。
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
- 配置较繁琐，配置项名称和内容都缺乏直观性，同时[文档](https://v2raya.org/docs/prologue/introduction/)不够全面，尤其缺乏对配置页面关键配置项含义的说明（`用户文档->手册`栏有挺多TODO），对小白而言不算很友好。
- 启用透明代理后`apt update`命令工作将不正常，无法正确拉取仓库元数据。
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
然后根据提示选择选项进行配置，在配置完毕后即可获取V2RayA中可使用的订阅链接。

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

> 如果需要使用`system tun`模式转发全局系统流量，则必须在`ufw`中增加放通`tun0`的规则，否则将无法联网。
> ```bash
> sudo ufw allow in on tun0
> sudo ufw allow out on tun0
> ```
{: .prompt-tip }

6. 启用透明代理后，即可在浏览器、终端等位置使用透明代理，如果终端使用透明代理无效的，可以考虑

## S-UI
S-UI是一个可以在VPS上搭建代理节点服务端的一个面板应用，支持Shadowsocks、VMess、Trojan等多种主流协议。
开源地址：[S-UI](https://github.com/alireza0/s-ui)

### 安装
```bash
bash <(curl -Ls https://raw.githubusercontent.com/alireza0/s-ui/master/install.sh)
```
然后根据提示修改配置项安装即可。我只修改了`admin credentials`选项，设置了登录面板使用的用户名和密码。
### 配置
1. 命令行配置

```bash
s-ui
```
然后根据提示选择选项进行配置即可获取V2RayA中可使用的订阅。

2. WebUI配置

安装完毕后，只要没有在安装过程中修改配置，默认的WebUI地址为：[http://localhost:2095/app](http://localhost:2095/app)。
访问该地址然后使用安装阶段设置的用户名和密码即可登录。

3. TLS设置

参照图中设置即可，需要配置`TLS`和`REALITY`两个TLS配置。
![s-ui_tls_setting_1](https://cdn.jsdelivr.net/gh/fgfgfdg8/ImageStage/img/s-ui_tls_setting_1.png)
![s-ui_tls_setting_2](https://cdn.jsdelivr.net/gh/fgfgfdg8/ImageStage/img/s-ui_tls_setting_2.png)

4. 代理入站设置

在登录面板后，按照图中设置`vless`，`hysteria`和`hysteria2`三种协议的入站（亲测只有这3种好用，同时需要放通防火墙的UDP1025-65535全部端口）：
![s-ui_proxy_setting_1](https://cdn.jsdelivr.net/gh/fgfgfdg8/ImageStage/img/s-ui_proxy_setting_1.png)
![s-ui_proxy_setting_2](https://cdn.jsdelivr.net/gh/fgfgfdg8/ImageStage/img/s-ui_proxy_setting_2.png)
![s-ui_proxy_setting_3](https://cdn.jsdelivr.net/gh/fgfgfdg8/ImageStage/img/s-ui_proxy_setting_3.png)


5. 设置用户与获取订阅链接

在用户设置中添加用户：
![s-ui_proxy_setting_4](https://cdn.jsdelivr.net/gh/fgfgfdg8/ImageStage/img/s-ui_proxy_setting_4.png)
随后就可以点击二维码图标查看订阅二维码了。点击二维码可以复制对应的订阅链接，可以在支持订阅的客户端里导入。
![s-ui_proxy_setting_6](https://cdn.jsdelivr.net/gh/fgfgfdg8/ImageStage/img/s-ui_proxy_setting_6.png)

6. 面板动态域名设置

到[ClouDNS](https://www.cloudns.net/)上注册一个账号，然后在Dashboard界面用`createe zone`按钮创建一个新的免费DDNS域名，然后添加一个`A`记录指向自己的VPS IP：
![ddns_domain_apply_1](https://cdn.jsdelivr.net/gh/fgfgfdg8/ImageStage/img/ddns_domain_apply_1.png)
![ddns_domain_apply_2](https://cdn.jsdelivr.net/gh/fgfgfdg8/ImageStage/img/ddns_domain_apply_2.png)

也可以用[dynv6](https://dynv6.com/)配置DDNS，操作更为简便，而且每个账户没有ClouDNS那样严格的1个域名的限制。

7. 域名SSL证书申请与应用

在`s-ui`命令行界面选择`19`为域名申请证书，申请完毕后脚本会告知申请的SSL证书文件路径在哪里，记录下来然后输入面板的设置里：
![s-ui_proxy_setting_5](https://cdn.jsdelivr.net/gh/fgfgfdg8/ImageStage/img/s-ui_proxy_setting_5.png)
![s-ui_proxy_setting_7](https://cdn.jsdelivr.net/gh/fgfgfdg8/ImageStage/img/s-ui_proxy_setting_7.png)
保存后重启面板，即可使用配置的域名+端口号，以`https`形式访问面板了。

8. 整合多个节点实现自建机场一个订阅包含所有节点

可以在用户管理中新建一个用户，然后编辑，在“链接”选项卡中添加别的节点的外部订阅链接：
![s-ui_proxy_setting_8](https://cdn.jsdelivr.net/gh/fgfgfdg8/ImageStage/img/s-ui_proxy_setting_8.png)
但是注意，每个入站规则名称要不一样，否则出现重名，客户端将无法使用订阅！

> 以上方法搭建的节点没有国内流量免代理自动分流规则。
{: .prompt-tip }  

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

## 命令行代理：proxychains
`proxychains`是Linux下一个常用的命令行代理工具，可以为在终端中执行的命令指定网络代理，其原理应该是在要执行的命令前动态链接其自己的共享`.so`库，这样就可以劫持所执行的命令的网络流量，然后将这些网络流量转发到配置的代理服务器。
`proxychains`和上述的`V2RayA`联用，可以强制为某些不走透明代理的命令强行显示指定使用代理，从而实现网络加速。同时，使用`proxychains`也可以灵活地决定哪些命令使用代理哪些不使用代理，灵活度更高。
### 安装
```bash
sudo apt install proxychains4
```
### 配置
安装完毕后，编辑`/etc/proxychains4.conf`文件，主要修改点有：
1. 将默认的`strict_chain`注释掉（启用该选项要求文件最后配置的所有代理都有效，否则代理链失效将不能正常进行代理），然后取消`dynamic_chain`（文件最后配置的任意一条代理有效都可以实现命令行代理）的注释；
2. 在文件最后根据之前在`V2RayA`中配置的代理方式、代理服务器地址及端口号配置代理链设置，例如我的设置就是`socks5  127.0.0.1 20173`，这里配置一条有效的就够用了。  

```text
# proxychains.conf  VER 4.x
#
#        HTTP, SOCKS4a, SOCKS5 tunneling proxifier with DNS.


# The option below identifies how the ProxyList is treated.
# only one option should be uncommented at time,
# otherwise the last appearing option will be accepted
#
dynamic_chain
#
# Dynamic - Each connection will be done via chained proxies
# all proxies chained in the order as they appear in the list
# at least one proxy must be online to play in chain
# (dead proxies are skipped)
# otherwise EINTR is returned to the app
#
#strict_chain
#
# Strict - Each connection will be done via chained proxies
# all proxies chained in the order as they appear in the list
# all proxies must be online to play in chain
# otherwise EINTR is returned to the app
#
#round_robin_chain
#
# Round Robin - Each connection will be done via chained proxies
# of chain_len length
# all proxies chained in the order as they appear in the list
# at least one proxy must be online to play in chain
# (dead proxies are skipped).
# the start of the current proxy chain is the proxy after the last
# proxy in the previously invoked proxy chain.
# if the end of the proxy chain is reached while looking for proxies
# start at the beginning again.
# otherwise EINTR is returned to the app
# These semantics are not guaranteed in a multithreaded environment.
#
#random_chain
#
# Random - Each connection will be done via random proxy
# (or proxy chain, see  chain_len) from the list.
# this option is good to test your IDS :)

# Make sense only if random_chain or round_robin_chain
#chain_len = 2

# Quiet mode (no output from library)
#quiet_mode

## Proxy DNS requests - no leak for DNS data
# (disable all of the 3 items below to not proxy your DNS requests)

# method 1. this uses the proxychains4 style method to do remote dns:
# a thread is spawned that serves DNS requests and hands down an ip
# assigned from an internal list (via remote_dns_subnet).
# this is the easiest (setup-wise) and fastest method, however on
# systems with buggy libcs and very complex software like webbrowsers
# this might not work and/or cause crashes.
proxy_dns

# method 2. use the old proxyresolv script to proxy DNS requests
# in proxychains 3.1 style. requires `proxyresolv` in $PATH
# plus a dynamically linked `dig` binary.
# this is a lot slower than `proxy_dns`, doesn't support .onion URLs,
# but might be more compatible with complex software like webbrowsers.
#proxy_dns_old

# method 3. use proxychains4-daemon process to serve remote DNS requests.
# this is similar to the threaded `proxy_dns` method, however it requires
# that proxychains4-daemon is already running on the specified address.
# on the plus side it doesn't do malloc/threads so it should be quite
# compatible with complex, async-unsafe software.
# note that if you don't start proxychains4-daemon before using this,
# the process will simply hang.
#proxy_dns_daemon 127.0.0.1:1053

# set the class A subnet number to use for the internal remote DNS mapping
# we use the reserved 224.x.x.x range by default,
# if the proxified app does a DNS request, we will return an IP from that range.
# on further accesses to this ip we will send the saved DNS name to the proxy.
# in case some control-freak app checks the returned ip, and denies to 
# connect, you can use another subnet, e.g. 10.x.x.x or 127.x.x.x.
# of course you should make sure that the proxified app does not need
# *real* access to this subnet. 
# i.e. dont use the same subnet then in the localnet section
#remote_dns_subnet 127 
#remote_dns_subnet 10
remote_dns_subnet 224

# Some timeouts in milliseconds
tcp_read_time_out 15000
tcp_connect_time_out 8000

### Examples for localnet exclusion
## localnet ranges will *not* use a proxy to connect.
## note that localnet works only when plain IP addresses are passed to the app,
## the hostname resolves via /etc/hosts, or proxy_dns is disabled or proxy_dns_old used.

## Exclude connections to 192.168.1.0/24 with port 80
# localnet 192.168.1.0:80/255.255.255.0

## Exclude connections to 192.168.100.0/24
# localnet 192.168.100.0/255.255.255.0

## Exclude connections to ANYwhere with port 80
# localnet 0.0.0.0:80/0.0.0.0
# localnet [::]:80/0

## RFC6890 Loopback address range
## if you enable this, you have to make sure remote_dns_subnet is not 127
## you'll need to enable it if you want to use an application that 
## connects to localhost.
# localnet 127.0.0.0/255.0.0.0
# localnet ::1/128

## RFC1918 Private Address Ranges
# localnet 10.0.0.0/255.0.0.0
# localnet 172.16.0.0/255.240.0.0
# localnet 192.168.0.0/255.255.0.0

### Examples for dnat
## Trying to proxy connections to destinations which are dnatted,
## will result in proxying connections to the new given destinations.
## Whenever I connect to 1.1.1.1 on port 1234 actually connect to 1.1.1.2 on port 443
# dnat 1.1.1.1:1234  1.1.1.2:443

## Whenever I connect to 1.1.1.1 on port 443 actually connect to 1.1.1.2 on port 443
## (no need to write :443 again)
# dnat 1.1.1.2:443  1.1.1.2

## No matter what port I connect to on 1.1.1.1 port actually connect to 1.1.1.2 on port 443
# dnat 1.1.1.1  1.1.1.2:443

## Always, instead of connecting to 1.1.1.1, connect to 1.1.1.2
# dnat 1.1.1.1  1.1.1.2

# ProxyList format
#       type  ip  port [user pass]
#       (values separated by 'tab' or 'blank')
#
#       only numeric ipv4 addresses are valid
#
#
#        Examples:
#
#               socks5  192.168.67.78   1080    lamer   secret
#               http    192.168.89.3    8080    justu   hidden
#               socks4  192.168.1.49    1080
#               http    192.168.39.93   8080
#
#
#       proxy types: http, socks4, socks5, raw
#         * raw: The traffic is simply forwarded to the proxy without modification.
#        ( auth types supported: "basic"-http  "user/pass"-socks )
#
[ProxyList]
# add proxy here ...
# meanwile
# defaults set to "tor"
socks5  127.0.0.1 20173
```

### 使用
在需要进行代理的命令前加上`proxychains4`，使用`proxychains4 <command>`即可使用代理执行命令，例如`proxychains git clone <repo>`等。当然也可以通过在`.bashrc`或者`.zshrc`中配置别名来减少每次需要输入的字母，例如我使用`pc`：
```bash
alias pc='function pcxy(){ proxychains4 $* ;}; pcxy'
```

## WSL配置网络代理
参见[为 WSL2 设置代理](https://www.cnblogs.com/qiuliw/p/18927370)，最主要的设置是关闭Windows到WSL的防火墙，另外要关闭`WSL Settings`中的“启用localhost转发”开关，不然代理也无法使用：

```text
3、关闭Hyper-V虚拟网卡的防火墙

Get-NetFirewallHyperVVMCreator #获取wsl GUID
通常都为{40E0AC32-46A5-438A-A0B2-2B479E8F2E90}, 如果不一样后面的命令都需要替换

Get-NetFirewallHyperVVMSetting -PolicyStore ActiveStore -Name '{40E0AC32-46A5-438A-A0B2-2B479E8F2E90}' # 查看Hyper-V防火墙状态
Name                  : {40E0AC32-46A5-438A-A0B2-2B479E8F2E90}
Enabled               : True
DefaultInboundAction  : Block # 默认拦截 宿主机 发往 Hyper-V 虚拟机（包括 WSL2）的入站连接，拦住了包的返回过程
DefaultOutboundAction : Allow #  WSL2 向外包发送到宿主机默认是允许的
LoopbackEnabled       : True
AllowHostPolicyMerge  : True
关闭Hyper-V防火墙(要使用管理员权限下的Powershell)

Set-NetFirewallHyperVVMSetting -Name '{40E0AC32-46A5-438A-A0B2-2B479E8F2E90}' -Enabled False
4、关闭win11对wsl的防火墙

要使用管理员权限下的Powershell

New-NetFirewallRule -DisplayName "WSL vEthernet inbound" `
  -Direction Inbound `
  -InterfaceAlias "vEthernet (WSL*)" `
  -Action Allow `
  -Profile Any

New-NetFirewallRule -DisplayName "WSL vEthernet outbound" `
  -Direction Outbound `
  -InterfaceAlias "vEthernet (WSL*)" `
  -Action Allow `
  -Profile Any
如果关掉Hyper-V防火墙还是无法访问，检查一下ubuntu的防火墙并进行关闭。
```

![alt text](assets/img/env_config/proxy_setup/4.png)
_WSL网络设置_

配置完毕后在`.bashrc`或者`.zshrc`中增加以下条目以自动设置命令行代理环境变量：
```bash
GW_IP=$(ip route | grep '^default via' | awk '{print $3}')
export HTTP_PROXY="http://$GW_IP:7897"
export HTTPS_PROXY="http://$GW_IP:7897"
export ALL_PROXY="http://$GW_IP:7897"
```
缺点是这种配置没有`proxychains`灵活，使用上就跟Clash Verge Rev的系统代理模式一样，因为Clash Verge Rev的系统代理模式也是自动设置命令行代理的环境变量。

## 反向代理：frp
> 使用反向代理本身是一件极具风险的事情，因为它破坏了原有单位内网的独立与隔离性，需要使用者自行承担网络安全风险，如果你无法承担因私自使用反向代理导致所在单位的网络管理制度的惩罚后果，而且确实有远程办公、跨域访问等需要，请不要使用反向代理，并转为寻求使用经过审批的专用VPN等方案。
{: .prompt-warning }  

`frp`是一个高性能反向代理应用，支持多种协议，包括 TCP、UDP、HTTP、HTTPS 等，并且具备 P2P 通信功能，可以安全、便捷地将内网服务暴露到公网，通过拥有公网 IP 的节点进行中转。
如果需要安全地暴露内网服务，可以参考官方文档：[安全地暴露内网服务](https://gofrp.org/zh-cn/docs/examples/stcp/)。这里的为了配置简单使用了默认证书，如果对安全性要求更高的话可以自己去查官方文档来看如何生成。
### 安装
公网机器和内网机器分别到[frp Release](https://github.com/fatedier/frp/releases)页面下载合适版本的frp文件，放到公网机器及内网机器合适的路径下，然后执行解压：
```bash
tar -xzvf ./frp_<版本>_linux_amd64.tar.gz
```

### 配置
为了免于在访问者机器上安装`frpc`，我使用了SSH ProxyJump+frp的混合转发方式。正常使用STCP方式从客户端访问内网机器的话，需要客户端机器也安装并配置`frpc`，较为繁琐，因此我这个方案相当于用运行在公网机器上的一个`frpc`直接将内网机器的ssh端口映射到了公网机器的`127.0.0.1`的某端口上，这样再结合SSH的ProxyJump功能（就是建立一个SSH加密的从22端口到另一个地址的TCP连接隧道，这里是`127.0.0.1:6000`），从而避免了直接将内网机器的SSH端口宝路达公网，只需要客户端机器简单配置一下SSH的ProxyJump，免去了下载、配置`frpc`的麻烦，同时可以保证全隧道连接加密保证访问安全。

#### `frp`配置文件
1. 公网机器：需要同时运行`frps`和`frpc`

`frps.toml`：

```text
bindPort = 7000 # 服务端口
auth.token = "<token>" # 用于保证安全的token，只有与frps.toml中设置一致的frpc才可以连接到该frps，我是使用openssl rand -hex 20命令随机生成的
```

`frpc.toml`：  

```text
serverAddr = "127.0.0.1" # 部署在与frps运行的同一台机器上
serverPort = 7000 # 访问frps的服务端口
auth.token = "<与frps.tml中一致的token>"

# 多台内网机器可以共用一个frps，通过创建多个[[visitors]]字段的方式部署多条隧道，例如这里的写法
# 第一台机器
[[visitors]]
name = "secret_ssh_visitor"
type = "stcp"
# 要访问的 stcp 代理的名字
serverName = "secret_ssh" # 要与内网机器的frpc.toml中设置一致
secretKey = "密码" # 需要与被访问的内网机器该项设置的密码一致才可以访问
# 绑定本地端口以访问 SSH 服务
bindAddr = "127.0.0.1"
bindPort = 6000
transport.useEncryption = true
transport.useCompression = true

# 第二台机器
[[visitors]]
name = "secret_ssh_visitor2"
type = "stcp"
# 要访问的 stcp 代理的名字
serverName = "secret_ssh2"
secretKey = "密码"
# 绑定本地端口以访问 SSH 服务
bindAddr = "127.0.0.1"
bindPort = 6001
transport.useEncryption = true
transport.useCompression = true

# 第二台机器有一个vllm服务也可以加入
[[visitors]]
name = "name3"
type = "stcp"
# 要访问的 stcp 代理的名字
serverName = "vllm"
secretKey = "密码"
# 绑定本地端口以访问 SSH 服务
bindAddr = "0.0.0.0"
bindPort = <port3>
transport.useEncryption = true
transport.useCompression = true
```

2. 内网机器：
`frpc.toml`：  

```text
serverAddr = "<公网机器IP>"
serverPort = 7000 # frps服务端口
auth.token = "<与frps.tml中一致的token>"
transport.tls.enable = true

[[proxies]]
name = "secret_ssh" # 配置要被访问的服务名，要与访问者的`frpc.toml`中一致
type = "stcp"
# 只有与此处设置的 secretKey 一致的用户才能访问此服务
secretKey = "密码" # 只有与这里配置的密码一致的frpc才可以访问到此服务
localIP = "127.0.0.1"
localPort = 22
transport.useEncryption = true
transport.useCompression = true

# 这台机器上还有一个vllm服务
[[proxies]]
name = "vllm"
type = "stcp"
# 只有与此处设置的 secretKey 一致的用户才能访问此服务
secretKey = "密码"
localIP = "0.0.0.0"
localPort = 9091
transport.useEncryption = true
transport.useCompression = true
```

#### 注册为服务
使用`sudo vim /etc/systemd/system <name>.service`的命令将以下配置文件根据自己需要修改后贴进去：
1. 公网机器：需要配置`frps`和`frpc`两个服务
- 配置文件：  
`frps.service`:  

```text
[Unit]
# 服务名称，可自定义
Description = frp server
After = network.target syslog.target
Wants = network.target

[Service]
Type = simple
# 启动frps的命令，需修改为您的frps的安装路径
ExecStart = /etc/frp/frps -c /etc/frp/frps.toml
Restart=on-failure
RestartSec=60

[Install]
WantedBy = multi-user.target
```

`frpc.service`:   

```text
[Unit]
# 服务名称，可自定义
Description = frp client
After = network.target syslog.target
Wants = network.target
After = frps.service

[Service]
Type = simple
# 启动frps的命令，需修改为您的frps的安装路径
ExecStart = /etc/frp/frpc -c /etc/frp/frpc.toml
Restart=on-failure
RestartSec=60

[Install]
WantedBy = multi-user.target
```

- 启动服务：然后设置服务开机启动并启动服务，注意要先启动`frps`再启动`frpc`：
```bash
sudo systemctl enable frps
sudo systemctl enable frpc
sudo systemctl start frps
sudo systemctl start frpc
```

2. 内网机器：需要配置`frpc`服务
- 配置文件：  
`frpc.service`:  

```text
[Unit]
# 服务名称，可自定义
Description = frp client
After = network.target syslog.target
Wants = network.target
After = frps.service

[Service]
Type = simple
# 启动frps的命令，需修改为您的frps的安装路径
ExecStart = /etc/frp/frpc -c /etc/frp/frpc.toml
Restart=on-failure
RestartSec=60

[Install]
WantedBy = multi-user.target
```

- 启动服务：然后设置服务开机启动并启动服务：

```bash
sudo systemctl enable frpc
sudo systemctl start frpc
```

### 访问内网机器的客户端机器的SSH配置文件
```text
Host public_machine  
  HostName <public_ip>
  User <public_user>
  Port 22  
  IdentityFile <public_machine_identity_file>

Host provate_machine
  HostName 127.0.0.1
  User <private_user>
  Port <在public_machine的frpc.toml中配置的port>
  ProxyJump public_machine
  IdentityFile <private_machine_identity_file>
```

然后就可以在VSCode等IDE或者Terminal中直接通过SSH访问内网机器了。