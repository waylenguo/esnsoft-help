## ETL

|日期|作者|备注|
|------|------|------|
|2020-01-16|吴小海|新增|
|2020-01-16|吕希兵|新增|

### 指南

ETL是英文Extract-Transform-Load的缩写，用来描述将数据从来源端经过抽取、转换、加载至目的端的过程。
N9B底层平台支持ETL相关功能；用户通过ETL数据源定义、ETL数据采集定义、ETL任务定义等一系列相关定义可实现源数据转换目标数据功能；
同时结合平台产品N9B-BI相关功能展示相关采集数据。

#### 概念说明

1. ETL数据源定义

   ETL数据采集程序获取或者生成数据的数据源定义，支持常用关系型数据库(MySql、Oracle、SqlServer)、非关系型数据库(Hbase、MongoDB、ElasticSearch)等;

1. ETL数据集定义

   定义取数的来源，用于给数据采集提供源数据;
   
1. ETL数据采集定义
   
	从【源数据定义】的表中取数据（可聚合），按规则合并后，做一定的运算后存到【目标数据定义】中的目标数据表中
   
1. ETL任务定义
   
   一系列ETL数据采集任务定义归类；N9B平台定时任务定义--【ETL任务执行】定时加载启用状态ETL任务执行任务调用;
   
#### 使用说明

##### 使用步骤
1. ETL数据源定义
1. ETL数据集定义
1. ETL数据采集定义
1. ETL任务定义
1. 启用定时任务定义
   任务号：SN-CMC.ETL.01 任务名称：ETL任务执行 
   需要在定时任务定义维护界面启用该定时任务定义，同时Workspace.xml 配置文件设置 startTimerTask="true"

##### ETL数据源定义

   ETL数据源定义支持2种定义方式：文件定义、界面配置；其中文件定义方式配置路径： cfg\res\etl\source\*.xml
   数据源类型选择关系型数据库(Mysql、SqlServer、Oracle),数据源地址相关配置取值Workspace.xml定义配置文件的账套ID、Workspace Id;
   数据源类型选择非关系型数据库(Hbase、MongoDB、ElasticSearch),数据源地址读取 System.properties一系列KEY定义;
   具体详细配置参考：**【ETL数据源】**
   
   数据源定义：
   <img src='help/SN-CMC/ETL/image/1001.png' />

##### ETL数据集定义

数据集定义：
<img src='help/SN-CMC/ETL/image/1004.png' />
   
字段说明：
```
主表:
源数据表:可辅助输入选择单个表,也可手动录入多表
过滤条件:数据采集时会拼上该过滤条件,且可以写数据采集中的宏(大师默认的宏/自定义宏),采集时会根据根据采集参数替换宏的内容,宏格式为${参数名};例如:year=${yea}
子表:
物理字段:如果表达式为空,则该字段需为真实存在的字段,否则可为自定义字段
排序:如果选择了,则装载的数据会按照该字段进行排序
表达式:数据库操作时,可用于写函数表达式,例如case when,sum,max.....
聚合方式:如果所有字段中存在聚合方式,则该字段会做聚合操作,未定义聚合方式的所有字段则均为分组字段
```
   
##### ETL数据采集定义
    
**源数据定义**

用于从定义的数据源中取数

字段说明：

