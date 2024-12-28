## 组织结构

|日期|作者|备注|
|------|------|------|
|2017-07-25|郭鑫|初稿|
|2018-10-23|冀小雷|审核|
|2019-11-07|郭鑫|修改|


### 指南

#### 概念说明

组织是ERP中为了核算定义的一组具有上下级关系树状节点集合。

#### 使用说明

##### 组织码表

* 商户码表

#comuser

* 组织类型码表

#sheet.btype

* 岗位码表：

#sheet.gwcode

* 用户码表

#sheet.users：分商户时，跨商户选择人员时使用。

* 人员码表

#sheet.wcode

* 运维部门码表

#sheet.limbcode

* 部门码表

#sheet.bcode

* 部门人员码表

#sheet.bwcode

##### 跨商户码表

* 码表配置

后台维护人员使用，参数商户必录,业务界面,不需要处理商户，默认当前商户。

```
sheet.bcode、sheet.bwcode
```

组织码表支持配置参数：

```xml
selectMiddle 可选中间级
mutiple 多选
cmprops.pmFromPane="{cuicode:'bcode.cuicode'}" 对应界面取参数
cmprops.pmFromPane="{filterByWcode:'bcode.wcode'}"：根据界面wcode过滤
aiprops.copyMap="{out_wcode:'wcode'}" 拷贝字段
aiprops.dsConfigs="{initToLevel:1}" 树初始展开级次
cmparams.btype="01"   固定值参数
cmparams.btype:组织类型
cmparams.filterDate：过滤生效数据
cmparams.cuicode：商户过滤

显示名全名： showfname="true"
按级次显示名：
uiprops.showLvFullnm="[n1,n2,n3]"
	n大于0：正向第N级；
	n=0：当前组织编码；
	n小于0：向上N级；
```

* 码表显示名处理

登录用户与需要显示的组织码表不属于同一个商户。包含sheet.bcode、sheet.wcode、sheet.users等对应的码表处理。

**推荐配置监听**，效率高。(限制:处理字段和商户字段同一个数据集)

```xml
<!--客户端监听：配置商户字段，处理字段；-->
<jslistener>
	#new snsoft.sheet.bcode.comm.CodeNameRefCuicodeCliListener({cuicodeCol:'cuscuicode',codeCol:'insured_wcode'})
</jslistener>
<!--服务端监听：配置商户字段，处理字段，对应码表；-->
<uilisteners><![CDATA[
	snsoft.cmc.ucode.CodeNameRefCuicodeUIListener.new?cuicodeCol=cuscuicode&codeCol=insured_wcode&codeTbl=sheet.wcode
]]></uilisteners>
```

详情页且商户字段和需要显示的字段不在同一张表。

```xml
<!--用户显示名；-->
uiprops.renderer="new snsoft.sheet.bcode.comm.UsercodeNameRender({uiname:'xx',cuicodeCol:'',ucodeCol:''})"
<!--人员显示名；-->
uiprops.renderer="new snsoft.sheet.bcode.comm.WcodeNameRender({uiname:'xx',cuicodeCol:'',wcodeCol:''})"
<!--部门；-->
uiprops.renderer="new snsoft.sheet.bcode.comm.BcodeNameRender({uiname:'xx',cuicodeCol:'',bcodeCol:''})"
```

##### 组织数据

* 当前登录用户数据获取

```java
// 一、获取UserSession
UserSession usersession = AppContext.getUserSession(true);
// 二、UserSession中包含获取用户编码 、人员编码、组织编码、用户岗位等方法。
```

* 组织间关系通过UcodeService服务来实现，获取方法：

```java
// 默认当前商户或不区分商户
UCodeService ucodeService = UCodeServiceFactory.impl.getUCodeService();
// 跨商户
UCodeService ucodeService = UCodeServiceFactory.impl.getUCodeService(cuicode);
```

##### 历史组织使用

借助Oracle的FDA(Flashback Data Archive=闪回数据归档)功能实现此需求。

