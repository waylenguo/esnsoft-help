## 字典定义	

|日期|作者|备注|
|------|------|------|
|2018-10-16|吕希兵|初稿|
|2019-10-12|聂明笛|修改|

### 指南	

#### 概念说明

1. 通过JSON文件配置少数据、少变化的简单码表。
2. 通过不同语言的配置文件实现国际化。
3. 优先获取数据库的码名配置，数据库未找到才会找文件，两者的配置不会同时出现。

#### 使用说明

##### 配置方式

用界面定义给字段配置字典时，案例如下图：

<img src="help/SN-CMC/Dict/img/main-dict-view.png" />

XML界面定义给字段配置字典时，案例如下:

```xml
<c name="qtyunit" title="统计单位" sqltype="12" width="120" disableed="true" aidInputerBtn="true" codedata="#DT_erp.unit" showname="true" showcode="true" droplist="true" droplistWithSearchPane="true" codeNameMatch="true" />
```

```
列上面配字典时关键属性：
codedata="#DT_字典号"				--码表
showname="true" 					--显示名
aidInputerBtn="true"				--辅助输入按钮
showcode="true"					--显示码
disableed="true"					--编辑禁止
droplist="true"					--下拉方式
droplistWithSearchPane="true"	--(下拉框)含输入搜索框
codeNameMatch="true"				--码名同时匹配
```

##### 字典停用

目前字典的码名映射默认过滤掉停用的码，服务端的字典数据加载服务不受影响
为此功能单独扩了一个参数cmparams.SQLFILTER_FIX用以过滤停用数据
注：不要用cmparams.SQLFILTER_FIX="null"的方式配置，要用cmparams="SQLFILTER_FIX:null",前面的方式解析null的时候有问题

```xml
/**正常情况下默认只显示未停用的数据*/
<c name="code" sqltype="12" codedata="#DT_90.SEX" />
/**全显示*/
<c name="code" sqltype="12" codedata="#DT_90.SEX" cmparams="SQLFILTER_FIX:null"/>
/**有其他条件的情况下全显示*/
<c name="code" sqltype="12" codedata="#DT_90.SEX" cmparams="SQLFILTER_FIX:null,SQLFILTER:&quot;code='F'&quot;"/>
```

##### 字典过滤

* XML定义字典过滤

```xml
<c name="code" sqltype="12" title="编码" width="160"  cmparams.SQLFILTER="code in ('3')"  codedata="#DT_IRMP-RMS.risk_level" />
```

* 界面定义字典过滤

<img src="help/SN-CMC/Dict/img/main-dict-view3.png" />

* 动态字典过滤

```
1、XML或数据库界面定义中配置：cmparams.SQLFILTER="code > ${status}"
2、客户端JS监听辅助录入动态传递参数：
public void itemAidInputing(Table table, TableEvent e)
	{
		super.itemAidInputing(table, e);
		if ($eq(e.forTblColumn.name, "gwcode"))
		{
			CodeData selectOptions = (CodeData) e.item.selectOptions;
			//清理客户端缓存，请求远程
			//最好判断与上次条件不一样时才设置，否则每次设置都会清除掉缓存，影响速度		
			selectOptions.clearBuffered();
			selectOptions.loadParameter.$set("status", table.getQueryParamValue("status"));
		}
	}
```

* 自定义过滤器

```
XML或数据库界面定义中配置：
单元属性：cmparams.FILTER=snsoft.dx.codedata.DefaultCodeDataRowFilter

public class DefaultCodeDataRowFilter implements snsoft.dx.codedata.CodeDataRowFilter 
{

	@Override
	public void init(CodeData codeData) {
	}

	@Override
	public boolean filterAccept(Object[] a, CodeData codeData, int row) {
		return true;
	}

	@Override
	public void endFilter(CodeData codeData) {
		
		
	}

}
```

##### 拓展字段

拓展字段共预留了5个，var01,var02,var03,var04,var05
主要用于级联选择和拷贝字段

```
{
	"dicticode": "Dict02",
	"name": "测试字典02",
	"remark": "用于测试级联",
	"var01":"拓展字段",
	"infos": [
		{
			"code": "aa",
			"name": "我叫名字1",
			"var01": "30,70"
		},
		{
			"code": "bb",
			"name": "我叫名字2",
			"var01": "10"
		},
		{
			"code": "cc",
			"name": "我叫名字3",
			"var01": "15,90"
		}
	]
}
```

##### 级联字典

下例中：Dict02字典通过取cd1字段的值过滤字典值，只显示var01中有这个值的字典值

```
<c name="cd1" title="测试字典01" width="120" sqltype="12" codedata="#DT_SN-HELP.Dict01" showname="true" disableed="true" aidInputerBtn="true" />
<c name="cd2" title="测试字典02" width="120" sqltype="12" codedata="#DT_SN-HELP.Dict02" showname="true" disableed="true" aidInputerBtn="true" 
	cmprops.pmFromPane="{var01:'cd1'}" />
```

