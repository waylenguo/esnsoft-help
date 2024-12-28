## WebSocket(SSE)

|日期|作者|备注|
|------|------|------|
|2018-11-06|陈泰林|初稿|
|2019-10-11|陈泰林|修改|

### 指南
#### 概念说明

WebSocket能够让客户端和远程服务端通过web建立全双工通信。支持传输二进制字符串和文本字符串；
一般用于服务端向客户端发送展示信息、运维部署预警通知、实时通讯等；

SSE（Server-Sent Events，服务器发送事件）,基于HTTP，支持从服务器到客户端的单项通信。支持传输文本，不支持传输二进制字符。
适用于在股票行情、新闻推送的这种只需要服务器发送消息给客户端场景

```
SSE与WebSocket有相似功能，都是用来建立浏览器与服务器之间的通信渠道。两者的区别在于：

	WebSocket是全双工通道，可以双向通信，功能更强；SSE是单向通道，只能服务器向浏览器端发送。
	WebSocket是一个新的协议，需要服务器端支持；SSE则是部署在 HTTP协议之上的，现有的服务器软件都支持。
	SSE是一个轻量级协议，相对简单；WebSocket是一种较重的协议，相对复杂。
	SSE默认支持断线重连，WebSocket则需要自己保持心跳。
	SSE支持自定义发送的数据类型。
	SSE不支持CORS 参数url就是服务器网址，必须与当前网页的网址在同一个网域（domain），而且协议和端口都必须相同。
```


#### 使用说明

**WS服务器配置**

&#35;websocket=========
snsoft.websocket=true

&#35;kafka可不配置============
&#35;发送方kafka
snsoft.websocket.mqtype=kafka    
snsoft.kafka=true
snsoft.kafka.host=127.0.0.1:9092

&#35;redis可不配置============
&#35;发送方redis(默认)
snsoft.websocket.mqtype=redis
snsoft.redis=true
snsoft.redis.host=127.0.0.1
snsoft.redis.port=6379

&#35;jms可不配置============
&#35;发送方jms
snsoft.websocket.mqtype=jms
snsoft.jms=true
snsoft.jms.brokerURL=nio://127.0.0.1:61616

**SV服务器配置**

&#35;kafka可不配置============
&#35;发送方kafka
snsoft.kafka=true
snsoft.kafka.host=127.0.0.1:9092

&#35;redis可不配置============
&#35;发送方redis(默认)
snsoft.redis=true
snsoft.redis.host=127.0.0.1
snsoft.redis.port=6379

&#35;jms可不配置============
&#35;发送方jms
snsoft.jms=true
snsoft.jms.brokerURL=nio://127.0.0.1:61616

**UI服务器配置**


&#35;websocket=========
snsoft.websocket.host=ws://192.168.0.194:2020/N9BWS/websocket
&#35;snsoft.websocket.host=wss://n.esnsoft.cn/N9BWS/websocket

&#35;kafka可不配置============
&#35;发送方kafka
snsoft.kafka=true
snsoft.kafka.host=127.0.0.1:9092

&#35;redis可不配置============
&#35;发送方redis(默认)
snsoft.redis=true
snsoft.redis.host=127.0.0.1
snsoft.redis.port=6379

&#35;jms可不配置============
&#35;发送方jms
snsoft.jms=true
snsoft.jms.brokerURL=nio://127.0.0.1:61616

** SSE配置
> 配置在UI服务器， redis、kafka、jms的配置与webSocket相同

&#35;sse=========
snsoft.sse=true

### 手册

#### 自定义展示信息

**开发WEBSOCKET(SSE)自定义展示信息**

位置：客户端UI中cfg/res/websocket/WebSocketMessageType*.xml
参考：cfg/res/websocket/WebSocketMessageTypeSN-CMC.xml

