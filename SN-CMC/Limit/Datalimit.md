## 操作权限

|日期|作者|备注|
|------|------|------|
|2018-09-25|郭鑫|初稿|
|2019-11-11|郭鑫|修改|

### 指南

#### 概念说明

在数据交互过程中，几乎所有数据的读写及相关方法的访问都需要进行权限控制，总体分为对数据的读权限和写权限：
1. 读权限：一般使用过滤条件对数据进行过滤（AuthInfoUtils.buildFilter）；
2. 写权限：一般使用对已知的数据进行判断是否有指定的操作权限（AuthInfoUtils.hasLimit）；

#### 使用说明

##### 操作权限文件配置

**注意：权限结构一般由开发根据需求确定，直接定义在产品/项目文件中**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<datalimdef limnm="二级管理员(运维部门管理其他)" xmlns="http://www.snsoft.com.cn/schema/Datalimdef" xsi:schemaLocation="http://www.snsoft.com.cn/schema/Datalimdef http://www.snsoft.com.cn/schema/Datalimdef.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <datalimop opid="C" opnm="修改"/>
    <datalimop opid="R" opnm="查询"/>
    <datalimflds fldname="cuicode" fldtitle="商户" fldtype="12"/>
    <datalimflds fldname="limbcode" fldtitle="运维部门" fldtype="12" btype="00" fldopts="1" />
</datalimdef>
```

##### 权限宏

* 常用宏

配置数据权限时使用宏配置

|标题|宏|
|--|--|
|商户|$USERCUICODE|
|用户|$USERCODE|
|人员|$USERWCODE|
|运维部门|$USERLIMBCODE|
|部门|$USERBCODE|
|部门(岗位部门:推荐使用)|$USERGBCODE|

**注意【USERBCODE】和【USERGBCODE】区别**

|部门|人员|岗位|
|--|--|--|
|一部|张三|岗位1|
|二部|张三|其他岗位|

权限分配时：
1. 【岗位1】分配【$USERGBCODE】解析结果：一部
1. 【岗位1】分配【$USERBCODE】解析结果：一部,二部

**部门级次**

$BCODE(01@$USERGBCODE)[n]
n>0：正向第N级；
n=0：当前组织编码；
n<0：向上N级；

B01.B02.B03.B04

|标题|宏|示例|
|--|--|--|
|一级部门|$BCODE(01@$USERGBCODE)[1]|B01|
|二级部门|$BCODE(01@$USERGBCODE)[2]|B02|
|末级部门|$BCODE(01@$USERGBCODE)[0]<br>或<br>$USERGBCODE|B04|
|上级部门|$BCODE(01@$USERGBCODE)[-1]|B03|

* 扩展宏

$USERKEPTBCODE 记账部门宏
$USERCORPBCODE 公司宏
$USERGKEPTBCODE 岗位记账部门宏
$USERGCORPBCODE 岗位公司宏

与系统选项配合使用;DATA.DATALIMLISTENERS=snsoft.plat.bas.bcode.service.PlatDataLimitListener.new

**公司级次**

C01.C02.C03.C04

|标题|宏|示例|
|--|--|--|
|一级公司|$BCODE(02@$USERCORPBCODE)[1]|C01|
|二级公司|$BCODE(02@$USERCORPBCODE)[2]|C02|
|末级公司|$BCODE(02@$USERCORPBCODE)[0]<br>或<br>$USERCORPBCODE|C04|
|上级公司|$BCODE(02@$USERCORPBCODE)[-1]|C03|

**记账级次**

F01.F02.F03.F04

|标题|宏|示例|
|--|--|--|
|一级记账|$BCODE(03@$USERKEPTBCODE)[1]|F01|
|二级记账|$BCODE(03@$USERKEPTBCODE)[2]|F02|
|末级记账|$BCODE(03@$USERKEPTBCODE)[0]<br>或<br>$USERKEPTBCODE|F04|
|上级记账|$BCODE(03@$USERKEPTBCODE)[-1]|F03|

* 自定义权限宏功能说明

```
底层提供一套标准权限宏，以满足大部分权限配置。对于一些个性化的特殊权限，通过配置自定义权限宏来满足。
主要包含limkey,limbcode,limtac,bedate,ledate等字段。
返回逗号分割的字符串:"w1,w2,w3"
```

* 自定义权限宏实现代码

```
// 提供全局宏：
// 1. userInfo：当前用户回话信息
userInfo.getUserCode()// 当前【用户】编码
userInfo.getUserWCode()// 当前【人员】编码
userInfo.getUserCuicode()// 当前【商户】编码
userInfo.getUserGWCode()// 当前用户【岗位】编码，多个逗号分隔
userInfo.getUserBCode()// 当前用户【部门】编码，多个逗号分隔
userInfo.getUserLimBcode()// 当前用户【运维部门】编码
// 2. ucodeService：当前用户组织信息

