title: Quartz+Spring的集群配置
date: 2011-06-01 15:18:23
comments: true
categories: java
tags:
- Quartz
- Spring
---
原来配置的Quartz是通过spring配置文件生效的，发现在非集群式的服务器上运行良好，但是将工程部署到水平 集群服务器上去后改定时功能不能正常运行，没有任何错误日志，于是从jar包、JDK版本、cronExpression到服务器 类型，甚至查到了服务器操作系统的类型，都没能找到解决的办法，后来才知道是集群惹的祸！ 详细步骤如下：

1、按照Quartz集群工作原理
![](http://imogos.oss.aliyuncs.com/quartz-spring-cluster.jpg)
<!--more-->
图：表示了每个节点直接与数据库通信，若离开数据库将对其他节点一无所知在数据库中建表。建表模版在Quartz包下docs/dbTables下，选择相应的数据库和版本即可。DB2_V8的11个Table列表如下：
``` xml
QRTZ_JOB_LISTENERS
QRTZ_TRIGGER_LISTENERS
QRTZ_FIRED_TRIGGERS
QRTZ_PAUSED_TRIGGER_GRPS
QRTZ_SCHEDULER_STATE
QRTZ_LOCKS
QRTZ_SIMPLE_TRIGGERS
QRTZ_CRON_TRIGGERS
QRTZ_TRIGGERS
QRTZ_JOB_DETAILS
QRTZ_CALENDARS
QRTZ_BLOB_TRIGGERS
```
2、配置数据库连接池，如果spring已经配置则不需要再另行配置，只需在后面配置的`applicationContext-quartz.xml`引入即可。 applicationContext.xml：
``` xml
<bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    <property name="location">
        <value>classpath:dataConfig.properties</value>
    </property>
</bean>
<bean id="ds34" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
    <property name="driverClassName">
        <value>${ds34.driver}</value>
    </property>
    <property name="url">
        <value>${ds34.url}</value>
    </property>
    <property name="username">
        <value>${ds34.username}</value>
    </property>
    <property name="password">
        <value>${ds34.password}</value>
    </property>
    <property name="maxActive" value="5"></property>
    <property name="maxIdle" value="20"></property>
    <property name="maxWait" value="50"></property>
    <property name="defaultAutoCommit" value="true"></property>
</bean>
```
dataConfig.properties：
``` xml
ds34.driver=com.ibm.db2.jcc.DB2Driver
ds34.url=jdbc:db2://192.168.*.*:50000/XXXX
ds34.username=admin
ds34.password=*******
```
3、配置quartz.properties + org.quartz.scheduler.instanceName属性可为任何值，用在 JDBC JobStore 中来唯一标识实例，但是所有集群节点中必须相同。 + org.quartz.scheduler.instanceId　属性为 AUTO即可，基于主机名和时间戳来产生实例 ID。 + org.quartz.jobStore.class属性为 JobStoreTX，将任务持久化到数据中。因为集群中节点依赖于数据库来传播 Scheduler 实例的状态，你只能在使用 JDBC JobStore 时应用 Quartz 集群。这意味着你必须使用JobStoreTX 或是JobStoreCMT 作为 Job 存储；你不能在集群中使用 RAMJobStore。 + org.quartz.jobStore.isClustered 属性为 true，你就告诉了 Scheduler 实例要它参与到一个集群当中。这一属性会贯穿于调度框架的始终，用于修改集群环境中操作的默认行为。 + org.quartz.jobStore.clusterCheckinInterval 属性定义了Scheduler 实例检入到数据库中的频率(单位：毫秒)。Scheduler 检查是否其他的实例到了它们应当检入的时候未检入；这能指出一个失败的 Scheduler 实例，且当前 Scheduler 会以此来接管任何执行失败并可恢复的 Job。通过检入操作，Scheduler 也会更新自身的状态记录。clusterChedkinInterval 越小，Scheduler 节点检查失败的 Scheduler 实例就越频繁。默认值是 15000 (即15 秒)。
quartz.properties：
``` xml
##Quartz 调度任务所需的配置文件
##org.quartz.scheduler.instanceName属性可为任何值，用在 JDBC JobStore 中来唯一标识实例，但是所有集群节点中必须相同。
org.quartz.scheduler.instanceName = HumsScheduler
##org.quartz.scheduler.instanceId　属性为 AUTO即可，基于主机名和时间戳来产生实例 ID。
org.quartz.scheduler.instanceId = AUTO
org.quartz.threadPool.class = org.quartz.simpl.SimpleThreadPool
org.quartz.threadPool.threadCount = 10
org.quartz.threadPool.threadPriority = 5
org.quartz.threadPool.threadsInheritContextClassLoaderOfInitializingThread = true
org.quartz.jobStore.misfireThreshold = 60000
##org.quartz.jobStore.class属性为 JobStoreTX，将任务持久化到数据中。
##因为集群中节点依赖于数据库来传播 Scheduler 实例的状态，你只能在使用 JDBC JobStore 时应用Quartz 集群。
##这意味着你必须使用 JobStoreTX 或是 JobStoreCMT 作为 Job 存储；你不能在集群中使用 RAMJobStore。
orgorg.quartz.jobStore.class = org.quartz.impl.jdbcjobstore.JobStoreTX
orgorg.quartz.jobStore.driverDelegateClass=org.quartz.impl.jdbcjobstore.StdJDBCDelegate
org.quartz.jobStore.tablePrefix = QRTZ_
org.quartz.jobStore.maxMisfiresToHandleAtATime=10
##org.quartz.jobStore.isClustered 属性为 true，你就告诉了 Scheduler 实例要它参与到一个集群当中。
##这一属性会贯穿于调度框架的始终，用于修改集群环境中操作的默认行为。
org.quartz.jobStore.isClustered = true
##org.quartz.jobStore.clusterCheckinInterval 属性定义了Scheduler 实例检入到数据库中的频率(单位：毫秒)。
##Scheduler 检查是否其他的实例到了它们应当检入的时候未检入；这能指出一个失败的 Scheduler 实例，且当前 Scheduler 会以此来接管任何执行失败并可恢复的 Job。
##通过检入操作，Scheduler 也会更新自身的状态记录。clusterChedkinInterval 越小，Scheduler 节点检查失败的 Scheduler 实例就越频繁。默认值是 15000 (即15 秒)。
org.quartz.jobStore.clusterCheckinInterval = 20000
```
4、 配置applicationContext-quartz.xml
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN 2.0//EN" "http://www.springframework.org/dtd/
spring-beans-2.0.dtd">
<beans>
    <bean name="quartzScheduler" class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
        <property name="dataSource">
            <ref bean="ds34"/>  <!--数据源引用指向，包含集群所需的所有表-->
        </property>
        <property name="applicationContextSchedulerContextKey" value="applicationContextKey"/>
        <!--applicationContextSchedulerContextKey： 是org.springframework.scheduling.quartz.SchedulerFactoryBean这个类中把spring上下 文以key/value的方式存放在了quartz的上下文中了，可以用applicationContextSchedulerContextKey所定义的key得到对应的spring上下文-->
        <property name="configLocation" value="classpath:quartz.properties"/>
        <!--configLocation：用于指明quartz的配置文件的位置 -->
        <property name="triggers">
            <list>
                <ref bean="trigger1"/>
            </list>
        </property>
    </bean>
    <bean id="jobDetail1" class="org.springframework.scheduling.quartz.JobDetailBean">
        <property name="jobClass">
            <value>继承QuartzJobBean的类的引用,如果不继承QuartzJobBean可以参考 http://www.javaeye.com/topic/486055</value>
        </property>
    </bean>
    <bean id="trigger1" class="org.springframework.scheduling.quartz.CronTriggerBean">
        <property name="jobDetail" ref="jobDetail1"/>
        <property name="cronExpression" value="0 0/5 * ? * * *"/>
        <!—cronExpression 表达式 -->
    </bean>
</beans>
```
5、配置Job任务注意：加入定时任务有两种方式：
① 继承QuartzJobBean的类，重写executeInternal()，详细写法：
``` xml
<bean id="jobDetail1" class="org.springframework.scheduling.quartz.JobDetailBean">
    <property name="jobClass">
        <value>继承QuartzJobBean的类的引用,如果不继承QuartzJobBean可以参考http://www.javaeye.com/topic/486055</value>
    </property>
</bean>
```
② 用org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean指定类和方法，但是直接使用会报java.io.NotSerializableException异常，一般用网上流传的（需要将两个类copy到自己的工程下，要有springJAR包，Job需要持久化到数据库中，SimpleService必须实现Serializable）frameworkx.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean，可以参考:[http://jira.springframework.org/browse/SPR-3797。详细写法：](http://jira.springframework.org/browse/SPR-3797。详细写法：)
``` xml
<bean id="jobDetail1" class=" 工程里MethodInvokingJobDetailFactoryBean的路径.MethodInvokingJobDetailFactoryBean">
    <property name="targetObject" ref="simpleService"/>
    <property name="targetMethod" value="testMethod1"/>
    <property name="shouldRecover" value="true"/>
</bean>
```
6、配置到spring配置文件，自动调度任务。
``` xml
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>
        <!--Quartz Spring applicationContext -->
        WEB-INF/classes/applicationContext-quartz.xml
    </param-value>
</context-param>
```
7、测试用例
``` java
package test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class MainTest {
    public static void main(String[] args) {
        ApplicationContext springContext = new ClassPathXmlApplicationContext(new String[]
{"classpath:applicationContext.xml","classpath:applicationContext-quartz.xml"});
    }
}
```
