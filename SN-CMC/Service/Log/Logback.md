## 系统日志

|日期|作者|备注|
|------|------|------|
|2018-09-28|吕希兵|初稿|
|2018-10-18|冀小雷|完善|
|2019-10-17|吕希兵|优化|

### 指南

N9B开发平台选用SLF4J配合LogBack作为日志框架，据原作者描述LogBack相比log4j实现效率更高，具有更好的处理I/O异常的能力，以及原生支持SLF4J等特性。

#### 概念说明

**核心功能**

1. 用于系统出现异常时的问题排查，通过配置文件实现对日志信息的过滤，通过“日志级别设置工具”动态设置日志级别，通过“系统日志查询”查看日志信息，极大的简化故障排查的难度。
1. 用于系统关键信息的记录，如：菜单、界面、按钮、远程的操作日志；具有一定安全风险的运维操作日志，如：SQL工具、TAC工具等，*** 出于安全考虑此日志必须打开 ***。

*** 另外作为日志信息的入口，LogBack配合Betas、Kafka、Redis、ELK（ElasticSearch + LogStash + Kibana）完成日志分析、系统监控、业务统计等附加功能，底层平台将此类功能在“时效监控系统SLA”中实现，详细内容参考：[时效监控【SN-SLA】](help.html?helpFile=help/SN-SLA/SLA/SLA.md)***
1. 系统相关操作的统计分析。
1. 接入系统、消息中间件、用户消息中心等应用系统服务处理的统计及监控。
1. 业务层面执行情况的统计及分析。
1. 支持服务器相关系统服务的日志收集分析，如：Linux操作系统、Redis、Oracle等。

*** 建议部署结构 *** 

LogBack/Beats-》Kafka/Redis（2选1）-》LogStash-》ElasticSearch-》Kibana（可选）

#### 使用说明

**N9B日志文件配置**

```
开发环境默认使用snadk-utils下的logback.xml。
可以通过以下方式定义自己的日志配置文件：
在snconfig/下配置名为logback-config.xml的文件，系统默认使用此名字的配置文件(名字固定)。
在System.properties中配置属性logback.configurationFile=D:\snsoft90\snconfig\xxx.xml指向你设定的配置文件 。
```

**单独指定SLF4J**
平台设置 `SLF4J` 并指向 `LogBack`，有些三方功能比较特殊，需单独指定（在System.properties 中设置，也可以通过 JVM 属性-D指定），如下：

```
#设置Freemarker使用SLF4J（LogBack）
org.freemarker.loggerLibrary=SLF4J

#设置Dubbo使用SLF4J（LogBack）
dubbo.application.logger=slf4j

#设置Hibernate(Validator)使用SLF4J（LogBack）
org.jboss.logging.provider=slf4j
```

### 手册

#### SLF4J

JAVA简单日志门面（Simple Logging Facade for Java，缩写SLF4J），是一套封装Logging 框架的API，以Facade模式实现。它不是具体的日志解决方案，它只服务于各种各样的日志系统。可以在软件部署的时候决定要使用的 Logging 框架，目前主要支持的有Java Logging API、log4j及logback等框架。以MIT授权方式发布。

log4j 提供 TRACE, DEBUG, INFO, WARN, ERROR 5个级别日志等级。
传统的日志中，我们大量使用字符串拼接的方式，如果当前系统日志Level不需要记录Debug等级的信息，就会浪费时间在产生不必要的信息上。
```
logger.debug("There are now " + count + " user accounts: " + userAccountList);
```
或者会使用是否debug模式的判断：
```
if (logger.isDebugEnabled()) {
    logger.debug("There are now " + count + " user accounts: " + userAccountList);
}
```
而slf4j提供了一种新的记录方式，就是使用占位符，在代码中表示为"{}"。"{}"会在运行时被某个提供的实际字符串所替换。这不仅降低了你代码中字符串连接次数，而且还节省了新建的String对象。
``````
logger.debug("There are now {} user accounts: {}", count, userAccountList);
```
以下是两个官方说明，可以解决各位开发同学日常使用中的部分选择疑惑。
- Logger变量是否应该声明为 static
声明Logger变量是否为static，目前官方给出的意见是两者各有优缺，交给各位开发同学选择。
	1. static
	优点：写法更为常见和成熟；CPU占用更少，loggers只在主类初始化时加载一次； 内存占用更低，logger 声明只占用一个引用。
	缺点：无法在应用间通过某种logger仓库技术共享logger实例；无法IOC
	1. non-static
	优点：可以在应用间共享logger实例；支持IOC。
	缺点： 写法不常见；CPU占用更高,每次创建主类实例时都会加载;内存占用更高,主类的每个实例都会创建一个引用。

- 在class中声明logger的推荐方式

如下是声明logger的一种写法，static由开发人员自主决定是否使用：
```
package some.package;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class MyClass {
  final (static) org.slf4j.Logger logger = org.slf4j.LoggerFactory.getLogger(this.getClass());
}
```

上面这种写法中，class作为声明的一部分导致代码无法复制粘贴。
在JDK7+中可以使用MethodHandles.lookup()来解决如上问题：
```
package some.package;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import java.lang.invoke.MethodHandles;

