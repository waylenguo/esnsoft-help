## 单据撤单

|日期|作者|备注|
|------|------|------|
|2018-08-27|冀小雷|初稿|
|2019-10-11|吴小海|重构|


### 指南	
#### 概念说明
1. N9B平台支持单据撤单功能,可通过相关配置实现撤单功能;
1. 撤单功能配置包含客户端监听及撤单配置服务;
1. 平台提供标准撤单Bean：
	1. SN-PLAT.StatusRetractBean ： 单据状态检查与修改；
	1. SN-PLAT.VBRetractBean : 版本备份撤单检查；
	1. SN-PLAT.CodeRefRetractBean : 编码引用撤单检查；


#### 使用说明
		
##### 监听配置

1. 界面定义配置
```xml
<jslistener>
	#new snsoft.plat.bas.sheet.retract.RetractListener({})
</jslistener>
```

1. 系统功能配置
```xml
<Functions>
	<Functype>SN-PLAT.JS</Functype>
	<Subtype>2</Subtype>
	<Funcimpl>#new snsoft.plat.bas.sheet.retract.RetractListener({})</Funcimpl>
	<Remark>单据撤单</Remark>
</Functions>
```

##### XML文件配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SheetRetractDef opids="B"
	xmlns="http://www.snsoft.com.cn/schema/SheetRetractDef"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetRetractDef http://www.snsoft.com.cn/schema/SheetRetractDef.xsd">
	<Title>${}</Title>
	<TacFormula><![CDATA[
		proc canRetract(context, event, innerCode)
			wcode = event.getRecord(innerCode).getWcode()
			if "warn" == wcode
				context.addWarn("warn")
				return true
			end if
			return wcode == null || "can" == wcode
		end proc
		proc getError(context, event, innerCode)
			return new snsoft.commons.util.BaseException("error")
		end proc
		proc retract(context, event, innerCode)
			sql = "update tst_plat_main set wcode=? where micode=?"
			getDatabaseByTable("tst_plat_main").executeUpdate(sql,"retracted",innerCode) 
		end proc
	]]></TacFormula>
	<Remark><![CDATA[
		单据撤单说明
	]]></Remark>
	<!-- 1:异步撤单 -->
	<RetractFlags>0</RetractFlags>
	<Retracts>
		<BeanName>SN-PLAT.VBRetractBean</BeanName>
		<Remark><![CDATA[
			1、版本备份：非正常版本不允许撤单；
		]]></Remark>
	</Retracts>
	<Retracts>
		<BeanName>SN-PLAT.StatusRetractBean</BeanName>
		<Remark><![CDATA[
			单据状态：
			1、：非生效状态不允许撤单；
			2、：撤单后将状态改为草拟；
		]]></Remark>
	</Retracts>
	<Retracts>
		<BeanName>SN-PLAT.CodeRefRetractBean?[{refcode:'help_ctry.ncode',title:'国别地区'}]</BeanName>
		<Remark><![CDATA[
			撤单编码引用检查
		]]></Remark>
	</Retracts>
	<Retracts>
		<BeanName>SN-XX.XX</BeanName>
		<Remark><![CDATA[
			下游单据撤单：
			1、生效状态不允许撤单；
			2、草拟状态可以直接删除；
		]]></Remark>
	</Retracts>
</SheetRetractDef>
```

##### 覆盖标准配置

另请参见[功能扩展](help.html?helpFile=help/SN-PLAT/PlatModule.md#功能扩展)。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SheetRetractDef opids="B"
	xmlns="http://www.snsoft.com.cn/schema/SheetRetractDef"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetRetractDef http://www.snsoft.com.cn/schema/SheetRetractDef.xsd">
	<Title>${}</Title>
    <ExcludeListeners><![CDATA[
		【#BeanName】(删除父节点Bean名称)
		【#BeanName?[{key:'value'}]】(删除带参父节点Bean名称)
		【snsoft.xx.XX.new?key=value】(删除父节点实例类名称)
		【*】(删除父节点所有Bean)
    ]]></ExcludeListeners>
	<Retracts>
		<BeanName>【#BeanName】(子节点新增Bean名称)</BeanName>
		<Remark><![CDATA[]]></Remark>
	</Retracts>
	<Retracts>
		<BeanName>【#BeanName?[{key:'value'}]】(子节点新增带参Bean名称)</BeanName>
		<Remark><![CDATA[]]></Remark>
	</Retracts>
	<Retracts>
		<BeanName>【snsoft.xx.XX.new?key=value】(子节点新增实例类名称)</BeanName>
		<Remark><![CDATA[]]></Remark>
	</Retracts>
</SheetRetractDef>
```


