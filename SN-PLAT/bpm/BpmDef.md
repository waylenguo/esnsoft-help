## 工作流设计文档

设计记录：

|设计人|设计日期|宣讲日期|
|------|------|------|
|陈泰林|2020-07-29|2020-07-31|

评审记录：

|评审日期|评审人|
|------|------|
|2020-07-31|冀小雷、郭鑫|

变更记录：

|变更日期|变更说明|
|------|------|
|2020-07-29|新增|

### 总体设计

* 【功能说明】

单据的工作流转

* 【主要结论】

1. 多条线执行时，下一个节点初始化条件是判断上节点是否都已经完成（上节点执行中或者未执行时未完成，同时节点未执行时跨节点找上一个节点一直找到已运行的节点为止，已运行的节点未完成时需等待完成，如果已运行的节点已完成时视为此条线已完成），节点每一次完成需要主动触发所有未完成的节点提交（待提交的节点）
1. 默认流程数据归属MDB，但预留数据库接口
1. 工作台执行打开界面填写数据，工作台上提交按钮和撤回按钮操作
1. 工作台查询子表数据；按钮控制：当前节点是未完成节点时提交按钮可用，否则不可用；当前节点是可撤销的节点时撤回按钮可用，否则不可用；可撤销的节点：内部程序处理，只有倒数第二个节点可以撤销（如果多个路线，按路线判断是否可以撤销）
1. 详情上只有提交和取消提交按钮；按钮控制：按钮起点未完成提交按钮可用，否则提交按钮不可用；取消提交按钮，同可撤销的节点相同处理逻辑

* 【流程目标】

![](help/SN-PLAT/bpm/img/BPM流程.png)

* 【BPM表结构】

![](help/SN-PLAT/bpm/img/BPM.png)

* 【流程图】

@startuml
Title 工作流控制流程图

actor 单据详情
participant bpmsheet as "BPMSheetService"
participant engine   as "BPMEngineService"
participant sheet    as "业务服务"

单据详情 -> bpmsheet : 提交
activate bpmsheet
	bpmsheet -> engine : 提交工作流
		activate engine
			engine -> engine : 获取BPM配置找开始节点(锁：表名+内码+节点)
			engine -> engine : 初始化执行提交检查（提交）
			engine -> engine : 询问信息返回（提交）（多个检查时一起询问，多一个是否询问参数）
			engine -> engine : 错误信息抛出（提交）
			engine -> engine : 初始化节点询问操作（询问人操作部门或者岗位）
			engine -> sheet :  业务单据状态变更
			engine -> engine : 初始化节点准备
			engine -> engine : 初始化执行提交动作（提交）
			engine -> engine : 初始化执行提交动作日志（提交）
			engine -> engine : 提交节点
		deactivate engine
deactivate bpmsheet

单据详情 -> bpmsheet : 取消提交工作流
activate bpmsheet
	bpmsheet -> engine : 取消提交工作流
	activate engine
		engine -> engine : 获取BPM配置找开始节点(锁：表名+内码+节点)
		engine -> engine : 检查是否可以取消（已经提交节点不可以取消）
		engine -> engine : 撤回开始节点
		engine -> engine : 初始化执行提交检查（撤回）
		engine -> engine : 询问信息返回（撤回）（多个检查时一起询问，多一个是否询问参数，不询问）
		engine -> engine : 错误信息抛出（撤回）
		engine -> sheet :  撤销业务单据状态（如果单据状态已经配置） 
		engine -> engine : 初始化执行提交动作（撤回）
	deactivate engine
deactivate bpmsheet

工作台 -> bpmsheet : 提交节点
activate bpmsheet
	bpmsheet -> engine : 提交节点
	activate engine
		engine -> engine : 获取BPM配置找节点(锁：表名+内码+节点)
		engine -> engine : 提交节点检查
		engine -> engine : 执行提交检查（提交）
		engine -> engine : 询问信息返回（提交）（多个检查时一起询问，多一个是否询问参数）
		engine -> engine : 错误信息抛出（提交）
		engine -> engine : 执行提交动作（提交）
		engine -> engine : 执行提交动作日志（提交）
		engine -> engine : 获取BPM配置找下一个节点（同步分布式锁：表名+内码，多节点都完成的情况）
		engine -> engine : 初始化执行提交检查（提交）
		engine -> engine : 询问信息返回（提交）（多个检查时一起询问，多一个是否询问参数）
		engine -> engine : 错误信息抛出（提交）
		engine -> engine : 初始化节点询问操作（询问人操作部门或者岗位）
		engine -> sheet :  业务单据状态变更
		engine -> engine : 初始化节点准备
		engine -> engine : 初始化执行提交动作（提交）
		engine -> engine : 初始化执行提交动作日志（提交）
	deactivate engine
deactivate bpmsheet

工作台 -> bpmsheet : 撤回节点
activate bpmsheet
	bpmsheet -> engine : 撤回节点
	activate engine
		engine -> engine : 获取BPM配置找撤回节点(锁：表名+内码+节点)
		engine -> engine : 撤回节点检查
		engine -> engine : 获取BPM配置找下级满足条件的多个节点
		engine -> engine : 执行提交检查（撤回）
		engine -> engine : 询问信息返回（撤回）（多个检查时一起询问，多一个是否询问参数，不需要询问）
		engine -> engine : 错误信息抛出（撤回）
		engine -> engine : 执行提交动作（撤回）
		engine -> engine : 执行提交动作日志（撤回）
		engine -> engine : 获取BPM配置找当前撤回节点
		engine -> engine : 初始化执行提交检查（撤回）
		engine -> engine : 询问信息返回（撤回）（多个检查时一起询问，多一个是否询问参数）
		engine -> engine : 错误信息抛出（撤回）
		engine -> engine : 初始化节点询问操作（询问人操作部门或者岗位）
		engine -> engine : 初始化节点准备
		engine -> sheet :  业务单据状态变更
		engine -> engine : 初始化执行提交动作（撤回）
		engine -> engine : 初始化执行提交动作日志（撤回）
	deactivate engine
