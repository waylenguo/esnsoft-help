## 界面数据分组
作者：冀小雷

### 界面数据分组说明

1、XML配置请参见对应的Schema文件【[tool-uigrp.xsd](do/BrowseSchema?schema=tool-uigrp.xsd)】；
2、此功能历史名称：左树分组 或 分组左树；

### 监听配置

界面数据分组分为系统功能配置和界面直接配置方式；
<b>注意：开发使用系统功能配置。界面直接配置是给实施搭建界面时分组使用的。</b>

#### 系统功能配置
1. 系统功能UI监听：snsoft.plat.bas.grp.UIDataGroupListener.new?grpcode=xxx
1. 系统功能JS监听类：#new snsoft.plat.bas.grp.UIDataGroupListener({})
1. 系统功能服务查询监听：snsoft.plat.bas.grp.UIDataGroupQueryListener.new

```xml
<Groups id="界面数据分组">
	<Functions>
		<Functype>SN-PLAT.QR</Functype>
		<Subtype>2</Subtype>
		<Funcimpl>snsoft.plat.bas.grp.UIDataGroupQueryListener.new</Funcimpl>
		<Remark>分组数据加载</Remark>
	</Functions>
	<Functions>
		<Functype>SN-PLAT.UI</Functype>
		<Subtype>2</Subtype>
		<Funcimpl>snsoft.plat.bas.grp.UIDataGroupListener.new?grpcode=xxx</Funcimpl>
		<Remark>加载分组配置</Remark>
	</Functions>
	<Functions>
		<Functype>SN-PLAT.JS</Functype>
		<Subtype>2</Subtype>
		<Funcimpl>#new snsoft.plat.bas.grp.UIDataGroupListener({})</Funcimpl>
		<Remark>处理客户端分组面板展示</Remark>
	</Functions>
</Groups>
```

#### 界面直接配置

此功能不允许有界面查询组件，直接从数据库取数时使用。

1. UI监听类：snsoft.plat.bas.grp.UIDataGroupListener.new?grpcode=xxx
1. JS监听类：#new snsoft.plat.bas.grp.UIDataGroupListener({})

```xml
<uilisteners><![CDATA[
	snsoft.plat.bas.grp.UIDataGroupListener.new?grpcode=xxx
]]></uilisteners>
<jslistener><![CDATA[
	#new snsoft.plat.bas.grp.UIDataGroupListener({})
]]></jslistener>
```


### XML文件配置


```xml
<?xml version="1.0" encoding="UTF-8"?>
<UIDataGroupDef xmlns="http://snsoft.cn/schema/tool-uigrp.xsd">
	<Title>标题</Title>
	<InitColumns>初始分组列名</InitColumns>
	<Remark><![CDATA[
		当前数据分组说明
	]]></Remark>
	<GroupColumns>
		<ColumnName>odate</ColumnName>
		<ColumnTitle>签约日期</ColumnTitle>
	</GroupColumns>
	<GroupColumns>
		<ColumnName>bcode</ColumnName>
		<ColumnTitle>部门</ColumnTitle>
	</GroupColumns>
</UIDataGroupDef>
```

### 分组Bean

1、分组Bean用于处理分组列值的显示及过滤，通过接口[snsoft.plat.bas.grp.service.UIGroupColumn]进行定义；
2、所有Bean实现必须继承默认实现[snsoft.plat.bas.grp.service.impl.DefaultUIGroupColumn]：
（1）必须为原型模式；
（2）必须重写DefaultUIGroupColumn的构造方法；

当前支持的分组Bean：
1、默认[SN-PLAT.DefaultUIGroupColumn]；
2、日期Bean[SN-PLAT.UIGroupColumnForDate]；

### 类图

@startuml
Title 界面数据分组类图
GroupNodeBuilder <|.. AbstractGroupBuilder
GroupFilterBuilder <|.. AbstractGroupBuilder

AbstractGroupBuilder <|-- GroupBuilderForSV
AbstractGroupBuilder <|-- GroupBuilderForUI

UIDataGroupService <.. UIDataGroupQueryListener
UIDataGroupService <.. ui.UIDataGroupListener

UIDataGroupQueryListener ..> GroupNodeBuilder
ui.UIDataGroupListener ..> GroupNodeBuilder

UIDataGroupQueryListener ..> GroupFilterBuilder
ui.UIDataGroupListener ..> GroupFilterBuilder

