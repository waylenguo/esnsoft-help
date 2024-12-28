## 单据复制

|日期|作者|备注|
|------|------|------|
|2018-08-27|冀小雷|初稿|
|2019-10-11|吴小海|重构|

### 指南	
#### 概念说明
N9B平台支持单据复制功能，实现单据复制;默认单据复制编码与单据编码相同;可以通过配置表进行配置对应关系;

#### 使用说明
		
##### 界面监听配置

```xml
<!--
JS监听类：
1、界面需要显示布局单据复制监听按钮，命令号=sheetCopy；
2、单据复制数据以当前行内码为标准；
3、单据复制完成后，调用对应的单据外码穿透；
-->
<jslistener><![CDATA[
	#new snsoft.plat.bas.sheetcopy.SheetCopyListener({sheetCode:'XXX'})
]]></jslistener>
```

##### 系统功能配置

```xml
<Groups id="单据附加功能">
	<Functions>
		<Functype>SN-PLAT.JS</Functype>
		<Subtype>2</Subtype>
		<Funcimpl>#new snsoft.plat.bas.sheetcopy.SheetCopyListener({sheetCode:'XXX'})</Funcimpl>
		<Remark>单据复制功能客户端监听类</Remark>
	</Functions>
</Groups>
```

##### XML配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SheetCopyDef
	xmlns="http://www.snsoft.com.cn/schema/SheetCopyDef" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetCopyDef http://www.snsoft.com.cn/schema/SheetCopyDef.xsd">
    <Title>${}</Title>
    <ForceCopyColumns><![CDATA[
		强制拷贝字段（不受默认值影响）
    ]]></ForceCopyColumns>
    <Listeners><![CDATA[
		单据复制监听
    ]]></Listeners>
    <TacFormula><![CDATA[
		单据复制Tac监听
    ]]></TacFormula>
    <Remark><![CDATA[
		当前单据复制说明
    ]]></Remark>
    <Details>
        <NotCopyTableName>不复制表名</NotCopyTableName>
        <NotCopyColumnName>不复制列名</NotCopyColumnName>
    </Details>
    <Details>
        <NotCopyTableName>不复制表名</NotCopyTableName>
        <NotCopyColumnName>*</NotCopyColumnName>
    </Details>
</SheetCopyDef>
```


##### 覆盖标准配置

另请参见[功能扩展](help.html?helpFile=help/SN-PLAT/PlatModule.md#功能扩展)。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SheetCopyDef 
	xmlns="http://www.snsoft.com.cn/schema/SheetCopyDef" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetCopyDef http://www.snsoft.com.cn/schema/SheetCopyDef.xsd">
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
</SheetCopyDef>
```

### 手册	

1. 单据复制功能配置包含客户端监听以及单据复制配置文件2部分;其中客户端监听配置可在界面中配置或者加入系统功能(二选一方式);
1. 单据复制配置文件路径：
	* 标准配置：resources/cfg/res/sheetcopy/*.xml；
	* 覆盖配置：resources/cfg/res/sheetcopy/[sccode]/*.xml；
1. 单据复制配置请参见对应的Schema文件【[SheetCopyDef.xsd](do/BrowseSchema?schema=SheetCopyDef.xsd)】；
		
### 实践	
	
### 设计


#### 类图

@startuml
Title 单据复制功能类图

sv.SheetCopyListener <|.. ScriptSheetCopyListener

SheetCopyFactory <.. SheetCopyController
SheetCopyService <.. SheetCopyController
SheetCopyService <.. SheetCopyFactory

SheetCopyService <|.. SheetCopyServiceImpl

class xjs.SheetCopyListener {
	# void oncmd_sheetCopy(Table table);
}

interface SheetCopyDefService {
	+ List<SheetCopyDef> queryUI(SheetCopyDefParams params);
	+ void saveUI(List<SheetCopyDef> records);
	+ List<SheetCopyDetail> queryDetailUI(SheetCopyDetailParams params);
	+ void syncToDB(String sccode);
}

interface SheetCopyController {
	+ T sheetCopy(String sheetCode, String innerCode);
}

interface SheetCopyFactory {
	+ SheetCopyDef getSheetCopyDef(String sccode);
	+ SheetCopyDef getSheetCopyDef(String sheetCode, String innerCode);
	+ <T> SheetCopyService<T> newSheetCopyService(SheetCopyDef scDef);
}

interface SheetCopyService {
	+ T sheetCopy(String sheetCode, String innerCode)
}

class SheetCopyServiceImpl {
	# SheetCopyDef scDef;
	
}

interface sv.SheetCopyListener {
	+ void afterClone(SheetCopyEvent event);
	+ void beforeSave(SheetCopyEvent event);
	+ void afterSave(SheetCopyEvent event);
}

class ScriptSheetCopyListener {
	# String script;
}
@enduml

#### 时序图

@startuml
Title 单据复制时序图

actor 客户端

participant jslis   as "js.SheetCopyListener"
participant ctrl    as "SheetCopyController"
participant fac     as "SheetCopyFactory"
participant service as "SheetCopyService"

客户端 -> jslis : [oncmd_sheetCopy]\n执行单据复制
activate jslis
	jslis -> ctrl : [sheetCopy]\nUI层服务交互
	activate ctrl
		ctrl -> fac : [getSheetCopyDef]\n获取单据复制配置对象
		activate fac
		fac -> ctrl : 
		deactivate fac
		ctrl -> fac : [newSheetCopyService]\n获取单据复制服务实现
		activate fac
		fac -> ctrl : 
		deactivate fac
		ctrl -> service : [sheetCopy]\n执行单据复制
		activate service
		service -> ctrl : 返回单据复制信息
		deactivate service
	ctrl -> jslis : 
	deactivate ctrl
jslis -> 客户端 :
deactivate jslis
@enduml