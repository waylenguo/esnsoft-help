## 单据接口日志

|日期|作者|备注|
|------|------|------|
|2019-10-17|郭鑫|初稿|


### 指南
#### 概念说明

平台提供单据接口日志记录功能,支持单据状态变更记录其对应操作日志;

#### 使用说明

##### 系统功能配置

* 服务端

```xml
<Groups id="单据接口日志">
    <Functions>
        <Functype>SN-PLAT.SV</Functype>
        <Subtype>2</Subtype>
        <Funcimpl><![CDATA[
        snsoft.plat.bas.log.listener.PortlogStatusListener.new?sheetcode=单据号&tblName=存储表名
        ]]></Funcimpl>
        <Remark>单据接口日志</Remark>
    </Functions>
</Groups>
```

* UI端

```xml
<Groups id="单据接口日志">
    <Functions>
        <Functype>SN-PLAT.JS</Functype>
        <Subtype>2</Subtype>
        <Funcimpl>
            <![CDATA[
            #new snsoft.plat.bas.log.PortlogJSListener({tgtUINames:['主界面'],sheetcode:'单据号',logTblName:'存储表名'})
            ]]>
        </Funcimpl>
        <Remark>单据接口日志</Remark>
    </Functions>
</Groups>
```

##### 界面嵌入配置

```xml
<B title="接口日志">
    <include src="SN-PLAT.Log.Portlog#plat_portlog" mainui="主界面"/>
</B>
```

##### 单据接口日志配置
【[配置界面](uiinvoke/00/zh_CN/theme0/SN-PLAT.Log.PortlogDef.html)】


### 实践

配置参考:

* 主表

|参数|配置内容|
|------|------|
|单据号|FT-TRD.LStock.Cord|

* 子表

|参数|配置内容|
|------|------|
|原状态|60|
|目标状态|61|
|服务名称|南北调用|
|接口调用方向|报文发送|
|接口调用结果|南北数据发送成功|

### 设计

#### 表结构定义

* 主表

|字段名|字段标题|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|pdcode|（编码）主键|VARCHAR|SZIBILL|是|是||||
|sheetcode|单据号|VARCHAR|SZSHEET||是||||
|remark|说明|VARCHAR|SZTEXT||||||

* 子表

|字段名|字段标题|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|pdgcode|（编码）主键|VARCHAR|SZIBILL|是|是||||
|pdcode|（编码）外键|VARCHAR|SZIBILL|是|是||||
|fromstatus|原状态|VARCHAR|SZSTATUS||是||||
|tostatus|目标状态|VARCHAR|SZSTATUS||是||||
|svname|服务名称|VARCHAR|128||||||
|portdirection|接口调用方向|VARCHAR|128||||||
|portresault|接口调用结果|VARCHAR|SZTEXT||||||

* 单据接口日志记录表

|字段名|字段标题|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|plicode|（编码）主键|VARCHAR|SZIBILL|是|是||||
|pdgcode|（编码）外键|VARCHAR|SZIBILL|是|是||||
|cuicode|商户编码|VARCHAR|SZCUICODE||是||||
|innercode|单据内码|VARCHAR|SZIBILL||是||||
|sheetcode|单据号|VARCHAR|SZSHEET||是||||
|portnum|接口调用类型|Int|||||||
|porttime|接口调用时间|DATE|||||||
|portresault|接口调用结果|VARCHAR|SZTEXT||||||

#### 接口设计

* 【接口】

|包名类名|名称|主要功能|
|------|------|------|
|snsoft.plat.bas.log.service.PortlogdefUIService| 单据接口日志定义UI服务接口   | 权限 |
|snsoft.plat.bas.log.service.PortlogdefService|单据接口日志定义服务接口|服务提供|
|snsoft.plat.bas.log.listener.PortlogStatusListener|单据状态改变处理监听|监听状态与记录日志|
|snsoft.plat.bas.log.service.PortlogUIService|单据接口日志UI服务|提供单据接口日志查询服务|
|snsoft.plat.bas.log.service.PortlogService|单据接口日志服务|提供单据接口日志查询、存盘服务|


|接口名称|方法名|输入说明|输出说明|
|------|------|------|------|
|PortlogdefUIService|queryUI|列表分页数据|单据接口日志定义查询|
|PortlogdefUIService|queryGUI|列表分页数据|单据接口日志定义查询|
|PortlogdefUIService|saveUI|列表分页数据|单据接口日志定义存盘|
|PortlogdefService|query|列表分页数据|单据接口日志定义查询|
|PortlogdefService|queryG|列表分页数据|单据接口日志定义查询|
|PortlogdefService|save|列表分页数据|单据接口日志定义存盘|
|PortlogUIService|queryPortlogUI|列表数据|单据接口日志记录查询|
|PortlogService|queryPortlog|列表数据|单据接口日志记录查询|
|PortlogService|savePortlog|单据接口日志记录|单据接口日志记录存盘|
|PortlogStatusListener|afterChange|状态改变到60状态的数据|无|

#### 类调用关系

【接口日志定义类图】
@startuml

	PortlogdefUIService ..> PortlogdefService

interface PortlogdefUIService {

	QueryResults<Portlogdef> queryUI(PortlogdefParams params);

	QueryResults<Portlogdefg> queryUI(PortlogdefDetailParams params);

	SaveResults saveUI(SaveParams<Portlogdef> params);
}

interface PortlogdefService {

	QueryResults<Portlogdef> query(PortlogdefParams params);

	QueryResults<Portlogdefg> query(PortlogdefDetailParams params);

	SaveResults save(SaveParams<Portlogdef> params);
}
@enduml


【接口日志记录类图】
@startuml

	PortlogStatusListener ..> PortlogFactory

	PortlogStatusListener ..> PortlogService

	PortlogUIService ..> PortlogService


class PortlogStatusListener {

	public void afterChange(FunctionSaveShareEvent<V> se, SaveParams<V> params, V record, String oldStatus, String newStatus);
}

interface PortlogFactory {

	PortlogdefCache getPortlogDef(String sheetcode);

}

interface PortlogService {

	QueryResults<Portlog> queryPortlog(PortlogParams params);

	void savePortlog(Portlog vo);
}



interface PortlogUIService {

	QueryResults<Portlog> queryPortlogUI(PortlogParams params);
}



@enduml

#### 核心算法

PortlogStatusListener#afterChange
单据接口日志监听监听业务数据值改变，根据单据接口日志定义判断是否需要记录日志，及记录日志内容。