public class MyClass {
final (static) Logger logger = LoggerFactory.getLogger(MethodHandles.lookup().lookupClass());
}
```
这样就可以复制粘贴了。

#### Logback

##### LogBack结构

LogBack被分为3个组件，logback-core, logback-classic 和 logback-access。
- logback-core提供了LogBack的核心功能，是另外两个组件的基础。
- logback-classic则实现了Slf4j的API，所以当想配合Slf4j使用时，需要将logback-classic加入classpath。
- logback-access是为了集成Servlet环境而准备的，可提供HTTP-access的日志接口。

##### 与SLF4J的结合

我们从java代码最简单的获取logger开始
```
Logger logger = LoggerFactory.getLogger(xxx.class.getName());
```
LoggerFactory是slf4j的日志工厂，获取logger方法就来自这里。
```
public static Logger getLogger(String name) {
ILoggerFactory iLoggerFactory = getILoggerFactory();
return iLoggerFactory.getLogger(name);
}
```
这个方法里面有分为两个过程。第一个过程是获取ILoggerFactory，就是真正的日志工厂。第二个过程就是从真正的日志工厂中获取logger。 第一个过程又分为三个部分。
- 加载org/slf4j/impl/StaticLoggerBinder.class文件
```
paths = ClassLoader.getSystemResources(STATIC_LOGGER_BINDER_PATH);
//STATIC_LOGGER_BINDER_PATH = "org/slf4j/impl/StaticLoggerBinder.class"
```
- 随机选取一个StaticLoggerBinder.class来创建一个单例
当项目中存在多个StaticLoggerBinder.class文件时，运行项目会出现以下日志：
```
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/C:/Users/jiangmitiao/.m2/repository/ch/qos/logback/logback-classic/1.1.3/logback-classic-1.1.3.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/C:/Users/jiangmitiao/.m2/repository/org/slf4j/slf4j-log4j12/1.7.12/slf4j-log4j12-1.7.12.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [ch.qos.logback.classic.util.ContextSelectorStaticBinder]
```
最后会随机选择一个StaticLoggerBinder.class来创建一个单例

```
StaticLoggerBinder.getSingleton();
```
- 返回一个ILoggerFactory实例
```
StaticLoggerBinder.getSingleton().getLoggerFactory();
```
所以slf4j与其他实际的日志框架的集成jar包中，都会含有这样的一个org/slf4j/impl/StaticLoggerBinder.class类文件，并且提供一个ILoggerFactory的实现。

第二个过程就是每一个和slf4j集成的日志框架中实现ILoggerFactory方法getLogger()的实例所做的事了。


##### 功能组件

- Logger
作为日志的记录器，把它关联到应用的对应的context上后，主要用于存放日志对象，也可以定义日志类型、级别。

```
org.slf4j.Logger logger = org.slf4j.LoggerFactory.getLogger(getClass());
logger.trace("");//一般不使用
logger.debug("");
logger.info("");
logger.warn("");
logger.error("");
```
- Appender
主要用于指定日志输出的目的地，目的地可以是控制台、文件、远程套接字服务器、MySQL、PostreSQL、Oracle和其他数据库、JMS和远程UNIX Syslog守护进程等。

- Layout 
负责把事件转换成字符串，格式化的日志信息的输出。

- logger context
各个logger 都被关联到一个 LoggerContext，LoggerContext负责制造logger，也负责以树结构排列各 logger。其他所有logger也通过org.slf4j.LoggerFactory 类的静态方法getLogger取得。 getLogger方法以 logger 名称为参数。用同一名字调用LoggerFactory.getLogger 方法所得到的永远都是同一个logger对象的引用。

- 有效级别及级别的继承

Logger 可以被分配级别。级别包括：TRACE、DEBUG、INFO、WARN 和 ERROR，定义于 ch.qos.logback.classic.Level类。如果logger没有被分配级别，那么它将从有被分配级别的最近的祖先那里继承级别。root logger 默认级别是 DEBUG。

- 打印方法与基本的选择规则
打印方法决定记录请求的级别。例如，如果 L 是一个 logger 实例，那么，语句 L.info("..")是一条级别为 INFO 的记录语句。记录请求的级别在高于或等于其 logger 的有效级别时被称为被启用，否则，称为被禁用。记录请求级别为 p，其 logger的有效级别为 q，只有则当p>=q时，该请求才会被执行。
该规则是 logback 的核心。级别排序为： TRACE < DEBUG < INFO < WARN < ERROR。


##### 系统配置

开发环境默认使用snadk-utils下的logback.xml。
可以通过以下方式定义自己的日志配置文件：
在snconfig/下配置名为logback-config.xml的文件，系统默认使用此名字的配置文件(名字固定)。
在System.properties中配置属性logback.configurationFile=D:\snsoft90\snconfig\xxx.xml指向你设定的配置文件 。

如果配置文件 logback-config.xml 和 logback.xml 都不存在，那么 logback 默认地会调用BasicConfigurator ，创建一个最小化配置。最小化配置由一个关联到根 logger 的ConsoleAppender 组成。输出用模式为`%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n`的 PatternLayoutEncoder 进行格式化。root logger 默认级别是 DEBUG。

**Logback的配置文件**
Logback 配置文件的语法非常灵活。正因为灵活，所以无法用 DTD 或 XML schema 进行定义。尽管如此，可以这样描述配置文件的基本结构：以`<configuration>`开头，后面有零个或多个`<appender>`元素，有零个或多个`<logger>`元素，有最多一个`<root>`元素。

**Logback默认配置的步骤**
- 尝试在 classpath 下查找文件 logback-config.xml
- 如果文件不存在，则查找 logback.groovy
- 如果文件不存在，则查找文件 logback.xml
- 如果文件都不存在，logback 用 BasicConfigurator 自动对自己进行配置，这会导致记录输出到控制台。

##### 日志级别
日志级别从高到低：OFF、 FATAL、ERROR、WARN、INFO、 DEBUG、TRACE、ALL
定义于ch.qos.logback.classic.Level类
如果 logger没有被分配级别，那么它将从有被分配级别的最近的祖先那里继承级别。
root logger 默认级别是 DEBUG。

```
OFF为最高等级关闭了日志信息				
FATAL为可能导致应用中止的严重事件错误			
ERROR为严重错误主要是程序的错误				
WARN为一般警告，比如session丢失				
INFO为一般要显示的信息，比如登录登出			
DEBUG为程序的调试信息						
TRACE为比DEBUG更细粒度的事件信息	  			
ALL为最低等级，将打开所有级别的日志			
还有一个特殊值INHERITED或者同义词NULL，代表强制执行上级的级别。
```
*** 日志级别设置 *** 

测试、预生产、生产环境需要调试日志输出时可以动态设置日志级别，底层平台提供了“日志级别设置”的工具，主要包括以下两个功能：

- 级别设置：动态调整日志级别，可以多选“日志名称”，默认调整未独立定义的“子日志名称”。
- 重置所有级别：恢复所有的初始日志级别，注意不支持选择特定“日志名称”恢复日志级别。

### 实践

#### 统计分析日志

|日志类型|日志名称|处理程序|备注|
|------|------|------|------|
|客户端日志：菜单日志|snsoft.xjs.Menu|XjsUtils||
|客户端日志：按钮日志|snsoft.xjs.button|XjsUtils||
|服务端日志：界面日志|snsoft.ui.invoke|UIInvokeController|包括初始化、远程、数据装载、数据存盘等|
|服务端日志：远程日志|snsoft.ui.invoke|UIInvokeController||
|服务端日志：数据库工具日志|snsoft.cmc.tools.ui|UISqlExecuteListener等|SQL Mongo ES工具日志|
|服务端日志：TAC日志|snsoft.cmc.tools.service.impl.RunScript|RunScript||
|服务端日志：登录日志|snsoft.bas.auth.LoginService|LoginService|||
|服务端日志：业务日志|snsoft.busilogger|BusiLoggerService|||

#### 数据库监控

*** 日志名称 *** 
- snsoft.dx.sql.SqlReadDataSet
- snsoft.dx.sql.SqlDatabase

*** 系统选项 *** 
- Diag.TraceSqlTime = 5000  :  日志输出查询超过 5000毫秒 的 sql，默认为5000毫秒；日志等级为WARN
- Diag.LogDBInfoTimeout = 30  :  日志输出超过连接时间 30秒的 数据库连接，默认为0不启用;日志等级为WARN 
- DbPool.WarnCount = 50  : 连接池超过50个时日志输出，默认为0不启用；日志等级为WARN

#### 消息中间件工具日志

- snsoft.mq.message.service.impl.MQMessageToolsSeviceImpl
- snsoft.mq.buff.service.impl.MQBuffToolsSeviceImpl

#### LogBack文件配置

##### 根节点
`configuration`

```
scan：当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true.
scanPeriod：设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当scan为true时，此属性生效。默认的时间间隔为1分钟.
debug：当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。
```

XML配置代码：

```
<configuration scan="true"scanPeriod="60 second"debug="false">
    <!-- 其他配置省略-->
