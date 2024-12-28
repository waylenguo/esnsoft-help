## 接入系统(SN-ACS)

|日期|作者|备注|
|------|------|------|
|2018-09-28|彭小松|初稿|
|2019-03-28|宋建中|修改|

## 主要功能

* N9B系统与第三方系统相互间的异步、同步调用
* 接入缓冲处理
* 限流、熔断及接入控制
* 安全校验
* 密文的加解密
* 输入、输出服务的统计日志

## 工程结构

接入系统工程为snadk-acs，一共有以下4个子工程：

```
服务端:API、UI
客户端:Client
前端:XJS
```

## 部署说明

1. 接入系统属于UI层系统，API+UI+XJS
1. 接入系统的客户端Client与应用系统的服务端部署在一起。
1. <span style="color:red">服务部署时需要修改UI服务的web.xml文件，增加对/access开头的请求的映射</span>

```xml
	<!--    web.xml配置示例     -->
	<servlet-mapping>
		<servlet-name>SpringMVC</servlet-name>
		<url-pattern>/access/*</url-pattern>
	</servlet-mapping>
	<!-- 接入请求需要初始化UserSession时要配置filter（主要在前后端分离开发场景下应用）-->
	<filter-mapping>
		<filter-name>UserSessionFilter</filter-name>
		<url-pattern>/access/*</url-pattern>
	</filter-mapping>
```


## 应用场景

### 第三方同步调用N9B


@startuml

Title 第三方同步调用N9B
participant three   as "三方系统"
participant acs   as "接入系统"
participant client   as "业务系统(包含ACS/MQ客户端)"

activate three
three -> acs : 三方系统请求
activate acs
acs -> acs : 查找接入配置
acs -> acs : 处理接入数据
acs -> client : 服务调用
activate client
client -> client : 服务执行
client -> acs : 返回处理结果
deactivate client
acs -> acs : 处理返回数据
acs -> three : 响应三方系统
deactivate acs
deactivate three

@enduml

```
第三方组织JSON/XML参数通过标准URL调用接入系统 ：AccessController.dataAccess()
接入系统接收第三方请求 ：AccessDataService.inDataAccess()
调用条件检查、流量控制、熔断处理、对象转换、加解密、验签、令牌验证、监听执行
服务调用 ： AccessDataService.remoteInvoke()
ACS客户端接收  ：AccessReceiveService.apply()
服务执行 ： AccessReceiveService.asynchronDataAccess()
对象转换、加解密、监听执行
响应第三方
```

### 第三方异步调用N9B
@startuml

Title 第三方异步调用N9B-创建消息
participant three   as "三方系统"
participant acs   as "接入系统"
participant client   as "业务系统(包含ACS/MQ客户端)"
participant mq   as "消息中间件"

activate three
three -> acs : 三方系统请求
activate acs
acs -> acs : 查找接入配置
acs -> acs : 处理接入数据
acs -> client : 服务调用
activate client
client -> mq : 调用消息客户端
activate mq
mq -> mq : 创建消息
mq -> client : 
deactivate mq
client -> acs :
deactivate client
acs -> three :
deactivate acs
deactivate three

@enduml

@startuml

Title 第三方异步调用N9B-执行消息
participant mq   as "消息中间件"
participant client   as "业务系统(包含ACS/MQ客户端)"
participant acs   as "接入系统"
participant three   as "三方系统"

activate mq
mq -> client : 消息执行
activate client
client -> client : 服务执行
client -> acs : 返回处理结果
activate acs
acs -> acs : 处理返回数据
acs -> three : 响应三方系统
activate three
three -> three : 接收响应
three -> acs :
deactivate three
acs -> client :
deactivate acs
client -> mq :
deactivate client
deactivate mq

@enduml

```
第1步 创建消息
第三方组织JSON/XML参数通过标准URL调用接入系统 ：AccessController.dataAccess()
接入系统接收第三方请求 ：AccessDataService.inDataAccess()
调用条件检查、流量控制、熔断处理、对象转换、加解密、验签、令牌验证、监听执行
服务调用 ： AccessDataService.remoteInvoke() 
ACS客户端接收  ：AccessReceiveService.apply()
异步调用 ： AccessReceiveService.asynchronDataAccess()
发送消息 ： AccessMessageService.sendMessage()
对象转换、加解密、监听执行
响应第三方接收数据成功
第2步 执行消息
消息推送定时任务 ：MQPushTimer.run()
消息推送  ：MQMessageSevice.pushMessage()
MQ客户端接收 ：MQReceiveService.receiveMessage()
消息执行 ：AccessMessageService.runMessage()
服务调用  ： AccessMessageService.executeMessage()
执行结果回调 ： AccessMessageService.callbackMessage()
回调接入系统 ：AccessSendService.sendDataAccess()
响应第三方
删除过程数据 ： AccessMessageService.delRunParam()
返回消息执行结果
```

