## 审批实施手册

|日期|作者|备注|
|------|------|------|
|2022-09-05|冀小雷|初版|

### 审批链

1. 场景：
	ERP中业务单据新建时为【草拟】状态，不可用于流转，此时可以进行删改；
	确定不再修改(一般为核心数据不在修改)时将单据设置为【生效】状态，可以参与业务的流转；
	如果业务单据从【草拟】到【生效】不能由制单人确定，需要由相关职能部门/岗位的人员进行审批，则需要借助审批链配置。
1. 概念：企业根据本司制度规范，对【草拟】状态的业务单据进行审批的过程配置称之为审批链。
1. 操作：
	1. 提交：将审批数据从当前节点流转到下一个节点
		* 单据详情内首次提交：选择待审人；
		* 中间节点审批后提交：选择待审人或提交生效
		* 被驳回单据再次提交：选择待审人；
	1. 审批：当前只能部门/岗位的待审人(被授权人)履行职责对单据进行审核
		* 同意：如果为当前级次最后一个审批人，则需要提交到下一个节点；
		* 驳回：可以驳回至起始节点，也可以驳回至已经审批通过的中间节点；
		* 转审：转给其他人审核；
		* 加审：增加当前级次的审批人；
	1. 取消提交：撤回已经流转到下一个节点
		* 如果下一个节点已经审批则无法撤回；
		* 【禁止反悔】：禁止取消提交，禁止取消驳回（不包括取消审批）；
		* 首次提交撤回；
		* 被驳回至草拟状态提交后撤回；
		* 被驳回至中间节点提交后撤回；
		* 审批并提交后撤回；
		* 取消驳回；
	1. 取消审批：已经审批同意的节点取消审批同意
		* 如果当前级次已经完成并提交到下一个节点则无法取消；
		* 取消审批后的节点需要再次审批；
1. 未启用/启用/停用：
	1. 概念：
		* 未启用的审批链可以随意删改，不参与业务审批流程(<b>取消启用后删改可能导致已有的审批数据异常</b>)；
		* 启用的审批链：供审批程序使用 <b>且 参与</b>单据审批关系的匹配；
		* 停用的审批链：供审批程序使用 <b>但 不参与</b>单据审批关系的匹配；
	1. 建议：启用后已经参与审批且存在审批数据的审批链不建议取消启用后再次修改
		* 存在bug 或 需求调整必须取消启用并修改：
			* <b>禁止删除审批链</b>
			* <b>禁止删除已经使用的节点</b>
			* 可以增加节点
			* 可以增改待审人
			* 其他非禁止的操作
		* 公司审批流程发生变动时可以停用原审批链并新增审批链对应到业务单据上(建议同时禁用原单据审批关系)，用以确保：
			* 已经完成的审批任务可以正常展示
			* 未完成的审批任务能够继续审批
		 

### 审批关键字

在【审批链定义-公式】【审批操作-条件表达式】中通过[$keyword]可以直接引用的keyword称之为关键字。

1. 默认关键字：
	* outercode(string)：单据外码
	* vsn(integer)：版本号(前提：当前单据支持版本备份)