```

* 特殊操作符

支持嵌套

|操作符|示例|效果|
|--|--|--|
|非：~|~1|c<>&apos;1&apos;|
|规则：%_|P%|c like &apos;P%&apos;|
|或：&#124;|A&#124;B|c=&apos;A&apos; or c=&apos;B&apos;|
|与：&|A&B|c=&apos;A&apos; and c=&apos;B&apos;|
|优先级：()|~(A&#124;B)|c<>&apos;A&apos; and c<>&apos;B&apos;|


##### 权限注解

* AuthParam

接口需要判断权限时需要添加权限注解：snsoft.api.service.AuthParam
所有判断权限的地方首先判断是否超级管理员，超级管理员拥有所有权限；同理如果一个功能仅允许超级管理员使用，直接配置此注解，不需要指定sheetCode和opids即可

```
权限的配置是在服务端组件接口方法上定义的，例：@AuthParam(sheetCode = "SN-HELP.order", opids = { "R", "C" })
注解@AuthParam配置方式，对于单据参数sheetCode = "SN-HELP.order"，需要单据号和权限id对应，当通过单据号找不到权限id的时候，默认认为单据号=权限id查询权限。 参数opids = { "R", "C" }对应检查R查、C操作权限。
权限注解参数：
sheetCode:定义单据编号
opids:定义权限操作
joinColumn:查询子表时使用：使用主表内码字段进行权限的判断
alias:表关联后，拼接权限过滤条件的表别名
wAdmin:wAdmin = 2 是 管理员用户可以使用该服务,wAdmin = 0|1超级管理员可以使用此服务
```

* InnerCode

内码注解：snsoft.api.service.InnerCode
远程方法调用判断权限时，需要指定数据内码配合AuthParam使用，根据内码判断权限

* 常用情景配置说明

```
1、单据查询
（1）主表
	@AuthParam(sheetCode="SN-XX.YY", opids={"R", "C"})
	QueryResults<T> query(QueryParams params);
（2）详情页面子表或孙表
	@AuthParam(sheetCode="SN-XX.YY", opids={"R", "C"}, joinColumn="拷贝主表字段(内码列)")
	List<T> query(QueryParams params);
2、单据存盘
	@AuthParam(sheetCode="SN-XX.YY", opids={"C"})
	SaveResults save(SaveParams<T> params);
3、远程方法：仅判断操作权限（与数据无关）
	@AuthParam(sheetCode="SN-XX.YY", opids={"ZZ"})
	Object invoke(Object p);
4、远程方法：判断数据权限
	@AuthParam(sheetCode="SN-XX.YY", opids={"ZZ"})
	Object invoke(@InnerCode String innerCode);
或
	@AuthParam(sheetCode="SN-XX.YY", opids={"ZZ"})
	Object invoke(@InnerCode String[] innerCodes);
或
	@AuthParam(sheetCode="SN-XX.YY", opids={"ZZ"})
	Object invoke(@InnerCode List<String> innerCodes);
或
	@AuthParam(sheetCode="SN-XX.YY", opids={"ZZ"})
	Object invoke(@InnerCode Map<String,Object> innerCodes);
或
	@AuthParam(sheetCode="@Param(1)", opids={"ZZ"})
	Object invoke(@InnerCode Map<String,Object> innerCodes,String sheetCode);
 {@code @Param(1)}：取第二个参数为单据号；
5、只有超级管理员可以调用的方法
	@AuthParam
	Object query/save/invoke(Object p);
6、管理员及以上权限可以调用的方法
	@AuthParam(wAdmin=2)
	Object query/save/invoke(Object p);