**支持2中写法**
```java
1、直接写方法
onMessage:function(msg)
{
    alert(msg);
}
2、只支持一个监听
#new snsoft.cmc.websocket.ControlListener();
public void onMessage(Object message)
{
}
```
**底层默认支持**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<WebSocketMessageTypes xmlns="http://www.snsoft.com.cn/schema/WebSocketMessageType"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/WebSocketMessageType http://www.snsoft.com.cn/schema/WebSocketMessageType.xsd"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<MessageTypes wscode="SN-CMC.Alert" wsname="警示消息(SN-CMC)">
		<JSListener><![CDATA[
		onMessage:function(msg)
		{
			alert(msg);
		}
		]]></JSListener>
		<Remark><![CDATA[
		用于向客户端登录首页面推送警示消息：
		1、消息体必须为文本；
		]]></Remark>
	</MessageTypes>
	<MessageTypes wscode="SN-CMC.Ctrl" wsname="管控消息(SN-CMC)">
		<JSListener><![CDATA[
		#new snsoft.cmc.websocket.ControlListener();
		]]></JSListener>
		<Remark><![CDATA[
		用于调试指定用户接收到的WebSocket信息。
		]]></Remark>
	</MessageTypes>
	<MessageTypes wscode="SN-CMC.WarnTip" wsname="右下角预警消息(SN-CMC)">
    		<JSListener><![CDATA[
    		#new Xjs.websocket.WSDefaultMessageProcesser();
    		]]></JSListener>
    		<Remark><![CDATA[
    		用于向客户端登录首页面推送右下角提示消息，消息体结构如下：
    		{
    			msgid:"消息id",
    			title:"标题",
    			content:"内容",
    			muiid:"对应的界面定义，可以为空",
    			funcid:"对应的界面路由，可以为空",
    			sheetcode:"单据号，穿透定义使用",
    			fname:"发送人",
    			senddate:"发送日期",
    			params:{}
    		}
    		打开传递参数格式：
			InitValue.paramkey = InitValue|paramkey
			initTblname = initTblname
    		]]></Remark>
    </MessageTypes>
</WebSocketMessageTypes>
```

参见[WebSocket定义](uiinvoke/00/zh_CN/theme0/90.websocket.WebsocketDef.html)

参见[WebSocket管理](uiinvoke/00/zh_CN/theme0/90.websocket.Websocket.html)

**WEBSOCKET与用户消息UMC建立关系**参见[UMC用戶消息](help.html?helpFile=help/SN-UMC/UMC/UMCConfig.md#启用WS说明)

#### 配置详细说明

* WS服务器配置SN.ID=服务器名称
* 消息中间件不指定时默认使用redis，redis也没有时默认使用本地
* system.properties中可选配置参数
  snsoft.websocket.opts
  1：检查是否登录  2：检查重复登录 4：监听 是否需要 获取用户列表
  snsoft.websocket.client.flag
  1：WebSocket连接关闭时提示信息  2: 添加心跳  默认值为2
  snsoft.websocket.client.hearttime
  心跳时间间隔，单位秒  默认值为120=2分钟  
  SpringBoot启动时需设置 已废弃不用配置
  snsoft.websocket.springboot=true 
* WS服务器支持心跳检测根据最后交互时间与当前时间差值比较，时间较长时主动关闭连接
* 发送消息默认类型支持
  Message : 一般消息
  ReLogin :用户在其他地方重新登录
  LoginFail : 登录失败
  UserLogin : 有其他新用户登录
  UserLogout : 有其他用户退出
  ping：心跳
* kafka发送消主题
  inline-notify：Kafka消息中间件在线通知主题
  sqlupdate-notify：sqlupdate-notify数据更新主题
* 发送消息入口
  MsgUtils.sendWebSocketMessage(userId, messageText, msgType)
* 部署情况
```java
  开发环境：SpringBoot启动项中添加
    /**
	 * WebSocket 启动注册功能
	 * @see WebSocketServerEndpoint
	 */
	@Bean
	public ServerEndpointExporter serverEndpointExporter()
	{
		return new ServerEndpointExporter();
	}