1. [审批描述](APPR.md#审批描述)
	* 关键数据字段(向上兼容)
	* 关键数据公式(向上兼容)
1. 审批链定义-关键数据公式
<pre>
	proc kf(kvMap,main)
		// kvMap={} 关键字缓存
		// main=vo  主表VO对象
		// 仅填写Tac代码块即可
		put("kw","审批链定义")
	end proc
</pre>
1. 单据审批关系-关键数据公式
	同【审批链定义-关键数据公式】

* 优先级(三者关系为互斥)：单据审批关系>审批链定义>审批描述；
* 默认首次提交时生成，也可以通过审批单元进行配置多次生成；

### 审批描述

为了方便审批人快速了解对应业务单据的概要信息，在审批工作台需要显示业务单据的部分信息，通过审批描述进行配置。
默认首次提交时生成，也可以通过审批单元进行配置多次生成；
详情参见：[审批描述](APPR.md#审批描述)

### 单据审批关系

单据关系：
1. 根据单据业务员对应的部门、岗位、用户进行匹配；
2. 根据部门匹配时，级次较深的优先级更高；
3. Tac匹配条件优先级最高；
4. 开发/SIT环境(sn.test=true)找不到审批链时默认生成一条自己审批的审批链；
详情参见：[单据审批关系](APPR.md#单据审批关系)

### 异步审批[MQ]

1. 需要依赖SN-MQ系统；
	```
	#System.properties配置
	SN-MQ.Comm.mongo=true
	SN-MQ.Comm.mongo.uri=mongodb://192.168.1.135:27017/dev_help_mq

	SN-MQ.Comm.redis=true
	SN-MQ.Comm.redis.host=192.168.1.135
	SN-MQ.Comm.redis.port=6379
	```
1. 需要在应用系统增加对应的MQ配置
	* 路径：cfg/mq/message/[sysid].xml；
	* 可以参考[帮助系统示例](uiinvoke/00/zh_CN/theme0/SN-MQ.MessageConf.html?InitValue.messagetype=SN-APPR&AutoRefresh=1)；
	* <b>MQ需要启用[定时任务](uiinvoke/00/zh_CN/theme0/90.Tool.TimerTask.html?InitValue.taskid=SN-MQ-001&InitValue.flags=0&AutoRefresh=1)</b>；
	* <b>MQ消息查看[消息通知管理](uiinvoke/00/zh_CN/theme0/SN-MQ.Message.html?InitValue.messagetype=SN-APPR&AutoRefresh=1)</b>；
1. 审批**[单元类型=终点]**节点的业务状态设置为**【审批状态=69(待生效)】**；
1. 功能类图如下：
@startuml

AsyncSendMessageService <.. AsyncUtils:上行
AsyncUtils <.. DefaultFunctionService:上行

BusiAccessService <.. WFMQCallBack:异步回调

class DefaultFunctionService {
	# void modifyStatus();
}

class AsyncUtils {
	+ UIResponse<T> sendMessage();
}

interface AsyncSendMessageService {
	+ UIResponse<T> actionMessage();
}

class WFMQCallBack {
	+ void callBack(WFMQParams params);
}

interface BusiAccessService {
	void keep(String innercode);
}
@enduml

### 审批消息[UMC]

1. 需要依赖SN-UMC系统
<pre>
	#System.properties配置
	SN-UMC.Comm.mongo=true
	SN-UMC.Comm.mongo.uri=mongodb://192.168.1.135:27017/dev_help_umc
	#Redis消息通知
	SN-UMC.Comm.redis=true
	SN-UMC.Comm.redis.host=192.168.1.135
	SN-UMC.Comm.redis.port=6379
	#WebSocket内部消息通知
	snsoft.websocket=true
	snsoft.websocket.host=ws://127.0.0.1:30500/cloud/websocket
</pre>
1. 默认消息模板配置[UMCMessageTypeConfSN-APPR.xml]，可以指定自己的配置：
	* 路径：cfg/umc/message/UMCMessageTypeConf[sysid].xml；
	* 参见[审批UMC消息模板](uiinvoke/00/zh_CN/theme0/SN-UMC.MessageTypeConf.html?InitValue.message_type_id=SN-APPR.%25&AutoRefresh=1)；
	* UMC需要启用[定时任务](uiinvoke/00/zh_CN/theme0/90.Tool.TimerTask.html?InitValue.taskid=SN-UMC.001&InitValue.flags=0&AutoRefresh=1)</b>；
1. 在审批节点中配置该节点启用的消息类型，消息分为四种：
	* 1=站内信：需要启用WebSocket，参见[WebSocket文档](../../SN-CMC/Websocket/Websocket.md)；
	* 2=邮件：需要配置SMTP协议，参见[消息供应商配置](uiinvoke/00/zh_CN/theme0/SN-UMC.MessageSysConf.html)；
	* 4=短信：需要配置短信引擎，参见[消息供应商配置](uiinvoke/00/zh_CN/theme0/SN-UMC.MessageSysConf.html)；
	* 8=微信：需要配置微信通道，参见[消息供应商配置](uiinvoke/00/zh_CN/theme0/SN-UMC.MessageSysConf.html)；
1. 审批发送的消息有四种（可以通过Tac预留接口进行修改消息发送的配置[预定义Tac](uiinvoke/00/zh_CN/theme0/90.prt.DefTac.html)）：
	* 提交待审消息，默认发送给待审人；
	* 提交生效消息，默认发送给提交人、创建人、单据属主；
	* 驳回至草拟消息，默认发送给提交人、创建人、单据属主；
	* 驳回至中间级消息，默认发送给该节点所有待审人、实际已经审核人；
<pre>
	// 四种消息对应四个Tac接口（amo对象的说明参见类图ApprMessageObject）
	// 提交待审消息
	proc messageForSubmit(amo)
	end proc
	// 提交生效消息
	proc messageForKeep(amo)
	end proc
	// 驳回至草拟消息
	proc messageForRejectToDraft(amo)
	end proc
	// 驳回至中间级消息
	proc messageForRejectToMiddle(amo)
	end proc
</pre>
1. 消息发送Tac（可以通过【[预定义Tac](uiinvoke/00/zh_CN/theme0/90.prt.DefTac.html?InitValue.sysid=SN-APPR&InitValue.defid=WFMSGSender&AutoRefresh=1)】功能进行重定义[sysid=SN-APPR,defid=WFMSGSender]）
<pre>
	// 默认通过UMC进行发送
	// 可以通过重写此Tac修改消息发送机制
	sysmacro = null	// 替换宏，Map类型
	umctype = null	// 消息模板ID，String类型
	toucodes = null	// 消息接收人，String[]类型
	service = getBean("SN-UMC.UMCMessageService")
	message = new snsoft.umc.api.message.entity.UMCUsercodeParamMessage()
	message.setSysmacro(sysmacro)
	list= new java.util.ArrayList()
	receinfo = new snsoft.umc.api.message.entity.UMCReceiveInfo()
	ulist = java.util.Arrays.asList(toucodes)
	receinfo.setCodeList(ulist)
	list.add(receinfo)
	message.setReceiveInfos(list)
	message.setMessage_type_id(umctype)
	service.sendUsercodeParamMessage(message)
</pre>
1. 功能类图
@startuml

Workflowu <--o FunctionApprEvent
TaskPerformRequest <--o FunctionApprEvent
FunctionApprEvent <--o ApprMessageObject

ApprMessageObject <.left. ApprMessageListener
UMCMessageService <.up. ApprMessageListener

class ApprMessageListener {
	+ void performSubmit(FunctionApprEvent<V> event, String innerCode);
	+ void performReject(FunctionApprEvent<V> event, String innerCode);
	# void invokeAMO(ApprMessageObject<V> amo, String procName);
	# void invokeSend(String umctype, Map<String,Object> sysmacro, String[] toucodes);
	# void sendMessaage(ApprMessageObject<V> amo);
	# void messageForSubmit(ApprMessageObject<V> amo);
	# void messageForKeep(ApprMessageObject<V> amo);
	# void messageForRejectToMiddle(ApprMessageObject<V> amo);
	# void messageForRejectToDraft(ApprMessageObject<V> amo);
}

interface UMCMessageService {
	+ boolean sendUsercodeParamMessage(UMCUsercodeParamMessage msg);
}

class ApprMessageObject<V> {
	- FunctionApprEvent<V> event;
	- String innerCode;
	- String sheetCode;
	- String sysid;
	- int msgtypes;
	- String umctype;
	- Map<String,Object> sysmacro;

	+ void addUcode(String ucode);
	+ void removeUcode(String ucode);
	+ String[] getToucodes();

	+ void setMsgtypes(int msgtypes);
	+ void addMsgtype(int type);
	+ void removeMsgtype(int type);

	+ ApprSheetVO getSheetVO();
	+ ApprDataVO getApprVO();
	+ ApprDataVO getLastApprVO();

	+ void addMacro(String key, Object value);
	+ Map<String,Object> getMacro();
}

class FunctionApprEvent<V> {
	# Workflowu fm;
	# Workflowu to;
	# TaskPerformRequest performRequest;
	+ V getRecord(String innercode);
}

class Workflowu {
	+ String getWfcode();
	+ String getWfuid();
	+ String getUname();
	+ Integer getRmode();
	+ String getUtype();
	+ String getStatus();
	+ int getMsgoptions();
}

class TaskPerformRequest {
	+ String wfcode;
	+ String taskid;
	+ String performexpl;
	+ int performstat;
	+ int rejectToIdx;
}
@enduml