deactivate bpmsheet


@enduml

### 使用手册

#### 单据详情配置

界面按钮：
```
<ToolbarBtn name="cargopay_btn_submitbpm" noClientComponent="true" title="提交" uiprops.cellClassName="ui-head-btn" xprops="iconClassName:icons-btn-submit" />

<ToolbarBtn name="cargopay_btn_backnode" noClientComponent="true" title="撤回" uiprops.cellClassName="ui-head-btn" xprops="iconClassName:icons-btn-recall" />
```

界面流程：
```
<m:P title="流程情况">
            <m:DialogPane hidden="true" layoutm="grid" name="nodeParam" region="north" title="流程结果">
                <c name="bpmid" noblank="true" sqltype="12" title="流程ID" />
            </m:DialogPane>
            <m:T region="center" name="nodeTbl" uiprops.cellClassName="ui-tabpane-level2">
                <include src="SN-PLAT.BPM.BPMPlatNodelist#nodedata" mainui="nodeParam" />
                <m:P title="工作流过程" name="nodeflow"></m:P>
                <m:P title="工作流进程" name="noderoute"></m:P>
            </m:T>
</m:P>
```

界面监听:
```
<Groups id="工作流单据详情">
		<Functions>
			<Functype>SN-PLAT.JS</Functype>
			<Subtype>2</Subtype>
			<Funcimpl>#new snsoft.plat.bas.bpm.BPMSheetListener({"tgtUINames":["cargopay"],"taskTblName":"nodedata"})
			</Funcimpl>
			<Remark>工作流-按钮</Remark>
		</Functions>
		<Functions>
			<Functype>SN-PLAT.UI</Functype>
			<Subtype>1</Subtype>
			<Funcimpl><![CDATA[
			 SN-PLAT.BPMSheetListener?[{}]
			 ]]>
			</Funcimpl>
			<Remark>工作流-按钮</Remark>
		</Functions>
</Groups>
<Groups id="工作流审批">
        <Functions>
            <Functype>SN-PLAT.WF</Functype>
            <Subtype>1</Subtype>
            <Funcimpl>SN-APPR.ApprBPMListener</Funcimpl>
            <Remark>审批提交节点信息</Remark>
        </Functions>
</Groups> 
<Groups id="工作流处理">
        <Functions>
            <Functype>SN-PLAT.SV</Functype>
            <Subtype>1</Subtype>
            <Funcimpl>SN-PLAT.BPMSheetDelCheckSVListener</Funcimpl>
            <Remark>工作流业务单据删除检查</Remark>
        </Functions>
</Groups> 
```

#### 单据入口配置

界面按钮：
```
<ToolbarBtn name="plat_bl_py_btn_querybpm" title="流程情况" noClientComponent="true" />
```

界面监听:
```
<Groups id="工作流单据入口">
		<Functions>
			<Functype>SN-PLAT.JS</Functype>
			<Subtype>2</Subtype>
			<Funcimpl>#new snsoft.plat.bas.bpm.BPMSheetListener({"tgtUINames":["plat_bl_py"]})</Funcimpl>
			<Remark>工作流-情况</Remark>
		</Functions>
</Groups> 
``` 

#### 单据VO配置

```
VO必须实现工作流接口 BPMSheetVO
```

#### 单据服务配置

初始化服务
```
BPMProcessInitService->初始化服务接口
BPMProcessInitMQService->初始化服务MQ接口
BPMProcessInitSubService->初始化服务子流程接口
BPMSysOpeProcessInitService->系统内置操作信息提示初始化服务
BPMSysUmcProcessInitService->系统内置用户消息初始化服务
ApprBPMProcessInitService->系统提供开启审批链初始化服务
```

执行服务
```
BPMProcessExeService->执行服务接口
BPMSubmitCheckService->系统提供提交单据检查执行服务
BPMSysSubProcessService->系统提供子流程回调主流程执行服务
BPMMessageApprService->系统提供审批回调流程执行服务，由审批监听触发
BPMMessageMQService->系统提供MQ回调流程执行服务
BPMSysTacProcessService->系统内置支持TAC执行服务
```

服务介绍
```
BPMProcessService->服务接口
BPMProcessInitService->初始化服务接口
BPMProcessExeService->执行服务接口

//提交检查
BPMProcessCheckResult checkPos(BPMProcessEvent event);
//提交执行
BPMProcessExeResult processPos(BPMProcessEvent event);
//撤回检查
BPMProcessCheckResult checkNeg(BPMProcessEvent event);
//撤回执行
BPMProcessExeResult processNeg(BPMProcessEvent event);

//TAC提交检查
proc checkPos(event)
end proc
//TAC提交执行
proc processPos(event)
end proc
//TAC撤回检查
proc checkNeg(event)
end proc
//TAC撤回执行
proc processNeg(event)
end proc
```

流程路线
```
TAC内置变量：
data->单据数据
bpmdata->工作流数据
bpmdef->工作流配置
nodefmdef->节点从配置
nodetodef->节点到配置
linedef->流程路线配置
```

