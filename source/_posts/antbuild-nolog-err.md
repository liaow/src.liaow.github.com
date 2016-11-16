title: Ant编译莫名终止或者无日志输出解决方法
date: 2011-03-05 02:18:23
comments: true
categories: java
tags:
- Ant
- Eclipse
---
可能用ant开发的人都或多或少遇到过这样的情况：
*   一是ant在编译时有可能出现编译未完成便莫名其妙的异常终止了；
*   二是ant在编译时能完成但是日志“卡”在某行就不在输出了。
<!--more-->
遇到这种情况，可能是中文问题惹的祸，有几种解决方案可以选择
*   如果是名其妙的异常终止了，尝试以下方法：
    1、eclipse->Run->External tool->External tool Configurations（或者build.xml右键）->Main->Arguments:
    `-log org.apache.tools.ant.NoBannerLogger`
    2、eclipse->Run->External tool->External tool Configurations（或者build.xml右键）->JRE->VM arguments:
    `-Duser.language=en`
*   如果是日志“卡”住了，尝试以下方法：
eclipse->Run->External tool->External tool Configurations（或者build.xml右键）->Common->Console Encoding: <span style="color: #ff0000;">GBK</span>
