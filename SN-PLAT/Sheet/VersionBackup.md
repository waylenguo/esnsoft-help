## 版本备份

|日期|作者|备注|
|------|------|------|
|2018-09-25|宋莹磊|初稿|
|2019-10-09|冀小雷|调整|

### 指南

#### 版本备份说明

有些时候某些单据（如合同等）存在变更，但是希望保留历史的信息用于查看或对比，此时使用版本备份功能。

1. 版本备份配置请参见对应的Schema文件【[VersionBackupDef.xsd](do/BrowseSchema?schema=VersionBackupDef.xsd)】；
1. 单据与版本备份关系：可以通过配置表进行配置对应关系，默认版本备份编码与单据编码相同；
1. 配置文件路径：
	* 标准配置：res/vnbk/[vbcode].xml；
	* 覆盖配置：res/vnbk/[vbcode]/\*.xml；
1. 主表VO类需要实现接口`snsoft.plat.bas.sheet.vnbk.vo.VersionBackupVO`；
1. 版本历史查询功能需要明细页面的查询参数类实现`snsoft.plat.bas.sheet.vnbk.service.VBVersionParams`；
1. 所有版本备份表需要新建表结构，表名规则为【*_vb】，克隆原来的所有列，关联字段需要创建索引；

注意事项：
* 数据核销考虑：CorService；
* 提交生效特殊处理；
* 取消生效特殊处理：；
* 未生效的修改单不允许删除，只允许撤销：AbstractFunctionService；
* 核销关系字段特殊处理:CopyXcodeListener；
* 撤单：仅正常版本可以撤单；
* 单据附件；

#### 界面直接配置

```xml
<!--
JS监听类：
1、界面需要显示布局版本备份监听按钮，命令号=vnbk；
2、版本备份数据以当前行内码为标准；
3、版本备份完成后，调用对应的单据外码穿透；
-->
<jslistener><![CDATA[
	#new snsoft.plat.bas.sheet.vnbk.VersionBackupListener({sheetCode:''})
]]></jslistener>
```

#### 系统功能配置

```xml
<Groups id="单据附加功能">
	<Functions>
		<Functype>SN-PLAT.JS</Functype>
		<Subtype>2</Subtype>
		<Funcimpl>#new snsoft.plat.bas.sheet.vnbk.VersionBackupListener({sheetCode:'${sheetCode}'})</Funcimpl>
		<Remark>版本备份：客户端监听类</Remark>
	</Functions>
</Groups>

<Groups id="版本备份">
	<Functions>
		<Functype>SN-PLAT.SV</Functype>
		<Subtype>1</Subtype>
		<Funcimpl>SN-PLAT.VersionBackupFunctionListener</Funcimpl>
		<Remark>版本备份：存盘监听（使用于单据写服务）</Remark>
	</Functions>
</Groups>
```

#### 版本历史及比对

##### 历史版本
    
1. 标准公共界面引用：
    内嵌Tab方式：
    ```xml
    <include src="SN-PLAT.VBVersion.VBHistoryEmbed#vbhistory" mainui="xxx" />
    ```
    弹窗方式：
    ```properties
    //配置监听 #new snsoft.plat.bas.sheet.vnbk.VersionBackupListener({sheetCode:'XXXX'})，界面定义布局cmd=vbHistory的按钮。
    ```
    
##### 版本比对

1. 客户端监听：
    ```properties
    #new snsoft.plat.bas.sheet.vnbk.CompareVersionJSListener({ignoCmpCell:'predate,modifydate'})
    
    参数说明：
    //主表界面名
    mUiName
    //忽略比较的列 多个 , 分割 格式ignoCmpCell:{uiname:"celname1,celname2"}；示例：ignoCmpCell:{salorder:"predate,modifydate"}
    ignoCmpCell
    //版本备份时会含有版本号的列(ORD00001<01>)，用于清洗数据； 示例：hasVsnFlagCell:{salorder:"predate,modifydate"}
	hasVsnFlagCell
    //忽略禁用按钮 格式ignoDisTBtn:{uiname:"cmd1,cmd2"}；示例：ignoDisTBtn:{salorder:"newSheet,openSheet"}
    ignoDisTBtn
    //忽略禁用操作列按钮 格式ignoDisOpBtn:{uiname:"cmd1,cmd2"}；示例：ignoDisOpBtn:{salorder:"add,delete"}
    ignoDisOpBtn
    //忽略比较的表 格式ignoCmpTbls:"salorder,salorderg"
    ignoCmpTbls
    //版本管理表名(uiname) 默认vbhistory
    vbHistoryTblName
    ```

1. UI监听：
    ```properties
    snsoft.plat.bas.sheet.vnbk.CompareVersionUIListener.new
    ```

1. 界面定义配置：含有版本比对功能的母单据详情页面参数需要增加如下参数：
    ```xml
    <!--配置此参数在传入历史版本号时可以加载出历史数据 返回结果中的ClientProperties里使用key：oldVersionList取数据-->
    <c name="oldvsn" title="历史版本号" sqltype="4" />
    ```
   
1. 查询参数 版本备份的表参数对象实现VBVersionParams接口，增加`int	oldvsn`参数，并编写get/set方法，参数不需要@SqlColumn注解；实现getInnerfld方法，返回单据主表内码字段名。


### 手册

* 详情参见[VersionBackupDef.xsd](do/BrowseSchema?schema=VersionBackupDef.xsd)。

#### XML文件配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<VersionBackupDef
	xmlns="http://www.snsoft.com.cn/schema/VersionBackupDef"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/VersionBackupDef http://www.snsoft.com.cn/schema/VersionBackupDef.xsd">
	<Title>${}</Title>
	<Listeners><![CDATA[
		版本备份监听
		1、#SN-PLAT.StatusVersionBackupListener：处理单据状态；
		2、snsoft.plat.bas.cor.service.impl.CorSourceSaveListener.new?corcodes=SN-HELP.Salorder：取消版本后处理核销数据；
		3、##SN-APPR.ApprVersionBackupListener：审批数据备份（按需）；
	]]></Listeners>
	<TacFormula><![CDATA[
		版本备份Tac监听
	]]></TacFormula>
	<Remark><![CDATA[
		版本备份说明
	]]></Remark>
	<Details>
		<NoBackupTableName>不备份表名</NoBackupTableName>
		<NoBackupColumnName>不备份列名</NoBackupColumnName>
	</Details>
	<Details>
		<NoBackupTableName>不备份表名</NoBackupTableName>
	</Details>
</VersionBackupDef>
```

#### 覆盖标准配置

另请参见[功能扩展](help.html?helpFile=help/SN-PLAT/PlatModule.md#功能扩展)。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<VersionBackupDef
	xmlns="http://www.snsoft.com.cn/schema/VersionBackupDef"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/VersionBackupDef http://www.snsoft.com.cn/schema/VersionBackupDef.xsd">
    <Title>${}</Title>
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
</VersionBackupDef>
```

### 设计

**类图**

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

**时序图**

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
