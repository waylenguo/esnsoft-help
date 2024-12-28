## 穿透配置
作者：冀小雷

### 指南

**概念**
用于处理客户端根据关系列值打开对应的明细页面。
<b>开放式设计及实现</b>，仅提供双击穿透列后调用对应的逻辑代码，如果返回指定的对象信息，则进行标准界面打开，否则不做任何处理。
格式参见【[tool-viewdetail.xsd](do/BrowseSchema?schema=tool-viewdetail.xsd)】

**监听配置**

```xml
<!--界面监听
建议不配置UI监听，首次双击时加载双击列名。
-->
<jslistener><![CDATA[
	#new snsoft.plat.bas.viewdetail.ViewDetailListener({})
]]></jslistener>

<!--系统功能监听-->
<Functions>
	<Functype>SN-PLAT.JS</Functype>
	<Subtype>2</Subtype>
	<Funcimpl>#new snsoft.plat.bas.viewdetail.ViewDetailListener({})</Funcimpl>
	<Remark>处理穿透逻辑</Remark>
</Functions>
```

### 手册

* 路径：cfg/res/ViewDetail[sysid].xml；
* 建议：穿透时返回<b>funcid</b>，用于支持界面路由功能；

#### Demo

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ViewDetails xmlns="http://snsoft.cn/schema/tool-viewdetail.xsd">
	<!--客户端：JS示例-->
	<Details>
		<Colname>双击列名</Colname>
		<JSBlock><![CDATA[
			info.title = "穿透打开界面标题"
			info.funcid = "功能号" // 优先级高
			info.muiid = "界面号" // 优先级低
			info.pm.AutoRefresh = 1
			info.pm["InitValue.列"] = data.get("列")
			return info
		]]></JSBlock>
		<Remark><![CDATA[
			当前穿透列说明
		]]></Remark>
	</Details>
	<!--服务端：Tac示例-->
	<Details>
		<Colname>双击列名</Colname>
		<!--
		内码=<NULL>：入口新建
		sheetEntry=true：单据入口打开
		-->
		<TACBlock><![CDATA[
			info.title = "穿透打开界面标题"
			info.funcid = "功能号" // 优先级高
			info.muiid = "界面号" // 优先级低
			info.pm.put("AutoRefresh",1)
			info.pm.put("InitValue.列",data.get("列"))
			return info
		]]></TACBlock>
		<Remark><![CDATA[
			当前穿透列说明
		]]></Remark>
	</Details>
</ViewDetails>
```

#### 属性/界面列说明
1. 文件
	说明当前穿透配置，来自文件定义；当前行不可修改；
1. 穿透列名
	主键，需要唯一，配置穿透监听双击列时，根据此列进行判断是否为穿透列；
1. JS代码块

```javascript
/**
 * 此为原始代码，JS代码块会设置在方法体内：
 * 1、table：当前表；
 * 2、dataSet：当前数据集；
 * 3、colname：当前双击的列名；
**/
public DetailInfo execute(data,colname)
{
	var info = {}, info.pm = {};
	// TODO replace by js block
}

// 一个实现的例子
info.title = "Tab页面标题"
if(false)
{
	// TODO 供后期实施扩展用
} else {
	info.funcid = "界面路由号"
	// 或者 info.muiid = "界面定义号"
	info.pm.AutoRefresh = 1
	info.pm["InitValue.innercode"] = dataSet.innerfld
}
return info

// 另一个实现的例子
innercode = dataSet.innerfld
if(innercode==null)
	throw new Error("内码不可为空")
var muiid = "界面定义号";
dialog = Xjs.ui.util.UIUtil.loadDialog(muiid)
Xjs.ui.util.UIUtil.showDialog(dialog)
```
4. Tac代码块

```javascript
/**
 * Tac中可以使用的全局变量：
 * 1、colname：双击的列名；
 * 2、info：已经创建好的DetailInfo对象，可以直接返回；
 * 3、dataSet.get("rootUiid")：客户端界面定义号；
 * 4、dataSet.get("tableName")：客户端界面定义对应的表名；
 * 5、dataSet.get("xxx")：客户端双击行列数据；
**/

// 一个例子的实现
info.title = "Tab页面标题"
if false
	// TODO 供后期实施扩展用
elif
	info.funcid = "界面路由号"
	// 或者 info.muiid = "界面定义号"
	info.pm.put("AutoRefresh",1)
	info.pm.put("InitValue.innercode",dataSet.get("innerfld"))
end if
return info
```
5. 说明
	需要说明当前穿透列的负责人及业务场景；


### 设计

**类图**

@startuml
Title 穿透功能类图

ui.ViewDetailListener ..> ViewDetailService

class ViewDetailService {
	+ List<ViewDetail> queryUI(DefParams params);
	+ void saveUI(List<ViewDetail> records);
	+ void syncToDB(String colname);
	+ String[] getColnames();
	+ String getJSBlock(String colname);
	+ Object invokeTac(String colname, Object cdata);
}
class ui.ViewDetailListener {
	+ void initComponent();
} 
class xjs.ViewDetailListener {
	+ void onTableDblClick();
	+ void oncmd_viewDetail();
	+ {static} boolean hasViewDetailColumn();
	+ {static} boolean invokeViewDetail();
} 
@enduml

**时序图**

@startuml
Title 穿透功能时序图
actor 客户端
participant client as "xjs.ViewDetailListener"
participant server as "ViewDetailService"

客户端 -> client : 双击：onTableDblClick()\n按钮：oncmd_viewDetail()\n调用：ViewDetailListener.invokeViewDetail
activate client

client -> client : invokeViewDetail()
activate client

client -> client : getJSBlock:获取当前列对应的JS代码块
activate client

client -> server : getJSBlock:获取当前列对应的JS代码块
activate server

server -> client : 返回对应的JS代码块 或 空
deactivate server
deactivate client

client -> server : invokeTac:JS代码块为空或返回的打开信息为空，则调用对应的Tac代码块
activate server

server -> client : 返回对应的穿透信息
deactivate client

client -> 客户端 : 根据穿透信息打开对应的界面
deactivate client
@enduml
