## 定时任务

|日期|作者|备注|
|------|------|------|
|2018-09-25|陈泰林 、 周鹏辉 |初稿|
|2019-10-21|陈泰林|修改|

### 指南

#### 概念说明

定时任务是在规定的时间里执行任务，支持数据库和配置文件。

#### 使用说明

1. WorkSpace.xml中对应的账套必须启用定时任务[startTimerTask="true"]
1. 定义配置文件路径：cfg/timertask/TimerTask*.xml
1. 参考：TimerTaskSN-UMC.xml
1. 配置文件请参见对应的Schema文件【[TimerTask.xsd](do/BrowseSchema?schema=TimerTask.xsd)】
```xml
<?xml version="1.0" encoding="UTF-8"?>
<timertask-list xmlns="http://www.snsoft.com.cn/schema/TimerTask" xmlns="http://www.snsoft.com.cn/schema/TimerTask" xsi:schemaLocation="http://www.snsoft.com.cn/schema/TimerTask		http://www.snsoft.com.cn/schema/TimerTask.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<timertask id="10000" name="标准测试java" 
         work-time="00:00-24:00" interval="10"
         auto-start="true"
         thread = "true"
         task-type="java"
		 disabled="false"
		 workspc="00"
        ><task>
            snsoft.help.timertask.service.impl.TimerTaskRunableTest.new?type=1
        </task>
    </timertask>
	<timertask id="10001" name="标准测试bean" 
         work-time="00:00-24:00" interval="10"
         auto-start="true"
         thread = "true"
         task-type="bean"
		 disabled="true"
        ><task>
            SN-UMC.UMCTimer
        </task>
     </timertask>
     <timertask id="10002" name="标准测试tac" 
         work-time="00:00-24:00" interval="10"
         auto-start="true"
         thread = "true"
         task-type="tac"
		 disabled="true"
        ><task>
            task = getBean("SN-UMC.UMCTimer")  	
            task.run()
        </task>
     </timertask>	
</timertask-list>	
```

**注意：**定时任务配置文件中定义模板，使用时同步至数据库，并指定服务器运行。

### 手册

#### 详细说明
```
定时任务只能在UI端执行，如果执行服务端程序，可以通过Bean或者Tac的方式调用服务端提供的服务；
账套workspc可以不配置，默认为00，如果指定账套，定时任务只在指定的账套下执行（注意：XML配置中的账套必须大写）；
账套配置文件（WorkSpace.xml）中，设置startTimerTask="true"时表示自动启动指定账套的定时任务，在定时任务查询中点击重启所有可以触发；
服务器执行：如果指定了服务器，任务执行只能在指定的服务器上运行，在分布式结构下用处不大；
优先级的作用：任务对象都是继承线程的，优先级就是线程的优先级，高优先级的线程比低优先级的线程有更高的几率得到执行，1到10的范围指定，10优先级最高，默认是5；
自动启动含义：如果不配置自定启动，定时任务需要到定时任务查询中手动启动，如果配置自动启动，任务在保存或者重启时自动的启动定时任务；
独立线程含义：是否通过独立线程的方式执行，一般都设置为独立线程执行；
定时任务定义保存后，会自动触发所有修改过的定时任务重新执行；
XML配置修改的定时任务，在配置管理上传后会重新加载XML配置的定时任务重新执行；
系统选项（TimerTask.LogsFileName）：表示定时任务日志文件指定，定时任务加载任务时打印日志；
位置在系统选项->SysOption01->TimerTask
例如：TimerTask.LogsFileName=TimeTaskLog或者TimerTask.LogsFileName=D://snsoft90/snconfig/TimeTaskLog.txt
生产库中定时任务使用数据库中定义（根据需要配置指定服务器执行）
```
<img src="help/SN-CMC/TimeRtask/img/main-timer-list.png"/>

#### 配置属性说明

```
disabled：禁用，定时任务是否禁用（true：禁用，默认false）
id：任务号
name：任务名
task-type：任务类型
     （1）Java：配置Java线程类，格式为【snsoft.xxx.XXXX.new】；
     （2）Tac：Tac代码块；
     （3）Bean：SpringBean获取线程服务，定义BeanID；
interval：间隔（秒），定时任务执行间隔时间
auto-start：自动启动，定时任务是否自动启动（默认false）
thread：独立线程，定时任务是否独立线程（默认false）
work-time：工作时间，定时任务执行时间段（例如：00:00-07:00）
task：任务调用，定时任务代码块（根据task-type，如task-type=bean，放入服务名）
servhost：服务机器，多个逗号分割，用于控制当前定时任务在哪台服务器上执行
priority：线程优先级
flags：定时任务标识,flags#1 自动启动, flags#2 独立线程 , flags#4 禁用
remark：定时任务说明
```

**XML特殊符号：**
```
空格：&#x0020;
Tab：&#x0009;
回车：&#x000D; 
换行：&#x000A;
workspc：任务执行帐套，必须是大写的账套，为空默认为00(snsoft.config.WorkSpace.WSID)
exparams特殊属性：
env.USERCODE：用户编码信息，用于定时任务的指定权限
env.USERNAME：用户名称信息
env.USERCUICODE：商户编码信息，用于定时任务的指定权限
{'env.USERCODE':'U01','env.USERNAME':'U01','env.USERCUICODE':'C01'}
```