#### 工作流配置

```xml
<BPMDef xmlns="http://www.snsoft.com.cn/schema/BPMDef"
        xsi:schemaLocation="http://www.snsoft.com.cn/schema/BPMDef http://www.snsoft.com.cn/schema/BPMDef.xsd"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        bpmname="工作流标准配置" enabled="0"
        >  
    <remark>工作流标准配置</remark>
    <BPMNodeDef>
        <!--节点编号:-->
        <ndcode>100</ndcode>
        <!--节点名称:-->
        <ndname>XXX100</ndname>
        <!--节点类型:10前端交互 20后端执行-->
        <ndtype>10</ndtype>
        <!--节点模型:10起始节点 20终止节点 30执行节点-->
        <ndmode>10</ndmode>
        <!--业务状态:-->
        <busistatus></busistatus>
        <!--坐标:-->
        <posx>2</posx>
        <!--坐标:-->
        <posy>0</posy>
        <!--打开界面:-->
        <opui></opui>
        <BPMNodeTaskDef>
            <!--服务类型:10初始化执行20提交执行-->
            <svtype>10</svtype>
            <!--服务编排:-->
            <svcode>1010</svcode>
            <!--执行顺序:-->
            <sortidx>10</sortidx>
            <!--执行服务:-->
            <svbean>XXXXXX</svbean>
            <!--服务说明:-->
            <svremark>服务说明</svremark>
        </BPMNodeTaskDef>
        <BPMNodeTaskDef>
            <!--服务类型:10初始化执行20提交执行-->
            <svtype>20</svtype>
            <!--服务编排:-->
            <svcode>2010</svcode>
            <!--执行顺序:-->
            <sortidx>10</sortidx>
            <!--执行服务:-->
            <svbean>XXXXXX</svbean>
            <!--服务说明:-->
            <svremark>服务说明</svremark>
        </BPMNodeTaskDef>
    </BPMNodeDef>
    <BPMNodeLineDef>
	     <!--开始节点编号:-->
	   	 <ndcodefm>100</ndcodefm>
	   	 <!--到达节点编号:-->
	   	 <ndcodeto>210</ndcodeto>
	   	 <!--TAC条件:-->
	   	 <tacformula><![CDATA[
	     status = data.getStatus()	    
	     status == "10"
	     ]]></tacformula>
    </BPMNodeLineDef>
    <BPMNodeLineDef>
	     <!--开始节点编号:-->
	   	 <ndcodefm>100</ndcodefm>
	   	 <!--到达节点编号:-->
	   	 <ndcodeto>220</ndcodeto>
	   	 <!--TAC条件:-->
	   	 <tacformula><![CDATA[
	     status = data.getStatus()	    
	     status == "10"
	     ]]></tacformula>
    </BPMNodeLineDef>
    <BPMNodeDef>
        <!--节点编号:-->
        <ndcode>210</ndcode>
        <!--节点名称:-->
        <ndname>XXX210</ndname>
         <!--节点类型:10前端交互 20后端执行-->
        <ndtype>10</ndtype>
        <!--节点模型:10起始节点 20终止节点 30执行节点-->
        <ndmode>30</ndmode>
        <!--业务状态:-->
        <busistatus></busistatus>
        <!--坐标:-->
        <posx>4</posx>
        <!--坐标:-->
        <posy>1</posy>
        <!--打开界面:-->
        <opui></opui>
        <!--Zero or more repetitions:-->
        <BPMNodeTaskDef>
            <!--服务类型:10初始化执行20提交执行-->
            <svtype>20</svtype>
            <!--服务编排:-->
            <svcode>2010</svcode>
            <!--执行顺序:-->
            <sortidx>10</sortidx>
            <!--执行服务:-->
            <svbean>XXXXXX</svbean>
            <!--服务说明:-->
            <svremark>服务说明</svremark>
        </BPMNodeTaskDef>
    </BPMNodeDef>
    <BPMNodeLineDef>
	     <!--开始节点编号:-->
	   	 <ndcodefm>210</ndcodefm>
	   	 <!--到达节点编号:-->
	   	 <ndcodeto>300</ndcodeto>
	   	 <!--TAC条件:-->
	   	 <tacformula><![CDATA[
	   	 true
	     ]]></tacformula>
    </BPMNodeLineDef>
    <BPMNodeDef>
        <!--节点编号:-->
        <ndcode>220</ndcode>
        <!--节点名称:-->
        <ndname>XXX220</ndname>
         <!--节点类型:10前端交互 20后端执行-->
        <ndtype>10</ndtype>
        <!--节点模型:10起始节点 20终止节点 30执行节点-->
        <ndmode>30</ndmode>
        <!--业务状态:-->
        <busistatus></busistatus>
        <!--坐标:-->
        <posx>0</posx>
        <!--坐标:-->
        <posy>1</posy>
        <!--打开界面:-->
        <opui></opui>
        <!--Zero or more repetitions:-->
        <BPMNodeTaskDef>
            <!--服务类型:10初始化执行20提交执行-->
            <svtype>20</svtype>
            <!--服务编排:-->
            <svcode>2010</svcode>
            <!--执行顺序:-->
            <sortidx>10</sortidx>
            <!--执行服务:-->
            <svbean>XXXXXX</svbean>
            <!--服务说明:-->
            <svremark>服务说明</svremark>
        </BPMNodeTaskDef>
    </BPMNodeDef>
    <BPMNodeLineDef>
	     <!--开始节点编号:-->
	   	 <ndcodefm>220</ndcodefm>
	   	 <!--到达节点编号:-->
	   	 <ndcodeto>300</ndcodeto>
	   	 <!--TAC条件:-->
	   	 <tacformula><![CDATA[
	   	 true
	     ]]></tacformula>
    </BPMNodeLineDef>
    <BPMNodeDef>
        <!--节点编号:-->
        <ndcode>300</ndcode>
        <!--节点名称:-->
        <ndname>XXX300（TAC）</ndname>
         <!--节点类型:10前端交互 20后端执行-->
        <ndtype>10</ndtype>
        <!--节点模型:10起始节点 20终止节点 30执行节点-->
        <ndmode>30</ndmode>
        <!--业务状态:-->
        <busistatus></busistatus>
        <!--坐标:-->
        <posx>2</posx>
        <!--坐标:-->
        <posy>2</posy>
        <!--打开界面:-->
        <opui>SN-HELP.Bill.TrdPayBillDetail</opui>
        <!--Zero or more repetitions:-->
        <BPMNodeTaskDef>
            <!--服务类型:10初始化执行20提交执行-->
            <svtype>20</svtype>
            <!--服务编排:-->
            <svcode>2010</svcode>
            <!--执行顺序:-->
            <sortidx>10</sortidx>
            <!--执行服务:-->
            <svbean>XXXXXX</svbean>
            <!--服务说明:-->
            <svremark>服务说明</svremark>
        </BPMNodeTaskDef>
        <BPMNodeTaskDef>
            <!--服务类型:10初始化执行20提交执行-->
            <svtype>20</svtype>
            <!--服务编排:-->
            <svcode>2020</svcode>
            <!--执行顺序:-->
            <sortidx>20</sortidx>
            <!--执行服务:-->
            <svtac><![CDATA[
            proc checkPos(event)
		    end proc
            proc processPos(event)
		    end proc
		    proc checkNeg(event)
		    end proc
		    proc processNeg(event)
		    end proc
	     ]]></svtac>
	       <!--服务说明:-->
            <svremark>服务说明</svremark>
        </BPMNodeTaskDef>
    </BPMNodeDef>
    <BPMNodeLineDef>
	     <!--开始节点编号:-->
	   	 <ndcodefm>300</ndcodefm>
	   	 <!--到达节点编号:-->
	   	 <ndcodeto>400</ndcodeto>
	   	 <!--TAC条件:-->
	   	 <tacformula><![CDATA[
	     true
	     ]]></tacformula>
    </BPMNodeLineDef>
    <BPMNodeDef>
        <!--节点编号:-->
        <ndcode>400</ndcode>
        <!--节点名称:-->
        <ndname>XXX400（审批）</ndname>
         <!--节点类型:10前端交互 20后端执行-->
        <ndtype>20</ndtype>
        <!--节点模型:10起始节点 20终止节点 30执行节点-->
        <ndmode>30</ndmode>
        <!--业务状态:-->
        <busistatus></busistatus>
        <!--坐标:-->
        <posx>2</posx>
        <!--坐标:-->
        <posy>4</posy>
        <!--消息:-->
        <sendflag>1</sendflag>
        <!--打开界面:-->
        <opui></opui>
        <!--Zero or more repetitions:-->
        <BPMNodeTaskDef>
            <!--服务类型:10初始化执行20提交执行-->
            <svtype>10</svtype>
            <!--服务编排:-->
            <svcode>1010</svcode>
            <!--执行顺序:-->
            <sortidx>10</sortidx>
            <!--执行服务:-->
            <svbean>SN-APPR.ApprBPMProcessInitService</svbean>
            <!--服务说明:-->
            <svremark><![CDATA[
审批回调说明:
<Functions>
   <Functype>SN-PLAT.WF</Functype>
   <Subtype>1</Subtype>
   <Funcimpl>SN-APPR.ApprBPMListener</Funcimpl>
   <Remark>审批提交节点信息</Remark>
</Functions>  
	     ]]></svremark>
        </BPMNodeTaskDef>
    </BPMNodeDef>
    <BPMNodeLineDef>
	     <!--开始节点编号:-->
	   	 <ndcodefm>400</ndcodefm>
	   	 <!--到达节点编号:-->
	   	 <ndcodeto>500</ndcodeto>
	   	 <!--TAC条件:-->
	   	 <tacformula><![CDATA[
	   	 isApproved(bpmdata,nodefmdef)
	     ]]></tacformula>
    </BPMNodeLineDef>
    <BPMNodeLineDef>
	     <!--开始节点编号:-->
	   	 <ndcodefm>400</ndcodefm>
	   	 <!--到达节点编号:-->
	   	 <ndcodeto>600</ndcodeto>
	   	 <!--TAC条件:-->
	   	 <tacformula><![CDATA[
	     !isApproved(bpmdata,nodefmdef)
	     ]]></tacformula>
    </BPMNodeLineDef>
    <BPMNodeDef>
        <!--节点编号:-->
        <ndcode>500</ndcode>
        <!--节点名称:-->
        <ndname>XXX500（MQ）</ndname>
        <!--节点类型:10前端交互 20后端执行-->
        <ndtype>20</ndtype>
        <!--节点模型:10起始节点 20终止节点 30执行节点-->
        <ndmode>30</ndmode>
        <!--业务状态:-->
        <busistatus></busistatus>
        <!--消息:-->
        <sendflag>1</sendflag>
        <!--坐标:-->
        <posx>6</posx>
        <!--坐标:-->
        <posy>3</posy>
        <!--打开界面:-->
        <opui></opui>
        <!--Zero or more repetitions:-->
        <BPMNodeTaskDef>
            <!--服务类型:10初始化执行20提交执行-->
            <svtype>10</svtype>
            <!--服务编排:-->
            <svcode>1010</svcode>
            <!--执行顺序:-->
            <sortidx>10</sortidx>
            <!--执行服务:-->
            <svbean>SN-HELP.HELPPayBillMQService</svbean>
            <!--服务说明:-->
            <svremark><![CDATA[
MQ回调说明:
SN-PLAT.BPMMessageMQService.submitNode
	     ]]></svremark>
        </BPMNodeTaskDef>
    </BPMNodeDef>
    <BPMNodeLineDef>
	     <!--开始节点编号:-->
	   	 <ndcodefm>500</ndcodefm>
	   	 <!--到达节点编号:-->
	   	 <ndcodeto>800</ndcodeto>
	   	 <!--TAC条件:-->
	   	 <tacformula><![CDATA[
	     true
	     ]]></tacformula>
    </BPMNodeLineDef>
    <BPMNodeDef>
        <!--节点编号:-->
        <ndcode>600</ndcode>
        <!--节点名称:-->
        <ndname>XXX600（审批驳回）</ndname>
        <!--节点类型:10前端交互 20后端执行-->
        <ndtype>10</ndtype>
        <!--节点模型:10起始节点 20终止节点 30执行节点-->
        <ndmode>30</ndmode>
        <!--业务状态:-->
        <busistatus></busistatus>
        <!--消息:-->
        <sendflag>1</sendflag>
        <!--坐标:-->
        <posx>6</posx>
        <!--坐标:-->
        <posy>5</posy>
        <!--打开界面:-->
        <opui></opui>
        <!--Zero or more repetitions:-->
        <BPMNodeTaskDef>
            <!--服务类型:10初始化执行20提交执行-->
            <svtype>20</svtype>
            <!--服务编排:-->
            <svcode>2010</svcode>
            <!--执行顺序:-->
            <sortidx>10</sortidx>
            <!--执行服务:-->
            <svbean>XXXXXX</svbean>
            <!--服务说明:-->
            <svremark>服务说明</svremark>
        </BPMNodeTaskDef>
    </BPMNodeDef>
    <BPMNodeLineDef>
	     <!--开始节点编号:-->
	   	 <ndcodefm>600</ndcodefm>
	   	 <!--到达节点编号:-->
	   	 <ndcodeto>210</ndcodeto>
	   	 <!--TAC条件:-->
	   	 <tacformula><![CDATA[
	     status = data.getStatus()	 
	     status != "10"
	     ]]></tacformula>
    </BPMNodeLineDef>
    <BPMNodeLineDef>
	     <!--开始节点编号:-->
	   	 <ndcodefm>600</ndcodefm>
	   	 <!--到达节点编号:-->
	   	 <ndcodeto>220</ndcodeto>
	   	 <!--TAC条件:-->
	   	 <tacformula><![CDATA[
	     status = data.getStatus()	 
	     status != "10"
	     ]]></tacformula>
    </BPMNodeLineDef>
    <BPMNodeDef>
        <!--节点编号:-->
        <ndcode>800</ndcode>
        <!--节点名称:-->
        <ndname>XXX800（子流程）</ndname>
        <!--节点类型:10前端交互 20后端执行-->
        <ndtype>20</ndtype>
        <!--节点模型:10起始节点 20终止节点 30执行节点-->
        <ndmode>30</ndmode>
        <!--业务状态:-->
        <busistatus></busistatus>
        <!--坐标:-->
        <posx>8</posx>
        <!--坐标:-->
        <posy>3</posy>
        <!--打开界面:-->
        <opui></opui>
        <!--Zero or more repetitions:-->
        <BPMNodeTaskDef>
            <!--服务类型:10初始化执行20提交执行-->
            <svtype>10</svtype>
            <!--服务编排:-->
            <svcode>1010</svcode>
            <!--执行顺序:-->
            <sortidx>10</sortidx>
            <!--执行服务:-->
            <svbean>SN-HELP.HELPPayBillSubService</svbean>
            <!--服务说明:-->
            <svremark>子流程初始化服务</svremark>
        </BPMNodeTaskDef>
    </BPMNodeDef>
    <BPMNodeLineDef>
	     <!--开始节点编号:-->
	   	 <ndcodefm>800</ndcodefm>
	   	 <!--到达节点编号:-->
	   	 <ndcodeto>900</ndcodeto>
	   	 <!--TAC条件:-->
	   	 <tacformula><![CDATA[
	     true
	     ]]></tacformula>
    </BPMNodeLineDef>
    <BPMNodeDef>
        <!--节点编号:-->
        <ndcode>900</ndcode>
        <!--节点名称:-->
        <ndname>XXX900（结束）</ndname>
        <!--节点类型:10前端交互 20后端执行-->
        <ndtype>10</ndtype>
        <!--节点模型:10起始节点 20终止节点 30执行节点-->
        <ndmode>20</ndmode>
        <!--业务状态:-->
        <busistatus></busistatus>
        <!--坐标:-->
        <posx>10</posx>
        <!--坐标:-->
        <posy>3</posy>
        <!--打开界面:-->
        <opui></opui>
        <!--Zero or more repetitions:-->
        <BPMNodeTaskDef>
            <!--服务类型:10初始化执行20提交执行-->
            <svtype>20</svtype>
            <!--服务编排:-->
            <svcode>2010</svcode>
            <!--执行顺序:-->
            <sortidx>10</sortidx>
            <!--执行服务:-->
            <svbean>XXXXXX</svbean>
            <!--服务说明:-->
            <svremark>服务说明</svremark>
        </BPMNodeTaskDef>
        <BPMNodeTaskDef>
            <!--服务类型:10初始化执行20提交执行-->
            <svtype>20</svtype>
            <!--服务编排:-->
            <svcode>2020</svcode>
            <!--执行顺序:-->
            <sortidx>20</sortidx>
            <!--执行服务:-->
            <svbean>SN-PLAT.BPMSysSubProcessService</svbean>
            <!--服务说明:-->
            <svremark>子流程回调服务</svremark>
        </BPMNodeTaskDef>
    </BPMNodeDef>
</BPMDef>
```
 