```sql
--4、FDA
--4.1 创建FDA归档
CREATE FLASHBACK ARCHIVE fda1
	TABLESPACE SNFDADATA_TBS QUOTA 16M RETENTION 5 YEAR;
--4.2 设置默认FDA
ALTER FLASHBACK ARCHIVE fda1 SET DEFAULT;
--4.3 设置需要开启闪回归档的表
ALTER TABLE cu_ufg.users 		FLASHBACK ARCHIVE;
ALTER TABLE cu_ufg.usersa 		FLASHBACK ARCHIVE;
ALTER TABLE cu_ufg.userdata		FLASHBACK ARCHIVE;
ALTER TABLE cu_ufg.gwcode 		FLASHBACK ARCHIVE;
ALTER TABLE cu_ufg.usergwcode	FLASHBACK ARCHIVE;
ALTER TABLE cu_ufg.wcode 		FLASHBACK ARCHIVE;
ALTER TABLE cu_ufg.bcode 		FLASHBACK ARCHIVE;
ALTER TABLE cu_ufg.bwcode 		FLASHBACK ARCHIVE;
ALTER TABLE cu_ufg.bwgwcode 	FLASHBACK ARCHIVE;
ALTER TABLE cu_ufg.busibcode 	FLASHBACK ARCHIVE;
ALTER TABLE cu_ufg.corpbcode 	FLASHBACK ARCHIVE;
ALTER TABLE cu_ufg.keptbcode 	FLASHBACK ARCHIVE;
ALTER TABLE cu_ufg.costbcode 	FLASHBACK ARCHIVE;
ALTER TABLE cu_ufg.rcbcode 		FLASHBACK ARCHIVE;
ALTER TABLE cu_ufg.rkcbcode 	FLASHBACK ARCHIVE;
ALTER TABLE cu_cfg.datalims 	FLASHBACK ARCHIVE;
--4.4 辅助功能
--增加定额空间
ALTER FLASHBACK ARCHIVE fda1 modify TABLESPACE SNFDADATA_TBS QUOTA 1G;
--修改保留期
ALTER FLASHBACK ARCHIVE fda1 MODIFY RETENTION 2 YEAR;
--删除数据
ALTER FLASHBACK ARCHIVE fla1 PURGE BEFORE TIMESTAMP (SYSTIMESTAMP - INTERVAL '1' day);
--删除FDA
DROP FLASHBACK ARCHIVE fla1;
--4.5 查询
--查询闪回归档信息
select * from dba_flashback_archive
--查询闪回归档与表空间关系
select * from dba_flashback_archive_ts
--查询FDA表
select * from dba_flashback_archive_tables
```

### 手册

#### 商户管理员

* 概念：商户注册成功后默认生成的用户为商户管理员（也可以手工设置），商户管理员可以查看所有商户菜单且读写所有商户数据。
* **设置商户管理员**：在【管控中心-用户管理】处设置（物理结构为users.wadmin&4）；
* **设置单据支持**：在单据定义中设置支持商户管理员功能（物理结构为busheet.sheetflags&4），此单据对商户管理员可用，不用单独分配权限；
	* 商户单据的UIService必须注解@Cuicode，用于强制过滤商户；
* **设置菜单支持**：在菜单定义中设置支持商户管理员功能（物理结构为menu.flags&0x080），此菜单对商户管理员可见，不用单独分配权限；


#### 商户维护

在大选项启用商户功能时，APP.StartCuicode=true。维护商户编码、商户名称、商户简码等字段，每个商户拥有各自独立的组织结构；
维护好商户信息，然后再能去组织维护中生成组织根节点，添加用户人员等信息。

#### 组织类型

入口界面可支持查询登录系统支持的所有组织类型。组织类型配置文件目录：src\main\resources\snsoft\res\bcode\btype.json。管控中心默认支持00：运维组织；01：业务组织；02：法人组织；03：财务组织；04：成本组织。如需支持其他类型组织在各自工程下追加即可。

业务组织：集团实际存在的组织结构，明细级下面与人员建立关系。做业务单据时选择的部门人员，就是这个。
法人组织：集团下多个分公司，每个公司一个法人组织，对公申报审计使用。
财务组织：用来记录以财务组织为单位的收入支出。
成本组织：用来记录项目中该成本组织对应部门人员的支出。

