## 编码引用

|日期|作者|备注|
|------|------|------|
|2019-01-18|冀小雷|修改|
|2019-10-11|聂明笛|修改|
|2023-06-09|李 军|修改|

### 指南	

#### 概念说明

某些基础码表的编码支持修改或删除，则在修改或删除前需要判断是否被引用及对应的引用信息；同时需要支持修改后的编码替换功能，如客商合并等。
* 支持可配置禁用，由实施决定如何处理；
* 支持实施Tac接入；
* 支持分布式系统调用；
* 提供定义管控界面；
* 提供编码引用查询管控页面；
* 提供编码替换管控页面；
* 提供编码总体替换前和替换后处理接口:CodeRefOverallBean,在配置主表处理服务处配置;
* 提供CodeRefOverallBean默认实现SN-PLAT.CodeRefOverallDefaultBean，支持记录替换日志功能，但需要在主表添加处理服务Bean。

#### 使用说明

* 系统功能配置sv监听
```xml
    <Functions grp="存盘处理">
		<Functype>SN-PLAT.SV</Functype>
		<Subtype>2</Subtype>
		<Funcimpl><![CDATA[Codetbl.md
		    snsoft.plat.bas.coderef.service.impl.CheckRefSVListener.new?refcode=help_ctry.ncode&title=国别地区
		]]></Funcimpl>
		<Remark>删除、修改时，引用检查。</Remark>
	</Functions>
```

* 撤单配置
```xml
<Retracts>
	<BeanName>SN-PLAT.CodeRefRetractBean?[{refcode:'help_ctry.ncode',title:'国别地区'}]</BeanName>
	<Remark><![CDATA[
	撤单编码引用检查
	]]></Remark>
</Retracts>
```
### 手册

#### 配置路径

* 当前系统配置引用路径：cfg/res/coderef/XXX.xml，XXX推荐使用'表名.列名'；
* 其他系统配置引用路径：cfg/res/coderef/配置文件名/XXX.xml；

#### 配置DEMO

```xml
<CodeRefDef disabled="0" title="" xmlns="http://www.snsoft.com.cn/schema/tool-coderef"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/tool-coderef http://www.snsoft.com.cn/schema/tool-coderef.xsd"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	refbean="SN-PLAT.CodeRefOverallDefaultBean" aidinputer="BK.JC.TradAC" replaceable="1">
	<Remark></Remark>
	<CodeRefs sysid="" desccode="" title="" disabled="0">
		<QuerySQL><![CDATA[
			select 1 from salorder where wcode=:code
			select 1 from salorder where wcode=:code {cmfilter}
			select 1 from salorderg where gcode=:code
			select 1 from salorderg g,salorder m where m.salordicode=g.salordicode and gcode=:code {cmfilter} 
			select 1 from salorder where bcode=:code1 and wcode=:code2
		]]></QuerySQL>
		<ReplaceSQL><![CDATA[
			update salorder set wcode=:newcode where wcode=:oldcode
			update salorder set wcode=:newcode where wcode=:oldcode {cmfilter}
			update salorderg set gcode=:newcode where gcode=:oldcode
			update salorderg set gcode=:newcode where gcode=:oldcode and exists(select 1 from salorder where salorder.salordicode=salorderg.salordicode and {cmfilter})
			update salorder set bcode=:newcode1, wcode=:newcode2 where bcode=:oldcode1 and wcode=:oldcode2
		]]></ReplaceSQL>
		<CMFilter>cuicode=:cuicode/m.cuicode=:cuicode</CMFilter>
		<RefBean></RefBean>
		<RefTac><![CDATA[
			proc isCodeUsed(code)
			end proc
			proc replaceCode(oldcode,newcode)
			end proc
		]]></RefTac>
		<Remark></Remark>
	</CodeRefs>
</CodeRefDef>
```

#### 相关界面

