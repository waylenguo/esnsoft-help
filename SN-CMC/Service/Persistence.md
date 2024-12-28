## 持久化

### 设计规范

1. 内码列名：~icode，外码列名：~code；
1. 对应的核销列名：~icodex、~codex（如果存在）；
1. 列大小：尽可能使用标准宏，如SZIBILL、SZNBILL等；
1. 主表必须存在列：创建人、创建时间、修改人、修改时间；
1. 索引：根据不同的查询需求场景对不同的列创建索引；
	1. 创建时间、业务日期；
	1. 内外码、核销码、关联关系列；
1. 配置表应该创建外键约束，且数据源ID应该是CONFIG，表空间SNSYSDATA_TBS，索引空间SNSYSINDX_TBS；
1. 所有的名称(表、列、外键、索引、视图等)都<font color="red">必须小写</font>；
<b>注意：索引列应该是非空固定列！</b>

```xml
<table id="32000" name="xx_yyyyy" title="主表" datasrcid="XXXWRITE" rdatasrcid="XXXREAD" storegrp="SNXXXDATA_TBS">
	<column name="~icode" title="内码" type="VARCHAR(SZIBILL)" primkey="true" />
	<column name="~code" title="外码" type="VARCHAR(SZNBILL)" notnull="true" />
	<column name="~icodex" title="内码核销" type="VARCHAR(SZIBILL)" notnull="true" />
	<column name="~codex" title="外码核销" type="VARCHAR(SZNBILL)" notnull="true" />
	...
	<column name="vprepare" title="创建人" type="VARCHAR(SZUCODE)" />
	<column name="predate" title="创建时间" type="DATE" />
	<column name="modifier" title="修改人" type="VARCHAR(SZUCODE)" />
	<column name="modifydate" title="修改时间" type="DATE" />
	...
	<index name="xx_yyyyy_code" fields="~code" storegrp="SNXXXINDX_TBS"/>
	<index name="xx_yyyyy_icodex" fields="~icodex" storegrp="SNXXXINDX_TBS"/>
	<index name="xx_yyyyy_codex" fields="~codex" storegrp="SNXXXINDX_TBS"/>
	<index name="xx_yyyyy_predate" fields="predate" storegrp="SNXXXINDX_TBS"/>
</table>

<table id="32001" name="xx_yyyyyg" title="子表" datasrcid="XXXWRITE" rdatasrcid="XXXREAD" storegrp="SNXXXDATA_TBS">
	<column name="~icode" title="外键" type="VARCHAR(SZIBILL)" notnull="true" />
	<column name="~gicode" title="主键" type="VARCHAR(SZIBILL)" primkey="true" />
	<column name="~gicodex" title="主键核销" type="VARCHAR(SZIBILL)" notnull="true" />
	...
	<index name="xx_yyyyyg_icode" fields="~icode" storegrp="SNXXXINDX_TBS"/>
	<index name="xx_yyyyyg_gicodex" fields="~gicodex" storegrp="SNXXXINDX_TBS"/>
</table>
```

### 表达式

#### 表达式指南

1. 用于解析及在不同的数据库中进行转换；

#### 表达式手册

##### 条件表达式

|类别|对象|条件|
|--|--|--|
|常量|||
||`SqlExpr.constExpr("B01")`|`'B01'`|
||`SqlExpr.constExpr(1)`|`1`|
||`SqlExpr.constExpr(toDate(2019,10,10))`|oracle:`to_date('2019-10-10','yyyy-mm-dd')`<br/>sqlserver:`convert(datetime,'2019-10-10',20)`<br/>mysql:`str_to_date('2019-10-10','%Y-%m-%d')`|
|比较|仅以文本为例||
|等于|`SqlExpr.columnEqValue("col","B01")`|`col='B01'`|
|不等于|`SqlExpr.columnNotEqValue("col","B01")`|`col<>'B01'`|
|大于|`SqlExpr.columnOpValue(GT,"col","B01")`|`col>'B01'`|
|大于等于|`SqlExpr.columnOpValue(GE,"col","B01")`|`col>='B01'`|
|小于|`SqlExpr.columnOpValue(LT,"col","B01")`|`col<'B01'`|
|小于等于|`SqlExpr.columnOpValue(LE,"col","B01")`|`col<='B01'`|
|匹配|`SqlExpr.columnOpValue(LIKE,"col","B01%")`|`col like 'B01%'`|
|不匹配|`SqlExpr.columnOpValue(NOTLIKE,"col","B01%")`|`col not like 'B01%'`|
|空值|`SqlExpr.isnull(new SqlExpr("col"))`|`col is null`|
|非空值|`SqlExpr.isnotnull(new SqlExpr("col"))`|`col is not null`|
|区间|`SqlExpr.columnBetweenValue("col","a","z")`|`col between 'a' and 'z'`|
|非区间|`SqlExpr.columnBetweenNValue("col","a","z")`|`col not between 'a' and 'z'`|
|in|`SqlExpr.columnInValues("col",toArray("a","b"))`|`col in ('a','b')`|
|not in|`SqlExpr.columnNotInValues("col",toArray("a","b"))`|`col not in ('a','b')`|
|<b>not等于</b>|`SqlExpr.columnEqValue("col","B01").not()`|`col<>'B01'`|
|<b>not不等于</b>|`SqlExpr.columnNotEqValue("col","B01").not()`|`col='B01'`|
|<b>and</b>|`SqlExpr.columnEqValue("c1","B01").and(SqlExpr.columnEqValue("c2","B02"))`|`c1='B01' and c2='B02'`|
|<b>or</b>|`SqlExpr.columnEqValue("c1","B01").or(SqlExpr.columnEqValue("c2","B02"))`|`c1='B01' or c2='B02'`|

