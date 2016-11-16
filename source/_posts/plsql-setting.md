title: PLSQL习惯设置
date: 2012-04-01 04:37:53
updated: 2013-08-22 03:11:04
comments: true
categories: db
tags:
- PLSQL
---
工欲善其事，必先利其器，一些优秀的PL/SQL习惯设置能帮助开发节省不少时间。
## 一、备份与恢复PL/SQL首选项
*   备份
    ①选项首选项集
    ![](http://imogos.oss.aliyuncs.com/plsqlsetting/plsqlsetting-1.png)
    ②选择定义首选项
    ![](http://imogos.oss.aliyuncs.com/plsqlsetting/plsqlsetting-2.png)
    ③复制所有首选项保存即备份。
*   恢复
    同上，将保存的文本粘贴到文本域，确定，应用
<!--more-->
## 二、常用设置
*   1、 登录历史
    定义：存储历史，带口令存储
    固定用户：格式user/passwd@db（分组用“>”，注释用“--”）
    如图
    ![](http://imogos.oss.aliyuncs.com/plsqlsetting/plsqlsetting-3.png)
    示例
    ``` json
    >本机ORCL@orcl
    --dba，sysdba，sysoper，oracle权限最高的用户
    SYS/123@ORCL as SYSDBA
    --dba，oracle权限次高的用户
    SYSTEM/123@ORCL
    SYSTEM/123@ORCL as sysdba
    SYSTEM/123@ORCL as sysdba
    ```

*   2、 关键字大小写
    根据习惯设置，一般设置大写

*   3、 自动替换
    如输入“s_”会自动替换成“SELECT_ FROM”
    ``` json
    s=SELECT
    sf=SELECT * FROM
    sc=SELECT COUNT(1) FROM
    sr=SELECT R.*,ROWID FROM
    d=DELETE FROM
    i=INSERT INTO
    gb=GROUP BY
    od=ORDER BY
    td=TO_DATE('','yyyymmdd hh24:mi:ss')
    ```

*   4、 代码助手
    ①建议在延时比较严重的情况下去掉自动激活，改而设置快捷“Alt+/”，保持跟Eclipse一致；
    ②编辑/选择/大写改成Alt+F5，编辑/选择/小写改成Ctrl+F5，这个和UE保持一致；
    ③编辑/重做改成Ctrl+Y，和大多工具保持一致；
    ④文件/新建/SQL窗口 Ctrl+N
    ⑤文件/关闭 Ctrl+W
    ⑥编辑/PL/SQL 美化器 Ctrl+Shift+F
    ⑦编辑/选择/注释 Ctrl+/
    ⑧编辑/选择/取消注释 Ctrl+Shift+/
    ⑨编辑/专用复制/Java Ctrl+Shift+C
