## 创建表结构

|日期|作者|备注|
|------|------|------|
|2018-10-19|郭鑫|初稿|
|2019-11-07|郭鑫|修改|

### 指南

#### 概念说明

用于描述关系型数据库表结构和数据库视图的XML配置，配合底层程序生成数据库物理结构、数据库视图和表结构定义数据。
一般一个系统一个宏定义文件，放在资源目录  snsoft\sndata 下面，格式为 DBMacro[系统ID].xml；
一般一个系统一个建表文件，放在资源目录  snsoft\sndata 下面，格式为 CreateDatabase[系统ID].xml；
一个数据库视图文件，放在资源目录  snsoft\sndata 下面，格式为CreateView[系统ID].xml；

#### 使用说明


##### 表结构配置

底层提供了标准的建表宏文件【DBMacro00.xml】，   其他系统表结构配置文件可以继承<include src="sndata/DBMacro00.xml" />该宏文件也可以直接引入底层标准宏。
建表配置文件以db为根节点，子节点包含引入（include）节点、宏（macro）节点、表（table）节点。
常用字段长度必须使用宏定义，方便修改时统一修改。

* 宏配置文件

```xml
<db id="00" title="N9B表结构常量定义" xmlns="http://www.snsoft.com.cn/schema/CreateDatabase" xsi:schemaLocation="http://www.snsoft.com.cn/schema/CreateDatabase http://www.snsoft.com.cn/schema/CreateDatabase.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

	<!-- 商户 -->
	<macro name="SZCUICODE" title="商户" value="32" />
</db>
```

* 表结构配置文件

```xml
<db id="SN-CMC" title="管控中心" groupid="核心底层" xmlns="http://www.snsoft.com.cn/schema/CreateDatabase" xsi:schemaLocation="http://www.snsoft.com.cn/schema/CreateDatabase http://www.snsoft.com.cn/schema/CreateDatabase.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<!-- 引入底层的宏定义文件，必须引入！ -->
    <include src="sndata/DBMacro00.xml"/>
	<!-- 表定义 -->
	<table id="00002" name="comuser" title="商户" datasrcid="UCODECFG"
		storegrp="SNSYSDATA_TBS">
		<!-- 字段定义 -->
		<column id="10" name="cuicode" type="VARCHAR(SZCUICODE)" title="商户编码"
			primkey="true" />
		<column id="20" name="cuname" type="VARCHAR(128)" title="商户名称" />
		<!-- 常用字段长度描述必须使用宏定义 -->
		<column id="30" name="vprepare" type="VARCHAR(SZUCODE)" title="创建人" />
		<column id="40" name="predate" type="DATE" title="创建日期" />
		<column id="50" name="modifier" type="VARCHAR(SZUCODE)" title="修改人" />
		<column id="60" name="modifydate" type="DATE" title="修改时间" />
	</table>
</db>
```

* 表结构命名规范

因各数据库对表名，表字段长度限制不一致，其中orcale对表名、字段名、索引名、约束名长度限制都为30，为避免起名麻烦，表名字段名都在15以下。
其中各子系统表名以简写开头加下划线。如，业务平台系统号SN-PLAT，表名为 plat_表名。
字段名因各插件对下划线解析不一致，不要使用下划线。

```
 数据库	表名长度限制		列名长度限制
mysql		64				64
oracle		30				30
db2			128				28
access		64				64
mssql		128				128
```

##### 数据源规范

* 底层数据源

底层规划了三个数据源CONFIG、UCODECFG、DEFAULT

```
CONFIG:配置数据源，配置表建议使用该数据源，如：权限定义、菜单定义、系统选项等。
UCODECFG：码表数据源，字典码表相关表使用，如：部门、人员、岗位。
DEFAULT：无法找到数据源时默认的数据源，应用层程序不要使用。
```

* 应用层数据源

开发各业务模块时，每个系统需要各自独立的数据源。
业务数据源分为读数据源、写数据源。如合同模块号SN-ORDER，则定义数据源：读数据源（ORDER_R）、写数据源（ORDER_W）。

```
	数据类型	数据源
	配置数据	CONFIG
	码表数据	UCODECFG
	业务数据	XXXX_R、XXXX_W
```
##### 表空间规范

* 底层表空间

底层规划了一个表空间SNSYSDATA_TBS

* 应用层表空间

```
	数据类型	表空间
	配置数据	SNSYSDATA_TBS
	码表数据	SNSYSDATA_TBS
	业务数据	[系统ID]_DATA_TBS，如：业务平台系统号SN-PLAT，表空间为SN-PLAT_DATA_TBS
```

##### 数据库视图

视图（View）是从一个或多个表（或视图）导出的表。视图与表（有时为与视图区别，也称表为基本表——Base Table）不同，视图是一个虚表，即视图所对应的数据不进行实际存储，数据库中只存储视图的定义，在对视图的数据进行操作时，系统根据视图的定义去操作与视图相关联的基本表。

* 视图配置文件

