### MQ消息中间件

|日期|作者|备注|
|------|------|------|
|2017-06-05|陈泰林|初稿|
|2018-10-15|陈泰林|修改|
|2019-10-15|陈泰林|修改|

### 指南

#### 主要功能

1. 异步处理
1. 应用解耦
1. 流量削峰
1. 消息顺序执行
1. 限流、熔断
1. 推送模式、订阅模式
1. 暂停、插队
1. 消息服务路由及编排
1. 异构系统事务处理

#### 使用说明

**MQ服务配置**
&#35;SN-MQ服务端=================
SN-MQ.Comm.mongo=true
SN-MQ.Comm.mongo.uri=mongodb://127.0.0.1:27017/mq
SN-MQ.Comm.redis=true
SN-MQ.Comm.redis.host=127.0.0.1
SN-MQ.Comm.redis.port=6379

&#35;如果MQ服务器需要发送WEBSOCKET消息时，必须配置KAFKA；目前MQ走客户端处理消息任务，不需要MQ服务器配置此项
&#35;snsoft.kafka=true
&#35;snsoft.kafka.host=127.0.0.1:9092

&#35;启用时效监控时，必须配置日志KAFKA
&#35;snsoft.kafka.log=true
&#35;snsoft.kafka.log.host=127.0.0.1:9092

&#35;消息执行过程中默认登录人员usercode（系统管理员） 不配置默认adm=================
&#35;Login.DefaultSysUsercode=U000000001

&#35;消息执行过程中分布式配置=================
SN-CORE.Group.Name=SN-MQ

**客户端服务器配置**其他业务服务器配置
&#35;消息执行过程中分布式客户端配置=================
SN-CORE.Group.Name=HC-AT,HC-ES

&#35;如果客户端服务器需要发送WEBSOCKET消息时，必须配置KAFKA
&#35;snsoft.kafka=true
&#35;snsoft.kafka.host=127.0.0.1:9092

&#35;启用时效监控时，必须配置日志KAFKA
&#35;SN-MQ.Monitor.Sla=true
&#35;snsoft.kafka.log=true
&#35;snsoft.kafka.log.host=127.0.0.1:9092

**定时任务**

1. 参考文件：TimerTaskSN-MQ.xml
1. MQ的定时任务底层提供的只是一个“配置模板”是禁用状态，实际使用需要自己同步到数据库中并且指定执行的服务器
1. SN-MQ-消息推送：消息核心定时任务，内部处理主干、降级、延迟、完结的消息，支持集群使用
1. SN-MQ-超次消息恢复处理：超次的消息自动处理（超次消息是捕捉网络异常产生的消息），不支持集群使用
1. SN-MQ-删除过期消息处理：备份的消息过期后自动删除，不支持集群使用
1. SN-MQ-消息扫描：处理扫描报文的消息，支持集群使用
1. SN-MQ-消息缓冲处理：处理缓冲消息，支持集群使用
1. SN-MQ-消息合并、压缩、解压处理：处理合并、压缩、解压文件消息，支持集群使用
1. SN-MQ-消息监控信息处理：消息监控定时任务，不支持集群使用
1. SN-MQ-消息扫描监控信息处理：消息扫描监控定时任务，不支持集群使用

**注意事项**

**事项1：**第一次使用MQ，必须执行消息初始化【位置[消息通知管理](uiinvoke/00/zh_CN/theme0/SN-MQ.Message.html)中初始化工具】
**事项2：**MQ使用的redis可关闭持久化(消息的持久化已通过MongoDB自行实现)，redis每次重启需要重新初始化
```
修改Redis配置文件 redis.conf 
1.注释掉原来的持久化规则
#save 900 1
#save 300 10
#save 60 10000
2.设置为空
save ""
然后重启redis服务即可。
```
**事项3：**MQ部署后，在运行期间，为了换包时保障数据不丢失，要求换包时必须通过MQ服务器管理暂停MQ服务器，并且等待服务器线程数到达为零（实际情况而定，知道没有任务在执行时即可换包），即可换包。
开发模式下可以在消息管理界面点击【消息测试执行】进行本地调试
同步消息说明：
1. 消息同步队列中有记录同步时运行失败消息ID
1. 同步消息失败后解决程序bug后恢复异常消息
1. 同步消息失败后通过其他方式解决了，跳过异常消息
1. 同步消息不正常操作造成同步锁一直锁消息的情况（但又没有异常消息）：

```
清除消息同步锁，下次再来相同分组消息时会自动触发同步
找到正常要执行的消息，重新放入队列[同步触发]，此时正常消息再队列中有2个（重复执行）
```

#### 维护工具

