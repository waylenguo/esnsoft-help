## 数据库分片

|日期|作者|备注|
|------|------|------|
|2018-09-25|郭鑫 |初稿|

### 综述

&emsp;&emsp;数据量达到一定规模的应用系统，为满足系统对数据访问的性能，需要使用分布式存储技术。在分布式存储系统中，数据需要分散存储在多台设备上，其中数据分片（Sharding）是分布式存储的关键技术，数据分片通过定义的分片规则来确定数据在多台存储设备上分布。数据分片要达到三个目的：
1. 分布均匀，即每台设备上的数据量要尽可能相近；
2. 负载均衡，即每台设备上的请求量要尽可能相近；
3. 便于扩容，扩容时产生的数据迁移尽可能少；
4. 尽可能减少对业务层开发的侵入；保证应用层SQL书写的兼容性与自由度；

南北SN-N9B开发平台的持久层以三种方式对“数据分片”提供支持：

1: **虚拟服务器方式**

&emsp;&emsp;虚拟服务器是一个逻辑上的存储服务器，通过该服务器来访问多个分片的物理数据库：

 ![数据分片-虚拟服务器方式](images/数据分片-虚拟服务器方式.png)

&emsp;&emsp;虚拟服务器上一般部署一个MyCat、TiDB等第三方的应用，该应用提供兼容的MySQL等数据库访问协议，应用系统通过标准JDBC方式访问逻辑数据库，拦截请求的SQL，解析这些SQL并将请求路由到物理数据库，再将结果合并后返回到应用系统。
优点：由于逻辑数据库提供的是标准的数据库访问协议，应用系统几乎不需要做任何改造。
缺点:
1. 需要增加逻辑上的存储服务器，增加了部署的复杂性。
1. 数据从物理服务器到应用系统，中间经过逻辑服务器会多一次网络请求，对性能、系统整体稳定性都有可能的影响；
1. 当应用系统请求量较大时，逻辑服务器的压力较大，内存要求也较高，需要集群，
1. 对分表、分库等方式的支持依赖于第三方应用，例如mycat就不支持同时分库又分表

2: **虚拟JDBC方式**

&emsp;&emsp;这种访问方式是通过一个虚拟的JDBC，该JDBC通过对请求的SQL进行解析，将请求路由到多个实际物理数据库的JDBC进行访问，再将结果合并后提供给应用系统。这种方式需要在应用系统中提供虚拟JDBC的驱动程序。
&emsp;&emsp;南北SN-N9B中使用当当开源sharding-jdbc作为虚拟JDBC的驱动程序对该方式提供支持，在数据层的连接池、事务等都可以使用sharding-jdbc提供的数据连接。
下图是sharding-jdbc执行流程图:
 ![ShardingJDBC原理](help/SN-HELP/DeveloperGuide/images/ShardingJDBC原理.png)

3: **基于DAO层分片数据存取方式**

&emsp;&emsp;由于南北SN-N9B的持久层使用了自主开发的数据层框架，该框架在JDBC基础上又封装了一层数据库的访问对象(Database)及操作对象（DefaultDAO），应用系统所有DAO对数据库的访问都是基于该对象完成。所以系统在DAO层也可以实现对分片数据的存取。从结构上讲此方式与虚拟JDBC方式基本是一致的，但是对SQL的解析、路由都是在DAO层封装实现的。
&emsp;&emsp;南北SN-N9B 支持分库方式、分表方式或分库又分表方式对分片数据的存取。关键技术点：
1. 数据分片规则的定义与解析：
  分片规则由一个XML配置文件定义。通过该配置文件定义：
	1. 相关表的分片节点；
	1. 数据记录在分片节点上的存取的路由规则；
	1. 表间关系定义；确保 关联的主子表记录在同一个节点上，确保SQL关联查询的正确执行；
	1. 分片策略灵活，可支持等号，between，in等多维度分片
 下面是一个配置文件的例子:

 ```xml
<?xml version="1.0" encoding="UTF-8"?>
<data-partion-rules>
  <datasource id="snsoft80_demo_sharding1">
	 <db-master logicDsName="demo-data" host="127.0.0.1"  database="demo" user="sa" password="sa" type="sqlserver" />
      <db-node id="dn1" host = "127.0.0.1"  database="part01" user="sa" password="sa" type="sqlserver" />
      <db-node id="dn2" host = "127.0.0.1"  database="part02" user="sa" password="sa" type="sqlserver" />
	  <table name="testparttbl" by-column="id" rule="snsoft.dx.dbpart.rulealg.DBPartitionRuleModAlgorithm.new" >
	      <node dsid="dn1" tables="*，*_01"/>
	      <node dsid="dn2" tables="*_02，*_03"/>
	  </table>
  </datasource>
</data-partion-rules>
```

