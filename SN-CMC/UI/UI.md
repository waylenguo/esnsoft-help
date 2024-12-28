## 界面定义

|日期|作者|备注|
|------|------|------|
|2017-06-05|陈泰林|初稿|
|2018-10-09|吕希兵|界面定义以及国际化部分文档更新|
|2019-11-27|郭鑫|重构|

### 指南

#### 概念说明

界面定义可以通过两种方式配置：XML配置、数据库配置，可以通过系统选项UI.XML来定义是否使用数据库配置。
UI.XML=true(建议配置)，表示只加载XML配置；
UI.XML=false(默认)，表示XML配置、数据库配置都加载，其中数据库配置优先级更高。
建议生产环境、开发环境全部使用XML配置方式。
生产环境通过配置系统进行管理，减少配置的误操作，方便配置的版本管理，特别是回滚的操作。
数据库配置不建议使用

#### 使用说明

##### 文件界面定义说明

```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<m:B uiprops.domStyle="{marginTop:&quot;40px&quot;}" fullPage="true" options0="1" title="查询标题"
xmlns="http://www.snsoft.com.cn/schema/UI"
     xmlns:m="http://www.snsoft.com.cn/schema/UI"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://www.snsoft.com.cn/schema/UI http://www.snsoft.com.cn/schema/UI.xsd">
	/**布局查询对话框**/
	<m:DialogPane name="param" region="north" hidden="true" layoutm="grid" title="查询参数">
		/**布局过滤条件**/
		<c name="riskcode" title="单据编号" sqltype="12" noblank="true" />
	</m:DialogPane>
	/**布局工具条**/
	<m:Toolbar name="fixtoolbar" title="悬浮工具条" uiprops.cellClassName="head-toolbar-panel">
		/**布局提交按钮并修改样式**/
		<ToolbarBtn name="risk_btn_wfsubmit" title="提交" noClientComponent="true" uiprops.cellClassName="ui-head-btn"
			xprops.iconClassName="icons-hbtn-submit" />
	</m:Toolbar>
	/**左右布局**/
	<m:H region="center" uiprops.leftSize="500">
		<m:GridTable mainui="param" name="risk" sqlexpr="rms_risk" rdonly="true" noCollapseQPaneAfterRefresh="true"
			showToolbar="false" /**是否显示工具条，因为案例单独布局了进度条所以这里设置为false**/
			uiprops.domStyle="{overflow:&quot;auto&quot;}"
			xprops.LoadDataService="IRMP-RMS.RMSRiskUIService" /**增加查询组件**/
			xprops.SaveDataService="IRMP-RMS.RMSRiskUIService" /**增加存盘组件**/>
			<uilisteners>
				<![CDATA[
					/**增加UI监听**/
					snsoft.bas.sheet.VOListener.new;//默认值
					snsoft.bas.approval.ui.UITaskPerformListener.new;//流程
					snsoft.bas.sheet.busi.SheetListener.new?sheetCode=IRMP-RMS.ProductRisk;//单据
				]]>
			</uilisteners>
			<jslistener>
			<![CDATA[
				/**增加客户端监听**/
				#new snsoft.wf.TaskPerformListener({addSubmitBtn:true,confirmSubmitText:"是否确认提交到“${status}”状态?"})
				#new snsoft.sheet.busi.BusiListener({useStatusControl:true})
				oncmd_printPdf:function(table){}//可以直接写JS代码
			]]>
			</jslistener>
			<css>
			<![CDATA[
	#UIA_passengers{position: relative!important}
	#UIA_rece_payments{position: relative!important}
	#UIA_pay_payments{position: relative!important}
	#UIA_segments{position: relative!important}
			]]>
			</css>
			<attrs>
				/**界面绑定按钮**/
				<attr type="203" name="wfsubmit"/>
			</attrs>
			/**界面控制	推荐使用界面属性控制功能**/
			<!-- 此方式不建议使用。
			<ctrlcol idx="1" matchmode=">=" name="status" sqltype="12"/>
			<ctrlval disableAppend="true" disableDelete="true" disableed="true" idx="1" rdonly="true" val.status="69"/>
			<ctrlval ctrlFor="btn:printPdf" disabled="true" hidden="true" idx="2" val.status="69"/>-->
			/**表列属性**/
			<c name="riskicode" sqltype="12" title="主键" hidden="true" />
			<c name="riskcode" sqltype="12" title="单据编号" />
			<c name="sheetcode" sqltype="12" title="单据号" hidden="true" />
			<c name="vprepare" sqltype="12" title="评估人" width="80" codedata="#users" showname="true" />
			<c name="status" sqltype="12" title="状态" width="80" codedata="#90.status;sheetcode=IRMP-RMS.ProductRisk" showname="true" />
			<c name="predate" sqltype="91" hidden="true" descOrd="true" modifydateColumn="true" />
		</m:GridTable>
		<m:GridTable mainui="risk" name="detail" sqlexpr="rms_riskg" xprops.LoadDataService="IRMP-RMS.RMSRiskUIService#queryDetail">
			<c name="riskicode" sqltype="12" hidden="true" xprops.cpmastercol=":riskicode" />
			<c name="riskgicode" sqltype="12" hidden="true" />
			<c name="ciq_code" sqltype="12" title="CIQ编码" width="120" asceOrd="true" />
		</m:GridTable>
	</m:H>
</m:B>
```

##### 相关规范
* 窗口号规范
```
系统ID+"."+模块+"."+功能名称，
例如：界面定义IRMP-RMS.Exp.RiskFixList生成XML配置文件对应的是cfg/ui/res/IRMP-RMS/Exp文件夹下RiskFixList.xml文件
```
* 界面定义规范
```
界面单元定义字段使用小写
界面显示尽量不要出现滚动条
根据预计数据量的大小确认是否需要分页，数据量小于5K可不用使用分页
```
* 样式调整
```
uiprops.domStyle 用于定义附加的样式
与顶部的距离可配置：uiprops.domStyle="{marginTop:40px}"
自动充满：   uiprops.domStyle="{overflow:auto}"
```

##### 界面加载
```
界面加载优先加载数据库中配置
界面其次加载XML配置（线上：加载zk缓存XML文件；开发本地：加载本地XML文件）
注意：如果系统选项配置（通常在System.properties文件中配置）
UI.NoCache=true表示：界面定义、菜单定义、码表定义，不使用缓存，默认false,一般用于开发环境
UI.XML=true(建议配置)表示只加载XML配置；UI.XML=false(默认),表示XML配置、数据库配置，其中数据库配置优先级更高
```