### 工程结构

工程名称：plat-bas-api、plat-bas-impl、plat-bas-ui、plat-bas-xjs
包名路径：snsoft.plat.bas.bpm.*

### 模块设计

##### 单据定义

1. 单据定义：
          配置权限使用（指向运维部门权限）
          单据号：SN-PLAT.BpmDef

##### 界面

1. 工作流定义：SN-PLAT.Bpm.BpmDef；
1. 单据/工作流关系定义：SN-PLAT.Bpm.SheetBpm；
1. 工作台：SN-PLAT.Bpm.BpmNodeMessage；
1. 工作台管控：SN-PLAT.Bpm.CMC.BpmNodeMessage；

##### 实体类

* 【实体类】

|包名类名|名称|类型|主要功能|
|------|------|------|------|
|snsoft.plat.bas.bpm.vo.BPMDef|工作流配置|VO类|配置存储|
|snsoft.plat.bas.bpm.vo.BPMNodeLineDef|工作流节点流程路线配置子表|VO类|配置存储|
|snsoft.plat.bas.bpm.vo.BPMNodeDef|工作流节点配置子表|VO类|配置存储|
|snsoft.plat.bas.bpm.vo.BPMNodeTaskDef|工作流节点任务配置孙表|VO类|配置存储|
|snsoft.plat.bas.bpm.vo.BPMNodeWWDef|工作流节点操作配置孙表|VO类|配置存储，单独使用查询存盘服务，起点/终点/后台运行节点不配置，目前只能选择相同类型的操作|
|snsoft.plat.bas.bpm.vo.BPMSheetDef|工作流与单据关系配置|VO类|配置存储，参考单据审批链关系定义|
|snsoft.plat.bas.bpm.vo.BPMData|工作流数据|VO类|默认提供MDB存储，存储工作流主数据|
|snsoft.plat.bas.bpm.vo.BPMNode|工作流数据节点子表|VO类|默认提供MDB存储，存储工作流流转数据节点的执行情况|
|snsoft.plat.bas.bpm.vo.BPMTask|工作流数据任务孙表|VO类|默认提供MDB存储，存储工作流流转数据节点的任务执行情况|
|snsoft.plat.bas.bpm.vo.BPMLog|工作流数据日志|VO类|默认提供MDB存储，存储工作流流转数据过程中日志记录|

