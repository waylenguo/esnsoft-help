## 界面属性控制

|日期|作者|备注|
|------|------|------|
|2019-09-27|冀小雷|重新整理|

### 指南

#### 概念说明

用于根据不同条件动态控制客户端界面组件的相关属性（不同组件控制的属性不同）。
支持控制的组件及组件属性如下：

|<b>组件</b>|<b>属性</b>|
|--|--|
|Table/表||
||addable：新增|
||delable：删除|
||editable：编辑|
||readOnly：只读|
||visible：显示|
|Column/列||
||visible：显示<br/>**Record表需要配合layoutOnClient使用**|
||editable：编辑|
||readOnly：只读|
||nonBlank：强制录入|
||nonBlankOnSubmit：提交检查|
||ignoreTblEdable：表编辑禁止可改|
||ignoreTblRdonly：表只读可改|
||cf=颜色：设置单元前景色,色值为RGB颜色，如：cf=#0040ff|
||cb=颜色：设置单元背景色,色值为RGB颜色，如：cb=#0040ff|
||tf=颜色：设置标题前景色,色值为RGB颜色，如：tf=#0040ff|
||tb=颜色：设置标题背景色,色值为RGB颜色，如：tb=#0040ff|
|Button/按钮|pop=按钮命令号：控制右键菜单按钮|
||disabled：禁用|
||hidden：隐藏|
|Record-GroupPane<br/>Record表分组框||
||visible：显示|
||readOnly：只读(其中的所有列只读)|
|TabPanel/标签页||
||visible：显示|
|OperateButton<br/>操作列按钮||
||disabled：禁用|

#### 使用说明

* 主表配置UI监听：snsoft.ui.optctrl.UIOptCtrlListener.new；

```xml
<!--界面定义UI监听-->
<uilisteners><![CDATA[
	snsoft.ui.optctrl.UIOptCtrlListener.new
]]><uilisteners>

<!--系统功能UI监听-->
<Functions>
	<Functype>SN-PLAT.UI</Functype>
	<Subtype>2</Subtype>
	<Funcimpl>snsoft.ui.optctrl.UIOptCtrlListener.new</Funcimpl>
	<Remark>界面属性控制</Remark>
</Functions>
```

* 配置与界面路径相同的配置文件（支持数据库配置）即可；
	**详细配置参见[OptsCtrl.xsd](do/BrowseSchema?schema=OptsCtrl.xsd)**


### 手册

#### 配置说明

