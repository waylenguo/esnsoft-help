## 系统功能配置

|日期|作者|备注|
|------|------|------|
|2018-10-19|冀小雷|初稿|

### 系统功能说明

1. 系统功能是若干个功能监听的集合，在UI层是指UI监听与JS监听，在服务层是指查询、存盘、审批监听；
1. 系统功能配置请参见对应的Schema文件【[plat-sysfunc.xsd](do/BrowseSchema?schema=plat-sysfunc.xsd)】；
1. 配置文件路径：
	* 标准配置：res/sysfunc/*.xml；
	* 覆盖配置：res/sysfunc/[funccode]/*.xml；
	* 系统功能配置路径支持分级：res/sysfunc/[sysid]/xx/yy/zz.xml；
1. 系统功能支持覆盖：
	* 覆盖规则：在res/sysfunc目录下新建文件夹，文件夹名称与被覆盖的系统功能号相同；在该文件夹下增加系统功能定义文件；
	* 通过replace属性指定需要覆盖的类名或Bean名称；
	* 如果功能监听存在多个，则需要在被覆盖的系统功能中通过refid进行区分，覆盖时通过【replace=&quot;#refid&quot;】进行指定覆盖；
	
### 二次开发支持

* 在标准二次开发（同步至数据库）的基础上再次支持辅助二次开发；
* 通过【附加脚本】的方式进行支持：
	* 服务存盘脚本；
	* 服务查询脚本；
	* UI监听脚本；
	* JS监听脚本；
	* 审批监听脚本；

1. 服务查询脚本：通过销售合同-入口进行演示（将所有客户设置为[中华人名共和国-1]）
	[销售合同-入口](uiinvoke/00/zh_CN/theme0/SN-HELP.Ord.SalorderList.html)
	[系统功能](uiinvoke/00/zh_CN/theme0/SN-PLAT.SystemFunctions.html?InitValue.funccode=SN-HELP.Salorder.Entry.SV&AutoRefresh=1)
```sql
proc afterQuery(se,params,results)
  it = it(results.getData())
  while it.hasNext()
    it.next().setCcode("中华人名共和国-1")
  end while
end proc
```
2. UI监听脚本：通过销售合同-入口进行演示（将所有客户设置为[中华人名共和国-2]）
	[销售合同-入口](uiinvoke/00/zh_CN/theme0/SN-HELP.Ord.SalorderList.html)
	[系统功能](uiinvoke/00/zh_CN/theme0/SN-PLAT.SystemFunctions.html?InitValue.funccode=SN-HELP.Salorder.Entry.UI&AutoRefresh=1)
```sql
proc onDataLoaded(event,rs)
	ars = snsoft.dx.ReadDataSetFactory.impl.toArrayReadDataSet(rs,true)
	idx = ars.columnAt("ccode")
	it = it(ars.loadDataSetValues())
	while it.hasNext()
		it.next()[idx]="中华人名共和国-2"
	end while
	return ars
end proc
```
3. JS监听脚本：通过销售合同-入口进行演示（将所有客户设置为[中华人名共和国-3]）
	[销售合同-入口](uiinvoke/00/zh_CN/theme0/SN-HELP.Ord.SalorderList.html)
	[系统功能](uiinvoke/00/zh_CN/theme0/SN-PLAT.SystemFunctions.html?InitValue.funccode=SN-HELP.Salorder.Entry.UI&AutoRefresh=1)
```js
dataLoaded:function(dataSet,e)
{
	for(var i = 0;i < dataSet.getRowCount();i++)
	{
		dataSet.gotoRow(i);
		dataSet.setValue("ccode","中华人名共和国-3");
	}
}
```
4. 服务存盘脚本：通过销售合同详情进行演示（存盘时，如果客户修改为[666]则抛出错误[中华人名共和国-4]）
	[销售合同-详情](uiinvoke/00/zh_CN/theme0/SN-HELP.Ord.SalorderDetail.html?_v=1571898470281#?AutoRefresh=1&InitValue.salordicode=<NULL>)
	[系统功能](uiinvoke/00/zh_CN/theme0/SN-PLAT.SystemFunctions.html?InitValue.funccode=SN-HELP.Salorder.SV&AutoRefresh=1)
```sql
proc beforeSave(se,params,results,record)
	if record.isColumnStored("ccode") && "666" == record.getCcode()
		throw new java.lang.RuntimeException("中华人名共和国-4")
	end if
end proc
```
5. 审批监听脚本：通过销售合同详情进行演示（提交时，如果客户为[888]则抛出错误[中华人名共和国-5]）
	[销售合同-详情](uiinvoke/00/zh_CN/theme0/SN-HELP.Ord.SalorderDetailWFSub.html?InitValue.salordicode=<NULL>&AutoRefresh=1)
	[系统功能](uiinvoke/00/zh_CN/theme0/SN-PLAT.SystemFunctions.html?InitValue.funccode=SN-HELP.Salorder.SV&AutoRefresh=1)
```sql
proc performSubmit(event,innercode)
	record = event.getRecord(innercode)
	if "888" == record.getCcode()
		throw new java.lang.RuntimeException("中华人名共和国-5")
	end if
end proc
```

#### Tac脚本支持

<table>
	<tr>
		<th align='center' colspan='3'>查询监听脚本支持</th>
	</tr>
	<tr>
		<th>查询前</th>
		<td>
		proc beforeQuery(se, params)<br/>
		end proc
		</td>
		<td>转换或设置参数</td>
	</tr>
	<tr>
		<th>执行查询</th>
		<td>
		proc executeQuery(se, params)<br/>
		end proc
		</td>
		<td>返回值List&lt;VO&gt;<br/>如果返回值不为空<br/>则认为是查询结果</td>
	</tr>
	<tr>
		<th>查询后</th>
		<td>
		proc afterQuery(se, params, results)<br/>
		end proc
		</td>
		<td>可以对查询结果进行处理<br/>如：聚合、补数、过滤等</td>
	</tr>
	<tr>
		<th align='center' colspan='3'>存盘监听脚本支持</th>
	</tr>
	<tr>
		<th align='center'>类型</th>
		<th align='center'>声明</th>
		<th align='center'>说明</th>
	</tr>
	<tr>
		<th>事务开始前</th>
		<td>
		proc beforeTrans(params, results)<br/>
		end proc
		</td>
		<td>全量</td>
	</tr>
	<tr>
		<th>事务开始前</th>
		<td>
		proc beforeTrans(params, results, record)<br/>
		end proc
		</td>
		<td>单记录</td>
	</tr>
	<tr>
		<th>存盘前</th>
		<td>
		proc beforeSaveAll(params, results)<br/>
		end proc
		</td>
		<td>全量[事务中]</td>
	</tr>
	<tr>
		<th>存盘前</th>
		<td>
		proc beforeSave(se, params, results, record)<br/>
		end proc
		</td>
		<td>单记录[事务中]</td>
	</tr>
	<tr>
		<th>存盘后</th>
		<td>
		proc afterSave(se, params, results, record)<br/>
		end proc
		</td>
		<td>单记录[事务中]</td>
	</tr>
	<tr>
		<th>存盘后</th>
		<td>
		proc afterSaveAll(params, results)<br/>
		end proc
		</td>
		<td>全量[事务中]</td>
	</tr>
	<tr>
		<th>事务结束后</th>
		<td>
		proc afterTrans(params, results, record)<br/>
		end proc
		</td>
		<td>单记录</td>
	</tr>
	<tr>
		<th>事务结束后</th>
		<td>
		proc afterTrans(params, results)<br/>
		end proc
		</td>
		<td>全量</td>
	</tr>
	<tr>
		<th>提交检查</th>
		<td>
		proc submitCheck(main)<br/>
		end proc
		</td>
		<td></td>
	</tr>
	<tr>
		<th align='center' colspan='3'>单据状态扩展接口</th>
	</tr>
	<tr>
		<th>状态改变前</th>
		<td>
		proc beforeChange(se, params, results, record,oldStatus,newStatus)<br/>
		end proc
		</td>
		<td rowspan='2'></td>
	</tr>
	<tr>
		<th>状态改变后</th>
		<td>
		proc afterChange(se, params, record,oldStatus,newStatus)<br/>
		end proc
		</td>
	</tr>
	<tr>
		<th>提交前</th>
		<td>
		proc beforeSubmit(se, params, results, record)<br/>
		end proc
		</td>
		<td rowspan='2'>状态从【<=20】改为【>20】</td>
	</tr>
	<tr>
		<th>提交后</th>
		<td>
		proc afterSubmit(se, params, record)<br/>
		end proc
		</td>
	</tr>
	<tr>
		<th>取消提交前</th>
		<td>
		proc beforeBackSubmit(se, params, results, record)<br/>
		end proc
		</td>
		<td rowspan='2'>状态从【>20】改为【<=20】</td>
	</tr>
	<tr>
		<th>取消提交后</th>
		<td>
		proc afterBackSubmit(se, params, record)<br/>
		end proc
		</td>
	</tr>
	<tr>
		<th>生效/启用前</th>
		<td>
		proc beforeRatify(se, params, results, record)<br/>
		end proc
		</td>
		<td rowspan='2'>状态从【<70】改为【=70】</td>
	</tr>
	<tr>
		<th>生效/启用后</th>
		<td>
		proc afterRatify(se, params, record)<br/>
		end proc
		</td>
	</tr>
	<tr>
		<th>取消生效/启用前</th>
		<td>
		proc beforeUnratify(se, params, results, record)<br/>
		end proc
		</td>
		<td rowspan='2'>状态从【=70】改为【<70】</td>
	</tr>
	<tr>
		<th>取消生效/启用后</th>
		<td>
		proc afterUnratify(se, params, record)<br/>
		end proc
		</td>
	</tr>
	<tr>
		<th>终结前</th>
		<td>
		proc beforeEnd(se, params, results, record)<br/>
		end proc
		</td>
		<td rowspan='2'>状态从【=70】改为【=75】</td>
	</tr>
	<tr>
		<th>终结后</th>
		<td>
		proc afterEnd(se, params, record)<br/>
		end proc
		</td>
	</tr>
	<tr>
		<th>取消终结前</th>
		<td>
		proc beforeCancelEnd(se, params, results, record)<br/>
		end proc
		</td>
		<td rowspan='2'>状态从【=75】改为【=70】</td>
	</tr>
	<tr>
		<th>取消终结后</th>
		<td>
		proc afterCancelEnd(se, params, record)<br/>
		end proc
		</td>
	</tr>
	<tr>
		<th>作废前</th>
		<td>
		proc beforeStop(se, params, results, record)<br/>
		end proc
		</td>
		<td rowspan='2'>状态从【=70】改为【=90】</td>
	</tr>
	<tr>
		<th>作废后</th>
		<td>
		proc afterStop(se, params, record)<br/>
		end proc
		</td>
	</tr>
	<tr>
		<th>取消作废前</th>
		<td>
		proc beforeCancelStop(se, params, results, record)<br/>
		end proc
		</td>
		<td rowspan='2'>状态从【=90】改为【=70】</td>
	</tr>
	<tr>
		<th>取消作废后</th>
		<td>
		proc afterCancelStop(se, params, record)<br/>
		end proc
		</td>
	</tr>
</table>

### 监听配置

* 接入层系统功能启用配置

```xml
<!--UI层界面定义配置-->
<uilisteners>
	snsoft.plat.bas.busi.PlatFunctionListener.new?funccode=系统功能编号
</uilisteners>
<jslistener>
	#new snsoft.plat.bas.busi.PlatFunctionListener({})
</jslistener>

<!--
关于UI功能监听：
1、如果不继承ui.SystemFunctionListener，则该监听仅作用于当前界面；
2、如果集成子ui.SystemFunctionListener且tgtUINames不为空，则监听仅作用于指定的界面；
3、如果为Tac脚本，则可以通过全局变量tgtUINames指定作用的界面；
-->
<!--
关于JS功能监听：
1、如果不继承js.SystemFunctionListener，则该监听仅作用于当前界面；
2、如果集成子js.SystemFunctionListener且tgtUINames不为空，则监听仅作用于指定的界面；
3、如果为JS脚本，则可以通过全局变量tgtUINames指定作用的界面；
-->
```

* 服务层系统功能启用配置

```java
// 系统功能服务层监听启用配置

// 1、查询监听
public class XxxServceImpl extends DefaultFunctionService<T> implements XxxService
{
	@Overrdie
	public QueryResults<S> queryXxxUI(XxxParams params)
	{
		// 因为一个服务可以存在多个查询，故系统功能号在调用时传递
		return super.uiQuery(params,"系统功能号");
	}
}

// 2、存盘/审批监听：因为一个功能仅允许存在一个写方法，故系统功能号在服务上定义
@Funccode("系统功能号")
public interface XxxServie
{
	SaveResults<T> saveXxxUI(SaveParams<T> params);
}
```

### XML配置文件

<b>注意：服务层、UI层系统功能号不要共用；不同功能的系统功能配置不要共用</b>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SystemFunctions 
	xmlns="http://www.snsoft.com.cn/schema/plat-sysfunc"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/plat-sysfunc http://www.snsoft.com.cn/schema/plat-sysfunc.xsd">
	<Name>系统功能名称</Name>
	<Remark><![CDATA[
	系统功能说明
	]]></Remark>
	<Functions grp="类别">
		<Functype></Functype>
		<Subtype>1:Bean;2:Class;3:Tac;4:JS</Subtype>
		<Funcimpl><![CDATA[
		]]></Funcimpl>
		<Remark></Remark>
	</Functions>
	<Groups id="分组">
		<Functions grp="类别">
			<Functype></Functype>
			<Subtype>1:Bean;2:Class;3:Tac;4:JS</Subtype>
			<Funcimpl><![CDATA[
			]]></Funcimpl>
			<Remark></Remark>
		</Functions>
	</Groups>
</SystemFunctions>
```

### 覆盖标准配置

另请参见[功能扩展](help.html?helpFile=help/SN-PLAT/PlatModule.md#功能扩展)。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SystemFunctions 
	xmlns="http://www.snsoft.com.cn/schema/plat-sysfunc"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/plat-sysfunc http://www.snsoft.com.cn/schema/plat-sysfunc.xsd">
    <ExcludeListeners><![CDATA[
		【#BeanName】(删除父节点Bean名称)
		【#BeanName?[{key:'value'}]】(删除带参父节点Bean名称)
		【snsoft.xx.XX.new?key=value】(删除父节点实例类名称)
		【*】(删除父节点所有Bean)
    ]]></ExcludeListeners>
	<Functions>
		<Functype>*</Functype>
		<Subtype>*</Subtype>
		<Funcimpl><![CDATA[
		【#BeanName】(子节点新增Bean名称)
		]]></Funcimpl>
		<Remark></Remark>
	</Functions>
	<Functions>
		<Functype>*</Functype>
		<Subtype>*</Subtype>
		<Funcimpl><![CDATA[
		【#BeanName?[{key:'value'}]】(子节点新增带参Bean名称)
		]]></Funcimpl>
		<Remark></Remark>
	</Functions>
	<Functions>
		<Functype>*</Functype>
		<Subtype>*</Subtype>
		<Funcimpl><![CDATA[
		【snsoft.xx.XX.new?key=value】(子节点新增实例类名称)
		]]></Funcimpl>
		<Remark></Remark>
	</Functions>
</SystemFunctions>
```

### 定义类图

@startuml
Title 系统功能定义类图

SystemFunctions     "1" o--> "n" SystemFunction
SystemFunctions     "1" o--> "n" SystemFunctionGroup
SystemFunctionGroup "1" o--> "n" SystemFunction

SystemFunctionService <|.. SystemFunctionServiceImpl
AbstractDefService    <|-- SystemFunctionServiceImpl

class SystemFunctions {
	# String  funccode;
	# String  name;
	# String  remark;
	# List<SystemFunction>       functions;
	# List<SystemFunctionGroup>  groups;
}

class SystemFunction {
	# String functype;
	# String subtype;
	# String funcimpl;
	# int stopflag;
}

class SystemFunctionGroup {
	# String grp;
	# List<SystemFunction> functions;
}

interface SystemFunctionService {
	void saveUI(List<SystemFunctions> records);
	List<SystemFunctions> queryUI();
	List<SystemFunction> queryFunctionsUI();
	void syncToDB(String funccode);
	List<SystemFunction> getSystemFunctions(String funccode, String functype);
}

class SystemFunctionServiceImpl {
	# void beforeSyncToDB(SystemFunctions def);
}
@enduml

### 功能类图

@startuml
Title 系统功能功能类图

FunctionQueryService <|.. AbstractFunctionService
FunctionSaveService  <|.. AbstractFunctionService
DefaultFunctionService  -right-|> AbstractFunctionService
AbstractFunctionService .right.> SystemFunctionService

AbstractFunctionService o--> FunctionQueryListener
AbstractFunctionService o--> FunctionSaveListener

SystemFunctionService <.up. ui.PlatFunctionListener
ui.PlatFunctionListener o--> ui.SystemFunctionListener
js.PlatFunctionListener o--> js.SystemFunctionListener
ui.PlatFunctionListener <.right. js.PlatFunctionListener




interface SystemFunctionService {
	List<SystemFunction> getSystemFunctions(String funccode, String functype);
}

interface FunctionQueryService {
	QueryResults<V> uiQuery(QueryParams params);
	QueryResults<V> uiQuery(QueryParams params, String funccode);
}

interface FunctionSaveService {
	SaveResults uiSave(SaveParams<V> params);
	SaveResults uiSave(SaveParams<V> params, String funccode);
}

abstract class AbstractFunctionService {
	
}

class DefaultFunctionService {
	
}

interface FunctionQueryListener {
	void beforeQuery(se, QueryParams params);
	List<V> executeQuery(se, QueryParams params);
	void afterQuery(se, QueryParams params, QueryResults<V> results);
}

interface FunctionSaveListener {
	void submitCheck(List<String> messages, V main);
	void beforeSaveAll(SaveParams<V> params, SaveResults results);
	void beforeSave(se, SaveParams<V> params, SaveResults results, V record);
	void afterSave(se, SaveParams<V> params, SaveResults results, V record);
	void afterSaveAll(SaveParams<V> params, SaveResults results);
}

class ui.PlatFunctionListener {
	
}

class ui.SystemFunctionListener {
	
}

class js.PlatFunctionListener {
	
}

class js.SystemFunctionListener {
	
}
@enduml

### 监听解析类图

@startuml
Title 监听解析类图

FuncImplParser <|.. ApprListenerParser
FuncImplParser <|.. FunctionListenerParser
FuncImplParser <|.. JSListenerParser
FuncImplParser <|.. UIListenerParser

interface FuncImplParser {
	+ T parse(String subtype, String impl);
}

class ApprListenerParser {
	// 审批监听解析类
}

class FunctionListenerParser {
	// 服务监听解析器
}

class JSListenerParser {
	// JS监听解析器
}

class UIListenerParser {
	// UI监听解析器
}
@enduml

### 接入层时序图

@startuml
Title 接入层界面初始化时序图

actor 界面初始化

participant ui as "ui.PlatFunctionListener"
participant sv as "SystemFunctionService"
participant ps as "FuncImplParser"
participant js as "js.PlatFunctionListener"

界面初始化 -> ui : [initComponent] \n界面初始化
activate ui
	ui -> ui : [getSheetFunctionJSListener] \n获取JS监听列表
	activate ui
		ui -> sv : [getSystemFunctions] \n获取JS功能列表
		activate sv
		sv -> ui
		deactivate sv
		
		ui -> ps : [parse] \n解析JS监听或脚本
		activate ps
		ps -> ui
		deactivate ps
	deactivate ui
	
	ui -> js : [initComponent] \n将JS监听或脚本添加到对应的界面上
	activate js
	js -> ui
	deactivate js
ui -> 界面初始化
deactivate ui
@enduml

### 服务层时序图

@startuml
Title 接入层界面初始化时序图

actor at as "查询+存盘+审批"

participant svc   as "AbstractFunctionService"
participant query as "FunctionQueryListener"
participant save  as "FunctionSaveListener"
participant appr  as "ApprListener"

at -> svc : [uiQuery]
activate svc #F44336
	svc -> svc : [funcQuery]
	activate svc
		svc -> svc : [getQueryListeners]
		svc -> query : [beforeQuery]
		svc -> query : [executeQuery]
		svc -> svc : [doQuery]
		svc -> query : [afterQuery]
	svc -> svc
	deactivate svc
svc -> at
deactivate svc

at -> svc : [uiSave]
activate svc #2196F3
	svc -> svc : [getSaveListeners]
	svc -> svc : [executeSave]
	activate svc
		svc -> save : [beforeSave]
		svc -> svc : [doSave]
		svc -> save : [afterSave]
	deactivate svc
svc -> at
deactivate svc

at -> svc : [审批接口]
activate svc #4CAF50
	svc -> appr : [beforeSubmit]\n[beforePerformSubmit]\n[afterRejectApprSubmit=afterReject]\n[afterRatifyComplete=afterComplete]\n[aftePerformBackSubmit=afterBackSubmit]\n[afterRatifyUnitComplete]\n[getWFcodeInfo]
svc -> at
deactivate svc
@enduml