* 工作流配置表bpm_def

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|cuicode|商户|VARCHAR|SZCUICODE|是|是||||
|bpmcode|配置编号|VARCHAR|SZIBILL|是|是||||
|bpmname|配置名称|VARCHAR|SZNAME||||||
|status|状态|VARCHAR|STATUS|||||10:草拟 70：启用 90 ：停用|
|limbcode|运维组织|VARCHAR|SZUCODE||||||
|fromsys|系统下发|SMALLINT|||||||
|remark|备注|VARCHAR|SZTEXT||||||
|vprepare|创建人|VARCHAR|SZUCODE|| ||| |
|predate|创建日期|DATE||| ||| |
|modifier|修改人|VARCHAR|SZUCODE|| ||| |
|modifydate|修改时间|DATE||||||xxx|

* 工作流节点表bpm_nd

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|cuicode|商户|VARCHAR|SZCUICODE|是|是||||
|bpmcode|配置编号|VARCHAR|SZIBILL|是|是||||
|ndcode|节点编号|VARCHAR|SZIBILL|是|是||||
|ndname|节点名称|VARCHAR|SZNAME||||||
|ndtype|节点类型|VARCHAR|2|||||前端交互、后端执行|
|ndmode|节点模式|VARCHAR|2|||||起始/中间/终止|
|busistatus|单据状态|VARCHAR|SZSTATUS|||||单据状态|
|posx|坐标X|VARCHAR|SZTEXT||||||
|posy|坐标Y|VARCHAR|SZTEXT||||||
|opui|界面路径|VARCHAR|SZTEXT|||||funcid/muiid|

