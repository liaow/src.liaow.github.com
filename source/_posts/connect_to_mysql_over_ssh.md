title: SSH隧道连接远程MYSQL数据库[Client,JDBC]
date: 2015-10-21 15:18:23
comments: true
categories: db
tags:
- MySQL
- Java
---

本文分别介绍JDBC及客户端（以Navicat为例子）通过SSH隧道连接远程MySQL数据库的步骤：

# 一、JDBC
JDBC url配置需要使用隧道端口转发，本文环境：win8 + 绿色版ssh命令

 1. 建立SSH隧道连接
 原理是利用ssh命令在本机开个端口，这个端口为隧道的入口端口，通过SSH隧道转发端口达到访问远程mysql服务。
 ``` bash
 ssh -NCPf root@10.12.1.2 -L 3366:192.168.1.100:3306`
 -    root@10.12.1.2 是登陆mysql这个远程服务器的SSH用户名和IP地址
 -    -L 3366:192.168.1.100:3306 本机IP为192.168.1.100端口为3388到远程IP端口为3306的映射。也可以设置为0.0.0.0，这样任意IP都可以连接

 -参数解释
 -C    使用压缩功能，是可选的，加快速度。
 -P    用一个非特权端口进行出去的连接。
 -f    一旦SSH完成认证并建立port forwarding，则转入后台运行。
 -N    不执行远程命令。该参数在只打开转发端口时很有用（V2版本SSH支持）
 ```
 <!--more-->
 2. 检查端口
 执行完后查看本地连接情况
 ``` bash
 $ netstat -tulnp | grep 3366
 tcp    0    0 127.0.0.1:3366    0.0.0.0:*    LISTEN    14273/ssh
 tcp    0    0 ::1:3366          :::*         LISTEN    14273/ssh
 ```
 连接测试可以继续通过客户端测试或者使用`mysql -uroot -pxxxx -P3366 -h127.0.0.1 `

 # 二、客户端
新建连接步骤如下：
**注意：常规项主机名需要填loalhost或者127.0.0.1，用户名及密码同SSH；**
 1. 配置SSH项
 ![配置SSH项](http://imogos.oss-cn-hangzhou.aliyuncs.com/connect_to_mysql_over_ssh/1.png)
 2. 配置常规项
 ![配置常规项](http://imogos.oss-cn-hangzhou.aliyuncs.com/connect_to_mysql_over_ssh/2.png)

