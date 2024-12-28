## UMC用戶消息

|日期|作者|备注|
|------|------|------|
|2018-10-16|陈泰林|初稿|
|2019-10-12|陈泰林|修改|
|2020-07-03|陈泰林|修改|

### 指南

#### 概念说明

用户消息主要是针对**当前系统用户**发送消息，发送用户消息时可指定多用户、多人员、多岗位、多部门，可以容忍消息丢失；
用户消息类型目前支持站内信、手机短信、邮件、三方APP(微信)、极光推送等5种发送类型；

#### 使用说明

**配置说明**
&#35;SN-UMC服务端=================
SN-UMC.Comm.mongo=true
SN-UMC.Comm.mongo.uri=mongodb://127.0.0.1:27017/umc
SN-UMC.Comm.redis=true
SN-UMC.Comm.redis.host=127.0.0.1
SN-UMC.Comm.redis.port=6379
&#35;SN-UMC.Push.Messageflag：推送队列消息执行是否存储，1：只存储成功的消息 2：只存储失败的消息 3：存储成功和失败的消息 0：不存储，默认 3
&#35;SN-UMC.Push.Messageflag=3
&#35;SN-UMC.Push.MessageStop：站内信支持在线停止发送消息；如果是测试环境下要进行压力测试，需要停止发送消息时，值设置为true
&#35;SN-UMC.Push.MessageStop=false

&#35;启用WEBSOCKET(SSE)时，需配置消息中间件
支持1：
&#35;kafka============
&#35;发送方kafka
&#35;snsoft.kafka=true
&#35;snsoft.kafka.host=127.0.0.1:9092
支持2：
&#35;redis============
&#35;发送方redis(默认)
&#35;snsoft.redis=true
&#35;snsoft.redis.host=127.0.0.1
&#35;snsoft.redis.port=6379
支持3：
&#35;jms============
&#35;发送方jms
&#35;snsoft.jms=true
&#35;snsoft.jms.brokerURL=nio://127.0.0.1:61616

&#35;启用时效监控时，必须配置日志KAFKA
&#35;snsoft.kafka.log=true
&#35;snsoft.kafka.log.host=127.0.0.1:9092

**定时任务**

1. 参考文件：TimerTaskSN-UMC.xml
1. UMC的定时任务底层提供的只是一个“配置模板”是禁用状态，实际使用需要自己同步到数据库中并且指定执行的服务器
1. SN-UMC-用户消息推送：消息核心定时任务，内部处理第一队列和第二队列的消息，支持集群使用

**消息客户端**

非必须使用，一般使用单据时，建议使用客户端
涉及功能：单据消息（必须）、模板TAC（使用消息客户端时在单据客户端上执行模板TAC，否则本地执行模板TAC）、仿真格式（使用消息客户端时在单据客户端上执行打印，否则本地执行打印）

**注意事项**
特别重要：UMC部署后，在运行期间，为了换包时保障数据不丢失，要求换包时必须通过UMC服务器管理暂停UMC服务器，并且等待服务器线程数到达为零（实际情况而定，知道没有任务在执行时即可换包），即可换包。特别说明：如不考虑消息丢失情况可忽略
UMC运行期间如想停止UMC发送消息，需要将UMC服务器暂停运行，然后将UMC定时任务停止，前提必须设置了消息超时时间，超过时间后消息超时不发送消息
UMC运行期间如想停止UMC发送消息，可以将系统选项SN-UMC.Push.MessageStop=true，消息将不再发送
UMC服务日志级别必须是info，可查看发送消息日志
模板TAC处理发送消息，如果定义了客户端，在客户端上执行模板TAC，否则本地执行模板TAC
接收对象功能，发送消息时都会额外发送这些对象
发送系统分类匹配消息（queryMessageConfigsByFitSheet），通过系统分类匹配（单据、部门、公司，如果传递也跟着匹配），发送匹配的消息；如果此时模板配置了模板TAC，走【模板TAC处理发送消息】，否则直接发送消息
定时频率功能，支持集群部署，当满足频率时，走【模板TAC处理发送消息】，同时记录定时频率日志（定义多个频率时，如果都满的话会都发送）

### 手册

**注意：消息模板配置**和**消息系统配置**配置上没有关联，发送消息时才发生关联，原则是发送同一类型的消息系统配置可动态改变。

#### 消息模板配置
1. 位置：客户端UI中cfg/umc/message/UMCMessageTypeConf*.xml
1. 参考：UMCMessageTypeConfSN-UMC.xml
1. UMC提供默认参数：
```
   fromusercode：发送用户码
   tousercode  ：接受用户码
   fromusername：发送用户名
   fromloginname：发送用户名 登录名
   tousername  ：接受用户名
   tologinname ：接受用户名  登录名
   frommail    ：发送邮件地址
   tomail      ：接受邮件地址
   frommobile  ：发送手机号
   tomobile    ：接受手机号
   date        ：当前日期 yyyy-MM-dd
   datetime    ：当前是时间 yyyy-MM-dd HH:mi:ss
```
1. 参数宏格式：{param}
1. 编码规则：S.系统号.3位流水号
1. 短信文本格式：
```
   阿里.云通讯格式定义：签名【阿里大于测试】->模板【SMS_70540165】->参数及文本【${customer:{authcode}}】
   容联.云通讯格式定义：短信平台模板号->${参数值1}${参数值2}...
   腾讯.云通讯格式定义：深圳中电->366921->${45678}为您的登录验证码，请于${3}分钟内填写。如非本人操作，请忽略本短信。
   一信通格式定义：您的验证码为：123456
 ```