</configuration>
```

##### 主要子节点

主要包括3个子节点：appender、logger和root。


###### appender

负责写日志的组件，主要用于指定日志输出的目的地，目的地可以是控制台、文件、远程套接字服务器、MySQL、PostreSQL、Oracle和其他数据库、JMS和远程UNIX Syslog守护进程等。`<appender>`有两个必要属性name和class。name指定appender名称，class指定appender的全限定名。

官方说明：[https://logback.qos.ch/manual/appenders.html](https://logback.qos.ch/manual/appenders.html)

*** ConsoleAppender  ***

把日志添加到控制台，有以下子节点：
- `<encoder>`：对日志进行格式化。
- `<target>`：字符串 System.out 或者 System.err ，默认 System.out

```xml
<configuration>
<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
<encoder>
<pattern>%-4relative [%thread] %-5level %logger{35} - %msg %n</pattern>
</encoder>
</appender>
<root level="DEBUG">
<appender-ref ref="STDOUT" ></appender>
</root>
</configuration>
```




*** FileAppender *** 

把日志添加到文件，有以下子节点：
- `<file>`：被写入的文件名，可以是相对目录，也可以是绝对目录，如果上级目录不存在会自动创建，没有默认值。
- `<append>`：如果是 true，日志被追加到文件结尾，如果是 false，清空现存文件，默认是true。
- `<encoder>`：对记录事件进行格式化。（具体参数稍后讲解 ）
- `<prudent>`：如果是 true，日志会被安全的写入文件，即使其他的FileAppender也在向此文件做写入操作，效率低，默认是 false

```java
//将所有的debug类型的日志输出到debug.log中
>
 <appender name="DEBUGFILE" class="ch.qos.logback.core.FileAppender">
    <file>debug.log</file>
    <!-- append: true,日志被追加到文件结尾; false,清空现存文件;默认是true -->
    <append>true</append>
    <filter class="ch.qos.logback.classic.filter.LevelFilter">
        <!-- LevelFilter: 级别过滤器，根据日志级别进行过滤 -->
        <level>DEBUG</level>
        <onMatch>ACCEPT</onMatch>
        <onMismatch>DENY</onMismatch>
    </filter>
    <encoder>
        <Pattern> [%d{yyyy-MM-dd HH:mm:ss.SSS}] [%5level] [%thread] %logger{0} %msg%n</Pattern>
        <charset>UTF-8</charset>
    </encoder>