##### 字典拷贝

下例中：Dict02字典选中后会把var01中的值带到cd4中

```
<c name="cd3" title="拷贝字典" width="120" sqltype="12" codedata="#DT_SN-HELP.Dict02" showname="true" disableed="true" aidInputerBtn="true" 
	aiprops.copyMap="{'cd4':'var01'}"/>
<c name="cd4" sqltype="12" title="拷贝字段" disableed="true"/>
```

### 手册

#### 存放路径规范

```
字典文件存放UI工程     
系统加载字典时会加载模块对应 cfg/dict/ 下面格式为 DictInfo*.json 的文件
多语言设置：在同路径下添加名字前缀相同加下划线语言的文件，类似DictInfo*_en.json
```

#### 相关命名规范
```
字典文件名称要求(DictInfo*.json)："DictInfo"+系统ID+".json"
字典使用："#DT_"+系统ID+"."+字典号
字典号（dicticode）要求：一般使用对应小写的字段名
字典说明（remark）内容要求：必须详细的说明，包括 含义，使用范围，注意事项等
```

#### 字典定义

* 说明：支持多语言的字典，需要在多语言文件中添加dicticode相同的字典配置，infos中的值若跟汉语的码多应，则顺序最好一样

```
	[
		{
			"dicticode": "test1",
			"name": "字典名称",
			"remark": "字典详细说明",//注意必须详细的说明
			"isCuicode":"true",//加上该属性后，表示该字典为商户字典
			"infos": [
				{
					"code": "00",
					"name": "名称-0",
					"remark": "字典项说明"
				},
				{
					"code": "01",
					"name": "名称-1",
					"remark": "字典项说明"
				}
			]
		},
		{
			"dicticode": "test2",
			...
		},
		...
	]
```

* 参数描述：

```
dicticode：字典号，使用对应的字段名
name：字典名称
isCuicode:商户字典标识
remark：字典说明，必须详细的说明，包括：含义、使用范围、注意事项等
infos：字典内容
infos-code：字典码
infos-name：字典名
infos-remark：字典项说明
```

#### 相关界面

管控中心-->开发配置-->基础配置-->系统字典：[系统字典](uiinvoke/00/zh_CN/theme0/90.DICTDEF.html)
管控中心-->开发配置-->基础配置-->商户字典：[商户字典](uiinvoke/00/zh_CN/theme0/90.CUICODEDICTDEF.html)

```
页面数据优先查找数据库，数据库不存在的查找文件，数据库数据和文件数据不共存
不支持新增，如需修改，请使用同步至数据库功能，将文件内容拷贝到数据库，进行修改，字典编码不可修改
字典字段【xx】为多语言列，依据系统选项的配置控制显隐
商户字典与字典定义功能类似
```

### 常见问题

#### 加载原理
字典的是通过`配置管理系统 `加载和存储的。
 参见 [配置管理系统](help.html?helpFile=help/SN-CMC/Configkit/Configkit.md)

#### 如何查找替换
```
Eclipse中打开文件搜索，搜索后可进行替换（注意：一定要确定替换的都是正确的，可先查找，如果搜索范围内都是正确的直接替换），如下图
```
<img src="help/SN-CMC/Dict/img/main-dict-view5.png" />
<img src="help/SN-CMC/Dict/img/main-dict-view6.png" />
```
搜索结果：
```
<img src="help/SN-CMC/Dict/img/main-dict-view7.png" />

### 多语言
#### 处理方式
```
语言是登录时从服务器中取过来的语言（注意：首先从系统选项APP.Languages中获取，如果没有，获取服务器本地语言）
参考：snsoft.commons.config.LangUtils.getConfigLanguages()
设置系统语言：
1、系统选项APP.Languages=en_US,zh_CN
2、系统登录时默认语言Login.DefautLanguage=zh_CN
3、APP.Languages不存在时，系统默认的本地化设置（服务器所在地区的语言）
可以在启动JVM时通过命令参数指定：java -Duser.language=en -Duser.region=US

字典多语言处理多个文件顺序：
1、DictInfo00_en_US.json，2、DictInfo00_en.json，3、DictInfo00.json
字典多语言处理程序：
snsoft.cfg.client.file.FileDictConfigContent.initConfigs()
```
#### 语言规范

```
多语言规范文件名称
英文：DictInfo + 系统ID  + _en.json
中文：DictInfo + 系统ID  + _zh.json

```

### 相关程序
#### 服务端相关程序