界面定义的加载 参见 [配置管理](help.html?helpFile=help%2FSN-CMC%2FConfigkit%2FConfigkit.md)

##### 其他说明

* 查找属性

可以通过[界面定义](uiinvoke/00/zh_CN/theme0/90.UI.html)或者【[UI.xsd](do/BrowseSchema?schema=UI.xsd)】查找找对应属性


* 图标样式

[按钮图标库](theme0/iframefonts/icons.html)
[系统图标](mainpage/00/css/system-icons.html)
[菜单图标【小图标】](mainpage/00/css/left-menu-icons.html)
[菜单图标【大图标】](mainpage/01/css/left-menu-icons.html)

* 布局页签

```
(a)、窗口组织(数据库定义)：
常见的布局标签 B、T、H、V、U、UA、C、P，在类snsoft.ui.UIComponentLoader中解析；
以 <标签 name="uiname"></标签> 形式出现
数据库界面定义搭建好后，点按钮"导出XML"可转化为XML文件；
(b)、XML定义(文件定义)：
常见的布局标签 B、T、H、V、C、P、c，在类snsoft.ui.XMLUIComponentLoader中解析；
XML文件定义中界面标签以 <m:标签名 属性名="属性值"></m:标签> 这样形式定义
m:就代表当前的是界面，界面标签有B、T、H、V、C、P；
c是列标签，不需要定义m前缀；

<b>1： B标签</b>
B标签可作为布局根元素，即为是用Border布局；数据库定义勾选满页选项、文件中 设置属性 fullPage="true"， 那么这个B布局的界面就是全屏模式
B标签属性 fullPage="true" 全屏模式 ；title="标题" 页面的标题
其子元素需要指定 [在父组件中的位置]属性 region，region可设置的值有 center、north、south、west、east、null；
设置为null时就是不布局出来，不设置默认就是center；
一个B布局中只能有一个center；可以有多个north、south等；
设置为center时组件就可根据屏幕大小自适应 (在全屏模式时起作用)；
B标签也可嵌套使用，在局部内使用B布局；

<b>2： T标签</b>
T标签指的是tab页签，对应的类型是 TabPanel；

<b>3： H标签</b>
H标签指的是左右布局标签，对应的类型是SplitPane；
指定左边部分宽度 设置属性 uiprops.leftSize
指定右边部分宽度 设置属性 uiprops.rightSize
一般指定左边部分即可

<b>4：V标签</b>
V标签指的是上下布局标签，对应的类型是SplitPane；
界面默认就是从上往下布局的，使用V标签有可调整大小的分隔线；
指定上半部分宽度 设置属性 uiprops.leftSize
指定下半部分宽度 设置属性 uiprops.rightSize
一般指定上半部分即可

<b>5：U、UA标签</b>
U、UA标签都是在界面定义中使用到的，可代表的是界面；
一般在界面定义中窗口组织中用到，在Xml中就直接指定类型，比如直接定义DialogPane、GridTable、RecordTable等
U和UA的区别，如果只有一个界面的话，可以直接用<UA />，就代表使用了Attach模式
新版本默认就是Attach模式了，所以理论上U和UA没啥区别

<b>6：C标签</b>
C标签指的是容器，对应的类型是Container；
一般是多个界面作为一组，可使用C标签圈起来，作为一组使用

<b>7：P标签/b>
P标签指的是面板，对应的类型是Panel；
P标签就是一个空Panel，可以往里边添加想要的内容

<b>8：c标签/b>
c标签指的是column列；
```

* 视图-XML视图

如果生成了XML配置文件可以查看XML配置内容

### 手册

#### 文件界面定义