1. SQL的解析与路由：
	（1）基于Yacc技术实现对 SQL 的解析，包括对 数据查询，增、删、改的类型的SQL解析 。
	（2）支持聚合，分组，排序，分页等查询。
	（3）多线程并发方式处理路由后的SQL。
1.  减少对应用层开发的侵入。数据的分片对应用层是透明的，应用层以标准方式编写DAO组件或SQL语句，完全不改变代码的编写方式。


### 配置workspace.xml

* 添加数据分片数据源
```xml
<datasource id="数据源ID" database="数据分片数据源ID" type="sharding"/>
```
* 帐套文件属性介绍

1. id：数据源ID，对应表结构定义中的读写数据源ID，当前帐套内保证唯一。
1. database：数据分片数据源ID，对应ShardingRule.xml（说明见下文）中的datasource数据源id，当前帐套内保证唯一！
1. type：数据库类型，现在支持两种类别
	1. sharding（自主开发数据分片，推荐使用）
	1. ddsharding（当当开源Sharding-JDBC数据分片）。


### 新建ShardingRule.xml文件

在 workspace.xml 同目录下，新建数据分片规则配置文件 ShardingRule.xml 

#### 配置示例
```xml
<?xml version="1.0" encoding="UTF-8"?>  <!-- XML文件头  -->
<data-partion-rules>	<!-- 固定格式，配置起始位置  -->
	<datasource id="数据分片数据源ID">  <!-- id对应workspace.xml中的database  -->
		<!-- 表结构存储对应的数据源定义  -->
		<db-master database="主数据源ID" host = "IP地址" user="用户" password="口令" type="mysql" />
		<!-- 数据分片业务数据存储对应的多个数据源定义  -->
		<db-node id="XXX01" database="数据源ID" host="IP地址" port="3306" user="用户" password="口令" type="4" />
		<db-node id="XXX02" database="数据源ID" host="IP地址" port="3306" user="用户" password="口令" type="4" />
		<table name="逻辑表名，支持多个逗号分割" by-column="分片规则依据字段，支持一个"	db-nodes="XXX01,XXX02" tbl-nodes="*_001,*_002" 
			<!-- 分片规则对象  -->
			rule="snsoft.dx.sharding.rulealg.ShardingRuleSuffixAlgorithm.new?DBIDPos=0&amp;TBLIDPos=3" />
	</datasource>
</data-partion-rules>
```
#### table属性介绍

1. name：逻辑表名，定义数据分片的逻辑表名，支持多个表，通过逗号分割，比如主子孙表可以定义在一起。
1. by-column：分片规则依据字段，用于分片规则判断参数的取值，支持一个字段。
1. db-nodes：使用的数据源列表
1. tbl-nodes：物理存储表列表，*等于name中定义的表名，*_001,*_002 相当于  逻辑表名_001,逻辑表名_002
1. rule:数据分片规则对象


### 分片算法实现
snsoft.dx.sharding.ShardingRoute 数据分片计算接口
snsoft.dx.sharding.impl.ShardingRouteImpl 数据分片计算实现
分片算法不需要应用层开发重实现，配合应用层的分片规则，提供标准的逻辑实现。
主分片依据字段：一般为主键，by-column的定义字段
副分片依据字段：可以是任意查询字段，一般为业务时间、商户编码、部门编码、客户编码、用户编码等。

### 数据分片使用

#### 常用的分片规则

主分片依据字段一般都是主键，并在主键中包含分片的信息。此时需配合对应的主键分码程序，比如： ShardingMakeCodeService，通过`编码生成规则`定义配合VO对象的 `@DefaultValue("SheetAccode:单据号")`实现分码的处理。
主分片依据字段、副分片依据字段， 相关表结构都需要保存此字段，如子表、孙表、关联表、核销表等，即关联表使用同一分片规则，保证内部关联的正确性。

1. 业务数据：时间分库、主键哈希余数分表
主分片依据字段：主键
副分片依据字段：时间字段，一般是制单日期。

