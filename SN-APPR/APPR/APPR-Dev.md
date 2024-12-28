## 审批链开发手册

|日期|作者|备注|
|------|------|------|
|2020-04-21|冀小雷|初版|

### 常用类

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th colspan="3" align="center">审批系统常用类</th>
	</tr>
	<tr>
		<th align="center">类名</th>
		<th align="center">方法名</th>
		<th align="center">说明</th>
	</tr>
	<tr>
		<th colspan="3">客户端(appr-xjs)</th>
	</tr>
	<tr>
		<th colspan="3">ApprSubmitCliListener(提交监听类)</th>
	</tr>
	<tr>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th colspan="3">XjsApprOrgService(提交服务，对应ApprOrgService)</th>
	</tr>
	<tr>
		<th colspan="3">TaskPerformListener(审批监听类)</th>
	</tr>
	<tr>
		<th colspan="3">WFCService(审批服务，对应UIWorkflowService)</th>
	</tr>
	<tr>
		<th colspan="3">RatifyDialogListener(审批对话框)</th>
	</tr>
	<tr>
		<th colspan="3">SubmitDialogListener(提交对话框)</th>
	</tr>
	<tr>
		<th colspan="3">接入层(appr-ui)</th>
	</tr>
	<tr>
		<th colspan="3">UITaskPerformListener(UI监听)</th>
	</tr>
	<tr>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th colspan="3">UIWorkflowService(审批接入服务)</th>
	</tr>
	<tr>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th colspan="3">服务层(appr-impl)</th>
	</tr>
	<tr>
		<th colspan="3">WFServiceContext(审批环境上下文)</th>
	</tr>
	<tr>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th colspan="3">WfSubmitUserSelector(选择待审人/转阅人/转审人)</th>
	</tr>
	<tr>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th colspan="3">WFSubmitAddionalUser(转阅逻辑处理)</th>
	</tr>
	<tr>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th colspan="3">WFSubmitServiceImpl(提交逻辑处理)</th>
	</tr>
	<tr>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th colspan="3">WFRejectServiceImpl(驳回逻辑处理)</th>
	</tr>
	<tr>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th colspan="3">WFRatifyServiceImpl(审批逻辑处理)</th>
	</tr>
	<tr>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th colspan="3">WFBackSubmitServiceImpl(取消提交逻辑处理)</th>
	</tr>
	<tr>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th colspan="3">业务交互层(appr-client)</th>
	</tr>
	<tr>
		<th colspan="3">ApprDataOrgService(审批与业务数据交互服务)</th>
	</tr>
	<tr>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th colspan="3">ApprClientService(客户端服务)</th>
	</tr>
	<tr>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr>
		<th colspan="3">WFExplService(审批描述服务)</th>
	</tr>
	<tr>
		<td></td>
		<td></td>
		<td></td>
	</tr>
</table>


### 结构说明

#### 审批工作台

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th colspan="4" align="center">支持的查询参数说明</th>
	</tr>
	<tr>
		<th align='center'>名称</th>
		<th align='center'>标题</th>
		<th align='center'>比较方式</th>
		<th align='center'>说明</th>
	</tr>
	<tr>
		<td>sheetcode</td>
		<td>单据类型</td>
		<td>等值匹配</td>
		<td>选择设置了审批链的单据类型(apprdata.sheetcode)</td>
	</tr>
	<tr>
		<td>outercode</td>
		<td>单据编号</td>
		<td>模糊匹配</td>
		<td>单据外码，如合同号、发货单号等(apprdata.outercode)</td>
	</tr>
	<tr>
		<td>submitdatefm</td>
		<td>提交日期从</td>
		<td>大于等于</td>
		<td>审批明细任务提交日期(tasklist.submitdate)</td>
	</tr>
	<tr>
		<td>submitdateto</td>
		<td>提交日期至</td>
		<td>小于等于</td>
		<td>审批明细任务提交日期(tasklist.submitdate)</td>
	</tr>
	<tr>
		<td>bcode</td>
		<td>单据部门</td>
		<td>路径匹配</td>
		<td>业务单据所属部门，支持中间级(apprdata.bcode)</td>
	</tr>
	<tr>
		<td>wcode</td>
		<td>单据人员</td>
		<td>等值匹配</td>
		<td>业务单据所属人员(apprdata.wcode)</td>
	</tr>
	<tr>
		<td>taskflag</td>
		<td>任务标识</td>
		<td>等值匹配</td>
		<td>非空必录(tasklist.taskflag)：<br/>
			1=待审;
			2=已审;
			3=待阅;
			4=已阅
		</td>
	</tr>
	<tr>
		<td>submitucode</td>
		<td>提交人</td>
		<td>等值匹配</td>
		<td>任务提交人(tasklist.submitucode)</td>
	</tr>
	<tr>
		<td>performucode</td>
		<td>审批人</td>
		<td>等值匹配</td>
		<td>任务审批人(tasklist.performucode)</td>
	</tr>
