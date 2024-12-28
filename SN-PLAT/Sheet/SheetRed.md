## 单据红冲

|日期|作者|备注|
|------|------|------|
|2019-05-20|冀小雷|初稿|


### 单据红冲说明

1. 单据红冲配置请参见对应的Schema文件【[SheetRedDef.xsd](do/BrowseSchema?schema=SheetRedDef.xsd)】；
1. 单据与红冲配置关系：可以通过配置表进行配置对应关系，默认红冲配置编码与单据编码相同；
1. 配置文件路径：res/sheetred/*.xml

### 监听配置
#### 界面直接配置

```xml
<!--
JS监听类：
1、界面需要显示布局单据红冲监听按钮；
	红冲：red
	红蓝：redBlue
	取消：cancelRed
2、单据红冲数据以当前行内码为标准；
3、单据红冲完成后，调用对应的单据外码穿透；
-->
<jslistener><![CDATA[
	#new snsoft.plat.bas.sheet.red.SheetRedJSListener({sheetCode:''})
]]></jslistener>
```

#### 系统功能配置

```xml
<Groups id="单据附加功能">
	<Functions>
		<Functype>SN-PLAT.JS</Functype>
		<Subtype>2</Subtype>
		<Funcimpl>#new snsoft.plat.bas.sheet.red.SheetRedJSListener({sheetCode:''})</Funcimpl>
		<Remark>单据红冲</Remark>
	</Functions>
</Groups>

<Groups id="单据红冲">
	<Functions>
		<Functype>SN-PLAT.SV</Functype>
		<Subtype>1</Subtype>
		<Funcimpl>SN-PLAT.SheetRedSVListener</Funcimpl>
		<Remark>单据红冲删除校验</Remark>
	</Functions>
</Groups>
```

### XML文件配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SheetRedDef redname="${}" 
	xmlns="http://www.snsoft.com.cn/schema/SheetRedDef"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetRedDef http://www.snsoft.com.cn/schema/SheetRedDef.xsd" >
	<Listeners>
	snsoft.plat.bas.sheet.red.service.impl.SheetRedCorListener.new?corcodes=
	</Listeners>
	<TacFormula></TacFormula>
	<Remark><![CDATA[
	销售合同红冲配置
	]]></Remark>
	<Details tblname="plat_salorder" colname="fcy" />
	<Details tblname="plat_salorder" colname="rmb" />
	<Details tblname="plat_salorder" colname="fee" />
	<Details tblname="plat_salorderg" colname="qtc" />
	<Details tblname="plat_salorderg" colname="fcy" />
	<Details tblname="plat_salorderg" colname="rmb" />
	<Details tblname="plat_salorderg" colname="fee" />
</SheetRedDef>
```

### 覆盖标准配置

另请参见[功能扩展](help.html?helpFile=help/SN-PLAT/PlatModule.md#功能扩展)。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SheetRedDef redname="${}" 
	xmlns="http://www.snsoft.com.cn/schema/SheetRedDef"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetRedDef http://www.snsoft.com.cn/schema/SheetRedDef.xsd" >
    <ExcludeListeners><![CDATA[
		【#BeanName】(删除父节点Bean名称)
		【#BeanName?[{key:'value'}]】(删除带参父节点Bean名称)
		【snsoft.xx.XX.new?key=value】(删除父节点实例类名称)
		【*】(删除父节点所有Bean)
    ]]></ExcludeListeners>
    <Listeners><![CDATA[
		【#BeanName】(子节点新增Bean名称)
		【#BeanName?[{key:'value'}]】(子节点新增带参Bean名称)
		【snsoft.xx.XX.new?key=value】(子节点新增实例类名称)
    ]]></Listeners>
</SheetRedDef>
```

### 类图

@startuml
Title 版本备份功能类图


VersionBackupListener <|.. DefaultVersionBackupListener
DefaultVersionBackupListener <|-- ScriptVersionBackupListener
DefaultVersionBackupListener <|-- StatusVersionBackupListener

VersionBackupService <.. VersionBackupController
VersionBackupFactory <.. VersionBackupController
VersionBackupService <.. VersionBackupFactory
VersionBackupListener <--* VersionBackupService

interface VersionBackupDefService {
	// 版本备份定义维护服务
	+ List<VersionBackupDef> queryUI();
	+ void saveUI();
	+ List<VersionBackupDetail> queryDetailUI();
	+ void syncToDB(String vbcode);
}

interface VersionBackupController {
	+ T backup(String sheetCode, String innerCode);
	+ void cancel(String sheetCode, String innerCode);
	+ List<T> queryHistory(String sheetCode, String innerCode);
}

interface VersionBackupFactory {
	+ VersionBackupDef getVersionBackupDef(String vbcode);
	+ VersionBackupService<?> newVersionBackupService();
}

interface VersionBackupService {
	+ T backup(String innerCode);
	+ void cancel(String innerCode);
	+ void keep(String innerCode);
	+ void unkeep(String innerCode);
	+ List<T> queryHistory(String innerCode);
}

interface VersionBackupListener {

}

class DefaultVersionBackupListener {

}

class ScriptVersionBackupListener {
	// 脚本监听类
}

class StatusVersionBackupListener {
	// 单据状态监听类
}

class VersionBackupFunctionListener {
	+ void beforeQuery();
	+ void beforeSave();
	+ void afterSave();
}

interface VBSheetDefService {
	// 单据版本备份关系维护服务
	List<VBSheet> query(VBSheetParams params);
	void save(List<VBSheet> records);
	void syncToDB(String sheetCode);
}

@enduml

### 时序图

@startuml
Title 版本备份功能时序图

actor 客户端
participant jslis   as "js.VersionBackupListener"
participant ctrl    as "VersionBackupController"
participant fac     as "VersionBackupFactory"
participant service as "VersionBackupService"

客户端 -> jslis : [vnbk]
activate jslis

	jslis -> ctrl : [backup]
	activate ctrl
	
		ctrl -> fac : [newVersionBackupService]\n创建版本备份服务
		activate fac
			fac -> fac : [getVbcode]\n获取版本备份配置编码
			fac -> fac : [getVersionBackupDef]\n获取版本备份定义对象
		fac -> ctrl :
		deactivate fac
		
		ctrl -> service : [backup]\n版本备份逻辑
		activate service
		service -> ctrl : 返回新版本单据内码
		deactivate service
			
	ctrl -> jslis : 
	deactivate ctrl

jslis -> 客户端
deactivate jslis
@enduml

### 核心逻辑

|过程|主键|红冲核销码[r]|原单据主键[o]|红冲标识|说明|状态|
|-|-|-|-|-|-|-|
|原始|D01|D01|D01|0|无|70|


|过程|主键|红冲核销码[r]|原单据主键[o]|红冲标识|说明|状态|
|-|-|-|-|-|-|-|
|原始|D01|D01|D01|1|取消|70|
|红票|D02|D01|D01|2||70|

|过程|主键|红冲核销码[r]|原单据主键[o]|红冲标识|说明|状态|
|-|-|-|-|-|-|-|
|原始|D01|D01|D01|4|取消|70|
|红票|D02|D01|D01|2||70|
|蓝票|D03|D01|D01|0||10|


|过程|主键|红冲核销码[r]|原单据主键[o]|红冲标识|说明|状态|
|-|-|-|-|-|-|-|
|原始|D01|D01|D01|4|取消|70|
|红票|D02|D01|D01|2||70|
|蓝票-原始|D03|D01|D01|4|取消|70|
|蓝票-红冲|D04|D01|D03|2||70|
|蓝票-蓝票|D05|D01|D03|0||10|

* 撤销检查：
	* 仅【红冲标识=4】(红蓝原始单据)可以进行撤销红冲；
	* 如果存在蓝票且蓝票已经生效，则不允许撤销红冲；
	* 如果接口检查不通过，则不允许撤销；
* 撤销逻辑：
	* 调用接口处理下游数据
	* 删除原单据主键[o]=inner and redflag in(0,2)
	* 修改当前单据(红冲标识=0)

**注意：撤单检查，必须(主键=红冲核销码[r] and redflag=0)，否则不允许撤单。**