1. 用户消息类型配置请参见对应的Schema文件【[UMCMessageTypeConf.xsd](do/BrowseSchema?schema=UMCMessageTypeConf.xsd)】
```xml
<?xml version="1.0" encoding="UTF-8"?>
<UMCMessageTypeConfList xmlns="http://www.snsoft.com.cn/schema/UMCMessageTypeConf" 
    xsi:schemaLocation="http://www.snsoft.com.cn/schema/UMCMessageTypeConf http://www.snsoft.com.cn/schema/UMCMessageTypeConf.xsd"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<!-- 
	 message_send_flag=1:站内信2:邮件4:短信8:微信16:QQ32:极光  
	 message_type_id=消息模板ID:规则 S.系统.3位自增
	 message_type_name=消息模板名称
	 message_type_mode=10:即时20:定时
	 mstgroupid=消息类别，标志消息模板归属
	 status=10:草拟70:启用90:停用
	 message_sys_id=系统分类，标志消息模板具体系统类型
	-->
	<UMCMessageTypeConf message_send_flag="63" message_type_id="S.SN-UMC.001" message_type_name="用户消息标准模板" message_type_mode="10"  mstgroupid="S.SN-UMC.001" message_sys_id="SN-UMC01" status="90">
		<!-- 【模板TAC】 -->
		<messagetac><![CDATA[
setSendMsg(false)
def = getMessageconf()           
recinfolist = newArrayList()
recinfo = createReceiveInfo(record.getCuicode(),"U","C00000000200002")
recinfolist.add(recinfo)
msg = createMsgParam(recinfolist,null)
addMsg(msg)
		]]></messagetac>
		<!-- 客户端 【模板TAC】、【仿真打印】 在执行客户端上运行，不设置时在本服务内执行 -->
		<serverid>SN-UMC</serverid>
		<!-- 站内信消息模板 -->
		<UMCMessageTypeMode>
			<!-- 消息模板标记位 1:启用FreeMark语法解析  2:使用SSE传输消息 -->
			<messageflag>0</messageflag>
			<!-- 消息模板类型 10:站内信模板 20:邮件模板 30:短信模板 40:微信模板 50:QQ模板 60:极光模板 -->
			<messagetype>10</messagetype>
			<!-- 消息模板标题 -->
			<messagetitle>消息模板标题</messagetitle>
			<!-- 消息模板内容 -->
			<messagetext><![CDATA[
			{sheetname}[{outercode}]待审核。
			]]></messagetext>
			<!-- 消息模板WS提醒类型 -->
			<messagewstype>SN-CMC.WarnTip</messagewstype>
		</UMCMessageTypeMode>
		<!-- 邮件消息模板 -->
		<UMCMessageTypeMode>
			<!-- 消息模板标记位 1:启用FreeMark语法解析 -->
			<messageflag>0</messageflag>
			<!-- 消息模板类型 10:站内信模板 20:邮件模板 30:短信模板 40:微信模板 50:QQ模板 60:极光模板 -->
			<messagetype>20</messagetype>
			<!-- 消息模板标题 -->
			<messagetitle>消息模板标题</messagetitle>
			<!-- 消息模板内容 -->
			<messagetext><![CDATA[
			{sheetname}[{outercode}]待审核。
			]]></messagetext>
			<!-- 消息【仿真打印】ID -->
			<messagemaildefid>xxxxxx</messagemaildefid>
		</UMCMessageTypeMode>
		<!-- 短信消息模板 -->
		<UMCMessageTypeMode>
			<!-- 消息模板标记位 1:启用FreeMark语法解析 -->
			<messageflag>0</messageflag>
			<!-- 消息模板类型 10:站内信模板 20:邮件模板 30:短信模板 40:微信模板 50:QQ模板 60:极光模板 -->
			<messagetype>30</messagetype>
			<!-- 消息模板标题 -->
			<messagetitle>消息模板标题</messagetitle>
			<!-- 消息模板内容 -->
			<messagetext>166333->${参数值1}${参数值2}  : 【商务部外贸云服务】验证码：{1}，有效时间{2}分钟。请勿给他人使用！</messagetext>
		</UMCMessageTypeMode>
		<!-- 微信消息模板 -->
		<UMCMessageTypeMode>
			<!-- 消息模板标记位 1:启用FreeMark语法解析 -->
			<messageflag>0</messageflag>
			<!-- 消息模板类型 10:站内信模板 20:邮件模板 30:短信模板 40:微信模板 50:QQ模板 60:极光模板 -->
			<messagetype>40</messagetype>
			<!-- 消息模板标题 -->
			<messagetitle>消息模板标题</messagetitle>
			<!-- 消息模板内容 -->
			<messagetext><![CDATA[
			{sheetname}[{outercode}]待审核。
			]]></messagetext>
		</UMCMessageTypeMode>
		<!-- QQ消息模板 -->
		<UMCMessageTypeMode>
			<!-- 消息模板标记位 1:启用FreeMark语法解析 -->
			<messageflag>0</messageflag>
			<!-- 消息模板类型 10:站内信模板 20:邮件模板 30:短信模板 40:微信模板 50:QQ模板 60:极光模板 -->
			<messagetype>50</messagetype>
			<!-- 消息模板标题 -->
			<messagetitle>消息模板标题</messagetitle>
			<!-- 消息模板内容 -->
			<messagetext>{sheetname}[{outercode}]待审核。</messagetext>
		</UMCMessageTypeMode>
		<!-- 极光消息模板 -->
		<UMCMessageTypeMode>
			<!-- 消息模板标记位 1:启用FreeMark语法解析 -->
			<messageflag>0</messageflag>
			<!-- 消息模板类型 10:站内信模板 20:邮件模板 30:短信模板 40:微信模板 50:QQ模板 60:极光模板 -->
			<messagetype>60</messagetype>
			<!-- 消息模板内容 -->
			<messagetext>{sheetname}[{outercode}]待审核。</messagetext>
			<!-- 消息模板URL地址 -->
			<jpushurl>xxxxxx</jpushurl>
			<!-- 消息模板页面类型 -->
			<jpushpagetype>xxxxxx</jpushpagetype>
		</UMCMessageTypeMode>
		<!-- 适用范围 -->
		<UMCMessageTypeFitSheet>
			<!-- 所有单据 -->
			<fitall>1</fitall>
			<!-- 适用单据 -->
			<sheetcode>%</sheetcode>
			<!-- 适用部门 -->
			<bcode>xxxxxx</bcode>
			<!-- 适用公司 -->
			<corpbcode>xxxxxx</corpbcode>
			<!-- 适用单据 -->
			<fittype>10</fittype>
		</UMCMessageTypeFitSheet>
		<UMCMessageTypeFitSheet>
			<!-- 所有单据 -->
			<fitall>1</fitall>
			<!-- 排除单据 -->
			<sheetcode>%</sheetcode>
			<!-- 排除部门 -->
			<bcode>xxxxxx</bcode>
			<!-- 排除公司 -->
			<corpbcode>xxxxxx</corpbcode>
			<!-- 排除单据 -->
			<fittype>20</fittype>
		</UMCMessageTypeFitSheet>
		<!-- 定时频率 -->
		<UMCMessageTypeFre>
			<!-- 发送频率 每年 -->
			<sendfre>10</sendfre>
			<!-- 月份 -->
			<fremonths>1,3,5</fremonths>
			<!-- 日期 -->
			<fredays>1,2,3</fredays>
			<!-- 小时-->
			<frehour>1</frehour>
			<!-- 分钟-->
			<freminute>1</freminute>
		</UMCMessageTypeFre>
		<UMCMessageTypeFre>
			<!-- 发送频率 每月 -->
			<sendfre>20</sendfre>
			<!-- 日期 -->
			<fredays>1,2,3</fredays>
			<!-- 小时-->
			<frehour>1</frehour>
			<!-- 分钟-->
			<freminute>1</freminute>
		</UMCMessageTypeFre>
		<UMCMessageTypeFre>
			<!-- 发送频率 每周 -->
			<sendfre>30</sendfre>
			<!-- 星期 -->
			<freweeks>1,3,5</freweeks>
			<!-- 小时-->
			<frehour>1</frehour>
			<!-- 分钟-->
			<freminute>1</freminute>
		</UMCMessageTypeFre>
		<UMCMessageTypeFre>
			<!-- 发送频率 每天 -->
			<sendfre>40</sendfre>
			<!-- 小时-->
			<frehour>1</frehour>
			<!-- 分钟-->
			<freminute>1</freminute>
		</UMCMessageTypeFre>
		<UMCMessageTypeFre>
			<!-- 发送频率 指定时间 -->
			<sendfre>50</sendfre>
			<!-- 指定时间-->
			<fretime>2020-06-29 11:50:00</fretime>
		</UMCMessageTypeFre>
	</UMCMessageTypeConf>
</UMCMessageTypeConfList>
```

