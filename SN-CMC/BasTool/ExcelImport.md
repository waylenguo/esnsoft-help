## Excel导入
 
|日期|作者|备注|
|------|------|------|
|2018-06-26|冀小雷|初稿|
|2019-04-16|赵玉柱|调整|
|2019-10-17|吴小海|重构|


### 指南	
#### 概念说明
N9B平台提供Excel导入功能支持，根据现有需求提供标准Excel导入及策略Excel导入2种方案;
1. 标准导入功能支持：存在错误情况下直接抛出异常，停止数据导入并且回滚已导入数据;
1. 策略导入功能支持3种情况：(1)一次性导入 (2)询问导入；(3)有错误则不导入;
  (1)一次性导入会将正确数据导入，错误数据回显界面，支持错误数据导出;
  (2)询问导入会先判断是否存在正确数据，存在则询问是否继续导入，确定后执行导入;错误数据不导入;支持正确数据、错误数据导出;
  (3)同标准Excel导入;支持正确数据、错误数据导出;

使用过程中可根据需求自行确定使用Excel导入方案;
注：
    如果在使用过程中存在相关疑问或者需求可联系 **赵玉柱** 提供技术支持;

#### 使用说明

1. 添加Excel导入客户端监听
   界面定义方式或者系统服务配置任一
   
