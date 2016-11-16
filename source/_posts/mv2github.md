title: 博客迁移Github
date: 2013-08-11 19:18:23
updated: 2013-08-22 03:11:04
comments: true
categories: others
tags:
- GitHub
- Markdown
- Hexo
---
由于godaddy主机到期且速度缓慢，所以一直在寻求一个免费又能体现伪geek写博方式，于是我发现了github,后来接触到了Hexo：
*   不可思议的快速 ─ 只要一眨眼静态文件即生成完成
*   支持 Markdown
*   仅需一道指令即可部署到 GitHub Pages 和 Heroku
*   已移植 Octopress 插件
*   高扩展性、自订性
*   兼容于 Windows, Mac & Linux
<!--more-->

安装什么的就不多说了，点链接[Hexo](https://hexo.io/zh-cn) 。

有几个需要注意的地方，google到的很多教程才慢慢摸索出自己想要的结果：
*   域名绑定github
   关于域名绑定的方法官方也写得有点含糊，google到的更是照搬不误。 以master分支类型为例：
    - 你需要建一个以'username.github.com'的版本库；
    - 在'_config.yml'配置版本库；
    - ping或者dig username.github.com[对应github刚刚新建的主版本库]，得到版本库对应的ip；
    - 如果顶级域名，增加A记录到上一步得到的ip。若是二级域名还需要增加C记录；
    - 在主版本库中添加CNAME，内容顶级域名[imogos.com](http://imogos.com)（不带www前缀）。二级域名填对应的域名，如[me.imogos.com]
*   中文乱码
     文件另存为'Utf-8 无BOM'格式
