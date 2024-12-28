### 界面定义创建工具【XML】

|日期|作者|备注|
|------|------|------|
|2019-10-23|冯继亮|初稿|

### 指南
[界面定义创建工具【XML】](uiinvoke/00/zh_CN/theme0/90.UICREATEXML.html?_blank)

```
VO对象：
	暂不支持多个主VO。

QueryParam对象:
	若是内部类，最后一个分割符用$
	例：snsoft.code.user.service.UserDataService$UserDataParams

生成路径：
	因为界面定义号及系统号是按路径截取的，所以建议使用真实的界面定义路径
	例：D:\snsoft90\sn_ft\ft-trd\trd-ui\src\main\resources\cfg\ui\res\FT-TRD\Def\

文件名称：
	不包含文件后缀。
	
窗口组织节点：
	QueryParamPane：与DialogPanel的不同，QueryParamPane会默认输出QueryParam查询列。
	VOGridTable | VORecordTable：通过 name 属性绑定预设的界面属性,与GridTable | RecordTable的不同, 当前标签会通过 sqlexpr 属性绑定VO对象，输出VO列。
	P：相当于Paenl
	其他节点与大师的窗口组织描述含义一致。

注：参数录入框的辅助录入使用ClassLoader加载服务内的文件，非当前服务内文件查询不到。

```




