## 单据操作日志

|日期|作者|备注|
|------|------|------|
|2020-5-12|张东|初稿|


### 指南
#### 概念说明

平台提供单据操作日志记录功能,支持记录撤单、驳回等单据操作自定义日志内容记录;

**本功能必须启用mongodb**

#### 使用说明

##### 界面引用

```xml
<include src="SN-PLAT.SheetOpLogPubRes#plat_shtoplog" mainui="xxx" />
```

##### 系统功能配置

* 客户端

```xml
<Functions>
    <Functype>SN-PLAT.JS</Functype>
    <Subtype>2</Subtype>
    <Funcimpl>#new snsoft.plat.bas.oplog.SheetOpLogQueryJSListener({tgtUINames:["plat_shtoplog"],sheetCode:"XXX"})</Funcimpl>
    <Remark>单据操作日志查询</Remark>
</Functions>
```

* 服务端

```xml
<Groups id="单操作日志">
    <Functions>
        <Functype>SN-PLAT.SV</Functype>
        <Subtype>1</Subtype>
        <Funcimpl><![CDATA[
        SN-PLAT.SheetOpLogStatusListener?['${sheetCode}']
        ]]></Funcimpl>
        <Remark>撤单操作日志存盘监听</Remark>
    </Functions>
    <Functions>
        <Functype>SN-PLAT.WF</Functype>
        <Subtype>1</Subtype>
        <Funcimpl><![CDATA[
        SN-APPR.SheetOpLogApprMsgListener?['${sheetCode}']
        ]]></Funcimpl>
        <Remark>单据操作日志审批消息监听</Remark>
    </Functions>
</Groups>
```

##### Tac内容编辑

* TAC内置方法
```properties
getCodeName(codetbl,code)：码名映射。
getCodeNameByCuicode(codetbl,code,cuicode)：根据商户码名映射。
#参数说明
codetbl：码表id
code：码
cuicode：商户编码
```

* TAC参数变量
```properties
refData：为当前业务数据VO。
```

示例：
```properties
str ="客户编号：" + refData.getCcode()
str = str + "\n"
str = str + "自定义。。。。。"
str = str + "币种：" + getCodeName(refData.getFcode())
return str
```

##### 单据接口日志配置
【[配置界面](uiinvoke/00/zh_CN/theme0/SN-PLAT.SheetOpLogDef.html)】


### 设计

## 单据版本日志

### 指南

单据版本日志：以VO主表为主体记录了该单据的所有增删改操作；
菜单【[单据版本日志](uiinvoke/DEV_ORCL/zh_CN/theme0/90.Logger.VsnHistory.html)】：以时间为维度查询所有的修改日志；
菜单【[记录版本日志](uiinvoke/DEV_ORCL/zh_CN/theme0/90.Logger.VsnRecord.html)】：以修改单据为维度查询指定记录的修改日志。

### 手册

1. 在DefaultDAO中进行实现；
2. 字段含义：
	* op：
		+：新增行
		-：删除行
		#：修改行
		*：无操作行
	* opcols：修改的列
3. 记录的信息：
	* 单据服务实现了AbstractFunctionService的单据，记录全量VO；
	* 其他需要调用【VOUtils.processUnion(List<VO>)】方法做全量记录，否则仅做增量记录；

### 设计

* 单据号：SN-CMC.VsnLog
* 类图

@startuml
Title 单据版本日志类图

VsnLogService <-- VsnLogServiceImpl

VsnLogUIService <-- VsnLogUIServiceImpl

VsnLogService <.left. VsnLogUIServiceImpl

interface VsnLogService {
	+ void append(VOClassInfo info, Collection<VO> mains);// 追加版本日志
	+ QueryResults<VsnLog> queryHistory(HistoryQueryParam params);// 以时间为维度查询所有的修改日志
	+ QueryResults<VsnLog> queryRecord(RecordQueryParam params);// 以修改单据为维度查询指定记录的修改日志
}

class VsnLogServiceImpl {
}

class VsnLog {
	+ String tblname;
	+ String innercodes;
	+ int    vsn;
	+ Map    data;
	+ String modifier;
	+ Date   modifydate;
}

interface VsnLogUIService {
	+ QueryResults<VsnLog> queryHistoryUI(HistoryQueryParam params);
	+ QueryResults<VsnLog> queryRecordUI(RecordQueryParam params);
}

class VsnLogUIServiceImpl {
}

@enduml