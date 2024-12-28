## 业务日志
|日期|作者|备注|
|------|------|------|
|2018-11-08|冀小雷|初版|

### 功能介绍

业务日志的记录及后续分析适用于大型项目，将日志记录文件后推送至Redis；
通过配置将Redis中的日志经过清洗存储至ES中。

记录日志的格式为：
PREFIX|ticket|slacode+pointid+runtime#[innercode+outercode]#[grp1,grp2...]#[agg1,agg2...]

### 开发规范

@startuml

Title 业务日志记录类图

BusiLoggerService <|.. BusiLoggerServiceImpl
BusiLoggerServiceImpl .right.> BusiLogger

interface BusiLoggerService {
	void info(BusiLogger loggerInfo);
}

class BusiLoggerServiceImpl {
	# Logger logger;
}

class BusiLogger {
	- String slacode;
	- String pointid;
	- String innercode;
	- String outercode;
	- long runtime;
	- String[] grps;
	- BigDecimal[] aggs;
}
@enduml

### Logback配置文件

```xml
<configuration>
	...
	<appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<!-- 可指定路径-->
		<file>busilogger.log</file>
		<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
	  		<!-- daily rollover -->
			<fileNamePattern>busilogger.%d{yyyy-MM-dd HH}.log</fileNamePattern>
	  		<!-- keep 30 days' worth of history capped at 3GB total size -->
			<maxHistory>30</maxHistory>
			<totalSizeCap>5GB</totalSizeCap>
		</rollingPolicy>
		<encoder>
			<pattern>%d|%msg%n</pattern>
		</encoder>
		<filter class="ch.qos.logback.core.filter.EvaluatorFilter">
			<evaluator class="ch.qos.logback.classic.boolex.GEventEvaluator">
				<expression><![CDATA[
					e.loggerName.equals("snsoft.busilogger")
				]]></expression>
			</evaluator>
			<OnMismatch>DENY</OnMismatch>
			<OnMatch>ACCEPT</OnMatch>
		</filter>
	</appender>
	...
</configuration>
```