1. 添加Excel导入配置
   配置文件路径：resources/cfg/res/excel/*.xml
   
1. 添加Excel导入模板
   模板文件路径：resources/cfg/res/excel/template/*.xlsx
   
注：
    此功能仅管理员可以使用;
    相关界面按钮设置参考 【**实践**】 中DEMO配置
    
    
#### 国际化说明
 ```text
    1. 国际化最终翻译字段数量基于Excel导入界面列数与系统配置语言数
    
    2. 目前支持的国际化字段包括
     主表：
         标题:xls_title_{xlscode}
         模板:xls_tmp_{xlscode}
         解析选项.sheetName:xls_ops_{xlscode}_sheetName
     子表：
         标题:xls_field_{xlscode}_{tablecolumn}=test
         
    3. 其他说明：
         由于Excel导出模板不支持修改sheet页签，故方案为多个Excel导入模板的形式，优先找本语言下的模板，如果没有，找默认模板
         
    4. 遗留问题，目前本页面不支持存盘
 ```
        
        

##### 导入客户端监听

 1. 标准场景
 ```xml
 <Functions grp="Excel">
     <Functype>SN-PLAT.JS</Functype>
     <Subtype>2</Subtype>
     <Funcimpl><![CDATA[
     #new snsoft.bastool.excel.ExcelImportListener({xlscode:'SN-HELP.Salorder11'})
     ]]></Funcimpl>
     <Remark>Excel主子导入功能：Excel导入+模板下载</Remark>
 </Functions>
 ```
 
 1. 导入策略
 ```xml
    <Functions grp="Excel">
        <Functype>SN-PLAT.JS</Functype>
        <Subtype>2</Subtype>
        <Funcimpl><![CDATA[
        #new snsoft.bastool.excel.ExcelStrategyImportListener({xlscode:'SN-HELP.Salorder23',strategyEnables:'2'})
        ]]></Funcimpl>
        <Remark>Excel主子导入功能：Excel导入+模板下载</Remark>
    </Functions>
 ```
 
 1. 导入策略参数说明
 
|参数|参数说明|默认值|示例|
|-|-|-|-|
|xlscode|excel导入配置编号|无默认值，非空|SN-PLAT.OneTable|
|isNewWindow|是否在新窗口打开excel导入页面|否，默认在当前页面弹框|true|
|saveBeforeImport|excel导入之前存盘，针对单据|否，执行之前不进行数据存盘|false|
|hiddenDownBtn|隐藏下载模版按钮|否|false|
|strategyEnables|可选导入策略（1：一次性导入；2：询问导入；4：有错误则不导入），取按位与，最小为默认值|7|3|
|runParam|传递到服务端runParam参数|{}|<table border="1" cols="2"> <tr><td>固定值</td><td>"key":"value"</td></tr> <tr><td>表数据值</td><td>"key2":"T.TableName#fldname"</td></tr><tr><td>表查询参数值</td><td>D.TableName#fldname</td></tr></table><br/>{<br/>"key1":12345,<br/>"key2":"T.TableName#fldname",<br/>"key3":"D.TableName#fldname"<br/>}<br/>==》<br/>{<br/>"key1":"12345",<br/>"key2":"表名为TableName的fldname字段值",<br/>"key3":"表名为TableName的fldname查询参数（此值一般为点查询之前）值"<br/>}|

##### XML模板

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ExcelImportDef xmlns="http://www.snsoft.com.cn/schema/ExcelImportDef" xsi:schemaLocation="http://www.snsoft.com.cn/schema/ExcelImportDef http://www.snsoft.com.cn/schema/ExcelImportDef.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<Title>销售合同单表导入</Title>
	<ImpType>SN-PLAT.01</ImpType>
	<ImpOpts>0x01+0x02+0x04</ImpOpts>
	<SheetCode>SN-HELP.Salorder</SheetCode>
	<ReadOptions><![CDATA[
	"skipRows":1,
	"sheetName":"销售合同"
	]]></ReadOptions>
	<TacFormula></TacFormula>
	<ExcelTemplate>SN-HELP.Salorder10.xlsx|销售合同[单表].xlsx</ExcelTemplate>
<!-- 	<ExcelTemplate>sysid#defid|销售合同[单表].xlsx</ExcelTemplate> -->
	<Remark><![CDATA[
	主子表：
	1、主表新建；
	2、处理默认值；
	3、处理码表转换；
	]]></Remark>
	<Fields>
		<TableColumn>ccode</TableColumn>
		<Title>客户</Title>
		<ExcelColumn>B</ExcelColumn>
	</Fields>
	<Fields>
		<TableColumn>fcode</TableColumn>
		<Title>币种</Title>
		<ExcelColumn>C</ExcelColumn>
		<CtrlOpts>0x01</CtrlOpts>
		<TacFormula></TacFormula>
	</Fields>
	<Fields>
		<TableColumn>fcy</TableColumn>
		<Title>金额</Title>
		<ExcelColumn>D</ExcelColumn>
		<TacFormula></TacFormula>
	</Fields>
	<Fields>
		<TableColumn>fee</TableColumn>
		<Title>费用</Title>
		<ExcelColumn>E</ExcelColumn>
	</Fields>
	<Fields>
		<TableColumn>odate</TableColumn>
		<Title>日期</Title>
		<ExcelColumn>F</ExcelColumn>
	</Fields>
	<Fields>
		<TableColumn>plat_salorder_ext.var01</TableColumn>
		<Title>联系人</Title>
		<ExcelColumn>G</ExcelColumn>
	</Fields>
</ExcelImportDef>
```
##### TAC模版
参数见最下方【[参数设计](#参数设计)】
```text
    proc rebuildXlsDef(xlsDef)
        //此方法为了重构excel导入配置
        //导入选项
        imppots = xlsDef.getImpopts()
        ba = snsoft.commons.util.DataUtils.tacOp2(snsoft.commons.util.DataUtils.OPCODE_BITAND,imppots,64)
        if(ba==0)
            //校验类型
            imppots = ba+64
        end if
        xlsDef.setImpopts(imppots)
        //字段定义
        fields = xlsDef.getFields()
        //此处自己判定是否会下标越界
        demonameFld = fields.get(0)
        //字段选项
        ctrlopts = demonameFld.getCtrlopts()
        //字段非空校验
        bo = snsoft.commons.util.DataUtils.tacOp2(snsoft.commons.util.DataUtils.OPCODE_BITOR,ctrlopts,64)
        if(bo == 0)
            ctrlopts = ctrlopts+0x40
        end if
        demonameFld.setCtrlopts(ctrlopts)
        return xlsDef
    end proc
    proc stopParse(event,row)
        //当前行是否已经停止解析了 return true 表示已经停止解析
        return false
    end proc
    proc afterRow(event,data)
        //当前行解析完成，未设置默认值，未进行数据校验
    end proc
    proc beforeDetailMerge(event,dataList)
        //主子表数据合并之前
    end proc
    proc afterAll(event,dataList)
        //所有行解析完成之后
    end proc
    proc afterDefValue(event,dataList)
        //所有数据设置完成默认值
    end proc
    proc afterValid(event,dataList)
        //所有数据做完数据校验
    end proc
    proc beforeSave(event,dao,db,dataList)
        //数据存盘之前,事务内
    end proc
    proc save(event,dataList)
        //listsner存盘，返回true表示使用listener存盘代替其他存盘，将调用不到 beforeSave 方法，而且afterSave的dao与db参数为空
        return false
    end proc
    proc afterSave(event,dao,db,dataList)
        //数据存盘之后
        //事务内
    end proc
    proc afterFinal(event,dataList)
        //解析与存盘完成，事务外
    end proc
```

### 手册		

XML配置请参见对应的Schema文件【[ExcelImportDef.xsd](do/BrowseSchema?schema=ExcelImportDef.xsd)】；

### 实践	

参考界面原[销售合同导入](uiinvoke/00/zh_CN/theme0/SN-HELP.Ord.SalorderList.html)不再维护
参考界面新[Excel导入测试](uiinvoke/00/zh_CN/theme0/SN-HELP.Excel.ExcelImportDemo.html)

### 设计


#### 导入类型

##### 类型说明

导入类型用于扩展Excel导入的解析方式：
1、扩展接口：ExcelImportParser；
2、当前支持：
（1）单表解析：SN-PLAT.01=ExcelImportOneTableParser（参见对应的单元测试类ExcelImportOneTableParserTest）；
（2）主子解析：SN-PLAT.02=ExcelImportMainDetailParser（参见对应的单元测试类ExcelImportMainDetailParserTest）；
（3）MapList：SN-PLAT.06=ExcelImportMapListParser（参见对应的单元测试类ExcelImportMapListParserTest）；

##### 类型类图

@startuml
Title Excel导入类型类图

ExcelImportParser <.. AbstractExcelImportParser

AbstractExcelImportParser <.. ExcelImportOneTableParser 
AbstractExcelImportParser <.. ExcelImportMainDetailParser   
AbstractExcelImportParser <.. ExcelImportMapListParser  

class ExcelImportParser<T> {
	public T parse(ExcelReader reader);
	public void afterParse(List<?> list);
}


class AbstractExcelImportParser {
	protected Object parseCellValue();
}

class ExcelImportOneTableParser {
	// 返回单表VO链表
}

class ExcelImportMainDetailParser {
	// 返回主子表主表VO链表
}

class ExcelImportMapListParser {
	// 将Excel中行转换为Map链表返回
}

@enduml


#### 标准导入



##### 标准导入类图

@startuml
Title Excel导入类图

sv.ExcelImportParser <|.. sv.AbstractExcelImportParser
sv.AbstractExcelImportParser <|-- sv.ExcelImportMainDetailParser
sv.AbstractExcelImportParser <|-- sv.ExcelImportMapListParser
sv.AbstractExcelImportParser <|-- sv.ExcelImportOneTableParser

sv.ExcelImportParser <.. sv.ExcelImportService
sv.ExcelImportService <.. sv.ExcelImportFactory
sv.ExcelImportFactory <--* sv.ExcelImportController

sv.ExcelImportController <.. ui.ExcelImportProgressListener

ui.ExcelImportProgressListener <.. js.ExcelImportListener

js.ExcelImportDefService <.. js.ExcelImportListener

class js.ExcelImportListener {
	# String xlscode;
	# boolean hiddenDownBtn;
	# JSObject runParam;
	# void excelImport(Table table);
	# void processReturnValue(Object returnValue);
}

class js.ExcelImportDefService {
	+ void syncToDB(String xlscode);
	+ String downloadTemplate(String xlscode);
	+ XlsJS getJS(String xlscode);
}

class ui.ExcelImportProgressListener {
	// Excel导入功能接入层处理监听类
	+ Object run(UIEvent event);
}

interface sv.ExcelImportController {
	// 分布式服务接入器
	+ T excelImport(ExcelImportEntity entity);
}

interface sv.ExcelImportFactory {
	+ ExcelImportDef getExcelImportDef(String xlscode);
	+ ExcelImportService newExcelImportService(String xlscode);
}

interface sv.ExcelImportService {
	+ T excelImport(InputStream in, String suffix);
	+ T excelImport(InputStream in, String suffix, Map runParam);
}

interface sv.ExcelImportParser {
	+ T parse(ExcelReader reader, Map runParam);
}

abstract class sv.AbstractExcelImportParser {
	# ExcelImportDef xlsDef;
	# Object parseRecord(ExcelRow row);
	# Map parseMap(ExcelRow row);
}

class sv.ExcelImportMainDetailParser{
	// 主子
}
class sv.ExcelImportOneTableParser{
	// 单表
}
class sv.ExcelImportMapListParser{
	// MapList
}
@enduml

##### 标准导入时序图

@startuml

Title Excel导入时序图

actor 应用层

应用层 -> js.ExcelImportListener
js.ExcelImportListener -> ui.ExcelImportProgressListener : [run]从客户端到服务端的接入
ui.ExcelImportProgressListener -> ExcelImportController : [excelImport]根据系统号\n确定调用的分布式服务
note over ui.ExcelImportProgressListener 
	客户端调用Excel导入，
	在此处处理分布式服务。
end note
ExcelImportController -> ExcelImportFactory : [newExcelImportService]\n创建Excel导入服务示例
ExcelImportFactory --> ExcelImportController : [return]返回
ExcelImportController -> ExcelImportService : [excelImport] 读取Excel

ExcelImportService -> ExcelImportTypeService : [getExcelImportType]\n获取导入类型对象
ExcelImportTypeService --> ExcelImportService : [return]返回
ExcelImportService -> ExcelImportParser : [parse]根据配置解析Excel
ExcelImportParser --> ExcelImportService : [return]返回
ExcelImportService --> ExcelImportController : [return]返回
ExcelImportController --> ui.ExcelImportProgressListener : [return]返回
ui.ExcelImportProgressListener --> js.ExcelImportListener : [return]返回
js.ExcelImportListener --> 应用层 : [return]返回

@enduml

##### 标准导入使用说明
1. 标准场景：可以配置客户端监听类[snsoft.plat.bas.excel.ExcelImportListener]；
2. 非标准场景：可以并且建议自行调用Excel导入服务
3. 参考示例：参考界面[销售管理导入](uiinvoke/00/zh_CN/theme0/SN-HELP.Ord.SalorderList.html)
4. 打印格式帮助文档[打印格式帮助](help.html?helpFile=help/SN-CMC/Print/PrintDef.md)
```
	ExcelImportFactory	xlsFactory =  SpringBeanUtils.getBeanByName("SN-BAS.ExcelImportFactory");
	ExcelImportService<T> xlsService = xlsFactory.newExcelImportService(xlscode/xlsDef);
	T result = xlsService.excelImport(xlsdata,xlstype);
	// 默认值处理、校验、存盘、调用等等
	...
```


#### 策略导入

##### 策略导入类图


@startuml
Title Excel导入类图

sv.ExcelImportParser <|.. sv.AbstractExcelImportParser
sv.AbstractExcelImportParser <|-- sv.ExcelImportMainDetailParser
sv.AbstractExcelImportParser <|-- sv.ExcelImportMapListParser
sv.AbstractExcelImportParser <|-- sv.ExcelImportOneTableParser

sv.ExcelImportParser <.. sv.ExcelStrategyImportService
sv.ExcelImportParser <.. sv.ExcelImportService
sv.ExcelImportService <--* sv.ExcelStrategyImportController
sv.ExcelStrategyImportService <--* sv.ExcelStrategyImportController

sv.ExcelStrategyImportController <.. ui.ExcelStrategyImportProgressListener

ui.ExcelStrategyImportProgressListener <.. js.ExcelStrategyImportDialogPaneListener

js.ExcelImportDefUIService <.. js.ExcelStrategyImportDialogPaneListener



class js.ExcelStrategyImportDialogPaneListener {
	+ void oncmd_startImport();
	+ void buildAndStart(Jsobject params);
	# void lockStart(boolean isLock);
	# Ajax buildUIInvokeAjax(JSObject params);
	# void onAjaxImportReady(Ajax ajax, int readyStatus);
	# void onAjaxContinueImportReady(Ajax ajax, int readyStatus);
	- void reset();
	- void buildTableData(GridTable tb, Array<JSObject> datas, MenuPaneNode expBtn);
	- void getTableFieldValue(String tablenames, String field, JSObject data);
	- void setReturnValue(StrategyParserResult values)
	
}

interface js.ExcelImportDefUIService {
	+ void queryDefAndCols(String xlscode);
}

class ui.ExcelStrategyImportProgressListener {
	// Excel导入功能接入层处理监听类
	+ Object run(UIEvent event);
}

interface sv.ExcelStrategyImportController {
	+ T excelImport1(ExcelImportEntity entity);
	+ T excelImport2(ExcelImportEntity entity);
}


interface sv.ExcelStrategyImportService {
	+ void save(StrategyParserResult<T> request, Map<String,Object> runParams);
}
interface sv.ExcelImportService {
	+ T excelImport(InputStream in, String suffix);
	+ T excelImport(InputStream in, String suffix, Map runParam);
}

interface sv.ExcelImportParser {
	+ T parse(ExcelReader reader, Map runParam);
}

abstract class sv.AbstractExcelImportParser {
	# ExcelImportDef xlsDef;
	# Object parseRecord(ExcelRow row);
	# Map parseMap(ExcelRow row);
}

class sv.ExcelImportMainDetailParser{
	// 主子
}
class sv.ExcelImportOneTableParser{
	// 单表
}
class sv.ExcelImportMapListParser{
	// MapList
}
@enduml

##### 策略导入时序图

@startuml

Title Excel导入时序图

actor 应用层

应用层 -> js.ExcelStrategyImportDialogPaneListener
js.ExcelStrategyImportDialogPaneListener -> ui.ExcelStrategyImportProgressListener : [run]从客户端到服务端的接入
ui.ExcelStrategyImportProgressListener -> ExcelStrategyImportController : [excelImport]根据系统号\n确定调用的分布式服务
note over ui.ExcelStrategyImportProgressListener 
	客户端调用Excel导入，
	在此处处理分布式服务。
end note
ExcelStrategyImportController -> ExcelImportFactory : [newExcelImportService]\n创建Excel校验服务示例
ExcelImportFactory --> ExcelStrategyImportController : [return]返回结果
ExcelImportController -> ExcelImportService : [excelImport] 读取Excel与数据初步校验
ExcelImportService -> ExcelImportTypeService : [getExcelImportType]\n获取导入类型对象
ExcelImportTypeService --> ExcelImportService : [return]返回
ExcelImportService -> ExcelImportParser : [parse]根据配置解析Excel
ExcelImportParser --> ExcelImportService : [return]返回
ExcelImportService --> ExcelStrategyImportController : [return]，校验成功则直接进行数据存盘，校验失败且非一次性导入，则返回客户端进行询问或下载
ExcelStrategyImportController --> ExcelStrategyImportService : [save]
ExcelStrategyImportService --> ExcelImportParser : [afterParser] 数据入库
ExcelImportParser --> ExcelStrategyImportService : [return] 返回处理结果
ExcelStrategyImportService --> ExcelStrategyImportController : [return] 返回处理结果
ExcelStrategyImportController --> ui.ExcelStrategyImportProgressListener : [return]
ui.ExcelStrategyImportProgressListener --> js.ExcelStrategyImportDialogPaneListener : [return]返回
js.ExcelStrategyImportDialogPaneListener --> 应用层 : [return]返回

@enduml

##### 继续导入

###### 类图



@startuml
Title Excel继续导入类图

sv.ExcelImportParser <|.. sv.AbstractExcelImportParser
sv.AbstractExcelImportParser <|-- sv.ExcelImportMainDetailParser
sv.AbstractExcelImportParser <|-- sv.ExcelImportMapListParser
sv.AbstractExcelImportParser <|-- sv.ExcelImportOneTableParser

sv.ExcelImportParser <.. sv.ExcelStrategyImportService
sv.ExcelStrategyImportService <--* sv.ExcelStrategyImportController

sv.ExcelStrategyImportController <.. ui.ExcelStrategyImportProgressListener

ui.ExcelStrategyImportProgressListener <.. js.ExcelStrategyImportDialogPaneListener




class js.ExcelStrategyImportDialogPaneListener {
	+ void oncmd_startImport();
	+ void buildAndStart(Jsobject params);
	# void lockStart(boolean isLock);
	# Ajax buildUIInvokeAjax(JSObject params);
	# void onAjaxImportReady(Ajax ajax, int readyStatus);
	# void onAjaxContinueImportReady(Ajax ajax, int readyStatus);
	- void reset();
	- void buildTableData(GridTable tb, Array<JSObject> datas, MenuPaneNode expBtn);
	- void getTableFieldValue(String tablenames, String field, JSObject data);
	- void setReturnValue(StrategyParserResult values)
	
}


class ui.ExcelStrategyImportProgressListener {
	// Excel导入功能接入层处理监听类
	+ Object run(UIEvent event);
}

interface sv.ExcelStrategyImportController {
	+ T excelImport1(ExcelImportEntity entity);
	+ T excelImport2(ExcelImportEntity entity);
}


interface sv.ExcelStrategyImportService {
	+ void save(StrategyParserResult<T> request, Map<String,Object> runParams);
}

interface sv.ExcelImportParser {
	+ T parse(ExcelReader reader, Map runParam);
}

abstract class sv.AbstractExcelImportParser {
	# ExcelImportDef xlsDef;
	# Object parseRecord(ExcelRow row);
	# Map parseMap(ExcelRow row);
}

class sv.ExcelImportMainDetailParser{
	// 主子
}
class sv.ExcelImportOneTableParser{
	// 单表
}
class sv.ExcelImportMapListParser{
	// MapList
}
@enduml

###### 时序图


@startuml

Title Excel继续导入时序图

actor 应用层

应用层 -> js.ExcelStrategyImportDialogPaneListener
js.ExcelStrategyImportDialogPaneListener -> ui.ExcelStrategyImportProgressListener : [run]客户端传入数据到服务端
ui.ExcelStrategyImportProgressListener -> ExcelStrategyImportController : [excelImport]根据系统号\n确定调用的分布式服务
note over ui.ExcelStrategyImportProgressListener 
	客户端调用Excel导入，
	在此处处理分布式服务。
end note
ExcelStrategyImportController --> ExcelStrategyImportService : [save]
ExcelStrategyImportService --> ExcelImportParser : [afterParser] 数据入库
ExcelImportParser --> ExcelStrategyImportService : [return] 返回处理结果
ExcelStrategyImportService --> ExcelStrategyImportController : [return] 返回处理结果
ExcelStrategyImportController --> ui.ExcelStrategyImportProgressListener : [return]
ui.ExcelStrategyImportProgressListener --> js.ExcelStrategyImportDialogPaneListener : [return]返回
js.ExcelStrategyImportDialogPaneListener --> 应用层 : [return]返回

@enduml

##### 策略导出

###### 导出类图



@startuml
Title Excel导出类图


ui.ExcelStrategyImportTableListener <.. js.ExcelStrategyImportDialogTableListener




class js.ExcelStrategyImportDialogTableListener {
	+ void oncmd_export1(Table table);
	+ void oncmd_export2(Table table);
	+ void doExport(Table table);
}


class ui.ExcelStrategyImportTableListener {
	+ void prepareLoadData(UIEvent event);
	+ void loadData(UIEvent event);
}
@enduml

###### 导出时序图

@startuml

Title Excel导出时序图

actor 应用层

应用层 -> js.ExcelStrategyImportDialogTableListener

js.ExcelStrategyImportDialogTableListener -> js.ExcelStrategyImportDialogTableListener : [doExport] 导出前数据准备
js.ExcelStrategyImportDialogTableListener -> ui.ExcelStrategyImportTableListener : [loadData] 数据导出
note over ui.ExcelStrategyImportTableListener 
	将数据解析为excel需要的格式
end note
@enduml



##### 参数设计
###### event
ExcelImportEvent
```java
    //获取UserCode
    public String getUsercode()
    {
        return getStringParamValue("Usercode");
    }

    //获取Cuicode
    public String getCuicode()
    {
        return getStringParamValue("Cuicode");
    }

    //获取String类型的参数
    public String getStringParamValue(String key)
    {
        Map<String,Object> runParams = getRunParams();
        return runParams == null?null: StrUtils.obj2str(runParams.get(key));
    }
    //获取运行执行参数
    public Map<String,Object> getRunParams()
    {
        return runParams;
    }
```
###### xlsDef
ExcelImportDef
```java
    //title 标题
	public String getTitle()
	{
		return title;
	}

	public void setTitle(String value)
	{
		this.title = value;
	}
	//导入编号
	public String getXlscode()
	{
		return xlscode;
	}

	public void setXlscode(String xlscode)
	{
		this.xlscode = xlscode;
	}
	//导入类型
	public String getImptype()
	{
		return imptype;
	}

	public void setImptype(String imptype)
	{
		this.imptype = imptype;
	}
    //导入选项 1:默认值;2:校验;3:存盘;8:不抛出异常;16:教研完成不直接走afterParse/save;32:商户日志;64:校验数据类型
	public int getImpopts()
	{
		return impopts == null ? 0 : impopts.intValue();
	}

	public void setImpopts(Integer impopts)
	{
		this.impopts = impopts;
	}

	public void setImpopts(int impopts)
	{
		this.impopts = Integer.valueOf(impopts);
	}
	//当前Excel导入对应的单据号
	public String getSheetcode()
	{
		return sheetcode;
	}

	public void setSheetcode(String sheetcode)
	{
		this.sheetcode = sheetcode;
	}
	//当前Excel导入对应的VO类路径
	public String getVoclass()
	{
		return voclass;
	}

	public void setVoclass(String voclass)
	{
		this.voclass = voclass;
	}
	//当前excel导入对应的解析选项
	public String getReadoptions()
	{
		return readoptions;
	}

	public void setReadoptions(String readoptions)
	{
		this.readoptions = readoptions;
	}
	//当前excel对应的异常模板
	public String getErrtemplate()
	{
		return errtemplate;
	}

	public void setErrtemplate(String errtemplate)
	{
		this.errtemplate = errtemplate;
	}
	//当前excel导入对应的Tac公式
	public String getTacformula()
	{
		return tacformula;
	}

	public void setTacformula(String tacformula)
	{
		this.tacformula = tacformula;
	}
	//当前excel对应的jsConfig,js处理excel导入的结果回执，非策略导入生效
	public String getJsconfig()
	{
		return jsconfig;
	}

	public void setJsconfig(String jsconfig)
	{
		this.jsconfig = jsconfig;
	}
    //当前excel对应的js公式,js处理excel导入的结果回执，非策略导入生效
	public String getJsformula()
	{
		return jsformula;
	}

	public void setJsformula(String jsformula)
	{
		this.jsformula = jsformula;
	}
	//excel导入模板，一般为下载模板
	public String getExceltemplate()
	{
		return exceltemplate;
	}

	public void setExceltemplate(String exceltemplate)
	{
		this.exceltemplate = exceltemplate;
	}
    //excel导入字段
	public void setFields(List<ExcelImportFieldDef> fields)
	{
		this.fields = fields;
	}

	public List<ExcelImportFieldDef> getFields()
	{
		if (fields == null)
		{
			fields = new ArrayList<ExcelImportFieldDef>();
		}
		return this.fields;
	}
	//excel导入表名
	public String getTblname()
	{
		return tblname;
	}

	public void setTblname(String tblname)
	{
		this.tblname = tblname;
	}
	//excel导入操作权限
	public String getOptids()
	{
		return optids;
	}

	public void setOptids(String optids)
	{
		this.optids = optids;
	}
```

##### fldDef
ExcelImportFieldDef
```java
    //excel导入编号
    public String getXlscode()
	{
		return xlscode;
	}

	public void setXlscode(String xlscode)
	{
		this.xlscode = xlscode;
	}
    //表列，对应vo字段或对应数据库表字段
	public String getTablecolumn()
	{
		return tablecolumn;
	}

	public void setTablecolumn(String tablecolumn)
	{
		this.tablecolumn = tablecolumn;
	}
	//字段标题
	public String getTitle()
	{
		return title;
	}

	public void setTitle(String title)
	{
		this.title = title;
	}
	//excel字段（A,B等）
	public String getExcelcolumn()
	{
		return excelcolumn;
	}

	public void setExcelcolumn(String excelcolumn)
	{
		this.excelcolumn = excelcolumn;
	}
	//码表
	public String getCodedata()
	{
		return codedata;
	}

	public void setCodedata(String codedata)
	{
		this.codedata = codedata;
	}
	//码表参数
	public String getCmparams()
	{
		return cmparams;
	}

	public void setCmparams(String cmparams)
	{
		this.cmparams = cmparams;
	}
	//导入选项0x0001 : 无码返空;0x0002 : 转为大写;0x0004 : 转为小写;0x0008 : 超长截断;0x0010 : 
	// 首字大写;0x0020 : 为空取上;0x0040 : 非空判断;0x0080 : 验证小数位;0x0100 : 图片格式;
	// 0x0200 : 两端去空(针对字符串);0x0400 : 生成缩略图
	public int getCtrlopts()
	{
		return ctrlopts == null ? 0 : ctrlopts.intValue();
	}

	public void setCtrlopts(int ctrlopts)
	{
		this.ctrlopts = Integer.valueOf(ctrlopts);
	}

	public void setCtrlopts(Integer ctrlopts)
	{
		this.ctrlopts = ctrlopts;
	}
	//字段类型
	public Integer getSqltype()
	{
		return sqltype;
	}

	public int getSqltypeAsInt()
	{
		return sqltype == null ? 0 : sqltype.intValue();
	}

	public void setSqltype(Integer sqltype)
	{
		this.sqltype = sqltype;
	}
	//最大长度 超长截断生效
	public Integer getMaxlength()
	{
		return maxlength;
	}

	public int getMaxlengthAsInt()
	{
		return maxlength == null ? 0 : maxlength;
	}

	public void setMaxlength(Integer maxlength)
	{
		this.maxlength = maxlength;
	}
	//字段转换tac
	public String getTacformula()
	{
		return tacformula;
	}

	public void setTacformula(String tacformula)
	{
		this.tacformula = tacformula;
	}
```

###### data
行数据
当导入类型为 SN-PLAT.01|单表导入 的情况下  行数据为表vo
当导入类型为 SN-PLAT.02|主子导入 的情况下  合并之前行数据为主子表一对一的vo，合并之后为行数据为主子表一对多的vo
当导入类型为 SN-PLAT.06|MapList  的情况下  行数据为map

###### dataList
表数据
当导入类型为 SN-PLAT.01|单表导入 的情况下  表数据为vo的list集合
当导入类型为 SN-PLAT.02|主子导入 的情况下  合并之前表数据为主子表一对一vo的list集合，合并之后为主子表一对多vo的list集合
当导入类型为 SN-PLAT.06|MapList  的情况下  表数据为map的list集合

###### dao
数据持久化dao对象
当导入类型为 SN-PLAT.06|mapList的情况下，此参数为空
当存在listsner的save方法返回为true的情况下，此参数为空

###### db
数据持久化db对象
当存在listener的save方法返回为true的情况下，此参数为空

###### row
ExcelRow
```java
//当前行数据，格式为 {A:valueA,B:valueB}
@Override
public Map<String,String> getSourceData()
{
    return vs;
}
//当前Excel行
@Override
public int getExcelRow()
{
    return row;
}
//当前行数据数组
@Override
public String[] getRowValues()
{
    return rowValues;
}
//根据列名/标题获取当前行列数据
@Override
public String getValue(String c)
{
    if (titleMap != null)
    {
        String key = titleMap.get(c);
        if (key != null)
        {
            String v = vs.get(key);
            if (v != null)
            {
                return v;
            }
        }
        //去掉对星号的兼容
        else
        {
            key = titleMap.get("*" + c);
            if (key != null)
            {
                String v = vs.get(key);
                if (v != null)
                {
                    return v;
                }
            }
        }
    }
    return vs.get(c);
}
//根据列下标获取当前行列数据
@Override
public String getValue(int c)
{
    if (c > rowValues.length - 1)
    {
        return null;
    }
    return rowValues[c];
}
//获取当前行列数据的Bigdecimal值
@Override
public BigDecimal getBig(int c)
{
    return DataUtils.obj2big(getValue(c));
}
//获取当前行列数据的日期值
@Override
public Date getDate(int c)
{
    return DateUtils.toDate(getValue(c));
}
//获取当前行列数据的时间值
@Override
public Date getTime(int c)
{
    return DateUtils.toDate(getValue(c));
}
//获取当前行列数据的int值
@Override
public int getInt(int c)
{
    return StrUtils.obj2int(getValue(c));
}
//获取当前行列数据的长整型值
@Override
public long getLong(int c)
{
    return StrUtils.obj2int(getValue(c));
}
//获取左上角在当前行的所有图片信息
@Override
public Map<String,List<ExcelCellPicture>> getRowPics()
{
    return rowPics;
}
//获取左上角在当前行列的所有图片信息
@Override
public List<ExcelCellPicture> getPics(String col)
{
    Map<String,List<ExcelCellPicture>> rowPics = getRowPics();
    String titleColumn = getTitleColumn(col);
    if (rowPics != null)
    {
        return rowPics.get(titleColumn);
    }
    return null;
}
//验证标题
@Override
public boolean validTitle(ExcelImportFieldDef def)
{
    //...
}
```

##### 其他说明
Excel字段设置值 已提出公共程序
针对MapList类型数据，设置值调用snsoft.bastool.excel.service.ExcelMapValueSetBean#setValue
针对VOList类型数据，设置值调用snsoft.bastool.excel.service.ExcelVOValueSetBean#setValue

###### 说明
1. 针对MapList类型数据
解析Bean SN-BAS.ExcelImportMapListParser
Excel导入类型 SN-PLAT.06
Bean前缀 ExcelMapValueSetBean

1. 根据字段类型（Excel导入配置字段定义的值类型）映射
数值->java.math.BigDecimal
整数->java.lang.Integer
文本->java.lang.String
日期->java.util.Date

1. 针对VOList类型数据
解析Bean SN-BAS.ExcelImportOneTableParser,SN-BAS.ExcelImportMainDetailParser
Excel导入类型 SN-PLAT.01,SN-PLAT.02
Bean前缀 ExcelVOValueSetBean

1. 根据字段类型映射
如果Excel导入字段有对应的值类型，则使用值类型作为类型转换
如果不存在对应的值类型，则使用Excel导入VO对应的字段实际类型
基本类型将会转换为对应的包装类
void会转换为对应的Void.class 

###### 获取Bean逻辑
1. 根据其类型与父类（不包括接口），递归获取Bean
比如BigDecimal->Number->Object
则查找Bean逻辑为
ExcelMapValueSetBeanForBigDecimal
ExcelMapValueSetBeanForNumber
ExcelMapValueSetBean

1. 如果字段值为空，则按照Void处理
查找Bean逻辑为
ExcelMapValueSetBeanForVoid
ExcelMapValueSetBean
