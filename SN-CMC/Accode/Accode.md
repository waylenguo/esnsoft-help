## 编码规则

|日期|作者|备注|
|------|------|------|
|2018-09-25|陈泰林、周鹏辉|初稿|
|2019-10-14|聂明笛|修改|

### 指南

#### 概念说明

用于实现为系统中某些字段根据某些规则自动编码，比如数据内码，数据外码等。

#### 使用说明


```xml
<!--界面定义配置UI监听-->
<!-- snsoft.plat.bas.sheet.defaultvalue.SheetDefaultValueListener.new;已经合并到VOListener -->
<uilisteners>
    snsoft.bas.sheet.VOListener.new;
</uilisteners>
<!--系统功能配置UI监听-->
<Functions>
    <Functype>SN-PLAT.UI</Functype>
    <Subtype>2</Subtype>
    <Funcimpl><![CDATA[
    snsoft.bas.sheet.VOListener.new
    ]]></Funcimpl>
    <Remark>VO注解监听</Remark>
</Functions>
<!--
<Functions>
    <Functype>SN-PLAT.UI</Functype>
    <Subtype>2</Subtype>
    <Funcimpl><![CDATA[
    snsoft.plat.bas.sheet.defaultvalue.SheetDefaultValueListener.new
    ]]></Funcimpl>
    <Remark>VO默认值监听 已经合并到VOListener</Remark>
</Functions>
-->
```

* VO类需要注解@DefaultValue设置默认值（只在第一次新增时设置初值）

```java
@DefaultValue("AutoAlloc:________________")//编码16位自增，注意:此方式只能用于调试,不能用于正式系统
@DefaultValue("SheetAccode:"+单据号)//单据编码规则根据编码文件生成（首先找单据对应的编码规则文件，如果单据没有配置编码规则文件使用同单据号同名的编码规则文件）
@DefaultValue("Accode:"+编码规则文件名)//编码规则根据编码文件生成
```

* 自定义编码事例(数据库):

```java
MakeCodeService makeCodeService = SpringBeanUtils.getBeanByName("SN-CORE.MakeCodeService");
MakeCodeParam codeParam = new MakeCodeParam("表名", "字段");
codeParam.setCodeLike(new SimpleDateFormat("yyyyMMdd").format(date) + StrUtils.newString('_', 6));
codeParam.setCount(总共分配多长码);
String[] codes = makeCodeService.makeCodes(codeParam);
```

* 自定义编码事例(MongDB):

```java
MakeCodeService makeCodeService = SpringBeanUtils.getBeanByName("SN-CORE.MakeCodeService");
MakeCodeParam codeParam = new MakeCodeParam("mongo服务名", "表名", "字段");
Date date = DateUtils.getServerDate();
codeParam.setCount(总共分配多长码);
codeParam.setCodeLike(new SimpleDateFormat("yyyyMMdd").format(date) + StrUtils.newString('_', 6));
String[] codes = makeCodeService.makeMGCodes(codeParam);
```

* VO类默认值事例

```java
	DefaultValueService dftService = SpringBeanUtils.getBeanByName("SN-SHEET.DefaultValueService");
	dftService.setDefaultValues(VO[] { vo }, true);
```

自定义设置默认值：实现snsoft.bas.sheet.dft.service.DefaultValueBean<T>接口

#### 规则说明

<table>
	<tr>
		<th align='center' colspan='3'>编码【规则(code like)】说明</th>
	</tr>
	<tr>
		<th align='center'>类型</th>
		<th align='center'>脚本</th>
		<th align='center'>说明</th>
	</tr>
	<tr>
		<td>字符：PREFIX001</td>
		<td>proc getCodeLike(refData)<br/>
			&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return &quot;PREFIX&quot;+inc(3)<br/>
			end proc
		</td>
		<td>
			前缀PREFIX+三位自增(从1开始)
		</td>
	</tr>
	<tr>
		<td>字符：PREFIX100</td>
		<td>proc getCodeLike(refData)<br/>
			　　return &quot;PREFIX&quot;+inc(3)<br/>
			end proc<br/>
			proc getDigset(refData)<br/>
			　　digset = new char[3][0]<br/>
			　　//第一位的自增顺序为123456789（跳过0）<br/>
			　　digset[2]=&quot;123456789&quot;.toCharArray()<br/>
			　　return digset<br/>
			end proc
		</td>
		<td>
			前缀PREFIX+三位自增(从100开始)
		</td>
	</tr>
	<tr>
		<td>整形：1</td>
		<td>
		</td>
		<td>
			自增(从1开始)
		</td>
	</tr>
	<tr>
		<td>整形：100</td>
		<td>
			proc getDigset(refData)<br/>
			　　digset = new char[3][0]<br/>
			　　//第一位的自增顺序为123456789（跳过0）<br/>
			　　digset[2]=&quot;123456789&quot;.toCharArray()<br/>
			　　return digset<br/>
			end proc
		</td>
		<td>
			自增(从100开始)
		</td>
	</tr>
