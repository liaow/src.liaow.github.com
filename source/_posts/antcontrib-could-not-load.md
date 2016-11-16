title: antcontrib提示错误Could not load definitions from resource解决办法
date: 2011-08-08 10:18:23
comments: true
categories: java
tags:
- Ant

---
使用Ant过程中，在build.xml添加
`<taskdef resource="net/sf/antcontrib/antlib.xml"/>`
时候执行 for task时候总是提示错误：
`[taskdef] Could not load definitions from resource net/sf/antcontrib/antlib.xml. It could not be found.`
<!--more-->
解决办法有2个：
*   AntHome中加入ant-contrib-1.0b3.jar
    在Eclipse-->Windows-->Preferences-->Ant-->Runtime-->classpath-->加入ant-contrib-1.0b3.jar
*   在build.xml中引入antcontrib.properties
``` xml
<taskdef resource=”net/sf/antcontrib/antcontrib.properties”/>
    <classpath/>
        <pathelement location="/java/tools/apache-ant-1.6.5/lib/ant-contrib.jar"/>
    </classpath/>
</taskdef/>
```
