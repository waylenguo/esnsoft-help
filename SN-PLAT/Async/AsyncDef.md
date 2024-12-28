## 单据异步处理

|日期|作者|备注|
|------|------|------|
|2019-06-26|赵玉柱|初稿|
|2019-10-26|吴小海|重构|


### 指南	
#### 概念说明

平台提供单据异步处理功能,支持单据状态变更异步推送数据到第三方系统;

#### 使用说明
		
##### 系统功能配置
系统功能SV
```xml
<Groups id="提交发送异步消息">
    <Functions>
        <Functype>SN-PLAT.SV</Functype>
        <Subtype>1</Subtype>
        <Funcimpl><![CDATA[
        SN-PLAT.AsyncStatusListener?[{}]
        ]]></Funcimpl>
        <Remark>提交发送异步消息</Remark>
    </Functions>
</Groups>
```

系统功能UI
```xml
<Groups id="提交发送异步消息">
    <Functions>
        <Functype>SN-PLAT.SV</Functype>
        <Subtype>1</Subtype>
        <Funcimpl><![CDATA[
        SN-PLAT.AsyncStatusListener?[{}]
        ]]></Funcimpl>
        <Remark>提交发送异步消息</Remark>
    </Functions>
</Groups>
```


系统功能UI
```xml
<Groups id="异步失败后的处理">
    <Functions>
        <Functype>SN-PLAT.JS</Functype>
        <Subtype>2</Subtype>
        <Funcimpl>#new snsoft.plat.bas.async.listener.AsyncStatusListener({})</Funcimpl>
        <Remark>主表按钮-状态59-12的数据处理</Remark>
    </Functions>
</Groups>

```

##### 异步处理配置
【[配置界面](uiinvoke/00/zh_CN/theme0/SN-PLAT.Async.AsyncDef.html)】


### 实践	

配置参考:

|参数|配置内容|
|------|------|
|单据号|FT-TRD.LStock.Cord|
|TAC|底部文本TAC|
|消息创建Bean|CEC-TRD.ColligateOrderAsyncRetractSendDataService|


TAC配置:
```text
proc isCalc(record)
    result = "70".equals(record.getStatus())
    return result
end proc
//请求方
proc getRequester(record)
	return "FT-TRD"
end proc
//响应方
proc getResponder(record)
	return "SN-MQ"
end proc
//消息类型
proc getMessagetype(record)
    return "DEFRETRACTINVOKE"
end proc
//接入发送方
proc getSender(record)
	return "FT-TRD"
end proc
//接入接收方
proc getReceiver(record)
	return "CEC-TMS"
end proc
//接入编号
proc getAcscode(record)
	return "FT203"
end proc
//操作用户
proc getUcode(record)
	return record.getVprepare()
end proc
```





	
### 设计

#### 表结构定义

|字段名|字段标题|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|asdcode|（编码）主键|VARCHAR|SZIBILL|是|是||||
|sheetcode|单据号|VARCHAR|SZSHEET||是||||
|asynctac|条件tac|VARCHAR|SZTAC||是||||
|dispatchbeanid|消息创建beanId|VARCHAR|SZBEANNAME||||||
|interactivebeanid|消息处理BeanId|VARCHAR|SZBEANNAME||||||
|remark|说明|VARCHAR|SZTEXT||||||


#### 接口设计

* 【接口】

|包名类名|名称|主要功能|
|------|------|------|
|snsoft.plat.bas.async.service.AsyncDefUIService| 消息异步处理UI服务   | 权限 |
|snsoft.plat.bas.async.service.AsyncDefService|消息异步处理服务|服务提供|
|snsoft.plat.bas.async.service.impl.AsyncStatusListener|单据状态改变异步处理监听|监听状态与发送消息|
|snsoft.api.async.service.AsyncSendMessageService|消息发送接口|提供发送消息服务|


|接口名称|方法名|输入说明|输出说明|
|------|------|------|------|
|AsyncDefUIService|queryAsyncDefUI|消息异步定义查询|列表分页数据|
|AsyncDefUIService|saveAsyncDefUI|列表分页数据|消息异步定义存盘|
|AsyncDefService|queryAsyncDefUI|消息异步定义查询|列表分页数据|
|AsyncDefService|saveAsyncDefUI|列表分页数据|消息异步定义存盘|
|AsyncDefService|getAsyncDef|单据号|单据对应异步处理定义|
|AsyncStatusListener|afterChange|状态改变到60状态的数据|无|
|AsyncSendMessageService|sendMessage|消息数据|消息创建结果|

#### 类调用关系

【类图】
@startuml

	AsyncDefUIService ..> AsyncDefService
	
	AsyncStatusListener ..> AsyncDefService
	
	AsyncStatusListener ..> AsyncSendMessageService
	
interface AsyncDefUIService {

	QueryResults<AsyncDef> queryAsyncDefUI(AsyncDefParams params);
	
	void saveAsyncDefUI(SaveParams<AsyncDef> params);
}

interface AsyncDefService {

	QueryResults<AsyncDef> queryAsyncDefUI(AsyncDefParams params);

	void saveAsyncDefUI(SaveParams<AsyncDef> params);

	public List<AsyncDef> getAsyncDef(String sheetCode);
}
interface AsyncSendMessageService {

	public UIResponse<Object> sendMessage(AsyncSendMessage message);
}


class AsyncStatusListener {

	public void afterChange(FunctionSaveShareEvent<V> se, SaveParams<V> params, V record, String oldStatus, String newStatus);
	private AsyncSendMessage getAsyncSendMessage(AsyncTac tac, V record);
	private AsyncTac searchAsyncTac(V record);
}
@enduml

#### 核心算法

AsyncStatusListener#afterChange
> 1. 判断原数据状态小于60状态，并且当前数据要提交到60状态

> 2. 判断当前数据是否需要发送消息

>>2.1 若当前数据需要发送消息，则执行创建消息操作，并返回创建消息的结果

>>>2.1.1 若创建消息成功  则将数据更新到61 消息已创建成功状态

>>>2.1.2 若创建消息失败 则将数据更新到59 消息创建失败状态

>>>2.2.3 执行对应消息，将数据发送到第三方系统，并等待第三方系统的同步或异步回执

>>>2.1.4 若第三方系统同步回执处理成功  则将数据更新到 70 状态

>>>2.1.5 若第三方系统同步回执成功 则不处理

>>>2.1.6 若第三方系统同步回执失败 则将数据更新到59 消息发送失败状态

>>>2.1.7 若第三方系统异步回执处理成功 则将数据更新到70 状态

>>>2.1.8 若第三方系统异步回执处理失败 则将数据更新到59状态 处理失败

>>>2.1.9 若第三方系统异步回执处理失败，并回执为可修改，则将数据更新到12 可修改 状态  期待修改后进行后续提交

>>2.2 若当前数据不需要创建消息 则直接将数据更新到70状态