</table>

#### 常用码表说明

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th colspan="3" align="center">常用码表</th>
	</tr>
	<tr>
		<th rowspan="6">审批方式</th>
		<td>0=会审</td>
		<td>所有可选待审人必须全部审核通过才可以进行到下一个节点</td>
	</tr>
	<tr>
		<td>1=单审</td>
		<td>所有选中的待审人只要有一个审核通过即可进行到下一个节点</td>
	</tr>
	<tr>
		<td>2=选审</td>
		<td>所有选中的待审人必须全部审核通过才可以进行到下一个节点</td>
	</tr>
	<tr>
		<td>3=选单审</td>
		<td>所有选中的待审人只要有一个审核通过即可进行到下一个节点</td>
	</tr>
	<tr>
		<td>4=单选</td>
		<td>从可选的待审人中选择一个人进行审核</td>
	</tr>
	<tr>
		<td>5=按组会审</td>
		<td>通过【审批人】子表列【会审组】设置会审组别</td>
	</tr>
	<tr>
		<th rowspan="3">单元类型</th>
		<td>prepare=起点</td>
		<td>一般对应单据的可编辑状态（10=草拟、11=修改、15=驳回等）</td>
	</tr>
	<tr>
		<td>ratify=审批</td>
		<td>待审节点，需要设置审批人</td>
	</tr>
	<tr>
		<td>perform=终点</td>
		<td>审批结束，一般对应单据的生效[70]/预生效[69]状态</td>
	</tr>
	<tr>
		<th rowspan="4">提交方式</th>
		<td>0=提交</td>
		<td>首次提交/审批同意后的提交</td>
	</tr>
	<tr>
		<td>1=驳回</td>
		<td>驳回产生的新审批记录</td>
	</tr>
	<tr>
		<td>2=转审</td>
		<td>转审产生的待审记录</td>
	</tr>
	<tr>
		<td>3=转审返回</td>
		<td>【转审同意后返回审批人】生成的记录</td>
	</tr>
	<tr>
		<th rowspan="8">审批状态</th>
		<td>0=待审</td>
		<td>提交的待审记录</td>
	</tr>
	<tr>
		<td>1=同意</td>
		<td>审批同意</td>
	</tr>
	<tr>
		<td>2=不同意</td>
		<td>参考其他人意见</td>
	</tr>
	<tr>
		<td>3=上报</td>
		<td>保留意见并上报</td>
	</tr>
	<tr>
		<td>4=驳回</td>
		<td>直接驳回</td>
	</tr>
	<tr>
		<td>16=本级</td>
		<td>本级审批通过</td>
	</tr>
	<tr>
		<td>32=全部</td>
		<td>全部审批通过</td>
	</tr>
	<tr>
		<td>5=转审</td>
		<td>转审</td>
	</tr>
	<tr>
		<th colspan="3" align="center">审批工作台</th>
	</tr>
	<tr>
		<td>游离（taskflag=0）</td>
		<td colspan='2'>
			驳回至草拟<br/>
			本级次其他人审核(同意/驳回)<br/>
		</td>
	</tr>
	<tr>
		<td>待审（taskflag=1）</td>
		<td colspan='2'>
			待审人尚未审核：<br/>
			首次提交<br/>
			审核提交<br/>
			取消审批提交后（单个审批人）<br/>
			取消审批后（多个审批人）<br/>
			被驳回至中间级（单个）<br/>
			被驳回至中间级（多个）<br/>
			转审生成的记录<br/>
		</td>
	</tr>
	<tr>
		<td>已审（taskflag=2）</td>
		<td colspan='2'>
			待审人已经审核或无需审核：<br/>
			同意<br/>
			驳回<br/>
			转审<br/>
			保留意见并上报<br/>
			提交至生效(直接生效+审批后生效)<br/>
		</td>
	</tr>
	<tr>
		<td>待阅（taskflag=3）</td>
		<td colspan='2'></td>
	</tr>
	<tr>
		<td>已阅（taskflag=4）</td>
		<td colspan='2'></td>
	</tr>
