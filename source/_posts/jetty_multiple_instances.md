title: Jetty9 多实例（多环境）部署项目
date: 2016-2-3 10:18:23
comments: true
categories: java
tags:
- Jetty
---

Jetty9版本为jetty-9.3.6.v20151106，需要注意的是Jetty9的JDK要求1.7+。
OS版本为CentOS Linux release 7.1.1503 (Core)
JDK版本为Java(TM) SE Runtime Environment (build 1.8.0_60-b27)
此文不涉及Nginx反向代理配置。

### 一、背景
因为资源问题需在同一台机器上配置Jetty生产环境和开发环境，要求独立启动互不影响，所以配置了两个实例分别对应生产环境开发环境。
 <!--more-->

### 二、配置
 1. JDK 1.7+环境准备，略
 2. 下载jetty并解压缩，略
 3. 生产环境Jetty配置
    使用标准配置即可（可以自定义默认端口号）。
    修改start.ini，找到jetty.http.port打开注释并修改端口号
``` ini
jetty.http.port=8000
```
 4. 开发环境Jetty配置
    同一台机器上配置Jetty生产环境和开发环境，只需要保障pid和port不一样即可。配置步骤如下：
    ① 将生成环境Jetty目录copy一份（假设重命名为jetty_dev）
    ② 修改开发环境Jetty（副本Jetty）的默认端口，方法同上。
    ③ 修改开发环境Jetty（副本Jetty）的pid路径。在jetty_dev/bin/jetty.sh前面加上JETTY_HOME和JETTY_RUN。注：jetty_dev/run目录需要创建
``` bash
    JETTY_HOME=/alidata/server/jetty_dev
    JETTY_RUN=$JETTY_HOME/run
```
   此时，开发环境jetty.pid文件位于jetty_dev/run，和生产环境都为jetty.pid（位于默认目录/var/run)，互不影响。
 5. 开发环境Jetty另一种配置
    ①、②同上
    ③ 将jetty_dev/bin/jetty.sh重命名为jetty_dev.sh。在jetty_dev/bin/jetty_dev.sh前面加上JETTY_HOME
``` bash
    JETTY_HOME=/alidata/server/jetty_dev
```
   此时，开发环境jetty_dev.pid和生产环境jetty.pid文件同时位于/var/run，文件名不同互不影响。