1. 界面定义配置文件路径：resources/cfg/ui/res/【系统号】/*【任意文件夹】/*.xml
1. 界面定义配置请参见对应的Schema文件【[UI.xsd](do/BrowseSchema?schema=UI.xsd)】；

#### 数据库界面定义

定义界面位置：【管控中心】-【开发配置】-【界面配置】-[界面定义](uiinvoke/00/zh_CN/theme0/90.UI.html)

##### 窗口属性
```
窗口号：界面定义唯一编号，格式为系统号+"."+模块+"."+功能名称，如：IRMP-RMS.Clt.RiskListTest）
窗口名称：界面定义名称
窗口组织：界面的布局方式（可参照其他工具-窗口组织说明）
备注：备注说明
被嵌套：这个界面在对话框之类的嵌套界面中打开
满页：界面满页，一般与B标签一起用，不用设置宽度和高度，一般都要设置，不设置宽高的情况下，也不设置满页，则页面空白
```

**修改日期**列上存在背景色含义为：
```
灰色：XML配置文件不存在或修改日期相同
绿色：数据库配置为最新配置，如果编辑完成需尽早"导出XML"
红色：XML配置为最新配置，此时需要先"从XML配置导入"再进行编辑操作
```

###### 导出XML
<b>导出XML：根据关联配置导出到指定的路径</b>
注意：根据界面定义号匹配，例如IRMP-RMS.Clt.RiskListTest，会匹配到IRMP-RMS中UI模块对应的路径cfg/ui/res/IRMP-RMS/Clt/RiskListTest.xml）

###### 导入XML
<b>1、导入XML-从XML文件导入：选择指定的XML文件，设置界面定义号，导入到数据库</b>


<b>2、导入XML-从XML配置导入：根据关联配置将指定路径下的文件导入到数据库</b>

特殊情况：
```
如果选择：http://127.0.0.1:2020/irmp/uiinvoke/00/zh_CN/theme0/90.UI.html?InitValue.muiid=IRMP-RMS.Clt.RiskListTest&AutoRefresh=1方式打开界面
存在XML文件，并且数据库中不存在界面定义
可通过点击【从XML配置导入】，系统会根据路径匹配到界面号（InitValue.muiid=IRMP-RMS.Clt.RiskListTest），导入数据库
```
**注意：**根据界面定义号匹配，例如IRMP-RMS.Clt.RiskListTest，会匹配到IRMP-RMS中UI模块对应的路径cfg/ui/res/IRMP-RMS/Clt/RiskListTest.xml）

###### 拷贝界面
<b>1、拷贝界面-拷贝界面：根据设置拷贝指定界面号，在目标界面上操作</b>

设置列介绍：
```
选择账套：选择指定账套
录入源窗口号：录入指定账套的界面定义号
录入源界面号：录入指定账套的界面定义号下面的界面号
录入目标窗口号：录入目标界面定义号（也就是当前界面号）
录入目标界面号：录入目标界面定义号下面的新的界面号
```
<b>2、拷贝界面-拷贝窗口：根据设置拷贝指定界面定义，在源窗口上操作</b>

设置列介绍：
```
选择账套：选择指定账套
录入源窗口号：录入指定账套的界面定义号（也就是当前界面号）
录入新窗口号：录入新的界面定义号
```
###### 视图
<b>1、视图-XML视图：如果生成XML文件，XML文件的内容</b>
**注意：**导出XML文件之前，可以先看到XML文件的内容

<b>2、视图-界面结构树：根据已经产生的XML文件，加载看到界面定义（XML文件）的结构树</b>
**注意：**导出XML文件之后，可以看到XML文件的结构树

<b>3、视图-测试打开：界面定义开发完后，可以测试打开界面，看到界面的效果</b>


###### 清除缓存
<b>清除缓存：清除界面定义的服务端缓存</b>

**注意：**浏览器缓存需要自己手动清理（快捷键：Ctrl+Shift+Delete）

##### 界面属性

```
禁用：定义的界面是否禁止使用
界面号：定义的界面号
名称：定义的界面名称
标题：定义的界面标题
```

###### 数据SQL
* 表名
数据表名

* 读写分离
通过 "(读)数据源ID"、"(写)数据源ID" 分别配置读写数据源，
* 分页行数
是否分页及分页的行数
* (过滤条件)类别
条件类别
* 条件
过滤条件，过滤条件和过滤界面的匹配关系，一般都是filter，除非过滤条件需要分组对应界面或程序

* 读写组件
```
界面定义绑定读写组件，虽然分为"数据查询组件"、"数据存盘组件"两个定义，但是建议对应同一服务；
包括读写组件、接入系统、消息中间件、业务流程引擎等的服务配置，建议以子系统下某功能为单位在同一个服务类上实现；
定义格式：BeanID#方法，查询组件默认query(QueryParams queryParams)方法，存盘组件默认save(VO[] vos)方法
```

###### 服务端监听

* 运行(V3.1)
```
在界面类型是ProgressPane时调用：
调用服务：格式为 sv:BeanID.runProgress， 例如  sv:SN-HELP.ProgressPanelService.runProgress
调用静态方法：格式为 st:类的全路径.runProgress， 例如 st:snsoft.help.progresspanel.service.impl.ProgressPanelServiceImpl.runProgress
调用流程：格式为 fl:流程ID
```

* (服务端)UI监听
```
UI监听为界面上服务端监听事件，格式为 类的全路径.new 多个使用;分割
snsoft.cmc.uidef.UITableDefListener.new;
snsoft.cmc.uidef.uikit.ui.UIKitTableDefUIListener.new
```

* (服务端)Tac脚本
该功能适用于二次开发，使用Tac语法编写脚本实现UI监听相关功能。
最终底层会加载UITacScriptListener的实现，动态调用Tac中的方法。
推荐使用！

例如UI监听中的prepareLoadData方法：
```
proc prepareLoadData(event)
	     wcode = snsoft.util.AppBasUtil.getUserWcode(e.envParams)
         e.parameter.put("filter.[filterexpr]_taskfilter","(s.devdesigner='"+wcode+"' or s.developer='"+wcode+"')")
end proc

proc onDataLoaded(event,rs)
       ...//方法实现
       return rs
end proc
```

* (服务端)Java脚本
该功能适用于二次开发，使用Java语法编写脚本实现UI监听相关功能。
最终底层会使用动态加载的方式生成一个UI监听的实现。
注意：由于没有支持import关键字，所有的类必须声明为带包名的全路径。
不推荐使用！
例如UI监听中的prepareLoadData方法：
```
public void prepareLoadData(snsoft.ui.UIEvent e){
	String wcode = snsoft.util.AppBasUtil.getUserWcode(e.envParams);
	e.parameter.put("filter.[filterexpr]_taskfilter","(s.devdesigner='"+wcode+"' or s.developer='"+wcode+"')");
}
```

* (服务端)JS脚本
该功能适用于二次开发，使用JS语法编写脚本实现UI监听相关功能。
最终底层会加载UIJSScriptListener的实现，动态调用JS脚本中的方法。
不推荐使用！

###### 客户端监听
* (客户端)JS监听

在界面定义上定义客户端JS监听，格式为  #new 类路径()  或    #new 类路径({})
如：  #new snsoft.cmc.uidef.UITableDefListener()

* (客户端)JS脚本
在界面定义上定义JS脚本，功能基本同上。
```
oncmd_clientInfo:function()
		{
			var title = Xjs.ResBundle.getString("00","SNSOFT");
			var info = Xjs.ResBundle.getString("00","CLIENTNEEDREDL");
			Xjs.ui.UIUtil.showInfoDialog(title,info);
		}
```

###### 属性查询
<b>属性查询/编辑/清除：显示所有已经设置值的属性，可以直接编辑属性值，编辑确认；也可以勾选要删除属性列，删除确认</b>

###### 界面初始化

<b>新建界面定义完成后，可进行标准初始化操作</b>
```
D-查询-对话框-初始化
G-查询-列表页-初始化
D-单据-对话框-初始化
G-单据-列表页-初始化
D-定义-对话框-初始化
G-定义-列表页-初始化
TB-普通-工具条-初始化
```
###### 界面设置

<b>界面分类编辑设置</b>
```
监听相关设置
SQL相关设置
读写属性设置
布局相关设置
其他常用属性
```
###### 其他工具

```
其他界面属性
其他属性
客户端数据集属性		查看数据集属性
分页设置				设置分页
立即刷新				设置立即刷新
表结构查询			查询当前表的表结构，Grid和Record可用
窗口组织说明			窗口组织写法说明

```

###### 监听组件
根据在cmc-ui\src\main\resources\snsoft\ui\uimodel下配置的监听能自动填写监听
需要额外设置属性的会弹框提示，不需要设置属性，不会弹框
```
界面属性控制--根据配置能对界面属性在现有控制的基础上施加额外的控制，只读等
单据权限--配置单据的权限
文件转库--配置syncToDB按钮，会将从文件中读取到的数据复制到数据库中
VO功能
业务流程
```

##### 界面控制

**注意:此功能不推荐使用。**
推荐使用界面属性控制：[界面属性控制](uiinvoke/00/zh_CN/theme0/90.UIOptCtrlDef.html)

###### 界面控制字段

<b>
可以定义多个控制字段，用于控制表或者字段的隐藏、只读等相关属性。
多个控制字段间为"并且"的关系，或的关系需转换为"并且"的关系定义 如： A || B  = !A && !B
</b>
```
序号：序号
字段名：定义的字段名称（界面定义中必须存在，跨界面号可用：界面号+"."+字段名）
字段标题：用于控制属性值显示标题
匹配方式：匹配的方式（0:等于;1:Like;2:大于;3:大于等于;4:小于;5:小于等于;6:不等于;8:位与;9:位与(相对8反向);10:部门）;
类型：-7:布尔;2:数值;4:整数;12:文本;70:文件;91:日期;
字段值匹配JS类：TableOptsCtrlListener.MatchValue.match(Object value)，特殊匹配条件下可继承重实现规则;
	格式类似于：Xjs.table.sample.LimitMatchValue,写法亦可参考此类
```
###### 控制属性值
```
序号：序号
定义的界面控制字段：值
控制属性：
```
###### 控制按钮属性值
```
序号：序号
单元号：btn: + 按钮命令号
定义的界面控制字段：值
控制属性：隐藏、禁用
```
##### 单元定义
```
单元号：单元号（唯一）
布局序号：单元排序的序号
字段名：字段名称
类型：字段类型
显示名称：字段显示名称
宽度：字段的宽度
SQL表达式：SQL表达式（字段名与表字段不一致时使用SQL表达式）
说明：说明
```
###### 标准输入
单元定义上可批量修改标准设置
标准设置配置项位置【管控中心】-【系统工具】-【界面配置工具】-[界面工具配置](uiinvoke/00/zh_CN/theme0/90.UIKITDEF.html)
###### 单元设置1
单元定义上可批量修改单元设置
标准设置配置项位置【管控中心】-【系统工具】-【界面配置工具】-[界面工具配置](uiinvoke/00/zh_CN/theme0/90.UIKITDEF.html)
###### 单元设置2
标准设置配置项位置【管控中心】-【系统工具】-【界面配置工具】-[界面工具配置](uiinvoke/00/zh_CN/theme0/90.UIKITDEF.html)
###### 单元工具
```
单元自动生成：可根据界面表名（或者界面其他属性xprops配置UIVO:'snsoft.cmc.codedata.vo.CMCCodeData'），自动有选择的生成单元定义
序号重排：将所有单元字段按序号重新排列
单元分组：将选中的单元进行分组
宽度设置：将选中的单元进行宽度设置
```
###### 其他工具2
```
数据类型设置：单元定义上可批量修改单元定义的类型
计算列：单元定义上可批量修改单元定义的计算列
设置默认值：单元定义上可批量修改单元定义的默认值
关联主表字段：单元定义上可批量修改单元定义的关联主表字段
拷贝主表字段：单元定义上可批量修改单元定义的拷贝主表字段
其他单元属性：单元定义上可批量修改单元定义的其他单元属性
其他属性：单元定义上可批量修改单元定义的其他属性
编码定义查询：单元定义上查询编码定义
```
###### 拷贝
复制单元定义（可多选复制）
###### 插入
插入复制后的单元定义
###### 覆盖
覆盖复制后的单元定义
##### 单元属性
###### 单元查询
<b>属性查询/编辑/清除：显示所有已经设置值的属性，可以直接编辑属性值，编辑确认；也可以勾选要删除属性列，删除确认</b>

###### 单元编辑
单元进行编辑
###### 检查工具
单元进行检查

##### 控制属性值

**注意:此功能不推荐使用。**
推荐使用界面属性控制：[界面属性控制](uiinvoke/00/zh_CN/theme0/90.UIOptCtrlDef.html)
```
序号：序号
定义的界面控制字段：值
控制属性：
```
##### 其他属性

<b>其他属性配置</b>

```
添加按钮：添加按钮命令
添加树形配置：添加树形显示命令
```
##### 界面结构树
<b>视图-界面结构树：根据已经产生的XML文件，加载看到界面定义（XML文件）的结构树</b>
注意：导出XML文件之后，可以看到XML文件的结构树

#### 常用属性

##### 常用界面属性

```
noCollapseQPaneAfterRefresh：刷新后不自动隐藏参数面板
autoRefresh：立即刷新
disableed：禁止编辑
rdonly：只读
backInitValues：显示重置（uiprops.backInitValues="true"）
btnsLayoutMode：重置第2行显示（xprops.btnsLayoutMode="2"）
sqlop：查询条件自定义（xprops.sqlop:'<='）
nmpre：查询条件类别（nmpre="filter"）
dsprops.pageRows：分页行数（dsprops.pageRows="100"）
dsprops：保存模式，一般MongDB的界面会添加（dsprops="saveDataMode:1,saveOnRowNavigate:false" uiprops="resetKeyFilterOnRefresh:1"）
uiprops.domStyle：界面属性样式（uiprops.domStyle="{overflow:'auto'}"）
rowIntervalColor：grid表格默认隔行变色的表格（去掉各行变色：xprops.rowIntervalColor="false"）
PageBody：界面类型相当于B标签;单据内使用
uiprops.cellClassName：TAB页样式,默认是一级tab，二级tab 设置属性：uiprops.cellClassName="ui-tabpane-level2"，三级tab 设置属性 uiprops.cellClassName="ui-tabpane-level3"
```

##### 常用单元属性
```
asceOrd：排序（true：升序，false：降序）
codedata：码表属性
cmprops：码表属性
cmparams：码表查询参数（ cmparams=SQLFILTER:"code in ('3')"))）
cmparams.FILTER：码表数据过滤方法(分号分隔)
aiprops：辅助录入属性（dlgParam:{sheetcode:'@sheet.Bcode',opids:'@R'}）
aidInputerBtn：辅助录入属性
aidInputableIfRdonly：只读时可用辅助录入
droplist：下拉方式
disableed：禁止编辑
rdonly：只读
showname：码表显示名称
showcode：码表显示码
sqlexpr：SQL表达式
xprops.cpmastercol：复制主表字段（xprops.cpmastercol=":riskicode"）
noblank：不为空
noblankOnSubmit：提交时空值检查
initval：初值（当前日期：%CURDATEYEAR-%CURDATEMONTH-%CURDATEDAY，上一年：%PREVDATEYEAR-%CURDATEMONTH-%CURDATEDAY）
mergeSameToLastRow：相同值合并
```

#### 相关配置文件
```
界面属性定义："/snadk-ui/src/main/resources/snsoft/ui/UIAttrs.xml ",用于定义界面属性、单元属性、控制属性
界面扩展属性："snadk-ui/src/main/resources/snsoft/ui/UIXProps.xml",用于定义界面属性中的其他界面属性 、其他属性、数据集属性的具体属性值
单元扩展属性："snadk-ui/src/main/resources/snsoft/ui/CellUIXProps.xml",用于单元界面属性中的其他界面属性、其他属性 、按钮属性的具体属性值
XML属性与值的对应关系："snadk-ui/src/main/resources/snsoft/ui/UIAttrsNameMap.xml",用于定义按位赋值的属性对应的XML属性
界面工具配置："cmc-ui/src/main/resources/snsoft/cmc/uidef/uikit/data/UIKITDEF.xml",用于界面工具相关的配置定义
界面与XML对应配置：UI工程对应"src/main/resource"下面"snsoft/ui/sourcepath/UIResourceConfig*.json"，用于配置界面与XML转换的路径
```

#### 数据库XML配置转换

开发配置，用来配置xml和数据库配置相互转换的功能；

##### 配置转换
```
UI配置服务：snsoft.cmc.uidef.resource.service.UIResourceService
UI配置服务入口：snsoft.cmc.uidef.resource.service.UIConfigResourceService
配置转换步骤：
	某系统ID=IRMP
	1、System.properties文件配置XML资源位置
	业务模块位置：UIDEF.SourcePath.IRMP=D:/snsoft90/IRMP
	UIDEF.SourcePath：数据库优先，底层默认路径：D:/snsoft90/snsoft_adk
	UIDEF.SourcePath.product：数据库优先，底层产品默认路径：D:/snsoft90/snadk_product
	UIDEF.SourcePath.help：数据库优先，帮助中心默认路径：D:/snsoft90/snadk_help
	2、项目UI工程配置src/main/resource下面snsoft/ui/sourcepath/UIResourceConfigIRMP.json文件
	[
		{
			"path": "{UIDEF.SourcePath.IRMP}/00-irmp-comm/comm-ui/",
			"matches": "IRMP-COMM%",
			"name":"IRMP-COMM_公共",
			"modular":"IRMP-COMM",
			"flag":2,
			"desc":"flag:1,禁用；0默认，可用;2:不可批量操作;modular:界面定义导入时，指定资源文件夹（包括子路径）"
		},
		{
			"path": "{UIDEF.SourcePath.IRMP}/21-irmp-rms/rms-ui/",
			"matches": "IRMP-RMS%",
			"name":"IRMP-RMS_风险管理系统",
			"modular":"IRMP-RMS",
			"flag":2,
			"desc":"flag:1,禁用；0默认，可用;2:不可批量操作;modular:界面定义导入时，指定资源文件夹（包括子路径）"
		}
	]

```

##### 配置项说明
```
path：界面定义导出时XML的存放路径或者界面定义导入时XML界面定义资源路径；（1、可配置宏地址2、可配置绝对地址）
		{UIDEF.SourcePath.irmp}/00-irmp-comm/comm-ui/表示导入或者导出XML界面定义存放的位置（指定到模块，会自动添加cfg/ui/res/）
matches：界面定义导出时匹配数据库中的界面定义或者界面定义导入时在XML资源路径下匹配合适的文件；
		IRMP-COMM%表示匹配界面定义IRMP-COMM开始的(例如：界面定义号IRMP-COMM.test，导出时生成XML对应的是IRMP-COMM文件夹下test.xml文件)
modular：界面定义导入时，指定导入的资源文件夹（包括子路径）；（注意：界面定义导出时不使用此项）
		IRMP-COMM表示对应的cfg/ui/res/IRMP-COMM文件夹下的所有XML文件（界面定义）（包含子路径，实际只在导入数据库时使用）
name：显示的名称(唯一标识)，用于导出和导入时选择模块时使用；
flag：flag:1,禁用；0默认，可用；2:不可批量操作（后续可扩展）；
desc：描述；
```
##### 配置说明
```
系统加载配置时会加载UI工程对应src/main/resource下面所有snsoft/ui/sourcepath/UIResourceConfig*.json文件
配置文件名称要求(UIResourceConfig*.json)："UIResourceConfig"+系统号+".json"
可在 System.properties 配置系统选项
宏地址替换：UIDEF.SourcePath*，存在多个不同的路径默认支持宏，UIDEF.SourcePath、UIDEF.SourcePath.product、UIDEF.SourcePath.help
UIDEF.SourcePath：数据库优先，底层默认路径：D:/snsoft90/snsoft_adk；
UIDEF.SourcePath.product：数据库优先，底层产品默认路径：D:/snsoft90/snadk_product；
UIDEF.SourcePath.help：数据库优先，帮助中心默认路径：D:/snsoft90/snadk_help；
```
<b>界面与XML对应配置位置：管控中心-系统工具-界面配置工具-[界面与XML对应配置](uiinvoke/00/zh_CN/theme0/90.UICONFIG.html)</b>
[UIDEF.SourcePath]\snadk-cmc\cmc-ui\src\main\resources\cfg\ui\res\90\UI.xml

<b>对应宏资源真实路径，如：</b>

D:\snsoft90\snsoft_adk

<b>右键-->界面定义（界面定义中导入导出即可正常使用）</b>

#### 国际化

##### 菜单位置

管控中心-开发配置->界面配置->[界面国际化](uiinvoke/00/zh_CN/theme0/90.LangAllUI.html)

##### 功能说明

功能上是界面定义的拓展，仅导入到数据库中的界面定义可用，可用于在配置某些属性的多语言

支持的多语言框分别有
```
窗口名称
表的标题
【单元定义】中列的显示名称
【其他属性】中按钮的名称
```
由XML转数据库，支持了多语言的转换，在XML中多语言的设置类似
```xml
<m:RecordTable title="销售合同" name="order" sqlexpr="help_erp_order" title_en="order" title_aa="aaa">
	<c name="ordcode" title="合同编码" sqltype="12" width="120" rdonly="true" title_en="ordcode"/>
```

#### 界面使用统计

基于MongoDB日志applog记录的统计信息：
* 在日志表上执行以下MapReduce脚本
```javascript
// 首次
db.applog.mapReduce(
	function(){emit(this.uiinfo.substring(0,this.uiinfo.indexOf("._M")),1)},
	function(key,values){return Array.sum(values)},
	{out:'ui_sum',query:{logger:"snsoft.ui.invoke",uiinfo:{$regex:"(!init)$"}}}
)
// 每天执行：需要替换日期过滤条件
db.applog.mapReduce(
	function(){emit(this.uiinfo.substring(0,this.uiinfo.indexOf("._M")),1)},
	function(key,values){return Array.sum(values)},
	{
		out:{reduce:'ui_sum'},
		query:{
			logger:"snsoft.ui.invoke",
			uiinfo:{$regex:"(!init)$"},
			logtime:{$gt:new Date('2020-02-29T18:21:00')}
		},
		finalize:function(key,values){return values}
	}
)
```
* 该脚本将统计信息记录到ui_sum表中
	* _id：为界面定义ID
	* value：记录使用次数

#### 查询工具

在界面的"(服务端)Tac脚本"中定义查询工具脚本，简单示例可以参考界面：[查询工具例子](uiinvoke/00/zh_CN/theme0/SN-HELP.UI.QueryTool.QueryTool.html)

##### 示例
```
//初始化主查询对象
proc initQueryTool(queryTool)
    queryTool.newMainDataObject()//主查询对象
    queryTool.addLoadedCalFormula("fcydiff", "[fcy]-[pfcy]")//计算公式
    queryTool.addLoadCalcRowFormula("fcytotal", "[fcy<SUM(0:ROW)>]") //计算累加
    queryTool.addLoadedCalRateFormula("rate01", "fcy","pfcy")//同比
    queryTool.addEngrossRateFldName("rate02", "fcy") //占比
    queryTool.addRankings("rank", "fcy", "id")//排名
end proc
//初始化子查询对象
proc addSubQuery_1(subQuery)
    sql = "select year,gcode,max(gname),sum(fcy) from busibalancestorage where busicode='502010' and gcode in (%KeyFilterSQL) group by year,gcode"
	subQuery.sql = sql
	subQuery.filterKeyColumn = "gcode"
	subQuery.keyColumns = "gcode,year"
	subQuery.resultColumns = "gname,pfcy"
	subQuery.prophaseDataMode = 1//补行
end proc
```
##### 主查询对象
主查询对象需要在`initQueryTool`方法中初始化。初始化方法为：
```
proc initQueryTool(queryTool)
    queryTool.newMainDataObject()//主查询对象
end proc
```
主查询对象初始化方法主要用于处理计算公式、计算同比占比和排名等业务场景。
目前实现的计算公式等方法是在子查询补数完成后计算的，使用时应当注意。
##### 计算列
对于简单计算公式：
```
queryTool.addLoadedCalFormula(String resultFldname, String calformula)
resultFldname：计算结果列名
calformula：计算公式，列名用[]括住。
```
对于复杂计算公式：
```
queryTool.addLoadedCalFormula(String resultFldname, String calformula, String relationfldnames, int comparesresult, boolean bUseCalTac)
resultFldname：计算结果列名
calformula：计算Tac表达式，例如：
"fcy<0 ? 0 : fcy+100"
relationfldnames：计算相关的列名
comparesresult：计算结果视为 0 （1:等于零;2:大于零;4:小于零）
bUseCalTac：计算关系是否按照Tac执行，当为true时，relationfldnames必须枚举。且calformula不能有[]，必须符合Tac语法。
```
##### 行计算公式
```
queryTool.addLoadCalcRowFormula(String resultFldname, String rowFormula)
resultFldname：结算结果列名
rowFormula：行计算公式，例如 `[A<ROW-1>]+[B<SUM(ROW-1：ROW)>]` 代表 `resultFldname = 上一行 A列值 + 从上一行到当前行 B列值的累加`
行计算公式支持的写法如下：
        B                 B列当前行值
		B<X>              B列第X行值;
		B<ROW±X>          B列ROW行上/下数第X行值;
		B<SUM>            B列所有行合计值
		B<SUM(X1:X2)>     B列从X1行到X2行的合计
		B<SUM(X1:ROW)>    B列从X1行到ROW行的合计
		B<SUM(X1:ROW±X)>    B列从X1行到ROW±X行的合计
		B<SUM(ROW:X2)>    B列从ROW行R到X2行的合计
		B<SUM(ROW±X:X2)>    B列从ROW±X行到X2行的合计
		B<SUM(ROW±X1:ROW±X2)>    ...
```

##### 计算同比
```
queryTool.addLoadedCalRateFormula(String resultFldname, String fldname, String upfldname)
resultFldname：计算结果列名
fldname：本期列名
upfldname：上期列名

```
##### 计算占比
```
queryTool.addEngrossRateFldName(String rateFldName, String calRateGistFldName)
rateFldName：占比列名
calRateGistFldName：计算占比依据的列名
```

##### 计算排名
```
queryTool.addRankings(String rankingsFldNames, String rankingsByFldNames, String rankingskeyFldNames)
rankingsFldNames：排名列名
rankingsByFldNames：计算排名依据的列名
rankingskeyFldNames：排名分组列名
```

##### 数据过滤
数据过滤在`filterAccept`方法中实现。返回false时，当前行数据从结果集中删除。实现例子如下：
```
proc filterAccept(queryTool, dc, values)
    return toInt(values[0]) > 2000
end proc
```

##### 子查询补数
子查询对象使用`addSubQuery_x`方法初始化，其中x为子查询序号，开发约定序号从1开始自增，且连续不可间断。
初始化方法为：
```
proc addSubQuery_1(subQuery)
//子查询 1
end proc
proc addSubQuery_2(subQuery)
//子查询 2
end proc
...
proc addSubQuery_n(subQuery)
//子查询 n
end proc

```

##### 子查询属性
subQuery子查询对象常用属性：
```
filterKeyColumn：
记录到临时表中的主查询数据列的所有值，在子查询条件中替换 sql 中的%KeyFilterSQL字符
keyColumns：
条件列(分组列),子查询对象补数的依据列,对应主查询对象的列名
resultColumns：
补数列名,对应的主查询对象的列名
db：
子查询使用的db，当不指定时，默认使用主查询的db
sql：
子查询sql
prophaseDataMode：
子查询补数后剩余的数据集记录（0：不保留，1：插入数据集，2：主数据最后追加数据集）
limitSheetCode：
子查询 sql 使用的权限单据号
limitSheetAlias：
子查询过滤权限使用的表别名，默认为'm'
queryLevel：
子查询执行级次，目前支持0和1，补数时先执行级次为0的子查询，后查询更高级次的子查询。默认级次为0
subValues：
补数二维数组，当初始化子查询对象指定了subValues时，直接使用该二维数据补数，不再执行sql

```
##### 常用方法
subQuery子查询对象常用方法：
```
subQuery.setGroupRule(String groupRule)
将子查询结果集依据分组规则进行合并。该方法是为了支持字符串list而实现。
如果sql分组函数可以实现的需求，不建议使用该方法，尽量使用sql分组解决。
groupRule：子查询分组规则。
当指定分组规则时，分组规则个数必须同子查询结果列数保持一致。
支持的分组为：group,sum,min,max,count,list,distlist,last
    group:分组项
    sum:求和
    min:最小值
    max:最大值
    count:计数
    list:以','分隔拼接字符串
    distlist:按照去重的方式以','分隔拼接字符串
    last:补数取最后一行的值

```

### 实践

#### 数据加载

##### 关系数据库
关系数据库主子界面一般对应不同表结构，界面遵循所见即加载的原则，子表数据可能会延迟加载
```
主界面：在子界面上定义主界面，确定主子关系
关联主表字段：设置子界面的单元属性，确定关联关系
```

##### MongoDB(只读)
MongoDB数据为主子数据一次加载，此时需如下配置
主表配置
```
dsprops="saveDataMode:1,saveOnRowNavigate:false"
单元<c hidden="true" name="messageparams" />
```
子表配置：
```
sqlexpr="<memory>"
dsprops.dataInMasterFld="messageparams"
```

##### MongoDB(读写)

MongDB数据为主子数据一次加载，此时需如下配置
主表配置
```
xprops.checkFieldCells="true" dsprops="forceSaveColumns:['*']"
辅表单元<c hidden="true" name="atccode" sqlexpr="atccode.atccode" />
子表单元<c hidden="true" name="segments" />

嵌入结构：
<GroupPane layoutwidth="3" name="segments_inner" notInDataSet="true" title="航程信息"
    xprops="BInnerTblName:segments,hideGrpTitle:false">
	<ToolbarBtn name="segments_btn_addPassenger"
		noClientComponent="true" tags="tbtn" title="添加乘机人"
		xprops="iconClassName:icons-btn-insert" />
	<c name="segments" notInDataSet="true" />
</GroupPane>
```
子表配置：

```
<m:GridTable dsprops.dataInMasterFld="passengers"
					mutiple="true" height="200" xprops.checkFieldCells="true"
					uiprops.disableGoFirstRow="2"
					dsprops="forceSaveColumns:['*'],saveOnRowNavigate:false" mainui="pro_order"
					name="passengers" sqlexpr="&lt;memory&gt;" title="乘机人信息" disableSort="true"
					uiprops="maxVisibleRows:9,dragSelecable:false">
</m:GridTable>

辅表单元<c hidden="true" name="profitsource" sqltype="12" title="利润来源" sqlexpr="atccode.profitsource" />
```

MDB UI监听
```
/**
 *
 * <p>标题：设置无主键情况指定含有主键</p>
 * <p>功能：设置无主键情况指定含有主键</p>
 */