### N9B同步调用第三方
@startuml

Title N9B同步调用第三方

participant client   as "业务系统(包含ACS/MQ客户端)"
participant acs   as "接入系统"
participant three   as "三方系统"

activate client
client -> acs : 服务调用
activate acs
acs -> acs : 处理调用数据
acs -> three : 调用三方系统
activate three
three -> three : 接收响应
three -> acs : 返回处理结果
acs -> acs : 处理返回数据
deactivate three
acs -> client :
deactivate acs
deactivate client

@enduml

```
业务系统调用接入系统API：AccessSendService.sendDataAccess()
调用条件检查、流量控制、对象转换、加解密、监听执行
服务调用 ： AccessDataService.remoteInvoke()
对象转换、加解密、监听执行
结果返回到业务系统
```

### N9B异步调用第三方

@startuml

Title N9B异步调用第三方
participant busi   as "业务系统(包含ACS/MQ客户端)"
participant mq   as "消息中间件"
participant client   as "业务系统(包含ACS/MQ客户端)"
participant acs   as "接入系统"
participant three   as "三方系统"

activate busi
busi -> mq : 消息发送
activate mq
mq -> mq : 创建消息
mq -> busi
deactivate mq
deactivate busi


mq -> client : 消息推送
activate mq
activate client
client -> client : 服务执行
client -> acs : 返回处理结果
activate acs
acs -> acs : 处理返回数据
acs -> three : 响应三方系统
activate three
three -> three : 接收响应
three -> acs :
deactivate three
acs -> client :
deactivate acs
client -> mq :
deactivate client
deactivate mq

@enduml

```
第1步 创建消息
业务系统通过MQ客户端发送消息 ：AccessMessageService.sendMessage()
消息中间件创建消息
第2步 执行消息
消息推送定时任务 ：MQPushTimer.run()
消息推送  ：MQMessageSevice.pushMessage()
MQ客户端接收 ：MQReceiveService.receiveMessage()
消息执行 ：AccessMessageService.runMessage()
服务调用  ： AccessMessageService.executeMessage()
执行结果回调 ： AccessMessageService.callbackMessage()
回调接入系统 ：AccessSendService.sendDataAccess()
响应第三方
删除过程数据 ： AccessMessageService.delRunParam()
返回消息执行结果
```

### 前后端分离开发


@startuml

Title 第三方同步调用N9B
participant three   as "前端页面"
participant acs   as "接入系统/UI服务"
participant client   as "业务系统(包含ACS/MQ客户端)"

activate three
three -> acs : 前端页面
activate acs
acs -> acs : 查找接入配置
acs -> acs : 处理接入数据
acs -> acs : UI服务调用(权限控制)
acs -> client : 业务服务调用
activate client
client -> client : 服务执行
client -> acs : 返回处理结果
deactivate client
acs -> acs : 处理返回数据
acs -> three : 响应前端页面
deactivate acs
deactivate three

@enduml

```
前端页面通过标准URL调用接入系统 ：AccessController.dataAccess()
接入系统接收前端页面请求 ：AccessDataService.inDataAccess()
调用条件检查、流量控制、熔断处理、对象转换、监听执行
服务调用 ： AccessDataService.remoteInvoke()
UI服务执行：XXXUIService
业务服务执行 ： XXXService
对象转换、监听执行
前端页面响应
```

## 接入配置

#### 接入方配置

```
stoped          停用，关闭与此接入系统的调用
fromfile		文件，配置来自于XML文件   
sender          接入方编码，接入方需分配一个编码用于接入的管控
sname           接入方名称  
acstype			服务调用方向，用于限制接入方的调用方向，IN 第三方调用系统(调入) OUT 系统调用第三方(调出) INSID 内部系统
allowip			允许IP，用于限制接入的第三方服务器IP地址
tokenkey		写入头信息中token的key名称
token			token，用于鉴权
usercode        用户编码：接收请求是用于构建Session使用
signkey			写入头信息中sign的key名称
versionkey		写入头信息中version的key名称
encripted		密文，与此接入方交互需要密文
signoption		签名校验，签名校验的方式 0：不校验1：MD5验签 2： SHA256验签
verifytoken		令牌校验，是否验证令牌 
dateformat		日期格式：默认：yyyy-MM-dd HH:mm:ss
encripttype     加密方式 AES/RSA/DES
presecretkey	秘钥源信息，用于创建秘钥
secretkey       秘钥
secretiv        位移码
contenttype		数据格式， JSON XML TEXT，建议使用 JSON 格式交互数据
encoding       	数据编码
fileresid		文件目录资源，用于通过FTP方式输出数据时配置的输出文件目录
callbackurl		回调地址，用于调用第三方URL地址
mqtype          MQ配置，接入系统与第三方对接的MQ配置类型  
flags           2:输出数据不加密
remark          说明 ，停用时需描述原因  
```

