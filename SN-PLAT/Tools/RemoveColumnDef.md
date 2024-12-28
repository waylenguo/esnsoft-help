## 删除列
|日期|作者|备注|
|------|------|------|
|2018-12-21|冀小雷|初稿|

### 业务说明

相同的业务针对不同的岗位有不同的显示要求，如商品信息中的成本价与销售价，后台维护人员可以看到所有信息，但是针对业务员则要求只能看到销售价，不能看到成本价；针对这种需求给出三种方案：
1. 在同一个界面处理：针对特定岗位如业务员岗，服务端处理不加载数据，客户端不展示对应的列；此种处理方案可能导致逻辑错误；尤其是涉及到数据写操作时的错误，是致命的；
1. 使用不同的界面进行处理：由于后台数据权限相同，只要业务员记住后台页面的URL即可访问；或者控制URL权限，由于页面量太大，权限控制无法应用；
1. 不考虑写操作，不考虑业务逻辑，使用同一个界面解决此问题；

其他说明：
1. 效果参见[帮助中心实例](uiinvoke/00/zh_CN/theme0/SN-HELP.Plat.RMColDemo.html)
1. 本功能仅支持数据库配置，开发环境不使用，应用层开发不考虑此情况；
1. 此功能仅用于客户的特殊要求，内部不做推广；

### 配置说明

1. 界面列：
	* 界面号：界面号；
	* 界面名：界面名；
	* 匹配岗位：用于设置匹配的岗位，多选；
	* 删除列：设置该岗位需要删除的列，多个逗号分隔；
1. 权限：默认根据运维部门进行权限控制，与组织结构权限相同；

### 监听配置

#### 界面配置

```xml
<uilisteners><![CDATA[
	snsoft.plat.bas.rmcol.RemoveColumnUIListener.new
]]></uilisteners>
<jslistener><![CDATA[
	#new snsoft.plat.bas.rmcol.RemoveColumnCliListener({})
]]></jslistener>
```

#### 系统功能配置

```xml
<Groups id="删除列">
	<Functions>
		<Functype>SN-PLAT.UI</Functype>
		<Subtype>2</Subtype>
		<Funcimpl>
			snsoft.plat.bas.rmcol.RemoveColumnUIListener.new
		</Funcimpl>
		<Remark>初始化删除列+数据加后摸出删除列数据</Remark>
	</Functions>
	<Functions>
		<Functype>SN-PLAT.SV</Functype>
		<Subtype>2</Subtype>
		<Funcimpl>
			#new snsoft.plat.bas.rmcol.RemoveColumnCliListener({})
		</Funcimpl>
		<Remark>客户端删除列</Remark>
	</Functions>
</Groups>
```


### 类图

@startuml
Title 列删除类图

RemoveColumnDef <.. RemoveColumnService
RemoveColumnService <|.up. RemoveColumnServiceImpl
RemoveColumnService <.. ui.RemoveColumnListener

interface RemoveColumnService {
	+ QueryResults<RemoveColumnDef> queryUI(RMColQueryParams params);
	+ SaveResults saveUI(SaveParams<RemoveColumnDef> params);
	+ Set<String> getRemoveColumns(String muiid, String uiname);
	+ ArrayReadDataSet rebuildDataSet(UIComponent ui, ArrayReadDataSet ars);
}

class RemoveColumnServiceImpl {
}

class ui.RemoveColumnListener {
	+ void initComponent();
	+ ReadDataSet onDataLoaded();
}

class js.RemoveColumnListener {
	+ void initComponent();
}

class RemoveColumnDef {
	- String muiid;
	- String uiname;
	- Integer uflag;
	- String gwcodes;
	- String rmcols;
	- String limbcode;
	- String cuicode;
	- String remark;
}

@enduml
