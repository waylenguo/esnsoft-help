## 码表CodeData

|日期|作者|备注|
|------|------|------|
|2018-09-25|陈泰林、周鹏辉|初稿|
|2019-10-10|吕希兵|优化|

### 指南

#### 概念说明

CodeData指的是码表，是包括码表定义、辅助录入一系列功能的统称。
新版底层中，我们将用于界面的码名映射和辅助录入区分开，两者各司其职，从而避免旧版中界面辅助录入列较多，导致码名映射效率不高的问题。
辅助录入和码名映射理应是两个功能，只是一般情况下会成对出现。

#### 使用说明

**文件配置**

```
<!-- 数据库中普通码表加载数据，更多的属性配置参考手册中的配置列说明或者实践中的配置文件 -->
<codedata id="fcode" table="fcode" title="币种" expl="币种">
    <column name="fcode" type="12" title="币种编码" width="200" primkey="true" />
    <column name="fname" type="12" title="币种名称" width="300" />
</codedata>
```

**界面配置**

上面的例子fcode码表在界面中配置在codedata属性中，如下：

```
 #xx.fcode(xx为码表配置文件的文件名CodeDataxx.xml)
```

**程序调用**

* 客户端

```
1.dataSet中获取码表
DataColumn col = dataSet.getColumn("xx");
JSObject vmap = col.valueMap;
2.远程获取码表
xjs.dx.CodeDataService service = xjs.core.RInvoke.newBean(xjs.dx.CodeDataService.class);
service.getCodeName((CodeDataParams) $o("CODEDEFID", "xx.fcode"),"");
更多参考手册
```

* 服务端

	```
	CodeData codeDataSup = CodeDataService.impl.loadCodeData("xx.fcode", null);
	Object[][] codeData = codeDataSup.getValues();
	```

**参考Demo**
动态码表定义[管控中心]参考界面：[动态码表定义](uiinvoke/00/zh_CN/theme0/90.AidTest.html)
码名显示[帮助中心]参考界面：[码名显示](uiinvoke/00/zh_CN/theme0/SN-HELP.UI.ShowName.html)

### 手册

CodeData定义分为文件和数据库两部分：
```
- 文件定义：snsoft/codedata/CoderData*.xml，其中*代指系统号。
- 数据库定义：codetbl,codetbla,codetblc 一共三张表
在【开发配置-基础配置-码表定义】菜单中，我们可以查询到文件定义和数据库定义合并后的码表定义结果集。并且在界面录入属性后，可以将对应属性保存到数据库中。数据库属性和文件属性可以同时存在，但数据库的优先级比较高，会先找数据库，数据库没有才会去查文件系统，从开发层面来说，我们要求开发使用文件定义的方式，本菜单仅用于开发速查使用。在发布产品时，一般不会发布数据库配置。
```

#### 配置列说明
**码表属性说明配置**

```
码表属性配置文件：Attrs_CodeData.xml
码表属性描述配置文件：Attr_Expl.xml
```
码表所有属性及作用请参考码表定义：如下图：R
<img src="help/SN-CMC/Codetbl/img/Codetbl5.png" />

**CODEDATA属性**

