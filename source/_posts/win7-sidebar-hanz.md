title: 初尝win7侧边栏工具汉化
date: 2011-08-12 05:18:23
comments: true
categories: other
tags:
- win
---
今日在某网站看到一个很酷的流量监控侧边栏小工具，功能如下：
*   信号强度
*   内/外网IP
*   Google地图IP信息
*   网络测速（64位系统）
*   上/下行速度监控
*   网络使用率
<!--more-->
预览图：
![](http://imogos.oss.aliyuncs.com/win7-sidebar-hanz/win7-sidebar-hanz1.png)
一番使用下来感觉便被这炫酷的悬浮效果所吸引，遂决定将其汉化推广。
1.  首先找到侧边栏的安装目录
    `C:/Users/[用户]/AppData/Local/Microsoft/Windows Sidebar/Gadgets/Network_Meter_V6.4.gadget`；
2.  选项汉化，将`settings.html`对应翻译成中文；
3.  界面汉化，将`gadget.html`对应翻译成中文；
4.  侧边栏说明汉化，将`gadget.xml`对应翻译成中文；
5.  将所有需要汉化的文件都汉化后，用winrar压缩，压缩格式要选择zip的，文件重命名为`xxx.gadget`。 注意：压缩时应该选择所有的文件进行压缩，而不是选择外层的目录。

汉化后效果：
![](http://imogos.oss.aliyuncs.com/win7-sidebar-hanz/win7-sidebar-hanz2.jpg)
汉化版载地址： [CSDN下载](http://download.csdn.net/source/3092070) [远景下载](http://bbs.pcbeta.com/viewthread.php?tid=847267&page=1&extra=)
原版下载地址：[官方](http://addgadget.com/network_meter/)