class GroupNodeBuilder {
	+ GroupNode buildNode();
}
class GroupFilterBuilder {
	+ SqlExpr buildFilter(String levelCode);
}
class AbstractGroupBuilder {
	# {abstract} boolean isDate(String colname);
	# {abstract} String getGroupColumnExpr(String colname);
	# {abstract} Object[][] getGroupValues();
	# {abstract} String getGroupTitle();
	# {abstract} UIGroupColumn getNodeGroupColumn(String colname);
}
class GroupBuilderForSV {
	// 实现父类所有抽象方法
}
class GroupBuilderForUI {
	// 实现父类所有抽象方法
}
class UIDataGroupService {
	+ UIDataGroupDef getGroupDef(String grpcode);
	+ String[][] getGroupColumnValues(String grpcode);
}
class UIDataGroupQueryListener {
	+ void beforeQuery();
	+ void afterQuery();
}
class ui.UIDataGroupListener {
	+ void initComponent();
	+ void prepareQuery();
	+ void prepareLoadData();
	+ void onDataLoaded();
} 
class xjs.UIDataGroupListener {
	+ void initComponent();
	+ void itemValueChanged();
	+ void dataLoaded();
	+ void performCommand();
	+ void dataSetRefreshing();
} 
@enduml

### 时序图

@startuml
Title 界面数据分组[UI]时序图

actor 客户端
participant jslis   as "xjs.UIDataGroupListener"
participant uilis   as "ui.UIDataGroupListener"
participant service as "UIDataGroupService"
participant node    as "GroupNodeBuilder"
participant filter  as "GroupFilterBuilder"

客户端 -> jslis : 数据刷新：\n设置分组项参数
activate jslis
note over jslis: UI层数据分组-数据加载

	jslis -> uilis : onDataLoaded：\n处理分组数据逻辑
	activate uilis
	
		uilis -> service : getGroupDef：\n获取数据分组定义对象
		activate service
		
		service -> uilis : 返回数据分组定义对象
		deactivate service
		
		uilis -> node : buildNode：\n创建分组节点
		activate node
		
		node -> uilis : 返回分组节点
		deactivate node
	
	uilis -> jslis : 返回数据及分组节点
	deactivate uilis

jslis -> 客户端 : 绘制分组节点
deactivate jslis


客户端 -> jslis : 节点过滤：\n设置分组项参数及节点分级参数
activate jslis
note over jslis: UI层数据分组-节点过滤

	jslis -> uilis : prepareLoadData：\n处理节点分级过滤
	activate uilis
	
		uilis -> service : getGroupDef：\n获取数据分组定义对象
		activate service
		
		service -> uilis : 返回数据分组定义对象
		deactivate service
		
		uilis -> filter : buildFilter：\n构建分级节点过滤条件
		activate filter
		
		filter -> uilis : 返回分级节点过滤条件
		deactivate filter
	
	uilis -> jslis : 返回加载的数据
	deactivate uilis

jslis -> 客户端 : 
deactivate jslis

@enduml

@startuml
Title 界面数据分组[SV]时序图

actor 客户端
participant jslis   as "xjs.UIDataGroupListener"
participant uilis   as "ui.UIDataGroupListener"
participant svlis   as "UIDataGroupQueryListener"
participant service as "UIDataGroupService"
participant node    as "GroupNodeBuilder"
participant filter  as "GroupFilterBuilder"

客户端 -> jslis : 数据刷新：\n设置分组项参数
activate jslis
note over jslis: SV层数据分组-数据加载

	jslis -> uilis : prepareQuery：\n参数转换
	activate uilis
	
		uilis -> svlis : afterQuery:\n处理分组数据逻辑
		activate svlis
		
			svlis -> service : getGroupDef：\n获取数据分组定义对象
			activate service
			
			service -> svlis : 返回数据分组定义对象
			deactivate service
			
			svlis -> node : buildNode：\n创建分组节点
			activate node
			
			node -> svlis : 返回分组节点
			deactivate node
		
		svlis -> uilis : 
		deactivate svlis
	
	uilis -> jslis : 返回数据及分组节点
	deactivate uilis

jslis -> 客户端 : 绘制分组节点
deactivate jslis


客户端 -> jslis : 节点过滤：\n设置分组项参数及节点分级参数
activate jslis
note over jslis: SV层数据分组-节点过滤

	jslis -> uilis : prepareQuery：\n参数转换
	activate uilis
	
		uilis -> svlis : beforeQuery:\n处理节点分级过滤
		activate svlis
		
			svlis -> service : getGroupDef：\n获取数据分组定义对象
			activate service
			
			service -> svlis : 返回数据分组定义对象
			deactivate service
			
			svlis -> filter : buildFilter：\n构建分级节点过滤条件
			activate filter
			
			filter -> svlis : 返回分级节点过滤条件
			deactivate filter
		
		svlis -> uilis : 
		deactivate svlis
	
	uilis -> jslis : 返回加载的数据
	deactivate uilis

jslis -> 客户端 : 
deactivate jslis

@enduml