#### 消息模板分类
1. 位置：客户端UI中cfg/umc/messagegroup/UMCMessageTypeGroupConf*.xml
1. 参考：UMCMessageTypeGroupConfSN-UMC.xml
1. 编码规则：S.系统号.3位流水号
1. 消息模板分类请参见对应的Schema文件【[UMCMessageTypeGroupConf.xsd](do/BrowseSchema?schema=UMCMessageTypeGroupConf.xsd)】
```xml
<?xml version="1.0" encoding="UTF-8"?>
<UMCMessageTypeConfGroupList xmlns="http://www.snsoft.com.cn/schema/UMCMessageTypeConfGroupList"
    xsi:schemaLocation="http://www.snsoft.com.cn/schema/UMCMessageTypeConfGroupList http://www.snsoft.com.cn/schema/UMCMessageTypeGroupConf.xsd" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <UMCMessageTypeGroupConf idx="10" mstgroupid="S.SN-UMC.001" mstgroupname="用户消息" status="70">
    <remark>用户消息默认分组</remark>
  </UMCMessageTypeGroupConf>
</UMCMessageTypeConfGroupList>
```

#### 消息系统分类
1. 位置：客户端UI中cfg/umc/messagesys/UMCMessageTypeSysConf*.xml
1. 参考：UMCMessageTypeSysConfSN-UMC.xml
1. 消息系统分类请参见对应的Schema文件【[UMCMessageSysConfig.xsd](do/BrowseSchema?schema=UMCMessageSysConfig.xsd)】
```xml
<?xml version="1.0" encoding="UTF-8"?>
<UMCMessageSysConfigList xmlns="http://www.snsoft.com.cn/schema/UMCMessageSysConfig" xsi:schemaLocation="http://www.snsoft.com.cn/schema/UMCMessageSysConfig http://www.snsoft.com.cn/schema/UMCMessageSysConfig.xsd"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"> 
  <UMCMessageSysConfig messagesysid="SN-UMC01" messagesysname="用户消息">
    <remark>用户消息</remark>
  </UMCMessageSysConfig>
</UMCMessageSysConfigList>
```

#### 消息系统配置
1. 位置：客户端UI中cfg/umc/sys/UMCSysConf*.xml
1. 参考：UMCSysConfSN-UMC.xml
1. 用户消息系统配置请参见对应的Schema文件【[UMCSysConfig.xsd](do/BrowseSchema?schema=UMCSysConfig.xsd)】
```xml
<?xml version="1.0" encoding="UTF-8"?>
<UMCSysConfigList xmlns="http://www.snsoft.com.cn/schema/UMCSysConfig" xsi:schemaLocation="http://www.snsoft.com.cn/schema/UMCSysConfig http://www.snsoft.com.cn/schema/UMCSysConfig.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<UMCSysConfig sysid="SN-UMC" sysname="用户消息">
	    <UMCMAILSysConfig mailflag="0">
          <smtpuser>chentailin@snsoft.com.cn</smtpuser>
          <smtppass>XXXXXX</smtppass>
          <smtphost>smtp.263.net</smtphost>
          <smtpport>25</smtpport>
          <smtpssl>0</smtpssl>
          <smtptls>0</smtptls>
          <smtphtml>0</smtphtml>
        </UMCMAILSysConfig>
        <UMCMOBILESysConfig mobiletype="0">
          <host>app.cloopen.com</host>
          <port>8883</port>
          <apikey>XXXXXX</apikey>
          <apiauthtoken>XXXXXX</apiauthtoken>
          <appid>XXXXXX</appid>
        </UMCMOBILESysConfig>
		<UMCWEBSITESysConfig websiteflag="1">
			<usercode>系统</usercode>
		</UMCWEBSITESysConfig>
		<!-- 极光推送供应商配置 -->
		<UMCJPushSysConfig appkey="极光推送appkey" mastersecret="极光推送mastersecret" EnvFlags="1"/> <!-- EnvFlags#2 生产环境  ios使用 -->
	</UMCSysConfig>
</UMCSysConfigList>
```

**供应商配置**：Spring-Configs.xml
```xml
<!--UMC极光供应商配置服务 UMC -->
<sn:option name="SN-UMC.Jpush.Appkey">xxxxxx</sn:option>
<sn:option name="SN-UMC.Jpush.Mastersecret">xxxxxx</sn:option>
<sn:option name="SN-UMC.Jpush.Envflags">2</sn:option>
<!--UMC短信供应商配置服务 UMC -->
<sn:option name="SN-UMC.Mobile.MobileSenderService">snsoft.sms.Service</sn:option>
<!--UMC邮件供应商配置服务 UMC -->
<sn:option name="SN-UMC.Mail.MailSenderService">snsoft.mail.Sender</sn:option>
<!--短信供应商服务 UMC-->
<bean id="snsoft.sms.Service" class="snsoft.bas.mobile.service.impl.MobileServiceCCPImpl">
	<property name="host" value="app.cloopen.com" />
	<property name="port" value="8883" />
	<property name="apikey" value="XXXXXXXXXXXXXXXXXXXXXXXXXX" />
	<property name="apiauthtoken" value="XXXXXXXXXXXXXXXXXXXXXXXXXX" />
	<property name="appid" value="XXXXXXXXXXXXXXXXXXXXXXXXXX" />
</bean>
<!--邮件供应商服务 UMC-->
<bean id="snsoft.mail.Sender" class="snsoft.bas.mail.impl.DefaultMailSenderService">
	<property name="server" value="smtp.263xmail.com"/>
	<property name="user" value="xxxxxx@snsoft.com.cn"/>
	<property name="password" value="xxxxxx"/>
	<property name="fromAddress" value="系统管理员&lt;xxxxxx@snsoft.com.cn&gt;"/>
</bean>
```

