## 构建动态列

|日期|作者|备注|
|------|------|------|
|2019-4-19|郭鑫|初稿|
|2019-11-07|郭鑫|修改|
|2020-07-03|张东|修改|

### 指南

#### 概念说明

N9B中存在一些动态列的构造。包含动态新增列，动态设置列属性、码表；
现底层提供统一的动态列实现接口，业务层需要根据需求实现其中个别方法即可。

#### 使用说明

#####  实现动态列接口

* <font color='red'>snsoft.api.bas.IBuildColumn</font>

<font color='red'>接口定义了常用的基础列属性，可以实现接口以扩展属性。</font>
<font color='red'>动态列定义功能必须继承此接口。</font>

<font color='red'>实现类必须实现方法loadExtJsonMap()，该方法返回需要返回到客户端的扩展属性的Map结构，并同意放到客户端对象BuildColumn.cellExtAttrs中，后续继承客户端监听处理扩展属性时，从cellExtAttrs中获取。</font>

##### 默认抽象类

* snsoft.api.bas.BuildColumn

    此抽象类作为动态码表基础实现。

* 实现参考：snsoft.dx.data.entity.Datalimflds

##### 列信息封装

* snsoft.bas.sheet.column.BuildColumnUIListener$ColumnInfoObj&lt;I extends IBuildColumn&gt;

    构造动态列所需要的信息封装类，返回至客户端的对象结构，默认包含构造列信息集合，如果有其他信息可继承重写；

    默认属性：
    ```java
    //元数据
    private final List<I> columnInfos;
    ```
    继承重写参考：snsoft.cmc.lim.DatalimsUIListener$DatalimmColumnInfos

##### 继承UI监听

* snsoft.bas.sheet.column.BuildColumnUIListener

* 参数:

    ```properties
    /**
     * 加载选项：initcomp/dataloaded；
     * initcomp：在initComponent时加载
     * dataloaded：在OnDataloaded时加载
     * 为空时默认按需发远程加载
     * 默认为空
     * 示例：onInitOpt=initcomp
     */
    onInitOpt;
    /**
     * 控制列明（多个,分割）
     */
    activeCols;
    /**
     * 构造列信息查询参数
     * 示例：fixLoadParams='type':'01','code':2,'name':{'n1':'r1','n2':'r2'}
     */
    fixLoadParams;
    ```

* 需要重写的方法：

    ```java
    //根据加载时机选择如下方法重写：
  
    /**
     * 加载构造列信息
     * 组件初始化加载构造列信息时需重写的方法
     */
    protected List<T> loadColInfoOnInit(Map<String,Object> loadParams)

    /**
     * 加载构造列信息
     * 数据加载完成后加载构造列信息时需重写的方法
     */
    protected Map<String,ColumnInfoObj<?>> loadColInfoOnDataLoaded(UIEvent e, ArrayReadDataSet ars)
    ```
    
* <font color='red'>码表:</font>
    <font color='red'>码表初始化在列信息构建的时候。</font>

实现参考：snsoft.cmc.lim.DatalimsUIListener

#####  继承JS监听

* 基础父类：snsoft.sheet.busi.column.BuildColumnJSListener

    该监听默认设置列的基本属性，不实现列的新增；

* 公共参数:

    ```properties
    //构造列素在表表名
  	activeTblname;
  	//控制列明（多个,分割） 默认赋值UI监听的activeCols
  	activeCols;
    /*
	 * 固定参数，默认赋值UI监听的fixLoadParams
	 * 示例：fixLoadParams={type:'01',code:'02'}
	 */
	fixLoadParams;
    ```
  
* Grid表相关参数

    ```properties
    //在此角标位置之前插入列，不为空时动态增加列。
    beforeIndex;
    ```

* Record表相关参数
    
    ```properties
    //构造区域名称 默认uicolumn
    layoutGrpName;
    ```

* 方法：

    ```java
    //核心流程方法
    protected void reBuildTableColumns()//触发构造列
    protected void beforeBuildColumns(ColumnInfoObj columnInfoObj)//构建所有TableColumn前调用
    protected void buildColumns()//构造列信息（基础属性）
          protected void beforeBuildColumn(BuildColumn columnInfo)//构建TableColumn前调用
          protected String getFldname(String fldname)//列名处理 可返回多个
          protected TableColumn getTableColumn(String fldname, BuildColumn fld)//构建TableColumn，默认从构造列所在表中获取
          protected void applyTCAttrs(TableColumn c, BuildColumn columnInfo)//设置列信息
                  protected String getColumnInfoIgnoreAttrs(TableColumn c, BuildColumn columnInfo)//获取忽略的属性
                  protected void updateColumnAidinput(BuildColumn columnInfo, TableColumn c)//更新码表相关信息
          protected void afterBuildColumn(String fldname, TableColumn c)//构建TableColumn后调用
    protected void afterBuildColumns(ColumnInfoObj columnInfoObj)//构建所有TableColumn后调用
    //其他方法
    protected void reSetTableColumnInfos(String fldName, TableColumn[] tcs)//重新设置构造列信息
    protected BuildColumn getCurColumnInfo(String fldName)//从正在使用的构造列信息中获取指定名称构造信息
    ```

* 实现参考：snsoft.cmc.lim.datalim.DataLimsCliListener

### 手册

### 实践

参考界面[岗位操作权限](uiinvoke/00/zh_CN/theme0/90.lim.LimitForGwcode.html)
该功能是通过[操作权限配置](uiinvoke/00/zh_CN/theme0/90.lim.DataLimitDef.html)中的字段属性定义，来动态新增列并设置列属性、码表；


### 设计


**时序图**

@startuml
Title 动态列构造时序图 （initComponent）
actor 客户端
participant client as "snsoft.sheet.busi.column.BuildColumnJSListener"
participant server1 as "snsoft.bas.sheet.column.BuildColumnUIListener"


客户端 -> server1 : 页面加载，初始化，initComponent(), 
activate server1

client --> server1 : onInitOpt=initcomp：加载动态列定义 loadColInfoOnInit：加载动态列定义
activate client

client --> client : 按时机触发构造列：reBuildTableColumns()
activate client

client --> 客户端 : 展示构建好的动态列
deactivate 客户端

@enduml


@startuml
Title 动态列构造时序图 （onDataLoaded）
actor 客户端
participant client as "snsoft.sheet.busi.column.BuildColumnJSListener"
participant server1 as "snsoft.bas.sheet.column.BuildColumnUIListener"


客户端 -> server1 : 数据加载，onDataLoaded(), 
activate server1

client --> server1 : onInitOpt=dataloaded：加载动态列定义 loadColInfoOnDataLoaded：加载动态列定义
activate client

client --> client : 按时机触发构造列：reBuildTableColumns()
activate client

client --> 客户端 : 展示构建好的动态列
deactivate 客户端

@enduml