</table>

**注：codelike为空，认为是整形编码**

### 手册

#### 编码规则【文件】

* 路径：/res/accode/系统号-*.xml;
* 参见[Accode.xsd](do/BrowseSchema?schema=Accode.xsd);

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Codelike xmlns="http://www.snsoft.com.cn/schema/Accode" xsi:schemaLocation="http://www.snsoft.com.cn/schema/Accode http://www.snsoft.com.cn/schema/Accode.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<name>编码规则</name>
	<!--配置此项表示为指定的MongDB地址分码，不配置此项表示为数据库分码-->
	<mongobean>mongoTemplate</mongobean>
	<codeinfo><![CDATA[ 
		// 返回编码规则（遇到时间格式yyyyMMddHHmmss会自动变成当前时间，遇到inc(leng)会变成自增leng位）
		// 针对日期时间 2018-12-31 13:24:56
		// yyyy=2018
		// yy=18
		// MM=12
		// dd=31
		// HH=13
		// mm=23
		// ss=56
		proc  getCodeLike(refData)
			return "yyyyMMdd"+inc(3)
		end proc
		//返回过滤条件（一般不需要加过滤条件,下例为按商户分组编码）
		proc getExtFilter(refData)
			if startCuicode()
                userSession = snsoft.context.AppContext.getUserSession(true)
                cuicode = userSession.getUserCuicode()
                filter = add(filter,eq("cuicode",cuicode))
            end if
            return filter
		end proc
		// 指定自增规则
		proc getDigset(refData)
			digset = new char[3][0]
			// 三位自增，指定第一位自增顺序为123456789
			digset[2] = "123456789".toCharArray()
			return digset
		end proc
	]]></codeinfo>
	<remark><![CDATA[ 
		说明
	]]></remark>