#### 配置比对说明

可查看实体类：snsoft.dx.vo.timertask.entity.TimerTaskConfig 

|    XML配置列			|    数据库表（timetaskdef）列   	| 		说明   						|
| ----------------------|:--------------------------|:------------------------------|
| 	id	    			| 	taskid		        	|   	任务号					|
| 	name				| 	taskname         		|    	任务名					|
| 	task-type			| 	tasktype          		|    	任务类型					|
|	interval    		|	gaptime					|		调用时间间隔(秒)				|
|	work-time  			|	wtime					|		工作时间					|
|	servhost    		|	servhost				|		服务机器					|
|	task       	 		|	taskproc				|		任务调用					|
|	priority    		|	taskpri					|		优先级				    |
|	auto-start  		|	flags#1					|		自动启动				    |
|	thread      		|	flags#2					|		独立线程				    |
|	remark      		|	remark					|		备注	     			    |
|	disabled   			|	flags#4					|		禁用				    	|
|	workspc    			|	exparams.workspc		|		任务执行帐套				|
|	env.USERCODE    	|	exparams.env.USERCODE	|		用户编码信息				|
|	env.USERNAME    	|	exparams.env.USERNAME	|		用户名称信息				|
|	env.USERCUICODE    	|	exparams.env.USERCUICODE	|		商户编码信息				|

#### 其他参数
数据库配置列其他参数(exparams)，对应XML配置的exparams属性，多个使用"回车"符分割如下：

workspc=10
env.USERCODE=U001

XML配置的exparams属性，数据库配置列其他参数(exparams)，多个使用"换行"符分割如下：

env.USERCODE=U0001&#x000A;env.USERNAME=陈泰林

#### 多线程服务执行

底层提供了多线程执行的定时任务基类，详细情况参见类注释
snsoft.commons.util.timer.task.ServTaskTimer
snsoft.commons.util.timer.task.ServWorkerTimer

### 设计

#### 相关规范
```java
//定时任务XML配置文件存放在UI工程
//定时任务加载 cfg/timertask下面TimerTask-系统ID.xml文件和数据库中定时任务定义
//定时任务配置文件命名规范："TimerTask-"+系统ID+".xml"
//定时任务添加默认登录信息
try (final UserSession.ReplaceUserSession replaceUserSession = UserSessionManager.impl
					.replaceUserSession(new LoginInfo(WorkSpace.WSID, "00", null, LoginUtils.C_adminCode, "Admin"));)
{
	// TODO 默认
}
或者
try (final UserSession.ReplaceUserSession replaceUserSession = UserSessionManager.impl.replaceUserSession(new LoginInfo(WorkSpace.WSID, "00",
							null, StrUtils.isEmpty(StrUtils.obj2str(SysConfig.impl.getSysOption((String) null, "Login.DefaultSysUsercode")) ? LoginUtils.C_adminCode : StrUtils.obj2str(SysConfig.impl.getSysOption((String) null, "Login.DefaultSysUsercode"), StrUtils.isEmpty(StrUtils.obj2str(SysConfig.impl.getSysOption((String) null, "Login.DefaultSysUsercode")) ? "Admin" : null));)
{
	// TODO 指定默认登录人
}
或者
try (final UserSession.ReplaceUserSession replaceUserSession= UserSessionManager.impl.replaceUserSession(new LoginInfo(WorkSpace.WSID,AccessConstant.SYSID,null, LoginUtils.C_adminCode,"Admin"),new UserSessionManager.ReplaceOption().setCreateSessionId(true));)
{
	// TODO 替换登录信息的同时设置redis登录信息
}

```


#### 定时任务定义

菜单位置：【管控中心】-【开发配置】-【[定时任务定义](uiinvoke/00/zh_CN/theme0/90.Tool.TimerTask.html)】

<img src="help/SN-CMC/TimeRtask/img/TIMERTASK2.png"/> 


#### 定时任务查询

菜单位置：【管控中心】-【开发配置】-【[定时任务查询](uiinvoke/00/zh_CN/theme0/90.QTIMERTASK.html)】

主要用处：
```
查询当前正在执行的定时任务，检查是否存在漏执行的定时任务
检测某个定时任务是否正常执行，主要检查：服务器、账套、工作时间、运行时间
检测某个定时任务是否有重复执行的问题
```
<img src="help/SN-CMC/TimeRtask/img/TIMERTASK3.png"/> 

提供相关的管理工具：
```
停止/启动：停止或启动某个定时任务；
测试执行：直接调用某个定时任务，只在开发环境测试使用；
删除所有：删除所有的定时任务；
重启所有：将所有正在执行的定时任务删除，重新加载全部配置对应的定时任务重新执行；
```

#### 相关程序