**短信配置**：荣联(云通讯)
```
<bean id="component-SMSServiceCCP" class="snsoft.bas.mobile.service.impl.MobileServiceCCPImpl">
		<property name="host" value="app.cloopen.com" />
		<property name="port" value="8883" />
		<property name="apikey" value="XXXXXXXXXXXXXXXXXXXXXXXXXX" />
		<property name="apiauthtoken" value="XXXXXXXXXXXXXXXXXXXXXXXXXX" />
		<property name="appid" value="XXXXXXXXXXXXXXXXXXXXXXXXXX" />
</bean>
```
**短信配置**：美联软通
```
 <bean id="component-SMSServiceMLT" class="snsoft.bas.mobile.service.impl.MobileServiceMLTImpl">
     <property name="user" value="user" />
     <property name="password" value="1111" />
     <property name="apikey" value="aaaaaaaaaaaaaaaaaaaa" />
 </bean>
 ```
**短信配置**：Aliyun
```
 <bean id="component-SMSServiceAliyun" class="snsoft.bas.mobile.service.impl.MobileServiceAliyunImpl">
     <property name="accessKeyId" value="accessKeyId" />
     <property name="accessKeySecret" value="aaaaaaaaaaaaaaaaaaaa" />
  </bean>   
  ```
**短信配置**：亿美软通
```
<bean id="component-SMSServiceYMT" class="snsoft.bas.mobile.service.impl.MobileServiceYMTImpl">
		<property name="serviceurl" value="http://sdk999ws.eucp.b2m.cn:8080/sdk/SDKService" />
		<property name="serialno" value="xxxx" />
		<property name="key"  value="xxx" />
		<property name="addSerial"  value="xxxx" />
		<property name="password"  value="xxxx" />
		<property name="charset" value="GBK" />
		<!--15:GBK;8:utf-8;0:ISO-8859-1  据了解,国产/山寨机,经常仅支持GBK和ISO-8859-1-->
</bean>
```
**短信配置**：一信通
```
<bean id="component-MobileServiceUms" class="snsoft.bas.mobile.service.impl.MobileServiceUmsImpl">
     <property name="spCode" value="${spCode}" />
     <property name="loginName" value="loginName" />
     <property name="password" value="1111" />
     <property name="serialNumber" value="serialNumber" />
 </bean>
 ```
**短信配置**：腾讯云
```
<bean id="component-MobileServiceQCloud" class="snsoft.bas.mobile.service.impl.MobileServiceQCloudImpl">
 <property name="appid" value="${appid}" />
 <property name="appkey" value="${appkey}" />
</bean>
```