public class CCMDBPrimarykeyUIListener extends DefaultUIListener
{
	public CCMDBPrimarykeyUIListener(Map<String,Object> parameter)
	{
		super(parameter);
	}

	@Override
	public ReadDataSet onDataLoaded(UIEvent event, ReadDataSet rs)
	{
		rs.putClientProperty("_$UIRS.Opts", 2);
		return super.onDataLoaded(event, rs);
	}
}
```
MDB CLI监听

```
/*#
 lib=snsoft/hc/cc/cc.js
 #*/
package snsoft.hc.cc.bas.busi.cli;

import js.HTMLElement;
import js.JSObject;
import snsoft.sheet.busi.BusiListener;
import xjs.dx.DataSet;
import xjs.dx.DataSetEvent;
/**
 *
 * <p>标题：MDB</p>
 * <p>功能：</p>
 */
public class CCMDBBusiListener extends BusiListener
{
	//保存之后是否刷新
	protected boolean saveAfterRefresh;

	public CCMDBBusiListener(JSObject<Object> params)
	{
		super(params);
	}

	@Override
	public void dataSetSaved(DataSet dataSet, DataSetEvent e)
	{
		super.dataSetSaved(dataSet, e);
		String id = (String) dataSet.getValue("id");
		String idRefresh = (String) dataSet.getRefreshParameter("id");
		if ($bool(id) && (!$bool(idRefresh) || !idRefresh.equals(id)))
		{
			mainTable.setQueryParamValue("id", id);
		}
		refreshOriginalLand();
	}