</Codelike>
```

* 维护界面：[编码规则【文件】](uiinvoke/00/zh_CN/theme0/90.sheet.AccodeFile.html)

#### 编码规则【数据库】

* 维护界面：[编码规则-数据库](uiinvoke/00/zh_CN/theme0/90.sheet.AccodeDB.html)

* 编码规则参数说明：

```
编码：主键，只读，必录项，新增记录存盘时按5位流水号自动分配
名称：编码规则名称
商户分组：勾选项，新增记录时缺省选中，选中时则分配流水号时缺省在当前商户数据范围内进行分配；若未选中则分配流水号时在不区分商户的所有数据范围内进行分配
分组过滤条件：大文本，定义分配流水号时数据过滤范围，即定义分配最大流水号时数据的SQL过滤条件
运维部门：必录项，权限字段，显示名，选择权限范围内有效的末级运维部门
状态：编码规则使用状态
```

* 规则定义参数说明：

```
序号：自增
类型：10：常量、20：变量、30：递增、40：TAC、50：手录
位数：若“类型”选择‘30：递增’或‘50：手录’时设置为必录项，否则非必录项，禁止负数及零，只能录入正整数
参考值：缺省只读，根据“类型”的选择做如下处理：若“类型”为‘10：常量’时，“参考值”列设置为必录项；若“类型”为‘20：变量’时，“参考值”列设置为必录项，下拉选择： CURDATE:当前日期（8位）、CURYEAR4:当年（4位）、CURYEAR2:当年（2位）、CURMONTH:当月（2位）、CURDAY:当日（2位）；若“类型”为‘40：TAC’时，“参考值”列设置为必录项；若“类型”为‘30：递增’或‘50：手录’或空值时，“参考值”列设置为只读。
不参与递增：选中后，表示该规则定义分配的编码号段在分配流水号（若定义了递增类型规则）时不参与数据范围限定
```

* 使用单据参数说明：

```
序号：自增；
单据类型：选择适用单据；
适用公司：选择使用公司。
适用部门：选择使用不闷；
编码字段：记录该编码规则适用的字段名；
TAC依赖字段：定义“匹配编码规则TAC”及‘40：TAC’类TAC规则依赖的单据主表字段；
匹配编码规则TAC：单据匹配编码规则时，单据类型、适用部门、适用公司必须匹配上外，若定义了本列值，则本TAC执行值必须返回TRUE才算匹配上；
界面编码字段可改：本列值勾选上则该编码字段在单据界面中可修改状态下需设置为可修改，否则需设置为只读；
界面编码字段可改校验：“界面编码字段可改”若勾选上，需定义存盘时该编码字段值与编码规则检验时的策略。缺省只读不必录，显示名，下拉选择：10：不校验、20：提示、30：错误（{校验选项}系统字典）；
不启用：勾选项，若选中则对应的单据类型、适用公司、适用部门，不启用自动分码，即编码字段手动录入。
```

#### 单据编码规则

* 单据编码规则是根据单据类型及对应的业务数据查找对应的编码规则，默认查找与单据号相同的编码规则。
* 一个单据可以对应多个编码规则；
* 参见[单据/编码规则关系](uiinvoke/00/zh_CN/theme0/90.sheet.SheetAccode.html)；
* 支配JSON格式的配置文件：res/sheetaccode/[sheetcode].json；
	```json
	[
		{
			"accode": "编码规则编码",
			"colname": "分码列",
			"refcolumns":"分码依赖列，可以为空",
			"bcode":"适用部门,可以为空"
		}
	]
	```
* 编码规则系统功能监听（需要依赖sn-plat）
	```xml
	<!--服务端存盘监听-->
	<Functions>
		<Functype>SN-PLAT.SV</Functype>
		<Subtype>2</Subtype>
		<Funcimpl><![CDATA[
		snsoft.plat.bas.busi.service.impl.SheetAccodeSVListener.new?sheetCode=SN-HELP.Salorder&colname=salordcode
		]]></Funcimpl>
		<Remark>单据编码规则校验</Remark>
	</Functions>
	<!--UI层监听-->
	<Functions>
		<Functype>SN-PLAT.UI</Functype>
		<Subtype>2</Subtype>
		<Funcimpl><![CDATA[
			snsoft.plat.bas.busi.SheetAccodeUIListener.new?sheetCode=SN-HELP.Salorder&colname=salordcode
		]]></Funcimpl>
		<Remark>单据编码规则初始化</Remark>
	</Functions>
	<Functions>
		<Functype>SN-PLAT.JS</Functype>
		<Subtype>2</Subtype>
		<Funcimpl>#new snsoft.plat.bas.busi.SheetAccodeJSListener({colname:'salordcode'})</Funcimpl>
		<Remark>单据编码规则逻辑处理</Remark>
	</Functions>
	```


#### 分码信息查询

* 出现分码错误时，比如主键重复、分码错误等，使用此分码查询检测分码异常的原因：[分码信息查询](uiinvoke/00/zh_CN/theme0/90.QueryCodes.html)

#### 分码最大值信息

* 启用分码信息中间表[mkcode]，用于解决业务表数据量过大时查询初始分码效率问题。
* 需要开启系统选项 DATA.EnableMakeCodeTable

#### 序列分码

```java
// 1. 创建序列
String sql = db.getDialect().buildCreateSequence(seqname,start,increment,minval,maxval,cachecount,cycle);
db.executeUpdate(sql);

// 2. 获取下一个值
int nextValue = StrUtils.obj2int(db.query1(db.getDialect().nextValueSQL(seqname)));
int nextValue = AccodeService.getSeqNextValue(table,seqname);