1. 恢复异常消息：
       条件：异常消息
       正常结果：恢复至运行时消息，进队列重新执行
       同步结果：当前消息ID必须等于同步时运行失败消息ID->设置同步，恢复至运行时消息，进队列重新执行
1. 跳过异常消息：
       条件：异常消息
       正常结果：恢复至运行时消息，设置跳过标记，进队列重新执行
       同步结果：当前消息ID必须等于同步时运行失败消息ID->设置同步，恢复至运行时消息，设置跳过标记，进队列重新执行
1. 激活等待消息：
       条件：等待消息
       正常结果：恢复至运行时消息，进队列重新执行
1. 废弃异常消息：
       条件：异常消息
       正常结果：设置作废标记，直接进入备份消息
       同步结果：如果有同步锁禁止作废，否则设置作废标记，直接进入备份消息
1. 重新放入队列：
       条件：非异常消息或者作废的异常消息
       正常结果：进队列重新执行
       同步结果：当前消息ID必须等于同步时运行失败消息ID->进队列重新执行
1. 重新放入队列[同步触发]：
       条件：非异常消息或者作废的异常消息
       正常结果：进队列重新执行
       同步结果：当前消息ID必须等于同步时运行失败消息ID->设置同步，进队列重新执行
1. 消息强制撤回：
       条件：成功消息或者作废消息
       正常结果：恢复至运行时消息，设置不作废，进队列重新执行

### 手册

#### 接入系统说明
1. 位置：客户端UI中cfg/mq/sys/MQSysConfs*.xml
1. 参考：MQSysConfsSN-MQ.xml
1. 用途：MQ消息中对应的系统（请求方/响应方）定义说明
1. MQ接入系统配置请参见对应的Schema文件【[MQSysConfs.xsd](do/BrowseSchema?schema=MQSysConfs.xsd)】
```xml
<?xml version="1.0" encoding="UTF-8"?>
<MQSysConfs xmlns="http://www.snsoft.com.cn/schema/MQSysConfs" xsi:schemaLocation="http://www.snsoft.com.cn/schema/MQSysConfs http://www.snsoft.com.cn/schema/MQSysConfs.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <sysconfs>
	  <sysid>SN-MQ</sysid>
	  <sysname>消息中心</sysname>
	  <sysparamconfs>
	    <paramname>serviceurl</paramname>
	    <paramvalue></paramvalue>
	    <remark>消息中心</remark>
	  </sysparamconfs>
	  <sysparamconfs>
	    <paramname>tokenserv</paramname>
	    <paramvalue></paramvalue>
	    <remark>令牌回调方法</remark>
	  </sysparamconfs>  
	  <sysparamconfs>
	    <paramname>fsmacro</paramname>
	    <paramvalue></paramvalue>
	    <remark>文件宏地址</remark>
	  </sysparamconfs>
	  <sysparamconfs>
	    <paramname>secretcharset</paramname>
	    <paramvalue></paramvalue>
	    <remark>报文编码格式</remark>
	  </sysparamconfs>
	  <sysparamconfs>
	    <paramname>secretkeyspec</paramname>
	    <paramvalue></paramvalue>
	    <remark>加密方式</remark>
	  </sysparamconfs>
	  <sysparamconfs>
	    <paramname>secretkey</paramname>
	    <paramvalue></paramvalue>
	    <remark>秘钥</remark>
	  </sysparamconfs>
	  <sysparamconfs>
	    <paramname>secretiv</paramname>
	    <paramvalue></paramvalue>
	    <remark>位移码</remark>
	  </sysparamconfs>
	  <sysparamconfs>
	    <paramname>secretmsgformat</paramname>
	    <paramvalue></paramvalue>
	    <remark>加密报文格式</remark>
	  </sysparamconfs>  
	  <remark>消息中心</remark>
  </sysconfs>
</MQSysConfs>
```