对应关系：
业务组织（n）    （1） 法人组织
法人组织（1）     （n）财务组织
业务组织（1）+法人组织（1）唯一对应 （1） 财务组织
成本组织(1)   (n)业务组织+人员

#### 岗位维护

1. SN-N9B中的岗位组织重要的构成部分； 岗位是组织要求个体完成的一项或多项责任以及为此赋予个体的权限的总和。岗位维护主要针对系统可能用到的岗位进行维护（增删改），包含用户岗位、部门岗位：
	1. 用户岗位:直接在用户维护中设置岗位（注意：该岗位优先级大于部门岗位，如果给用户设置岗位，则对其设置的部门岗不在其作用）。
	1. 部门岗位：在部门人员维护中设置部门下某个人员的岗位，该人员在不同的部门可以拥有不同的岗位，以适应复杂的ERP岗位设置。

#### 用户维护

用户维护主要针对系统可登录用户进行维护的功能。主要维护登录名称、用户名称、对应人员、用户岗位、生效日期、失效日期等信息。
用户可以通过记录的wcode找到唯一对应人员。

#### 用户管理

“用户管理”主要用于设置超级管理员、二级管理员信息、重置密码、解除锁定等功能。
仅超级用户有此功能权限。

#### 组织维护

* 组织状态

组织中都有状态操作列，该列分为三个状态：待生效、生效中、已失效。状态是根据生效日期和失效日期动态显示的，默认为生效状态。
```
待生效：即“生效日期”不为空且“生效日期”大于当前系统日期的记录；
已生效：（“生效日期”为空或“生效日期”小于等于当前系统日期）并且（“失效日期”为空或“失效日期”大于当前系统日期）的记录；
已失效：即“失效日期”不为空且“失效日期”小于等于当前系统日期的记录。
```

* 组织维护入口界面

```
部门维护主要用来维护部门维护信息、部门上下级、部门迁移、部门排序、生效日期、失效日期等信息。
```

* 界面操作

* 新建根节点

```
点击新建根节点按钮，将根据组织类型创建对应的组织结构根节点。仅超级管理员有此功能。
```

#### 人员维护

人员维护主要针对系统用到的人员进行维护（增删改）。

#### 运维部门维护

运维部门组织类型：00,是一种特殊的组织结构，其结构与组织维护一致，可以在组织维护中进行维护。其主要用来管理组织相关功能的权限。

* 不同点

```
只有编码、名称、生效日期需要维护
用来管理组织相关权限，不在业务单据中使用
部门、岗位、用户、人员等需要选择运维部门来权限各自的权限范围。
```

#### 部门人员维护

部门人员维护主要用来维护部门下有哪些人员、部门下人员的岗位、部门下人员的生效日期、部门下人员的失效日期等信息。

##### 操作按钮

* 批量添加人员

```
点击批量添加人员按钮，将弹出人员多选码表，将选定人员添加到所有已选择的部门下，本功能支持选择中间级，即选择了中间级的话会处理该中间级下面所有的子记录。
```
* 批量添加岗位

```
点击批量添加岗位按钮，将弹出岗位多选码表，将选定岗位添加到所有已选择的部门下所有的人员上；
本功能支持选择中间级，即选择了中间级的话会处理其下所有的子记录。
同时本功能会直接覆盖掉原有岗位的数值，请谨慎使用。
```

* 批量取消岗位

```
点击批量取消岗位按钮，将弹出岗位多选码表，所选定的岗位会在已选择的部门下所有人员的岗位列中取消掉。
本功能支持选择中间级，即选择了中间级的话会取消掉其下所有子记录的该岗位，请谨慎使用。
```

#### 组织依赖关系

主要维护组织间相互依赖关系，如用户A对应人员A，则删除人员A时，需要检查在人员A是否在用户中使用，如果被使用则抛错或者提示，不允许删除。否则用户A中记录的人员A则变成脏数据，找不到对应记录。

#### 历史组织结构