1. 定义界面：[SN-PLAT.RefCode.Def](/N9Bhelp/uiinvoke/00/zh_CN/theme0/SN-PLAT.RefCode.Def.html)
	* 参数：定义编码，定义名称；
1. 引用查询界面：[SN-PLAT.RefCode.Query](/N9Bhelp/uiinvoke/00/zh_CN/theme0/SN-PLAT.RefCode.Query.html)
	* 参数：商户（如果启用），定义编码，引用编码；
	* 列：系统号，编码，标题；
1. 编码替换界面：[SN-PLAT.RefCode.Replace](/N9Bhelp/uiinvoke/00/zh_CN/theme0/SN-PLAT.RefCode.Replace.html)
	* 参数：商户（如果启用），定义编码，老编码，新编码；
	* 列：系统号，编码，标题，个数；
		
### 实践	

* 国别地区中引用大洲编码：[大洲](/N9Bhelp/uiinvoke/00/zh_CN/theme0/SN-HELP.Bas.Continent.html)，[国别地区](/N9Bhelp/uiinvoke/00/zh_CN/theme0/SN-HELP.Bas.Country.html)

### 设计

【类图】

@startuml

Title 编码引用功能类图

CodeRefDefService ..> CodeRefDef
CodeRefDefService ..> CodeRef

CodeRefService .left.> CodeRefDistService
CodeRefService ..> CodeRefDef
CodeRefService ..> RefDesc
CodeRefService ..> RepDesc

CodeRefDef o--> CodeRef
RepDesc -up-> RefDesc

CodeRefDistService o-down-> CodeRefBean

CodeRefSqlBean -up-|> CodeRefBean
CodeRefTacBean -up-|> CodeRefBean

interface CodeRefDefService {
	+ List<CodeRefDef> queryDefUI(QueryParams params);
	+ List<CodeRef> queryRefUI(QueryParams params);
	+ List<RefDesc> queryUsedUI(QueryParams params);
	+ List<RepDesc> queryReplaceUI(QueryParams params);
	+ void saveDefUI(List<CodeRefDef> records);
	+ void syncToDB(String refcode);
}

interface CodeRefService {
	+ CodeRefDef getCodeRefDef(String refcode);
	+ boolean isCodeUsed(String refcode,Object code);
	+ List<RefDesc> getRefMessages(String refcode,Object code);
	+ List<RepDesc> replaceCode(String refcode,Object oldcode,Object newcode);
}

interface CodeRefDistService {
	+ boolean isCodeUsed(List<CodeRef> refs,Object code);
	+ List<RefDesc> getRefMessages(List<CodeRef> refs, Object code);
	+ List<RepDesc> replaceCode(List<CodeRef> refs,Object oldcode,Object newcode);
}

interface CodeRefBean {
	+ boolean isCodeUsed(Object code);
	+ long replaceCode(Object oldcode,Object newcode);
}

class CodeRefSqlBean {
	- CodeRef codeRef;
	+ boolean isCodeUsed(Object code);
	+ long replaceCode(Object oldcode,Object newcode);
}

class CodeRefTacBean {
	- CodeRef codeRef;
	+ boolean isCodeUsed(Object code);
	+ long replaceCode(Object oldcode,Object newcode);
}

class CodeRefDef {
	- String refcode;
	- List<CodeRef> refs;
}

class CodeRef {
	- String refcode;
	- String sysid;
	- String desccode;
}

class RefDesc {
	- String sysid;
	- String desccode;
	- String title;
}

class RepDesc {
	- long count;
}

@enduml

【时序图】
@startuml

Title 编码引用功能时序图

actor 调用者

participant sv as "CodeRefService"
participant ds as "CodeRefDistService"
participant bn as "CodeRefBean"

调用者-> sv : [isCodeUsed/replaceCode]
activate sv
	sv -> ds : [isCodeUsed/replaceCode]
	activate ds
		ds -> bn
	ds -> sv
	deactivate ds
sv -> 调用者
deactivate sv

@enduml