title: Android SDK更新失败解决办法
date: 2011-08-02 05:18:23
updated: 2013-08-22 03:11:04
comments: true
categories: app
tags: Android
---
前不久，有群内兄弟反映不能更新Android SDK。如下图：
![](http://imogos.oss.aliyuncs.com/android-sdk-update/31.jpg)
提示`sdk r6 sdk setup.exe`升级失败，提示环境变量错误，重新设置无效；提示病毒或者系统路径影响，无可操作方法。
`SDK Manager:failed to install Warning! A folder failed to be renamed or moved.On Windows this typically means that a propram is using that folder&gt;&gt;&gt;D:/android/android-sdk-windows/tools.When ready,press YES to try again.`
<!--more-->
以前也确实遇到过这样的情况：
直接在Myeclipse等IDE或者进入tools文件夹运行android.bat都会出现这样的错误，原因可能是tools文件夹正在被访问所致。

**解决办法一：**
①配置SDK环境变量（如果在安装时已经配置，跳过此步骤）
②cmd运行`android --help`是否有android命令提示（如果在安装时已经配置，跳过此步骤）
![](http://imogos.oss.aliyuncs.com/android-sdk-update/32.jpg)
配置环境变量成功后，就可以更新SDK了。
③cmd运行android，进入‘Android SDK and AVD Manager’。后面的步骤就不用说了吧！
![](http://imogos.oss.aliyuncs.com/android-sdk-update/33.jpg)
![](http://imogos.oss.aliyuncs.com/android-sdk-update/34.jpg)

**解决办法二：**
①.进入sdk的temp文件夹，下载好后会有一个tools_r06-windows.zip(版本号可能会不一样)
②.解压此文件，如解压成tools_r06-windows
③.进入tools_r06-windows文件夹，将tools_r06-windows里的文件复制
④.覆盖sdk根目录的tools文件夹中的文件