```
定义接口：snsoft.dx.codedata.CodeDataDef
加载入口：snsoft.dx.codedata.bean.DictLoader.loadDicsForFile(String dicticode, String fileinfo, CodeDataDef def)
获取码表或者字典的服务：snsoft.dx.codedata.CodeDataService
获取码表或者字典方法：
	CodeDataParams cdp = new CodeDataParams("DT_Test.test1");
	CodeData codeData = CodeDataService.impl.loadCodeData(cdp);
	CodeData codeData = CodeDataService.impl.loadCodeData("DT_Test.test1", null);
	values = codeDataSup.getValues();
	values二维数组说明：下标0是码表对应的码，下标1是码表对应的名
```
#### 客户端相关程序
```
客户端dataSet获取码表或者字典方法：
	DataColumn column = dataSet.getColumn("test");
	JSObject valueMap = column.valueMap;
	说明：这个根据界面dataSet列来获取码表值
客户端远程获取码表或者字典方法：
	xjs.dx.CodeDataService service = xjs.core.RInvoke.newBean(xjs.dx.CodeDataService.class);
	service.getCodeName((CodeDataParams) $o("CODEDEFID", "sheet.wcode"), wcodearr[i]);
	service.getCodeName((CodeDataParams) $o("CODEDEFID", "DT_90.limFld"),"");
```
```
注：大部分参数只在部分方法中有效果
服务端：snsoft.dx.codedata.impl.CodeDataServiceImpl
客户端：xjs.dx.CodeDataService
CodeDataParams参数说明
	"CODEDEFID"			: 码表名
	"FILTER"			: 
	"SQLFILTER"			: 
	"QueryParams"		：Map<Stirng,Object>其他查询条件：Sql 中 的 参数等 
		以上三个参数配合使用
		当filer为true时，会根据sqlFilter的值过滤码表的值，具体规则为
		sqlFilter可直接为sql条件，类似name='xxx';
		或者可以name='${name}',则会从queryParams里取key为name的值组成条件;
		也可以user='%EnvParam(USERCODE)',则会从userSession当中取对应的登录信息组成条件进行过滤
	"Filters"			:String[] 过滤条件类全名snsoft.xxxx....?xxx&xxx&xxx，需要继承snsoft.dx.codedata.CodeDataRowFilter
						适应于：UI.loadCodeData
	"DataFilters" 		: 未发现有方法用到或已经废弃
	"PrefixCode"		:未发现有方法用到或已经废弃
	"LevlCode"			: 废弃： 考虑 直接使用 CodeDataDef 中的 
	"LevlColumn"		:
	"Options"			:未发现有方法用到或已经废弃
	"PgFromRow"			:
	"PgToRow"			:分页处理,返回数据的行数为pgToRow - pgFromRow。
	"LIMID"				:权限 id :limid==null时  在 CodeDataDef 中 获取：
	"SelectColumns"		:int[]选择要展示的列
	"USERCODES"			:未发现有方法用到或已经废弃
	"MATCHCOLUMNS"		:未发现有方法用到或已经废弃
	"SHARELIMFILTER"	:未发现有方法用到或已经废弃
	"LimDataValues"		:权限过滤	定义：LimDataValues=dtype:01,03 只使用权限定义中 dtype in(01,03)的记录
	"limUserCode"		:String[],可以填写USERCODE,USERBCODE,USERNAME,USERWCODE,USERGWCODE,USERCUICODE,WADMIN
						示例：USERCODE='%EnvParam(USERCODE)'会从userCode取对应信息过滤
	"LimMatchColumns"	:指定的权限字段，如果为空，则使用当前权限的所有权限字段
	"LimShareFilter"	:共享表的  过滤条件， 一般 sheetcode='?'
	"LimAlterNames"		:需要判断权限的业务数据
	"ALTERDATACOLUMNS"	:未发现有方法用到或已经废弃
	"doDataFilter"		: 客户端传入的 过滤条件 ：
						 *  doDataFilter==1 ---- 权限等过滤之前  需要首先 过滤 用户的查询条件
						 *  doDataFilter==2 ---- 返回的数据中， 已经对用户的查询条件过滤过
```

### 商户字典	

#### 功能说明

商户字典是指每个商户配置自己独立的字典，所以首先需要启用商户选项。
```
System.propertites中设置APP.StartCuicode=true建议使用方式
SysOption00.xml中APP.StartCuicode选项配置成true;
dictinfo表需要重新生成表结构，cuicode字段只有商户选项启用时才会生成。
```

页面功能组件
供自主搭建商户字典页面时使用
```
@Service("SN-CMC.CMCDictDefUIService")

snsoft.cmc.codedata.dict.service.CMCDictDefUIService
//商户字典主表查询
CMCDictDef[] queryDictDefC(CMCDictDefParam param);
//商户字典子表查询
List<DictInfos> queryDetail(CMCDictInfoParam param);
//商户字典修改保存
void saveC(List<CMCDictDef> defs);
```
#### 商户字典使用
配置和代码功能使用与普通字典相同，但只会获取当前商户的字典。