	/**
	 * 刷新后重新回到原来的地方
	 */
	private void refreshOriginalLand()
	{
		if (saveAfterRefresh)
		{
			HTMLElement activeElement = window.document.activeElement;
			HTMLElement ss = mainTable.getRootComponent().getDOM();
			int top = ss.scrollTop;
			mainTable.refreshTable();
			activeElement.focus();
			ss.scrollTop = top;
		}
	}
}

```


#### GridTable配置
##### 满页模式
全屏模式下GridTable自适应高度
```
满页模式下GridTable高度可以根据屏幕自适应，会随着窗口的大小改变而改变
满页模式：B标签布局，设置fullPage="true"属性
GridTable设置布局位置 region="center"
grid表格不需要隔行变色;设置属性 xprops.rowIntervalColor="false"
```
##### 非满页模式
```
如果不是满页模式，GridTable就要配置高度方式来显示；配置高度有两种方式
1：直接设置高度 height="300"
2：按行数来设置高度，必须需要设置属性
最小显示行数 uiprops.minVisibleRows="5"
根据table调整的高度 uiprops.fitInDOMHeight="1"
若有最大行限制 再加上 uiprops.maxVisibleRows="10"
a、他常用可配置的属性
禁用纵向滚动条 uiprops.disableYScroll="true"
可参见文件 UIXProps.xml 中GridTable属性
还有一种模式scrollMode=1模式(一般不推荐使用；无滚动条，使用里边内容来撑开外边)
```

##### 分页设置
1. 列表页数据量较大时需设置分页。
1. 子表一般无需设置分页。
```
<m:GridTable mainui="query" name="gwcode" nmpre="filter"
                     noCollapseQPaneAfterRefresh="true" layoutm="grid" region="center"
                     sqlexpr="gwcode" title="岗位" rdonly="true" disableSave="true"
                     dsprops.pageRows="100" xprops="simplePageNav:true"
                     xprops.LoadDataService="SN-CODE.GwcodeUIService#queryGwcodes">
