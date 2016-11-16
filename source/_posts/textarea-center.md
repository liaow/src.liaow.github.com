title: textarea标签莫名其妙的居中显示文本
date: 2011-08-19 01:09:23
comments: true
categories: web
tags:
- textarea
---
今天可是被`<textarea></textarea>`悲剧了一回，`<textarea>`标签中的内容总是居中显示。 奇怪了，无论怎么设置`style`都不能解决，而且提交到`form`中去也是莫名其妙的多出很多空格来。
几经折腾终于找到了原因：
如果`<textarea>`标签写成如下形式
``` html
<textarea>
内容内容</textarea>
```
浏览器会解析为:
``` html
<textarea></br>内容内容</textarea>
```
所以在写`<textarea>`标签时一定要写成
``` html
<textarea>内容内容</textarea>
```
