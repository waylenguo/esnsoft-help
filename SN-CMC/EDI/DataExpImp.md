## 数据导入导出

|日期|作者|备注|
|------|------|------|
|2018-09-25|陈泰林、周鹏辉、聂明笛|初稿|
|2019-11-01|聂明笛|修改|

### 数据导出定义

#### 指南

##### 概念说明

用于关系数据库间数据的导出、导入。【注意】：数据导入时，若包含使用分码的对象未勾选清空分码缓存，需要到【管控中心】--->【系统运维】---> 【系统监控】--->【分码信息查询】中将分码重置，否则可能会出现新增信息主键重复的问题。

##### 使用说明

通过[数据导出定义](uiinvoke/00/zh_CN/theme0/90.DATAEXPORTTTDEF.html)定义需要导出的数据，可配置导出哪些表，哪些列；导入时保留哪些原数据，哪些列；导入、导出前对数据的处理等。

#### 手册

##### 相关界面

* 维护界面：[数据导出定义](uiinvoke/00/zh_CN/theme0/90.DATAEXPORTTTDEF.html)

##### 参数说明

* 数据导出定义：按钮【数据导出】--->导出当前定义的数据并弹出下载压缩文件。

|列|说明|
|------|------|
|编号|导入导出编号|
|名称|导入导出名称|
|导出|数据导出时必须勾选，否则在【数据导出】页面无法选择|
|保存参数|DataArchiveListeners=导出监听类，格式为[snsoft.*.*Listener.new?k=v;snsoft.*.*.Listener.new?k=v]；实现接口{snsoft.dx.tools.DataArchiveListener}；可以修改导出属性；可以修改导出参数；
| |	ImportListeners=导入监听类，格式为[snsoft.*.*Listener.new?k=v;snsoft.*.*.Listener.new?k=v]；实现接口{snsoft.dx.util.DxInsertValueListener}；可以在数据导入前做数据处理|
|数据源ID|定义的数据源|
|宏定义|可以定义子表过滤条件宏，如：按商户导出时，子表过滤条件写【cuicode='${cuicode}'】，此处配置【cuicode=商户编码】即可|

* 导出明细定义：按钮【拷贝其他定义明细】--->拷贝其他导出定义的明细数据至当前定义。

|列|说明|
|------|------|
|序号|导出及导入的顺序；|
|说明|定义的SQL语句进行说明|
|表名|定义的SQL语句表，应该跟主表定义的数据源保持一致，否则会报错|
|列名|定义导出的表的列；【空或\*】：导出所有列；【col1,col2】：指定列用逗号分隔；【*,-col1,-col2】：表示排除列名，多个用逗号分隔|
|条件|导出时过滤条件；支持环境变量替换：%EnvParam(xx)；支持参数替换：%Param(xx)；支持主表宏替换：${xx}|
|子表|关联导出的子表表名|
|删除原记录|在目标库中，导入数据之前先删除满足条件的数据，然后导入数据|
|保留数据列|在目标库中，保留此处SQL语句查询出的数据，其中SQL语句支持宏替换；支持环境变量替换：%EnvParam(xx)；支持参数替换：%Param(xx)；支持主表宏替换：${xx}|
|保留数据行|在目标库中，保留此处SQL语句查询出的数据，其中SQL语句支持宏替换；支持环境变量替换：%EnvParam(xx)；支持参数替换：%Param(xx)；支持主表宏替换：${xx}|
|导入前执行|在目标库中，导入数据之前执行SQl语句其中SQL语句支持宏替换；支持环境变量替换：%EnvParam(xx)；支持参数替换：%Param(xx)；支持主表宏替换：${xx}|

#### 实践

项目正式上线之前，需要维护相关的组织结构数据，可以从测试环境导出，再导入线上数据库；

#### 设计

【类图】

@startuml

Title 数据导入导出类图

DataArchive ..> DataArchiveListener
DataArchive ..> DxInsertValueListener

interface DataArchiveListener {
	+ void beforeBuildArchiveSqls(DataArchiveEvent e);
}

interface DxInsertValueListener {
	+ void dataImpoting(DxInsertValueEvent e);
	+ void tableInserting(DxInsertValueEvent e);
	+ void dataValueInserting(DxInsertValueEvent e);
	+ void dataValueInserted(DxInsertValueEvent e);
	+ void tableInserted(DxInsertValueEvent e);
	+ void allTableInserted(DxInsertValueEvent e);
}

class DataArchive {
	+ ContentOutput createArchiveFile(Map<String,?> parameter, List<ReadDataSetProvider> rsList, Map<String,Object> archiveProperties);
	+ ContentOutput createArchiveFileBySqls(Database db, String sqlA[], Map<String,?> rsProperties[], Map<String,?> parameter,Map<String,Object> archiveProperties);
	+ ContentOutput createArchiveFileBySqls(Map<String,?> parameter);
	+ void n7FormatDataImport(Map<String,?> xparameter);
	+ String importDataQuery(Map<String,?> xparameter);
	+ void importArchiveData(Map<String,?> xparameter);
	+ String[][] listDataGrpNames(Map<String,?> param);
	+ ContentOutput createArchiveFileByGrps(Map<String,?> parameter);
	+ ContentOutput createArchiveFileByUDef(Map<String,Object> parameter);
	+ boolean isN7Format(Object data1[][], Object data2[][]);
	+ boolean isN7Format(java.io.InputStream is);
	+ void buildUDefExportSqls(Database configDB, List<String> vSqls, List<Map<String,Object>> vRsProps, Map<String,?> parameter, Map<String,Object> archiveProperties);
}