```
1、codedata标签中：id：码表ID==>加上系统号对应主表中的【码表ID】
2、codedata标签中：table：表名==>对应主表中的【物理表名】
3、codedata标签中：filter：过滤条件==>对应主表中的【过滤条件】
					code1=?code1和code1=:code1用法一致，都是取码表配置列cmparams.xxx属性的值（即码表查询参数【xxx="01"】）
4、codedata标签中：title：码表标题==>对应主表中的【说明】
5、codedata标签中：expl：说明描述列（必须添加）==>对应主表中的【备注】
6、codedata标签中：ViewSortColumns：排序字段==>对应子表码表属性的【排序字段】
7、codedata标签中：CodeDataLoader：码表数据加载类（例如：snsoft.cmc.bcode.loader.BtypeCodeDataLoader.new）==>对应子表码表属性的【CodeData构造方法】
8、codedata标签中：ModiDateCaseTbls：码表缓存相关表名，用于清理缓存使用。==>对应子表码表属性的【码表数据时间相关表】
9、codedata标签中：ui.aidinputer：辅助输入弹出框（1、指定界面定义Xjs.ui.SelectTableDataDialog.new，2、不配置走大师默认，3、可自定义snsoft.help.sheet.FcodeAidInputer.new）
					==>对应子表码表属性的【辅助输入】
10、codedata标签中：ui.aiprops：辅助录入属性（JSON格式）==>对应子表码表属性的【其他选码对话框属性】
					属性详细介绍请参考选择的“辅助输入弹出框”类型（Xjs.ui.SelectTableDataDialog.new）
					例如：
					ui.aiprops="muiid:'90.codedata.BCODE',tblname:'bcode',column:'bcode',rtnNameFld:'bname',copyMap:{wcode:'wcode'},refreshOnOpen:true,initParasVales:{btype:'01'},pctSize:'30*80'"
					弹出界面参数常量传递：
					dlgParam:{sheetcode:@IRMP-RMS.CtrlRule,opids:@C}
					initParasVales:{btype:'01'}
					弹出界面参数动态传递：dlgParam:{ciq_code:'界面名称.界面字段'}==>具体是指将当前页面的某个界面的某个字段传到弹出的码表框中的过滤条件中
11、codedata标签中：ui.options2：辅助录入选项： 0x001：下拉方式 0x002：按钮方式（其他具体参考界面定义）(类似ui.options2="0x001")==>对应子表码表属性的【下拉方式】/【按钮方式】
12、codedata标签中：ui.options4：辅助录入选项： 0x001：分级 0x002：可选中间级 0x010：（下拉）含输入搜索框 0x020：立即刷新（其他具体参考界面定义）
					==>对应子表码表属性的【分级】/【可选中间级】/【(下拉框)含输入搜索框】/【立即刷新】
13、codedata标签中：ui.cmprops：码表属性（JSON格式，参见Xjs.CodeData）==>对应子表码表属性的【其他码表(CodeData)属性】
14、codedata标签中：ui.cmprops.pgRowCount：客户端弹出框-分页行数==>对应子表码表属性的【分页行数】
15、codedata标签中：ui.cmparams.FILTER：辅助录入过滤器（例如：ui.cmparams.FILTER="snsoft.bas.sheet.bcode.codedata.LimbcodeFilter.new?sheetcode=sheet.Wcode"）
					==>对应子表码表属性的【表数据过滤器(分号分隔)】
```
**COLUMN属性**

```
1、codedata标签中：column：列
2、column标签中：name：列名==>对应子表码表字段中的【fldname 字段名】
3、column标签中：type：列类型==>对应子表码表字段中的【sqltype 类型】
4、column标签中：title：列标题==>对应子表码表字段中的【fldtitle 标题】
5、column标签中：width：列宽==>对应子表码表字段中的【dispwidth 显示宽】
6、column标签中：primkey：列是否为主键，主要用于码表数据排序，排序好的数据才能正确的码名映射==>对应子表码表字段中的【flags#1】
7、column标签中：displayFlags：列是否隐藏（displayFlags="16"隐藏）==>对应子表码表字段中的【dispflags#16】
8、column标签中：leveled：列分级（leveled="true"）==>对应子表码表字段中的【xflags#1】
9、使用大师默认弹出框配置多选时，可在界面XML，要求多选字段标签属性，添加 mutiple="true"
```

#### 相关程序

**客户端事件**

客户端区分Table中辅助录入和DialogPane中辅助录入，一共有如下两种事件：
- TableListener.itemAidInputing
- DialogPaneListener.itemAidInputing
```java
#一个改写辅助录入属性的例子
if( e.item.name=="gcode" )
{
xjs.ui.util.SelectCodeDialog select = (SelectCodeDialog)((xjs.ui.InputField)e.item).aidInputer;
CodeData codeData = select.codeData;
// CodeData codeData = (CodeData)e.item.selectOptions;//aidInputer中没有时，需要这样取codedata
codeData.setLoadParameter("dclass", dclass);
}
```
在itemAdiInputing事件中我们可以改写辅助录入的属性，甚至使用动态码表章节介绍的功能设置Column使用动态获取的辅助录入。

**客户端**

