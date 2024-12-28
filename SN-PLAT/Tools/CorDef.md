## 核销配置
作者：冀小雷

### 核销及配置说明

1. 此处核销是指用于记录原始数值、已经被目标使用的数值、剩余数值的逻辑关系的处理过程：
	1. 原始数值：通过接口由应用层开发提供；
	1. 使用数值：通过接口由应用开发提供；
	1. 剩余数值：由核销服务进行处理；
1. 核销配置请参见对应的Schema文件【[CorDef.xsd](do/BrowseSchema?schema=CorDef.xsd)】；
1. 配置文件路径：res/cor/*.xml；
1. 核销配置参见示例[销售合同-单据核销配置](uiinvoke/00/zh_CN/theme0/SN-PLAT.CorDef.html?InitValue.corcode=SN-HELP.Salorder&AutoRefresh=1)；

### 监听配置

```xml
<Functions grp="数据核销：原始记录">
	<Functype>SN-PLAT.SV</Functype>
	<Subtype>2</Subtype>
	<Funcimpl><![CDATA[
	snsoft.plat.bas.cor.service.impl.CorSourceSaveListener.new?corcodes=code1,..,codeN
	]]></Funcimpl>
	<Remark>生效后生成核销余额数据</Remark>
</Functions>

<Functions grp="数据核销：目标记录">
	<Functype>SN-PLAT.SV</Functype>
	<Subtype>2</Subtype>
	<Funcimpl><![CDATA[
	snsoft.plat.bas.cor.service.impl.CorTargetSaveListener.new?corcodes=code1,..,codeN
	]]></Funcimpl>
	<Remark>存盘后处理核销追踪记录及余额记录</Remark>
</Functions>


<Functions>
	<Functype>SN-PLAT.JS</Functype>
	<Subtype>2</Subtype>
	<Funcimpl><![CDATA[
	#new snsoft.plat.bas.cor.CorTargetCheckJSListener({tgtUINames:["salshipg"],corcode:'核销编码',corrfld:'核销关系列',corfld:'核销值列'})
	]]></Funcimpl>
	<Remark>核销目标值改变前校验</Remark>
</Functions>
```

### XML配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<CorDef xmlns="http://snsoft.cn/schema/CorDef.xsd">
	<Name></Name>
	<SourceBean></SourceBean>
	<BalanceClass></BalanceClass>
	<TraceClass></TraceClass>
	<SourceTable></SourceTable>
	<CorRelaField></CorRelaField>
	<CorFields></CorFields>
	<Flags></Flags>
	<Remark><![CDATA[
	]]></Remark>
	<Targets>
		<TargetTable></TargetTable>
		<TargetBean></TargetBean>
		<Remark></Remark>
	</Targets>
</CorDef>
```

### 类图

@startuml
Title 数据核销定义类图

CorDef *--> CorTarget
CorDef <.. CorDefService
CorTarget <.. CorDefService

interface CorDefService {
	+ List<CorDef> queryUI(CorDefQueryParams params);
	+ void saveUI(List<CorDef> records);
	+ List<CorTarget> queryTargetsUI(CorTargetQueryParams params);
	+ void syncToDB(String corcode);
}

class CorDef {
	# String  sourcebean;
	# String  balclass;
	# String  trcclass;
	# String  srctbl;
	# String  correlafld;
	# String  corfields;
	# Integer flags;
	# List<CorTarget> targets;
}

class CorTarget {
	# String tgttbl;
	# String targetbean;
}
@enduml

@startuml
Title 数据核销逻辑类图


CorFactory .> CorClient

CorClient .up.> SourceEntity
CorClient .up.> TargetEntity

CorClient ..> CorSourceService

CorSourceService <.down.> CorService
CorSourceService ..> CorBalanceRecord
CorSourceService ..> CorTraceRecord

CorService ..> CorTargetService

CorBalanceRecord --|> CorRecord
CorTraceRecord --|> CorRecord

interface CorFactory {
	// 核销工厂：用于创建对应的核销服务
	+ CorDef getCorDef(String corcode);
	+ CorService<S,T> newCorService(String corcode);
	+ CorClient<S,T> newCorClient(String corcode);
}

interface CorTargetService {
	// 加载目标数据服务
	+ List<T> loadTarget(Collection<String> tgticodes);
}

interface CorService {
	// 核销服务
	void createSource(Collection<String> xcodes);
	void modifySource(Collection<String> xcodes);
	void deleteSource(Collection<String> xcodes);
	
	void appendTarget(Collection<String> tgticodes, String tgttbl);
	void modifyTarget(Collection<String> tgticodes, String tgttbl);
	void deleteTarget(Collection<String> tgticodes, String tgttbl);
}

class SourceEntity {
	- Collection<String> createXcodes;
	- Collection<String> modifyXcodes;
	- Collection<String> deleteXcodes;
}

class TargetEntity {
	- String tgttbl;
	- Collection<String> appendTgticodes;
	- Collection<String> modifyTgticodes;
	- Collection<String> deleteTgticodes;
	- Collection<String> xcodes;
}

interface CorSourceService {
	// 核销原始数据服务
	List<Source> loadSource(Collection<String> xcodes);
	List<Source> loadBalance(Collection<String> xcodes);
	List<Target> loadTrace(Collection<String> tgticodes, String tgttbl);
	List<Target> loadTrace(String correlafld, Collection<String> xcodes);
	void processSource(String corcode, SourceEntity se);
	void processTarget(String corcode, TargetEntity te);
}

interface CorClient {
	// 核销调用入口封装类
	void processSource(SourceEntity se);
	void processSource(SourceEntity se, Runnable lockTask);
	
	void processTarget(TargetEntity te);
	void processTarget(TargetEntity te, Runnable lockTask);
}

interface CorRecord {
	+ void setXcode(String xcode);
	+ String getXcode();
	+ void setCorValue(BigDecimal corValue);
	+ BigDecimal getCorValue();
}

interface CorBalanceRecord {
	// 余额表接口
	+ int getCorflag();
	+ void setCorflag(int corflag);
	+ BigDecimal getCoredValue();
	+ BigDecimal getCoringValue();
}

interface CorTraceRecord {
	// 记录表接口
	+ String getTgticode();
	+ String getTgttbl();
}
@enduml

@startuml
Title 数据核销监听类图

CorSaveListener <|-- CorSourceSaveListener
CorSaveListener <|-- CorTargetSaveListener

class CorSaveListener {
	# Map<String,CorEntity> parseCorRecords(Object master);
	# void loopCor(Consumer<CorDef> c);
}

class CorSourceSaveListener {
	// 原始数据存盘监听
	# int getProcessMode(FunctionSaveShareEvent<V> se, V record);
}

note bottom of CorSourceSaveListener
	1、生效/撤单时因为原始数据的改变而处理核销数据；
	2、版本备份单据新版本特殊处理。
end note

class CorTargetSaveListener {
	// 目标数据存盘监听
	# boolean filter(FunctionSaveShareEvent<V> se, CorDef corDef, V record, Object vo);
}

note bottom of CorTargetSaveListener
	1、目标主表删除时：处理可能存在的子表核销数据；
	2、核销表删除、修改核销数据时处理核销数据；
end note
@enduml

### 时序图

#### 原始数据核销时序图

@startuml
Title 原始数据核销时序图

actor 原始数据

participant fac as "CorFactory"
participant clt as "CorClient"
participant src as "CorSourceService"
participant svc as "CorService"

原始数据 -> fac : [newCorClient]
activate fac
	fac -> clt : [processSource] 
	activate clt
		clt -> src : [processSource]
		activate src
			src -> svc : <font color="#F44336">[createSource] <b>新增核销记录</b></font>
			activate svc #F44336
				svc -> src : [loadSource] \n加载原始数据
				activate src
				src -> svc
				deactivate src
				
				svc -> svc : [insertBalance] \n插入余额记录
			svc -> src
			deactivate svc
			
			src -> svc : <font color="#2196F3">[modifySource] <b>修改核销记录</b></font>
			activate svc #2196F3
				svc -> src : [loadSource] \n加载原始数据
				activate src
				src -> svc
				deactivate src
				
				svc -> svc : [loadBalance] \n加载余额记录
				
				svc -> svc : [checkExceed] \n校验是否符合核销关系
				
				svc -> svc : [updateBalance] \n修改余额记录
			svc -> src
			deactivate svc
			
			src -> svc : <font color="#4CAF50">[deleteSource] <b>删除核销记录</b></font>
			activate svc #4CAF50
				svc -> svc : [checkDelete] \n检查是否可以删除
				
				svc -> svc : [deleteBalance] \n删除余额记录
			svc -> src
			deactivate svc
		src -> clt
		deactivate src
	clt -> fac
	deactivate clt
fac -> 原始数据
deactivate fac
@enduml

#### 目标数据核销时序图

@startuml
Title 目标数据核销时序图

actor 目标数据

participant fac as "CorFactory"
participant clt as "CorClient"
participant src as "CorSourceService"
participant svc as "CorService"
participant tgt as "CorTargetService"

目标数据 -> fac : [newCorClient]
activate fac
	fac -> clt : [processTarget] 
	activate clt
		clt -> src : [processTarget]
		activate src
			src -> svc : <font color="#F44336">[appendTarget] <b>追加目标记录</b></font>
			activate svc #F44336
				svc -> tgt : [loadTarget] \n加载目标数据
				activate tgt
				tgt -> svc
				deactivate tgt
				
				svc -> svc : [loadBalance] \n加载余额数据
				svc -> svc : [buildTraceSumMap] \n已核销总和
				svc -> svc : [checkOverForAppend] \n检查是否超出核销
				svc -> svc : [insertTrace] \n生成追踪记录
				svc -> svc : [processBalanceForAppend] \n处理核销数值关系
				svc -> svc : [updateBalance] \n更新余额记录
			svc -> src
			deactivate svc
			
			src -> svc : <font color="#2196F3">[modifyTarget] <b>修改目标记录</b></font>
			activate svc #2196F3
				svc -> tgt : [loadTarget] \n加载目标数据
				activate tgt
				tgt -> svc
				deactivate tgt
				
				svc -> svc : [loadBalance] \n加载余额数据
				svc -> svc : [buildTraceSumMap] \n新核销总和
				svc -> svc : [buildTraceSumMap] \n老核销总和
				svc -> svc : [checkOverForModify] \n检查是否超出核销
				svc -> svc : [updateTrace] \n修改追踪记录
				svc -> svc : [processBalanceForModify] \n处理核销数值关系
				svc -> svc : [updateBalance] \n更新余额记录
			svc -> src
			deactivate svc
			
			src -> svc : <font color="#4CAF50">[deleteTarget] <b>删除目标记录</b></font>
			activate svc #4CAF50
				svc -> src : [loadTrace] \n加载追踪记录
				activate src
				src -> svc
				deactivate src
				
				svc -> src : [loadBalance] \n加载余额记录
				activate src
				src -> svc
				deactivate src
				
				svc -> svc : [deleteTrace] \n删除追踪记录
				svc -> svc : [buildTraceSumMap] \n核销总和
				svc -> svc : [processBalanceForDelete] \n处理核销数值关系
				svc -> svc : [updateBalance] \n更新余额记录
			svc -> src
			deactivate svc
		src -> clt
		deactivate src
	clt -> fac
	deactivate clt
fac -> 目标数据
deactivate fac
@enduml

### 异常提示语

* 当目标核销值超过余额时抛出Cor.07的异常错误：
	Cor.07=‘%0’超过剩余值：原值=%1，剩余=%2，目标增量=%3，目标原值=%4，最大录入值=%5（%2+%4），核销码=%6
* 目前系统默认：
	Cor.07=本次录入超过最大可录入值（%5），请重新录入！
* 项目上可以在System.properties中通过Cor.07进行配置格式

|参数|说明|
|-|-|
|%0|核销列标题|
|%1|余额表原值|
|%2|余额表余额值，即原值减去已核销值|
|%3|目标本次的核销增量，可能为正/负/零|
|%4|目标表本次修改之前的值|
|%5|目标表修改时本次可以录入的最大值|

### 被动生成余额

* 概念：
	* 默认主动生成余额，即原单据生效后生成余额表；
	* 被动生成余额，即在首次目标核销时生成余额表；
* 选项：
	* 系统选项：SN-PLAT.Cor.ReactiveBal
	* 核销选项：plat_cordef.reactivebal
	* 核销选项 优先级高于 系统选项，默认N（即不是被动模式，为主动模式）
* 工作台查询：
	* 参数配置方式@SqlColumn(filterBean=CorRecord.FilterBean+"?['corcode']")

|类型|核销未完成|核销已完成|核销工作台查询/视图|
|-|-|-|-|
|主动|corflag in(0,1)|corflag in(2,3)|等值连接|
|被动|corflag is null or corflag in(0,1)|corflag in(2,3)|左连接|

### 单元测试

#### 场景说明

<table>
	<tr>
		<th align="center" colspan="2">原始数据</th>
	</tr>
	<tr>
		<th align="center">核销码</th>
		<th align="center">原始值</th>
	</tr>
	<tr>
		<td>X01</td>
		<td>10</td>
	</tr>
</table>

<table>
	<tr>
		<th align="center">情景</th>
		<th align="center">分类</th>
		<th align="center">核销码</th>
		<th align="center">原值</th>
		<th align="center">已核销</th>
		<th align="center">剩余值</th>
		<th align="center"><font color="red">标识位</font></th>
	</tr>
	<tr>
		<td>原始记录</td>
		<td>余额</td>
		<td>X01</td>
		<td>10</td>
		<td>0</td>
		<td>10</td>
		<td align="center"><font color="red"><b>0</b></font></td>
	</tr>
	<tr>
		<td rowspan="3">核销部分</td>
		<td >目标</td>
		<td colspan="5">
			<table>
				<tr>
					<th align="center">目标码</th>
					<th align="center">核销码</th>
					<th align="center">核销值</th>
				</tr>
				<tr>
					<td>T01</td>
					<td>X01</td>
					<td>2</td>
				</tr>
			</table>
		</td>
	</tr>
	<tr>
		<td >追踪</td>
		<td colspan="5">
			<table>
				<tr>
					<th align="center">目标表</th>
					<th align="center">目标码</th>
					<th align="center">核销码</th>
					<th align="center">核销值</th>
				</tr>
				<tr>
					<td>目标表名</td>
					<td>T01</td>
					<td>X01</td>
					<td>2</td>
				</tr>
			</table>
		</td>
	</tr>
	<tr>
		<td>余额</td>
		<td>X01</td>
		<td>10</td>
		<td>2</td>
		<td>8</td>
		<td align="center"><font color="red"><b>1</b></font></td>
	</tr>
	<tr>
		<td rowspan="3">核销完成</td>
		<td >目标</td>
		<td colspan="5">
			<table>
				<tr>
					<th align="center">目标码</th>
					<th align="center">核销码</th>
					<th align="center">核销值</th>
				</tr>
				<tr>
					<td>T01</td>
					<td>X01</td>
					<td>2</td>
				</tr>
				<tr>
					<td>T02</td>
					<td>X01</td>
					<td>8</td>
				</tr>
			</table>
		</td>
	</tr>
	<tr>
		<td >追踪</td>
		<td colspan="5">
			<table>
				<tr>
					<th align="center">目标表</th>
					<th align="center">目标码</th>
					<th align="center">核销码</th>
					<th align="center">核销值</th>
				</tr>
				<tr>
					<td>目标表名</td>
					<td>T01</td>
					<td>X01</td>
					<td>2</td>
				</tr>
				<tr>
					<td>目标表名</td>
					<td>T02</td>
					<td>X01</td>
					<td>8</td>
				</tr>
			</table>
		</td>
	</tr>
	<tr>
		<td>余额</td>
		<td>X01</td>
		<td>10</td>
		<td>10</td>
		<td>0</td>
		<td align="center"><font color="red"><b>2</b></font></td>
	</tr>
	<tr>
		<td rowspan="3">核销溢出</td>
		<td >目标</td>
		<td colspan="5">
			<table>
				<tr>
					<th align="center">目标码</th>
					<th align="center">核销码</th>
					<th align="center">核销值</th>
				</tr>
				<tr>
					<td>T01</td>
					<td>X01</td>
					<td>2</td>
				</tr>
				<tr>
					<td>T02</td>
					<td>X01</td>
					<td>8</td>
				</tr>
				<tr>
					<td>T03</td>
					<td>X01</td>
					<td>3</td>
				</tr>
			</table>
		</td>
	</tr>
	<tr>
		<td >追踪</td>
		<td colspan="5">
			<table>
				<tr>
					<th align="center">目标表</th>
					<th align="center">目标码</th>
					<th align="center">核销码</th>
					<th align="center">核销值</th>
				</tr>
				<tr>
					<td>目标表名</td>
					<td>T01</td>
					<td>X01</td>
					<td>2</td>
				</tr>
				<tr>
					<td>目标表名</td>
					<td>T02</td>
					<td>X01</td>
					<td>8</td>
				</tr>
				<tr>
					<td>目标表名</td>
					<td>T03</td>
					<td>X01</td>
					<td>3</td>
				</tr>
			</table>
		</td>
	</tr>
	<tr>
		<td>余额</td>
		<td>X01</td>
		<td>10</td>
		<td>13</td>
		<td>-3</td>
		<td align="center"><font color="red"><b>3</b></font></td>
	</tr>
</table>

注意：
1. 不允许核销溢出，除非配置flags&1；
2. 目标核销不允许为0或空，除非配置flags&2；

#### 原值测试

* 单元测试类CorTestForSource
1. 新增时：
	* 核销值不可为空；
	* 核销值不可为零；
1. 修改时：
	* 核销值不可为空；
	* 核销值可以为零（红冲的情况）；
1. 删除时：不做控制；

#### 目标值测试

* 单元测试类CorTestForTarget
1. 新增/修改时：
	* 目标值不能超过原值；
	* 目标值超过原值：flags&1；
	* 目标值不可为空或零；
	* 目标值可以为空或零：flags&2；
	* 单原多目标不超；
	* 单原多目标超；
1. 修改时：
	* 目标值不能超越原值；
	* 单原多目标不超（存在增删改）；
	* 单原多目标超（存在增删改）；
1. 删除时：
	* 不能超过原值（红冲记录删除时）；

#### 原目标交叉测试

* 单元测试类CorTestForCross
1. 原值删除：
	* 存在目标时，不可删除；
1. 原值修改：
	* 不能少于已核销值；

#### 上游存盘监听

* 单元测试类CorTestForSourceSave
1. 生效后生成余额记录；
1. 撤单后删除余额记录；
1. 版本备份生效后交叉判断余额表的增删改；

#### 下游存盘监听
* 单元测试类CorTestForTargetSave

#### 多核销列

#### 异步核销（下游） 

#### 版本备份(上游)

#### 单据红冲(下游)