@enduml

### 数据导出

#### 指南

##### 使用说明

通过选择[数据导出定义](uiinvoke/00/zh_CN/theme0/90.DATAEXPORTTTDEF.html)中的定义导出相关数据。

#### 手册

##### 相关界面

* 使用界面：[数据导出](uiinvoke/00/zh_CN/theme0/90.EXPDATA8.html)

##### 参数说明

|列|说明|
|------|------|
|导出内容|选择[数据导出定义]中的定义|
|数据日期|暂不支持|
|附加宏|使用键值对即可，可用于替换导出明细表中的过滤条件及SQL语句；与数据导出定义中的宏相比，此处优先级高|

### 数据导出【SQL方式】

#### 指南

##### 概念说明

一般用于临时数据的导出导入。

##### 使用说明

通过[数据导出[SQL方式]](uiinvoke/00/zh_CN/theme0/90.EXPDATA6.html)定义需要导出的数据sql，多条sql用‘;’分隔。

#### 手册

##### 相关界面

* 使用界面：[数据导出[SQL方式]](uiinvoke/00/zh_CN/theme0/90.EXPDATA6.html)

##### 参数说明

|列|说明|
|------|------|
|SQL|数据导出的查询sql，多条使用‘;’分隔|
|保存到文件|保存到服务器工程下指定文件名（默认不填，导出下载到本地）|
|导入时删除|在目标库中，导入数据之前先删除满足条件的数据，然后导入数据|
|数据源|下拉选择导出的数据源|

### 导出数据查询

#### 指南

##### 使用说明

选择已导出成功的压缩文件进行解析，可展现为具体导出的数据。

#### 手册

##### 相关界面

使用界面：[导出数据查询](uiinvoke/00/zh_CN/theme0/90.ConfigBrowser.html)

##### 参数说明

数据文件：成功导出的压缩文件。

### 数据导入

#### 指南

##### 使用说明

根据界面[数据导出](uiinvoke/00/zh_CN/theme0/90.EXPDATA8.html)或者[数据导出[SQL方式]](uiinvoke/00/zh_CN/theme0/90.EXPDATA6.html)中导出到压缩文件，可以在此功能中进行导入。

#### 手册

##### 相关界面

* 使用界面：[数据导入](uiinvoke/00/zh_CN/theme0/90.IMPDATA.html)

##### 参数说明

|列|说明|
|------|------|
|文件1(本地)|选择导入的文件|
|文件2(本地)|选择导入的文件|
|(根据记录数)强制插入数据|如果数据插入出现冲突，强制插入数据，插入前删除|
|使用事务|默认不使用事务，勾选后整个数据源里的数据都使用同一个事务，数据插入满足批量导入记录数，事务提交（阿里云因为按事务进行数据同步，为了提高效率可以勾选）|
|按表事务|启用事务后，默认所有配置使用一个事务，设置此选项按照表进行设置事务，一个表中的数据插入完成，事务提交|
|清空分码缓存|重置分码信息，防止新增数据主码重复报错|
|每次批量导入记录数|跟事务连用，批量提交数据记录数，如果不设置，则是一次性导入，如果数据量太大可能会严重影响效率|
|数据源ID|将配置的数据导入到指定的数据源|

* 导入脚本：
```sql
	/**
	 * <pre>
	 * 所有数据导入前调用，可用参数：
	 * e.readDataSetArchive
	 * e.db
	 * e.progress
	 * </pre>
	 */
	proc dataImpoting(e)
	end proc
	/**<pre>
	 * 表插入，旧数据删除 之前 调用，可用参数：
	 * e.db
	 * e.tableName
	 * e.progress
	 * e.sql
	 * e.forceInsert
	 * e.dataColumns
	 * e.deleteFilter
	 * </pre>
	 */
	proc tableInserting(e)
	end proc
	/**
	 * <pre>
	 * 处理当前行数据，可用参数在{@link #tableInserting(DxInsertValueEvent)}上扩展：
	 * e.data 当前行数据
	 * 
	 * 其中：e.data.clear()可以组织行插入
	 * e.data.put("cuicode","XX")可以强制设置插入值
	 * </pre>
	 * @param e
	 */
	proc dataValueInserting(e)
	end proc
	/**
	 * <pre>
	 * 当前行插入后，可用参数同{@link #dataValueInserting(DxInsertValueEvent)}
	 * </pre>
	 * @param e
	 */
	proc dataValueInserted(e)
	end proc
	/**
	 * <pre>
	 * 没找到调用的地方
	 * </pre>
	 * @param e
	 */
	proc tableInserted(e)
	end proc
	/**
	 * <pre>
	 * 所有表数据库导入完成后（事务中），可用参数：
	 * e.readDataSetArchive
	 * e.db
	 * e.progress
	 * </pre>
	 * @param e
	 */
	proc allTableInserted(e)
	end proc
```

