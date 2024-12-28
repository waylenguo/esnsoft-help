## 数据处理定义   

|日期|作者|备注|
|------|------|------|
|2018-11-22|顾中超|初稿|
|2019-08-02|顾中超|需求修改，参见修改需求2019-08-02|

### 指南	
#### 概念说明
数据处理用于调用一个或多个执行程序对现有数据进行修复或者加工处理，可支持编写TAC、SQL、调用JAVA静态方法、JAVA Bean、JAVA多线程处理、执行GROOVY代码。
数据处理分为2中处理类型：
```
1.补丁
   该处理类型的定义一般用于对数据的修复，仅能成功执行一次。
2.处理
   该处理类型的定义可重复执行。
```
#### 使用说明
* 可在直接在界面录入配置：管控中心->开发配置->其他配置->数据处理定义
* 支持xml配置，数据库配置优先于文件配置，文件配置编码以系统号开头+功能编码，例如：SN-HELP.SysPatch
* xml配置路径：cfg/res/syspatch/*.xml
* xml配置参考：SN-HELP.SysPatch.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<SysPatch>
	<!-- 处理编码(非空)-->
	<patchcode>SN-HELP.SysPatch</patchcode>
	<!-- 处理类型(非空)-->
	<patchtype>10</patchtype>
	<!-- 说明(非空)-->
	<explaininfo>处理说明</explaininfo>
	<!-- 处理标识 #1:确认;#2:进度条-->
	<flags>0x01+0x02</flags>
	<!-- 所属系统(非空) -->
	<sysids>SN-HELP</sysids>
	<!-- 开发版本号 -->
	<sysversions></sysversions>
	<!-- 备注 -->
	<remark>备注</remark>
	<!-- 数据处理明细  -->
	<SysPatchDetail>
		<!-- 执行顺序 (非空)-->
		<orderidx>10</orderidx>
		<!-- 说明 (非空)-->
		<explaininfo>TAC执行</explaininfo>
		<!-- 执行方式 (非空)10:TAC;20:JAVA;30:SQL;40:THREADPOOL;50:GROOVY-->
		<executemode>10</executemode>
		<!-- 执行程序 (非空) -->
		<executestr><![CDATA[
			deployer = getBean("SN-CMC.Deployer")
			deployer.clearCache()
			println("TAC执行成功")
		]]></executestr>
		<!-- 服务 -->
		<servflag></servflag>
		<!-- 表名 -->
		<tablename></tablename>
		<!-- 启动事务  -->
		<transflag></transflag>
		<!-- 备注 -->
		<remark></remark>
	</SysPatchDetail>
	<SysPatchDetail>
		<orderidx>20</orderidx>
		<explaininfo>JAVA执行(Bean)</explaininfo>
		<executemode>20</executemode>
		<executestr><![CDATA[SN-CMC.SysPatchService.querySysPathg]]></executestr>
		<servflag>1</servflag>
	</SysPatchDetail>
	<SysPatchDetail>
		<orderidx>25</orderidx>
		<explaininfo>JAVA执行(静态方法)</explaininfo>
		<executemode>20</executemode>
		<executestr><![CDATA[snsoft.cmc.tools.service.impl.SysPatchServiceImpl.test?test=1]]></executestr>
	</SysPatchDetail>
	<SysPatchDetail>
		<orderidx>30</orderidx>
		<explaininfo>SQL执行</explaininfo>
		<executemode>30</executemode>
		<executestr><![CDATA[
			update sys_patch set remark='示例说明' where patchcode='SN-HELP.SysPatch';
			update sys_patchg set remark ='示例说明' where patchcode='SN-HELP.SysPatch'
		]]></executestr>
	</SysPatchDetail>
	<SysPatchDetail>
		<orderidx>40</orderidx>
		<explaininfo>多线程执行</explaininfo>
		<executemode>40</executemode>
		<executestr>
			<!-- 
				示例：snsoft.TaskTest.getDataTest;snsoft.TaskTest.runTest;10;3 
				多线程执行任务，执行程序格式：getMethod;runMethod;nThreads;maxCount;space
				getMethod:获取执行数据方法
				runMethod:线程调用执行任务方法
				nThreads:线程池最大线程数，默认为：10
				maxCount:默认为0，调用getMethod最大次数，当调用getMethod获取数据maxCount次停止执行；maxCount为0时则直到getMethod返回值为空时线程结束
				space:执行时无可有线程时等待时间(毫秒)，默认为10000
			-->
		</executestr>
	</SysPatchDetail>
	<SysPatchDetail>
		<orderidx>50</orderidx>
		<explaininfo>GROOVY代码执行</explaininfo>
		<executemode>50</executemode>
		<executestr><![CDATA[
			println('groovy test');
		]]></executestr>
	</SysPatchDetail>
</SysPatch>
```

#### 修改需求
**2019-08-02**
* 将定义的配置和记录状态执行信息分开，配置同时支持数据库和XML
* 原定义表的子表 sys_patchg	系统数据处理定义子表  主键修改为双主键  patchcode	处理编码    orderidx	执行顺序
* 增加记录状态执行信息的两张表，包括 主表（处理编码、状态、执行次数、最近操作时间），子表（处理编码、执行顺序、状态）
* 增加对接 Jenkins的服务，可根据执行所有未执行过的补丁程序
```
1.执行所有可执行的数据类型为补丁的数据处理：
	SN-CMC.SysPatchService.executePatchType()
2.执行所有可执行的数据处理(补丁和处理):
	SN-CMC.SysPatchService.sysPatchExecute(String patchcode)
	patchcode:处理编码为null时，执行所有可执行的；具体执行则传入编码，多个以逗号分割
```

### 手册

**菜单位置：管控中心->开发配置->其他配置->数据处理定义**
#### 数据处理界面
**如下图：**
![](help/SN-CMC/Tool/SysPatch/img/SysPatch1.png)

**字段说明：**

|字段名|描述|
|------|------|
|确认|当该标识选中时，才能执行该条数据处理|
|处理编码|主键，自动生成，年月日加3位顺序号，定义后禁止修改|
|处理类型|用于区分明细中执行程序能否重复执行|
|说明|对当前记录的描述信息|
|状态|标识当前定义的执行结果|
|进度条|打了该标识，则在执行该处理时会出现进度条显示执行进度信息|


**功能按钮说明：**
1. 数据处理执行
```
用于执行选中的数据处理定义
```
2. 恢复执行
```
用于将已经执行成功的处理定义恢复到未执行状态，一般用于补丁的恢复后再次执行
```

#### 数据处理明细

![](help/SN-CMC/Tool/SysPatch/img/SysPatch2.png)

**字段说明：**

|字段名|描述|
|------|------|
|顺序号|数据处理按照该顺序执行，如：10、20、30... 建议增加间隔方便插入处理，定义后禁止修改|
|说明|对当前执行程序功能的描述信息|
|状态|标识当前执行程序的执行结果|
|执行方式|标识执行程序的类型(TAC、JAVA、SQL、THREADPOOL、GROOVY)|
|执行程序|需要执行的方法或者SQL、脚本|
|服务|打了该标识，标识执行程序调用的是Bean下的一个方法，例如：SN-CMC.SysPatchService.querySysPathg|
|表名|当执行方式为TAC、SQL、GROOVY时，用于通过该表名获取DB信息，当表名为空时则DB与当前表同一数据源|
|启动事务|当执行方式为TAC、SQL、GROOVY时，在执行程序时会开启DB事务|

#### 使用说明
1. TAC
```
执行底层TAC脚本，可使用变量db(表名构建的DB)、sysPatch(处理定义对象)、sysPatchDetail(处理定义明细对象)
示例：
deployer = getBean("SN-CMC.Deployer")
deployer.clearCache()
println("TAC执行成功")
```
2. JAVA
```
执行对应JAVA方法
(1). 静态JAVA方法:类全名.方法名，方法后可跟?传入参数
	   示例：
	  snsoft.cmc.tools.service.impl.SysPatchServiceImpl.test?test=1
(2). Bean的方法(服务标识需设置)：BeanID.方法名，方法后可跟?传入参数
 	   示例：
	  SN-CMC.SysPatchService.querySysPathg
```
3. SQL
```
执行SQL语句，仅可执行更新和删除操作的SQL语句，多个SQL执行以分号(;)分割；如需多个SQL在同一事务中操作，则需要设置启动事务标识；表名用于构建执行SQL的DB
示例：
update sys_patch set remark = "test sql";
update sys_patchg set remark = "test sql"
```
4. THREADPOOL
执行程序：
```
多线程执行任务，执行程序格式：getMethod;runMethod;nThreads;maxCount;space
getMethod:获取执行数据方法
runMethod:线程调用执行任务方法
nThreads:线程池最大线程数，默认为：10
maxCount:默认为0，调用getMethod最大次数，当调用getMethod获取数据maxCount次停止执行；maxCount为0时则直到getMethod返回值为空时线程结束
space:执行时无可有线程时等待时间(毫秒)，默认为10000
示例：
snsoft.TaskTest.getDataTest;snsoft.TaskTest.runTest;10;3
```
代码示例：
```
getMethod：参数为(Map params)或者(Progress progress,Map params)
public static String[] getTaskData(Map params)
{
	//获取需要执行的数据或任务数组对象
	return new String[] { "1", "2", "3" };
}
runMethod:参数为(Map params, Object tast)或者(Map params, Progress progress, Object tast)
public static void runTest(Map params, Object tast)
{
	//任务执行
	System.out.println("执行结果：" + tast);
}
```
5. GROOVY
```
执行GROOVY语法的代码
示例：
println('groovy test');
```
#### 数据处理日志
	用于记录每一次的执行结果，执行失败通过查看该日志进行问题排查。


#### 第三方服务调用

1、 执行所有可执行的处理
- 调用方法：
UIResponse<String> snsoft.cmc.tools.service.SysPatchService.sysPatchExecute(String patchcode)
参数说明：
patchcode：值为空时，执行所有可执行的处理；多个需要处理则以逗号(,)分割

2、 执行所有可执行的补丁
- 调用方法：
UIResponse<String> snsoft.cmc.tools.service.SysPatchService.executePatchType()

3、 jenkins调用示例：
```
调用Controller方法：snsoft.cmc.tools.controller.CMCUIController.runScript
请求地址：http://127.0.0.1:端口/应用名/ui/script/服务ID
请求体(POST)：
service = getBean("SN-CMC.SysPatchService")
service.sysPatchExecute(null)
返回结果：
{
    "status": "success"
}
描述：
status为"success"表示执行成功，其他均为失败
```
可供jenkins调用，实现自动化执行数据处理。

### 实践	
	
### 设计
