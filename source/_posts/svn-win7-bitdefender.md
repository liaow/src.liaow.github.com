title: 解决TortoiseSVN和Subclipse在win7+BitDefender下不能正常工作
date: 2011-07-13 05:18:23
comments: true
categories: other
tags:
- win
---
发现不能使用TortoiseSVN更新项目了，打开版本浏览器一直是处于‘请稍候，正在启动版本库浏览器’状态，无法使用。
![](http://imogos.oss.aliyuncs.com/tortoisesvn-subclipse-win7-bitdefender/41.png)
<!--more-->
到处找问题所在，终于将故障锁定在BitDefender上了。（我也纳闷了，在xp下同样的软件配置环境TortoiseSVN和Subclipse都用得好好的）
**问题描述：**
在Anti-virus的Protection Level找到了答案，如图：
![](http://imogos.oss.aliyuncs.com/tortoisesvn-subclipse-win7-bitdefender/42.png)
原来是BitDefender对HTTP协议进行过滤和监视。对于像SVN这样扩展HTTP的，会出现冲突，使得TortoiseSVN不能正常工作。尝试着把TortoiseSVN中的 TortoiseProc.exe加入白名单还是不能解决。当然也不能禁用Anti-virus功能，要不然装它干甚？！
**解决办法：**
以安全模式启动，在`[HKLM]/[SOFTWARE]/[BitDefender]`下面建立一个`REG_MULTI_SZ`值，名字叫`HttpAppSkip`，数据里面填入需要忽悠的程序的路径。如图：
![](http://imogos.oss.aliyuncs.com/tortoisesvn-subclipse-win7-bitdefender/43.png)
重启OS，重新打开TortoiseSVN版本库浏览器，解决！
但是。。。。。
**问题描述：**当我写完代码准备用Subclipse提交时又出问题了，进度条总是停留在0%。
**解决办法：**将myeclipse进程也加进‘HttpAppSkip’，回车隔开。
![](http://imogos.oss.aliyuncs.com/tortoisesvn-subclipse-win7-bitdefender/44.png)
至此，解决TortoiseSVN和Subclipse在win7+BitDefender下不能正常工作已经完美解决。