```
区间：(1:天,2:周,3:旬,4:半月,5:月,6:季度,7:半年,8:年),可用于作为过滤条件的一部分（非必须），同时作为目标数据的存储维度，同区间内重复执行定时任务会覆盖同维度的数据;如果为空时则同一采集数据维度始终相同,任务每次执行时会覆盖原有数据
TAC：支持了多个方法，便于自定义干涉数据处理的过程
	//event.getProcessPM():执行参数对象
	//event.getProcessPM().getStarttime():采集任务开始时间
	//event.getProcessPM().getEndtime:采集任务结束时间
	//event.getProcessPM().getStartdate:数据采集数据开始时间(用于确定采集的数据的范围)
	//event.getProcessPM().getEnddate:数据采集数据结束时间(用于确定采集的数据的范围)
	//event.getProcessPM().setExtParams(map):扩展参数,可用于程序处理期间个扩展口参数传
	//event.getProcessPM().getExtParams()
	//event.getProcessPM().setAutoInsert(boolean xx)	:自动插入,默认为true;为true时,会根据采集定义生成的数据自动入库;为false时,则仅采集数据,不做入库处理,可配合监听对数据进行扩展管理
	//event.getProcessPM().setDeldisable(boolean xx):禁止自动删除,默认为false;为false时,会根据采集区间计算的开始日期和结束日期对数据进行自动删除
	//event.getDsParams():数据集执行参数Map对象
	//所有数据集加载之前操作
	proc loadData(event)
	end proc
	
	//单个数据集数据加载之前操作
	proc loadData(event ,dS)//dS指的是源数据定义的VO
		event.getDsParams().put("xx","xx")//用于添加自定义条件，需要在条件中添加指定的宏
	end proc
	
	//数据计算完成后，可以对每条数据（ReadDataSet）做处理
	//event.dataSet:数据集
	//event.row:当前行号,可根据行号获取当前行数据进行处理(Object[] data = event.dataSet.getRowValues(event.row))
	proc onDataRowCreated(event)
	end proc
	
	//onDataRowCreated之后，rs为数据集对象,是全部数据
	proc onDataCreated(event,rs)
	end proc
	
	proc loadDivideData()//分摊运算时，作为分摊的源数据，返回值为BigDecimal
		return new java.math.BigDecimal(10000)
	end proc
	
	//删除目标表数据之前操作,可用于根据业务逻辑修改删除条件
	proc beforeDataDelete(event,filter)//filter删除后目标数据的条件,默认为startdate=xxx and enddate=xxxx
		//例如:return filter + "sex='1' and age=18"
		return filter
	end proc
	
分级汇总信息：通过《分析模板》工具对本次定义产生的结果集进行数据再次加工;例如分组、分级、求和、求平均等。
```
```
名称：自定义，会用于【目标数据定义】的取数公式标题
源数据地址：选取配置页面【ETL数据源】中配置的数据源地址，作为取数源
源数据表：根据源数据地址，选取该数据源中的表，不建议自行填写，作为取数源表，表必须是存在的
过滤条件：取数过程中的过滤条件，格式为xx="xx" and yy="yy" and zz>=${starttime} and year=${year}
			支持的宏
			大师默认的宏
				CURDATE：当天
				CURDATEP1：前一天
				CURDATEA1：后一天
				CURDATEP30：一个月前
				CURDATEP7：一周前
				CURDATETIME：带时分秒
				CURDATEYEAR：当前年
				CURDATEYEAR2：当前年后两位
				CURDATEMONTH：当前月
				CURDATEMONTH2：两位当前月
				CURDATEDAY：当前日
				CURDATEMONDAYS：当前月的第几天
				PREVDATEYEAR：前一年
				PREVDATEYEARM：前一月的年
				PREVDATEMONTH：前一月
				NEXTDATEMONTH：下一月
			自定义宏
				starttime	采集数据区间开始时间
				endtime	采集数据区间结束时间
				year		采集数据区间开始时间所在年
				month		采集数据区间开始时间所在月
				cuicode	执行人所在商户
			自定义宏是配合取数区间一起使用，会按定时任务的时间计算区间的起止时间，若条件中有starttime,endtime宏，会给赋值，
			同时在TAC中亦可以设置自定义宏
```

```
物理字段：源数据表的实际物理字段
列标题：标识字段
类型：字段的字段类型（支持整数，数值，文本，日期（不带时分秒），时间）
排序：desc降序，asc升序
聚合方式：求和，最小，最大，平均，计数
```

**目标数据定义**

采集的数据索要进行的预算，以及存储的目标表

```
目标数据地址：选取配置页面【ETL数据源】中配置的数据源地址，作为存储数据源
目标数据表：根据源数据地址，选取该数据源中的表，不建议自行填写，作为存储数源表，关系型数据库表必须是存在的
TAC:与源数据定义的TAC是同一个字段，参照
```