**注意：任何条件表达式.not()以后表示相反的意思**

##### 查询

```java
// 简单
String sql = "select wcode from users where usercode='U01'";
SqlExpr[] select = new SqlExpr[]{SqlExpr.id("wcode")};
SqlExpr where = SqlExpr.columnEqValue("usercode","U01");
SqlSelectStmt stmt = new SqlSelectStmt(select,"users",where);
// 关联
// 集合
```

##### 新增

##### 删除

##### 修改

#### 表达式设计

**类图**


@startuml

SqlInValues <-- SqlInConstValues
SqlInValues <-- SqlInConstValues2
SqlInValues <-- SqlSelectStmtA

SqlExpression <|.. SqlExpr
SqlExpr ..> SqlInValues:in
SqlExpr ..> SqlSelectStmtA:多行函数(all,any)

SqlStmt <-- SqlSelectStmtA
SqlSelectStmtA <-- SqlSelectStmt
SqlFromTable <.left. SqlSelectStmt
SqlSelectStmtA <-- SqlUnionSelectStmt

SqlStmt <-- SqlInsertStmt
SqlStmt <-- SqlDeleteStmt
SqlStmt <-- SqlUpdateStmt

interface SqlExpression {
	// 常量，在API中使用
	+ SqlExpression and(SqlExpression expr); // 与上另一个条件
	+ SqlExpression or(SqlExpression expr); // 或上另一个条件
}

class SqlExpr {
	// 表达式
	+ Object constValue;
	+ int op;
	+ SqlExpr[] sub;
	+ SqlInValues inValues;
	+ SqlExpr reduce();// 删除重复过滤
	+ SqlExpr not();// 取反
}

class SqlInValues {
	// in值表达式
	+ boolean valueIn(Object value, java.util.Map<?,?> idValues);
}

class SqlInConstValues {
	// 单列in
	+ Object[] values;
}

class SqlInConstValues2 {
	// 多列in
	+ Object[][] values;
}

class SqlStmt {
	+ SqlExpr getWhere();
	+ String[] getTables();
}

class SqlSelectStmtA {
	# SqlSelectStmtA reduceWhere();
}

class SqlSelectStmt {
	+ SqlExpr[] sqlSelect;
	+ SqlFromTable[] fromTables;
	+ SqlExpr where;
	+ SqlExpr[] groupby;
	+ SqlExpr having;
	+ SqlExpr[] orderby;
}

class SqlUnionSelectStmt {
	+ SqlSelectStmt[] selectStmts;
}

class SqlFromTable {
	+ String table;
	+ SqlStmt src;
	+ SqlExpr on;
	+ String[] nameLst;
}

class SqlInsertStmt {
	+ String    table;
	+ String[]  columns;
	+ SqlExpr[] values;
}

class SqlDeleteStmt {
	+ String table;
	+ SqlExpr where;
}

class SqlUpdateStmt {
	+ String    table;
	+ SqlExpr[] setExprs;
	+ SqlExpr   where;
}
@enduml

### 查询参数对象

#### 查询参数指南

* 用于界面查询服务与参数面板中的参数、界面配置的查询列、过滤条件、排序列、分页信息等进行对应；
* 功能界面参见[查询参数Demo](uiinvoke/00/zh_CN/theme0/SN-HELP.Ord.QueryParamsDemo.html)；

#### 查询参数手册

##### SqlColumn

提示：SqlColumn注解 与 QueryColumn类对应，可以通过如下Tac进行测试：
```
qcs = new snsoft.api.sql.QueryColumn[1]
op = snsoft.api.sql.SqlExpression.EQ
qcs[0] = new snsoft.api.sql.QueryColumn("col","v")
qcs[0].setOrExpr(snsoft.sql.SqlExpr.columnEqValue("a",1))
f = snsoft.dx.service.QueryFilterBuilder.impl.buildQueryFilter(qcs, null, {})
println(f)
```

```java
/**
 * 物理列名，默认与属性名相同；
 * 多列或匹配，逗号分隔，如【column="col1,col2"】，拼出的过滤条件为【col1=v or col2=v】；
 */
String column();

/**
 * 复合列名，如【columnExpr="','||col||','"】，过滤条件为【','||col||','='v'】（支持方言）；
 * 优先级高于column()；
 */
String columnExpr();

/**
 * 与表达式，如【andExpr="a=1"】，过滤条件为【col=v and a=1】；
 */
String andExpr();

/**
 * 或表达式，如【orExpr="a=1"】，过滤条件为【col=v or a=1】；
 */
String orExpr();

/**
 * 过滤条件比较运算符，默认0：
 * 1、单值：
 *   1）文本：【"v"】=>【col='v'】
 *   2）文本：【"v%"】=>【col like 'v%'】
 *   3）数值：【1】=>【col=1】
 * 2、多值：
 *   1）文本：【["v1","v2"]】=>【col='v1' or col='v2'】
 *   2）文本：【["v1","v2%"]】=>【col='v1' or col like 'v2%'】
 *   3）数值：【[1,2]】=>【col in(1,2)】
 * 
 * 注：可以通过指定sqlop的值【snsoft.api.sql.SqlExpression.*】避免自动转换
 */
int sqlop();

/**
 * 多值分隔符（默认逗号）：物理列存储多值，多值之间的分隔符，此时匹配使用like，如：
 * 物理存储【col="1,2,3"】，查询出含有元素[2]的行：【','||col||',' like '%,2,%'】
 */
String delim();

/**
 * 过滤条件组(默认0，即默认所有列之间为与的关系)：同组与，异组或；
 */
int grp();

/**
 * 过滤条件使用预编译模式：col=?，即
 * 参数有值时：col=?[v]
 * 参数为空时：col=?[null]
 */
boolean precompiled();

/**
 * 参数为空时的过滤条件为：col is null
 * 参数不为空时的过滤条件为：col = [v]
 */
boolean isnull();

/**
 * 列非空过滤条件：
 * 参数为空时的过滤条件为：col is not null
 * 参数非空时的过滤条件为：col = [v]
 */
boolean isnotnull();

/**
 * 不区分大小写，即upper(col) = upper(v)
 */
boolean ignorecase();

/**
 * 多值，如1,2或3#4#5等
 */
boolean multiValues();

/**
 * 时间过滤延时至当天的最后一秒
 */
boolean dateDelay();

/**
 * 整数值不为零时构造过滤条件
 */
boolean notZero();

/**
 * 使用 and 连接多个 (缺省 使用  or )
 */
boolean useAnd();
```