</appender>
```

*** RollingFileAppender ***

滚动记录文件，先将日志记录到指定文件，当符合某个条件时，将日志记录到其他文件。有以下子节点：
- `<file>`：被写入的文件名，可以是相对目录，也可以是绝对目录，如果上级目录不存在会自动创建，没有默认值。
- `<append>`：如果是 true，日志被追加到文件结尾，如果是 false，清空现存文件，默认是true。
- `<encoder>`：对记录事件进行格式化。（具体参数稍后讲解 ）
- `<rollingPolicy>`:当发生滚动时，决定 RollingFileAppender 的行为，涉及文件移动和重命名。
- `<triggeringPolicy>`: 告知 RollingFileAppender 何时激活滚动。
- `<prudent>`：当为true时，不支持FixedWindowRollingPolicy。支持TimeBasedRollingPolicy，但是有两个限制，1不支持也不允许文件压缩，2不能设置file属性，必须留空。


```
<appender name="INFOFILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
</appender >
```

另外还有SocketAppender、SMTPAppender、DBAppender、SyslogAppender、SiftingAppender，并不常用，这些就不在这里讲解了，大家可以参考官方文档。当然大家可以编写自己的Appender。


*** 自定义Appender *** 

```
snsoft.dx.mdb.logging.LogbackAppender 输出到MongoDB数据库
snsoft.dx.mdb.logging.LogbackESAppender 输出到ES数据库
snsoft.dx.mdb.logging.KafkaAppender 输出到Kafka，后面对接LogStash 
snsoft.dx.mdb.logging.LogbackRedisAppender 输出到Redis，后面对接LogStash

```
配置方式
```
	<appender name="MongoAppender" class="snsoft.dx.mdb.logging.LogbackAppender">
		<!--MongoDB数据库的地址，表名固定为applog，一般不用配置走底层默认MongoDB地址-->
		<mguri>mongodb://127.0.0.1:27017/n10log</mguri>
	</appender>
	<appender name="ESAppender" class="snsoft.dx.mdb.logging.LogbackESAppender">
		<!--Elasticsearch的地址，表名固定为applog，一般不用配置走底层默认Elasticsearch地址-->
		<clusterNodes>127.0.0.1:9300</clusterNodes>//集群节点地址，单台配一个
		<clusterName>esearch</clusterName>//集群名
	</appender>