企业几乎每天都在调整组织结构及权限数据，在某些场景下，需要查看历史某个时间点的组织结构或权限。
借助Oracle的FDA(Flashback Data Archive=闪回数据归档)功能实现此需求。
参见[历史组织结构](uiinvoke/00/zh_CN/theme0/90.Bcode.HistoryQuery.html)

#### 岗位权限说明

* 在设计或上线前阶段，用于辅助记录本司岗位与功能之间的关系；
* 在运行阶段，用于维护并记录本司岗位与功能之间的关系；
* 功能实现参见[岗位权限说明](uiinvoke/00/zh_CN/theme0/90.Bcode.GwcodeSheet.html)

Demo如下：
* 其中维护权限为通用权限，通过配置部门权限进行实现；
* 销售合同共享权限的分配：共享给自己或自己所在部门及上级部门，通过部门权限进行分配；

|岗位|销售合同[SN-HELP.Salorder]|组织管理[sheet.Bcode]|...|
|--|--|--|--|
|SN001=副总|查询[R]：全公司(一级部门)<br/>维护[C]：自己||...|
|SN002=经理|查询[R]：事业部(二级部门)<br/>维护[C]：自己||...|
|SN003=主管|查询[R]：事业部(三级部门)<br/>维护[C]：自己||...|
|SN004=业务员|查询[R]：自己<br/>维护[C]：自己||...|
|SN005=财务|查询[R]：对应的记账部门<br/>修改[U]：对应的记账部门||...|
|SN006=管理员|查询[R]：全公司(一级部门)|查询[R]：所在运维部门<br/>维护[C]：所在运维部门|...|

### 设计

@startuml
Title 组织结构ER图

人员 <-- 用户:wcode
用户 <-- 用户岗位:usercode
岗位 <-- 用户岗位:gwcode

单点登录 --> 用户:usercode
用户扩展 --> 用户:usercode

部门 <-- 部门人员:bcode
人员 <-- 部门人员:wcode
部门人员 <-- 部门人员岗位:bcode+wcode
岗位 <-- 部门人员岗位:gwcode

部门 <-- 记账部门扩展:bcode\nbtype=03
部门 <-- 公司扩展:bcode\nbtype=02
部门 <-- 部门扩展:bcode\nbtype=01
部门 <-- 核算组扩展:bcode\nbtype=04

记账部门扩展 <-- 部门公司记账关系
公司扩展 <-- 部门公司记账关系
部门扩展 <-- 部门公司记账关系

部门扩展 <.. 部门核算关系:部门 或\n部门人员
核算组扩展 <-- 部门核算关系

class 岗位 {
	// gwcode
	*gwcode:岗位编码
	gwname:岗位名称
}

class 人员 {
	// wcode
	*wcode:人员编码
	wname:人员名称
}

class 部门 {
	// bcode
	*bcode:部门编码
	bname:部门名称
}

class 用户 {
	// users
	*usercode:用户编码
	username:用户名称
	wcode:人员编码
}

class 用户扩展 {
	// userdata
	*usercode:用户编码
	sex:性别
	birthdate:生日
}

class 单点登录 {
	// usersa
	*appid:应用ID
	*openid:openid
	usercode:用户编码
}

class 用户岗位 {
	// usergwcode
	*usercode:用户编码
	*gwcode:岗位编码
}
class 部门人员 {
	// bwcode
	*bcode:部门编码
	*wcode:人员编码
}
class 部门人员岗位 {
	// bwgwcode
	*bcode:部门编码
	*wcode:人员编码
	*gwcode:岗位编码
}

class 部门扩展 {
	// busibcode
	*bcode:组织编码
}

class 公司扩展 {
	// corpbcode
	*bcode:组织编码
}

class 记账部门扩展 {
	// keptbcode
	*bcode:组织编码
}

class 核算组扩展 {
	// costbcode
	*bcode:部门编码
}

class 部门公司记账关系 {
	// rkcbcode
	*rkcbicode:主键
	keptbcode:记账部门
	corpbcode:公司
	defcorp：默认公司
	busibcode：部门
}

class 部门核算关系 {
	// rcbcode
	*rcbicode:主键
	costbcode:核算组
	bcode:部门
	wcode:人员
}
@enduml