</table>

#### 审批记录主表结构

<table>
	<tr>
		<th colspan="2" align="center">审批记录表结构[apprdata]</th>
	</tr>
	<tr>
		<td>cuicode</td>
		<td>主键：商户编码
		</td>
	</tr>
	<tr>
		<td>taskid</td>
		<td>主键：审批任务ID
		</td>
	</tr>
	<tr>
		<td>version</td>
		<td>主键：版本号
		</td>
	</tr>
	<tr>
		<td>apprsign</td>
		<td>标识</td>
	</tr>
	<tr>
		<td>sheetcode</td>
		<td>单据编码</td>
	</tr>
	<tr>
		<td>wfcode</td>
		<td>主流程号</td>
	</tr>
	<tr>
		<td>innercode</td>
		<td>单据内码</td>
	</tr>
	<tr>
		<td>outercode</td>
		<td>单据外码</td>
	</tr>
	<tr>
		<td>status</td>
		<td>单据提交状态：<br/>
		10：草拟（一般单据）<br/>
		11：修改（备份单据）<br/>
		</td>
	</tr>
	<tr>
		<td>predate</td>
		<td>单据创建时间</td>
	</tr>
	<tr>
		<td>odate</td>
		<td>单据业务日期</td>
	</tr>
	<tr>
		<td>bcode</td>
		<td>部门</td>
	</tr>
	<tr>
		<td>wcode</td>
		<td>人员</td>
	</tr>
	<tr>
		<td>corpbcode</td>
		<td>公司</td>
	</tr>
	<tr>
		<td>costbcode</td>
		<td>核算组</td>
	</tr>
	<tr>
		<td>ccode</td>
		<td>客户</td>
	</tr>
	<tr>
		<td>smdate</td>
		<td>提交日期</td>
	</tr>
	<tr>
		<td>smucode</td>
		<td>提交人</td>
	</tr>
	<tr>
		<td>displaytext</td>
		<td>展示信息</td>
	</tr>
	<tr>
		<td>opensheeturl</td>
		<td>打开地址</td>
	</tr>
	<tr>
		<td>kvmap</td>
		<td>kvmap</td>
	</tr>
	<tr>
		<td>finishdate</td>
		<td>完成时间</td>
	</tr>
	<tr>
		<td>modifydate</td>
		<td>修改时间</td>
	</tr>
</table>

#### 审批记录表结构