```
```json
  线上环境：Tomcat启动不需要添加任何启动项
  实际配置中的坑：
                 1、kafka配置中必须在server.properties指定listener
         		 2、使用https必须配置snsoft.websocket.host=wss://n.esnsoft.cn/N9BWS/websocket
         		 3、使用 Nginx必须配置支持WebSocket选项（不配置默认1分钟后断开连接）
         		  http {
      			  server {
          			location / {
             			root   html;
              			index  index.html index.htm;
              			proxy_pass http://webscoket; 
              			proxy_http_version 1.1; 
              			proxy_connect_timeout 12s; #websocket连接超时时间
              			proxy_read_timeout 1800s; #websocket (sse)，设置了30分钟，那么如果我30分钟内有通讯，或者30分钟内有做心跳的话，是可以保持连接不中断的，默认情况下，连接将会在无数据传输60秒后关闭
             			proxy_send_timeout 12s; #websocket发送超时时间
              			proxy_set_header Upgrade $http_upgrade; #这是webSocket的配置
              			proxy_set_header Connection "Upgrade"; #这是webSocket的配置
          				}
      				}
  				}
  				4、WebSocket已经支持了心跳，默认开启状态。
 ```

```java
  SSE客户端代码说明：为防止页面频繁刷新创建大量连接消耗系统资源，一定在界面关闭时，关闭连接
  示例：
       /// 绑定事件  页面关闭时  通知服务端  删除连接
		DOM.addListener(window, "beforeunload", ((js.IFunction_V) this::onWindowBeforeunload).createDelegate(this));
		public void onWindowBeforeunload()
		{
			if (messageSSE != null)
			{
				messageSSE.svclose();
			}
		}
 ```

### 设计

#### 功能介绍

* 基于Spring的WebSocket实现
* 使用Kafka消息中间件广播消息
* 使用自定义WebSocket配置实时调整信息提示
* 使用WebSocket管理界面对建立连接的用户进行管理监控及调试

#### 系统流程

![](help/SN-CMC/Websocket/image/WebSocket系统流程图.png)



#### 相关代码

* kafka配置：snsoft.dx.kafka.config.KafkaConfig
* WebSocket配置：snsoft.websocket.config.WebSocketConfig
* SSE配置：snsoft.sse.config.SSEConfig
* Spring集成WebSocket：snsoft.websocket.controller.WebSocketServerEndpoint
* WebSocket管理：snsoft.websocket.WebSocketSessionManager
* WebSocket封装Session连接：snsoft.websocket.WebSocketSession
* SSE管理：snsoft.sse.SSESessionManager
* SSE封装Session连接：snsoft.sse.SSESession
* 消息发送接口类：snsoft.commons.msg.MessageSender
* 服务发送消息入口：snsoft.commons.msg.MessageUtils
* kafka播放监控信息：snsoft.dx.msg.kafka.KafkaMessageListener
* kafka消息监听：snsoft.websocket.mq.MQMessageListener
* WebSocket（SSE）管理服务：snsoft.cmc.websocket.service.WebSocketDistService
* WebSocket（SSE）定义服务：snsoft.cmc.websocket.service.WebSocketUIService
* WebSocket（SSE）服务信息处理：snsoft.cmc.websocket.service.WebSocketServerInfoService
* HomePage中集成WebSocket（SSE）：snsoft.homepage.HomePage
* HomePage中集成WebSocket监听：xjs.websocket.HomePageWebSocket
* HomePage中集成SSE监听：xjs.websocket.HomePageSSE
* 客户端WebSocket入口封装：xjs.msg.MessageSocket
* 客户端SSE入口封装：xjs.msg.MessageSSE
* 客户端监听实现接口：xjs.msg.MessageListener

#### 标准规范

* 统一在WebSocket定义中定义要处理的监听
* 服务端发送消息使用MsgUtils.sendWebSocketMessage(userId, messageText, msgType)
* 监控WebSocket在WebSocket管理中统一管理所有的监听

#### 类图
@startuml
Title WebSocket(SSE)客户端功能类图

HomePage <|-- HomePageWebSocket
HomePageWebSocket <|-- MessageSocket
MessageListener <|.. HomePageWebSocket
MessageSocket <|-- WebSocket
HomePage <|-- HomePageSSE
HomePageSSE <|-- MessageSSE
MessageListener <|.. HomePageSSE
MessageSSE <|-- EventSource

class MessageListener {
	+ public void onOpen(MessageSocket s,js.WebSocketEvent ev)
	+ public void onError(MessageSocket s,js.WebSocketEvent ev)
	+ public void onClose(MessageSocket s,js.WebSocketEvent ev)
	+ public void onMessage(MessageSocket s,Message msg,js.WebSocketEvent ev)
}
class MessageSocket {
	+ public void open();
	+ public void setExtParam(JSObject extParams)
	+ public void close()
	+ protected void onOpen(js.WebSocketEvent ev)
	+ protected void onMessage(js.WebSocketEvent ev)
	+ protected void onClose(js.WebSocketEvent ev)
	+ protected void onError(js.WebSocketEvent ev)
}

class MessageSSE {
	+ public void open();
	+ public void setExtParam(JSObject extParams)
	+ public void close()
	+ protected void onOpen(js.WebSocketEvent ev)
	+ protected void onMessage(js.WebSocketEvent ev)
	+ protected void onError(js.WebSocketEvent ev)
}

@enduml
@startuml
Title WebSocket服务端功能类图

WebSocketServerEndpoint <|-- WebSocketSessionManager
WebSocketSessionManager <|-- WebSocketSession
WebSocketSession <|-- Session

class WebSocketServerEndpoint {
	+ public void onOpen(final javax.websocket.Session session, @PathParam("userId") final String userId)
	+ public void onClose(javax.websocket.Session session)
	+ public void onMessage(String message, javax.websocket.Session session)
	+ public void onError(Throwable ex)
}
class WebSocketSessionManager {
	+ public void onOpen(final WebSocketSession session)
	+ public void onClose(WebSocketSession session)
	+ public void onMessage(String message, String userId, Object _session)
	+ public int sendTextMessage(String toUserId, String message)
	+ public int userLogoutAll(String toUserId)
	+ public WebSocketSession[] getAllSessionsByUserID(String userId)
	+ public WebSocketSession[] getAllSessions()
	+ public void removeSession(WebSocketSession session)
	+ public WebSocketSession lookup(String userId, Object _session)
}


@enduml
@startuml
Title SSE服务端功能类图

SSEServerController <|-- SSESessionManager
SSESessionManager <|-- SSESession
SSESession <|-- SseEmitter

class SSEServerController {
	+ public void onOpen(HttpServletRequest request,@PathVariable String userId)
	+ public ResponseEntity<String> onClose(@PathVariable("userId") String userid, @PathVariable("sseId") String sseId)
}
class SSESessionManager {
	+ public void onOpen(final SSESession session)
	+ public void onClose(SSESession session)
	+ public void onMessage(String message, String userId, Object _session)
	+ public int sendTextMessage(String toUserId, String message)
	+ public int userLogoutAll(String toUserId)
	+ public SSESession[] getAllSessionsByUserID(String userId)
	+ public SSESession[] getAllSessions()
	+ public void removeSession(SSESession session)
	+ public SSESession lookup(String userId, Object _session)
}


@enduml

#### 时序图
@startuml
Title WebSocket(SSE)建立连接

actor 客户端

participant HomePage    as "HomePage"
participant HomePageWebSocket    as "HomePageWebSocket\n(HomePageWebSSE)"
participant MessageSocket   as "MessageSocket\n(MessageSSE)"
participant WebSocketServerEndpoint     as "WebSocketServerEndpoint\n(SSEServerController)"
participant WebSocketSessionManager     as "WebSocketSessionManager\n(SSESessionManager)"
participant WebSocketSession     as "WebSocketSession\n(SSESession)"


客户端 -> HomePage : [render]\n首页初始化
activate HomePage

	HomePage -> HomePageWebSocket : [HomePageWebSocket]\nxjs.websocket.HomePageWebSocket初始化websocket
	activate HomePageWebSocket
		HomePageWebSocket -> MessageSocket : [open]\nopen建立连接WebSocket
		activate MessageSocket
			MessageSocket -> WebSocketServerEndpoint : [onOpen]\nonOpen服务器端连接处理
			WebSocketServerEndpoint -> WebSocketSessionManager : [onOpen]\nonOpen服务端打开连接处理
			WebSocketSessionManager -> WebSocketSession : [add]\nadd添加缓存连接
		deactivate MessageSocket
	deactivate HomePageWebSocket
	
deactivate HomePage
@enduml

@startuml
Title WebSocket(SSE)发送消息

actor 服务端

participant MessageUtils    as "MessageUtils"
participant MessageSender    as "MessageSender"
participant KafkaMessageListener   as "KafkaMessageListener"
participant MQMessageListener     as "MQMessageListener"
participant WebSocketSessionManager     as "WebSocketSessionManager\n(SSESessionManager)"
participant WebSocketSession     as "WebSocketSession\n(SSESession)"
participant MessageListener     as "MessageListener"
participant WebSocketUIService     as "WebSocketUIService"
participant HomePageWebSocket     as "HomePageWebSocket\n(HomePageSSE)"

服务端-> MessageUtils : [getDefaultMessageSender]\n获取发送者
activate MessageUtils

	MessageUtils -> MessageSender : [sendMessage]\nsendMessage发送消息
	activate MessageSender
		MessageSender -> Kafka : [sendMessage]\n消息发送kafka
		Kafka -> KafkaMessageListener : [onMessage]\n发送消息
		activate KafkaMessageListener
		     KafkaMessageListener -> MQMessageListener : [acceptMessage]\n接受消息
			KafkaMessageListener -> MQMessageListener : [onMessage]\nMQ监听发送消息
			activate KafkaMessageListener
				KafkaMessageListener -> WebSocketSessionManager : [sendTextMessage]\n发送消息
				WebSocketSessionManager -> WebSocketSession : [sendTextMessage]\n发送消息
				WebSocketSession -> MessageListener : [onMessage]\n客户端监听发送消息
				activate MessageListener
				    MessageListener -> HomePageWebSocket : [onMessage]\n触发监听
				    activate HomePageWebSocket
					HomePageWebSocket -> WebSocketUIService : [getJSListener]\n执行代码块
				    deactivate HomePageWebSocket
				deactivate MessageListener
			deactivate KafkaMessageListener
		deactivate KafkaMessageListener
	deactivate MessageSender
	
deactivate MessageUtils
@enduml
