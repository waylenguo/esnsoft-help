## VOListener

|日期|作者|备注|
|------|------|------|
|2019-12-09|赵玉柱|Validations.md与DefaultValue.md合并|

### 功能划分
1. <font color="red">数据校验</font>
1. <font color="red">默认值</font>
### 更新说明

<font color="red">**原类名为snsoft.plat.bas.sheet.validate.SheetValidateListener与snsoft.plat.bas.sheet.defaultvalue.SheetDefaultValueListener修改合并为snsoft.bas.sheet.VOListener，为不兼容版本更新，Release4.3之前升4.3之后或开发版2019年12月9日之前升开发版之后**</font>
<font color="red">**[数据校验监听配置](#监听配置.)**</font>
<font color="red">**[默认值监听配置](#监听配置)**</font>

### 单据数据校验

|日期|作者|备注|
|------|------|------|
|2019-08-26|冀小雷|初稿|
|2019-10-16|吴小海|重构|
|2019-12-09|赵玉柱|不兼容版本更新|

#### 指南.
##### 概念说明.
单据数据校验功能为VO校验注解的补充,可以在不改变VO注解情况下实现单据数据校验。
此配置更加灵活,只需要调整相关配置文件就可以调整校验规则，方便正式环境实时调整相关校验;

##### 使用说明.
1. 界面定义添加单据校验客户端监听,支持界面定义配置或者系统功能配置方式;
1. 定义单据校验XML配置文件,配置文件路径：resources/cfg/res/validate/*.xml,文件名称为单据号;

###### 监听配置.
原配置
```xml
<!-- 界面定义配置 -->
<uilisteners>
	snsoft.plat.bas.sheet.validate.SheetValidateListener.new?sheetCode=[单据]
</jslistener>
```


```xml
<!-- 系统功能配置 -->
<Functions>
	<Functype>SN-PLAT.UI</Functype>
	<Subtype>2</Subtype>
	<Funcimpl>snsoft.plat.bas.sheet.validate.SheetValidateListener.new?sheetCode=[单据]</Funcimpl>
	<Remark>单据数据校验</Remark>
</Functions>
```
新配置
```xml
<!-- 界面定义配置 -->
<uilisteners>
	snsoft.bas.sheet.VOListener.new?sheetCode=[单据]&defOption=2
</jslistener>
```
```xml
<!-- 系统功能配置 -->
<Functions>
	<Functype>SN-PLAT.UI</Functype>
	<Subtype>2</Subtype>
	<Funcimpl>snsoft.bas.sheet.VOListener.new?sheetCode=[单据]&amp;defOption=1&amp;uiOption=uiname1,uiname2:option1;uiname3,uiname4:option2</Funcimpl>
	<Remark>单据数据校验</Remark>
</Functions>
```

|参数名称|参数说明|
|----|---|
|sheetCode|与原有snsoft.plat.bas.sheet.validate.SheetValidateListener含义相同，单据号|
|uiOption|界面id对应的启用功能，1表示启用单据校验功能,2标识启用单据默认值功能，若不配置表示启用[defOption]|
|defOption|若uiOption未配置当前界面id对应的启用功能，则按照此配置启用，默认值未3，全启用|


###### XML配置.
```xml

<?xml version="1.0" encoding="UTF-8"?>
<SheetValidationDef xmlns="http://www.snsoft.com.cn/schema/SheetValidationDef.xsd">
	<Remark><![CDATA[
		当前配置说明
	]]></Remark>
	<Validations tblname="表名" colname="列名">
		<ValidRule><![CDATA[
			校验规则：
			1、格式：[{},{}...]
			2、支持的校验规则：
			{Type:'NotNull',message:'**不可为空'}
			{Type:'Pattern',regexp:'正则',message:'**格式不正确'}
			{Type:'Min/DecimalMin',value:5,message:'**最小值不能小于5'}
			{Type:'Max/DecimalMax',value:5,message:'**最大值不能大于5'}
			{Type:'Length',max:5,message:'**最大值长度不能超过5'}
		]]></ValidRule>
		<Remark><![CDATA[
			当前列说明 注意事项：已删除,szChChar:2参数
		]]></Remark>
	</Validations>
</SheetValidationDef>
```

#### 手册.	

1. 单据数据校验配置请参见对应的Schema文件【[SheetValidationDef.xsd](do/BrowseSchema?schema=SheetValidationDef.xsd)】；	
		
#### 实践.	

参考配置文件
	
```xml
<?xml version="1.0" encoding="UTF-8"?>
<SheetValidationDef xmlns="http://www.snsoft.com.cn/schema/SheetValidationDef" xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetValidationDef http://www.snsoft.com.cn/schema/SheetValidationDef.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<Remark>销售合同校验规则</Remark>
	<Validations tblname="plat_salorder" colname="status">
		<ValidRule>[{type:"Length",max:2}]</ValidRule>
		<Remark>状态</Remark>
	</Validations>
	<Validations tblname="plat_salorderg" colname="unit">
		<ValidRule>[{type:"Length",max:3}]</ValidRule>
		<Remark>单位</Remark>
	</Validations>
</SheetValidationDef>
```
	
#### 设计.

##### 类图.

@startuml
Title 单据数据校验类图

comm.SheetValidateServiceFactory ..> comm.ValidateService

comm.ValidateService <|.. comm.ValidateServiceImpl
comm.ValidateServiceImpl <|-- comm.SheetValidateServiceImpl

comm.ConstraintDefFactory <.. comm.SheetValidateServiceImpl

ui.ItemConfigService <.. ui.SheetValidateListener
ui.ItemConfigFactory <.. ui.SheetValidateListener
comm.SheetValidateServiceFactory <.. ui.VOListener

interface def.SheetValidateDefService {
	// 定义维护服务
	+ List<SheetValidationDef> queryUI();
	+ void saveUI();
	+ List<SheetValidation> queryDetailUI();
	+ void syncToDB(String sheetCode);
}

interface comm.SheetValidateServiceFactory {
	+ SheetValidationDef getSheetValidationDef(String sheetCode);
	+ ValidateService newSheetValidateService(String sheetCode);
}

interface comm.ValidateService {
}

class comm.ValidateServiceImpl {
}

class comm.SheetValidateServiceImpl {
}

interface comm.ConstraintDefFactory {
	+ List<ConstraintDef<?, ?>> newConstraintDefs(vld);
}

class ui.VOListener {
	+ void initComponent();
}

interface ui.ItemConfigService {
	+ Map<String,ItemConfig> getItemConfigs();
}

interface ui.ItemConfigFactory {
	+ ItemConfig newItemConfig(vld);
}

@enduml

##### 时序图.
@startuml
Title 单据数据校验时序图

actor 界面初始化

participant uilis   as "ui.VOListener"
participant itemsvc as "ItemConfigService"
participant vldfac  as "SheetValidateServiceFactory"
participant itemfac as "ItemConfigFactory"

界面初始化 -> uilis : [initComponent]
activate uilis
	uilis -> itemsvc : [getItemConfigs] \nVO注解校验规则
	activate itemsvc
	itemsvc -> uilis
	deactivate itemsvc
	
	uilis -> vldfac : [getSheetValidationDef] \n获取校验配置
	activate vldfac
	vldfac -> uilis
	deactivate vldfac
	
	uilis -> itemfac : [newItemConfig] \n校验配置规则
	activate itemfac
	itemfac -> uilis
	deactivate itemfac
uilis -> 界面初始化
deactivate uilis
@enduml



### 默认值
|日期|作者|备注|
|------|------|------|
|2018-09-28|冀小雷|初稿|
|2018-11-21|顾中超|完善|
|2019-10-11|吴小海|合并、重构|

#### 功能介绍

N9B默认值设置，包含：VO默认值、单据默认值设置；用户单据或者界面打开默认值设置；

#### VO默认值

通过注解类snsoft.api.dft.DefaultValue，为实体字段设置默认值。

##### 属性说明
- value
```
默认值设置，例如：@DefaultValue(value = "10") 
```
- uivalue
```
对应的客户端默认值对象，例如：@DefaultValue(value = "CURTIME", uivalue = "new Xjs.dx.util.CurrentDateDefaultValue({})") 
 一般不需要在注解中指定，仅用于底层程序通过“value”设置转换为“uivalue”设置对应的客户端默认值对象
如果不需要转换时需定义 @DefaultValue(value = "CURTIME", uivalue = SnsoftConst.NullValue) 
不需要转换的情况现在仅用于 修改人、修改时间使用 
```
- processBean
```
用于特殊的默认值处理。
默认值处理Bean，必须实现接口snsoft.api.dft.DefaultValueBean。
```
- fix
```
 固定值，例如：@DefaultValue(value = "1", fix = true)
	1、插入时设置值；
	2、禁止修改；
	不能用于变量，只能是常量默认值。
```

##### 常用配置
配置列默认值可用注解@DefaultValue("")，例如：@DefaultValue("10")，用的比较多的时候是用词注解来配置编码规则。
编码规则支持的方式有：
1. 当前服务器时间：
```
(1) 不带时分秒
客户端类：new Xjs.dx.util.CurrentDateDefaultValue({serverTime:true})
此时：value=CURTIME
示例：
	/**合同日期*/
	@DefaultValue("CURDATE")
	private Date odate;
(2) 带时分秒
此时：value=CURTIME
示例：
	/**最后修改时间*/
	@DefaultValue(value = "CURTIME", uivalue = SnsoftConst.NullValue)
	private Date modifydate;
```
1. 分码：
```
(1)规则：
客户端类： new Xjs.dx.util.MakeCodeDefaultValue({codeLike:'Prefix_____',allocMode:1})
此时：value=AutoAlloc:Prefix_____
示例 ：
	@DefaultValue("AutoAlloc:Prefix_____")//按下划线个数生成对应位数的自增编码
(2)整型：
客户端类： new Xjs.dx.util.MakeCodeDefaultValue({allocMode:1})
此时：value=AutoAlloc:
示例 ：
	@DefaultValue("AutoAlloc:")
```
1. 编码规则：
```
客户端类： new Xjs.dx.util.AccodeValue({accode:'',refColumns:[...]})
此时：value=Accode:accode (accode为编码规则名)
示例 ：
	/**客户编码*/
	@Id
	@Column
	@DefaultValue("Accode:help_erp.ccode")//help_erp.ccode.xml编码规则配置文件
	private String ccode;
```
1. 单据编码规则：
详细参见 **单据默认值**defaultvalue
```
客户端类： new Xjs.dx.util.SheetAccodeValue({sheetcode:''})
此时：value=SheetAccode:sheetcode (sheetcode为单据号，需要对应一个编码规则。)
示例 ：
	/**客户编码*/
	@Id
	@Column
	@DefaultValue("SheetAccode:help_erp.ccode")
	private String ccode;
```
1. 部门：
```
此时：value=UCode:Bcode
示例：
	/**部门*/
	@Column
	@DefaultValue("UCode:Bcode")
	private String bcode;
```
1. 人员：
```
此时：value=UCode:Wcode
示例：
	/**人员*/
	@Column
	@DefaultValue("UCode:Wcode")
	private String wcode;
```
1. 对应的运维部门：
```
此时：value=UCode:Limbcode
示例：
	/**运维部门*/
	@Column
	@DefaultValue("UCode:Limbcode")
	private String limbcode;
```
1. 商户：
```
此时：value=UCode:Cuicode
示例：
	/**商户*/
	@Column
	@DefaultValue("UCode:Cuicode")
	private String cuicode;
```
1. 用户编码：
```
此时：value=UCode:Usercode
示例：
	/**用户编码*/
	@Column
	@DefaultValue("UCode:Usercode")
	private String usercode;
```
1. ObjectId（针对MongoDB主键）：
```
客户端类：new Xjs.dx.util.ObjectIdValue({})
此时：value=ObjectId
示例：
	@DefaultValue("ObjectId")
	private String id;
```
1. 拷贝主表字段：
```
此时：value=CopyMaster:主表字段名
示例：
	/**订单ID*/
	@Column
	@DefaultValue("CopyMaster:orderid")
	private String orderid;
```
1. 子表整数自增：
```
客户端类：new Xjs.dx.util.AutoIncIntDefaultValue({delta:10"})  //delta为序号间隔
此时：value=AutoInc:间隔
示例：
	/**序号*/
	@DefaultValue("AutoInc:10")  //序号按照10位递增
	private Integer ord;
```
1. 自定义默认值：
```
此时：processBean=BeanId   //BeanId为实现了snsoft.api.dft.DefaultValueBean接口的Service Bean ID
示例：
	类：snsoft.bas.sheet.busi.entity.BusiVO
	@Column
	@DefaultValue(processBean = "SN-CORE.SheetInfoDefaultValue")
	private String sheetcode;
```
1. 数据库序列默认值
```java
@Id
@Column
@DefaultValue("SEQ:seq_tbl_col")
private Integer sid;
```

#### 单据默认值

##### 单据默认值说明

1. 单据默认值配置请参见对应的Schema文件【[SheetDefaultValueDef.xsd](do/BrowseSchema?schema=SheetDefaultValueDef.xsd)】；
1. 此功能为VO校验注解的补充；
1. 配置文件名称为单据号；
1. 配置文件路径：resources/cfg/res/defaultvalue/*.xml；

##### 监听配置

原配置

```xml
<!-- 界面定义配置 -->
<uilisteners>
	snsoft.plat.bas.sheet.defaultvalue.SheetDefaultValueListener.new?sheetCode=[单据]
</jslistener>

<!-- 系统功能配置 -->
<Functions>
	<Functype>SN-PLAT.UI</Functype>
	<Subtype>2</Subtype>
	<Funcimpl>snsoft.plat.bas.sheet.defaultvalue.SheetDefaultValueListener.new?sheetCode=[单据]</Funcimpl>
	<Remark>单据默认值</Remark>
</Functions>
```
新配置

```xml
<!-- 界面定义配置 -->
<uilisteners>
	snsoft.bas.sheet.VOListener.new?sheetCode=[单据]&amp;funcOptions=2
</jslistener>

<!-- 系统功能配置 -->
<Functions>
	<Functype>SN-PLAT.UI</Functype>
	<Subtype>2</Subtype>
	<Funcimpl>snsoft.bas.sheet.VOListener.new?sheetCode=[单据]&amp;defOption=2&amp;uiOption=table1,table2:2;table3,table4:2</Funcimpl>
	<Remark>单据默认值</Remark>
</Functions>
```

|参数名称|参数说明|
|----|---|
|sheetCode|与原有snsoft.plat.bas.sheet.defaultvalue.SheetDefaultValueListener含义相同，单据号|
|uiOption|界面id对应的启用功能，1表示启用单据校验功能,2标识启用单据默认值功能，若不配置表示启用[defOption]|
|defOption|若uiOption未配置当前界面id对应的启用功能，则按照此配置启用，默认值未3，全启用|

##### XML配置
```xml
<?xml version="1.0" encoding="UTF-8"?>
<SheetDefaultValueDef xmlns="http://www.snsoft.com.cn/schema/SheetDefaultValueDef" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetDefaultValueDef http://www.snsoft.com.cn/schema/SheetDefaultValueDef.xsd ">
	<Remark><![CDATA[
		说明
	]]></Remark>
	<Defaults tblname="表名" colname="列名">
		<DefaultValue>默认值</DefaultValue>
		<Remark><![CDATA[
		说明
		]]></Remark>
	</Defaults>
</SheetDefaultValueDef>

```

##### 类图

@startuml
Title 单据数据校验类图

comm.SheetDefaultValueService <.. comm.SheetDefaultValueServiceFactory
comm.DefaultValueService <--* comm.SheetDefaultValueService
comm.SheetDefaultValueService <.. ui.VOListener

interface def.SheetDefaultValueDefService {
	// 定义维护服务
	+ List<SheetDefaultValueDef> queryUI();
	+ void saveUI();
	+ List<SheetDefaultValue> queryDetailUI();
	+ void syncToDB(String sheetCode);
}

interface comm.SheetDefaultValueServiceFactory {
	// 工厂
	+ SheetDefaultValueDef getSheetDefaultValueDef(String sheetCode);
	+ SheetDefaultValueService newDefaultValueService(String sheetCode);
}

interface comm.DefaultValueService {
	+ Map<String,JSExpression> getUIDefaultValues(Class<?> clazz);
	+ void setDefaultValues(Object[] vos, boolean recursive);
}

interface comm.SheetDefaultValueService {
	+ Map<String, JSExpression> getSheetUIDefaultValues(Class<?> clazz);
	+ void setSheetDefaultValues(VO[] vos, boolean recursive);
}

class ui.VOListener {
	+ void initComponent();
}
@enduml

##### 时序图
@startuml
Title 单据默认值时序图

actor 调用者

participant uilis as "ui.VOListener"
participant fac   as "SheetDefaultValueServiceFactory"
participant uisv  as "SheetDefaultValueService"
participant vosv  as "DefaultValueService"

调用者 -> uilis : [initComponent] \n界面初始化
activate uilis
	uilis -> fac : [newDefaultValueService] \n创建默认值服务
	activate fac
	fac -> uilis
	deactivate fac
	
	uilis -> uisv : [getSheetUIDefaultValues] \n获取默认值
	activate uisv
		uisv -> vosv : [getUIDefaultValues] \n获取VO默认值
		activate vosv
		vosv -> uisv
		deactivate vosv
	uisv -> uilis
	deactivate uisv
	
	uilis -> uilis : [addInitValues] \n设置默认值
	activate uilis
	deactivate uilis
uilis -> 调用者
deactivate uilis
@enduml