```
1. 定时任务查询服务：snsoft.cmc.tools.service.impl.TimerTaskUIServiceImpl
1. 定时任务运行入口监听：snsoft.cmc.tools.ui.TimertaskDefUIListener
1. 定时任务加载服务：snsoft.bas.config.impl.AppTimerTaskLoader
1. 定时任务支持方式实现方法：snsoft.bas.config.impl.AppTimerTaskLoader.buildTimerTask
1. 定时任务对应XML对象：snsoft.dx.vo.timertask.entity.TimerTaskConfig
1. 获取指定任务的定时任务：
	final TimerTask.TimerTaskRun taskRun = TimerTaskScheduler.impl.getTimerTaskRun(workSpc, taskid);
1. 定时任务重新装载任务：
	final UserSession userSession = snsoft.context.AppContext.getUserSession(true);
	TimerTask[] taskAll=((BasConfigs) SpringBeanUtils.getSpringBean("SN-CORE.BasConfigs")).loadTimerTask(userSession.getWorkspcID(), 0);
或者
	TimerTask[] taskAll=AppTimerTaskLoader.loadTask(idWorkspc, null, reload);
1. 定时任务运行服务：snsoft.commons.util.timer.service.impl.TimerTaskSchedulerImpl
1. 定时任务运行服务：snsoft.commons.util.timer.service.impl.TimerTaskRunImpl
1. 支持多线程的定时任务：snsoft.commons.util.timer.task.ServTimerTask
```

#### 使用场景

**Bean类型**

执行继承Runnable的服务BeanID
例如 ：snsoft.mq.api.message.service.MQPushTimer
```java
@SpringBean(name = "SN-MQ.MQPushTimer", impl = "snsoft.mq.message.timer.MQPushTimer")
public interface MQPushTimer extends Runnable, ServerControl
{
}
```

**Tac类型**

执行某段Tac
```javascript
	task = getBean("SN-MQ.MQPushTimer") 
	task.run()
```

**Java类型**

```java
//执行继承Runnable的JAVA对象
//例如 ： snsoft.help.timertask.service.impl.TimerTaskRunableTest.new?type=1
public class TimerTaskRunableTest implements Runnable
{
	String taskid;

	public TimerTaskRunableTest(Map<String,Object> taskMap)
	{
		String taskid = (String) taskMap.get("TASKID");
		this.taskid = taskid;
		System.out.println("");
	}

	@Override
	public void run()
	{
		UserSession userSession = snsoft.context.AppContext.getUserSession();
		String workSpc = userSession.getWorkspcID();
		final TimerTask.TimerTaskRun taskRun = TimerTaskScheduler.impl.getTimerTaskRun(workSpc, taskid);
		System.out.println("我启动了：时间=" + DateUtils.dateToString(DateUtils.getServerDate(false), 5));
	}
}
```

#### 类图

@startuml
Title 定时任务功能类图

DistTaskService <.. TimerTaskUIService
AppTimerTaskLoader <.. DistTaskService
TimerTaskScheduler <.. DistTaskService
TimerTaskScheduler <.. AppTimerTaskLoader

interface TimerTaskUIService {
	// 定时任务维护及查询服务
	+ List<TimerTaskConfig> queryUI(TimertaskParam params);
	+ void saveUI(List<TimerTaskConfig> records);
	+ List<TimeTaskVO> queryTimeTasks(TimerQueryParams params);
	+ void runTimerTask(String serverid, String workSpc, final String taskid);
	+ void removeAll();
	+ void restart();
	+ void restartAll();
	+ void syncToDB(String taskid);
}

interface DistTaskService {
	// 定时任务分布式服务
	+ List<TimeTaskVO> loadTimeTasks();
	+ void reload(String wsid);
	+ void runTimerTask(String workSpc, String taskid);
	+ void removeAll();
	+ void restartAll();
}

class AppTimerTaskLoader {
	// 定时任务加载类
	+ {static} void loadWSTask(int reload);
	+ {static} TimerTaskConfig loadTimerTaskConfig(String wsid, String taskid);
	+ {static} TimerTask buildTimerTask(TimerTaskConfig cfg);
}

interface TimerTaskScheduler {
	// 定时任务执行管理
	+ void addTimerTasks(TimerTask tasks[]);
	+ void removeTimerTask(TimerTask removeTasks[]);
	+ TimerTask.TimerTaskRun[] getAllTaskRun();
	+ void addQueueTask(java.lang.Runnable run);
}

@enduml

#### 时序图

@startuml
Title 定时任务时序图

actor 服务启动

participant loader    as "AppTimerTaskLoader"
participant scheduler as "TimerTaskScheduler"

服务启动 -> loader : [loadWSTask] \n加载定时任务
activate loader
	loader -> loader : [loadTask0] \n加载定时任务
	activate loader
		loader -> loader : [loadTimerTaskConfigs] \n加载数据库定时任务定义
		
		loader -> loader : [buildTimerTask] \n根据配置构建任务
		activate loader
		deactivate loader
	deactivate loader
	
	loader -> scheduler : [addTimerTasks] \n启动定时任务
	activate scheduler
	scheduler -> loader
	deactivate scheduler
loader -> 服务启动 :
deactivate loader
@enduml