* 工作流节点任务表bpm_nd_task

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|cuicode|商户|VARCHAR|SZCUICODE|是|是||||
|bpmcode|配置编号|VARCHAR|SZIBILL|是|是||||
|ndcode|节点编号|VARCHAR|SZIBILL|是|是||||
|svcode|服务编号|VARCHAR|SZIBILL|是|是||||
|sortidx|序号|SMALLINT||||||执行顺序号|
|svtype|服务类型|VARCHAR|2|||||初始服务、执行服务|
|svbean|服务定义|VARCHAR|SZTEXT|||||服务必须实现特定接口|

* 工作流节点流程线表bpm_nd_line

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|cuicode|商户|VARCHAR|SZCUICODE|是|是||||
|bpmcode|配置编号|VARCHAR|SZIBILL|是|是||||
|ndcodefm|节点编号|VARCHAR|SZIBILL|是|是||||
|ndcodeto|下游节点编号|VARCHAR|SZIBILL|是|是||||
|tacformula|执行条件|VARCHAR|SZTEXT||||||

* 工作流节点操作配置表bpm_ww

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|cuicode|商户|VARCHAR|SZCUICODE|是|是||||
|bpmcode|配置编号|VARCHAR|SZIBILL|是|是||||
|ndcode|节点编号|VARCHAR|SZIBILL|是|是||||
|sortidx|序号|SMALLINT||是|是|||执行顺序号|
|utype|操作类型|VARCHAR|255|||||同一个类型|
|ucode|操作编码|VARCHAR|255||||||
|pfinishtime|预计完成时间|Long||||||如果预计完成时间完成不了怎么办？|