```
分页相关为以下两个属性：
```
dsprops.pageRows="100"  设置为分页显示，每页100行数据
xprops="simplePageNav:true" 分页的界面宽度不够时使用简化版样式
```
#### RGridTable配置
```
界面类型为RGridTable类型，布局设置为grid即设置属性 layoutm="grid"
对齐方式默认是居中，设置为居左 align="left"
可设置 (单元)列数 cellcols="3" 指其单元有几列；若界面有分组则在分组单元上指定；若不指定默认是4列
```
#### RecordTable配置
```
界面类型为RecordTable类型；有分组就不需要设置界面的布局
增加分组单元为 GroupPane 类型；设置布局为grid，layoutm="grid"；
增加单元GroupPane的子单元；若是界面定义则是以布局序号为树形分组
GroupPane类型中可定义的属性
隐藏分组标题 xprops.hideGrpTitle="true"
record中引用其他界面(一般指grid表) xprops.BInnerTblName="uiname" 多个可用逗号隔开
隐藏分组块下边的分隔线 xprops.hideGrpLine="true"
```

#### 交叉表配置

参见[数据交叉](../../SN-HELP/Xjs/Bottomval.md#数据交叉)。

#### 树节点配置
```
配置界面类型为GridTable；
配置GridTable的其他属性：202数据集属性(web界面)，指定属性名称为 isTreeChildOf ，属性值为 new Xjs.dx.IsChildCodeOf({codeColumn:'menuid'})的一个属性；
其中 codeColumn 是数据上作为树节点的列名；
在xml中体现就是 作为GridTable的属性来定义
<attrs>
<attr name="isTreeChildOf" title="树结构" type="202" value="new Xjs.dx.IsChildCodeOf({codeColumn:'menuid'})" />
</attrs>
可指定初始展开级次 dsprops.initToLevel="1" ；若不指定则全部收缩，只显示根节点
数据的树节点列 要配置升序 asceOrd="true"；也可指定其他的列作为展示的树节点列，指定属性 treenode="true"
```
#### BaiduEchart图表

参见[BaiduEchart图表配置](help.html?helpFile=help/SN-CMC/BaiduEchart/BaiduEchart.md)
#### 自定义弹框

参见[自定义弹框](help.html?helpFile=help/SN-CMC/XJS/Dialog/Dialog.md)

#### 码表介绍

参见[码表介绍](help.html?helpFile=help/SN-CMC/Codetbl/Codetbl.md)

#### 字典介绍

参见[字典介绍](help.html?helpFile=help/SN-CMC/Dict/Dictdef.md)

#### Render+存储转换接口

参见[Render+存储转换接口](help.html?helpFile=help/SN-HELP/Xjs/Render/Render.md)

#### 合计值

参见[合计值](help.html?helpFile=help/SN-HELP/Xjs/Bottomval.md)

#### 辅助录入
参见[辅助录入](help.html?helpFile=help/SN-HELP/Xjs/AidInput/AidInput.md)

#### 界面属性控制

参见[界面属性控制](help.html?helpFile=help/SN-CMC/OptCtrl/OptCtrl.md)

#### DataSet值替换

参见[DataSet值替换](help.html?helpFile=help/SN-CMC/XJS/ReplaceValues/ReplaceValues.md)

#### 远程调用

参见[远程调用](help.html?helpFile=help/SN-HELP/Xjs/Rinvoke/Rinvoke.md)

#### 动态显示列组

参见[动态显示列组](help.html?helpFile=help/SN-HELP/Xjs/ColumnVisible/ColumnVisible.md)

#### 定义界面默认值
参考  UIComponentInit.getItemInitValues()
* %EnvParam(XXX) : 根据 UserSession 的值替换
* %OptParam(XXX) : 根据 界面配置的UI监听中 getMacroValue() 方法获取
* %SysOption(XXX) : 根据 系统选项值获取 SysConfig.impl.getSysOptionAsStr()
* 根据 MapUtils.defineDefaultMacro() 中常用值替换

#### 子表使用过滤条件
一般在  JS监听的dataSetRefreshing方法中处理如：
```
@Override
public void dataSetRefreshing(DataSet dataSet, DataSetEvent e)
{
	super.dataSetRefreshing(dataSet, e);
	if (this.mainDataSet != null && this.mainDataSet.getRow() > -1 && $ieq(dataSet, this.dataSet))
	{
		e.refreshParam.$set("serverid", this.mainTable.queryParam.getItemValue("serverid"));
	}
}
```

### 设计

### 界面号过滤器

#### 说明

界面号过滤器【snsoft.servlet.filter.UiidFilter】用以解决项目中需要屏蔽的一些界面过滤。

#### 配置方式

**类名：snsoft.servlet.filter.UiidFilter**

1. SpringBoot：示例
```java
@Bean
public UiidFilter filter_UiidFilter()
{
    return new UiidFilter(MapUtils.toMap("filterPtns", "90.Bcode.*,SN-HELP.Ord.SalorderList"));
}
@Bean
public FilterRegistrationBean<Filter> filter_uiid_register(UiidFilter uiidFilter)
{
    FilterRegistrationBean<Filter> filterRegistrationBean = new FilterRegistrationBean<>();
    filterRegistrationBean.setFilter(uiidFilter);
    filterRegistrationBean.setEnabled(true);
    filterRegistrationBean.addUrlPatterns("/uiinvoke/*");
    filterRegistrationBean.setOrder(999);
    return filterRegistrationBean;
}
```
1. web.xml:示例
```xml
<filter>
    <filter-name>UiidFilter</filter-name>
    <filter-class>snsoft.servlet.filter.UiidFilter</filter-class>
    <init-param>
        <param-name>filterPtns</param-name>
        <param-value>90.Bcode.*,SN-HELP.Ord.SalorderList</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>UiidFilter</filter-name>
    <url-pattern>/uiinvoke/*</url-pattern>
</filter-mapping>
```
1. 系统选项：示例
```properties
Start.WebFilters=uiinvoke/*:snsoft.servlet.filter.UiidFilter?filterPtns=90.Bcode.*,SN-HELP.Ord.SalorderList
```
1. 参数说明
```properties
匹配条件参数配置方式：
    构造参数：filterPtns,neg
    系统选项选项：Start.UiidFilterPtns
批量匹配条件获取优先级：参数>选项
```