```
1、客户端dataSet获取码表方法：
	DataColumn column = dataSet.getColumn("test");
	JSObject valueMap = column.valueMap;
	说明：这个根据界面dataSet列来获取码表值，只能获得列上面存在的值
	TableColumn column = table.getColumn("");
	table.getDisplayValue(column, 1, 1);
2、客户端远程获取码表方法：
	xjs.dx.CodeDataService service = xjs.core.RInvoke.newBean(xjs.dx.CodeDataService.class);
	service.getCodeName((CodeDataParams) $o("CODEDEFID", "sheet.wcode"), wcodearr[i]);
	service.getCodeName((CodeDataParams) $o("CODEDEFID", "90.limFld"),"");
3、客户端弹出指定界面定义：Xjs.ui.SelectTableDataDialog
4、客户端自定义弹出指定码表事例：
	1、AidInfo.newCodeDialog("#sheet.wcode", bwTbl, (js.IFunction_V1 &lt;SelectCodeDialog&gt;) this::onAidInputerUpdateParentValue, this);
	2、boolean onAidInputerUpdateParentValue(SelectCodeDialog dlgPane)
5、大师辅助录入默认继承：xjs.ui.ComboAidInputer
	注意：辅助录入属性aiprops.copyMap="{'hs_code_parent':'hs_code'}"表示码表选择列hs_code赋值table中的列hs_code_parent
6、码表分布式配置snsoft.dx.codedata.UniteCodeDataLoader
   <codedata id="SYS" ui.options2="0" ViewSortColumns="sysid" ui.options4="0x020" 
		CodeDataLoader="snsoft.dx.codedata.UniteCodeDataLoader.new?path=mq/sys/MQSysConfs*.xml&amp;mode=1"
		expl="消息中心接入系统定义">
		<column name="sysid" type="12" title="系统ID" width="200" sqlexpr="/MQSysConfs/sysconfs/sysid"
			primkey="true" />
		<column name="sysname" type="12" title="系统名称" width="300" sqlexpr="/MQSysConfs/sysconfs/sysname" />
	</codedata>
```

**服务端**

1、服务器获取码表或者字典的服务：snsoft.dx.codedata.CodeDataService
2、服务端获取码表或者字典方法

```
CodeData codeDataSup = CodeDataService.impl.loadCodeData("erp.test1", null);
Object[][] codeData = codeDataSup.getValues();
//codeData二维数组说明：下标0是码表对应的码，下标1是码表对应的名
```
3、码表自定义方法（解析码表）：snsoft.dx.codedata.impl.CodeDataServiceImpl.parseCodeNameMap
4、码表过滤器：snsoft.dx.codedata.DefaultCodeDataRowFilter
	事例：snsoft.bas.sheet.bcode.codedata.LimbcodeFilter继承snsoft.dx.codedata.DefaultCodeDataRowFilter

#### 相关规范
```
码表文件存放UI工程    
系统加载码表时会加载模块对应cfg/codedata下面CodeData*.xml文件
码表文件名称要求(CodeData*.xml)："CodeData"+系统ID+".xml"
码表使用："#"+系统ID+"."+码表id
码表id：规范使用字段id
码表expl：说明描述列，说明该码表的作用，必须添加
自定义码表请参看：snsoft.dx.codedata.impl.CodeDataServiceImpl.parseCodeNameMap
```

**CodeData.xsd**
同所有xml格式定义一样，CodeData的定义也有其约束文件：CodeData.xsd。其中定义了xml文件中可用的元素以及其属性，开发在配置CodeData时，请严格按照约束配置。否则解析xml文件时，xsd校验是无法通过的。

```
<codedata-list>节点码表定义根节点，只包含<codedata>一个元素。
<codedata>该节点即为码表节点，包含<column>一个元素。常用的属性为id以及table。其它属性可参见CodeData.xsd中codedataType定义相关内容。
<column>节点配置码名映射的列。该元素仅仅用于CodeData码名映射，或者一些简单码表的辅助录入列。
我们推荐这里配置：编码、名称两列。当配置列数较多时，请开发考虑使用aidinputer属性和aiprops属性指定合适的辅助录入器以及辅助录入界面定义。
```
**注意**

```
码表配置文件需要加如下属性，已开启码表xsd校验
<codedata-list xmlns="http://www.snsoft.com.cn/schema/CodeData" xsi:schemaLocation="http://www.snsoft.com.cn/schema/CodeData http://www.snsoft.com.cn/schema/CodeData.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
```

### 实践

#### 界面配置