```


###### logger

用来设置某一个包或者具体的某一个类的日志打印级别、以及指定`<appender>`。`<logger>`仅有一个name属性，一个可选的level和一个可选的additivity属性。

- name：用来指定受此logger约束的某一个包或者具体的某一个类。
- level：用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，还有一个特殊值INHERITED或者同义词NULL，代表强制执行上级的级别。如果未设置此属性，那么当前logger将会继承上级的级别。
- additivity：是否向上级logger传递打印信息。默认是true。

`<logger>`可以包含零个或多个`<appender-ref>`元素，标识这个appender将会添加到这个logger。


```
//未设置appender，此logger本身不打印任何信息，未设置级别，继承root的级别[DEBUG]，为“DEBUG”及大于“DEBUG”的日志信息传递给root，
<logger name="ch.qos.logback" />

<!--
- 1.将级别为“INFO”及大于“INFO”的日志信息交给此logger指定的名为“STDOUT”的appender处理，在控制台中打出日志，
-   不再向此logger的上级传递打印信息
- 2.additivity：为false，表示此logger的打印信息不再向上级传递,如果设置为true，会打印两次(传递给root，root再打印一次)
- 3.appender-ref：指定了名字为"STDOUT"的appender。
-->
<logger name="com.weizhi.common.LogMain" level="INFO" additivity="false">
    <appender-ref ref="STDOUT"/>
</logger>
```

###### root

相当于`<logger>`元素的根元素。

- level：用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，不能设置为INHERITED或者同义词NULL。默认是DEBUG。
- `<root>`可以包含零个或多个`<appender-ref>`元素，标识这个appender将会添加到这个logger。


###### filter

`appender`接收到的日志信息，再经过`filter`过滤后输出，包含标签：
```
<OnMismatch>:不符合条件的
<OnMatch>:符合条件的
返回DENY，日志将立即被抛弃不再经过其他过滤器；
返回NEUTRAL，有序列表里的下个`filter`过接着处理日志；
返回ACCEPT，日志会被立即处理，不再经过剩余`filter`
```
```
<filter class="ch.qos.logback.core.filter.EvaluatorFilter"> //过滤条件
	<evaluator class="ch.qos.logback.classic.boolex.GEventEvaluator"> //日志信息中包含以下单词的时候
		<expression>
		<![CDATA[
			e.message.contains("cachekcols,cachekeym") ||
			e.message.contains("Searching directory")
		]]>
		</expression>
	</evaluator>
	<OnMismatch>NEUTRAL</OnMismatch> //不符合交给下一个filter继续处理
	<OnMatch>DENY</OnMatch> //符合的直接抛弃
</filter>
<filter class="ch.qos.logback.classic.filter.LevelFilter"> //该文件只输出级别为INFO的日志
	<level>INFO</level>
	<onMatch>ACCEPT</onMatch>
	<onMismatch>DENY</onMismatch>
</filter>
<filter class="ch.qos.logback.classic.filter.ThresholdFilter">
    <level>INFO</level>
</filter>
```

**rollingPolicy**


* TimeBasedRollingPolicy
最常用的滚动策略，它根据时间来制定滚动策略，既负责滚动也负责触发滚动。有以下子节点：

- `<fileNamePattern>`: 必要节点，包含文件名及“%d”转换符，%d”可以包含一个Java.text.SimpleDateFormat指定的时间格式，如：%d{yyyy-MM}。如果直接使用 %d，默认格式是 yyyy-MM-dd。RollingFileAppender 的file字节点可有可无，通过设置file，可以为活动文件和归档文件指定不同位置，当前日志总是记录到file指定的文件（活动文件），活动文件的名字不会改变；如果没设置file，活动文件的名字会根据fileNamePattern 的值，每隔一段时间改变一次。“/”或者“\”会被当做目录分隔符。
- `<maxHistory>`: 可选节点，控制保留的归档文件的最大数量，超出数量就删除旧文件。假设设置每个月滚动，且<maxHistory>是6，则只保存最近6个月的文件，删除之前的旧文件。注意，删除旧文件时，那些为了归档而创建的目录也会被删除。

* FixedWindowRollingPolicy
 根据固定窗口算法重命名文件的滚动策略。有以下子节点：
- `<minIndex>`:窗口索引最小值。
- `<maxIndex>`:窗口索引最大值，当用户指定的窗口过大时，会自动将窗口设置为12。
- `<fileNamePattern>`: 必须包含“%i”例如，假设最小值和最大值分别为1和2，命名模式为 mylog%i.log,会产生归档文件mylog1.log和mylog2.log。还可以指定文件压缩选项，例如，mylog%i.log.gz 或者 没有log%i.log.zip

* triggeringPolicy
* SizeBasedTriggeringPolicy： 查看当前活动文件的大小，如果超过指定大小会告知RollingFileAppender 触发当前活动文件滚动。只有一个节点:
- `<maxFileSize>`:这是活动文件的大小，默认值是10MB。

* SizeBasedTriggeringPolicy
查看当前活动文件的大小，如果超过指定大小会告知RollingFileAppender 触发当前活动文件滚动。只有一个节点:
- `<maxFileSize>`:这是活动文件的大小，默认值是10MB。


```
<!-- 按照固定窗口模式生成日志文件，当文件大于20MB时，生成新的日志文件。
        -    窗口大小是1到3，当保存了3个归档文件后，将覆盖最早的日志。
        -    可以指定文件压缩选项
        -->
<rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
    <fileNamePattern>error.%d{yyyy-MM}(%i).log.zip</fileNamePattern>
    <minIndex>1</minIndex>
    <maxIndex>3</maxIndex>
    <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
    <maxFileSize>100MB</maxFileSize>
    </timeBasedFileNamingAndTriggeringPolicy>
    <maxHistory>30</maxHistory>
</rollingPolicy>
<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
        <!-- 每天生成一个日志文件，保存30天的日志文件
        - 如果隔一段时间没有输出日志，前面过期的日志不会被删除，只有再重新打印日志的时候，会触发删除过期日志的操作。
        -->
        <fileNamePattern>info.%d{yyyy-MM-dd}.log</fileNamePattern>
        <maxHistory>30</maxHistory>
        <TimeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
        <maxFileSize>100MB</maxFileSize>
        </TimeBasedFileNamingAndTriggeringPolicy>
    </rollingPolicy>
```

*** 自定义Filter  ***

官方说明：https://logback.qos.ch/manual/filters.html
Logback有两种Filter，基于chain的Regular Filter和TurboFilter。
```
Regular Filter:
这是一种继承ch.qos.logback.core.filter.Filter抽象类的Filter，只需要实现decide(ILoggingEvent event)方法。
decide方法返回ch.qos.logback.core.spi.FilterReply的枚举类型：DENY,NEUTRALorACCEPT
返回DENY或ACCEPT时，处于filterChain的调用会立即结束，根据返回结果处理Appender是否输出日志。返回NEUTRAL时，则继续调用下一个Filter。
TurboFilter:
这是一种继承ch.qos.logback.classic.turbo.TurboFilter抽象类的Filter，同样只需实现decide方法，返回FilterReply类型。
不同的是：
TurboFilter同 logging context 绑定，而不是appender。它的作用域比普通Filter更大，每次logging请求都会触发，而不是局限于特定appender。
在LoggingEvent创建前调用，因此，相比普通FIlter会更高效。
```

###### encoder

官方说明：[https://logback.qos.ch/manual/encoders.html](https://logback.qos.ch/manual/encoders.html)
负责两件事，一是把日志信息转换成字节数组，二是把字节数组写入到输出流。 目前PatternLayoutEncoder 是唯一有用的且默认的encoder ，有一个`<pattern>`节点，用来设置日志的输入格式。使用“%”加“转换符”方式。
格式修饰符，与转换符共同使用：
可选的格式修饰符位于“%”和转换符之间。第一个可选修饰符是左对齐 标志，符号是减号“-”；接着是可选的最小宽度 修饰符，用十进制数表示。如果字符小于最小宽度，则左填充或右填充，默认是左填充（即右对齐），填充符为空格。如果字符大于最小宽度，字符永远不会被截断。最大宽度 修饰符，符号是点号”.”后面加十进制数。如果字符大于最大宽度，则从前面截断。点符号“.”后面加减号“-”在加数字，表示从尾部截断。

```
>
<encoder>
    <Pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%5level] [%thread] %logger{0} %msg%n</Pattern> //日志输出格式 
    <charset>UTF-8</charset>
  </encoder>