1. 商户数据:商户编码区间分库，商户编码哈希+主键哈希余数分表的规整，单个商户数据支撑一个库多张表
主分片依据字段：主键
副分片依据字段：商户编码
同时支持“数据分片配置”，尽量保证数据的分片均衡。
“商户数据分片信息”中保存分片信息。

1. 用户数据:用户编码区间分库，用户编码哈希分表的规整，单个商户数据支撑一个库一张表
主分片依据字段：主键
副分片依据字段：用户编码
同时支持“数据分片配置”，尽量保证数据的分片均衡。
“用户数据分片信息”中保存分片信息。

1. 日期分片数据的主键分配
主分片依据字段：主键
副分片依据字段：时间字段，一般是出报表的时间。

* 其他分片规则

注意：这些实现不能直接用于应用开发，具体分片规则需要根据各自项目的需求分析后编写各自的实现类！！
1. snsoft.dx.sharding.rulealg.ShardingRuleSuffixAlgorithm.new
参数 DBIDPos、TBLIDPos 从by-column的值第几位到第几位确定数据源，值与数据源db-node节点id的结尾一致。
参数 TBLIDPos、TBLIDSize 同上用来确定存储在哪个表，与表的结尾保持一致。

1. snsoft.dx.sharding.rulealg.ShardingRuleModAlgorithm
根据by-column的值哈希取余获取数据源

*** 相关程序 ***
- ShardingMakeCodeService：配合数据分片的主键生成服务
- ShardingRuleService：数据分片规则服务

*** 用于ShardingRule.xml配置的分片算法 ***
继承核心程序：snsoft.dx.sharding.rulealg.AbsShardingRuleAlgorithm
- ComuserShardingRuleAlgorithm：针对商户关系型数据的数据分片算法。
- BusiShardingRuleAlgorithm：针对业务关系型数据的数据分片算法。
- UserShardingRuleAlgorithm：针对用户关系型数据的数据分片算法。
- DayShardingRuleAlgorithm：根据“日”分片处理,“日期”分库 hash分表。

如果有其他算法尽量在底层实现。

#### 编码生成规则

根据实际项目中使用情况，编码生成规则以商户为例说明

* 主表编码规则示例

文件名即编码规则号：HC-MC.MakeComuserInnerCode.xml 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Codelike>
	<name>商户分片数据生成主表主键</name>
	<remark>
	<![CDATA[ 
	]]>
	</remark>
	<codeinfo>
	<![CDATA[ 
	proc  makeCode(refData, count)
		tableName = refData.get("tableName")
		cuicode = refData.get("cuicode")
		makeCode = snsoft.commons.spring.SpringBeanUtils.getBeanByName("SN-CORE.ShardingMakeCodeService")
		array = makeCode.makeComuserInnerCode(tableName,cuicode,count)
		return snsoft.commons.util.ArrayUtils.toStringArray(array)
	end proc
	]]>
	</codeinfo>	
</Codelike>
```

* 子表编码规则示例

文件名即编码规则号：HC-MC.MakeComuserSubInnerCode.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Codelike>
	<name>商户分片数据根据主表内码生成主表主键</name>
	<remark>
	<![CDATA[ 
	]]>
	</remark>
	<codeinfo>
	<![CDATA[ 
	proc  makeCode(refData, count)
		tableName = refData.get("tableName")
		mianinnercode = refData.get("mianinnercode")
		makeCode = snsoft.commons.spring.SpringBeanUtils.getBeanByName("SN-CORE.ShardingMakeCodeService")
		array = makeCode.makeComuserSubInnerCode(tableName,mianinnercode,count)
		return snsoft.commons.util.ArrayUtils.toStringArray(array)
	end proc
	]]>
	</codeinfo>	
</Codelike>
```

#### 单据/编码规则关系

文件名HC-MC.QuotaControl.json，即单据号
```json
[
	{
		"accode": "HC-MC.MakeComuserInnerCode",
		"colname": "id",
		"refcolumns": "cuicode",
		"bcode": null
	},
	{
		"accode": "HC-MC.MakeComuserSubInnerCode",
		"colname": "quotacode",
		"refcolumns": "maininnercode:id",
		"bcode": null
	}
]

```

### 生成数据库

数据分片对应的实际物理表的更新同样使用数据库工具“生成数据库”，与正常生成表结构并没有区别。