在界面配置辅助录入或码表时，我们有以下几点要求：
- 共享界面配置：主界面列与共享界面列都要配置相同的码表及显示名属性。
- 主界面配置码表是供服务端数据加载时获取名称使用。
- 共享界面配置是供录入时获取名称使用。
当一个码表在主界面没有配置，而仅仅在共享界面配置时，客户端就会发送远程请求获得其编码的显示名称。当两个界面都配置时，编码的显示名称会在数据加载时就获得，减少一次远程请求。
配置文件/数据库定义的码表：`#00.users`
配置字典，以CMC.STATUS字典为例：`#DT_CMC.STATUS`
当数据源非数据库表时，我们往往需要在服务端提供静态方法或Service来返回二维数组作为码表数据，此时界面配置方式为：
```java
//codedata中
jmethod:服务端静态方法，配置方法全路径
service:服务组件方法，例如 SN-CMC.QueryCacheService.listCachServer
//listCachServer方法可以无参，也可以有一个Map参数
```
案例如下图：
<img src="help/SN-CMC/Codetbl/img/Codetbl2.png" />

#### 数据库配置

案例如下图：
<img src="help/SN-CMC/Codetbl/img/Codetbl1.png" />

#### 配置文件
```
以下码表的配置在页面92.CODEDATA1上都有实例
		
<!-- 数据库中普通码表加载数据 -->
<codedata id="fcode" table="fcode" title="" expl="">
	<column name="fcode" type="12" title="币种编码" width="200" primkey="true" />
	<column name="fname" type="12" title="币种名称" width="300" />
</codedata>

<!-- 码表加载时使用参数及过滤器filter -->
<codedata id="gwcode01" table="gwcode" filter="gwnamel1=:gwnamel1" title="" expl=""
	ui.cmparams="gwnamel1:'%EnvParam(USERCODE)'" <!-- 指定过滤条件的参数值 -->
	ui.cmparams.FILTER="snsoftx.test.dx.codedata.TestCodeDataFilterTmp.new">
	<column name="gwcode" type="12" title="编码" width="200" />
	<column name="gwname" type="12" title="名称" width="300" />
</codedata>

<!-- 码表分级，指定分级字段 -->
<codedata id="code06" table="code06" title="" expl="">
	<column name="code" type="12" title="编码" width="100" primkey="true" />
	<column name="name" type="12" title="名称" width="300" />
	<column name="sortidx" type="12" title="分级码" width="100" leveled="true" /> <!-- 指定 sortidx为分级字段 -->
</codedata>

<!-- 码表双主键模式（91.code05 多主键） -->
<codedata id="code05" table="code05" title="" expl="">
	<column name="code1" type="12" title="编码1" width="150" primkey="true" />
	<column name="code2" type="12" title="编码2" width="150" primkey="true" />
	<column name="name " type="12" title="名称" width="300" />
</codedata>

<!-- 码表动态查询参数 ：#91.code052 码表查询参数 ： code2:"01"-->
<codedata id="code052" table="code05" filter="code2=:code1" title="" expl="">
	<column name="code2 " type="12" title="编码" width="200" primkey="true" />
	<column name="name" type="12" title="名称" width="300" />
</codedata>

<!-- 码表多主码 多级次， 指定分级字段 -->
<codedata id="code07" table="code07" title="" expl="">
	<column name="code1" type="12" title="编码1" width="100" primkey="true" />
	<column name="code2 " type="12" title="名编码2" width="100" primkey="true" />
	<column name="name" type="12" title="名称" width="300" />
	<column name="sortidx" type="12" title="分级码" width="100" leveled="true" />
</codedata>

<!-- 码表指定界面定义弹出 -->
<codedata id="tcodeList" table="tcode" ui.aidinputer="Xjs.ui.SelectTableDataDialog.new" title="" expl=""
	ui.aiprops="muiid:'91.UI.Bas.tcodeList',tblname:'tcode',column:'tcode',rtnNameFld:'tname',pctSize:'60*80'">
	<column name="tcode" type="12" title="组织机构编码" width="200" primkey="true" />
	<column name="tname " type="12" title="组织机构名称" width="300" />
</codedata>	

<!-- 码表加载字典数据 -->
<codedata id="fcode" CodeDataLoader="snsoft.dx.codedata.bean.DictLoader.new?dicticode=SN-HELP.fcode" title="" expl=""
	ui.aidinputer="snsoft.help.sheet.FcodeAidInputer.new"
	ui.aiprops="listHeight:100,selOptions:1,cellTextFmt:['${code}:${name}']">
	<column name="code" type="12" title="编码" width="100" primkey="true" />
	<column name="name" type="12" title="名称" width="300" />
</codedata>	

<!-- 码表加载过滤参数 -->
codedata id="rank" table="cc_rank" expl="职级"  ui.options4="0x020" title="" expl="" 
		ui.cmparams="cuicode:'%QueryParam(cuicode)'"
		filter="cuicode=?cuicode">
		<column name="rankcode" type="12" title="编码" primkey="true" width="200" />
		<column name="rankname" type="12" title="名称" width="300" />
		<column name="cuicode" type="12" title="商户编码" width="300"  displayFlags="16"/>
</codedata>	
	
<!-- 码表分布式加载 -->
<codedata id="bank" ui.options4="0x020" ViewSortColumns="bankcode" title="" expl=""
	CodeDataLoader="snsoft.dx.codedata.UniteCodeDataLoader.new?path=cc/bank/CCBanks.xml&amp;mode=1" expl="银行">
    <column name="bankcode" type="12" sqlexpr="/CCBanks/banks/bankcode" title="编码" title_en="BankCode" width="200" primkey="true" />
	<column name="bankname" type="12" sqlexpr="/CCBanks/banks/bankname" title="名称" title_en="BankName" width="300" /> 
</codedata>
	
<!-- 码表复杂数据库加载 -->
<codedata id="ccode" table="ccode" expl="客商码表" ui.options4="0x020" filter="status='70'" ViewSortColumns="ccode" title="" expl="">
	<column name="ccode" type="12" title="编码" title_en="Ccode" width="120" primkey="true"/>
	<column name="cname" type="12" title="名称" title_en="Cname" width="250"/>
	<column name="shortcode" type="12" title="简码" title_en="Shortcode" width="120"/>
	<column displayFlags="16" name="status" type="12" title="状态" title_en="Status" width="50" hidden="true"/>
	<column displayFlags="16" name="cuicode" type="12" title="商户编码" width="120"/>
	<column displayFlags="16" name="ccodetype" type="12" title="客商类型" title_en="Ccodetype" width="300"/>
	<column displayFlags="16" name="bcode" type="12" title="部门" title_en="Bcode" width="100"/>
	<column displayFlags="16" name="wcode" type="12" title="人员" title_en="Wcode" width="100"/>
	<column displayFlags="16" name="corpbcode" type="12" title="公司" title_en="Corpbcode" width="100"/>
	<column displayFlags="16" name="cuscuicode" type="12" title="客商商户" title_en="CusCuicode" width="100"/>
</codedata>

<!-- 码表复杂配置加载 -->
<codedata id="ccodebank" table="ccodebank" ui.options4="0x020" expl="客商银行码表" ui.cmparams="ccode:'%QueryParam(ccode)'" filter="ccode=?ccode" title="" expl=""> 
	<column displayFlags="16" name="id" type="12" title="id" title_en="id" width="120" primkey="true" hidden="true"/>
	<column displayFlags="16" name="ccode" type="12" title="编码" title_en="Ccode" width="120" hidden="true"/>
	<column name="bankcnaps" type="12" title="银行CNAPS" title_en="bankcnaps" width="100"/>
	<column name="bankname" type="12" title="银行" title_en="bankname" width="100" codedata="#HC-CC.bank" showname="true"/>
	<column name="bankcode" type="12" title="银行账号" title_en="bankcode" width="120"/>
	<column name="fcode" type="12" title="银行CNAPS" title_en="fcode" width="100" codedata="#DT_HC-MC.fcode" showname="true"/>
	<column name="swiftcode" type="12" title="SWIFTCODE" title_en="swiftcode" width="100"/>
</codedata>

```