**微信配置**：定义公共号
WeiXin.DefaultAppID="wxd2d0b0eeadd4ec88"
WeiXin.SECRET.wxd2d0b0eeadd4ec88="97234b5a16e4c56abeee5c4434184a5b"
**微信配置**：关注公共号
usersa表中绑定微信关联数据，一般微信关注时调用本系统自动绑定
**微信配置**：【[微信公众平台测试](https://mp.weixin.qq.com/debug/cgi-bin/sandboxinfo?action=showinfo&t=sandbox/index)】
【[微信公众号官网](https://mp.weixin.qq.com)】
【[微信开发文档](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/Wechat_Open_Tag.html)】


#### 极光专属配置

**极光用户配置**【[极光官网](https://www.jiguang.cn/accounts/register)】
极光推送常见问题[极光推送常见问题](https://community.jiguang.cn/article/51450)
由于此配置一般各个环境不同，且与用户相关，所以此配置不存在文件配置
此界面为不可修改，仅仅可用接口调用进行用户信息存盘
bean = getBean("SN-UMC.UMCJPushUserConfigService")
user = new snsoft.umc.api.jpush.entry.UMCJPushUserConfig()
//savemode必须设置 用于检测是save insert update使用  暂时不支持 update2（修改内码）和delete
user.setSaveMode(2)
bean.saveUser(user)
关键逻辑
<span style="color:red">
UMCJPushUserConfig.saveMode 存盘类型 需求讨论结果 暂时不支持delete和update2
1、saveMode==update
需要内码字段和storedColumns配置，如果更新字段中包含用户编码字段，则要求用户编码非空，即不能将用户编码置空
验证源数据必须存在
2、saveMode=insert
插入情况下必须如果设置更新字段则内码和用户编码必须为更新字段，如果不设置字段，则取当前vo中所有非空字段为更新字段
并且需要验证源数据不能存在1、根据内码查询，2、用户编码+消息类型+系统编号验证数据不存在
3、saveMode=save
检查如果内码为空，则认为是insert，并且按照insert的校验逻辑进行校验
</span>

**极光页面配置**
此界面为了编辑页面，理论上为一个系统ID+一个页面类型对应一个页面URL
页面类型需要与消息类型配置的页面类型对应，且系统ID需要与消息供应商的极光配置对应

#### 发送消息API

**sendUsercodeFitParamMessage**
```java
Map<String,Object> data = new HashMap<>(VOUtils.toMap(warnContent));
List<String> sendCodes = userCodes.stream().distinct().collect(Collectors.toList());
UMCUsercodeFitParamMessage paramMessage = new UMCUsercodeFitParamMessage();
//消息系统ID
paramMessage.setMessagesysid(messagesysid);
//间隔时间（秒）中允许发送最大次数
paramMessage.setMaxtimes(CCUMCConstant.UMC_SENDMAXTIEMS);
//间隔时间（秒）
paramMessage.setSeconds(CCUMCConstant.UMC_SENDSECONDS);
//发送的用户列表
paramMessage.addReceiveInfo(null,null,sendCodes.toArray()); 
//参数宏
paramMessage.setSysmacro(data);
//消息发送超时5分钟后认为超时不发送
data.put(UMCConstant.INNER_MESSAGE_TIMEOUT_SECOND, 5 * 60);
//启用闹钟，闹钟开始时间
paramMessage.setDelayStartTime(snsoft.commons.util.DateUtils.textToDate("2020-5-26 19:30"))
//闹钟运行总次数
paramMessage.setDelayRunFre(5)
//闹钟运行时间间隔 单位分钟
paramMessage.setDelayGap(1)
//发送消息
boolean sendResut = messageSevice.sendUsercodeFitParamMessage(paramMessage);
```

**sendUsercodeParamMessage**
```java
Map<String,Object> data = new HashMap<>(VOUtils.toMap(warnContent));
List<String> sendCodes = userCodes.stream().distinct().collect(Collectors.toList());
UMCUsercodeParamMessage paramMessage = new UMCUsercodeParamMessage();
//消息类型ID
paramMessage.setMessage_type_id(message_type_id);
//供应商配置ID
paramMessage.setSysid(CCUMCConstant.UMC_Supplier);
//间隔时间（秒）中允许发送最大次数
paramMessage.setMaxtimes(CCUMCConstant.UMC_SENDMAXTIEMS);
//间隔时间（秒）
paramMessage.setSeconds(CCUMCConstant.UMC_SENDSECONDS);
//发送的用户列表
paramMessage.addReceiveInfo(null,null,sendCodes.toArray()); 
//参数宏
paramMessage.setSysmacro(data);
//消息发送超时5分钟后认为超时不发送
data.put(UMCConstant.INNER_MESSAGE_TIMEOUT_SECOND, 5 * 60);
//启用闹钟，闹钟开始时间
paramMessage.setDelayStartTime(snsoft.commons.util.DateUtils.textToDate("2020-5-26 19:30"))
//闹钟运行总次数
paramMessage.setDelayRunFre(5)
//闹钟运行时间间隔 单位分钟
paramMessage.setDelayGap(1)
//发送消息
boolean sendResut = messageSevice.sendUsercodeParamMessage(paramMessage);
```

**sendUsercodeMessage**
```java
UMCUsercodeMessage usercodeMessage = new UMCUsercodeMessage();
//供应商配置ID
usercodeMessage.setSysid(CCUMCConstant.UMC_Supplier);
//发送用户
usercodeMessage.addReceiveInfo(null,null,sendCodes.toArray()); 
//发送类型支持多个同时发送
usercodeMessage.setSendflag(UMCConstant.SENDE_TYPE_WEBSITE|UMCConstant.SENDE_TYPE_MAIL|UMCConstant.SENDE_TYPE_MOBILE|UMCConstant.SENDE_TYPE_WECHAT);
//发送消息标题
usercodeMessage.setTitle("title");
//发送内容
usercodeMessage.setContext("context");
//间隔时间（秒）中允许发送最大次数
usercodeMessage.setMaxtimes(CCUMCConstant.UMC_SENDMAXTIEMS);
//间隔时间（秒）
usercodeMessage.setSeconds(CCUMCConstant.UMC_SENDSECONDS);
Map<String,Object> tmpData = new HashMap<>();
//超时5分钟后不发送
tmpData.put(UMCConstant.INNER_MESSAGE_TIMEOUT_SECOND, 5 * 60);
//参数宏
usercodeMessage.setSysmacro(tmpData);
//启用闹钟，闹钟开始时间
usercodeMessage.setDelayStartTime(snsoft.commons.util.DateUtils.textToDate("2020-5-26 19:30"))
//闹钟运行总次数
usercodeMessage.setDelayRunFre(5)
//闹钟运行时间间隔 单位分钟
usercodeMessage.setDelayGap(1)
//发送消息
messageSevice.sendUsercodeMessage(usercodeMessage);
```

**sendParamMessage**
```java
UMCParamMessage paramMessage = new UMCParamMessage();
//供应商配置ID
paramMessage.setSysid(CCUMCConstant.UMC_Supplier);
//发送类型：MESSAGETYPE.MOBILE、MESSAGETYPE.WEBSITE、MESSAGETYPE.MAIL、MESSAGETYPE.WECHAT
paramMessage.setSend_type(MESSAGETYPE.MOBILE.getValue());
//消息类型ID
paramMessage.setMessage_type_id(message_type_id);
//间隔时间（秒）中允许发送最大次数
paramMessage.setMaxtimes(CCUMCConstant.UMC_SENDMAXTIEMS);
//间隔时间（秒）
paramMessage.setSeconds(CCUMCConstant.UMC_SENDSECONDS);
//响应方
paramMessage.setResponse(extUser.getMobile());
Map<String,Object> tmpData = new HashMap<>();
tmpData.putAll(data);
tmpData.put("touname", extUser.getUserName());
tmpData.put("mobile", extUser.getMobile());
//超时5分钟后不发送
tmpData.put(UMCConstant.INNER_MESSAGE_TIMEOUT_SECOND, 5 * 60);
//参数宏
paramMessage.setSysmacro(tmpData);
//启用闹钟，闹钟开始时间
paramMessage.setDelayStartTime(snsoft.commons.util.DateUtils.textToDate("2020-5-26 19:30"))
//闹钟运行总次数
paramMessage.setDelayRunFre(5)
//闹钟运行时间间隔 单位分钟
paramMessage.setDelayGap(1)
//发送消息
boolean sendResult = messageSevice.sendParamMessage(paramMessage);
```
**sendMessage**
```java
UMCMessage message = new UMCMessage();
//供应商配置ID
message.setSysid(CCUMCConstant.UMC_Supplier);
//发送消息标题
message.setTitle("title");
//发送消息内容
message.setContext("context");
//发送消息类型
message.setSend_type(UMCConstant.MESSAGETYPE.MOBILE.getValue());
//请求方  可以为空
message.setRequest("request");
//响应方
message.setResponse("response");
//间隔时间（秒）中允许发送最大次数
message.setMaxtimes(CCUMCConstant.UMC_SENDMAXTIEMS);
//间隔时间（秒）
message.setSeconds(CCUMCConstant.UMC_SENDSECONDS);
Map<String,Object> tmpData = new HashMap<>();
//超时5分钟后不发送
tmpData.put(UMCConstant.INNER_MESSAGE_TIMEOUT_SECOND, 5 * 60);
//设置宏参数
message.setSysmacro(tmpData);
//启用闹钟，闹钟开始时间
message.setDelayStartTime(snsoft.commons.util.DateUtils.textToDate("2020-5-26 19:30"))
//闹钟运行总次数
message.setDelayRunFre(5)
//闹钟运行时间间隔 单位分钟
message.setDelayGap(1)
//发送消息 此方法宏不替换标题和内容 属于真实的发送 不处理标题和内容
messageSevice.sendMessage(message);
```

#### 启用WS（SSE）说明

内置WEBSOCKET（SSE）参数：UMCConstant.INNER_MESSAGE_WEBSOCKET_MSGTYPE，如果参数中存在此参数，以此参数为准，否则走配置文件

**注意：**如果启用WEBSOCKET（SSE），用户消息服务器系统选项配置必须配置与WEBSOCKET相同的KAFKA服务地址。

#### 启用闹钟说明

内置闹钟参数：UMCConstant.INNER_MESSAGE_DELAY_STARTTIME	 	 闹钟开始时间，同时作为闹钟启用的开关
内置闹钟参数：UMCConstant.INNER_MESSAGE_DELAY_RUNFRE       	 执行消息总次数           默认1
内置闹钟参数：UMCConstant.INNER_MESSAGE_DELAY_GAP			 延迟间隔 单位分钟      默认10
示例：
//闹钟开始时间，同时作为闹钟启用的开关
msg.setDelayStartTime(snsoft.commons.util.DateUtils.getServerDate(false))
//执行消息总次数
msg.setDelayRunFre(2)
//延迟间隔 单位分钟 
msg.setDelayGap(10)
//延迟执行时，先执行发送消息，再执行延迟动作，默认false不开启
msg.setDelayFirst(true)

#### 模板TAC说明

执行TAC发送消息时必须设置是否发送消息，如果发送消息时，添加要发送的消息（可多个）
//设置发送消息（必须设置）
void setSendMsg(boolean sendMsg)
//添加发送消息（必须设置）
void addMsg(UMCUsercodeParamMessage msg)

内置变量：
```
cmd=命令（例如：afterSave）
event=事件，只有get和set方法，例如：event.getEventTarget()，获取事件源对象
oldrecord=旧对象，业务底层连用时表示修改之前的对象
record=最新对象，业务底层连用时表示最新修改的对象
```

内置方法：
```
//添加发送消息（必须设置）
void addMsg(UMCUsercodeParamMessage msg);
//添加发送消息
void addMsgList(List<UMCUsercodeParamMessage> msgList);
//清除所有发送消息
void clearMsg();
//移除发送消息
UMCUsercodeParamMessage removeMsg(UMCUsercodeParamMessage msg);
//获取发送消息列表
List<UMCUsercodeParamMessage> getMsgList();
//是否发送消息
boolean isSendMsg();
//设置发送消息（必须设置）
void setSendMsg(boolean sendMsg);
//获取消息配置
UMCMessageTypeConf getMessageconf();
//创建接收对象信息
UMCReceiveInfo createReceiveInfo(String cuicode, String rtype, String... codes);
//创建消息
UMCUsercodeParamMessage createMsgParam();
UMCUsercodeParamMessage createMsgParam(List<UMCReceiveInfo> receiveInfos, Map<String,Object> sysmacro);
UMCUsercodeParamMessage createMsgParam(List<UMCReceiveInfo> receiveInfos, Map<String,Object> sysmacro, String warnLevel, String sysid);
//创建消息自动消息转换
UMCUsercodeParamMessage createMsgParam(UMCMessageTacEvent event);
//添加穿透详情参数（record必须是VO对象）
void putViewDetail(UMCMessageTacEvent event, VO record, UMCUsercodeParamMessage msgParam)
void putViewDetail(UMCMessageTacEvent event, VO record, UMCUsercodeParamMessage msgParam, String sheetCode)
//添加穿透参数
void putViewColumn(UMCUsercodeParamMessage msgParam, String key, Object value);
//获取服务
<T> T getBeanByName(String beanId);
//判断状态是否改变 （必须是VO对象）
boolean judgeStatus(UMCMessageTacEvent event, VO record);
//判断某个列是否改变 （必须是VO对象）
boolean judgeColumn(UMCMessageTacEvent event, VO record, String column, Object defaultV);
//事件源是否系统分类对象
boolean isFitSys(UMCMessageTacEvent event)
//事件源是否定时频率对象
boolean isFitFre(UMCMessageTacEvent event)
//事件源是否单据消息对象
boolean isFitSheet(UMCMessageTacEvent event)
//获取数据库对象 （必须是VO对象）
VO getDBRecord(UMCMessageTacEvent event, VO record);
//获取旧对象
Serializable getOldRecord(UMCMessageTacEvent event);	
```

消息常用方法：
```
//添加接收对象信息
void addReceiveInfo(String cuicode, String rtype, String... codes)
//添加接收对象信息
void addReceiveInfo(UMCReceiveInfo receiveInfo)
//添加宏参数
void putMacro(String key, Object value)
//添加穿透Funcid
void putViewFunid(String funid)
//添加穿透参数
void putViewColumn(String key, Object value)
//添加指定数据的打印格式 new snsoft.umc.api.message.entity.UMCPrintDataSetParam$UMCPrintDataSet(new Object{ { } },DataColumn[])
void setPrintDataSet(UMCPrintDataSet mainRS, Map<String,UMCPrintDataSet> detailRS)
//添加指定UIID的打印格式
void setPrintUIID(Map<String,Object> queryP, String muiid)
//添加指定UIID的打印格式，特别说明：usercode=获取打印数据时的用户登录信息，为空时使用adm获取打印数据（打印数据通过UI端MUIID获取）
void setPrintUIID(Map<String,Object> queryP, String muiid,String usercode)
//设置发送用户
void setUsercode(String usercode)
//延迟执行开始时间
void setDelayStartTime(Date startTime)
//延迟执行总次数，默认1
void setDelayRunFre(int runfre)
//延迟执行间隔分钟，默认10分钟
void setDelayGap(int minute)
//延迟执行时，先执行发送消息，再执行延迟动作，默认false不开启
void setDelayFirst(boolean first)
//设置发送消息超时时间，单位秒
void setTimeoutSecond(int timeoutSecond)
//设置发送消息间隔时间（秒）
void setSeconds(long seconds)
//设置发送消息间隔时间（秒）中允许发送最大次数
void setMaxtimes(int maxtimes)
```

事例说明：
```
sendMsg=judgeStatus(event,record) && "70".equals(record.getStatus()) && "30".equals(oldrecord.getStatus())
setSendMsg(sendMsg)
if sendMsg
se = event.getEventTarget()
msg1 = createMsgParam()
pticode= record.getPticode()
prot = getBeanByName(snsoft.ft.trd.t35.prot.exp.service.ExpProtService.BeanID).queryByInnerCode(pticode)
// 外综协议号：{Ptcode}; 甲方（委托方）：{Ccodetrust}。
ptcode = prot.getPtcode()
cuicode = prot.getCuicode()
ccodetrust =snsoft.ft.trd.comm.utils.TrdCommUtils.getCodeNameByCuicode("FT-CODE.Ccode",  prot.getCcodetrust(),cuicode)
// 处理宏参数
msg1.putMacro("Ptcode", ptcode)
msg1.putMacro("Ccodetrust", ccodetrust)
//穿透界面处理
msg1.putViewFunid("FT-TRD.35.Prot.ExpProtDetail")
msg1.putViewColumn("pticode",pticode)
// 发送用户
msg1.setUsercode(se.getUsercode())
//添加岗位
msg1.addReceiveInfo(cuicode,snsoft.umc.api.base.constant.UMCConstant.MSG_RECEIVE_TYPE_G,null)
addMsg(msg1)
end if
```

系統分类的消息
```
boolean isFitSys = isFitSys(event)
if isFitSys && !"xxxx".equals(event.getEventTarget().getSheetcode())
    //自动创建消息
	msg = createMsgParam(event)
	setSendMsg(true)
	addMsg(msg)
end if
```

#### 定时频率说明

指定频率（每年、每月、每周、每天、指定时间），定时执行消息（定时执行模板TAC）
**注意：**设置定时任务，由于频率以分钟为单位，定时任务必须设置不超过每分钟执行一次

#### 系统分类说明

主要用于发送消息时指定具体的系统分类进行匹配，匹配到的消息按顺序执行（如果存在模板TAC，按模板TAC发送消息）
调用方法：sendUsercodeFitParamMessage

#### 仿真格式说明

消息执行时，如果是邮件模板并且指定仿真格式时，必须指定打印格式数据，进行仿真打印内容，默认HTML
指定打印格式数据：
1. 指定具体的打印数据
1. 指定界面定义和查询参数

#### 单据消息说明

适用范围查找，匹配到的消息按顺序执行模板TAC发送消息（此过程支持事物）
```xml
<Groups id="单据消息">
        <Functions>
            <Functype>SN-PLAT.SV</Functype>
            <Subtype>1</Subtype>
            <Funcimpl><![CDATA[
        SN-UMC.UMCSMDFitSheetSVListener?[{sheetCode:'SN-HELP.Salorder',isAsync:true}]
        ]]></Funcimpl>
            <Remark>单据消息</Remark>
        </Functions>
</Groups>
```
**注意：**业务单据依赖用户消息客户端

#### JMS发送配置

1. JMS发送延迟执行配置选项
1. 位置：客户端UI中cfg/umc/messagejms/*.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<UMCJmsMessageConfigList>
	<UMCJmsMessageConfig delaytype="SN-EM/Mass"  bean="SN-EM.EmailDelayNotifyForMass.dealJmsMessage" name="群发" />
	<UMCJmsMessageConfig delaytype="SN-EM/Cancel"  bean="SN-EM.EmailDelayNotifyForCancel.dealJmsMessage" name="可撤销" />
	<UMCJmsMessageConfig delaytype="SN-EM/Sepr"  bean="SN-EM.EmailDelayNotifyForSepr.dealJmsMessage" name="分别" />
	<UMCJmsMessageConfig delaytype="SN-EM/Todo"  bean="SN-EM.EmailDelayNotifyForTodo.dealJmsMessage" name="待办" />
	<UMCJmsMessageConfig delaytype="SN-EM/Time"  bean="SN-EM.EmailDelayNotifyForTime.dealJmsMessage" name="定时" />
	<UMCJmsMessageConfig delaytype="SN-EM/Retry"  bean="SN-EM.EmailDelayNotifyForRetry.dealJmsMessage" name="群发/分别重试" />
	<UMCJmsMessageConfig delaytype="SN-EM/Noti"  bean="SN-EM.EmailDelayNotifyForNoti.dealJmsMessage" name="回执" />
</UMCJmsMessageConfigList>
```
**注意**
默认使用AMQ，发送精准延迟消息
SN-UMC.Comm.jms.brokerURL=nio://10.77.49.3:61616
SN-UMC.Comm.jms.destination=SN-UMC
JMS发送服务：UMCJmsMessageService
jmskey：业务信息，保证发送消息唯一（系统/场景/业务内码），可根据jmskey进行取消发送，根据jmskey有且只能有一个正在执行的消息
如：sn-em/time/619da81afee03605656febd6
delaytype：JMS发送配置处理类型

### 设计

#### 主要功能

1. 灵活的队列散列，大幅减低Redis的主线程压力。
1. 支持发送消息邮件、站内信、短信、三方应用(微信等)、极光推送。
1. 支持消息按定时频率执行。
1. 支持单据消息，由实施人员动态干预发送消息。

#### 原理介绍
1. UMC用户消息采用【二队列】（发送队列和推送队列）处理消息
1. 发送队列只负责接受消息，推送队列只负责发送消息
1. 在发送队列之后分解消息进入推送队列（一个发送消息产生多个推送消息）
1. 分解消息时根据消息模板配置进行分解处理（消息模板配配置、响应方个数等参数决定发送消息个数）
1. 发送消息时根据供应商发送消息（此时只有供应商配置和消息参数，与消息模板配置无关）

![](help/SN-UMC/UMC/img/用户消息核心流程.png)

#### 情景说明

**站内信HTML**

```
<websitetext_format>
<![CDATA[
<div id="rpath" style="display: none;">{rpath}</div>
<div id="sheetrpathinner" style="display: none;">{sheetrpathinner}</div>
<div id="sheetrpathuiid" style="display: none;">{sheetrpathuiid}</div>
<div id="attachrpath" style="display: none;">{attachrpath}</div>
<a href="javascript:void();" onclick="var rpath=Xjs.DOM.findById('rpath',null).innerText;Xjs.ui.UIUtil.wopenUI(null,rpath,{AutoRefresh:1});" style="color: -webkit-link;" >审批工作台</a> 
<a href="javascript:void();" onclick="var sheetrpathinner=Xjs.DOM.findById('sheetrpathinner',null).innerText;var sheetrpathuiid=Xjs.DOM.findById('sheetrpathuiid',null).innerText;Xjs.ui.UIUtil.wopenUI(null,sheetrpathuiid,{AutoRefresh:1,'InitValue.rpicode':sheetrpathinner});" style="color: -webkit-link;" >单据详情</a> 
<a id="attachrpatha1" href="javascript:void();" onclick="var attachrpath=Xjs.DOM.findById('attachrpath',null).innerText;var href=Xjs.ui.UIUtil.buildAttachURL(attachrpath, false);if(!Xjs.ui.UIUtil.openInIFrame('',href)){window.open(href,'_blank');};" style="color: -webkit-link;" >下载附件</a> 
]]>
</websitetext_format>
```
**微信HTML**
```
<wechattext_format>
<![CDATA[
   文本内容<a href={rpath} >点击跳小程序</a>
   <a href="https://www.chinaexp365.com/mnt/index.html" >云平台</a>
]]>
</wechattext_format>
```
**邮件HTML**
```
<mailtext_format>
<![CDATA[
<div style="padding:0;margin:0 auto;font-family:'Microsoft YaHei';font-size:14px;border:solid 1px #ddd;">
	<div style="margin:0 0 54px;background:#18bd9b;">
		<span style="font-size:28px;line-height:36px;font-family:'Microsoft YaHei';color:#FFF;">容联 · 云通讯</span> <em>通讯从未如此简单</em> 
	</div>
	<div style="margin:0;padding:0 0 0 49px;">
		<h3 style="font-size:16px;color:#333333;">
			云通讯账号激活
		</h3>
		<p style="color:#555555;">
			您好！您的邮箱{tomail}于{datetime}注册了云通讯账号，请在{day}天内点击以下链接，来自{frommail}，激活该账号：
		</p>
<a href="https://www.yuntongxun.com/user/reg/verifyMail?id=8aaf070863399ed901633a415c8500b1&amp;email=bv2Dj/9BBrDRTIsouZRlOmJv9G7hQhVHLeINjEedOOY=&amp;vCode=919211d427f9d2baad3155453a6e3189&amp;vTime=MjAxOC0wNS0wNyAxODo1OQ==" target="_blank">https://www.yuntongxun.com/user/reg/verifyMail?id=8aaf070863399ed901633a415c8500b1&amp;email=bv2Dj/9BBrDRTIsouZRlOmJv9G7hQhVHLeINjEedOOY=&amp;vCode=919211d427f9d2baad3155453a6e3189&amp;vTime=MjAxOC0wNS0wNyAxODo1OQ==</a> 
		<p style="color:#999999;">
			(若无法点击链接请复制到地址栏访问；您若未注册云通讯请忽略此邮件，敬请谅解)
		</p>
		<div style="margin:0;padding:0;">
		</div>
	</div>
	<div style="padding:0 49px;margin:0;background:#f2f2f2;">
		<span style="line-height:61px;color:#999999;">本邮件由云通讯系统自动发出，请勿直接回复</span> <span style="line-height:61px;color:#999999;">&copy; 容联 · 云通讯  yuntongxun.com</span> 
	</div>
</div>
]]>
</mailtext_format>
```

#### UMC类图
@startuml
Title 用户消息功能类图

UMCMessageService <|.. UMCMessageSeviceImpl

UMCSendMessageSevice <|.. UMCMailSendMessageService
UMCSendMessageSevice <|.. UMCMobileSendMessageService
UMCSendMessageSevice <|.. UMCWebSiteSendMessageService
UMCSendMessageSevice <|.. UMCAppSendMessageService
UMCAppSendMessageService <|.. UMCWeChatSendMessageService
UMCAppSendMessageService <|.. UMCQQSendMessageService

UMCMobileSendMessageService <-- MobileServiceCCP
UMCMobileSendMessageService <-- MobileServiceMLT
UMCMobileSendMessageService <-- MobileServiceAliyun
UMCMobileSendMessageService <-- MobileServiceYMT
UMCMobileSendMessageService <-- MobileServiceUms
UMCMobileSendMessageService <-- MobileServiceQCloud

class MobileServiceCCP{
 + boolean sendMessagesNow();
}
class MobileServiceMLT{
 + boolean sendMessagesNow();
}
class MobileServiceAliyun{
 + boolean sendMessagesNow();
}
class MobileServiceYMT{
 + boolean sendMessagesNow();
}
class MobileServiceUms{
 + boolean sendMessagesNow();
}
class MobileServiceQCloud{
 + boolean sendMessagesNow();
}

class UMCSendMessageSevice {
	+ boolean sendMessage(UMCMessage message);
}
class UMCMailSendMessageService {
	+ boolean sendMessage(UMCMessage message);
}
class UMCMobileSendMessageService {
	+ boolean sendMessage(UMCMessage message);
}
class UMCWebSiteSendMessageService {
	+ boolean sendMessage(UMCMessage message);
}
class UMCAppSendMessageService {
	+ boolean sendMessage(UMCMessage message);
}
class UMCWeChatSendMessageService {
	+ boolean sendMessage(UMCMessage message);
}
class UMCQQSendMessageService {
	+ boolean sendMessage(UMCMessage message);
}

class UMCMessageService {
	+ boolean sendUsercodeMessage(UMCUsercodeMessage usercodeMessage);
	+ boolean sendUsercodeParamMessage(UMCUsercodeParamMessage usercodeParamMessage);
	+ boolean sendParamMessage(UMCParamMessage paramMessage);
	+ boolean sendMessage(UMCMessage message);
}

class UMCMessageSeviceImpl {
	+ boolean sendUsercodeMessage(UMCUsercodeMessage usercodeMessage);
	+ boolean sendUsercodeParamMessage(UMCUsercodeParamMessage usercodeParamMessage);
	+ boolean sendParamMessage(UMCParamMessage paramMessage);
	+ boolean sendMessage(UMCMessage message);
	+ void decompositionMessage(UMCBasInfo message);
	+ void pushMessage(UMCMessage message);
	
}


@enduml
#### UMC时序图

@startuml
Title UMC时序图

actor 客户端

participant service   as "UMCMessageService"
participant queue   as "UMCQueueService"
participant timer   as "UMCTimer"
participant sendmesage   as "UMCSendMessageSevice"
客户端 -> service : [sendMessage]\n发送消息
activate service

	service -> service : [checkSendMessage]\n检查消息
	service -> service : [offerSendMessage]\n消息进栈（第一队列）
	service -> queue : [offerQueueSendMessage]\n消息进栈（第一队列）

deactivate service
timer -> service :run
		activate timer		
			activate service
			queue <-> service : [pollSendMessage]\n消息出栈（第一队列）
			service -> service : [pushSendMessage]\n推送消息（第一队列）
			service -> service : [decompositionMessage]\n分解消息
			service -> service : [offerMessage]\n消息进栈（第二队列）
			service -> queue : [offerQueueMessage]\n消息进栈（第二队列）
			deactivate service
		deactivate timer
timer -> service :run
		activate timer		
			activate service
			queue <-> service : [pollMessage]\n消息出栈（第二队列）
			service -> service : [pushMessage]\n推送消息（第二队列）
			service -> service : [noSendMessage]\n消息检查不通过的消息，不允许执行
			service -> sendmesage : [sendMessage]\n发送消息
			activate sendmesage		
			sendmesage -> UMCMailSendMessageService : [sendMessage]\n发送邮件消息
			deactivate sendmesage
			
			service -> sendmesage : [sendMessage]\n发送消息
			activate sendmesage		
			sendmesage -> UMCMobileSendMessageService : [sendMessage]\n发送短信消息
			deactivate sendmesage
			
			service -> sendmesage : [sendMessage]\n发送消息
			activate sendmesage		
			sendmesage -> UMCWebSiteSendMessageService : [sendMessage]\n发送站内信消息
			deactivate sendmesage
			
			service -> sendmesage : [sendMessage]\n发送消息
			activate sendmesage		
			sendmesage -> UMCAppSendMessageService : [sendMessage]\n发送三方消息
			activate UMCAppSendMessageService		
			UMCAppSendMessageService -> UMCWeChatSendMessageService : [sendMessage]\n发送微信消息
			deactivate UMCAppSendMessageService
			
			deactivate sendmesage
		deactivate sendmesage
			deactivate service
		deactivate timer
service -> 客户端
@enduml