#### 服务接入配置
逻辑主键为：code + sender + receiver

```
stoped          停用，关闭与此接入服务
fromfile		文件，配置来自于XML文件
acsid			ID，数据库配置时生成的主键
acscode         接入编码，接入服务的编号
acsname         接入名称
sender			请求方，可以为空
receiver		响应方，可以为空
servtype		调用方式， Bean Http WS Kafka IBMMQ 等等
clientmode      客户端，是否走客户端模式(建议走客户端方式)，和  servtype=Bean 配合使用
servbean        调用定义,支持宏替换
asynchronmode   异步，是否需要走异步调用，建议为异步处理，主要原则为整个调用过程中有一个环节为异步处理即可
openconfig		开放，OpenAPI 公共的服务调入，无需接入方配置
loged			日志，是否记录接入日志，建议需要调试时打开
maxthrdcount	TPS，最大TPS限制，每秒钟最大的接入量
threshold		接入阀值，用于控制单位时间内最大接入量，格式：  S10(每秒最大接入量为10)、M(每分钟)、H(每小时)、D(每天)
msgtype         消息类型，不建议定义，建议走默认消息
cmdid			权限命令号，针对接入服务分配权限的命令号，通过菜单功能“接入权限分配”按照岗位或用户分配权限
xsdpath			XSD文件路径，用于通过XSD文件验证报文格式
cmdid			接入权限命令号。只在接入时校验
validated		是否调用VO校验服务
indexfields		是否设置索引字段值
defaulted		是否设置VO默认值
remark          说明
```

|调用方式|调用定义说明|示例|备注|
|------|------|------|
|Bean|BeanId + . + methodName + () + ? + 参数列表|SN-ACS.AccessTestService.test1()?param1=apm|若继承Function接口可不指定方法名,参数将加载到AccessRunParam的parameters属性中|
|HTTP|URL|http://127.0.0.1:2020/N9BWS/ws/wsorder|Post方式，Data数据放在请求体发出|
|HTTPGET|URL + ? + 参数列表|http://127.0.0.1:2020/N9BWS/ws/wsorder?param1=apm|Get方式，拼接在Url中|
|WS|{"url":WSDL地址,"sv":调用方法名}|{"url":"{Callback_URL}/uiinvoke/ws-SN-HELP.SNWSOrderService","sv":"queryOrders"}|AccessRunParam 中的 Data属性视为被调用WS方法的参数列表|


#### 客户端配置
逻辑主键为：code + sender + receiver
```
stoped          停用，关闭与此接入服务
fromfile		文件，配置来自于XML文件
acsid			ID，数据库配置时生成的主键
acscode         接入编码，接入服务的编号，与“服务接入配置”中“接入编码”相同
acsname         接入名称，与“服务接入配置”中“接入名称”相同
sender			请求方，可以为空
receiver		响应方，可以为空
listeners		监听，配置监听，用于公共标准的接入服务情况下，请求和响应数据的特殊转换
convercodein    输入数据格式转换定义编码，对接底层数据格式转换功能
convercodeout   输出数据格式转换定义编码，对接底层数据格式转换功能
transtable		事务表名，是否启动事务，定义为启动事务的表名
validated       校验，是否通过VO上的注解校验
defaulted		是否设置VO默认值
reqclass        实体类，服务执行时接入数据转换的VO对象
dateformat		日期格式：默认：yyyy-MM-dd HH:mm:ss
contenttype		数据格式， JSON XML TEXT，建议使用 JSON 格式交互数据
indexfields		索引字段，将 AccessRunParam id 设置到VO的溯源字段上
remark          说明
```
### 加密验签
详情参见[接入系统加密验签](help.html?helpFile=help/SN-ACS/acs/acsSecurity.md)

### 系统选项
详见 ：AccessSysOptionsService
SN-CORE.Group.Name ：客户端必须配置，配置为使用ACS客户端的系统名称。接入系统可以配置所有使用的客户端，仅用于客户端的检查。
SN-ACS.Redis ： ACS使用的Redis数据源，不配置时使用底层默认Redis
SN-ACS.Mongo ： ACS使用的Mongo数据源，不配置时使用底层默认Mongo
SN-ACS.MQ.Kafka ：ACS对接的Kafka类型，多个逗号分割
SN-ACS.MQ.Rabbit ：ACS对接的RabbitMQ类型，多个逗号分割
SN-ACS.MQ.JMS ：ACS对接的JMS类型，多个逗号分割
SN-ACS.MQ.RocketMQ ：ACS对接的RocketMQ类型，多个逗号分割