##### BcodeColumn

```java
/**
 * 组织类型，默认01=部门组织；
 */
String btype();

/**
 * 过滤列信息
 */
SqlColumn sqlColumn();
```

##### SubColumn

* 子查询过滤

```java
/**
 * 子表名表
 */
String table();

/**
 * 主表关联列，多个逗号分割
 */
String pJoinColumn();

/**
 * 子表关联列，多个逗号分割，默认与主表列对应相同
 */
String joinColumn();

/**
 * 过滤条件组(默认0，即默认所有列之间为与的关系)：同组与，异组或；
 */
int grp();

/**
 * 过滤列信息
 */
SqlColumn sqlColumn();
```

##### 附加参数

* 附加参数只是界面参数面板上有但是对应参数类中没有的（一般由实施后续根据项目需要而增加）。

```xml
<!--
	附件参数的运算符可以通过界面扩展属性指定
-->
<c name="col" sqltype="12" title="列" xprops.sqlop="!="/>
<!--
	支持的比较运算符：=,!=,<>,<,<=,>,>=,like,in,isnull,isnotnull
-->
```

#### 查询参数设计

**QueryParams转SqlExpr类图**

@startuml

ParamFilterBuilder .down.> FilterGroup

FilterGroup <|.. ColumnGroup
FilterGroup <|.. SubColumnGroup

ColumnGroup .down.> ColumnEntity
SubColumnGroup .down.> ColumnEntity

ColumnEntity <|.. SqlColumnEntity
SqlColumnEntity <|-- BcodeColumnEntity
SqlColumnEntity <|-- SubColumnEntity

SqlColumn <--* BcodeColumn
SqlColumn <--* SubColumn

class QueryParams {
	# String[] queryColumns;
	# SqlExpression	filter;
	# String group;
	# String having;
	# Sort sort;
	# boolean isRead;
	# boolean isCascade;
	- int firstRow;
	- int rowsPerPage;
	- int pageNo;
	- List<QueryColumn>	extQueryParams;
	- Map<String,Integer> columnOps;
	- Map<String,Integer> columnFlags;
	- Map<Object,Object> clientProperties;
}

class ParamFilterBuilder {
	QueryParams			params;
	Map<String,Object>	paramGetter;
	SqlExpr				initFilter;
	+ SqlExpr buildFilter();
	- Map<Integer,Map<Integer,FilterGroup<?>>> getGroupMap();
}

interface FilterGroup<T> {
	void add(T en);
	SqlExpr buildFilter(QueryParams params, Map<String,Object> paramGetter);
}

class ColumnGroup<SqlColumnEntity> {
	- List<ColumnEntity> list;
}

class SubColumnGroup<SubColumnEntity> {
	- Map<String,Map<Integer,List<ColumnEntity>>> mapList;
}

interface ColumnEntity {
	+ Integer getGroup();
	+ Integer getTypeGroup();
	+ SqlExpr buildFilter(QueryParams params, Map<String,Object> paramGetter);
}

class SqlColumnEntity {
	# VOField field;
	# SqlColumn sqlColumn;
}

class BcodeColumnEntity {
	# BcodeColumn bc;
}

class SubColumnEntity {
	# SubColumn sb;
	# String key;
}

interface SqlColumn {
	String column();
	String columnExpr();
	String andExpr();
	String orExpr();
	int sqlop();
	char delim();
	String filterBean();
	int grp();
}

interface BcodeColumn {
	String btype();
	SqlColumn sqlColumn();
}

interface SubColumn {
	String table();
	String pJoinColumn();
	String joinColumn();
	int grp();
	SqlColumn sqlColumn();
}

@enduml

**Database.QueryParams**

@startuml

class Database.QueryParams {
	// 查询列，为空时查询当前VO类中所有注解@Column的属性列
	String[] queryColumns;
	// 文本过滤条件
	String filter;
	// 结构过滤条件
	SqlExpr filterExpr;
	// 用于传递【col=:col】中的col值
	Map<String,Object> paramGetter;
	// 用于按顺序传递【col=?】中的?值
	SqlValue[] paramValues;
	// 分组
	String groupBy;
	// 分组过滤
	String having;
	// 查询排序
	String order;
	// 本次查询的起始行号，从0开始（即skips）
	int firstRow;
	// 本次查询行数，大于0的整数（即limit）
	int maxResults;
	// 是否返回总行数
	boolean queryTotalRow;
	// 如果返回总行数，通过此参数保存总行数（分页查询首次查询时返回此属性值）
	int totalRows;
	FilterValue<?>	afilter;
	// queryColumns中存在当前VO找不到的列，则从辅表中获取
	VOClassInfo.Relation[] cascadeVOs;
}
@enduml