* 工作流与单据关系表bpm_sheet

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|cuicode|商户|VARCHAR|SZCUICODE|是|是||||
|bpmcode|配置编号|VARCHAR|SZIBILL|是|是||||
|sheetcode|单据|VARCHAR|SZSHEET|是|是||||
|utype|匹配类型|VARCHAR|255||||||
|ucode|匹配值|VARCHAR|255||||||
|tacformula|Tac匹配条件|VARCHAR|SZTAC||||||
|limbcode|运维组织|VARCHAR|SZUCODE||||||
|remark|备注|VARCHAR|SZTEXT||||||
|vprepare|创建人|VARCHAR|SZUCODE|| ||| |
|predate|创建日期|DATE||| ||| |
|modifier|修改人|VARCHAR|SZUCODE|| ||| |
|modifydate|修改时间|DATE||||||xxx|

* 工作流数据bpm_data

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|bpmid|主键|VARCHAR||是||||bpmid=sheetcode.innercode|
|cuicode|商户|VARCHAR|||||||
|bpmcode|配置编号|VARCHAR|||||||
|sheetcode|单据|VARCHAR|||||||
|innercode|内码|VARCHAR|||||||
|outercode|外码|VARCHAR|||||||
|bcode|部门|VARCHAR|||||||
|wcode|业务员|VARCHAR|||||||
|corpbcode|公司|VARCHAR|||||||
|costbcode|核算组|VARCHAR|||||||
|ccode|客户|VARCHAR|||||||
|odate|业务时间|Date|||||||
|status|节点状态|VARCHAR||||||节点状态：10未开始 30 执行中  70完成 |
|finishdate|完成时间|Date||||||流程结束的时间|
|smdate|提交时间|Date||||||流程提交的时间|
|smucode|提交人|VARCHAR||||||流程提交人|
|vprepare|创建人|VARCHAR|SZUCODE|| ||| |
|predate|创建日期|DATE||| ||| |
|modifier|修改人|VARCHAR|SZUCODE|| ||| |
|modifydate|修改时间|DATE|||||||
|tasks|任务执行列表|bpm_task||||||xxx|

* 工作流数据节点子表bpm_node

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|bpmid|主键|VARCHAR||是||||bpmid=sheetcode.innercode|
|ndcode|节点编号|VARCHAR||是|||||
|utype|操作类型|VARCHAR||||||实际操作类型|
|ucode|操作编码|VARCHAR||||||实际操作编码|
|finishtime|完成时间|Long|||||||
|pfinishtime|预计完成时间|Long|||||||
|finishdate|完成时间|Date||||||节点完成的时间|
|smdate|提交时间|Date||||||节点提交的时间|
|smucode|提交人|VARCHAR||||||节点提交人|
|vprepare|创建人|VARCHAR|SZUCODE|| ||| |
|predate|创建日期|DATE||| ||| |
|status|节点状态|VARCHAR||||||节点状态：10准备  70完成 90 异常|
|xflags|节点扩展标记|SMALLINT||||||1：允许撤回   程序内部处理|

* 工作流数据任务子表bpm_task

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|bpmid|主键|VARCHAR||是||||bpmid=sheetcode.innercode|
|ndcode|节点编号|VARCHAR||是|||||
|svcode|服务编号|VARCHAR||是|||||
|posresult|正向提交执行结果|VARCHAR||||||执行结果 70 成功 90 失败|
|possdate|正向提交执行时间|Date|||||||
|posfinishtime|正向提交完成时间|Long|||||||
|negresult|撤回提交执行结果|VARCHAR||||||执行结果 70 成功 90 失败|
|negsdate|撤回提交执行时间|Date|||||||
|negfinishtime|撤回提交完成时间|Long||||||XXXXXX|

* 工作流数据日志bpm_log

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|logicode|主键|VARCHAR||是|||||
|cuicode|商户|VARCHAR|||||||
|sheetcode|单据|VARCHAR|||||||
|innercode|内码|VARCHAR|||||||
|bpmcode|配置编号|VARCHAR|||||||
|ndcode|节点编号|VARCHAR|||||||
|svcode|服务编号|VARCHAR|||||||
|svaction|服务动作|VARCHAR||||||提交/撤回|
|svresult|正向提交执行结果|VARCHAR||||||执行结果 70 成功 90 失败|
|svsdate|正向提交执行时间|Date|||||||
|svfinishtime|正向提交完成时间|Long|||||||
|sverror|执行结果错误信息|VARCHAR||||||XXXXXX|

##### 接口设计

* 【接口1】

