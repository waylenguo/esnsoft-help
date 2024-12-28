## 界面个性信息

|日期|作者|备注|
|------|------|------|
|2020-08-28|冀小雷|再次整理[UserTblInfoService]|
|2019-10-09|冀小雷|合并查询参数|
|2019-10-12|吕希兵|合并分析模板|

<table>
	<tr>
		<th></th>
		<th align="center">1:查询参数</th>
		<th align="center">2:打印参数</th>
		<th align="center">3:导出参数</th>
		<th align="center">5:分析模板</th>
		<th align="center">6:界面属性</th>
	</tr>
	<tr>
		<th>说明</th>
		<td>
			1. 适用界面类型：DialogPane/ProgressPane;<br/>
			2. 属性：<br/>
			（1）autoSaveParam="true"：<br/>
			　　自动保存，id=default，打开页面后自动带出；<br/>
			（2）saveParams="true"：<br/>
			　　手动保存，通过按钮选择装载；<br/>
		</td>
		<td></td>
		<td>
			1. 适用界面类型：Table;<br/>
			2. 右键->导出；
		</td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>加载</th>
		<td>
			1. 加载ucode='自己' 且 infoid='指定';<br/>
			（1）首次打开，加载infoid='default';<br/>
			（2）客户端选择加载infoid='选中';<br/>
		</td>
		<td></td>
		<td>
			1. 【装载参数】：下拉列表选择后进行装载；
		</td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>列表</th>
		<td>
			1、客户端【装载参数】功能下拉列表；<br/>
			2、客户端【参数保存】功能下拉列表；
		</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>保存</th>
		<td>
		客户端【参数保存】：<br/>
		1、新增；<br/>
		2、覆盖；<br/>
		3、删除；<br/>
		</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th>其他</th>
		<td>功能页面：00.PANEPARAMSAVE</td>
		<td></td>
		<td>
			功能页面1：00.TBLEXPORT<br/>
			功能页面2：00.PANEPARAMSAVE
		</td>
		<td>
			功能页面1：00.TBLGROUP<br/>
			功能页面2：00.PANEPARAMSAVE
		</td>
		<td></td>
	</tr>
	<tr>
		<th>补充</th>
		<td colspan="5">
			1、除默认查询参数[infoid='default']存储在表[usertblinfo2]中，其他均存储在表[usertblinfo]中；<br/>
			2、<font color='red'><b>多语言</b></font>：<br/>
			（1）数据库通过多语言列进行支持（infoid,infoid1,infoid2,infoid3,infoid4）；<br/>
			（2）文件(查询参数/分析模板)根据规则[ut\_{infotype}\_{muiid#uiname}\_{infoid}]到对应的资源文件中查找；<br/>
			3、数据读写服务UserTblInfoService；
		</td>
	</tr>
</table>

### 查询参数

#### 查询参数指南

**文件支持**
* 文件路径：cfg/ui/qrypm/[muiid].xml；
* 配置文件格式参见对应的Schema文件【[ui-pm.xsd](do/BrowseSchema?schema=ui-pm.xsd)】；

参考界面：[查询参数](uiinvoke/00/zh_CN/theme0/SN-HELP.UI.Bas.UserInfoQuery.html)
配置方式：`<m:DialogPane ... saveParams="true" >`

**保存参数**

* 参数名：为一组查询参数起一个名字，在装载参数时通过该名称选择；也可以通过下拉选择之前保存过的参数进行替换或删除
* 可使用部门：为一组查询参数定义可使用的部门

**装载参数**

将已经定义好的查询参数装载进来
* 参数名：可选择之前保存好的参数名

**查询参数类图**

@startuml
Title 查询参数类图

UICompParamService <|.. UICompParamService

class UICompParamService{
+ public Object load();
+ public Object save();
}


@enduml

**查询参数时序图**

@startuml
Title 查询参数时序图

actor 客户端

participant UICompParamService   as "UICompParamService"

客户端 -> UICompParamService : [按钮]\n加载/保存数据
activate UICompParamService

	UICompParamService -> UICompParamService : [load/save]\nload/save数据
	
UICompParamService -> 客户端 :内部方法
deactivate UICompParamService
@enduml

#### 查询参数手册

#### 查询参数设计

### 分组查询

#### 分组查询指南


**文件支持**
* 文件路径：cfg/ui/grppm/[muiid].xml；
* 配置文件格式参见对应的Schema文件【[ui-pm.xsd](do/BrowseSchema?schema=ui-pm.xsd)】；

参考界面：[分组参数](uiinvoke/00/zh_CN/theme0/SN-HELP.UI.BigGrid.html)
配置方式：
* ToolbarBtn：`<ToolbarBtn name="uiname_btn_groupquy" title="分析模板" noClientComponent="true" xprops.iconClassName="icons-btn-analyse" />`
* grid表配置：`<m:GridTable ······ groupData="true" >`
* 初始分组参数：按需配置`<m:GridTable ······ uiprops.initGrpName="testGroup" >`(testGroup为已经保存的参数)
按钮：
* 若存在关联至菜单的参数，则此处会显示参数名
* 分组查询：进入分组界面
* 明细数据：展现明细数据，即未分组的数据展现

分组查询弹出界面：
* 第一层：支持拖拽列名对列展示顺序排序
* 求和方式：前提为树形显示，配置该列所显示的求和方式
* 隐藏：列配置属性隐藏该列
* 交叉：列配置属性该列为交叉列，其后面的列均为交叉数据
* 升序：列配置属性按照该列升序排列数据
* 降序：列配置属性按照该列降序排列数据
* 显示次序：填写数字，对列进行排序，需要勾选按显示次序排序展示效果
* 分组长度：？？？
* 显示宽度：填写数字，定义该列显示宽度
* 交叉求和列名：首先该列是交叉列，填写的列名即为交叉列汇总列显示的列名
* 交叉求和方式：首先存在交叉列，并且当前列是交叉数据，选择求和方式，在汇总列显示该列的求和方式
* 组织类型：？？？
* 显示明细数据：显示列表数据
* 显示总计行：前提为树型显示，根节点为总计数据
* 树形显示：勾选后列表显示为树形结构，前提拖拽出划分树形层次的列（例如将列‘年’拖拽至第一层，将列‘月’拖拽至第二层）
* 第一列合并到树节点：前提为树型显示，将第一列合并到树节点
* 交叉列的汇总列排在前面：首先要有交叉列，且存在汇总列，则勾选此项，汇总列显示在交叉列的最前面
* 不简化交叉列显示名：？？？
* 空列在后面：？？？
* "合计"行居后：？？？
* 分组列只显示最后一项：前提为树型显示，若树有多层，勾选后只显示最后一层（例如年为第一层，月为第二层，点开后只显示月份）
* 按显示次序排序：列展示按照上方填写的‘显示次序’数字大小自左至右升序排列
* 查询到级次：前提为树形显示，设置单击查询按钮后列表初始显示层级
* 分组列列宽：前提为树型显示，设置分组项列宽

按钮：
* 确认：确认以此方式展现
* 放弃：放弃本次编辑
* 保存参数：将参数保存
```
参数名：为本次编辑的参数命名（也可选择之前保存过的参数进行替换）
可使用部门：选择可使用本参数的部门
关联到菜单：将该参数关联至分析模板按钮下方
确定：保存参数
删除：删除参数名相同的参数
放弃：放弃本次编辑
装载参数：通过选择参数名装载之前保存过的参数
```


**分组参数类图**

@startuml
Title 分组参数类图

UICompParamService <|.. UICompParamService

class UICompParamService{
+ public Object load();
+ public Object save();
}


@enduml

**分组参数时序图**

@startuml
Title 分组参数时序图

actor 客户端

participant UIComponentInit   as "UIComponentInit"
participant UICompParamService   as "UICompParamService"

客户端 -> UIComponentInit : [init]\ninit数据
activate UIComponentInit

	UIComponentInit -> UIComponentInit : [loadGroupParamNames]\n加载分组参数

UIComponentInit -> 客户端 :内部方法
deactivate UIComponentInit
客户端 -> UICompParamService : [按钮]\n加载/保存数据
activate UICompParamService

	UICompParamService -> UICompParamService : [load/save]\nload/save数据
	

UICompParamService -> 客户端 :内部方法
deactivate UICompParamService
@enduml


#### 分组查询手册

#### 分组查询设计

### 界面信息
#### 界面信息指南
#### 界面信息手册
#### 界面信息设计

### 导出参数
#### 导出参数指南
#### 导出参数手册
#### 导出参数设计

### 分析模板

#### 分析模板指南

**配置说明**

```
分析模板按钮命令号： groupquy
按钮配置：<ToolbarBtn name="uiname_btn_groupquy" title="分析模板" noClientComponent="true" xprops.iconClassName="icons-btn-analyse" />
GridTable配置属性:分组查询(groupData="true")
初始分组参数：GridTable配置属性uiprops.initGrpName="testGroupzz"(testGroupzz为已经保存的参数)
```

参见Demo[Grid大表Demo](uiinvoke/00/zh_CN/theme0/SN-HELP.UI.BigGrid.html)。

#### 分析模板手册

##### 分析模板

<img src="help/SN-CMC/UI/img/group2.png" />

```
groupTest1：已保存的参数，并勾选‘关联至菜单’
分组查询：进入分组参数配置界面
明细数据：展现初始界面
```

##### 分组查询

<img src="help/SN-CMC/UI/img/group1.png" />

##### 分组查询参数

支持拖拽对列展示顺序排序
```
求和方式：前提为树形显示，配置该列所显示的求和方式
隐藏：列配置属性隐藏该列
交叉：列配置属性该列为交叉列，其后面的列均为交叉数据
升序：列配置属性按照该列升序排列数据
降序：列配置属性按照该列降序排列数据
显示次序：填写数字，对列进行排序，需要勾选按显示次序排序展示效果
分组长度：暂无用处
显示宽度：填写数字，定义该列显示宽度
交叉求和列名：首先该列是交叉列，填写的列名即为交叉列汇总列显示的列名
交叉求和方式：首先存在交叉列，并且当前列是交叉数据，选择求和方式，在汇总列显示该列的求和方式
组织类型：暂无用处
显示明细数据：显示列表数据
显示总计行：前提为树型显示，根节点为总计数据
树形显示：勾选后列表显示为树形结构，前提拖拽出划分树形层次的列（例如将列‘年’拖拽至第一层，将列‘月’拖拽至第二层）
第一列合并到树节点：前提为树型显示，将第一列合并到树节点
交叉列的汇总列排在前面：首先要有交叉列，且存在汇总列，则勾选此项，汇总列显示在交叉列的最前面
分组列只显示最后一项：前提为树型显示，若树有多层，勾选后只显示最后一层（例如年为第一层，约为第二层，点开后只显示月份）
按显示次序排序：列展示按照上方填写的‘显示次序’数字大小自左至右升序排列
查询到级次：前提为树形显示，设置单击查询按钮后列表初始显示层级
分组列列宽：前提为树型显示，设置分组项列宽
```

##### 分组查询按钮

```
确认：确认以此方式展现
放弃：放弃本次编辑
保存参数：将参数保存
	参数名：为本次编辑的参数命名（也可选择之前保存过的参数）
	可使用部门：选择可使用本参数的部门
	关联到菜单：将该参数关联至分析模板按钮下方
	确定：保存参数
	删除：删除参数名相同的参数
	放弃：放弃本次编辑
装载参数：通过选择参数名装载之前保存过的参数
```

##### 明细数据
显示为进行分组查询的数据，即初始列表展示

#### 分析模板设计

```
分组配置界面：90.TBLGROUP
分组配置界面监听：xjs.table.tools.TableGroupData(这是分组配置界面的监听，不需要手动配置到需要分析模板的界面上，配置请参照配置说明)
```