**表对应关系**

|    XML配置列	|      数据库表（codetbl）列    	| 				说明   					    |
| ------------- |:--------------------------|:--------------------------------------|
| 		id	    | 	 	id		       		|   			码表ID					|
| 		table	| 		tblname         	|    			物理表名					|
| 		filter	| 		filter          	|    			过滤条件					|
|		expl    |		expl				|				说明描述					|

#### 动态码表
有些时候业务上会要求界面上的列使用的辅助录入根据某个选项设置，甚至界面的列就是动态创建的。此时我们就会运用到动态码表的技术。动态码表相关的代码如下：
```java
 #客户端远程Service
xjs.ui.aid.AidInfoService
 #客户端Aid工具类
xjs.ui.aid.AidInfo
xjs.ui.aid.AidInfoService.AidInfoParam
 #服务端Service
snsoft.ui.aid.service.AidInfoService
 #服务端实现
snsoft.ui.aid.service.impl.AidInfoServiceImpl
```
其中我们日常使用xjs.ui.aid.AidInfo,xjs.ui.aid.AidInfoService.AidInfoParam即可。其它类列示出来方便各位开发同学了解其实现原理。
常见的使用方式如下：
```java
AidInfoParam param = new AidInfoParam("#00.users", null);
AidInfo aidInfo = AidInfo.createAidInfo(param);
if (aidInfo.aidInputer != null)//此时判断是弹出辅助录入
{
c.setAidInputer(aidInfo.aidInputer);
}
if (aidInfo.selectOptions != null)//或者是下拉选择
{
c.setSelectOptions(aidInfo.toCodeData());
}
```

