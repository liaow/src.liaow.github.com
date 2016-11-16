title: oracle服务批处理
date: 2011-04-24 11:48:23
comments: true
categories: script
tags:
- Oracle
- Bat
---
主要功能：
 - 启动Oracle相关服务[ start]
 - 停止Oracle相关服务[ stop ]
 - 一键备份指定用户DB[ bak ]
    * 非Win32系统请自行修改变量rardir值为WinRAR安装路径
    * 备份目标文件夹默认D:\backdir
    * 备份压缩包密码默认123456
更新时间：2013-04-18 17:30PM 当前版本：V 0.2
<!--more-->
``` bat
@echo off
cls
title Oracle Control - @liaowu v0.4
:menu
color 0A
echo =============================================
echo       [ start]   Start All Ora Service
echo       [ stop ]   Stop All Ora Service
echo       [ bak  ]   Backup Ora Db And Rar
echo       [ exit ]   Exit Oracle Control Bat
echo =============================================

echo [注]非Win32系统请自行修改rardir路径为WinRAR路径
echo   Version 0.2 Update 2013-04-18 17:30PM

:cho
set choice=
set /p choice=          cmd:
IF NOT "%choice%"=="" SET choice=%choice%
if /i "%choice%"=="start" goto start
if /i "%choice%"=="stop" goto stop
if /i "%choice%"=="bak" goto bak
if /i "%choice%"=="exit" goto exit
echo invalid command!
goto cho

:start
net start OracleVssWriterORCL
net start OracleDBConsoleorcl
echo [桌面版可以注释] net start OracleJobSchedulerORCL
echo [桌面版可以注释] net start OracleMTSRecoveryService
net start OracleOraDb11g_home1ClrAgent
net start OracleOraDb11g_home1TNSListener
net start OracleServiceORCL
goto menu

:stop
net stop OracleVssWriterORCL
net stop OracleDBConsoleorcl
echo [桌面版可以注释] net stop OracleJobSchedulerORCL
echo [桌面版可以注释] net stop OracleMTSRecoveryService
net stop OracleOraDb11g_home1ClrAgent
net stop OracleOraDb11g_home1TNSListener
net stop OracleServiceORCL
goto menu

:bak
set rardir="C:\\Program Files\\WinRAR"
set conn=
set backdir=D:/backdir
set pwd=123456

set /p conn=         请输入数据库连接[user/pwd@db]:
set /p backdir=      请输入备份目标文件夹[默认D:\backdir]:
set /p pwd=          请设定备份压缩包密码[默认123456]:

IF NOT "%conn%"=="" (
    goto backupdb
    )ELSE (
    echo 请输入正确的用户名/密码@数据库
    goto bak
    )
:mddir
md "%backdir%"
echo 目录"%backdir%"已经创建
goto backupdb

:backupdb
if not exist %backdir% (
    CHOICE /C YN /T 5 /D Y /M 备份目录不存在,是否由程序自动创建?
    IF ERRORLEVEL 2 goto bak
    IF ERRORLEVEL 1 goto mddir
    )

echo 备份目录"%backdir%"
cd %backdir%
%backdir:~0,2%

echo 创建临时目录"%backdir%/temp%date:~0,4%%date:~5,2%%date:~8,2%"
md temp%date:~0,4%%date:~5,2%%date:~8,2%

echo 导出数据库
exp %conn% file=%backdir%/temp%date:~0,4%%date:~5,2%%date:~8,2%/ora_%date:~0,4%%date:~5,2%%date:~8,2%%RANDOM%.dmp

echo 压缩并创建密码
cd %rardir%
c:

rar a -p%pwd% %backdir%/ora_backup_%date:~0,4%%date:~5,2%%date:~8,2%.rar  %backdir%/temp%date:~0,4%%date:~5,2%%date:~8,2%

cd %backdir%
%backdir:~0,2%

echo 删除临时目录"%backdir%/temp%date:~0,4%%date:~5,2%%date:~8,2%"下文件
cd temp%date:~0,4%%date:~5,2%%date:~8,2%
del *.* /q

echo 删除临时目录"%backdir%/temp%date:~0,4%%date:~5,2%%date:~8,2%"
cd..
rd temp%date:~0,4%%date:~5,2%%date:~8,2%

goto menu

:exit
exit
```
oracle服务批处理.Bat[下载地址](http://imogos.oss.aliyuncs.com/OracleServiceControl.rar)