```xml
<?xml version="1.0" encoding="UTF-8"?>
<UIOptCtrl xmlns="http://www.snsoft.com.cn/schema/OptsCtrl.xsd">
	<!--表属性控制-->
	<UIOpts>
		<uiname>表名</uiname>
		<ctrlFields>
			属性名称:
				addable		添加[界面]
				delable		删除[界面]
				editable	编辑[界面+列]
				readOnly	只读[界面+列]
				visible		显示[界面+列]
		</ctrlFields>
		<setValue>true：满足条件设置属性，false：满足条件取消属性</setValue>
		<UIVals>
		</UIVals>
	</UIOpts>
	<!--列属性控制
	Record表列设置visible属性时，对应的列的外层父容器（GroupPane/RecordTable需要设置属性【layoutOnClient="true"】）
	-->
	<UIOpts>
		<uiname>表名</uiname>
		<colname>列名：多个逗号分隔</colname>
		<ctrlFields>
			属性名称:
				editable			编辑[界面+列]
				ignoreTblEdable		表编辑禁止可改[列]
				ignoreTblRdonly		表只读可改[列]
				nonBlank			强制录入[列]
				nonBlankOnSubmit	提交检查[列]
				readOnly			只读[界面+列]
				visible				显示[界面+列]
		</ctrlFields>
		<setValue>true：满足条件设置属性，false：满足条件取消属性</setValue>
		<UIVals>
		</UIVals>
	</UIOpts>
	<!--按钮属性控制-->
	<UIOpts>
		<uiname>表名</uiname>
		<button>按钮命令号：多个逗号分隔</button>
		<ctrlFields>
			属性名称:
				disabled	禁用[按钮]
				hidden		隐藏[按钮]
		</ctrlFields>
		<setValue>true：满足条件设置属性，false：满足条件取消属性</setValue>
		<UIVals>
		</UIVals>
	</UIOpts>
	<!--区域属性控制：Record表中的GroupPane整体显隐控制-->
	<UIOpts>
		<uiname>表名</uiname>
		<border>分组面板名称：多个逗号分隔</border>
		<ctrlFields>
			属性名称:
				visible	显示
		</ctrlFields>
		<setValue>true：满足条件设置属性，false：满足条件取消属性</setValue>
		<UIVals>
		</UIVals>
	</UIOpts>
	<!--标签显隐控制：标签页显示隐藏控制-->
	<UIOpts>
		<uiname>主表表名</uiname>
		<tabpane>标签页中的表名，多个逗号分隔</tabpane>
		<ctrlFields>
			属性名称:
				visible	显示
		</ctrlFields>
		<setValue>true：满足条件设置属性，false：满足条件取消属性</setValue>
		<UIVals>
		</UIVals>
	</UIOpts>
	<!--操作列操作按钮属性控制-->
	<UIOpts>
		<uiname>表名</uiname>
		<colname>操作列名</colname>
		<operateid>操作按钮命令id，多个逗号分隔</operateid>
		<ctrlFields>
			属性名称:
				disabled	禁用
		</ctrlFields>
		<setValue>true：满足条件设置属性，false：满足条件取消属性</setValue>
		<UIVals>
		</UIVals>
	</UIOpts>
	<!--
		以下为值匹配方式
		匹配方式：0:等于(默认);1:Like;2:大于;3:大于等于;4:小于;5:小于等于;6:不等于;8:位与;9:位与(相对8反向);10:部门;11:岗位
	-->
	<!--1.等于（当前表、文本值）-->
	<UIOpts>
		<UIVals>
			<colname>列名</colname>
			<matchValue>匹配值1，匹配值2</matchValue>
		</UIVals>
	</UIOpts>
	<!--2.等于（完整）-->
	<UIOpts>
		<UIVals>
			<uiname>表名</uiname>
			<colname>列名</colname>
			<sqltype>12</sqltype>
			<matchMode>0</matchMode>
			<matchValue>匹配值1，匹配值2</matchValue>
		</UIVals>
	</UIOpts>
	<!--3.取反-->
	<UIOpts>
		<UIVals>
			<uiname>表名</uiname>
			<colname>列名</colname>
			<sqltype>12</sqltype>
			<matchMode>0</matchMode>
			<matchValue>匹配值1，匹配值2</matchValue>
			<negmatch>1</negmatch>
		</UIVals>
	</UIOpts>
	<!--3.操作权限-->
	<UIOpts>
		<sheetcode>单据号</sheetcode>
		<btnOpids>操作编码，多个逗号分隔</btnOpids>
	</UIOpts>
	<!--4.数据权限权限-->
	<UIOpts>
		<sheetcode>单据号</sheetcode>
		<dataOpids>操作编码，多个逗号分隔</dataOpids>
	</UIOpts>
	<!--5.部门-->
	<UIOpts>
		<UIVals>
			<colname>bcode</colname>
			<matchMode>10</matchMode>
			<matchValue>B001,B002</matchValue>
		</UIVals>
	</UIOpts>
	<!--6.岗位-->
	<UIOpts>
		<UIVals>
			<colname>任意存在的列</colname>
			<matchMode>11</matchMode>
			<matchValue>G001,G002</matchValue>
		</UIVals>
	</UIOpts>
	<!--7.脚本-->
	<UIOpts>
		<UIVals>
			<colname>需要触发的列</colname>
			<matchValType><![CDATA[
			{
				findMatch:function()
				{
					// this.tbl("uiname") 获取指定的表
					// this.ds("uiname") 获取指定的数据集
					return this.table.dataSet.getValue("xx") == "yy";
				}
			}
			]]></matchValType>
		</UIVals>
	</UIOpts>
	<!--8.或（A='1' 或 B='2'）(同组与,异组或)-->
	<UIOpts>
		<UIVals grp="0">
			<colname>A</colname>
			<matchValue>1</matchValue>
		</UIVals>
		<UIVals grp="1">
			<colname>B</colname>
			<matchValue>2</matchValue>
		</UIVals>
	</UIOpts>
</UIOptCtrl>
```

