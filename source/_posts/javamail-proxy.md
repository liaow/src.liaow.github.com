title: javamail通过局域网代理发送邮件方式
date: 2011-07-13 05:18:23
updated: 2013-08-22 03:11:04
comments: true
categories: java
tags: Javamail
---
由于公司服务器与因特网连接都需要经过指定ip的代理配置，给javamail部署上线带来了一定的麻烦。 本文将详细为javamail配置代理发送邮件作解读。

一、通过代码方式，设置系统配置。
①SOCKS代理，只能使用`Properties props = System.getProperties();`来获取系统配置，否则设置代理不生效（本机测试结果，仅供参考）。参考代码：
``` java
Properties props = System.getProperties();
props.setProperty("ocksProxySet","true");
props.setProperty("socksProxyHost","192.168.1.3");
props.setProperty("socksProxyPort","1080");
```
<!--more-->
②HTTP代理，既可使用`System.getProperties();`也可使用`Properties props = new Properties();`方式。参考代码：
``` java
Properties props = new Properties();
Properties props = System.getProperties();
props.setProperty("proxySet","true");
props.setProperty("http.proxyHost","192.168.1.3");
props.setProperty("http.proxyPort","808");
```
二、通过配置文件
①通过Spring配置文件加载
配置JavaMailSender发送邮件实现类，在配置javaMailProperties设置代理（配置跟代理方式相似，本机测试只对HTTP代理有效）
``` xml
<bean id="senderImpl" class="org.springframework.mail.javamail.JavaMailSenderImpl">
    <property name="host">
        <value>smtp.sohu.com</value>
    </property>
    <property name="username">
        <value>username</value>
    </property>
    <property name="password">
        <value>password</value>
    </property>
    <property name="javaMailProperties">
        <props>
            <prop key="proxySet">true</prop>
            <prop key="http.proxyHost">192.168.1.3</prop>
            <prop key="http.proxyPort">808</prop>
            <prop key="mail.smtp.auth">true</prop>
        </props>
    </property>
</bean>
```

②通过配置服务器代理
在服务器启动时添加启动参数（以Tomcat6为例，tomcat6w.exe属性如下）
HTTP代理：
`-Dhttp.proxyHost=192.168.1.3 -Dhttp.proxyPort=808`
SOCKS代理：
`-DsocksProxyHost=192.168.1.3 -DsocksProxyPort=1080`

至此，javamail通过局域网代理发送邮件的两种方式已经介绍完毕，希望对各位读者有帮助。 如有纰漏之处还望高人留言指点。