// 3. VO属性配置(seq_tbl_col为序列名称)
@Id
@Column
@DefaultValue("SEQ:seq_tbl_col")
private Integer sid;
```


#### 右键配置导出

1. 支持右键数据导出，将当前配置界面的有效数据（启用）导出成一个文件（XML/南北标准配置文件），实施人员在数据库中配置标准数据，通过本功能将这些标准配置数据导出成文件用来打包发布。

1. 配置存放目录：
    审批链定义：snsoft/archive/init/accode/xxx.zip

#### 右键初始化

配置表数据为空时才可以初始化数据，数据来源是右键导出的配置。

### 设计

@startuml
Title 编码规则类图

xjs.AccodeValue ..> AccodeService
AccodeService ..> MakeCodeService

class xjs.AccodeValue {
	+ int getAllocMode(DataSet dataSet);
	+ Object getDefaultValue(DataSet dataSet, int columnIndex);
	+ JSObject<Object> parseRefData(DataSet dataSet, String table, String column);
	+ String getCodeLike(DataSet dataSet, String column);
	+ Object makeCode(String table, String column, String accode, JSObject<Object> refVO);
	+ String getAccode(DataSet dataSet, String column);
	+ String[] getRefColumns(DataSet dataSet, String column);
}

class AccodeService {
	+ AccodeInfo buildCodeLike(String accode, Object refVO, int count);
	+ AccodeInfo buildCodeLike(String accode, Object refVO);
	+ AccodeInfo buildCodeLike(String accode, Map<String,Object> refData, int count);
	+ AccodeInfo buildCodeLike(String accode, Map<String,Object> refData);
	+ String getCodeLike(String accode, Map<String,Object> refData);
	+ String makeCode(String table, String column, String accode, Object refVO);
	+ String[] makeCodes(String table, String column, int count, String accode, Object refVO);
	+ String makeCode(String table, String column, String accode, Map<String,Object> refData);
	+ String[] makeCodes(String table, String column, int count, String accode, Map<String,Object> refData);
}

class MakeCodeService {
	+ void reset(MakeCodeParam param);
	+ void reset(String key);
	+ void resetAll();
	+ java.util.Map<String,Object> getCodeBuffer();
	+ String makeCode(MakeCodeParam param);
	+ String[] makeCodes(MakeCodeParam param);
	+ String makeMGCode(MakeCodeParam param);
	+ String[] makeMGCodes(MakeCodeParam param);
	+ int makeCodeAsInt(MakeCodeParam param);
	+ int[] makeCodesAsInt(MakeCodeParam param);
	+ long makeCodeAsLong(MakeCodeParam param);
	+ long[] makeCodesAsLong(MakeCodeParam param);
	+ String currentCode(MakeCodeParam param);
	+ int currentCodeAsInt(MakeCodeParam param);
	+ long currentCodeAsLong(MakeCodeParam param);
	+ BatchCodeMaker batchMacker(long total, int numPerTime, MakeCodeParam param);
}

class DefaultValueService {
	+ Map<String,String> getSourceValues(Class<?> clazz);
	+ Map<String,Object> getDefaultValues(Class<?> clazz);
	+ Map<String,JSExpression> getUIDefaultValues(Class<?> clazz);
	+ void setDefaultValues(Object[] vos, boolean recursive);
	+ void setDefaultValues(Object[] vos, boolean recursive, boolean force);
	+ void setDefaultValues(Object master, Object[] vos, boolean recursive, boolean force);
	+ void setDefaultValues(Object master, Object[] vos, boolean recursive, boolean force, String[] cols);
	+ void setDefaultValues(List<?> vos, boolean recursive);
	+ void setDefaultValues(List<?> vos, boolean recursive, boolean force);
	+ void setDefaultValues(Object vo, boolean recursive);
	+ void setDefaultValues(Object vo, boolean recursive, boolean force);
	+ void assertNoFixColumnChanged(Object[] vos);
}

@enduml


**时序图**

@startuml
Title 编码规则时序图
actor 客户端
participant client as "xjs.AccodeValue"
participant server4 as "AccodeService"
participant server5 as "MakeCodeService"
participant server1 as "VOListener"
participant server2 as "DefaultValueService"
participant server3 as "DefaultValueBeanFactory"


客户端 -> server1 : 页面加载，初始化，initComponent()
activate server1

server1 --> server2 : getUIDefaultValues：加载解析默认值注解
activate server2

server2 --> server3 : newDefaultValueBean：解析默认注解，构建默认值对象
activate server3

server3 --> server2 : 返回默认值信息
deactivate server2

server2 --> server1 : 返回默认值信息
deactivate server1

server1 --> 客户端 : 返回默认值信息
deactivate 客户端

客户端 -> client : 存盘\n行提交
activate client

client -> client : getDefaultValue()
activate client

client -> server4 : makeCode:调用远程服务分码
activate server4

server4 -> server5 : makeCodes/makeMGCodes：分码
activate server5

server5 -> server4 : 返回分码信息
deactivate server4

server4 -> client : 返回分码信息
deactivate client

client -> 客户端 : 显示分码结果
deactivate 客户端
@enduml