```
物理字段：存储到目标表的物理字段
列标题：标识字段
类型：字段的字段类型（支持整数，数值，文本，日期（不带时分秒），时间）
公式：取数公式，分别对应源数据定义中的每个源数据，可以从对应源数据的结果集中取对应列，类似$xxx
	合并公式，以当前【目标数据定义】的结果结算结果，类似$xx-$yy(注意此处字段是【目标数据定义】中的字段，而不是取数公式中的【源数据】字段)
分组：用于标识多个数据源数据进行合并时所使用的关键字。
缩放比例：将目前的结果缩小一定的倍数，若是乘法则配置小数，主要用于单位计算（万元等等）
小数位：分摊保留的小数位，无则默认2位
运算分类：支持升序排名，降序排名，占比，分摊；分摊的数据源取TAC中的loadDivideData方法
运算依据字段：例如计算按销售额排名时，那么销售额字段就属于运算依据字段
运算方式：支持组外同级运算和组内同级运算，以排名举例，组外，是指所有数据一起排名，组内是指同个分组内单独排名
运算树节点：有些运算是需要对数据构造出一个树形结果的。例如组织，那么树节点就是path路径字段
运算级次字段：在树形结果的数据中，根据记录数所在树节点的位置会记录一个级次的数值，用于处理同级排名等运算。例如：在一个集团性质的组织架构中 （集团-公司-事业部-部门）那么集团属于1级，公司属于2级依次类推。
```
    
##### ETL任务定义
   ETL任务定义支持2种定义方式：文件定义、界面配置；其中文件定义方式配置路径： cfg\res\task 
   注：
    (1)只有启用状态ETL任务定义才会装载执行
    (2)ETL任务多个任务之间是并发处理的,单独某个ETL任务可开启多线程处理;多线程数量参考任务定义中的任务明细中采集任务数量;
    (3)ETL多个子任务之间是顺序执行的，按照序号升序排序(序号越低优先执行);多个子任务下的数据采集定义并发执行;
     序号较小子任务下全部数据采集执行完成，才去执行下一个子任务；
    (4)ETL任务孙表如果定义采集开启时间,则第一次按照该开始时间生成到当前时间的任务进行执行；如果未定义则取当前时间进行任务执行;
     该采集时间可以定义为当前服务器时间或者当前服务器时间前的某一个时间;
     数据采集定义会根据定义时间同服务器时间差，以天为单位生成生成多个不同天的子任务分别执行;
    (5)考虑到跨天的问题容易导致数据不准确，具体某天ETL定时任务第一次执行,会去重新装载前一天的数据采集任务到执行;
    (6)具体某任务、某明细、某采集、某天任务都会记录执行日志，日志关键字段：任务ID-明细ID-采集ID-开始日期-结束日期 为关键字；
     明细任务失败互不影响，定时任务定义不会去装载失败任务；可通过ETL任务日志过滤失败任务，重新恢复执行;
     
   具体详细配置参考：**【ETL任务】**
       
   任务定义：
   <img src='help/SN-CMC/ETL/image/1002.png' />
   
   任务明细定义：
   <img src='help/SN-CMC/ETL/image/1003.png' />
   
##### ETL任务日志
   1. ETL任务日志记录采集任务的执行结果,每天都会产生一条日志任务;下次执行时采集任务会从任务日志表中获取上次最新执行时间,从最新时间向后记录执行任务;
   2. 失败任务可根据维护工具重新执行;
   3. [清除数据],根据选择的日期删除日期之前的所有任务历史日志;
   4. [恢复失败任务]:指重新执行选中的任务中执行失败的任务;
   5. [恢复失败主任务]:指重新执行选中任务对应的主任务.
   	
   
   <img src='help/SN-CMC/ETL/image/1010.png' />

### 手册

#### ETL数据源