<table>
	<tr>
		<th colspan="2" align="center">审批记录表结构[apprtasklist]</th>
	</tr>
	<tr>
		<td>cuicode<br/>taskid<br/>version<br/></td>
		<td>主键(拷贝主表apprdata)
		</td>
	</tr>
	<tr>
		<td>idx</td>
		<td>待审批次号，同一提交或驳回中间级共享一个批次</td>
	</tr>
	<tr>
		<td>ucode</td>
		<td>待审人(usercode)：<br/>
		1. 驳回至草拟状态：\*；<br/>
		2. 提交至生效状态：\*；<br/>
		</td>
	</tr>
	<tr>
		<td>uidx</td>
		<td>同一待审批次内待审用户排序号，从1开始</td>
	</tr>
	<tr>
		<td>pidx</td>
		<td>上一个待审批次号</td>
	</tr>
	<tr>
		<td>gwcode</td>
		<td>按照岗位指定待审人时的岗位编码</td>
	</tr>
	<tr>
		<td>wfcode</td>
		<td>审批编码</td>
	</tr>
	<tr>
		<td>puid</td>
		<td>审批链开始节点</td>
	</tr>
	<tr>
		<td>wfuid</td>
		<td>审批链当前节点</td>
	</tr>
	<tr>
		<td>swfcode</td>
		<td>转审流程号
		</td>
	</tr>
	<tr>
		<td>swfidx</td>
		<td>转审批次号
		</td>
	</tr>
	<tr>
		<td>sheetcode</td>
		<td>单据类型
		</td>
	</tr>
	<tr>
		<td>taskname</td>
		<td>单据类型名称-当前节点名称</td>
	</tr>
	<tr>
		<td>tasktitle</td>
		<td>当前任务节点标题<br/>
		1. 可以通过审批监听设置；<br/>
		2. 可以通过审批属性【TaskTitleFMT】设置；<br/>
		3. 默认取审批名称[wfname]；<br/>
		</td>
	</tr>
	<tr>
		<td>taskexpl</td>
		<td>提交说明1
		</td>
	</tr>
	<tr>
		<td>taskexpl2</td>
		<td>提交说明2
		</td>
	</tr>
	<tr>
		<td>rmode</td>
		<td>审批方式(会审、单审等)
		</td>
	</tr>
	<tr>
		<td>sheetkv</td>
		<td>单据关键字段
		</td>
	</tr>
	<tr>
		<td>sheetvcode</td>
		<td>单据外码值
		</td>
	</tr>
	<tr>
		<td>grpid</td>
		<td>同审批批次ID
		</td>
	</tr>
	<tr>
		<td>predate</td>
		<td>创建时间</td>
	</tr>
	<tr>
		<td>submitmode</td>
		<td>提交方式，参见[常用码表说明](#常用码表说明)<br/>
		</td>
	</tr>
	<tr>
		<td>submitdate</td>
		<td>提交时间</td>
	</tr>
	<tr>
		<td>submitucode</td>
		<td>提交人(当前登录人)</td>
	</tr>
	<tr>
		<td>submitucode0</td>
		<td>提交人(上一级待审人)</td>
	</tr>
	<tr>
		<td>submitucode1</td>
		<td>提交人(审批提交人)</td>
	</tr>
	<tr>
		<td>performdate</td>
		<td>审批时间</td>
	</tr>
	<tr>
		<td>performucode</td>
		<td>审批人</td>
	</tr>
	<tr>
		<td>performexpl</td>
		<td>审批说明</td>
	</tr>
	<tr>
		<td>performstat</td>
		<td>审批操作(同意/驳回/转审/上报)</td>
	</tr>
	<tr>
		<td>performhost</td>
		<td>客户端IP</td>
	</tr>
	<tr>
		<td>performshost</td>
		<td>服务端IP</td>
	</tr>
	<tr>
		<td>rflags</td>
		<td><font color='red'><b>驳回标识</b></font></td>
	</tr>
	<tr>
		<td>fromstatus</td>
		<td>起始节点单据状态</td>
	</tr>
	<tr>
		<td>vstatus</td>
		<td>起始节点单据状态</td>
	</tr>
	<tr>
		<td>modifier</td>
		<td>修改人</td>
	</tr>
	<tr>
		<td>modifydate</td>
		<td>修改时间</td>
	</tr>
	<tr>
		<td>lockucode</td>
		<td>锁定人</td>
	</tr>
	<tr>
		<td>lockdate</td>
		<td>锁定时间</td>
	</tr>
	<tr>
		<td>locksinfo</td>
		<td>锁定说明</td>
	</tr>
	<tr>
		<td>adduinfo</td>
		<td><font color='red'>附加审批人明细</font></td>
	</tr>
	<tr>
		<td>warnexpl</td>
		<td>警告信息说明</td>
	</tr>
	<tr>
		<td>warntype</td>
		<td>警告审批任务类型</td>
	</tr>
	<tr>
		<td>ucodegrp</td>
		<td>审批人组</td>
	</tr>
	<tr>
		<td>ugpassrate</td>
		<td>审批人组通过率</td>
	</tr>
</table>

### 审批接口说明

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th colspan="5" align="center">审批接口说明</th>
	</tr>
	<tr>
		<th align="center">类型</th>
		<th align="center">接口</th>
		<th align="center">输入</th>
		<th align="center">输出</th>
		<th align="center">说明</th>
	</tr>
	<tr>
		<td>总体说明</td>
		<td colspan="4">
			wfcode:审批编号<br/>
			taskid:审批任务ID（规则【单据类型.单据内码】）
			ucode:审批用户
		</td>
	</tr>
	<tr>
		<th rowspan="1">公共方法</th>
		<td>获取审批配置<br/>UIWorkflowService.loadWorkflow</td>
		<td>
			String wfcode:审批编号(非空)<br/>
		</td>
		<td>
			审批配置JSON对象<br/>
			{wfcode:"",<br/>
			wfoptions:0,<br/>
			kcolumns:[],<br/>
			wfname:"",<br/>
			unitsCfg:[{}],<br/>
			startBU:"10",<br/>
			msgoptions:0<br/>
			}<br/>
		</td>
		<td></td>
	</tr>
	<tr>
		<th rowspan="3">提交</th>
		<td>准备提交数据<br/>ApprOrgService.xjsPerformApprData</td>
		<td>
			String sheetcode:单据类型(非空)<br/>
			String innercode:单据内码(非空)<br/>
			String rootUIid:提交界面号<br/>
			Date modifydate:单据修改时间<br/>
		</td>
		<td>
			{args:["wfcode","taskid"],<br/>
			method:"snsoft.wf.WFTaskSubmit.showSubmit"}<br/>
			snsoft.wf.WFTaskSubmit.showSubmit:客户端弹出框<br/>
		</td>
		<td></td>
	</tr>
	<tr>
		<td>获取提交信息<br/>UIWorkflowService.getSubmitPerformParams</td>
		<td>
			{wfcode:"wfcode",<br/>
			taskid:"taskid"}<br/>
		</td>
		<td>
			{info:null,<br/>
			info1:null,<br/>
			refreshCurRecord:false,<br/>
			refreshMainTblIRVals:null,<br/>
			refreshTaskid:null,<br/>
			submitParams:[{<br/>
			　uid:"10",<br/>
			　touid:"30",<br/>
			　aname:"",<br/>
			　rmode:2,<br/>
			　taskexpl0:"wfname",<br/>
			　toucodes:["usercode@gwcode:username"]<br/>
			}]}<br/>
		</td>
		<td>
		</td>
	</tr>
	<tr>
		<td>执行提交<br/>ApprOrgService.xjsPerformSubmit</td>
		<td>
			{wfcode:"wfcode",<br/>
			taskid:"taskid",<br/>
			fromuid:"10",<br/>
			touid:"30",<br/>
			submitconfirmed:true,<br/>
			taskexpl:null,<br/>
			msgoptions:0,<br/>
			toucodes:["usercode@gwcode"]}<br/>
		</td>
		<td>
			{info:null,<br/>
			info1:"当前单据提交到“uname”状态(审批人员:username)",<br/>
			refreshCurRecord:true,<br/>
			refreshMainTblIRVals:null,<br/>
			refreshTaskid:null,<br/>
			submitParams:null}<br/>
		</td>
		<td>
		</td>
	</tr>
	<tr>
		<th>取消提交</th>
		<td>执行取消<br/>UIWorkflowService.performBackSubmit</td>
		<td>
			{<br/>
			wfcode:"",<br/>
			taskid:"",<br/>
			ucode:""--取消人<br/>
			idx:1,--取消节点序号<br/>
			}<br/>
		</td>
		<td>
			{<br/>
			info:null,<br/>
			info1:"单据状态回退到:草拟状态",<br/>
			refreshCurRecord:true,<br/>
			refreshMainTblIRVals:null,<br/>
			refreshTaskid:"",<br/>
			submitParams:null<br/>
			}
		</td>
		<td></td>
	</tr>
	<tr>
		<th rowspan="2">驳回</th>
		<td>获取驳回节点<br/>UIWorkflowService.getRejectIdxListParams</td>
		<td>
			String taskid
		</td>
		<td>
			[<br/>
				{uid:"10",toucodes:null,idx:0,title:"单据可修改状态"},<br/>
				{uid:"30",toucodes:null,idx:1,title:"主管审核"}<br/>
			]<br/>
		</td>
		<td></td>
	</tr>
	<tr>
		<td>执行驳回<br/>UIWorkflowService.performReject</td>
		<td>
			{<br/>
			wfcode:"",<br/>
			taskid:"",<br/>
			ucode:"",<br/>
			idx:1,<br/>
			rejectToIdx:0,<br/>
			performstat:4,<br/>
			performexpl:"fdsfasdfs",<br/>
			taskexpl:"多链条"<br/>
			}
		</td>
		<td>
			{<br/>
			info:null,<br/>
			info1:"单据状态回退到:草拟状态",<br/>
			refreshCurRecord:true,<br/>
			refreshMainTblIRVals:null,<br/>
			refreshTaskid:"",<br/>
			submitParams:null<br/>
			}
		</td>
		<td></td>
	</tr>
	<tr>
		<th rowspan="7">审批</th>
		<td>审批参数<br/>UIWorkflowService.prepareRatifyParameter</td>
		<td>
			String taskid<br/>
			String wfcode<br/>
			String bu<br/>
		</td>
		<td>
			{<br/>
				taskinfo:[{}],//审批描述<br/>
				xrUsers1:[""],//手动转阅<br/>
				xrUsers2:[""]//自动转阅<br/>
			}
		</td>
		<td></td>
	</tr>
	<tr>
		<td>获取关检列值<br/>UIWorkflowService.getApprKeyValues</td>
		<td>
			String taskid<br/>
		</td>
		<td>
			{kcol:value}
		</td>
		<td></td>
	</tr>
	<tr>
		<td>获取审批详情<br/>#loadTaskListAsArray</td>
		<td>
			String taskid<br/>
		</td>
		<td>
			[{详情记录}]
		</td>
		<td></td>
	</tr>
	<tr>
		<td>同意<br/>UIWorkflowService.getSubmitPerformParams</td>
		<td>
			{<br/>
			wfcode:""<br/>
			taskid:"",<br/>
			idx:1,--记录序号<br/>
			ucode:"U000000007",--审批人<br/>
			performstat:1,--同意<br/>
			performexpl:"同意",--审批说明<br/>
			taskexpl:"多链条",--审批描述<br/>
			}
		</td>
		<td>
			{info:"当前人员已完成审批,需要继续审批的人员:\r\n张博",<br/>
			info1:null,<br/>
			refreshCurRecord:false,<br/>
			refreshMainTblIRVals:null,<br/>
			refreshTaskid:null,<br/>
			submitParams:null}
		</td>
		<td></td>
	</tr>
	<tr>
		<td>取消审批<br/>UIWorkflowService.clearPerformState</td>
		<td>
			{<br/>
			wfcode:""<br/>
			taskid:"",<br/>
			ucode:"",<br/>
			idx:1,<br/>
			performstat:0,<br/>
			performexpl:"同意",<br/>
			taskexpl:"多链条",<br/>
			}
		</td>
		<td>
			{<br/>
			info:null,<br/>
			info1:"审批状态被清除",<br/>
			refreshCurRecord:false,<br/>
			refreshMainTblIRVals:null,<br/>
			refreshTaskid:"",<br/>
			submitParams:null<br/>
			}
		</td>
		<td></td>
	</tr>
	<tr>
		<td>同意并提交待审<br/>UIWorkflowService.performSubmit</td>
		<td>
			{<br/>
			wfcode:"",<br/>
			taskid:"",<br/>
			ucode:"",<br/>
			idx:1,<br/>
			performstat:1,<br/>
			performexpl:"同意",<br/>
			fromuid:"30",<br/>
			touid:"31",<br/>
			submitconfirmed:true,<br/>
			toucodes:["U000000004@SN002"],<br/>
			taskexpl:"多链条",<br/>
			msgoptions:0<br/>
			}
		</td>
		<td>
			{info:null,info1:"当前单据提交到“经理”状态(审批人员:李叶胜)",<br/>
			refreshCurRecord:true,<br/>
			refreshMainTblIRVals:null,<br/>
			refreshTaskid:null,<br/>
			submitParams:null}
		</td>
		<td></td>
	</tr>
	<tr>
		<td>同意并提交生效<br/>UIWorkflowService.performSubmit</td>
		<td>
			{<br/>
			wfcode:"",<br/>
			taskid:"",<br/>
			ucode:"",<br/>
			idx:2,<br/>
			performstat:1,<br/>
			performexpl:"同意",<br/>
			fromuid:"31",<br/>
			touid:"70",<br/>
			submitconfirmed:true,<br/>
			toucodes:["*"],<br/>
			taskexpl:"多链条"<br/>
			}
		</td>
		<td>
			{info:null,<br/>
			info1:"当前单据提交到“生效”状态",<br/>
			refreshCurRecord:true,<br/>
			refreshMainTblIRVals:null,<br/>
			refreshTaskid:null,<br/>
			submitParams:null}
		</td>
		<td></td>
	</tr>
</table>

### 审批功能场景

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th colspan="3" align="center">审批功能场景分析</th>
	</tr>
	<tr>
		<th align="center">大类</th>
		<th align="center">子类</th>
		<th align="center">说明</th>
	</tr>
	<tr>
		<th rowspan="8">提交</th>
		<th rowspan="4">首次提交</th>
		<td>10->70：提交生效</td>
	</tr>
	<tr>
		<td>找不到审批人</td>
	</tr>
	<tr>
		<td>找到一个审批人</td>
	</tr>
	<tr>
		<td>找到多个审批人</td>
	</tr>
	<tr>
		<th rowspan='4'>审批后提交</th>
		<td>提交至生效</td>
	</tr>
	<tr>
		<td>找不到审批人</td>
	</tr>
	<tr>
		<td>找到一个审批人</td>
	</tr>
	<tr>
		<td>找到多个审批人</td>
	</tr>
	<tr>
		<th rowspan="6">取消提交</th>
		<th rowspan="3">取消首次提交</th>
		<td>已经审批</td>
	</tr>
	<tr>
		<td>选项【禁止取消提交】</td>
	</tr>
	<tr>
		<td>成功取消提交</td>
	</tr>
	<tr>
		<th rowspan="3">取消审批提交</th>
		<td>已经审批</td>
	</tr>
	<tr>
		<td>选项【禁止取消提交】</td>
	</tr>
	<tr>
		<td>成功取消提交</td>
	</tr>
	<tr>
		<th rowspan="4">驳回</th>
		<td colspan='2'>驳回至草拟</td>
	</tr>
	<tr>
		<td colspan='2'>驳回至中间级</td>
	</tr>
	<tr>
		<td colspan='2'>选项【禁止驳回】</td>
	</tr>
	<tr>
		<td colspan='2'>选项【禁止驳回到中间级】</td>
	</tr>
	<tr>
		<th rowspan="4">审批</th>
		<th rowspan="1">同意</th>
		<td></td>
	</tr>
	<tr>
		<th rowspan="1">转审</th>
		<td></td>
	</tr>
	<tr>
		<th rowspan="1">保留意见上报</th>
		<td></td>
	</tr>
	<tr>
		<th rowspan="1">取消审批</th>
		<td></td>
	</tr>
	<tr>
		<th rowspan="4">待审人</th>
		<td colspan='2'>指定用户</td>
	</tr>
	<tr>
		<td colspan='2'>指定岗位</td>
	</tr>
	<tr>
		<td colspan='2'>指定岗位+部门</td>
	</tr>
	<tr>
		<td colspan='2'>指定用户+数据部门</td>
	</tr>
	<tr>
		<th rowspan="11">审批方式</th>
		<th rowspan='3'>会审</th>
		<td>一个待审人</td>
	</tr>
	<tr>
		<td colspan='2'>多个待审人</td>
	</tr>
	<tr>
		<td colspan='2'>会审通过比例</td>
	</tr>
	<tr>
		<th rowspan='2'>单审</th>
		<td>一个待审人</td>
	</tr>
	<tr>
		<td colspan='2'>多个待审人</td>
	</tr>
	<tr>
		<th rowspan='2'>选审</th>
		<td>一个待审人</td>
	</tr>
	<tr>
		<td colspan='2'>多个待审人</td>
	</tr>
	<tr>
		<th rowspan='2'>选单审</th>
		<td>一个待审人</td>
	</tr>
	<tr>
		<td colspan='2'>多个待审人</td>
	</tr>
	<tr>
		<th>单选</th>
		<td>一个待审人</td>
	</tr>
	<tr>
		<th rowspan='1'><font color='red'>按组会审</font></th>
		<td>？？</td>
	</tr>
	<tr>
		<th rowspan="1">转审</th>
		<th rowspan='1'></th>
		<td></td>
	</tr>
	<tr>
		<th rowspan="11">单据审批关系</th>
		<td colspan='2'>默认匹配</td>
	</tr>
	<tr>
		<td colspan='2'>部门匹配</td>
	</tr>
	<tr>
		<td colspan='2'>匹配多个</td>
	</tr>
	<tr>
		<td colspan='2'>审批链停用，已提交记录继续使用原审批链进行</td>
	</tr>
	<tr>
		<td colspan='2'>匹配到新审批链，已提交记录继续使用原审批链进行</td>
	</tr>
</table>


### 使用说明


#### 开发注意

单据VO：实现 ApprSheetVO 接口
单据存取服务：实现 SheetApprAccessService 接口

#### 提交功能

```xml
<!--
提交审批
界面定义配置在需要审批的单据主表界面上增加js监听
-->
<!--提交按钮绑定：-->
<ToolbarBtn name="{UIName}_btn_submitappr" title="提交"  noClientComponent="true" uiprops.cellClassName="ui-head-btn" xprops.iconClassName="icons-btn-submit" />

<jslistener>
	#new snsoft.wf.busi.ApprSubmitCliListener({})
</jslistener>

<!-- 系统功能配置 -->
    <Groups id="审批">
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.wf.busi.ApprSubmitCliListener({"tgtUINames":["界面名称"]})</Funcimpl>
            <Remark>审批链-打开审批详情</Remark>
        </Functions>
    </Groups>
```

#### 审批功能

```xml
<!--
任务审批界面定义配置在需要审批的单据主表界面上
-->
<uilisteners>
	snsoft.approval.ui.UITaskPerformListener.new
</uilisteners>
<jslistener>
	#new snsoft.wf.TaskPerformListener({disableButtons:1,addOpenSheetBtn:false})
</jslistener>

<!--按钮绑定：-->
<ToolbarBtn name="{UIName}_btn_ratify" title="审批"  noClientComponent="true" uiprops.cellClassName="ui-head-btn" xprops.iconClassName="icons-btn-approve" />
<ToolbarBtn name="{UIName}_btn_backsubmit" title="取消提交"  noClientComponent="true" uiprops.cellClassName="ui-head-btn" xprops.iconClassName="icons-btn-recall" />

<!-- 系统功能配置 -->
<Groups id="审批">
	<Functions>
	    <Functype>SN-PLAT.JS</Functype>
	    <Subtype>2</Subtype>
	    <Funcimpl>#new snsoft.wf.TaskPerformListener({disableButtons:1,addOpenSheetBtn:false})</Funcimpl>
	    <Remark>审批链-审批</Remark>
	</Functions>
	<Functions>
	    <Functype>SN-PLAT.UI</Functype>
	    <Subtype>2</Subtype>
	    <Funcimpl>
	        snsoft.approval.ui.UITaskPerformListener.new
	    </Funcimpl>
	    <Remark>审批链-审批</Remark>
	</Functions>
</Groups>
```

#### 审批情况

* 入口配置

```xml

<!-- 入口按钮 -->

<ToolbarBtn name="界面名称_btn_queryappr" title="审批情况"
                    noClientComponent="true" xprops.iconClassName="icons-btn-open"/>

<!-- 系统功能 -->
    <Groups id="审批">
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.wf.busi.ApprSubmitCliListener({"tgtUINames":["界面名称"],"apprUiid":"SN-APPR.PlatTasklist"})</Funcimpl>
            <Remark>审批链-打开审批详情</Remark>
        </Functions>
    </Groups>
```

* 详情页配置

```xml

<!-- 界面定义 -->

<m:P title="审批情况">
		<m:DialogPane hidden="true" layoutm="grid" name="taskParam" region="north" title="审批结果">
			<c name="taskid" noblank="true" sqltype="12" title="审批ID"/>
		</m:DialogPane>
		<m:T region="center" name="taskTbl"  uiprops.cellClassName="ui-tabpane-level2">
			<include src="SN-APPR.PlatTasklist#apprdata" mainui="taskParam"/>
			<m:P title="审批流程" name="taskflow"></m:P>
			<m:P title="审批路线" name="taskroute"></m:P>
		</m:T>
	</m:P>

<!-- 系统功能 -->

        <Groups id="审批">
            <Functions>
                <Functype>SN-PLAT.JS</Functype>
                <Subtype>2</Subtype>
                <Funcimpl>#new snsoft.wf.busi.ApprSubmitCliListener({"tgtUINames":["界面名称"],"taskTblName":"apprdata"})</Funcimpl>
                <Remark>审批链-提交</Remark>
            </Functions>
        </Groups>

```

#### 其他配置

* system.properties

1. 配置审批客户端服务:

```
	SN-CORE.Group.Name=SN-PLAT,SN-HELP（系统号 ，分割）
	替换 ApprDataOrgService服务中的 SnsoftConst.CommonGroup
	@SpringBean(name = ApprDataOrgService.BeanName, group = SnsoftConst.CommonGroup)
```

1. 配置审批系统mongo数据库地址:

```
	SN-APPR.Comm.mongo=true
	SN-APPR.Comm.mongo.uri=mongodb://127.0.0.1:27017/N9B
```

* pom依赖

1. 业务系统IMPL 依赖 snadk-appr-client

```
	<dependency>
			<groupId>${project.groupId}</groupId>
			<artifactId>snadk-appr-client</artifactId>
			<version>${snadk.appr.version}</version>
	</dependency>
```

1. 业务系统UI服务 增加 snadk-appr-ui

```
	<dependency>
			<groupId>${project.groupId}</groupId>
			<artifactId>snadk-appr-ui</artifactId>
			<version>${snadk.appr.version}</version>
	</dependency>
```

* 缓存规则

||码表缓存|启用缓存|对象缓存|
|-|-|-|-|
|启用商户|[workflowm]&#124;\*${cuicode}\*|[workflowm]&#124;wfcodeAll-${cuicode}|[workflowm]&#124;${cuicode}-${wfcode}|
|不用商户|[workflowm]&#124;|[workflowm]&#124;wfcodeAll-|[workflowm]&#124;-${wfcode}|