### 手册
- xml配置文件路径：cfg/res/uicreatexml/UICreateXml*.xml
- [UICreateXMLDef.xsd](http://www.snsoft.com.cn/schema/UICreateXMLDef.xsd?_blank)
- [界面工具配置【XML】](uiinvoke/00/zh_CN/theme0/90.UIKITDEFXML.html?_blank)

#### 文件配置说明

##### 界面场景配置

字段说明：
```
默认窗口组织：
	1. 可以提供多个默认布局，以 <!--$ 分割。
		例：
			<!--$ 单表模式-->
			<B fullPage="true">
			    <QueryParamPane name="param" region="north"/>
			    <VOGridTable name="list" mainui="param" sqlexpr="{mTable}" />
			</B>
			<!--$ 上下布局-->
			<B fullPage="true">
			    <QueryParamPane name="param" region="north"/>
			    <V uiprops.leftSize="500" region="center" >
			        <VOGridTable name="list" mainui="param" sqlexpr="{mTable}" />
			        <VOGridTable name="glist" mainui="list" sqlexpr="{gTable}" />
			    </V>
			</B>
	2. mTable 默认取VO的主表，gTable 需要手动指定表，必须以OneMany的对应方式存在于VO中。	
	3. 通过窗口组织描述添加的节点属性高于配置定义的属性。
```

##### 界面属性配置
字段说明：
```
界面名称：
	生成xml时，通过界面名称获取界面属性。

```
##### 界面单元配置
字段说明：
```
字段名：
		生成xml时，通过 界面名称 + 字段名 获取界面单元属性。
		注：优先级高于界面标准单元属性。

固定列：
		适用于强制输出例，如工具条按钮、界面操作列等无法动态获取的列。

```
##### 界面标准单元配置
字段说明：
```
字段类型：
		生成xml时，通过 界面名称 + 字段类型 获取界面单元属性。

```
##### 界面属性及单元属性
字段说明：
```
支持宏替换属性：
	uilisteners,title,width,uixmlopt.firstcols,uixmlopt.endcols
相关宏：
	"SYSID":系统号 	
	"MUI":窗口号
	"TBLNAME":表名
	"INNERCODE":内码列名
	"UINAME":界面名
	"SHEETCODE":单据号
	"SHEETOUTFLD":单据外码
	"FLDNAME":字段名
	"FLDDISPLAYNAME":字段显示名
	
```

#### 类图

@startuml

Title "UI XML Builder 主要类关系图"


UXBuilder <|.. AbstractUXBuilder
AbstractUXBuilder <|-- 窗口.AbstractStdwlayoutUXBuilder
AbstractUXBuilder <|-- 容器.AbstractContainerUXBuilder
AbstractUXBuilder <|-- 单元列.AbstractFieldUXBuilder


	note left of "UXBuilder" : 构建器超类
	note left of "AbstractUXBuilder" : 构建器抽象类：\n抽象Element构建流程，提取公共方法
	note bottom of "窗口.AbstractStdwlayoutUXBuilder" : 窗口构建器抽象类：\n递归窗口组织描述，组织容器参数，调用容器构建器构建容器节点
	note bottom of "容器.AbstractContainerUXBuilder" : 容器构建器抽象类：\n 1 获取配置定义填充Element节点属性，\n 2 根据VO,QueryParam,表结构定义等信息组织单元列参数，调用单元列构建器构建单元列节点
	note bottom of "单元列.AbstractFieldUXBuilder" : 单元列构建器抽象类：\n 1 获取配置定义填充Element节点属性，\n 2根据VO，QueryParam,表结构定义等列信息生成节点属性

interface UXBuilder<T extends UXParams>{
	+ {abstract} UXElement create(T params)
	+ default UXBuilder newBuilder(String type,String suffix)
}

abstract class AbstractUXBuilder<T extends UXParams>{
	# void doBeforeCreate(T params)
	# UXElement initElement(T params)
	# UXElement doCreate(T params, UXElement element)
	# void doAfterCreate(T params)
}

abstract class 窗口.AbstractStdwlayoutUXBuilder<T extends UXStdwlayoutParams>{
	# UXElement recursionBuilder(Element _doc, UXElement element, UXParams params)
	# UXContainerParam newUXContainerParam(Element _doc, UXElement element, UXParams params)
}

abstract class 容器.AbstractContainerUXBuilder<T extends UXContainerParams>{
	# UXFieldParam newUXFieldParam(T params)
	# String getContainerType(T params)
}

abstract class 单元列.AbstractFieldUXBuilder<T extends UXFieldParams>{
	# UXElement doCreate(T params, UXElement element)
	# String getTypeName(T params)
}

@enduml


@startuml
Title "容器 UI XML Builder 类图"

AbstractContainerUXBuilder <|-- 表格.AbstractTableUXBuilder
表格.AbstractTableUXBuilder <|-- 表格.VOGridTableUXBuilder
表格.AbstractTableUXBuilder <|-- 表格.VORecordTableUXBuilder

AbstractContainerUXBuilder <|-- 面板.AbstractPanelUXBuilder
面板.AbstractPanelUXBuilder <|-- 面板.QueryParamPanelUXBuilder
面板.AbstractPanelUXBuilder <|-- 面板.DialogPanelUXBuilder

AbstractContainerUXBuilder <|-- 布局.AbstractLayoutUXBuilder
布局.AbstractLayoutUXBuilder <|-- 布局.HLayoutUXBuilder
布局.AbstractLayoutUXBuilder <|-- 布局.VLayoutUXBuilder
布局.AbstractLayoutUXBuilder <|-- 布局.TabPanelLayoutUXBuilder

AbstractContainerUXBuilder <|-- 页面.AbstractPageUXBuilder
页面.AbstractPageUXBuilder <|-- 页面.BPageUXBuilder
页面.AbstractPageUXBuilder <|-- 页面.PageBodyUXBuilder


abstract class AbstractContainerUXBuilder<T extends UXContainerParams>{
	# UXFieldParam newUXFieldParam(T params)
	# String getContainerType(T params)
}
abstract class 面板.AbstractPanelUXBuilder<T extends UXContainerParams>{
}
abstract class 布局.AbstractLayoutUXBuilder<T extends UXContainerParams>{
}
abstract class 页面.AbstractPageUXBuilder<T extends UXContainerParams>{
}
abstract class 表格.AbstractTableUXBuilder<T extends UXContainerParams>{
}


@enduml


@startuml
Title "单元列 UI XML Builder 类图"



AbstractFieldUXBuilder <|-- 面板字段.DialogPanelFieldUXBuilder
AbstractFieldUXBuilder <|-- 表格字段.AbstractTableFieldUXBuilder
AbstractFieldUXBuilder <|-- 工具条按钮.ToolbarFieldUXBuilder
表格字段.AbstractTableFieldUXBuilder <|-- 表格字段.GridTableFieldUXBuilder
表格字段.AbstractTableFieldUXBuilder <|-- 表格字段.RecordTableFieldUXBuilder


abstract class AbstractFieldUXBuilder<T extends UXFieldParams>{
	# UXElement doCreate(T params, UXElement element)
	# String getTypeName(T params)
}
abstract class 表格字段.AbstractTableFieldUXBuilder<T extends UXFieldParams>{
}

@enduml

@startuml
Title "UI XML Builder 构建参数类图"

UXParams <|-- UXStdwlayoutParams
UXParams <|-- UXContainerParams
UXContainerParams <|-- UXFidldParams

	note left of "UXStdwlayoutParams":窗口构建参数
	note right of "UXContainerParams":容器构建参数
	note right of "UXFidldParams":单元列构建参数

@enduml


#### 流程图

@startuml

Title 时序图

participant uilistener as "CreateUIXmlListener"

activate uilistener
	uilistener -> uilistener:参数检查
	uilistener -> uilistener:构建窗口参数\n UXStdwlayoutParams
	activate UXBuilder
		uilistener -> UXBuilder:获得默认窗口构建器 \n UXBuilder.defaultLayoutBuilder
		UXBuilder -> uilistener
		uilistener -> AbstractStdwlayoutUXBuilder: 调用窗口构建器
		activate AbstractStdwlayoutUXBuilder
			AbstractStdwlayoutUXBuilder -> AbstractStdwlayoutUXBuilder:递归窗口组织 \n 构建容器参数 UXContainerParams
			AbstractStdwlayoutUXBuilder -> UXBuilder:获得对应容器构建器 \n UXBuilder.newBuilder
			UXBuilder -> AbstractStdwlayoutUXBuilder
			AbstractStdwlayoutUXBuilder -> AbstractContainerUXBuilder:调用容器构建器
			activate AbstractContainerUXBuilder
				AbstractContainerUXBuilder -> AbstractContainerUXBuilder:根据UIXML定义属性，生成XML节点属性
				AbstractContainerUXBuilder -> AbstractContainerUXBuilder:根据UIXML定义属性，添加固定列
				AbstractContainerUXBuilder -> AbstractContainerUXBuilder:通过VO，QueryParam，表结构定义，\n构建单元列参数 UXFidldParams
				AbstractContainerUXBuilder -> UXBuilder:获得对应容器构建器 \n UXBuilder.newBuilder
				UXBuilder -> AbstractContainerUXBuilder
	deactivate UXBuilder
				AbstractContainerUXBuilder -> AbstractFieldUXBuilder:调用单元列构建器
				activate AbstractFieldUXBuilder
					AbstractFieldUXBuilder -> AbstractFieldUXBuilder: 根据UIXML定义属性，生成XML节点属性
					AbstractFieldUXBuilder -> AbstractContainerUXBuilder:返回单元列节点
				deactivate AbstractFieldUXBuilder
				AbstractContainerUXBuilder -> AbstractStdwlayoutUXBuilder:返回容器节点
			deactivate AbstractContainerUXBuilder
		AbstractStdwlayoutUXBuilder -> AbstractStdwlayoutUXBuilder:根据组装完成的窗口节点生成xml文件
		AbstractStdwlayoutUXBuilder -> uilistener:UI XML 生成完成
		deactivate AbstractStdwlayoutUXBuilder
deactivate uilistener

@enduml

### 设计

- 规范界面结构，提供标准界面属性及字段属性。
- 通过自定义窗口窗口组织，生成Table列信息及查询框信息，提高界面开发效率。




<script>
// 通过 js  实现  url 打开新窗口
var aTagArr = [].slice.apply(document.getElementsByTagName("a"));

aTagArr.forEach(function (e, i) {
  e.href.indexOf("_blank") > -1 ? e.target = "_blank" : null;
});
</script>





	
