## 码表映射关系

|日期|作者|备注|
|------|------|------|
|2019-06-21|聂明笛|初稿|
|2019-10-11|聂明笛|修改|

### 指南	

#### 概念说明

用于处理系统间含义相同，编码不同的情况下，编码的转换。
码表映射关系分为平台和商户两类，由两个维护界面进行管理，需要维护两套码表定义码表以及两套码表映射关系。

#### 使用说明

通过调用snsoft.bastool.rela.service.CodeRelaService中的服务可获得对应编码；VO上有对应码表注解时，可调用接口直接对VO值进行替换。

### 手册

#### 码表定义码表数据维护

商户码表定义数据路径：res/cuicodetbldef/CuiCodeTblDef*.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<CuiCodeTbls xmlns="http://www.snsoft.com.cn/schema/CuiCodeTblDef"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/CuiCodeTblDef http://www.snsoft.com.cn/schema/CuiCodeTblDef.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<CuiCodeTbl code="FT-CODE.Wcode" name="人员（产品标准）" options4="0x20" aidinputer="Xjs.ui.SelectCodeDialog.new" options1="0" options2="0"
		cmprops="loadCommand:'sv-FT-CODE.FTWcodeDataService.getValues'" aiprops="inputBgLabel:'姓名/身份证号/手机号码'"
		cmparams="FILTER:'snsoft.bas.sheet.bcode.codedata.LimbcodeFilter.new?sheetcode=FT-CODE.Bcode.Wcode;',cuicode:'%QueryParam(cuicode)',opids:'R,C'" />
	<CuiCodeTbl code="FT-CODE.Users" name="用户（产品标准）" options4="0x20" aidinputer="Xjs.ui.SelectCodeDialog.new" options2="0"
		cmprops="loadCommand:'sv-FT-CODE.FTUserCodeDataService.getValues'" options1="0" aiprops="inputBgLabel:'姓名/英文名'"
		cmparams="FILTER:'snsoft.bas.sheet.bcode.codedata.LimbcodeFilter.new?sheetcode=FT-CODE.Bcode.InUsers;',cuicode:'%QueryParam(cuicode)'" />
	<CuiCodeTbl code="FT-CODE.Gwcode" name="岗位（产品标准）" options4="0x020" cmprops="" options1="0" options2="0" aidinputer="" aiprops=""
		cmparams="FILTER:'snsoft.bas.sheet.bcode.codedata.LimbcodeFilter.new?sheetcode=FT-CODE.Bcode.Gwcode;',cuicode:'%QueryParam(cuicode)',SQLFILTER:&quot;status='70'&quot;" />
</CuiCodeTbls>
```

平台码表定义数据路径：res/codetbldef/CodeTblDef*.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<CodeTbls xmlns="http://www.snsoft.com.cn/schema/CodeTblDef"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/CodeTblDef http://www.snsoft.com.cn/schema/CodeTblDef.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<CodeTbl code="FT-CODE.Continent" name="大洲" options1="0" options2="0" options4="0x030"
		aiprops="promptInfo:'',cellTextFmt:['${contcode}-${contname}']" aidinputer="Xjs.ui.ComboAidInputer.new" cmparams="" cmprops="" />
	<CodeTbl code="FT-CODE.Ncode" name="国别地区" options1="0" options2="0" options4="0x030" aiprops="promptInfo:'',cellTextFmt:['${ncode}-${nname}']"
		aidinputer="Xjs.ui.ComboAidInputer.new" cmparams="" cmprops="" />
	<CodeTbl code="FT-CODE.CtryProv" name="省（含国别地区）" options1="0" options2="0" options4="0x030"
		aiprops="promptInfo:'',cellTextFmt:['${provcode}-${provname}-${shortname}-${nname}']" aidinputer="Xjs.ui.ComboAidInputer.new" cmparams=""
		cmprops="" />
</CodeTbls>
```

#### 码表映射关系数据维护

码表映射关系维护数据路径：res/rela/CodeRela*.xml
可在维护界面初始化至数据库进行维护

```xml
<?xml version="1.0" encoding="UTF-8"?>
<CodeRelas xmlns="http://www.snsoft.com.cn/schema/CodeRela"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/CodeRela http://www.snsoft.com.cn/schema/CodeRela.xsd"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<Relas relacode="C001" relaname="币种" codetbl="FT-CODE.Fcode" systype="基础数据" remark="说明" aidinputer="Xjs.ui.ComboAidInputer.new"
		aiprops="promptInfo:'',cellTextFmt:['${fcode}-${fname}']" options1="16" options4="48">
		<RelaInfos erpcode="IDR" thdcode="印度尼西亚卢比" />
		<RelaInfos erpcode="JPY" thdcode="日本元" />
		<RelaInfos erpcode="MOP" thdcode="澳门元" />
	</Relas>
</CodeRelas>
```

#### 相关界面

1. 码表映射关系维护(商户)：[90.bastool.CodeRela](uiinvoke/00/zh_CN/theme0/90.bastool.CodeRela.html)
    * 新建/修改：弹出框操作，录入必填字段即可
    * 初始化：将文件中的数据初始化至数据库进行编辑
    * 导入（子表）：可通过Excel导入子表数据
1. 码表映射关系维护(平台)：[90.bastool.CodeRelaComm](uiinvoke/00/zh_CN/theme0/90.bastool.CodeRelaComm.html)
    * 新建/修改：弹出框操作，录入必填字段即可
    * 初始化：将文件中的数据初始化至数据库进行编辑
    * 导入（子表）：可通过Excel导入子表数据

### 设计

**类图**

@startuml
Title 码表映射关系功能类图

class CodeRelaService {
	+ String getBy3Code(String thdcode, String cuicode, String systype, String codetbl);
	+ Map<String,String> getBy3Codes(String[] thdCodes, String cuicode, String systype, String codetbl);
	+ String getByErpCode(String erpCode, String cuicode, String systype, String codetbl);
	+ Map<String,String> getByErpCodes(String[] erpCodes, String cuicode, String systype, String codetbl);
	+ String getBy3Code(String thdcode, String relacode);
	+ Map<String,String> getBy3Codes(String[] thdCodes, String relacode);
	+ String getByErpCode(String erpCode, String relacode);
	+ Map<String,String> getByErpCodes(String[] erpCodes, String relacode);
	+ default <T> void replaceForThd(String systype, Collection<T> vos);
	+ default <T> void replaceForThd(String cuicode, String systype, Collection<T> vos);
	+ <T> void replaceForThd(String cuicode, String systype, Collection<T> vos, TriFunction<VOField,Object,Map<String,String>,Boolean> logicFunc);
	+ default <T> void replaceForERP(String systype, Collection<T> vos);
	+ default <T> void replaceForERP(String cuicode, String systype, Collection<T> vos);
	+ <T> void replaceForERP(String cuicode, String systype, Collection<T> vos, TriFunction<VOField,Object,Map<String,String>,Boolean> logicFunc);
	+ boolean getBySystype(String systype, String cuicode);
	+ CodeRela getCodeRela(String cuicode, String systype, String codetbl);
}
@enduml