%d/%date:输出日志时间点的日期或时间，默认格式ISO8601，可以在其后指定格式
%p/%le/%level:输出日志级别
%t/%thread:输出产生日志线程名
%c/%lo/%logger:输出日志的logger名,可有一个整形参数，功能是缩短logger名，设置为0表示只输入logger最右边点符号之后的字符串。
%C/%class:输出执行记录的调用者的全限定名，即类名全路径
%contextName：输出上下文名称
%L/%line:输出执行日志请求的行号，避免使用，影响效率
%m/%msg/%message:输出应用程序提供的信息
%M/%method:输出日志请求的方法名，影响效率，避免使用
%n:换行符
%r/%relative:输出从程序启动到创建日志记录的时间，单位毫秒
```

##### 其他子节点

###### 上下文名称

*** 设置上下文名称 ***
每个logger都关联到logger上下文，默认上下文名称为“default”。但可以使用`<contextName>`设置成其他名字，用于区分不同应用程序的记录。一旦设置，不能修改。

###### property

*** 设置变量  ***
用来定义变量值的标签，`<property>` 有两个属性，name和value；其中name的值是变量的名称，value的值时变量定义的值。通过`<property>`定义的值会被插入到logger上下文中。定义变量后，可以使“${}”来使用变量。
XML代码：
```
<configuration scan="true" scanPeriod="60 second" debug="false">
<property name="APP_Name" value="myAppName" />
<contextName>${APP_Name}</contextName>
<!-- 其他配置省略-->
</configuration>
```

###### timestamp

*** 获取时间戳字符串 *** 
两个属性 key:标识此`<timestamp>` 的名字；datePattern：设置将当前时间（解析配置文件的时间）转换为字符串的模式，遵循Java.txt.SimpleDateFormat的格式。
XML代码：
```
<configuration scan="true" scanPeriod="60 second" debug="false">
<timestamp key="bySecond" datePattern="yyyyMMdd'T'HHmmss"/>
<contextName>${bySecond}</contextName>
<!-- 其他配置省略-->
</configuration>
```

#### LogBack参考配置

- 开发环境参考配置

底层、应用层功能DEBUG级别；三方功能WARN（如果有需要INFO）级别；
全部“控制台”方式输出
[logback-config.xml 开发环境参考配置](help/SN-CMC/Service/Log/logback-config-dev.xml)

- 测试、预生产、生产环境参考配置

底层、应用层功能、三方功能默认设置WARN级别；需统计分析的日志信息设置INFO级别；故障排查时针对性的打开DEBUG级别；
根据各自项目的应用需求确定输出方式。
[logback-config.xml 测试、预生产、生产环境参考配置](help/SN-CMC/Service/Log/logback-config-prd.xml)

```xml
<
?xml version="1.0" encoding="UTF-8"?>
<!--
-scan:当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true
-scanPeriod:设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。
- 当scan为true时，此属性生效。默认的时间间隔为1分钟
-debug:当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。
-
- configuration 子节点为 appender、logger、root
-->
<configuration scan="true" scanPeriod="60 second" debug="false">

<!-- 负责写日志,控制台日志 -->
<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">

<!-- 一是把日志信息转换成字节数组,二是把字节数组写入到输出流 -->
<encoder>
<Pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%5level] [%thread] %logger{0} %msg%n</Pattern>
<charset>UTF-8</charset>
</encoder>
</appender>

<!-- 文件日志 -->
<appender name="DEBUG" class="ch.qos.logback.core.FileAppender">
<file>debug.log</file>
<!-- append: true,日志被追加到文件结尾; false,清空现存文件;默认是true -->
<append>true</append>
<filter class="ch.qos.logback.classic.filter.LevelFilter">
<!-- LevelFilter: 级别过滤器，根据日志级别进行过滤 -->
<level>DEBUG</level>
<onMatch>ACCEPT</onMatch>
<onMismatch>DENY</onMismatch>
</filter>
<encoder>
<Pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%5level] [%thread] %logger{0} %msg%n</Pattern>
<charset>UTF-8</charset>
</encoder>
</appender>

<!-- 滚动记录文件，先将日志记录到指定文件，当符合某个条件时，将日志记录到其他文件 -->
<appender name="INFO" class="ch.qos.logback.core.rolling.RollingFileAppender">
<File>info.log</File>

<!-- ThresholdFilter:临界值过滤器，过滤掉 TRACE 和 DEBUG 级别的日志 -->
<filter class="ch.qos.logback.classic.filter.ThresholdFilter">
<level>INFO</level>
</filter>

<encoder>
<Pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%5level] [%thread] %logger{0} %msg%n</Pattern>
<charset>UTF-8</charset>
</encoder>

<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
<!-- 每天生成一个日志文件，保存30天的日志文件
- 如果隔一段时间没有输出日志，前面过期的日志不会被删除，只有再重新打印日志的时候，会触发删除过期日志的操作。
-->
<fileNamePattern>info.%d{yyyy-MM-dd}.log</fileNamePattern>
<maxHistory>30</maxHistory>
<TimeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
<maxFileSize>100MB</maxFileSize>
</TimeBasedFileNamingAndTriggeringPolicy>
</rollingPolicy>
</appender >