注意：创建视图时也需要起个视图名称，这个名称会在表结构定义中生成，避免表名与视图名称重复。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<db id="01_View" title="基础视图"
    xmlns="http://www.snsoft.com.cn/schema/CreateView" xsi:schemaLocation="http://www.snsoft.com.cn/schema/CreateView http://www.snsoft.com.cn/schema/CreateView.xsd"  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

    <view name="userlimgrpview" title="用户授权视图" datasrcid="CONFIG" viewexpr="查询被授权用户">
        <![CDATA[
            SELECT
            	 m.userlimgrpicode,m.usercode,m.remark,m.bedate,m.ledate,m.vprepare,m.predate,m.modifier,m.modifydate
            	 ,g.sheetcode,g.opids ,u.usercode as ucode
            FROM
            	userlimgrp m,userlimgrpg g, userlimgrpu u
            WHERE
				u.userlimgrpicode=m.userlimgrpicode and u.userlimgrpicode=g.userlimgrpicode
			ORDER BY
			u.usercode,m.usercode
        ]]>
    </view>
</db>
```



##### 视图规范

1. 应用产品中多表关联都应该使用视图；
1. 表关联条件应该都有索引；
1. 视图应用场景中的常用条件或过滤条件列应该创建对应的索引或联合索引；


### 手册

#### 生成表结构

* 生成表结构功能菜单位与管控中心->系统工具->[生成数据库](uiinvoke/00/zh_CN/theme0/90.db.CreateDB.html)

1. 创建表结构配置文文件路径：resources/cfg/snsoft/sndata/*.xml
1. 创建表结构配置文请参见对应的Schema文件【[CreateDatabase.xsd](do/BrowseSchema?schema=CreateDatabase.xsd)】；

* 创建表结构配置文件节点说明

```
include：该标签用来引入宏定义文件。例如：<include src="sndata/DBMacro00.xml" />
macro：宏定义标签。包含name、value属性。 name属性需全大写。例如：<macro name="SZBCODE" value="32" />
table:定义具体表结构,包含属性：
	id：  表号，各系统统一分配一个大于20的前缀，如：21XXX，保证同系统表结构排序在一起，表号重复时不会报错。
		表号小于20000时底层判断为缓存表，应用层系统不要使用小于20000的表号，缓存表直接使用 localcache配置。
	name:具体的表名，全小写。
	title:表中文名。
	datasrcid：写数据源，必须指定
	rdatasrcid：读数据源，必须指定。
	storegrp：oracle表空间，一个子系统的业务至少独立使用一个表空间。
	localcache：缓存标识，localcache="1"表示为缓存表，与id<20000为或的关系，应用层需要缓存时配置。
	cachekcols：缓存字段，一个表可以有一个缓存子段也可以有多个，如bcode表，cachekcols="btype,cuicode"，用于缓存清除的条件判断。
	cachekeym：缓存规则，表新增或者修改删除，满足缓存规则时，则根据缓存规则清除缓存数据。如bcode表规则，cachekeym="*btype=*${btype}*,*cuicode=*${cuicode}*"
	expand:拓展字段，为true时，表示该表定义的列为另一个表名相同的表的拓展列，字段会生成到另一个表上，所有表属性，只有表名有意义。

	表结构定义包含三类节点；列定义、表依赖关系、索引：

	column： 表结构中字段定义；
		id:字段id，初始配置是间隔10定义，相关字段尽量排在一起，重复时不会报错。
		name：字段名称，非空。
		title：字段标题,非空。
		type：字段类型，必须存在，非空。
			VARCHAR：可变长字符数据。需要指定具体长度，VARCHAR(16),或适用宏VARCHAR(SZCUICODE)
			INTEGER:整数类型。
			DATE：日期类型。
			CLOB：大文本类型。 一般VARCHAR无法满足字段长度使用该类型。
			BLOB：二进制类型。用于存储二进制文件。
			LONGVARCHAR：不推荐使用，字符型一般使用VARCHAR无法满足时用CLOB。
			SMALLINT：小整数，一般用于标识位，如flag=1 启用，flag=0停用 。
			NUMERIC：数值数据，一般金额字段，指定保留小数位。NUMERIC(18.6)
		primkey：主键标识，指定该字段为主键。
		notnull：非空标识。
		default：指定字段默认值。
	fkeycontraint：表依赖关系
		name：依赖名称，一般：表名_字段名(tbldef_flddef)
		foreignKey:外键字段
		references:依赖表及字段，规则：表名(字段名)tbldef(tblname)。
		cascadeUpdate：主表修改时子表连锁更新、部分数据库不支持，不推荐使用。
		cascadeDelete：主表删除时子表连锁删除、部分数据库不支持，不推荐使用。
	index：索引
		name：索引名称，为保证唯一一般表名+字段或（表名+字段）简写，非空。
		fields：索引字段，多个字段逗号分割，非空。
		unique：是否唯一索引。
		storegrp：表空间，与表一致，一个子系统的业务至少独立使用一个表空间。
```

#### 生成视图

* 生成表结构功能菜单位与管控中心->系统工具->[生成视图](uiinvoke/00/zh_CN/theme0/90.db.CREATEVIEW.html)

1. 创建表结构配置文文件路径：resources/cfg/snsoft/sndata/*.xml
1. 创建表结构配置文请参见对应的Schema文件【[CreateView.xsd](do/BrowseSchema?schema=CreateView.xsd)】；

#### 表结构定义

表结构定义用来存储表结构配置文件中生成的表结构；包含四张表tbldef（表结构定义）,flddef（表字段定义）,idxdef（索引定义）,refdef（表依赖关系定义）；
同时在表结构定义中存储视图配置文件生成的视图。

* 生成表结构功能菜单位与管控中心->系统工具->[表结构定义](uiinvoke/00/zh_CN/theme0/90.TBLDEF.html)

### 实践

### 设计