### 手册		

1. 撤单客户端服务可通过界面定义方式或者系统功能服务配置任一方式配置;
1. 单据撤单配置文件路径：
	* 标准配置：resources/cfg/res/retract/*.xml；
	* 覆盖配置：resources/cfg/res/retract/[recode]/*.xml；
1. 单据撤单配置请参见对应的Schema文件【[SheetRetractDef.xsd](do/BrowseSchema?schema=SheetRetractDef.xsd)】；
1. 支持自定义撤单处理、校验逻辑；需要添加撤单Bean实现RetractBean接口相关方法;添加自定义撤单Bean到撤单配置文件中;

### 实践	
	
### 设计

#### 类图

@startuml
Title 单据撤单类图

bean.RetractBean <|-right- bean.StatusRetractBean
bean.RetractBean <|-left- bean.VBRetractBean
bean.RetractBean <|-up- bean.CodeRefRetractBean

bean.RetractBean <--o sv.RetractService

sv.RetractService <.. sv.RetractServiceFactory
sv.RetractServiceFactory <.. sv.RetractController
sv.RetractService <.. sv.RetractController

sv.RetractController <.. xjs.RetractListener

interface def.RetractDefService {
	+ QueryResults<RetractDef> queryUI();
	+ void saveUI();
	+ QueryResults<Retract> queryRetractsUI();
	+ void syncToDB(String recode);
}

interface def.RetractSheetDefService {
	+ List<RetractSheet> queryUI();
	+ void saveUI(List<RetractSheet> records);
	+ void syncToDB(String sheetCode);
}

interface bean.RetractBean {
	+ void check();
	+ void retract();
}

class bean.StatusRetractBean {
	// 单据状态撤单Bean
}

class bean.VBRetractBean {
	// 版本备份撤单Bean
}

interface sv.RetractController {
	+ RetractContext retract(String sheetCode, String innercode);
	+ RetractContext check(String sheetCode, String innercode);
}

interface sv.RetractServiceFactory {
	+ RetractDef getRetractDef(String recode);
	+ String getRecode(String sheetCode, String innerCode);
	+ RetractService newRetractService();
}

interface sv.RetractService {
	+ RetractContext check(String innerCode);
	+ RetractContext retract(String innerCode);
}

class xjs.RetractListener {
	# void oncmd_retract(Table table);
}
@enduml

#### 时序图
@startuml
Title 单据撤单时序图

actor 客户端

participant jslis   as "js.RetractListener"
participant ctrl    as "RetractController"
participant fac     as "RetractServiceFactory"
participant service as "RetractService"
participant bean    as "RetractBean"

客户端 -> jslis : [oncmd_retract]
activate jslis
	jslis -> ctrl : [check] \n撤单检查
	activate ctrl
		ctrl -> ctrl : [getRetractService] \n获取撤单服务
		activate ctrl
			ctrl -> fac : [newRetractService] \n创建撤单服务
			activate fac
			fac -> ctrl 
			deactivate fac
		deactivate ctrl
		
		ctrl -> service : [check] \n撤单检查
		activate service
			service -> bean : [check] \n当前Bean检查
			activate bean
			bean -> service 
			deactivate bean
		service -> ctrl 
		deactivate service
	ctrl -> jslis 
	deactivate ctrl
jslis -> 客户端
deactivate jslis
@enduml