### DB

#### DB指南

1. 用于从数据库获取满足需求的数据或将修改后的数据保存到数据库；
1. 使用原则：
	* **谁new谁关闭**
	* **谁起事务谁提交**
1. 应用层的程序仅可能使用DAO进行数据的读写；

#### DB手册

##### 获取及事务

* 获取并使用

```java
try (Database db = DBUtils.newDatabaseByTable("users", true))
{
    ...
}
// 建议写法
DBUtils.read("users", db->{
	...
	return ...;
});
// 或
DAO.newInstance(XxxVO.class).read(db->{
	...
	return ...;
});
// 注意：UI监听中或Tac代码块中可以直接获取并使用，因为外层会负责统一关闭
// UI监听
Database db = event.getDatabaseByTable("users",true)
...

// Tac代码中
db = getDatabaseByTable("users")
...
```

* 获取并使用事务

```java
// 建议写法
DBUtils.trans("users", db->{
	...
});
// 或
DBUtils.trans(XxxVO.class, (dao,db)->{
	...
});
// 或
DAO.newInstance(XxxVO.class).trans(dao->{
	...
	return ...;
});
```

##### 常用方法

* queryN(N=1,2,3)，用于获取一个值，一行值，多行值

```java
db.queryN("select wcode from users where usercode='xx'");
db.queryN("select wcode from users where usercode=?","xx");// 推荐写法
db.queryN("select wcode from users where usercode=?",new SqlValue("xx"));// 推荐写法
db.queryN("select wcode from users where usercode=:usercode",MapUtils.toMap("usercode","xx"));
```

* query()，用于获取VO数组或链表

```java
db.query(Users.class,"select * from users where limbcode=?",new SqlValue("B001"));
db.query(Users.class,"select * from users where limbcode=:limbcode",MapUtils.toMap("limbcode","B001"));
db.queryList(Users.class,"select * from users where limbcode=?",new SqlValue("B001"));
```

* queryReadDataSet()：用于获取游标式数据集，使用完成后需要关闭

```java
db.queryReadDataSet("select * from users");
db.queryReadDataSet("select * from users where limbcode=?",new SqlValue("B001"));
db.queryReadDataSet("select * from users where limbcode=:limbcode",MapUtils.toMap("limbcode","B001"));
// 使用方式
try(ReadDataSet rs = db.queryReadDataSet())
{
	while(rs.nextRow())
	{
		// 处理当前行数据
	}
}
```

* queryA()：用于获取二位数组数据集

```java
db.queryA("select * from users");
db.queryA("select * from users where limbcode=?",new SqlValue("B001"));
db.queryA("select * from users where limbcode=:limbcode",MapUtils.toMap("limbcode","B001"));
```

* queryCacheableValues/queryCacheableReadDataSet()：以缓存的方式获取二位数组或二位数组数据集（一般升序排序，用于二分查找）

```java
db.queryCacheableValues("select usercode,wcode from users order by 1");
db.queryCacheableValues("select usercode,wcode from users where limbcode=:limbcode order by 1",MapUtils.toMap("limbcode","B001"));
db.queryCacheableReadDataSet("select usercode,wcode from users order by 1");
db.queryCacheableReadDataSet("select usercode,wcode from users where limbcode=:limbcode order by 1",MapUtils.toMap("limbcode","B001"));
```

* 修改数据（**非特殊算法(如数据升级等)不建议使用这种方式，应该使用对应的Service封装的DAO进行数据的写操作**）

```java
// DML
db.executeUpdate("update users set password='xx' where usercode='yy'");
db.executeUpdate("update users set password=? where usercode=?","xx","yy");
db.executeUpdate("update users set password=? where usercode=?",new SqlValue("xx"),new SqlValue("yy"));
db.executeUpdate("update users set password=:password where usercode=:usercode",MapUtils.toMap("password","xx","usercode","yy"));
// 批量修改(增/删/改)
UpdateData updateData = new UpdateData();
updateData.addUpdate("users",MapUtils.toMap("usercode","U01","password","P01"));
updateData.addUpdate("users",MapUtils.toMap("usercode","U02","password","P02"));
...
db.updateData(updateData,true);
```

* 创建临时表

```java
String insql = db.createTmpTable(12).addValues(...).getInnerSql();// 创建单列临时表
//String insql = db.createTmpTable(12,4).addValues(...).getInnerSql();// 创建多列临时表
String sql = "select * from users where wcode in("+insql+")";
User[] users = db.query(User.class,sql,(SqlValue[])null);
...
```

* 获取元数据

```
// 获取表列名
String[] colnames = db.getTableColumnNames(tblname);
// 获取表主键列名
String[] pknames = db.getPrimaryKeys(tblname);
// 获取表列数据结构
DataColumn[] dcs = db.getTableColumns(tblname);
// 获取表索引结构
DataIndex[] indexes = db.getIndexes(tblname);
// 获取对其他表的约束结构
DataConstraint[] exCons = db.getExportedConstraints(tblname);
// 获取对当前表的约束结构
DataConstraint[] imCons = db.getImportedConstraints(tblname);
// 
```

#### DB设计