### 与开源MQ的对接

#### 与Kafka对接
接入服务配置.调用方式=Kafka
生产者：AccessDataServiceImpl.proccessKafka() 
消费者：ACSConfig.kafkaMessageListenerContainer()

#### 与RabbitMQ对接
接入服务配置.调用方式=RabbitMQ
生产者：AccessDataServiceImpl.proccessRabbitMQ()
消费者：ACSConfig.rabbitMessageListenerContainer()

#### 与ActiveMQ对接
接入服务配置.调用方式=JMS
生产者：AccessDataServiceImpl.proccessJMS()
消费者：ACSConfig.jmsMessageListenerContainer()

#### 与RocketMQ对接
接入服务配置.调用方式=RocketMQ
生产者：AccessDataServiceImpl.proccessRocketMQ()
消费者：ACSConfig.rocketMQMessageListenerContainer()

#### 与IBMMQ对接
接入服务配置.调用方式=IBMMQ
生产者：AccessDataServiceImpl.proccessIBMMQ()
消费者：ACSConfig.ibmMQMessageListenerContainer()

### 接口参数

#### 接入地址

** 标准接入地址 **
请求地址格式：`http://.../access/发送方/接入编码` `http://.../access/发送方/接收方/接入编码`
如：http://127.0.0.1:1030/acs/access/SN-ACS/DEMO001

** UI标准接入地址 **
请求地址格式：`http://.../access/ui/发送方/接入编码`
如：http://127.0.0.1:1030/acs/access/ui/SN-ACS/DEMO001
表单:
	http://127.0.0.1:1030/acs/access/ui/form/SN-ACS/DEMO001
	或
	http://127.0.0.1:1030/acs/access/ui/SN-ACS/DEMO001?acsUIFlags=1
进度条：	
	http://127.0.0.1:1030/acs/access/ui/progress/SN-ACS/DEMO001
	或
	http://127.0.0.1:1030/acs/access/ui/SN-ACS/DEMO001?acsUIFlags=2


#### Form数据

>Form表单数据的获取方式与标准接入接口一致.可以通过AccessRunParam或自定义Bean获取

** 关于表单中的文件数据 **
```
1. 从AccessRunParam 中获取
	AcsFormFileData filedata = AccessRunParam.getData().get("表单附件名称");
2. 从自定义Bean对象中获取
	// 有@AcsFormFile注解时，自动将附件传到FTP上，并返回文件地址赋值到Bean属性
	@AcsFormFile(name="表单附件名称,默认字段名"，fsid="fs宏，默认：STADOCROOT"，path="子路径，默认无")
	private String				file;
	// 无@AcsFormFile注解时，将AcsFormFileData对象赋值到Bean属性
	private AcsFormFileData		image;

```

#### 进度条返回示例