<!--<!– 异常日志输出 –>-->
<!--<appender name="EXCEPTION" class="ch.qos.logback.core.rolling.RollingFileAppender">-->
<!--<file>exception.log</file>-->
<!--<!– 求值过滤器，评估、鉴别日志是否符合指定条件. 需要额外的两个JAR包，commons-compiler.jar和janino.jar –>-->
<!--<filter class="ch.qos.logback.core.filter.EvaluatorFilter">-->
<!--<!– 默认为 ch.qos.logback.classic.boolex.JaninoEventEvaluator –>-->
<!--<evaluator>-->
<!--<!– 过滤掉所有日志消息中不包含"Exception"字符串的日志 –>-->
<!--<expression>return message.contains("Exception");</expression>-->
<!--</evaluator>-->
<!--<OnMatch>ACCEPT</OnMatch>-->
<!--<OnMismatch>DENY</OnMismatch>-->
<!--</filter>-->

<!--<triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">-->
<!--<!– 触发节点，按固定文件大小生成，超过5M，生成新的日志文件 –>-->
<!--<maxFileSize>5MB</maxFileSize>-->
<!--</triggeringPolicy>-->
<!--</appender>-->

<appender name="ERROR" class="ch.qos.logback.core.rolling.RollingFileAppender">
<file>error.log</file>

<encoder>
<Pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%5level] [%thread] %logger{0} %msg%n</Pattern>
<charset>UTF-8</charset>
</encoder>

<!-- 按照固定窗口模式生成日志文件，当文件大于20MB时，生成新的日志文件。
- 窗口大小是1到3，当保存了3个归档文件后，将覆盖最早的日志。
- 可以指定文件压缩选项
-->
<rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
<fileNamePattern>error.%d{yyyy-MM}(%i).log.zip</fileNamePattern>
<minIndex>1</minIndex>
<maxIndex>3</maxIndex>
<timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
<maxFileSize>100MB</maxFileSize>
</timeBasedFileNamingAndTriggeringPolicy>
<maxHistory>30</maxHistory>
</rollingPolicy>
</appender>

<!-- 异步输出 -->
<appender name ="ASYNC" class= "ch.qos.logback.classic.AsyncAppender">
<!-- 不丢失日志.默认的,如果队列的80%已满,则会丢弃TRACT、DEBUG、INFO级别的日志 -->
<discardingThreshold >0</discardingThreshold>
<!-- 更改默认的队列的深度,该值会影响性能.默认值为256 -->
<queueSize>512</queueSize>
<!-- 添加附加的appender,最多只能添加一个 -->
<appender-ref ref ="ERROR"/>
</appender>

<!--
- 1.name：包名或类名，用来指定受此logger约束的某一个包或者具体的某一个类
- 2.未设置打印级别，所以继承他的上级<root>的日志级别“DEBUG”
- 3.未设置additivity，默认为true，将此logger的打印信息向上级传递；
- 4.未设置appender，此logger本身不打印任何信息，级别为“DEBUG”及大于“DEBUG”的日志信息传递给root，
- root接到下级传递的信息，交给已经配置好的名为“STDOUT”的appender处理，“STDOUT”appender将信息打印到控制台；
-->
<logger name="ch.qos.logback" />

<!--
- 1.将级别为“INFO”及大于“INFO”的日志信息交给此logger指定的名为“STDOUT”的appender处理，在控制台中打出日志，
- 不再向次logger的上级 <logger name="logback"/> 传递打印信息
- 2.level：设置打印级别（TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF），还有一个特殊值INHERITED或者同义词NULL，代表强制执行上级的级别。
- 如果未设置此属性，那么当前logger将会继承上级的级别。
- 3.additivity：为false，表示此logger的打印信息不再向上级传递,如果设置为true，会打印两次
- 4.appender-ref：指定了名字为"STDOUT"的appender。
-->
<logger name="com.weizhi.common.LogMain" level="INFO" additivity="false">
<appender-ref ref="STDOUT"/>
<!--<appender-ref ref="DEBUG"/>-->
<!--<appender-ref ref="EXCEPTION"/>-->
<!--<appender-ref ref="INFO"/>-->
<!--<appender-ref ref="ERROR"/>-->
<appender-ref ref="ASYNC"/>
</logger>

<!--
- 根logger
- level:设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，不能设置为INHERITED或者同义词NULL。
- 默认是DEBUG。
-appender-ref:可以包含零个或多个<appender-ref>元素，标识这个appender将会添加到这个logger
-->
<root level="DEBUG">
<appender-ref ref="STDOUT"/>
<!--<appender-ref ref="DEBUG"/>-->
<!--<appender-ref ref="EXCEPTION"/>-->
<!--<appender-ref ref="INFO"/>-->
<appender-ref ref="ASYNC"/>
</root>
</configuration>
```

#### 参考文档

https://logback.qos.ch/documentation.html