#### 国际化

涉及两个地方，通过指定不同语言的属性或存储列实现。
1. 标题的国际化
1. 显示名称的国际化

<img src="help/SN-CMC/Codetbl/img/Codetbl6.png" />
根据系统选项中的APP.LANGS中配置的语言，取除zh(中文)外的前两种语言，暂只支持两种语言，根据码在91.applang字典中取对应的码名，未配置的显示编码
对于XML定义通过 name(字段名)、title(标题)两个属性配合语言后缀来定义多语言，如下：

```
<codedata id="码表ID" table="对应表名">
	<column name="code" title="中文标题" title_en="英文标题" width="200" primkey="true" />
	<column name="name" name_en="name1" title="中文标题" title_en="英文标题" width="300" />
</codedata>

```
对于数据库定义存储字段为 fldname(字段名)、fldtitle(标题)。
系统预留fldname1、fldname2、fldtitle1、fldtitle2，4个字段作为多语言的存储字段。
配合语言的系统选项 APP.LANGS获取的语言列表，并对应下标（1、2）顺序字段来定义多语言。

#### 查找替换
```
Eclipse中打开文件搜索，搜索后可进行替换（注意：一定要确定替换的都是正确的，可先查找，如果搜索范围内都是正确的直接替换），如下图
```
<img src="help/SN-CMC/Dict/img/main-dict-view5.png" />
<img src="help/SN-CMC/Dict/img/main-dict-view6.png" />
```
搜索结果：
```
<img src="help/SN-CMC/Dict/img/main-dict-view7.png" />

#### VO类中码表较验
```
服务器VO类中码表较验（根据实际业务场景需求配置）
码表校验注解：@CodeTable(value = "码表名", delim = ",")
delim:用于复选校验
如下图
```
<img src="help/SN-CMC/Codetbl/img/Codetbl4.png" />

#### 自定义弹出码表
```
案例如下图
```
<img src="help/SN-CMC/Codetbl/img/Codetbl3.png"  />
```
1、AidInfo.newCodeDialog("#sheet.wcode", bwTbl, (js.IFunction_V1 <SelectCodeDialog>) this::onAidInputerUpdateParentValue, this);
2、boolean onAidInputerUpdateParentValue(SelectCodeDialog dlgPane)
```

#### 码表过滤

码表最常用过滤条件：SQLFILTER与JSONFILTER，界面配置如下：
```
<c ... codedata="#..." cmparams="SQLFILTER:'...',JSONFILTER:{...}"/>
```

对比如下：

|SQLFILTER|JSONFILTER|
|-|-|
|a=1|{a:1}<br/>{n:&apos;a&apos;,v:1}<br/>{n:&apos;a&apos;,v:1,op:&apos;=&apos;}<br/>{name:&apos;a&apos;,value:1}|
|a=1 and b=&apos;2&apos;|{a:1,b:&apos;2&apos;}<br/>{and:[{a:1},{b:&apos;2&apos;}]}|
|a=1 or b=&apos;2&apos;|{or:{a:1,b:&apos;2&apos;}}<br/>{or:[{a:1},{b:&apos;2&apos;}]}|
|a is null|{a:null}|
|a is not null|{n:&apos;a&apos;,v:null,op:&apos;!=&apos;}

JSONFILTER关键字：
* 变量名称：n,name
* 值名称：v,value
* 操作符：op(<b>=</b>,!=,<>,>,>=,<,<=,in,like,!in,!like，默认=)