|包名类名|名称|主要功能|
|------|------|------|
|snsoft.plat.bas.bpm.service.BPMSheetService|工作流单据入口服务|服务接入统一入口|
|snsoft.plat.bas.bpm.service.BPMEngineService|工作流引擎服务|服务控制流转核心处理|
|snsoft.plat.bas.bpm.service.BPMDrivenService|工作流驱动服务|服务驱动介入扩展，辅助核心引擎数据流转|
|snsoft.plat.bas.bpm.service.BPMMessageService|工作流异步消息服务|工作流异步消息服务，默认实现消息异步调用BPM|
|snsoft.plat.bas.bpm.service.BPMDataService|工作流数据操作服务|工作流数据操作服务|
|snsoft.plat.bas.bpm.service.BPMLogService|工作流日志操作服务|工作流日志操作服务|
|snsoft.plat.bas.bpm.service.BPMDefService|工作流配置服务|工作流配置服务|
|snsoft.plat.bas.bpm.service.BPMDefUIService|工作流配置UI服务|工作流配置UI服务|

|接口名称|方法名|输入说明|输出说明|
|------|------|------|------|
|BPMSheetService|submitAndInit|BPMRequest|BPMResponse|
|BPMSheetService|retractAndInit|BPMRequest|无|
|BPMSheetService|submitNode|BPMDriveRequest|BPMResponse|
|BPMSheetService|retractNode|BPMDriveRequest|无|
|BPMEngineService|submitAndInit|BPMRequest|BPMResponse|
|BPMEngineService|retractAndInit|BPMRequest|无|
|BPMEngineService|submitNode|BPMDriveRequest|BPMResponse|
|BPMEngineService|retractNode|BPMDriveRequest|无|
|BPMMessageService|submitAndInit|BPMRequest|UIResponse|
|BPMMessageService|retractAndInit|BPMRequest|无|
|BPMMessageService|submitNode|BPMDriveRequest|UIResponse|
|BPMMessageService|retractNode|BPMDriveRequest|无|

* 工作流请求参数BPMRequest

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|cuicode|商户|VARCHAR|||||||
|sheetcode|单据|VARCHAR|||||||
|innercode|内码|VARCHAR|||||||
|bpmcode|配置编号|VARCHAR|||||||
|ndcode|节点编号|VARCHAR|||||||
|askui|忽略本次客户端交互询问|boolean||||||多种检查结果询问，每种检查用于第一次询问，第二次不在询问|

* 工作流相应结果BPMResponse

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|success|结果|VARCHAR|||||||
|askmessage|询问结果信息|VARCHAR||||||多种检查结果询问，每次只能有一种询问结果，每种检查一次性全部询问？|

* 【接口2】

|包名类名|名称|主要功能|
|------|------|------|
|snsoft.plat.bas.bpm.ui.BPMSheetListener|工作流单据UI监听|单据准备参数|
|snsoft.plat.bas.bpm.BPMSheetListener|工作流单据客户端监听|单据按钮控制，远程调用，询问动作|


#### 核心算法

1. 提交工作流：起始节点初始化，自动进入下一节点，做初始化节点
1. 取消工作流：只能在起始节点上点击按钮，找到满足添加的下级节点，做撤回操作
1. 提交节点：在未完成的节点上，点击提交节点，找到满足条件的下级节点，做初始化节点
1. 撤回节点：只能在能撤回的节点上点击按钮（多条线上最后一个未完成的节点的上一个节点），找到满足添加的下级节点，做撤回操作
1. 完结工作流：终点节点完成时，视为流程结束，调用完结工作流

#### 字典

服务执行结果：
编码：SN-PLAT.SVExeResult
数据：70:成功, 90:失败

编码：SN-PLAT.SVAction
数据：10:提交, 20:撤回

编码：SN-PLAT.SVType
数据：10:初始化, 20:执行

编码：SN-PLAT.NDType
数据：10:前端交互, 20:后端执行

编码：SN-PLAT.NDMode
数据：10:起始节点, 20:终止节点, 30:执行节点

#### 涉及功能

1. 配置开发（配置相关的服务，流程图绘制） 
1. 流程引擎（流程引擎相关的服务） ，日志操作（日志相关的服务） ，流程数据（流程数据相关的服务）
1. 按钮控制（公共控制单据详情及远程调用相关单据服务）
1. 工作台（左数型） 后续1
1. 单据动态流程图（挂在单据上的动态流程） 后续1
1. 审批接口和MQ回调接口对接  后续2
1. 用户消息发送接口对接  后续2

#### 疑问

1. 保证事物一致性(同步执行？),突然换包，突然报错等等   先不需要考虑 后续底层统一解决 
1. 起点：第一次提交的时候创建并执行到下一节点  ok
1. 终点：满足的上游节点全部执行完成并到达终点时，终点自动完成 ok
1. 流程控制必须在业务单据所在的系统中执行 ok
1. 多条线执行时如果找不到下级节点时怎么办停止？此时可认为等待外界干预，干预后才可以继续执行（流程线上执行条件可以是业务单据上的数据） 
1. 后端执行应该没有操作部门和岗位？？相当于异步审批 ok
1. 打开每个节点打开详情界面可不配置，不配置时走单据穿透 ？ 只有提交工作流时才有按钮，其他地方按钮只在工作台上
1. 任务节点中是否需要参数对象？？bpm_data、节点配置中  不需要
1. 多个任务线有条件执行时，下游节点如何开始执行？？ 参考结论
1. 界面提交时添加界面进度条，参考审批提交  ok
1. 不需要配置操作部门和岗位的节点（起点、终点、后台执行的节点），只有前端交互的节点需要此配置 ok
1. 单据撤单版本备份等操作需要考虑这种情况吗？  不考虑
1. 流程运行过程中如果流程线修改了，此时有问题吧   先不管