```

服务层获取方式：
Progress runProgress = AppContext.getUserSession().getRunProgress();


输出格式示例：
	LEVEL列说明：
		 	 -1 开始
		 	 0 : title
		     4-5 : 文本信息 ( 5: showInformationDialog )
		     1-3 Progress
		     6: HTML 信息， 目前仅用于  snadk2.0
		     20-30 : 应用层自定义信息
		     256 : 远程方法调用 -- 废弃
		     257 : 通知客户端
		     999：结束
		     
	TYPE列说明：TXT | RETURNVAL | ANCHOR | HTML | JAVASCRIPT     
	 
	完整返回内容示例：
	{"PID":12,"MID":1,"LEVEL":-1},
	{"PID":12,"MID":2,"TYPE":"TXT","LEVEL":0,"VALUE":"进度条标题，对应方法：snsoft.commons.util.Progress.setProgressTitle(String, String)\t","PERCENT":0,"MAXPERCENT":0},
	{"PID":12,"MID":3,"TYPE":"TXT","LEVEL":1,"VALUE":"进度条进度及文字，对应方法： snsoft.commons.util.Progress.setProgress(String, int, int)","PERCENT":1,"MAXPERCENT":100},
	{"PID":12,"MID":4,"TYPE":"TXT","LEVEL":4,"VALUE":"文本框文字，对应方法： snsoft.commons.util.Progress.addProgressMessage(String)","PERCENT":0,"MAXPERCENT":0},
	{"PID":12,"MID":5,"TYPE":"TXT","LEVEL":4,"VALUE":"下一条进度为HTML，对应方法： snsoft.commons.util.Progress.addHTML(String)","PERCENT":0,"MAXPERCENT":0},
	{"PID":12,"MID":6,"TYPE":"HTML","LEVEL":6,"VALUE":"<span style='color:red'>测试文字</span>"},
	{"PID":12,"MID":7,"TYPE":"TXT","LEVEL":4,"VALUE":"下一条进度为输出A标签，对应方法：snsoft.commons.util.Progress.addHTMLAnchor(String, String, String)","PERCENT":0,"MAXPERCENT":0},
	{"PID":12,"MID":8,"TYPE":"ANCHOR","LEVEL":6,"VALUE":{"url":"http://127.0.0.1:1031/N9Bacs/access/progress/SN-ACS/DEMO001","title":"title test","target":"_blank"}},
	{"PID":12,"MID":9,"TYPE":"TXT","LEVEL":4,"VALUE":"下一条进度为输出JS代码，对应方法：snsoft.commons.util.Progress.jsCall(String)","PERCENT":0,"MAXPERCENT":0},
	{"PID":12,"MID":10,"TYPE":"JAVASCRIPT","VALUE":"function(){console.log('test jscall'))}"},
	{"PID":12,"MID":11,"TYPE":"TXT","LEVEL":4,"VALUE":"下一条进度设置返回值，对应方法：snsoft.commons.util.Progress.setReturnValue(Object)","PERCENT":0,"MAXPERCENT":0},
	{"PID":12,"MID":12,"TYPE":"RETURNVAL","VALUE":{"int_k":1,"Array_k":["1111","22222"],"Object_k":{"code":"111","info":null,"data":"333","uiCuicode":null,"uiUsercode":null},"str_k":"xxxxxxxxx","double_k":12.12,"Date_k":1590988735833}},
	{"PID":12,"MID":18,"LEVEL":999}

```


### 类图

@startuml
Title 接入系统类图

AccessConfigService ..> AccessSender
AccessConfigService ..> AccessConfig

AccessController ..>  AccessRequest
AccessController ..>  AccessResponse

AccessController --* AccessConfigService
AccessController --* Resource

class AccessSender {
} 
class AccessConfig {
} 
class AccessConfigService {
	+ AccessConfig getAccessConfig(String id);
	+ AccessSender getAccessSender(String sender);
}
class AccessRequest {
	# AccessRequestHead head;
	# String data;
}
class UIResponse {
	# int result;
	# String message;
	# String ticket;
	# Object response;
} 
class AccessController {
}
class Resource {
	+ ObjectMapper objectMapper;
	+ Jaxb2Marshaller jaxb2Marshaller;
	+ ValueOperations valueOperations;
	+ LoginSessionStore sessionStore;
	+ ValidateService validateService;
	+ MapperService mapperService;
	+ DefaultValueService defaultValueService;
}
@enduml

### DEMO示例

#### Step1引入工程 
接入DEMO工程及相关启动工程
- acs-config 
- acs-run-service ：接入SV服务 
- acs-run-ui ：接入UI服务
- snadk-acs-demo-api：接入测试服务
- snadk-acs-demo-sv：接入测试服务
- snadk-acs-demo-ui：接入测试服务

#### Step2配置启动项
**Run-Service**
```
VM arguments:
-DSN.ID=ACS-SERVICE
-Ddubbo.protocol.port=20191
-DSN.ConfigPath=D:\snsoft90\snadk_product\product-acs\acs-demo-ui\snconfig-acs
-Dsn.test=true
```
**Run-UI**
```
VM arguments:
-DSN.ID=ACS-UI
-Ddubbo.protocol.port=20192
-DSN.ConfigPath=D:\snsoft90\snadk_product\product-acs\acs-demo-ui\snconfig-acs
-Dsn.test=true
```
**配置说明**
若不想使用demo中的snconfig，可以将对应的接入配置拷贝到自己的snconfig内	

#### Step3开始调试
- 接入调试
接口测试工具:[PostMan](https://www.getpostman.com/downloads/)
或
菜单位置：接入系统-> 接入配置-> 调试工具-> [HTTP-客户端调用](uiinvoke/00/zh_CN/theme0/90.Http.HttpClientExcute.html)
- 接出调试
菜单位置：接入系统-> 接入配置-> 调试工具-> [签名/密文/接出工具](uiinvoke/00/zh_CN/theme0/SN-ACS.SignEncryptTool.html)

### 遗留问题

* 对接 Sentinel 分布式系统的流量防卫兵
* HTTPS的支持




