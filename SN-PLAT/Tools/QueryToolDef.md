## 查询工具

|日期|作者|备注|
|------|------|------|
|2018-08-27|冀小雷|初稿|
|2019-10-11|吴小海|重构|

### 指南	
#### 概念说明

1. 查询工具用于补数；
1. XML配置请参见对应的Schema文件【[tool-query.xsd](do/BrowseSchema?schema=tool-query.xsd)】；
1. 查询工具配置文件路径：resources/cfg/res/qrytool/*.xml；

#### 使用说明

##### 监听配置
1. UI层补数
```xml
<uilisteners><![CDATA[
	snsoft.plat.bas.qrytool.QueryToolListener.new?qrycode=[查询工具配置编码]
]]></uilisteners>
```

1. 服务层补数
```xml
<Functions>
	<Functype>SN-PLAT.QR</Functype>
	<Subtype>2</Subtype>
	<Funcimpl>snsoft.plat.bas.qrytool.service.impl.QueryToolListener.new?qrycode=[查询工具配置编码]</Funcimpl>
	<Remark>查询工具：服务层补数</Remark>
</Functions>
```

##### XML文件配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<QueryToolDef xmlns="http://snsoft.cn/schema/tool-query.xsd">
	<Title>查询工具标题</Title>
	<Remark><![CDATA[
  		相关说明
  	]]></Remark>
	<Details>
		<Title>子查询标题</Title>
		<Remark>子查询说明</Remark>
		<MatchColumns>匹配列：col,col1=col2</MatchColumns>
		<ResultColumns>结果列：col,col1=col2</ResultColumns>
		<LoadSql><![CDATA[
			子查询加载SQL
		]]></LoadSql>
		<FilterColumn>主表过滤列名</FilterColumn>
		<Dsid>子查询数据源</Dsid>
		<TableName>子查询取数据源表名</TableName>
		<LoadImpl><![CDATA[
			子查询加载Bean 或 Tac
		]]></LoadImpl>
		<CMFilter><![CDATA[
            cuicode查询条件 在启用商户的情况下生效  需要 LoadSql 配置宏<cmfilter> 配合使用  当使用时，会拼装成 LoadSql and {cmfilter}
		]]></CMFilter>
		<Aggs>
			<ColumnName>列名</ColumnName>
			<AggType>聚合类型</AggType>
		</Aggs>
	</Details>
</QueryToolDef>
```

### 手册		
		
### 实践	
	
### 设计

#### 类图

@startuml
Title 查询工具类图
QueryToolDataLoader <|-- AbstractQueryToolDataLoader
AbstractQueryToolDataLoader <|.. SupTac

QueryToolDataLoader <.. AbstractSubDataService

SubDataService <|.. AbstractSubDataService
AbstractSubDataService <|-- SubDataServiceForDataSet
AbstractSubDataService <|-- SubDataServiceForList

SubDataService <.. QueryToolService

QueryToolService <.. ui.QueryToolListener
QueryToolServiceFactory <.. ui.QueryToolListener

QueryToolService <.. QueryToolServiceFactory

QueryToolService <.. sv.QueryToolListener
QueryToolServiceFactory <.. sv.QueryToolListener

interface QueryToolService<<P>> {
	void supData(P mainData);
}

interface QueryToolServiceFactory {
	<<T>> QueryToolService<T> newQueryToolService(String qrycode);
}

class ui.QueryToolListener {
	+ ReadDataSet onDataLoaded();
}
class sv.QueryToolListener {
	+ afterQuery();
}

interface SubDataService<<R>> {
	void dealData(R mainData);
}

abstract class AbstractSubDataService<<P>> {
	# QueryToolDef	qryDef;
	# {abstract} void supplementData();
	# {abstract} void fillFilterValues();
}

class SubDataServiceForDataSet<<R>> {
	
}

class SubDataServiceForList<<R>> {
	
}

interface QueryToolDataLoader {
	R loadData(P mainData);
}

interface AbstractQueryToolDataLoader {
	{static} Set<String> getColumnValues();
}

class SupTac {
	
}
@enduml

#### 时序图

@startuml
Title 界面数据分组[UI]时序图

actor 客户端
participant lis   as "QueryToolListener"
participant fac as "QueryToolServiceFactory"
participant sv as "QueryToolService"
participant sup    as "AbstractSubDataService"
participant loader    as "QueryToolDataLoader"

客户端 -> lis : [onDataLoaded/afterQuery]\n调用查询工具服务
activate lis
	lis -> fac : [newQueryToolService]\n创建查询工具服务
	activate fac
	fac -> lis : 
	deactivate fac
	
	lis -> sv : [supData]\n查询工具补数服务入口
	activate sv
		sv -> sup : [dealData]
		activate sup
			sup -> sup : [getSubData] \n加载补数数据
			activate sup
				sup -> sup : [loadSubDataByLoadimpl] \n加载数据
				activate sup
					sup -> loader : [loadData]\n加载补数数据
					activate loader
					loader -> sup :
					deactivate loader
				deactivate sup
				
				sup -> sup : [fillFilterValues] \n加载主表过滤列值
				activate sup
				deactivate sup
			deactivate sup
			
			sup -> sup : [supplementData] \n补数逻辑
			activate sup
				sup -> sup : [agg] 聚合数据
				activate sup
				deactivate sup
				
				sup -> sup : [appendData] 补数
				activate sup
				deactivate sup
			deactivate sup
		sup -> sv : 
		deactivate sup
	sv -> lis : 
	deactivate sv
lis -> 客户端 :
deactivate lis
@enduml