#### UIOptCtrlListener

<table>
	<tr>
		<th>情景</th>
		<th>说明</th>
		<th>配置</th>
		<th>补充</th>
	</tr>
	<tr>
		<td>界面属性配置</td>
		<td>
			根据界面属性配置进行界面控制<br/>
			即便配置了其他参数，此功能依然起作用。
		</td>
		<td colspan='2'>~.UIOptCtrlListener.new</td>
	</tr>
	<tr>
		<td>单据标准控制</td>
		<td>
		1、主表：<br/>
		  （1）有C权限：可以新增；<br/>
		  （2）有C权限 且 状态<=20：可以删除；<br/>
		  （3）有C/U权限 且 状态<=20：可以修改；<br/>
		2、其他：<br/>
		  （1）detailReadOnly=true：<br/>
		  	　　有C/U权限 且 状态<=20：非只读；<br/>
		  （2）detailReadOnly=false：<br/>
		  	　　有C/U权限 且 状态<=20：增删改；<br/>
		</td>
		<td>~.UIOptCtrlListener.new?sheetCode=&amp;stdUICtrl=true</td>
		<td rowspan='2'>
			可以通过optNames指定需要控制的界面属性<br/>
			optNames=main:addable+editable<br/>
			optNames=main:addable,detail=editable
		</td>
	</tr>
	<tr>
		<td>单据权限控制</td>
		<td>
			有C/U权限：增删改；<br/>
		</td>
		<td>~.UIOptCtrlListener.new?sheetCode=&amp;onlyLimitCtrl=true</td>
	</tr>
</table>



#### 右键配置导出

1. 支持右键数据导出，将当前配置界面的有效数据（启用）导出成一个文件（XML/南北标准配置文件），实施人员在数据库中配置标准数据，通过本功能将这些标准配置数据导出成文件用来打包发布。

1. 配置存放目录：
    界面属性控制：snsoft/archive/init/optctrl/xxx.zip

#### 右键初始化

配置表数据为空时才可以初始化数据，数据来源是右键导出的配置。

### 设计

#### 常用宏
1. 孙表取值列名宏

|宏|名称|说明|
|---|---|---|
|$USERGWCODE|岗位宏|匹配方式为岗位时生效|
|$USERGBCODE|部门宏|匹配方式为组织时生效|
|$USERCODE|用户宏|匹配方式为用户时生效|

#### 配置文件位置

<pre>UI工程下，cfg/ui/optctrl/**/*.xml</pre>

### 场景

界面属性控制参考界面：[界面属性控制](uiinvoke/00/zh_CN/theme0/SN-HELP.Ctrl.UI.html)
单元属性控制参考界面：[单元属性控制](uiinvoke/00/zh_CN/theme0/SN-HELP.Ctrl.Cell.html)
主子属性控制参考界面：[主子属性控制](uiinvoke/00/zh_CN/theme0/SN-HELP.Ctrl.Union.html)
按钮属性控制参考界面：[按钮属性控制](uiinvoke/00/zh_CN/theme0/SN-HELP.Ctrl.Button.html)
按钮属性控制参考界面：[区域显隐控制](uiinvoke/00/zh_CN/theme0/SN-HELP.Ctrl.Border.html)
按钮属性控制参考界面：[标签显隐控制](uiinvoke/00/zh_CN/theme0/SN-HELP.Ctrl.TabPane.html)
按钮属性控制参考界面：[操作按钮控制](uiinvoke/00/zh_CN/theme0/SN-HELP.Ctrl.Operate.html)

