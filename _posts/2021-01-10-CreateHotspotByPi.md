---
layout: post        # page:单页面,不在归档索引;post:有前后页面，索引
title: 使用树莓派创建热点     # 标题
date: 2021-01-10    # 日期
categories:         # 集合,暂未使用
Author:  jinyu      # 作者
tags: [笔记]              # 标签，博客分类页面显示
comments: true          # bool,是否可以评论
toc:                # bool,是否有目录
pinned:             # bool,是否置顶
description: 
hide: 
---

家里自己的房间离路由器很远，Wi-Fi信号不好，房间只有网线接口但MacBook没有网线接口，所以用树莓派整了个热点给电脑用。

正好今天整完了，就写篇博客记录下过程。

<!-- more -->

最终使用的是：https://github.com/lakinduakash/linux-wifi-hotspot这个仓库的项目来实现的热点。

## 查找工具

简单的进行搜索，我看到了一个GitHub上的Wi-Fi Access Point库：https://github.com/oblique/create_ap，不过这个库已经停止维护了，README.md上有这么一段介绍：

> **NOT MAINTAINED**
>
> This project is no longer maintained.
>
> If you are still interested in this project, checkout the following fork that also provides GUI: [lakinduakash/linux-wifi-hotspot](https://github.com/lakinduakash/linux-wifi-hotspot)

所以我前去接任维护的仓库：https://github.com/lakinduakash/linux-wifi-hotspot，去了解下。

## 开始部署

我的树莓派没有安装图形化界面，所以我用的是命令行方式部署，更详细的内容可以参考文档：https://github.com/lakinduakash/linux-wifi-hotspot/blob/master/src/scripts/README.md

### 安装

先很常规的clone仓库到本地，然后make安装。

```shell
git clone https://github.com/lakinduakash/linux-wifi-hotspot
cd linux-wifi-hotspot/src/scripts
make install
```

### 启动热点

如果使用默认的如下方式开启热点，启用的热点是WPA加密，似乎不太安全，所以这里我用的是WPA2通道，更加安全。

```shell
# sudo create_ap 无线网卡名 有线网卡名 热点名 密码
sudo create_ap wlan0 eth0 MyAccessPoint MyPassPhrase
```

更安装的pipe部署代码如下：

```shell
# sudo echo -e "热点名\n密码" | sudo create_ap 有线网卡名 无线网卡名
sudo echo -e "MyAccessPoint\nMyPassPhrase" | sudo  create_ap wlan0 eth0
```

上述命令执行过后，预期就会输出如下结果（部分私人信息我用`******`打了码）,就意味着热点启动成功，可以愉快的连接啦：

```shell
jinyu@ubuntu:~$ sudo echo -e "******\n******" |sudo  create_ap wlan0 eth0
Config dir: /tmp/create_ap.wlan0.conf.HCBP7vbt
PID: 5661
Network Manager found, set wlan0 as unmanaged device... DONE
Sharing Internet using method: nat
hostapd command-line interface: hostapd_cli -p /tmp/create_ap.wlan0.conf.HCBP7vbt/hostapd_ctrl
Configuration file: /tmp/create_ap.wlan0.conf.HCBP7vbt/hostapd.conf
wlan0: Could not connect to kernel driver
Using interface wlan0 with hwaddr ****** and ssid "******"
wlan0: interface state UNINITIALIZED->ENABLED
wlan0: AP-ENABLED
wlan0: STA ****** IEEE 802.11: associated
wlan0: AP-STA-CONNECTED ******
wlan0: STA ****** RADIUS: starting accounting session ******
wlan0: STA ****** WPA: pairwise key handshake completed (RSN)
```

当然如果要持久化开启热点，可以使用`Systemd service`来托管服务，也可以像我，直接使用`screen`创建了一个窗口来托管`create_ap`。

## 可能遇到的问题

ERROR: Failed to initialize lock

出现这个问题的主要原因是，热点关闭进程出了问题。在GitHub上也有相关Issue：https://github.com/oblique/create_ap/issues/384

解决方法就是进入`\tmp`目录，删除目录下文件名形如`create_ap.*.lock`的文件。类似代码如下：

```shell
sudo rm /tmp/create_ap.all.lock
```

## REF

* https://github.com/lakinduakash/linux-wifi-hotspot
* https://github.com/lakinduakash/linux-wifi-hotspot/blob/master/src/scripts/README.md
* https://zhuanlan.zhihu.com/p/101089893
* https://github.com/oblique/create_ap
* https://github.com/oblique/create_ap/issues/384