参见[DB手册](#DB手册)

### DAO

#### DAO指南

1. 用于业务服务对数据库读写数据的封装；
1. 所有业务层的Service都必须使用DAO进行数据的读写，非特殊场景需要特殊算法不应该使用Database；

#### DAO手册

* 获取DAO对象

```java
// 一般使用此方法获取，VO类XX类上必须通过@Table进行注解，且该表名必须存在于当前系统的tbldef中。
DAO dao = DAO.newInstance(XX.class);
// 需要链接的数据库无法通过VO类上指定的表获取，可以通过指定数据源的方式
DAO dao = DAO.newInstance(XX.class,String dsid);
// 默认通过VO类获取表名，可以通过此方法指定获取表名的方式（如版本备份的表名为~_vb等）
DAO dao = DAO.newInstance(XX.class,Function<VOClassInfo,String> getTblname);
```

* 界面操作

```java
// 列表查询：包含参数、条件及分页信息
QueryResults<V> results = dao.queryUI(queryParams);

// 单据详情或子表查询：强制拼主键过滤条件
QueryResults<V> results = dao.queryDetailUI(queryParams);

// 界面存盘
SaveResults results = dao.saveUI(saveParams);
```

* 根据主键查询

```
// 根据ID查询一条记录，并可以指定是否级联将子表及附表一起查询
dao.queryByID(id,cascade)
// 根据ID集合查询N条记录，并可以指定是否级联将子表及附表一起查询
dao.queryListByID(ids,cascade)
// 根据含有主键值的VO对象查询一条记录，并可以指定是否级联将子表及附表一起查询
dao.query(vo,cascade)
```

* 存盘

```java
// 1、新增
dao.insert(vo)

vo.setInsert()
dao.save(vo)
// 2、删除
dao.delete(vo)

vo.setDelete()
dao.save(vo)
// 3、修改(强烈建议明确指定修改的列名)
vo.setStoredColumns(cols);
dao.update(vo)

vo.setUpdate()
dao.save(vo)
```

* 其他

```java
// 判断是否存在
dao.isExist()

// 读
dao.read(db->{
	return ...;
});

dao.read((dao,db)->{
	return ...;
});

// 写
dao.trans(dao->{
	...
});

dao.trans((dao,db)->{
	...
});
```


#### DAO设计

DAO 设计模式上允许重写！

@startuml

DAO <|-- DefaultDAO
DefaultDAO <-- PrototypeDAO

interface DAO {
	// 调用接口
}

class DefaultDAO {
	// 默认实现
}

class PrototypeDAO {
	// Bean原型模式
}
@enduml

### MongoDAO

#### MongoDAO指南

1. 用于业务服务对MongoDB读写数据的封装；
1. 所有业务层的Service都必须使用MongoDAO进行数据的读写；
1. 使用MongoDAO进行操作几乎与关系型数据库一样，不用考虑底层数据库的实现；

#### MongoDAO手册

```java
DAO<MGOrder> dao = DAO.newInstance(MGOrder.class);
```

**<font color="red">整体使用及规范遵循DAO接口，参见[DAO手册](#DAO手册)</font>**

* 本章节着重说明开发规范（整体样例参考[MG订单](uiinvoke/00/zh_CN/theme0/SN-HELP.Ord.MGOrderList.html)）

##### 结构规范

1. 需要在[cfg/sndata/CreateMG[sysid].xml]文件中声明MongoDB中的表与MongoDB数据源的对应关系；
	```xml
	<table name="mgorder" beanname="mongoDatabase" tpltname="mongoTemplate" title="MG订单" clazz="snsoft.plat.demo.ord.std.vo.MGSalorder">
	</table>
	```
1. VO注解使用JPA规范，与关系型数据库基本保持一致：
	* 主表
		1. 除使用@Table注解外，还需要使用@Document进行注解，用于区分关系数据库与MongoDB数据库；
		1. 主表需要设置标准的逻辑主键，并使用@Id进行注解（主键最好不要使用MongoDB的id列，类似于Oracle的伪劣rowid）；
		```java
		@Table(name = "mgorder")
		@Document("mgorder")
		@SheetInfo(MGOrderService.SheetCode)
		@ValidResource("SN-HELP")
		public class MGOrder extends PlatSheetVO
		{
			@Id
			@Column
			@NotNull
			@DefaultValue("Accode:SN-HELP.MGInnerCode")
			private String	ordicode;
			@Valid
			@OneToOne
			@JoinColumn(name = "ordicode")
			private MGOrderExt			ext;
			@Valid
			@OneToMany
			@JoinColumn(name = "ordicode")
			@NotEmpty(groups = SubmitGroup.class)
			private List<MGOrderDetail>	details;
			...
		}
		```
	* 主表辅表（与关系型数据库保持一致）
		1. 需要拷贝主表的主键，并使用@Id进行注解；
		1. 需要设置逻辑表名，用于界面连锁更新使用；
		```java
		@ValidResource("SN-HELP")
		@Table(name = "mgorderext")
		public class MGOrderExt extends VO
		{
			@Id
			@Column
			@DefaultValue("CopyMaster:ordicode")
			private String				ordicode;
			...
		}
		```
	* 子表
		1. 需要拷贝主表的主键，用于多记录界面存盘时建立VO关系；
		1. 需要设置逻辑表名，用于界面查询使用；
		1. 需要设置逻辑主键，该逻辑主键相当于子表数组的下标（建议使用整形）；
		```java
		@Table(name = "mgdetail")
		public class MGOrderDetail extends VO
		{
			@Column
			@DefaultValue("CopyMaster:ordicode")
			private String					ordicode;
			@Id
			@Column
			@DefaultValue("AutoIncPK:1")
			private Integer					gid;
			...
		}
		```
	* 子表辅表：规范参见主表辅表；
	* 孙表
		1. 需要拷贝主表的主键，用于界面查询参数；
		1. 需要拷贝子表的主键，用于界面存盘时与子表建立关系；
		1. 需要设置逻辑表名，用于界面查询使用；
		1. 需要设置逻辑主键，该逻辑主键相当于孙表数组的下标（建议使用整形）；
		```java
		@Table(name = "mgelement")
		@ValidResource("SN-HELP")
		public class MGOrderElement extends VO
		{
			@Column
			@DefaultValue("CopyMaster:ordicode")
			private String				ordicode;
			@Column
			@DefaultValue("CopyMaster:gid")
			private Integer				gid;
			@Id
			@Column
			@DefaultValue("AutoIncPK:1")
			private Integer				pid;
			...
		}
		```

##### 服务规范

整体规范与关系型数据库保持一致，区别仅在**加载非主表**时：

```java
	// 主表加载，同关系数据库
	@Override
	public QueryResults<MGOrder> queryOrder(MGOrderParams params)
	{
		return super.queryDetailUI(params);
	}
	// 加载子表时，应该先加载主表记录，然后从主表记录中获取子表数据
	@Override
	public QueryResults<MGOrderDetail> queryGood(MGOrderParams params)
	{
		params.setQueryColumns(new String[] { "details" });// 指定仅加载子表列
		QueryResults<MGOrder> mainResults = queryOrder(params);
		List<MGOrder> orders = mainResults.getData();
		QueryResults<MGOrderDetail> results = new QueryResults<>();
		if (orders == null || orders.isEmpty())
		{
			return results;
		}
		results.setData(orders.get(0).getDetails());
		return results;
	}
	// 加载孙表时，应该先加载子表数据，然后从子表数据中获取孙表数据
	@Override
	public QueryResults<MGOrderElement> queryElement(MGOrderParams params)
	{
		QueryResults<MGOrderDetail> goodResults = queryGood(params);
		QueryResults<MGOrderElement> elementResults = new QueryResults<>();
		if (goodResults.getData() != null)
		{
			List<MGOrderElement> elements = new ArrayList<>();
			for (MGOrderDetail detail : goodResults.getData())
			{
				elements.addAll(detail.getElements());
			}
			elementResults.setData(elements);
		}
		return elementResults;
	}
```

##### 界面规范

与关系型数据库没有任何区别，按照[结构规范](#结构规范)中规定的结构(认为是关系型数据库表结构)进行界面布局即可。

#### MongoDAO设计

@startuml

DAO <-- MongoDAO
MongoDAO <-- MongoDAOImpl

MongoDAO .right.> MongoUpdater
MongoUpdater *-up-> MongoRecord

MongoDAOImpl .right.> BulkBuilder

interface DAO<V> {

}

interface MongoDAO<V> {
	+ int update(MongoUpdater<V> updater);
}

class MongoUpdater<V> {
	- List<MongoRecord<V>> records;
}

class MongoRecord<V> {
	// 增删改
	- int op;
	- V   record;
}

class MongoDAOImpl<V> {
	+ int count(SqlExpr filter);
	+ boolean isExist(SqlExpr filter);
	+ List<V> queryListByID(Collection<?> keys, boolean isCascade, boolean isRead);
	+ T[] query(Database.QueryParams queryParams, boolean isCascade, boolean isRead);
	+ List<V> queryList(SqlExpr where, boolean isCascade, boolean isRead);
	+ T queryByID(Object key, boolean isCascade, boolean isRead);
	+ T query(T vo, boolean isCascade, boolean isRead);
	+ QueryResults<V> queryUI(QueryParams params);
	+ QueryResults<V> queryDetailUI(QueryParams params);
	+ SaveResults saveUI(SaveParams<V> saveParams);
	+ int update(MongoUpdater<V> updater);
}

class BulkBuilder {
	+ BulkOperations bulkBuild();
	- Document buildInsert(VOClassInfo info, VO record);
	- void buildDelete(VOClassInfo masterInfo, VO record);
	- void buildUpdate(VOClassInfo masterInfo, VO newRecord);
	- void buildUpdate2(VOClassInfo masterInfo, VO newRecord);
}
@enduml

### ESDAO

#### ESDAO指南

1. 用于业务服务对ElasticsearchTemplate读写数据的封装；
1. 所有业务层的Service都必须使用ESDAO进行数据的读写；
1. 使用ESDAO进行操作几乎与关系型数据库一样，不用考虑底层数据库的实现；

#### ESDAO手册

```java
DAO<ESOrder> dao = DAO.newInstance(ESOrder.class);
```

**<font color="red">整体使用及规范遵循DAO接口，参见[DAO手册](#DAO手册)</font>**

* 本章节着重说明开发规范（整体样例参考[全文检索](uiinvoke/00/zh_CN/theme0/SN-HELP.Util.SearchList.html)）

##### 结构规范

1. 需要在[cfg/sndata/CreateES[sysid].xml]文件中声明ES中的表；
	```xml
	<table name="helpsearch" title="全文检索示例" clazz="snsoft.help.search.vo.HelpSearch">
        </table>
	```
1. VO注解使用JPA规范，与关系型数据库基本保持一致：
	* 主表
		1. 除使用@Table注解外，还需要使用@org.springframework.data.elasticsearch.annotations.Document进行注解，用于区分关系数据库与ES数据库；
		1. 主表需要设置标准的逻辑主键，并使用@org.springframework.data.annotation.Id进行注解；
		```java
			@Table(name = mtbl)
            	@org.springframework.data.elasticsearch.annotations.Document(indexName = mtbl, type = "doc")
            	public static class ESOrder extends VO implements ESVar
            	{
            		private static final long         serialVersionUID = -4709652279004361629L;
            		/**
            		 * 业务主键，@Id注解设置在业务主键上
            		 */
            		@org.springframework.data.annotation.Id
            		@Column
            		private              String       mid;
            		@Column
            		private              String       mvar1;
            		@Column
            		private              String       mvar2;
            		@OneToMany
            		@JoinColumn(name = "mid")
            		private              List<ESGood> goods;
            		@OneToOne
            		@JoinColumn(name = "mid")
            		private              ESOrderExt   ext;
            		...
		```
	* 主表辅表（与关系型数据库保持一致）
		1. 需要拷贝主表的主键，并使用@org.springframework.data.annotation.Id进行注解；
		1. 需要设置逻辑表名，用于界面连锁更新使用；
		```java
	    public static class ESOrderExt extends VO implements ESVar
    	{
    		private static final long   serialVersionUID = 4337091326919214069L;
    		@org.springframework.data.annotation.Id
    		@Column
    		@DefaultValue("CopyMaster:mid")
    		private              String mid;
    		@Column
    		private              String mevar1;
    		@Column
    		private              String mevar2;
			...
		}
		```
	* 子表
		1. 需要拷贝主表的主键，用于多记录界面存盘时建立VO关系；
		1. 需要设置逻辑表名，用于界面查询使用；
		1. 需要设置逻辑主键，该逻辑主键相当于子表数组的下标（建议使用整形）；
		```java
		public static class ESGood extends VO implements ESVar
        	{
        		private static final long            serialVersionUID = -3404993929456302727L;
        		@Column
        		@DefaultValue("CopyMaster:mid")
        		private              String          mid;
        		@org.springframework.data.annotation.Id
        		@Column
        		@DefaultValue("AutoIncPK:1")
        		private              Integer         gid;
        		@Column
        		private              String          gvar1;
        		@Column
        		private              String          gvar2;
			...
		}
		```
	* 子表辅表：规范参见主表辅表；

##### 服务规范

整体规范与关系型数据库保持一致，区别仅在**加载非主表**时：

```java
	// 主表加载，同关系数据库
	@Override
	public QueryResults<MGOrder> queryOrder(MGOrderParams params)
	{
		return super.queryDetailUI(params);
	}
	// 加载子表时，应该先加载主表记录，然后从主表记录中获取子表数据
	@Override
	public QueryResults<MGOrderDetail> queryGood(MGOrderParams params)
	{
		params.setQueryColumns(new String[] { "details" });// 指定仅加载子表列
		QueryResults<MGOrder> mainResults = queryOrder(params);
		List<MGOrder> orders = mainResults.getData();
		QueryResults<MGOrderDetail> results = new QueryResults<>();
		if (orders == null || orders.isEmpty())
		{
			return results;
		}
		results.setData(orders.get(0).getDetails());
		return results;
	}
```

##### 界面规范

与关系型数据库没有任何区别，按照[结构规范](#结构规范)中规定的结构(认为是关系型数据库表结构)进行界面布局即可。

#### ESDAO设计

@startuml

DAO <-- ESDAO
ESDAO <-- ESDAOImpl

ESDAO .right.> ESUpdater
ESUpdater *-up-> ESRecord

ESDAOImpl .right.> BulkBuilder

interface DAO<V> {

}

interface ESDAO<V> {
	+ int update(ESUpdater<V> updater);
}

class ESUpdater<V> {
	- List<ESRecord<V>> records;
}

class ESRecord<V> {
	// 增删改
	- int op;
	- V   record;
}

class ESDAOImpl<V> {
	+ int count(SqlExpr filter);
	+ boolean isExist(SqlExpr filter);
	+ List<V> queryListByID(Collection<?> keys, boolean isCascade, boolean isRead);
	+ T[] query(Database.QueryParams queryParams, boolean isCascade, boolean isRead);
	+ List<V> queryList(SqlExpr where, boolean isCascade, boolean isRead);
	+ T queryByID(Object key, boolean isCascade, boolean isRead);
	+ T query(T vo, boolean isCascade, boolean isRead);
	+ QueryResults<V> queryUI(QueryParams params);
	+ QueryResults<V> queryDetailUI(QueryParams params);
	+ SaveResults saveUI(SaveParams<V> saveParams);
	+ int update(ESUpdater<V> updater);
}

class BulkBuilder {
	+ int execute();
	- V buildUpdate(VOClassInfo masterInfo, VO newRecord);
	- V buildUpdate2(VOClassInfo masterInfo, VO newRecord);
}
@enduml


### 账套

#### 账套指南

Workspace.xml是定义南北系统数据源帐套的文件。
其加载顺序为：
    1.先加载snconfig中文件名为 [webContext].Workspace.xml 文件。例如 snsoft.Workspace.xml
    2.加载snconfig中文件名为Workspace.xml文件。
    3.加载classpath中文件名为configs/WorkSpace.xml文件。
当以上方式都无法加载到文件时，程序会抛出异常信息。

```xml
<workspace id="00" title="SQLServer" startTimerTask="false">
	<datasource id="BASDATA" host="127.0.0.1" port="1433" database="all_emp" user="sa" password="snjixl" type="sqlserver" />
	<datasource id="CONFIG" host="127.0.0.1" port="1433" database="all_emp" user="sa" password="snjixl" type="sqlserver" />
	<datasource id="UCODECFG" host="127.0.0.1" port="1433" database="all_emp" user="sa" password="snjixl" type="sqlserver" />
</workspace>
<workspace id="00" title="Oracle" startTimerTask="false">
	<datasource id="BASDATA" host="127.0.0.1" port="1521" database="orcl" user="cu_bas" password="snjixl" type="oracle" charset="utf8" />
	<datasource id="CONFIG" host="127.0.0.1" port="1521" database="orcl" user="cu_cfg" password="snjixl" type="oracle" charset="utf8" />
	<datasource id="UCODECFG" host="127.0.0.1" port="1521" database="orcl" user="cu_ucfg" password="snjixl" type="oracle" charset="utf8" />
</workspace>
<workspace id="00" title="MySQL" startTimerTask="false">
	<datasource id="BASDATA" host="127.0.0.1" port="3306" database="n9b" user="root" password="*" type="mysql" />
	<datasource id="CONFIG" host="127.0.0.1" port="3306" database="n9b" user="root" password="*" type="mysql" />
	<datasource id="UCODECFG" host="127.0.0.1" port="3306" database="n9b" user="root" password="*" type="mysql" />
</workspace>
<workspace id="00_" title="DB2" startTimerTask="false">
	<datasource id="BASDATA" host="127.0.0.1" port="50000" database="ytiiface" user="db2admin" password="snsoft123456." type="db2" charset="utf8" />
	<datasource id="CONFIG" host="127.0.0.1" port="50000" database="ytiiface" user="db2admin" password="snsoft123456." type="db2" charset="utf8" />
	<datasource id="UCODECFG" host="127.0.0.1" port="50000" database="ytiiface" user="db2admin" password="snsoft123456." type="db2" charset="utf8" />
</workspace>
```

#### 账套手册

可以参考[[WorkSpace.xsd](do/BrowseSchema?schema=WorkSpace.xsd)]()

* 账套属性

```
id: 帐套ID，只能使用字母（建议大写字母），数字，下划线定义。在单帐套系统定义时固定使用 00 。
title:帐套描述
visible: true,false .在登录页面帐套选择下拉框中是否可见。
startTimerTask: true,false .
                true:自动启动定时任务
                false:禁用定时任务
default: true,false .
         true:标识当前帐套为默认帐套。在程序中获取默认帐套时，会按一下顺序获取：
            1.取session中绑定的帐套
            2.取帐套ID为'00'的帐套
            3.取default="true"的第一个帐套
limdbsysid：生成数据库的系统号。多个时用','分隔，支持通配符。例如 limdbsysid="00,01,1%"
forceTableSpc :生成表时强制使用的表空间，一般不配置。当客户有要求说系统使用一个统一数据源时才需要配置，否则按南北标准数据源创建表。
forceIndexSpc ：生成索引时强制使用的表空间，一般不配置。功能同上。
CREATEDB.EXCLUDEDS :不生成数据库的数据源，多个时用','分隔。在定义一个外部数据源时往往不需要生成数据库。例如 CREATEDB.EXCLUDEDS="OUTREAD,OUTWRITE"
UI.STHTMLFSPATH：界面静态化文件系统路径，例如 UI.STHTMLFSPATH="file:///snsoft90/web/${wsp}/${lang}/${theme}"
UI.STHTMLPATH：界面静态化文件WEB路径，例如 UI.STHTMLPATH="${wsp}/${lang}/${theme}/${uiid}.html"
UI.STHTMLPATHRULE：界面静态化文件路径的拆分级次。 例如 UI.STHTMLPATHRULE="type=1&levl=3"
                    type:1:根据muiid 级次拆分; 2:根据muiid的MD5级次拆分
                  	levl:拆分几级目录结构，默认为3.
```

* 数据源属性

```
id: 数据源ID 。使用大写字母，数字，下划线定义。
    底层系统使用的数据源：BASDATA,CONFIG,UCODECFG
    BASDATA:底层业务数据源，如临时文件表等。
    CONFIG:配置数据源，用于获取底层配置表，例如：菜单定义、界面定义等等各种定义表。
    UCODECFG：登录信息数据源，用于获取users,wcode,bcode等组织相关表。
    业务系统可依据需求选取合适的数据源使用，同时也建议业务系统优先使用自定义的数据源。
type:数据库类型，常用的数据库类型有：
        1.mysql : 4
        2.oracle : 2
        3.sqlserver : 1
        4.dm : 0xA2
host:数据库host
port:数据库port
database:数据库名
user:数据库user
paswword:数据库密码，支持Base64加密。
        明文配置方法：paswword="123"
        Base64加密配置方法：paswword="`IiEg`"
url:数据库连接url，一般不需要配置，底层会根据数据库type自动拼接url。这里可支持一些特殊的数据库连接配置。
    oracle支持TNS方式:  url="jdbc:oracle:thin:@(DESCRIPTION=
             (LOAD_BALANCE=on)
             (ADDRESS_LIST=
                (ADDRESS=(PROTOCOL=TCP)(HOST=host1) (PORT=1521))
                (ADDRESS=(PROTOCOL=TCP)(HOST=host2) (PORT=1521)))
             (CONNECT_DATA=(SERVICE_NAME=service_name)))"

xflags:数据源标记位
        1：限制SQL工具操作
clusterid:数据库集群id，用于数据库集群缓存更新通知。当前数据源缓存更新时，会根据数据源获取同一集群id的所有数据源做缓存更新通知。

```

#### 账套设计

@startuml

interface WorkSpaceLoader {
	+ WorkSpace[] load(boolean forecReload);
	+ WorkSpace getWorkSpace();
	+ boolean isEmptyDatabase(String wsid);
	+ WorkSpace getWorkspace(String id);
}

interface WorkSpace {
	+ String getWorkspcID();
	+ String getWorkspcTitle();
	+ String getCharset(String dsID);
	+ String getCharsetByTable(String tblname);
	+ String getDataSource(String dsID);
	+ String getDataSourceByTable(String tableName);
	+ String getDsidByTable(String tableName, boolean readOnly);
	+ WorkSpace.DataSource getWorkspcDataSource(String dsID);
}
@enduml