#### 消息模型说明
1. 位置：客户端UI中cfg/mq/message/*.xml
1. 参考：SN-MQ_MQ-Template_SN-MQ.xml
1. 用途：消息的执行过程定义，核心配置
1. 消息模型请参见对应的Schema文件【[MQMessageConf.xsd](do/BrowseSchema?schema=MQMessageConf.xsd)】
```xml
<?xml version="1.0" encoding="UTF-8"?>
<MQMessageConf xmlns="http://www.snsoft.com.cn/schema/MQMessageConf" xsi:schemaLocation="http://www.snsoft.com.cn/schema/MQMessageConf http://www.snsoft.com.cn/schema/MQMessageConf.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<!-- 1：启用 -->
	<usedflag>0</usedflag>
	<!-- 请求方  -->
	<requester>SN-MQ</requester>
	<!-- 消息类型 -->
	<messagetype>MQ-Template</messagetype>
	<!-- 响应方 -->
	<responder>SN-MQ</responder>
	<!-- 日处理阀值 暂时未使用-->
	<thresholddays>0</thresholddays>
	<!-- 时效阀值 秒计算 当前服务器时间减去消息创建时间大于时效阀值（秒）时优先执行消息  3分钟-->
	<thresholdtimeliness>180</thresholdtimeliness>
	<!-- 保存模式 0  执行后（只在创建消息和消息执行完时存盘）  1 分步骤（消息下每个任务执行后都有存盘） 2 不保存 -->
	<savemodel>1</savemodel>
	<!-- 降级  0 主干 1 降级  2 暂停   4 停止  默认为0  主干  优先级：停止、暂停、降级、主干  -->
	<lesserflag>0</lesserflag>
	<!-- 1：缓冲 -->
	<buffflag>0</buffflag>
	<!-- 缓冲数量   缓冲多少个时合并创建消息 -->
	<buffcount>0</buffcount>
	<!-- 1：消息分组 -->
	<groupflag>0</groupflag>
	<!-- 1：消息加锁 -->
	<lockflag>1</lockflag>
	<!-- 1：同步 -->
	<sequflag>0</sequflag>
	<!-- 1：扫描 -->
	<scanflag>0</scanflag>
	<!-- 1：订阅 -->	
	<topicflag>0</topicflag>
	<!-- 最大线程  -->
	<maxthread>30</maxthread>
	<!-- 日志记录方式  1：运行时日志（result空时记录）  2：结果时日志（result不为空对结果进行记录）     -->
	<logflag>3</logflag>
	<!-- 执行服务器 -->
	<serverid></serverid>
	<!-- 备注说明 -->		
	<remark>MQ标准模板仅供参考</remark>
	<!-- 消息全局参数配置 -->	
	<paramconfs>
		<!-- 参数名称   -->
		<paramname>test1</paramname>
		<!-- 默认值    -->
		<defaultvalue>true</defaultvalue>
		<!-- 1：参数非空   -->
		<notnullflag>1</notnullflag>
		<!-- 参数说明-->
		<remark>测试参数1</remark>
		<!-- 参数覆盖  1 覆盖 0 不可覆盖-->
		<cover>0</cover>
		<!-- 参数校验格式（正则表达式）-->
		<validate><![CDATA[^[0-9]*$]]></validate>
	</paramconfs>
	<paramconfs>
		<!-- 参数名称   -->
		<paramname>test2</paramname>
		<!-- 默认值    -->
		<defaultvalue>true</defaultvalue>
		<!-- 1：参数非空   -->
		<notnullflag>1</notnullflag>
		<!-- 参数说明-->
		<remark>测试可覆盖参数2</remark>
		<!-- 参数覆盖  1 覆盖 0 不可覆盖-->
		<cover>1</cover>
		<!-- 参数校验格式（正则表达式）-->
		<validate><![CDATA[^[0-9]*$]]></validate>
	</paramconfs>
	<!-- 消息执行任务配置 -->	
	<taskconfs>
		<!-- 任务ID -->
		<taskid>1010</taskid>
		<!-- 响应方（服务的响应方，空时为消息响应方,支持参数宏替换{responder}）-->
		<responder>SN-MQ</responder>
		<!-- 客户端 1:使用客户端 分布式部署时使用  默认为0  -->
		<client>1</client>
		<!-- 服务调用方式  0 服务BEAN 1 HTTP响应  2 WebService调用-->
		<servicetype>0</servicetype>
		<!-- 服务名称 服务名 + . + 方法名 -->
		<serviceid>SN-MQ.MQPackService.packMessage</serviceid>
		<!-- 布局位置  偏离X的方向 一般2为一个间隔 也可不配置 默认顺序设置-->
		<cx>0</cx>
		<!-- 布局位置  偏离Y的方向 一般2为一个间隔 也可不配置 默认顺序设置-->
		<cy>0</cy>
		<!-- 任务说明 -->
		<remark>消息执行任务1（作者）</remark>
		<!-- 任务1010所需参数 -->
		<taskparamconfs>
			<!-- 参数名称 -->
			<paramname>test1</paramname>
			<!-- 参数默认值 -->
			<defaultvalue>packid</defaultvalue>
			<!-- 参数类型  1 发送   2  接收  -->
			<paramtype>1</paramtype>
			<!-- 是否非空  -->
			<notnullflag>1</notnullflag>
			<!-- 参数覆盖  1 覆盖 0 不可覆盖   默认0不可覆盖-->
			<cover>0</cover>
		</taskparamconfs>
		<taskparamconfs>
			<!-- 参数名称 -->
			<paramname>test2</paramname>
			<!-- 参数默认值 -->
			<defaultvalue>packidtest</defaultvalue>
			<!-- 参数类型  1 发送   2  接收  -->
			<paramtype>1</paramtype>
			<!-- 是否非空  -->
			<notnullflag>1</notnullflag>
			<!-- 参数覆盖  1 覆盖 0 不可覆盖   默认0不可覆盖-->
			<cover>1</cover>
		</taskparamconfs>
		<!-- 任务1010执行后续任务配置  也可不配置，默认顺序执行 -->
		<lineconfs>
			<!-- 执行下个任务ID -->
			<taskidto>1020</taskidto>
			<!-- 满足条件时执行下个任务ID（taskidto）  TAC的方式，使用时必须有值  -->
			<executor>
			<![CDATA[
			true
			]]>
			</executor>
		</lineconfs>

	</taskconfs>
	<taskconfs>
		<!-- 任务ID -->
		<taskid>1020</taskid>
		<!-- 响应方（服务的响应方，空时为消息响应方,支持参数宏替换{responder}）-->
		<responder>SN-MQ</responder>
		<!-- 客户端 1:使用客户端 分布式部署时使用  默认为0  -->
		<client>1</client>
		<!-- 服务调用方式  0 服务BEAN 1 HTTP响应  2 WebService调用-->
		<servicetype>0</servicetype>
		<!-- 服务名称 服务名 + . + 方法名 -->		
		<serviceid>SN-MQ.MQPackService.packMessage</serviceid>
		<!-- 布局位置  偏离X的方向 一般2为一个间隔  也可不配置 默认顺序设置 -->
		<cx>2</cx>
		<!-- 布局位置  偏离Y的方向 一般2为一个间隔 也可不配置 默认顺序设置 -->
		<cy>0</cy>
		<!-- 任务说明 -->
		<remark>消息执行任务2（作者）</remark>
		<!-- 任务1020所需参数 -->
		<taskparamconfs>
			<!-- 参数名称 -->
			<paramname>test1</paramname>
			<!-- 参数默认值 -->
			<defaultvalue>packid</defaultvalue>
			<!-- 参数类型  1 发送   2  接收  -->
			<paramtype>1</paramtype>
			<!-- 是否非空  -->
			<notnullflag>1</notnullflag>
			<!-- 参数覆盖  1 覆盖 0 不可覆盖   默认0不可覆盖-->
			<cover>0</cover>
		</taskparamconfs>
		<taskparamconfs>
			<!-- 参数名称 -->
			<paramname>test2</paramname>
			<!-- 参数默认值 -->
			<defaultvalue>packidtest</defaultvalue>
			<!-- 参数类型  1 发送   2  接收  -->
			<paramtype>1</paramtype>
			<!-- 是否非空  -->
			<notnullflag>1</notnullflag>
			<!-- 参数覆盖  1 覆盖 0 不可覆盖   默认0不可覆盖-->
			<cover>1</cover>
		</taskparamconfs>
		<!-- 任务1020执行后续任务配置  也可不配置，默认顺序执行 -->
		<lineconfs>
			<!-- 执行下个任务ID -->
			<taskidto>1030</taskidto>
			<!-- 满足条件时执行下个任务ID（taskidto）  TAC的方式，使用时必须有值  -->
			<executor>
			<![CDATA[
			true
			]]>
			</executor>
		</lineconfs>
	</taskconfs>
	<taskconfs>
		<!-- 任务ID -->
		<taskid>1030</taskid>
		<!-- 响应方（服务的响应方，空时为消息响应方,支持参数宏替换{responder}）-->
		<responder>SN-MQ</responder>
		<!-- 客户端 1:使用客户端 分布式部署时使用  默认为0  -->
		<client>1</client>
		<!-- 服务调用方式  0 服务BEAN 1 HTTP响应  2 WebService调用-->
		<servicetype>0</servicetype>
		<!-- 服务名称 服务名 + . + 方法名 -->		
		<serviceid>SN-MQ.MQPackService.packMessage</serviceid>
		<!-- 布局位置  偏离X的方向 一般2为一个间隔  也可不配置 默认顺序设置 -->
		<cx>2</cx>
		<!-- 布局位置  偏离Y的方向 一般2为一个间隔 也可不配置 默认顺序设置 -->
		<cy>2</cy>
		<!-- 任务说明 -->		
		<remark>消息执行任务3（作者）</remark>
		<!-- 任务1030所需参数 -->
		<taskparamconfs>
			<!-- 参数名称 -->
			<paramname>test1</paramname>
			<!-- 参数默认值 -->
			<defaultvalue>packid</defaultvalue>
			<!-- 参数类型  1 发送   2  接收  -->
			<paramtype>1</paramtype>
			<!-- 是否非空  -->
			<notnullflag>1</notnullflag>
			<!-- 参数覆盖  1 覆盖 0 不可覆盖   默认0不可覆盖-->
			<cover>0</cover>
		</taskparamconfs>
		<taskparamconfs>
			<!-- 参数名称 -->
			<paramname>test2</paramname>
			<!-- 参数默认值 -->
			<defaultvalue>packidtest</defaultvalue>
			<!-- 参数类型  1 发送   2  接收  -->
			<paramtype>1</paramtype>
			<!-- 是否非空  -->
			<notnullflag>1</notnullflag>
			<!-- 参数覆盖  1 覆盖 0 不可覆盖   默认0不可覆盖-->
			<cover>1</cover>
		</taskparamconfs>
	</taskconfs>
</MQMessageConf>
```

#### 订阅配置说明
1. 位置：客户端UI中cfg/mq/registtopic/MQMessageTopic*.xml
1. 用途：订阅消息模型中多个订阅方定义
1. 消息模型请参见对应的Schema文件【[MQMessageTopic.xsd](do/BrowseSchema?schema=MQMessageTopic.xsd)】
```xml
<?xml version="1.0" encoding="UTF-8"?>
<MQMessageTopicList xmlns="http://www.snsoft.com.cn/schema/MQMessageTopic" xsi:schemaLocation="http://www.snsoft.com.cn/schema/MQMessageTopic http://www.snsoft.com.cn/schema/MQMessageTopic.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<MQMessageTopic messagetype="MQ-TEST-0004" requester="SN-MQ" responder="SN-MQ"
		topic="SN-MQ" />
	<MQMessageTopic messagetype="MQ-TEST-0004" requester="SN-MQ" responder="SN-MQ"
		topic="SN-EMAIL" />
</MQMessageTopicList>
```

#### 文件扫描说明
1. 位置：客户端UI中cfg/mq/messagefileres/MQMessageFileRes*.xml
1. 用途：扫描消息模型中**文件资源**的关系定义
1. 请参见对应的Schema文件【[MQMessageFileRes.xsd](do/BrowseSchema?schema=MQMessageFileRes.xsd)】
```xml
<?xml version="1.0" encoding="UTF-8"?>
<MQMessageFileResList xmlns="http://www.snsoft.com.cn/schema/MQMessageFileRes" xsi:schemaLocation="http://www.snsoft.com.cn/schema/MQMessageFileRes http://www.snsoft.com.cn/schema/MQMessageFileRes.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<MQMessageFileRes file_res_id="MQ-TEST-1100"
		messagetype="MQ-TEST-1100" requester="SN-MQ" responder="SN-MQ" />
	<MQMessageFileRes file_res_id="MQ-TEST-1000"
		messagetype="MQ-TEST-1100" requester="SN-MQ" responder="SN-MQ" />
</MQMessageFileResList>
```

#### 文件资源定义
1. 位置：客户端UI中cfg/filesres/FilesResConfs*.xml
1. 用途：文件资源定义
1. 请参见对应的Schema文件【[FilesResConfs.xsd](do/BrowseSchema?schema=FilesResConfs.xsd)】
```xml
<?xml version="1.0" encoding="UTF-8"?>
<FilesResConfs xmlns="http://www.snsoft.com.cn/schema/FilesResConfs" xsi:schemaLocation="http://www.snsoft.com.cn/schema/FilesResConfs http://www.snsoft.com.cn/schema/FilesResConfs.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <configs>
    <id>MQ-TEST-1100</id>
    <usedflag>0</usedflag>
    <name>报文扫描测试B</name>
    <fsmacro>FSID.MQ.TEST</fsmacro><!-- file:D:/snsoft90/data/ftp/-->
    <dirpath>mq-test-1100</dirpath>
    <filesmatch>mq-test-1100%.xml</filesmatch>
    <checkmode></checkmode>
    <xsdpath></xsdpath>
    <encryptmode></encryptmode>
    <compresstmode></compresstmode>
    <filetype>XML</filetype>
    <fileprefix>mq-test-1100-</fileprefix>
    <backupflag>1</backupflag>
    <fsmacro_backup>FSID.MQ.TEST</fsmacro_backup>
    <remark></remark>
  </configs>  
</FilesResConfs>
```

#### 合并配置说明
1. 位置：客户端UI中cfg/mq/pack/MQPackConfs*.xml
1. 用途：处理服务器文件合并、压缩、解压的定义
1. 请参见对应的Schema文件【[MQPackConfs.xsd](do/BrowseSchema?schema=MQPackConfs.xsd)】
```xml
<?xml version="1.0" encoding="UTF-8"?>
<MQPackConfs xmlns="http://www.snsoft.com.cn/schema/MQPackConfs" xsi:schemaLocation="http://www.snsoft.com.cn/schema/MQPackConfs http://www.snsoft.com.cn/schema/MQPackConfs.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <packconfs>
    <usedflag>1</usedflag>
    <packid>packid10</packid>
    <packtype>10</packtype>
    <fsmacrosrc>FS.MQ.TEST</fsmacrosrc>
    <dirpathsrc>mq-test-10</dirpathsrc>
    <fsmacro>FS.MQ.TEST</fsmacro>
    <dirpath>mq-test-1010</dirpath>
    <temppath>mq-test-1010t-temp</temppath>
    <serverid>SN-MQ.CTL-server</serverid>
    <confid>SN-MQ_MQ-PACK-DEFAULT_SN-MQ</confid>
    <remark>remark</remark>
  </packconfs>
</MQPackConfs>
```

#### 发送消息介绍
**创建消息**
``` 
        public UIResponse<Object> createMessage(AccessRunParam runParam)
        {
            AsyncSendMessage sendMessage = new AsyncSendMessage();
            String msgtype = runParam.getMsgtype();
            String requester = "SN-ACS";
            String responder = "SN-MQ";//runParam.getReceiver();
            //msgtype 为空时走默认的消息  建议走此种方式  只有一个消息方便定义
            if (StrUtils.isEmpty(msgtype))
            {
                msgtype = "DEFINVOKE";
            }
            sendMessage.setMessagetype(msgtype);
            sendMessage.setRequester(requester);
            sendMessage.setResponder(responder);
            sendMessage.setIndexinfo(runParam.getId());
            sendMessage.setLockkey(msgtype + "-" + runParam.getId());
            sendMessage.setParameter(AsyncConstants.Async_Params_InnerCode, runParam.getId());
            String receiver = runParam.getReceiver();
            if (StrUtils.isNotEmpty(receiver))
            {
                sendMessage.setParameter(AsyncConstants.Async_Params_Responder, receiver);
            }
            String sender = runParam.getSender();
            if (StrUtils.isNotEmpty(sender))
            {
                sendMessage.setParameter(AsyncConstants.Async_Params_Requester, sender);
            }
            return AsyncUtils.sendMessage(sendMessage);
        }
 ```
        
**激活消息**	
 ```
    	 public UIResponse<Object> createMessage(String messageid)
    	 {
    		return AsyncUtils.actionMessage(messageid);
    	 }
   ```
    	 
**回调消息1**	
 ```
         public UIResponse<Object> freeRefQuota(ParamConversionHandle receiveMessage)
         {
        	return this.quotaService.freeRefQuota(receiveMessage);
         }    
  ```
  	
**回调消息2**需要依赖MQ的API	
 ```
        public MQResult freeRefQuota(MQReceiveMessage receiveMessage)
        {
        	return this.quotaService.freeRefQuota(receiveMessage);
        }
   ```
   
### 设计

#### 主要功能

1. 使用MongoDB做消息持久化，提供更好的性能、更好的容错性、更方便的集群部署。
1. 灵活的队列散列，大幅减低Redis的主线程压力。
1. 初始化不影响系统使用，支持使用的同时初始化操作。
1. 方便的消息备份策略，高效的延时、同步策略。
1. 支持消息顺序执行、优先、限流、分组、缓冲、降级、暂停、停止、订阅、等待、延迟等功能。
1. 支持消息服务执行编排，解决异构事务的问题。
1. 支持报文的扫描、打包、缓冲等数据交互功能。
1. 提供熔断机制，防止出现雪崩效应。

#### 应用场景

1. 异步处理
1. 异构事务
1. 订阅下发
1. 缓冲层
1. 服务编排
1. 延迟任务
1. 等待激活

#### 部署结构
- 分布式部署
![](help/SN-MQ/MQ/img/部署结构1.png)
- 简化集群部署
![](help/SN-MQ/MQ/img/部署结构2.png)
#### 时序图

![](help/SN-MQ/MQ/img/消息执行.png)

@startuml
Title MQ时序图

actor 客户端

participant client   as "MQClientService"
participant send    as "MQSendService"
participant service     as "MQMessageService"
participant timer as "MQMessagePushWorker"
participant queue as "MQQueueService"
participant dao as "MQMessageDao"

客户端 -> client : [sendMessage]\n发送消息
activate client
	client -> send : [sendMessage]\n发送消息
	activate send
		send -> service : [checkSendMessage]\n检查消息
		send -> service : [createMessage]\n创建消息
		activate service
		service -> dao :[insertMessage]\n消息存盘
		service -> service :[offerMessage]\n进入队列
		service -> queue :[offerQueueCore]\n进入队列
		deactivate service
	deactivate send
	timer -> service :run
		activate timer		
		activate service
		service <-> queue :[pollMessage]\n消息出栈
		service -> service :[pushMessage]\n推送消息
		service -> client :[remoteInvoke]\n响应方client
		deactivate service
		deactivate timer
client -> 客户端 :内部方法
deactivate client
@enduml

#### 基本架构
![](help/SN-MQ/MQ/img/消息中间件1.png)

![](help/SN-MQ/MQ/img/消息中间件部署图.png)

#### 原理简介
![](help/SN-MQ/MQ/img/消息中间件说明图.png)![](help/SN-MQ/MQ/img/消息中间件推送原理.png)
![](help/SN-MQ/MQ/img/消息中心核心流程.png)

##### 消息客户端发送

应用系统创建“客户端消息”， 调用“消息客户”端提供的“消息发送服务”发送消息到客户端。
“消息服务”收到“客户端发送消息”后，根据发送方、消息类型、接收方匹配消息模型，创建消息，生成消息ID。
匹配消息模型时，字段消息类型、发送方非空，接收方可为空。先按照消息类型、接收方、发送方匹配，如果接收方不为空则再按照消息类型、接收方两个匹配，最后按照消息类型单独匹配。

##### 消息队列的类型

1. 主干队列Core（QUEUE）：通过分队列数参数配置构建多个队列，分配较多资源的消息队列。
1. 降级队列Lesser（QUEUE）：通过分队列数参数配置构建多个队列，分配较少资源的消息队列。
1. 暂停队列Stop（QUEUE）：通过消息类型+发送方+接收方维度构建对应的队列，暂停执行的消息队列，恢复后进入正常队列。
1. 完结队列Comp（QUEUE）：通过分队列数参数配置构建多个队列，配合定时任务完成备份策略。
1. 缓冲队列Buff（QUEUE）：通过消息类型+发送方+接收方维度构建对应的队列，缓冲消息进入缓冲队列，等待合并创建消息，单独定时任务配合处理。
1. 延时队列Delay（QUEUE）：通过时间维度构建对应的队列，延时处理的消息暂时存放的队列，根据延时时间放置到多个队列中，定时任务配合处理。
1. 失败集合（SET）：通过消息类型+发送方+接收方维度构建对应的SET，处理失败的消息，用于展示及处理，调用的服务产生异常。
1. 异常集合（SET）：通过消息类型+发送方+接收方维度构建对应的SET，处理异常的消息，用于展示及处理，MQ产生异常。
1. 超次集合（SET）：通过消息类型+发送方+接收方维度构建对应的SET，处理超次的消息，用于展示及处理，多次网络产生异常。
1. 无效集合（SET）：通过消息类型+发送方+接收方维度构建对应的SET，处理无效的消息，用于展示及处理，第三方产生异常。

##### 延时消息的处理

判断异常为延时异常时，根据已执行次数和最小时间间隔计算延时队列KEY并放入对应的延时队列。
增加单线程服务根据Redis中记录的延时队列key的列表和当前服务器的时间处理延时队列中的消息到正常队列中。
延次的消息多次执行仍然未成功则进入超次状态停止执行。

##### 主干、降级队列获取消息

根据提前定义的线程数及分队列数组织多线程轮询获取“队列消息”，起始点可随机生成，一轮查找都没有处理到消息时可释放线程。
根据消息ID获取缓存的“消息”，根据“消息”执行的情况构建“客户端接收消息”，最后调用客户端，得到执行结果后保存消息，处理成功是如果需回调则反调发送方。
如果通过限流器ThresholdCaches抢锁成功则正常处理，处理失败时分组队列挂起，此时要特别注意及时解决失败的消息。
判断为订阅消息则从Zookeeper中获取订阅此消息的系统列表，创建一条消息，执行任务时再分别创建新的消息并执行，订阅产生的消息之间相互独立。

##### 客户端的消息执行

收到“客户端接收消息”，消息处理分为几种情况：
1. 调用应用系统中的MQ客户端，执行应用系统发布的服务。
1. 通过Http或WebService方式调用。

分布式服务部署下判断去重条件要根据消息ID和去重表（用Redis缓存实现判断）防止消息重复执行。
业务逻辑处理需保持幂等性，执行后返回消息执行结果。

##### 消息锁定KEY的使用
报文扫描创建消息前要先抢锁，抢锁成功则创建消息，消息成功后延时解锁，初始化时需要加锁。
同步消息的顺序执行操作前要根据分组ID抢锁，保证单线程处理顺序消息。

##### 消息初始化的处理

系统第一次使用时，选择当前时间直接进行初始化操作即可。未初始化情况下新加入的消息除了同步消息外并不影响执行，同步消息因为需要顺序执行所以必须进行初始化。由于存放初始化标记的Redis服务器重启导致的初始化标记丢失则要小心处理，此时不但影响同步消息还会影响历史消息，此时需要分析一个正确的时间点，即重启服务器前的时间点进行初始化，这样保证历史的消息也能够重新进入队列。特别注意如果时间点不正确则可能导致消息重复放入队列或消息未正确放入队列。

##### 消息持久化
消息持久化支持MongoDB的存储，消息执行完结后进入完结队列并通过定时任务多线程处理。根据消息保存方式，0 执行后  1 分步骤  2 不保存，确定消息的保存时点。建议选择 1，虽然存储次数比较多但是消息的存储更安全一些，主要是尽量避免服务器非正常重启引起的消息执行错误。

##### 暂停算法

- 系统选项“SN-MQ.Comm.Fuse.Count”大于0时启动“断路器”，定义消息执行连续多少次未成功则断路器生效（熔断），效果为进入暂停队列。
- 消息进入执行队列前需判断是否已暂停，如果已暂停则进入暂停队列。暂停的判断根据Redis中的暂停标记。消息监控定时任务根据“日处理阀值”自动暂停处理。

##### 插队算法

- 消息模型中定义“优先”则放入队列最前面执行。
- 消息计算运行时长（执行完成时间-创建时间），超过“时效阀值”则自动设置“插队”标记，放入队列最前面执行。

##### 去重
服务端去重：根据消息 lockkey(锁定KEY)到Redis中判断去重。
客户端去重：根据消息ID到Redis中判断去重。

#### MQService服务
##### Service服务接口
```
服务调用方法必须满足：一个参数：MQReceiveMessage receiveMessage，返回值：MQResult
例如：public MQResult receiveMessage(MQReceiveMessage receiveMessage)
```

##### Service相关程序
```
消息推送定时任务：SN-MQ.MQPushTimer对应类snsoft.mq.api.message.service.MQPushTimer
完结消息推送类：snsoft.mq.message.domain.MQMessageCompWorker
主干消息推送类：snsoft.mq.message.domain.MQMessageCoreWorker
延迟消息推送类：snsoft.mq.message.domain.MQMessageDelayWorker
降级消息推送类：snsoft.mq.message.domain.MQMessageLesserWorker
消息处理服务类：snsoft.mq.message.service.impl.MQMessageSeviceImpl
消息处理配置类：snsoft.mq.message.service.impl.MQMessageConfServiceImpl
```
##### Service类图

@startuml
Title MQService类图
interface MQPushTimer
interface MQMessageService
interface MQMessageConfService
abstract class MQMessagePushWorker
abstract class MQMessageWorker

MQMessageService <|.. MQMessageSeviceImpl
MQMessageConfService <|.. MQMessageConfServiceImpl

MQMessageWorker <|-- MQMessagePushWorker
MQMessageWorker <|-- MQMessageCompWorker
MQMessagePushWorker <|-- MQMessageCoreWorker
MQMessageWorker <|-- MQMessageDelayWorker
MQMessagePushWorker <|-- MQMessageLesserWorker
MQMessageService <|-- MQMessageConfService
MQMessagePushWorker <|-- MQMessageService
class MQMessageCompWorker {
}
class MQMessageCoreWorker {
}
class MQMessageDelayWorker {
}
class MQMessageLesserWorker {
}
class MQMessageSeviceImpl {
+ MQMessage createMessage(AsyncSendMessage sendMessage, MQMessageConf messageConf);
+ void offerMessage(MQMessage message, MQMessageConf messageConf);
+ void pushMessage(MQMessage message);
+ boolean remoteInvoke(MQMessage message, MQMessageConf messageConf, MQMessageTask task, MQMessageTaskConf taskConf, ScriptExecutor executor);
}
class MQMessageConfServiceImpl {
}
@enduml

#### 相关程序
```
出入库任务处理类：snsoft.mq.client.service.impl.MQReceiveServiceImpl调用方法receiveMessage
客户端配置获取服务：SN-MQ.MQClientConfigService，对应实现类：snsoft.mq.client.service.impl.MQClientConfigServiceImpl
客户端发送消息服务：SN-MQ.MQClientService，对应实现类：snsoft.mq.client.service.impl.MQClientServiceImpl
```