1. 数据源地址配置

 (1)关系型数据库
 
   配置文件：WorkSpace.xml
    
    ```xml
    <workspace id="10" title="N9B(本地mysql)" startTimerTask="false" >
        <datasource id="TEST_MYSQL" host="127.0.0.1" port="3307" database="n9b_wxh" user="root" password="123456" type="4" serverTimezone="GMT+8"/>
        <datasource id="TEST_ORACLE" host="127.0.0.1" port="1521" database="test2" user="root" password="123456" type="2" />
        <datasource id="TEST_SQLSERVER" host="127.0.0.1" port="1433" database="test3" user="root" password="123456" type="1" />	
    </workspace>
    ```
   
   注：此配置包含 mysql、sqlserver、oracle配置
    
 (2)非关型数据库
 
   配置文件：System.properties
    
   mongodb:
    
    ```
    SN-ACS.Client.mongo=true
    SN-ACS.Client.mongo.uri=mongodb://127.0.0.1:27017/acs
    
    snsoft.mongo=true
    snsoft.mongo.uri=mongodb://127.0.0.1:27017/n10log
    
    ```
   
  Hbase:
   暂不支持

  ElasticSearch:
  
  ```
   snsoft.es=true
   snsoft.es.nodes=10.77.49.17:9300,10.77.49.18:9300,10.77.49.19:9300
   snsoft.es.cluster.name=esnsoft-es

  ```
 
 (2)其他说明
 以上配置完成够需重启服务，才可以在数据源定义维护界面选择到具体数据源类型数据源地址

1. 数据源配置DEMO
  
  ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ETLSource xmlns="http://www.snsoft.com.cn/schema/ETLSource"
               xsi:schemaLocation="http://www.snsoft.com.cn/schema/ETLSource http://www.snsoft.com.cn/schema/ETLSource.xsd"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <SId>M0001</SId>
        <Status>70</Status>
        <SName>HELP数据源DEMO</SName>
        <Stype>10</Stype>
        <SUrl>00:HELP</SUrl>
        <Remark>HELP数据源</Remark>
    </ETLSource>
  ```
  配置路径：resources\cfg\res\etl\source\M0001.xml
  详细配置参数参考 [ETLSource.xsd](http://www.snsoft.com.cn/schema/ETLSource.xsd)
  
#### ETL数据采集
配置DEMO：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ETLDacq xmlns="http://www.snsoft.com.cn/schema/ETLDacq"
         xsi:schemaLocation="http://www.snsoft.com.cn/schema/ETLDacq http://www.snsoft.com.cn/schema/ETLDacq.xsd"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Dacqid>D000001</Dacqid>
    <Status>70</Status>
    <DacqName>MySQL->MySQL</DacqName>
    <Sid>M0001</Sid>
    <Stable>etl_demo</Stable>
    <PeriodMode>2</PeriodMode>
    <Tac>
    	<![CDATA[
    		proc loadData(event ,reportDS)
				//sid= reportDS.getSid()    //数据源编号
				//if sid=="M0001"
			    //	busibcodeFilter = "sheetcode = 'SN-HELP.Salorder'"
			  	//	event.getDsParams().put("busibcodeFilter",busibcodeFilter)
			  	//end if
			
			end proc
			proc loadDivideData()
				return new java.math.BigDecimal(10000)
			end proc
    	]]>
    </Tac>
    <Remark>测试DEMO</Remark>

    <ETLDacqg>
        <Idx>10</Idx>
        <FldName>dcode</FldName>
        <ColTitle>币种</ColTitle>
        <SqlType>12</SqlType>
        <Formula1>$fcode</Formula1>
        <Flags>16</Flags>
    </ETLDacqg>
    <ETLDacqg>
        <Idx>20</Idx>
        <FldName>dsum</FldName>
        <ColTitle>总金额</ColTitle>
        <SqlType>2</SqlType>
        <Formula1>$fcy</Formula1>
    </ETLDacqg>
    <ETLDacqg>
        <Idx>30</Idx>
        <FldName>dsum2</FldName>
        <ColTitle>总金额2</ColTitle>
        <SqlType>2</SqlType>
        <Formula1>$fee</Formula1>
    </ETLDacqg>
    <ETLDacqg>
        <Idx>40</Idx>
        <FldName>dsum3</FldName>
        <ColTitle>总金额3</ColTitle>
        <SqlType>2</SqlType>
        <Formula>$dsum2-$dsum</Formula>
        <DivNumber>10</DivNumber>
    </ETLDacqg>
    <ETLDacqg>
        <Idx>50</Idx>
        <FldName>dsort</FldName>
        <ColTitle>排名</ColTitle>
        <SqlType>4</SqlType>
        <CalcType>15</CalcType>
        <RefColumn>dsum3</RefColumn>
        <CalType>0</CalType>
    </ETLDacqg>
    <ETLDacqg>
        <Idx>60</Idx>
        <FldName>ddiv</FldName>
        <ColTitle>分摊</ColTitle>
        <SqlType>2</SqlType>
        <CalsCale>4</CalsCale>
        <CalcType>30</CalcType>
        <RefColumn>dsum3</RefColumn>
        <CalType>0</CalType>
    </ETLDacqg>

    <ETLDs>
        <Idx>10</Idx>
        <DsName>销售合同DEMO</DsName>
        <Sid>M0001</Sid>
        <Stable>plat_salorder</Stable>
        <Sfilter><![CDATA[
        	status='10' and odate <= ${endtime}
        ]]></Sfilter>
        <ETLDsg>
            <ColName>fcode</ColName>
            <Idx>10</Idx>
            <ColTitle>币种</ColTitle>
            <SqlType>12</SqlType>
            <SortType>asc</SortType>
        </ETLDsg>
        <ETLDsg>
            <ColName>fcy</ColName>
            <Idx>20</Idx>
            <ColTitle>原币金额</ColTitle>
            <SqlType>2</SqlType>
            <AggType>2</AggType>
        </ETLDsg>
        <ETLDsg>
            <ColName>rmb</ColName>
            <Idx>30</Idx>
            <ColTitle>本币金额</ColTitle>
            <SqlType>2</SqlType>
            <AggType>2</AggType>
        </ETLDsg>
        <ETLDsg>
            <ColName>fee</ColName>
            <Idx>40</Idx>
            <ColTitle>合同费用</ColTitle>
            <SqlType>2</SqlType>
            <AggType>2</AggType>
        </ETLDsg>
    </ETLDs>
</ETLDacq>
```