```

### 手册

#### 操作权限配置

菜单：[操作权限配置](uiinvoke/00/zh_CN/theme0/90.lim.DataLimitDef.html)：

操作权限配置用来定义一组具有相同的权限字段的集合。分为配置文件和数据库方式配置，数据库优先于配置文件。

##### 文件方式配置

操作权限配置支持XML格式配置：
路径：cfg/res/limit/
格式：*.xml，一个文件仅定义一条权限定义记录；
配置请参见对应的Schema文件【[Datalimdef.xsd](do/BrowseSchema?schema=Datalimdef.xsd)】；

##### 数据库方式配置

* 主表

操作权限复制按钮：复制当前行权限配置。

```
权限编码:权限配置主键
权限名称：名称
日期字段：如果配置了日期字段，则检查当前业务数据的日期与权限日期是否匹配：注意：
1.如果业务数据为空，则业务日期默认为当天；
2.如果业务日期为空，则认为有权限；
共享表名： 主表表名。
共享字段：主表唯一标识。
扩大共享表表名：扩大共享表，记录业务主表数据共享记录。
扩大共享表关联字段：扩大共享表记录业务主表唯一标识字段。
```

* 权限操作类型表

```
操作编码：一般默认 R查询；C修改；
操作名称：控制操作名称
```

* 权限字段

```
序号:排序字段
字段名称：权限字段常用的 bcode、wcode、usercode
字段标题：字段说明
字段宽度：配置权限时的字段显示宽度
类型：字段类型，文本、整数；
路径：一般组织使用，匹配组织及所有下级组织
双向：一般组织使用，匹配组织及所有下级组织+直接所有上级组织（直到根节点）；
多选：多值匹配，如status 匹配  10,20,30
空值:空值匹配，配置 wcode="" or wcode is null
组织类型：一般为业务组织，根据系统支持匹配
扩大共享配置：
扩大共享|字段名称：限字段常用的sbcode、swcode、susercode
扩大共享|路径：一般组织使用，匹配组织及所有下级组织
扩大共享|双向：一般组织使用，匹配组织及所有下级组织+直接所有上级组织（直到根节点）；
扩大共享|多选：多值匹配，如status 匹配  10,20,30
扩大共享|空值：空值匹配，配置 swcode="" or swcode is null
扩大共享|使用主表：在扩大共享表(sharebcode)过滤中，使用 主表值过滤
码表配置：
码表|码表：配置码表，如：#sheet.btype
码表|显示码：显示码表编码
码表|显示名：显示码表名称
码表|显示全名：显示码表全名
码表|下拉：配置下拉码表
码表|按钮：配置按钮方式码表
码表|大文本：配置大文本
码表|多选：配置多选码表
码表|分级：配置分级码表
码表|可选中间级：配置可选中间级码表
```

#### 操作权限分配

操作权限分配包含岗位操作权限、用户操作权限、部门操作权限。
注意：在实际运维中强烈建议使用岗位操作权限分配，不建议使用用户分配操作权限。

##### 岗位操作权限

菜单：[岗位操作权限](uiinvoke/00/zh_CN/theme0/90.lim.LimitForGwcode.html)

岗位操作权限用来配置某一岗位具有的操作权限，拥有该岗位的用户都具有对应菜单权限；选定岗位和权限配置：在岗位操作权限配置区域配置该岗位操作权限。

```
操作编码：根据【操作权限配置】中定义的操作类型作为下拉码表。
序号：排序字段
其他权限字段：如部门人员，根据【操作权限配置】中配置的权限字段动态显示。
启用日期：当前岗位操作权限的启用日期；
终止日期：当前岗位操作权限的终止日期；
修改时间：当前记录的修改时间；
```

* 权限复制按钮：

复制选定行权限分配数据。

* 拷贝其他岗位权限按钮：

拷贝在其他权限岗位配置号的权限数据。

##### 岗位操作权限(跨商户)

菜单：[岗位操作权限(跨商户)](uiinvoke/00/zh_CN/theme0/90.lim.LimitForGwcodeCuicode.html)
	此处用于多商户公用岗位时进行菜单权限的维护；
	**注意：未启用多商户时，此功能不可用**

##### 用户操作权限

菜单：[用户操作权限](uiinvoke/00/zh_CN/theme0/90.lim..LimitForUser.html)

用户操作权限用来配置某一用户具有的操作权限，选定用户和权限配置：在用户操作权限配置区域配置该用户操作权限。
```
操作编码：根据【操作权限配置】中定义的操作类型作为下拉码表。
序号：排序字段
其他权限字段：如部门人员，根据【操作权限配置】中配置的权限字段动态显示。
启用日期：当前岗位操作权限的启用日期；
终止日期：当前岗位操作权限的终止日期；
修改时间：当前记录的修改时间；
```

* 权限复制按钮：

复制选定行权限分配数据。

* 拷贝其他用户权限按钮：

拷贝在其他权限用户配置号的权限数据。

##### 部门操作权限

菜单：[部门操作权限](uiinvoke/00/zh_CN/theme0/90.lim.LimitForBcode.html)


部门操作权限用来配置某一部门下所有用户具有的操作权限，选定部门和权限配置：在用户操作权限配置区域配置该部门操作权限。

```
操作编码：根据【操作权限配置】中定义的操作类型作为下拉码表。
序号：排序字段
其他权限字段：如部门人员，根据【操作权限配置】中配置的权限字段动态显示。
启用日期：当前岗位操作权限的启用日期；
终止日期：当前岗位操作权限的终止日期；
修改时间：当前记录的修改时间；
```

* 权限复制按钮：

复制选定行权限分配数据。

* 拷贝其他用户权限按钮：

拷贝在其他权限用户配置号的权限数据。

#### 用户权限拷贝工作台

将选定用户权限拷贝至目标用户，包括：用户岗位、部门人员岗位、用户菜单权限、用户操作权限。

#### 操作权限查询

操作权限查询界面，主要是查询已经配置好的用户、岗位操作权限。

#### 用户权限查询

用户权限查询界面，主要是查询指定单据、指定用户的权限。

查询条件：
```
用户编码：非空选项，选择要查询权限的人员
单据号：选择要查询该人员在该单据号是否有权限
操作编码：C、D、R等操作权限码
单据内/外码：判断该用户在该单据内是有拥有【操作编码】对应的权限
```

#### 管理员权限视图

供管理员查看用户对指定单据具有的权限。


#### 用户权限视图

供用户查看自己所具有的的单据权限。

#### 权限过度关系

在分配【岗位/部门/用户】权限前，先设置与权限的对应关系[cmc_gwlim]。

**升级SQL如下**

```SQL
--岗位
insert into cmc_gwlim(cuicode,utype,ucode,limid)
select distinct m.cuicode,'G',m.ucode,m.limid
from datalims m
where m.utype='G'
and not exists(select 1 from cmc_gwlim g where g.cuicode=m.cuicode and g.utype='G' and g.ucode=m.ucode)
--用户
insert into cmc_gwlim(cuicode,utype,ucode,limid)
select distinct m.cuicode,'U',m.ucode,m.limid
from datalims m
where m.utype='U' and m.ucode!='*'
and not exists(select 1 from cmc_gwlim g where g.cuicode=m.cuicode and g.utype='U' and g.ucode=m.ucode)
--部门
insert into cmc_gwlim(cuicode,utype,ucode,limid)
select distinct m.cuicode,'B',m.bcode,m.limid
from datalims m
where m.utype='U' and m.ucode='*'
and not exists(select 1 from cmc_gwlim g where g.cuicode=m.cuicode and g.utype='B' and g.ucode=m.bcode)
```

#### 单据权限关系

* 单据权限关系是单据类型与权限定义之间的对应关系，默认查找与单据号相同的权限ID。
* 单据与权限之间为多对多关系；
* 参见[单据权限关系](uiinvoke/00/zh_CN/theme0/90.sheet.Busheetlim.html)；
* 支配JSON格式的配置文件：res/sheetlim/[sysid].json；
	```json
	[
		{
			"sheetcode": "sheet.Users",
			"limid": "sheet.Bcode"
		},
		{
			"sheetcode": "sheet.Gwcode",
			"limid": "sheet.Bcode"
		}
	]
	```

#### 扩大共享

##### 权限字段

<table>
	<tr>
		<th align='center' colspan='4'>扩大共享权限字段</th>
	</tr>
	<tr>
		<td>标准权限字段与共享权限字段<b>同</b>名</td>
		<td>
		&lt;datalimflds fldname=&quot;wcode&quot; fldtitle=&quot;人员&quot; fldtype=&quot;12&quot; /&gt;<br/>
		&lt;datalimflds fldname=&quot;xxwcode&quot; fldtitle=&quot;共享人员&quot; fldtype=&quot;12&quot; fldopts=&quot;8&quot; fldname2=&quot;wcode&quot; /&gt;
		</td>
	</tr>
	<tr>
		<td>标准权限字段与共享权限字段<b>异</b>名</td>
		<td>
		&lt;datalimflds fldname=&quot;wcode&quot; fldtitle=&quot;人员&quot; fldtype=&quot;12&quot; /&gt;<br/>
		&lt;datalimflds fldname=&quot;swcode&quot; fldtitle=&quot;共享人员&quot; fldtype=&quot;12&quot; fldopts=&quot;8&quot; fldname2=&quot;swcode&quot; /&gt;
		</td>
	</tr>
</table>

**注意：同名时不可公用权限字段，否则标准权限与共享权限无法区分**

##### 扩大共享使用说明
1. 单据权限配置中，配置对应的扩大共享表名对应字段（需要数据库中存在对应表，结构参考plat_share，并存在对应表结构定义，为了准确定位库，尽量与业务表同库）
1. 单据权限配置中配置字段，见上面 扩大共享权限字段
1. 打开业务数据，将对应数据的查询或操作权限共享给某个人

###### 界面修改针对共享弹框
1. 在原界面配置客户端监听#new snsoft.plat.bas.sheet.share.PlatSheetShareJSListener({}) 用于弹出共享弹框
1. 在原界面追加按钮 platShare，在弹出界面编辑将当前数据进行共享
1. 在原界面配置的sv系统功能配置存盘监听 snsoft.plat.bas.sheet.share.service.impl.PlatShareSVListener 或存盘Bean SN-PLAT.PlatShareSVListener 用于删除数据时删除共享数据，若界面禁止删除了忽略此步骤

###### 界面修改针对嵌入 
1. 在原界面配置客户端监听 #new snsoft.plat.bas.sheet.share.PlatShareMasterJSListener({}) 用于主表行移动时刷新权限共享表
1. 原界面追加 &lt;include src="SN-PLAT.Share.ShareSub#plat_share" mainui="salorder" /&gt; 用于显示内嵌权限配置
1. 在原界面配置的sv系统功能配置存盘监听 snsoft.plat.bas.sheet.share.service.impl.PlatShareSVListener 或存盘Bean SN-PLAT.PlatShareSVListener 用于删除数据时删除共享数据，若界面禁止删除了忽略此步骤

##### 扩大共享组件

<table>
	<tr>
		<th align='center' colspan='4'>扩大共享组件说明</th>
	</tr>
	<tr>
		<td>表名</td>
		<td>plat_share</td>
	</tr>
	<tr>
		<td>列名</td>
		<td>
			<b>cuicode</b>=商户<br/>
			<b>innercode</b>=原单据内码<br/>
			sopid=共享|操作<br/>
			sbcode=共享|部门<br/>
			swcode=共享|人员<br/>
			scorpbcode=共享|公司<br/>
			skeptbcode=共享|记账部门<br/>
			scostbcode=共享|核算组<br/>
			slimbcode=共享|运维部门<br/>
		</td>
	</tr>
	<tr>
		<td>配置</td>
		<td>
			1. 文件名为单据号：支持数据库；<br/>
			2. stblname：共享表名，默认为plat_share；<br/>
			3. scolnames：启用的列名（[sopid,sbcode,swcode]...slimbcode）；<br/>
			4. wopids：存盘权限操作码（判断原单据，默认C/U）；<br/>
			5. ropids：查询权限操作码（判断原单据，默认C/U/R）；<br/>
			6. sbopid：共享部门操作码（默认使用同一运维部门limbcode）；<br/>
			7. scopid：共享公司操作码（默认使用同一运维部门limbcode）；<br/>
			8. skopid：共享记账部门操作码（默认使用同一运维部门limbcode）；<br/>
			9. soopid：共享核算组操作码（默认使用同一运维部门limbcode）；<br/>
			10. slopid：共享运维部门操作码（默认使用同一运维部门limbcode）；<br/>
		</td>
	</tr>
	<tr>
		<td>组件</td>
		<td>
			PlatShareService：共享服务<br/>
			PlatShareSVListener：原单据存盘监听（原单据删除时删除对应的扩大共享）<br/>
			PlatShareUIListener：（共享对话框）初始化配置/读写权限校验/界面属性控制<br/>
			PlatShareJSListener：（共享对话框）在界面初始化之前设置单据号参数<br/>
			PlatSheetShareJSListener({sheetcode:&quot;&quot;})：单据界面配置，处理【扩大共享】按钮事件弹出共享对话框<br/>
		</td>
	</tr>
</table>

#### 单据部门人员选择

<table>
	<tr>
		<th align='center' colspan='4'>单据部门人员选择</th>
	</tr>
	<tr>
		<th align='center'>优先级</th>
		<th align='center'>匹配方式</th>
	</tr>
	<tr>
		<td>部门人员</td>
		<td>查找已分配的部门人员权限(包括扩大共享)</td>
	</tr>
	<tr>
		<td>公司</td>
		<td>查找已分配的公司权限(包括扩大共享),<br/>然后根据组织关系转为部门权限</td>
	</tr>
	<tr>
		<td>记账部门</td>
		<td>查找已分配的记账部门权限(包括扩大共享),<br/>然后根据组织关系转为部门权限</td>
	</tr>
	<tr>
		<td>运维部门</td>
		<td>查找已分配的运维部门权限(包括扩大共享),<br/>然后根据部门中的运维部门转为部门权限</td>
	</tr>
	<tr>
		<td>实现类</td>
		<td>snsoft.dx.data.service.impl.BwcodeFilterServiceImpl.BwcodeFilterBuilderImpl<br/>
		snsoft.plat.bas.bcode.service.PlatBwcodeFilterBuilderImpl</td>
	</tr>
</table>

#### DCL

对SQL ANSI 规范中DCL的支持

<table>
	<tr>
		<th align='center'>命令</th>
		<th align='center'>说明</th>
	</tr>
	<tr>
		<th colspan='2'>枚举类</th>
	</tr>
	<tr>
		<td>list users [like **pattern**]</td>
		<td>枚举所有用户，可以通过like指定过滤</td>
	</tr>
	<tr>
		<td>list roles [like **pattern**]</td>
		<td>枚举所有角色，可以通过like指定过滤</td>
	</tr>
	<tr>
		<td>list bcodes [like **pattern**]</td>
		<td>枚举所有部门，可以通过like指定过滤</td>
	</tr>
	<tr>
		<td>list sheets [like **pattern**]</td>
		<td>枚举所有单据，可以通过like指定过滤</td>
	</tr>
	<tr>
		<th colspan='2'>描述类</th>
	</tr>
	<tr>
		<td colspan='2'>desc user</td>
	</tr>
	<tr>
		<td>desc user [user]</td>
		<td>显示用户、人员、部门及岗位、岗位等组织信息及分配权限记录</td>
	</tr>
	<tr>
		<td>desc user [user] for sheetcode:op1/op2</td>
		<td>组织信息及指定单据的权限分配记录和对应的权限过滤条件</td>
	</tr>
	<tr>
		<td>desc role [gwcode]</td>
		<td>显示岗位信息、岗位对应的部门人员信息及岗位对应对的用户信息</td>
	</tr>
	<tr>
		<td>desc bcode [bcode]</td>
		<td>显示部门信息、(中间级)子部门信息、(末级)部门人员信息</td>
	</tr>
	<tr>
		<td>desc auth [limid]</td>
		<td>显示权限配置及分配信息</td>
	</tr>
	<tr>
		<td>desc sheet [sheet]</td>
		<td>显示单据及其相关信息</td>
	</tr>
	<tr>
		<th colspan='2'>修改类[alter]</th>
	</tr>
	<tr>
		<th colspan='2'>授权类[grant]</th>
	</tr>
	<tr>
		<th colspan='2'>撤销类[revoke]</th>
	</tr>
</table>

### 实践


#### 权限场景说明

<table>
	<tr>
		<th align='center'>权限场景说明</th>
	</tr>
	<tr>
		<th align='center'>一个权限字段</th>
	</tr>
	<tr>
		<td>
			<table>
				<tr>
					<th align='center' rowspan='2'>分配情况</th>
					<th align='center' colspan='2'>权限分配</th>
					<th align='center' rowspan='2'>过滤条件</th>
					<th align='center' colspan='4'>权限判断</th>
				</tr>
				<tr>
					<th align='center'>操作</th>
					<th align='center'>人员</th>
					<th align='center'>操作(R)</th>
					<th align='center'>数据(R,{wcode:'1'})</th>
					<th align='center'>数据(R,{wcode:'2'})</th>
				</tr>
				<tr>
					<td>为空</td>
					<td>R</td>
					<td></td>
					<td>1=0</td>
					<td>true</td>
					<td>false</td>
					<td>false</td>
				</tr>
				<tr>
					<td>通配</td>
					<td>R</td>
					<td>%</td>
					<td>1=1</td>
					<td>true</td>
					<td>true</td>
					<td>true</td>
				</tr>
				<tr>
					<td>指定</td>
					<td>R</td>
					<td>1</td>
					<td>wcode='1'</td>
					<td>true</td>
					<td>true</td>
					<td>false</td>
				</tr>
				<tr>
					<td rowspan='2'>双行</td>
					<td>R</td>
					<td></td>
					<td rowspan='2'>1=0 or 1=1<br/><b>1=1</b></td>
					<td rowspan='2'>true</td>
					<td rowspan='2'>true</td>
					<td rowspan='2'>true</td>
				</tr>
				<tr>
					<td>R</td>
					<td>%</td>
				</tr>
				<tr>
					<td rowspan='2'>双行</td>
					<td>R</td>
					<td></td>
					<td rowspan='2'>1=0 or wcode='1'<br/><b>wcode='1'</b></td>
					<td rowspan='2'>true</td>
					<td rowspan='2'>true</td>
					<td rowspan='2'>false</td>
				</tr>
				<tr>
					<td>R</td>
					<td>1</td>
				</tr>
				<tr>
					<td rowspan='2'>双行</td>
					<td>R</td>
					<td>%</td>
					<td rowspan='2'>1=1 or wcode='1'<br/><b>1=1</b></td>
					<td rowspan='2'>true</td>
					<td rowspan='2'>true</td>
					<td rowspan='2'>true</td>
				</tr>
				<tr>
					<td>R</td>
					<td>1</td>
				</tr>
			</table>
		</td>
	</tr>
</table>


### 设计

* 操作权限类图

@startuml
Title 操作权限类图

DataLimits <|.. AbsDataLimits
AbsDataLimits <|-- DataLimitsImpl

AuthParam <.. AuthInfo
AuthInfo <--* AuthInfoUtils
DataLimitsUtils <.. AuthInfoUtils
DataLimitsFatory <.. AuthInfoUtils
DataLimits <.. DataLimitsFatory
DataLimits <.. DataLimitsUtils

class AuthInfoUtils{
	- authInfo : AuthInfo;
	+ buildFilter() : SqlExpr
	+ hasLimit(data:Object) : boolean
}

class AuthInfo{
	-sheetCode:String
	-opids:String[]
}

interface DataLimitsFatory{
	+ getDataLimits(limid:String):Datalimits
}

interface DataLimitsUtils{
	+ getLimids(sheetCode:String):String[]
}

interface DataLimits{
	+ hasLimit(limParam:LimitParameter,data:Map<String,Object>):boolean
	+ buildFilterExpr(limParam:LimitParameter):SqlExpr
}
@enduml

* 操作权限时序图

@startuml
权限调用者 -> AuthInfo : [new]创建权限参数对象
权限调用者 -> AuthInfoUtils : [hasLimit]判断是否有权限
AuthInfoUtils -> AuthInfoUtils : [getLimids] 获取对应的权限ID
AuthInfoUtils -> DataLimitsUtils : [getLimids] 获取对应的权限ID
DataLimitsUtils -> AuthInfoUtils : 返回找到的limids
DataLimitsUtils -> DataLimitsFatory : [getDataLimits] 获取权限判断对象
DataLimitsFatory -> DataLimitsUtils : 返回权限判断对象
AuthInfoUtils -> DataLimits: [hasLimit] 判断是否有权限
DataLimits -> AuthInfoUtils: 返回判断结果
AuthInfoUtils -> 权限调用者 : 返回判断结果
@enduml