### 显示名称

#### 界面配置

参见[码表显示Demo](uiinvoke/00/zh_CN/theme0/SN-HELP.UI.ShowName.html)

1. 显示名
	```xml
	<c codedata="#[id]" showname="true" />
	```
1. 显示名(多选，默认逗号分隔)
	```xml
	<c codedata="#[id]" showname="true" mutiple="true"/>
	```
1. 显示名(多选，指定分隔符[-])
	```xml
	<c codedata="#[id]" showname="true" mutiple="true" uiprops.mutiValueDelim="-"/>
	```
1. 显示全名
	```xml
	<c codedata="#[id]" showfname="true" />
	```
1. 显示码
	```xml
	<c codedata="#[id]" showcode="true" />
	```
1. 显示名+获取焦点时显示编码
	```xml
	<c codedata="#[id]" shownode="true" showCodeOnFocus="true"/>
	```

#### 服务端取名称

```java
CodeData codeMap = snsoft.dx.codedata.CodeDataService.impl.loadCodeData(...);
String[] names = snsoft.dx.codedata.CodeDataUtils.getCodeNames(codeMap,[codes],false);
```

#### 客户端取名称

1. 根据界面配置获取名称(推荐：自带标准缓存)
	```java
	// 获取一个编码的名称
	Table.getCodeName(colname,code);
	// 获取多个编码的名称
	Table.getCodeNames(colname,[code1,code2]);
	```
1. 没有页面配置时业务逻辑(不推荐)
	```java
	CodeData codeData = new CodeData({},{CODEDEFID:"id"});
	// 获取一个编码的名称
	String name = codeData.getCodeName1(code);
	// 获取多个编码的名称
	String[] names = codeData.getCodeNames([code1,code2]);
	```

### 多主键码表

1. 多主键码表仅支持**显示名称、下拉选择、弹出选择**；
1. 多主键码表定义参见[码表定义](uiinvoke/00/zh_CN/theme0/90.CODETBL.html?InitValue.id=SN-HELP.Multikeys&AutoRefresh=1)；
1. 多主键使用页面参见[多主键码表](uiinvoke/00/zh_CN/theme0/SN-HELP.UI.Bas.Multikeys.html)；

```xml
<!--
码表配置
FilterValByColumns="key1"：客户端从服务端加载当前码表时按照key1列值进行过滤（snsoft.dx.codedata.impl.CodeDataServiceImpl.loadCodeData）
-->
<codedata id="Multikeysg" title="二主键码表" table="help_multikeysg" expl="二主键码表"
          ui.options4="0x00000020" FilterValByColumns="key1" >
    <column name="key1" type="12" title="大洲编码" width="200" primkey="true" displayFlags="0x10"/>
    <column name="key2" type="12" title="国家编码" width="200" primkey="true"/>
    <column name="name" type="12" title="国家名称" width="300"/>
</codedata>

<!--
列配置（GridTable、RecordTable、DialogPane、ProgressPane配置相同）
xprops.CodeData.KeyNames="droplist1" ：显示名时使用（xjs.ui.Cell.codeKeyNms/xjs.ui.CellRenderer.getCodeKey）
cmprops="pmFromPane:{key1:'droplist1'}" ：远程加载码表时作为参数的解析参数（xjs.dx.CodeData.prepareLoadParams）
-->
<c name="droplist2" sqltype="12" title="下拉|国家" width="120" aidInputerBtn="true" showname="true" droplist="true" codedata="#SN-HELP.Multikeysg"  disableed="true"
	xprops.CodeData.KeyNames="droplist1" 
	cmprops="pmFromPane:{key1:'droplist1'}"
/>
<c name="droplist5" sqltype="12" title="弹出|国家" width="120" aidInputerBtn="true" showname="true" codedata="#SN-HELP.Multikeysg"  disableed="true"
	xprops.CodeData.KeyNames="droplist4" 
	cmprops="pmFromPane:{key1:'droplist4'}"
/>

<!--
相关处理程序：
下拉显示名称：xjs.ui.ComboAidInputerA.getSelectValueText
弹出框显示名称：xjs.ui.util.SelectCodeDialog.getDisplayValue
表列显示名称：
	xjs.ui.CellRenderer.getValueName
	xjs.ui.CellRenderer.getCodeKey
-->
```