#### ETL任务
配置DEMO：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ETLTask xmlns="http://www.snsoft.com.cn/schema/ETLTask"
         xsi:schemaLocation="http://www.snsoft.com.cn/schema/ETLTask http://www.snsoft.com.cn/schema/ETLTask.xsd"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <TaskId>T000001</TaskId>
    <Status>70</Status>
    <TaskName>ETL-DEMO</TaskName>
    <ThreadNum>5</ThreadNum>
    <Remark>测试DEMO</Remark>

    <ETLTaskg>
        <Idx>10</Idx>
        <TaskgName>测试子表DEMO-01</TaskgName>
        <ETLTaskgDacq>
            <Idx>10</Idx>
            <DacqId>D000001</DacqId>
            <Params1>2020-01-13</Params1>
        </ETLTaskgDacq>
        <ETLTaskgDacq>
            <Idx>20</Idx>
            <DacqId>D000002</DacqId>
            <Params1>2020-01-13</Params1>
        </ETLTaskgDacq>
        <ETLTaskgDacq>
            <Idx>30</Idx>
            <DacqId>D000003</DacqId>
            <Params1>2020-01-13</Params1>
        </ETLTaskgDacq>
        <ETLTaskgDacq>
            <Idx>40</Idx>
            <DacqId>D000004</DacqId>
            <Params1>2020-01-13</Params1>
        </ETLTaskgDacq>
    </ETLTaskg>
</ETLTask>

```
详细配置参数参考 [ETLTask.xsd](http://www.snsoft.com.cn/schema/ETLTask.xsd)

#### TAC工具

[TAC](help.html?helpFile=help/SN-CMC/Tac/Exectac.md)

### 实践

1. [ETL数据源定义](uiinvoke/00/zh_CN/theme0/90.ETL.Source.html)
1. [ETL数据采集定义](uiinvoke/00/zh_CN/theme0/90.ETL.Dacq.html)
1. [ETL任务定义](uiinvoke/00/zh_CN/theme0/90.ETL.Task.html)
1. [ETL任务日志](uiinvoke/00/zh_CN/theme0/90.ETL.TaskLog.html)
1. [定时任务定义](uiinvoke/00/zh_CN/theme0/90.Tool.TimerTask.html)

### 设计