## 审批链定义

|日期|作者|备注|
|------|------|------|
|2020-04-22|冀小雷|重写|

参见[审批链开发手册](APPR-Dev.md)。
参见[审批链实施手册](APPR-Ops.md)。

### 指南

#### 概念说明

审批系统是用来对定义好的审批链进行审批的功能。
审批链是定义一系列审批节点、审批操作、审批人的功能。

### 手册

#### 审批链定义说明

##### 界面列说明

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th colspan="2" align="center">审批链定义操作说明</th>
	</tr>
	<tr>
		<th align="center">列</th>
		<th align="center">说明</th>
	</tr>
	<tr>
		<th colspan="2">操作按钮</th>
	</tr>
	<tr>
		<td>复制</td>
		<td>
			1. 对原审批定义有查询[R]权限，有新建[C]审批定义的权限才可以使用此功能；<br/>
			2. 复制的新审批编号为【原审批编号-NEW】，并立即刷新页面，然后手动修改审批编号；<br/>
			3. 如果新审批定义编号尚未修改，则不允许再次复制，否则报错【记录已存在】；
		</td>
	</tr>
	<tr>
		<td>启用</td>
		<td>
			1. 使用条件：对当前审批定义有操作[C]权限且未启用；<br/>
			2. 检查并提示：<br/>
			（1）审批单元为空提示；<br/>
			（2）审批操作为空提示；<br/>
			（3）【审批】单元没有设置审批人员提示；<br/>
			（4）【可转审】单元没有设置转审人员提示；<br/>
			3. 确定后将审批链定义修改为启用；
		</td>
	</tr>
	<tr>
		<td>取消启用</td>
		<td>
			1. 使用条件：对当前审批定义有操作[C]权限且已启用；<br/>
			2. 检查并提示：已经使用的审批任务个数及待审的任务个数；<br/>
			3. 确定后将审批链定义修改为未启用；
		</td>
	</tr>
	<tr>
		<td>停用</td>
		<td>已经启用并被业务单据使用的审批链，不能取消启用并删除；<br/>
			此时可以停用，已经使用的继续使用，新的单据不再使用；
		</td>
	</tr>
	<tr>
		<td>取消停用</td>
		<td>业务单据可以继续使用该审批链</td>
	</tr>
	<tr>
		<td>生成默认单元</td>
		<td>
			1. 使用条件：对当前审批定义有操作[C]权限且未启用；<br/>
			2. 检查：如果已经存在审批单元则不处理；<br/>
			3. 生成【10=草拟】节点及【70=生效】节点；
		</td>
	</tr>
	<tr>
		<td>生成默认操作</td>
		<td>
			1. 使用条件：对当前审批定义有操作[C]权限且未启用；<br/>
			2. 检查：如果已经存在审批操作则不处理；<br/>
			3. 顺序生成两个节点之间的操作；
		</td>
	</tr>
	<tr>
		<th colspan="2">审批链定义列说明</th>
	</tr>
	<tr>
		<td>启用</td>
		<td>可以通过单据关系引用
		</td>
	</tr>
	<tr>
		<td>停用</td>
		<td>保持历史关系，不可以被新的单据引用
		</td>
	</tr>
	<tr>
		<td>审批编号</td>
		<td>审批定义编号
		</td>
	</tr>
	<tr>
		<td>审批名称</td>
		<td>审批定义名称
		</td>
	</tr>
	<tr>
		<td>运维部门</td>
		<td>维护此审批链的运维部门，用于权限控制
		</td>
	</tr>
	<tr>
		<td>事件</td>
		<td>审批链监听，与系统选项【SN-APPR.WF.LISTENERS】联合使用：<br/>
		1. 多个使用分号[;]分隔；<br/>
		2 通过【@BeanName】的方式指定Bean。
		</td>
	</tr>
	<tr>
		<td>公式</td>
		<td>审批链监听对应的Tac脚本，参见[事件/公式](#事件/公式)。<br/>
		e.dataMap中的信息同【条件表达式】
		</td>
	</tr>
	<tr>
		<td>选项-自动生成流程操作</td>
		<td>可以不用设置【审批操作】（即节点间的流程关系）<br/>
		系统根据【审批单元】编码升序后自动建立相邻两个节点之间的关系
		</td>
	</tr>
	<tr>
		<td>选项-禁止驳回到中间级</td>
		<td>驳回后直接驳回到【草拟】状态<br/>
		否则如果存在中间级，则会弹出选择驳回节点窗口。
		</td>
	</tr>
	<tr>
		<th colspan="2">审批单元列说明</th>
	</tr>
	<tr>
		<td>单元编码</td>
		<td>10,30,...,70等，与业务状态无关
		</td>
	</tr>
	<tr>
		<td>单元名称</td>
		<td>草拟，待审，主管，财务...生效等
		</td>
	</tr>
	<tr>
		<td>选项1</td>
		<td>
			可转审<br/>
			禁止驳回<br/>
			禁止返回<br/>
			提交时不含已审人员<br/>
			禁止驳回到中间级<br/>
		</td>
	</tr>
	<tr>
		<td>选项2</td>
		<td>
		</td>
	</tr>
	<tr>
		<td>单元类型</td>
		<td>
			起点：审批起始单元<br/>
			审批：审批节点<br/>
			终点：审批终止节点<br/>
			参见[常用码表说明](APPR-Dev.md#常用码表说明)
		</td>
	</tr>
	<tr>
		<td>审批方式</td>
		<td>
		参见[常用码表说明](APPR-Dev.md#常用码表说明)
		</td>
	</tr>
	<tr>
		<td>审批状态</td>
		<td>
			当前节点对应的业务单据状态：<br/>
			起点：默认10/15；<br/>
			审批：默认30；<br/>
			终点：默认70，异步时设置为69；<br/>
		</td>
	</tr>
	<tr>
		<td>转审状态</td>
		<td>
			当前转审节点对应的业务状态。
		</td>
	</tr>
	<tr>
		<td>单据类型<br/>
		操作编码-审批<br/>
		操作编码-提交<br/>
		</td>
		<td>
			用于控制当前节点操作的权限<br/>
			1. 首次提交默认使用业务单据的C权限；<br/>
			2. 【操作编码-审批】：用于配置当前节点的审批权限（起始节点只有提交权限）；<br/>
			3. 【操作编码-提交】：用于配置当前节点的提交权限；<br/>
		</td>
	</tr>
	<tr>
		<td>消息选项</td>
		<td>
		</td>
	</tr>
	<tr>
		<td>会审通过比例</td>
		<td>
		四舍五入
		</td>
	</tr>
	<tr>
		<th colspan="2">审批人员列说明</th>
	</tr>
	<tr>
		<td>序号</td>
		<td>排序
		</td>
	</tr>
	<tr>
		<td>审批类型</td>
		<td>
			U：按用户指定；<br/>
			G：按岗位指定；<br/>
		</td>
	</tr>
	<tr>
		<td>用户</td>
		<td>
			【审批类型】为U时选择用户（$WBCODE单据属主）<br/>
			【审批类型】为G时选择岗位<br/>
		</td>
	</tr>
	<tr>
		<td>用户适用部门</td>
		<td>用于限定待审用户所属部门</td>
	</tr>
	<tr>
		<td>单据适用部门[值或宏]</td>
		<td>用于限定业务单据所属部门
		</td>
	</tr>
	<tr>
		<td>转阅|手动</td>
		<td>当前行设置的用户为手动转阅用户
		</td>
	</tr>
	<tr>
		<td>转阅|自动</td>
		<td>当前行设置的用户为自动转阅用户
		</td>
	</tr>
	<tr>
		<td>审批组|审批组</td>
		<td>转审方式为【按组会审】时用于设置组别
		</td>
	</tr>
	<tr>
		<td>审批组|通过比例</td>
		<td>转审方式为【按组会审】时用于设置当前组通过比例（四舍五入）
		</td>
	</tr>
	<tr>
		<td>转审|转审组</td>
		<td>转审对应的转审组别
		</td>
	</tr>
	<tr>
		<td>转审|审批方式</td>
		<td>转审组审批方式（会审/单审）
		</td>
	</tr>
	<tr>
		<td>选项-转审人员</td>
		<td>标识当前行设置的审批人为转审人
		</td>
	</tr>
	<tr>
		<td>选项-删除已审人员</td>
		<td>
		标识已经审核的人员不要出现在转审待审人中
		</td>
	</tr>
	<tr>
		<td>匹配组</td>
		<td>
		默认为空，当前行指定的人员为待审人/转阅人/转审人；<br/>
		存在值时用于tac中调用指定匹配组，参见[事件/公式](#事件/公式)；
		</td>
	</tr>
	<tr>
		<th colspan="2">审批操作列说明</th>
	</tr>
	<tr>
		<td>单元|从</td>
		<td>
		</td>
	</tr>
	<tr>
		<td>单元|到</td>
		<td>
		</td>
	</tr>
	<tr>
		<td>操作名称</td>
		<td>
		</td>
	</tr>
	<tr>
		<td>执行次序</td>
		<td>
		</td>
	</tr>
	<tr>
		<td>选项</td>
		<td>
		</td>
	</tr>
	<tr>
		<td>条件表达式</td>
		<td>
		能够获取到的信息同【审批链定义-公式】：<br/>
		1. 通过[$keyword]获取关键字段信息；<br/>
		2. 通过main.get("column")获取主表信息；<br/>
		3. 通过main.get("details")获取子表链表(List&lt;Map&gt;)；<br/>
		</td>
	</tr>
</table>

##### 事件/公式

* 【事件】为开发使用，实现接口【snsoft.approval.service.WFListener】即可；
* 【公式】为实施使用，方法与【snsoft.approval.service.WFListener】对应；

1. 提交前接口[wfActionSubmitToSelecting]

```sql
/**
 * WFEvent中的可用对象说明：
 *  1、workflow=snsoft.approval.service.Workflow
 *     workflow.getUnit("10")
 *  2、reqParameter=snsoft.approval.vo.TaskPerformRequest
 *  3、wfaction=snsoft.approval.service.Workflow$Action
 *     wfaction.getUid()    // ="10"
 *     wfaction.getTouid()  // ="30"
 *  4、submitParam=snsoft.approval.vo.SubmitPerformParameter
 *     toucodes=String[],存放待审用户编码
 *  5、dataMap=java.util.Map
 *     审批关键字
 *     dataMap.get("main")=[java.util.Map]业务主表数据
 *     dataMap.get("main").get("wcode")=业务单据属主
 *     dataMap.get("main").get("...")=其他业务单据主表数据
 *     dataMap.get("[keyword]")=获取关键字段数据
 */
/**
 * 设置待审人接口
 * e=WFEvent，可使用内部属性为：
 *  1、workflow
 *  2、reqParameter
 *  3、wfaction
 *  4、submitParam
 *    （1）toucodes：待审用户编码数组；
 *    （2）gwcodeOfUCode：待审用户编码对应的岗位；
 *  5、dataMap
 *  6、serviceContext：审批上下文
 *    （1）getUCodeService()：组织结构服务；
 *    （2）userSession：当前回话对象；
 *    （3）getWcode()：当前单据对应的人员；
 *    （4）getSheetBcode()：当前单据对应的部门；
 */
proc wfActionSubmitToSelecting(e)
	if e.wfaction.getUid()=="10" && e.wfaction.getTouid()=="30"
		e.submitParam.toucodes = toArray("U001")       // 指定待审人
		e.submitParam.gwcodeOfUCode.put("U001","G001") // 指定待审人对应的岗位（多个岗位逗号分隔）
	end if
	// 待审人为单据属主
	if e.wfaction.getUid()=="10" && e.wfaction.getTouid()=="30"
		wcode = e.dataMap.get("main").get("wcode")
		ucode = e.serviceContext.getUCodeService().getUserCodeByWCode(wcode)
		e.submitParam.toucodes = toArray(ucode)
	end if
	// 待审人根据子表部门人员确定（需要在【待审人】中定义【匹配组】）
	if e.wfaction.getUid()=="10" && e.wfaction.getTouid()=="30"
		bcodes = e.dataMap.get("bcodes")
		if bcodes  != null
			for bcode in bcodes .split(",")
				e.setSelector("details",bcode) // details为【匹配组】
			end for
		end if
	end if
end proc
```

#### 单据审批关系

1. 默认查找与单据号相同的审批链定义；
1. 可以根据业务单据属主对应的部门、岗位、用户进行匹配；
1. 可以根据Tac进行匹配；
```
// 如：某事业部币种为CNY走一条审批链，其他走另一条审批链
// data为Map类型的主表数据
// 1. CNY
data.get("fcode") == "CNY"
// 2. 非CNY
data.get("fcode") != "CNY"
```
1. 关键数据公式
```
	kvMap.put("bcode",main.getBcode())
	kvMap.put("ccode",main.getCcode())
	// 或
	put("bcode",main.getBcode())
	put("ccode",main.getCcode())
```

#### 审批授权

定义一些领导没时间审批数据，将应该自己审批的数据授权给助理或其他人代审。想要将自己的审批权限给别人，需要添加审批授权配置。

* 审批授权主表

```
授权人：谁要给别人授权
被授权人：授权给谁
启用时间：默认当天
终止时间：授权到啥时候
授权说明：说明文字
授权时间：授权日期
```

* 审批授权岗位

```
待审岗位：授权自己哪个岗位的审批
```

* 审批授权单据

```
授权单据：授权哪些单据给他
```

* 审批授权部门

```
单据部门：他可以审来自哪些部门的单据
```

#### 审批描述

##### 审批描述公共方法

* 多个审批描述公用的公式在此处进行定义，其他地方通过【编码】直接引用即可；

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th colspan="2" align="center">审批描述公共方法</th>
	</tr>
	<tr>
		<th align="center">列</th>
		<th align="center">说明</th>
	</tr>
	<tr>
		<td>编码</td>
		<td>做码表编码供引用</td>
	</tr>
	<tr>
		<td>名称</td>
		<td>做码表名称</td>
	</tr>
	<tr>
		<td>公式</td>
		<td>Tac取数公式，支持的格式及语法参见【审批描述定义】中的【取数公式】</td>
	</tr>
</table>

##### 审批描述定义

* 提供关键字取数公式，供审批链中其他公式使用；
* 提供审批描述构建方法，供审批时快速浏览单据信息；

<table style="display:inline-block;width:auto;margin-right:10px">
	<tr>
		<th colspan="2" align="center">审批描述定义</th>
	</tr>
	<tr>
		<th align="center">列</th>
		<th align="center">说明</th>
	</tr>
	<tr>
		<td>审批描述编号</td>
		<td></td>
	</tr>
	<tr>
		<td>审批描述名称</td>
		<td></td>
	</tr>
	<tr>
		<td>关键数据字段</td>
		<td>
		<table>
			<tr>
				<th align='center'>格式</th>
				<th align='center'>转换</th>
				<th align='center'>说明</th>
			</tr>
			<tr>
				<td>colname</td>
				<td>put("colname",$colname)</td>
				<td>从主表中获取列值</td>
			</tr>
			<tr>
				<td>tocol:fmcol</td>
				<td>put("tolcol",$fmcol)</td>
				<td>从主表中获取列值映射到另一个列上</td>
			</tr>
			<tr>
				<td>col:col:#codetbl</td>
				<td>put("col",getCodeName($col,"codetbl"))</td>
				<td>获取列值对应的码表名称赋值<br/>展示用，不推荐<br/>建议使用【审批描述明细】</td>
			</tr>
			<tr>
				<td>col:col:2</td>
				<td>put("col",toCur($col,2))</td>
				<td>取金额列值并保留2位小数</td>
			</tr>
			<tr>
				<th align='center' colspan='3'>示例</th>
			</tr>
			<tr>
				<th align='center'>实际数据</th>
				<th align='center'>关键数据字段配置</th>
				<th align='center'>结果</th>
			</tr>
			<tr>
				<td>salordcode=S001<br/>
				bcode=B01[业务一部]<br/>
				fcy=12.3456<br/>
				odate=1987-12-07<br/>
				</td>
				<td>
					salordcode<br/>
					outercode:salordcode<br/>
					bcode<br/>
					bname:bcode:#bcode<br/>
					fcy<br/>
					fcy2:fcy:2<br/>
				</td>
				<td>
					salordcode:'S001',<br/>
					outercodde:'S001',<br/>
					bcode:'B01',<br/>
					bname:'业务一部',<br/>
					fcy:12.3456,<br/>
					fcy2:12.35<br/>
				</td>
			</tr>
		</table>
		</td>
	</tr>
	<tr>
		<td>关键数据公式</td>
		<td>用于补充【关键数据字段】无法配置出来的复杂取数公式<br/>
			通过【put("key",value)】方法设置关键数据列值<br/>
			相关语法参见【审批描述明细】中的【取数公式】。
		</td>
	</tr>
	<tr>
		<td>显示模板</td>
		<td>此功能兼容原有项目，不再推荐使用(使用【审批描述明细】)；<br/>
		     【合同编号】%outercode<br/>
		     【币　　种】%fcode (%fname)<br/>
		     【金　　额】%fcy<br/>
		     【部　　门】%bcode (%bname)<br/>
		     【制单日期】%predate<br/>
		</td>
	</tr>
	<tr>
		<td>回调URL</td>
		<td></td>
	</tr>
	<tr>
		<th colspan='2'>审批描述明细</th>
	</tr>
	<tr>
		<td>序号</td>
		<td>主键，同时决定生成描述的前后顺序</td>
	</tr>
	<tr>
		<td>标题</td>
		<td>显示用标题，如【合同号】</td>
	</tr>
	<tr>
		<td>取数公式</td>
		<td>返回标题对应的内容<br/>
		main：全局变量，为主表VO对象<br/>
		ext：全局变量，为【审批描述前置公式】的返回结果<br/>
		$colname：从主表中获取列值(找不到列则从辅表中找)<br/>
		$colname$name：从主表中获取列值对应的码表名称（前提是开发通过@CodeTable注解设置了该列对应的码表）<br/>
		$colname$ext_tbl：从主表辅表中获取对应的列值<br/>
		getCodeName($colname,"码表")：获取指定列值对应的码表名称<br/>
		$sum$colname$tbl：获取子表列汇总值<br/>
		$max$colname$tbl：获取子表列最大值<br/>
		$min$colname$tbl：获取子表列最小值<br/>
		$concat$colname$tbl：获取子表列值(去重去空后逗号分隔)<br/>
		$count$colname$tbl：获取子表列值(非空记录个数)<br/>
		$countdis$colname$tbl：获取子表列值(非空去重记录个数)<br/>
		$exists$colname$tbl$value：返回子表列是否存在指定的值<br/>
		format("%1$tF",$datecol)：日期格式(1987-12-07)<br/>
		format("%1$tT",$datecol)：时间格式(18:30:25)<br/>
		formatDecimal("#,###.00",$fcy)：金额格式(1,234.00 或 4.35)<br/>
		formatDecimal("#.####",$qtc)：数量格式(12 或 12.3456)<br/>
		formatDecimal("#.##%",$ratio)：百分比格式(13% 或 13.45%)<br/>
		formatDate("MM/dd/yyyy",$odate)：日期(10/01/1949)<br/>
		formatDate("MMM.dd,yyyy",$odate,"en")：日期(Otc.01,1949)<br/>
		</td>
	</tr>
	<tr>
		<td>审批描述公共方法</td>
		<td>码表，选择【审批描述公共方法定义】中预定义的审批描述</td>
	</tr>
	<tr>
		<td>审批描述前置公式</td>
		<td>在公共方法调用前执行，将返回结果通过ext参数传递给【取数公式】【审批描述公共方法】<br/>
		main：全局变量，为主表VO对象<br/>
		使用方法参见【取数公式】<br/>
		</td>
	</tr>
	<tr>
		<td>颜色</td>
		<td>内容颜色，rgb模式
		</td>
	</tr>
	<tr>
		<td>标题式样</td>
		<td>标题CSS式样，JSON格式<br/>
		如:{color:'red'}
		</td>
	</tr>
	<tr>
		<td>内容式样</td>
		<td>内容CSS式样，JSON格式<br/>
		如:{color:'blue'}
		</td>
	</tr>
</table>

#### 常用调试Tac

* 查询待审

```
dao = getBean("SN-APPR.ApprDataReadDAO")
param = new snsoft.approval.service.ApprDataQueryParams()
param.setUiCuicode(getUserSession().getUserCuicode())
param.setUiUsercode(getUserSession().getUserCode())
param.setTaskflag(1)
start = now().getTime()
cnt = dao.queryApprTask(param).getData().size()
println("cnt = "+cnt)
println("gap = "+(now().getTime()-start))
```

#### MongoDB索引

```
tplt = getBean("SN-APPR.MongoTemplate")
ops = tplt.indexOps("apprdata")

// cuicode,taskid,version：逻辑主键索引（写操作使用）
keys = new org.bson.Document({"cuicode":1,"taskid":1,"version":1})
def = new org.springframework.data.mongodb.core.index.CompoundIndexDefinition(keys)
ops.ensureIndex(def)
// ucode+taskflag：待审/已审/待阅/已阅（工作台使用）
keys = new org.bson.Document({"tasklist.ucode":1,"tasklist.taskflag":1})
def = new org.springframework.data.mongodb.core.index.CompoundIndexDefinition(keys)
ops.ensureIndex(def)
// submitdate：提交日期（工作台使用，降序索引）
desc = org.springframework.data.domain.Sort$Direction.DESC
def = new org.springframework.data.mongodb.core.index.Index("tasklist.submitdate",desc)
ops.ensureIndex(def)
// performdate：审批日期（工作台使用，降序索引）
desc = org.springframework.data.domain.Sort$Direction.DESC
def = new org.springframework.data.mongodb.core.index.Index("tasklist.performdate",desc)
ops.ensureIndex(def)

list = ops.getIndexInfo()
for idx in list
	println(idx)
end for
```

#### 右键配置导出

1. 支持右键数据导出，将当前配置界面的有效数据（启用）导出成一个文件（XML/南北标准配置文件），实施人员在数据库中配置标准数据，通过本功能将这些标准配置数据导出成文件用来打包发布。

1. 配置存放目录：
    审批描述定义：snsoft/archive/init/appr/display/xxx.zip
    单据审批关系：snsoft/archive/init/appr/sheetwf/xxx.zip
    审批链定义：snsoft/archive/init/appr/workflow/xxx.zip

#### 右键初始化

配置表数据为空时才可以初始化数据，数据来源是右键导出的配置。

### 实践

#### 审批示例

示例参见：
1、[服务单据](uiinvoke/00/zh_CN/theme0/SN-HELP.Ord.SalorderList.html)（【打开[审批]】按钮）；
2、[UI单据](uiinvoke/00/zh_CN/theme0/SN-HELP.Ord.MapSalorder.html)。

### 设计

#### 审批链UML

* 审批授定义类图

@startuml
Title 审批授定义类图

WorkFlowDefService ..>  WorkflowParam
WorkFlowDefService ..>  Workflowm
WorkFlowDefService <|.. WorkFlowDefServiceImpl

interface WorkFlowDefService {
	+ List<Workflowm> queryWorkflowUI(WorkflowParam params);
	+ void saveWorkflowUI(List<Workflowm> records);
}

class WorkFlowDefServiceImpl{
//审批链定义实现
}

class WorkflowParam {
	# wfcode : String
	# wfname : String
	# gensrc : String
	# cuicode : String
}

Workflowm *..  Workflowu
Workflowu *..  Workfloww
Workflowm *..  Workflowa


class Workflowm {
	# wfcode : String
	# wfname : String
	其他、、、、
	# units : List<Workflowu>
	# actions : List<Workflowa>
}

class Workflowu {
	# wfuid : String
	# uname : String
	其他、、、、
	# users : List<Workfloww>
}

class Workfloww {
	# idx : int
	# utype : String
	# ucode : String
	# bcode : String
	# ubcode : String
	其他、、、、
}

class Workflowa {
	# wfuid : String
	# touid : String
	# aname : String
	其他、、、、
}

@enduml


* 审批核心类图

@startuml
Title 审批核心类图

WorkflowService ..>  TaskPerformRequest
WorkflowService ..>  TaskPerformResponse
WorkflowService <|.. WorkflowServiceImpl

interface WorkflowService {
	+ String[] getWfKeyColumns(String sheetCode, String bcode);
	+ java.util.Map<String,Object> getActiveWorkflowInfo(String sheetCode, String bcode, java.util.Map<String,?> kvMap);
	+ TaskPerformResponse getSubmitPerformParams(TaskPerformRequest reqParams);
	+ TaskPerformResponse performSubmit(TaskPerformRequest reqParams);
}

class WorkflowServiceImpl {
//审批链实现
}

class TaskPerformRequest {
	# wfcode : String
	# taskid : String
	# idx : int
	# ucode : String
	其他、、、、
}
class TaskPerformResponse {
	# info : String
	# info1 : String
	# submitParams : SubmitPerformParameter[]
}

WorkflowServiceImpl --* WorkflowLoader
WorkflowLoader <|.. WorkflowLoaderImpl

interface WorkflowLoader {
	+ Workflow[] loadByBCode(String sheetCode, String sheetBCode);
	+ WFSheet loadActiveWorkflow(String sheetCode, String bcode, java.util.Map<String,?> kvMap);
}

class WorkflowLoaderImpl {
//审批定义加载类
}

WorkflowServiceImpl --* WFSubmitServiceImpl

class WFSubmitServiceImpl {
	+ TaskPerformResponse getSubmitPerformParams();
	+ TaskPerformResponse performSubmit();
}

@enduml

* 审批链提交时序图

@startuml
actor ApprSubmitCliListener #red


box "审批Client Service"
participant ApprDataOrgService   #LightYellow
participant SheetApprAccessService  #yellow
end box
box "审批APPR Service" #LightGreen
participant ApprTaskService
participant WorkflowService
participant ApprDataService
end box
box "业务_APP Service" #LightBlue
end box

autonumber 1 1
ApprSubmitCliListener	[#red]-> ApprDataOrgService : [beforeSubmitApprData]得到审批人员对话框
ApprDataOrgService 		[#red]-> ApprTaskService : 匹配审批链,组织审批主数据并[submitApprData]提交审批系统
ApprTaskService 		[#red]-> ApprDataService : [insertApprData]保存审批主数据
ApprTaskService 		[#black]-> ApprDataOrgService : 返回 审批人员选择对话框；
ApprDataOrgService 		[#black]-> ApprSubmitCliListener : 得到审批人员选择对话框；
ApprSubmitCliListener	[#blue]->  ApprDataOrgService  : [performSubmit]提交审批任务
ApprDataOrgService  	[#blue]-> SheetApprAccessService  : [beforPerformSubmit]处理业务单据数据
ApprDataOrgService		[#blue]-> ApprTaskService  : [submit]提交审批任务
ApprTaskService			[#blue]-> WorkflowService : [performSubmit]提交审批任务
WorkflowService			[#blue]-> ApprDataService  : [insertTasklist]保存审批任务
ApprTaskService			[#black]--> ApprSubmitCliListener  : 提交结束
@enduml

* 审批链审批时序图

@startuml

actor TaskPerformListener #red

box "审批APPR Service" #LightGreen
participant ApprTaskService
participant WorkflowService
participant ApprDataService
end box

box "审批Client+APP Service"
participant ApprDataOrgService   #LightYellow
participant SheetApprAccessService  #yellow
end box

TaskPerformListener	[#red]-> WorkflowService : 执行审批操作
WorkflowService  	[#red]-> ApprDataService : [queryTasklistByTaskid] 得到审批任务
ApprDataService		[#red]--> WorkflowService : 返回审批任务
WorkflowService 	[#red]--> TaskPerformListener : 返回审批任务,显示审批对话框

TaskPerformListener [#blue]->  WorkflowService : [getSubmitPerformParams] 得到下级审批参数
WorkflowService 	[#blue]->  ApprDataService : 得到下级审批操作参数
ApprDataService 	[#blue]--> WorkflowService : 返回下级审批操作参数和审批数据
WorkflowService 	[#blue]--> TaskPerformListener : 返回下级审批参数组织提交下级审批对话框


TaskPerformListener [#magenta]-> WorkflowService : [performSubmit]提交下级审批任务
WorkflowService 	[#magenta]-> ApprDataService : 完成本级审批任务,保存下级审批任务
ApprDataService 	[#magenta]-> ApprDataOrgService : 调用审批客户端标准接口
ApprDataOrgService  [#magenta]-> SheetApprAccessService  : 审批完成处理业务单据数据
WorkflowService 	[#magenta]--> TaskPerformListener : 审批完成

@enduml

#### 审批授权UML

* 审批授权定义类图

@startuml
Title 审批授定义类图

TaskauthDefService ..>  TaskauthParams
TaskauthDefService ..>  Taskauth
TaskauthDefService <|.. TaskauthDefServiceImpl

interface TaskauthDefService {
	+ List<Taskauth> queryTaskauthUI(TaskauthParams params);
	+ void saveTaskauthUI(List<Taskauth> params);
}

class TaskauthDefServiceImpl {
//审批授权定义实现
}

class TaskauthParams {
	# cuicode : String
	# ucode : String
	# ucodeto : String
	# startdatefm : Date
	# statdateto : Date
}
class Taskauth {
	# taicode : String
	# cuicode : String
	# ucode : String
	# ucodeto : String
	# startdate : Date
	# enddate : Date
	# remark : String
	# authdate : Date
	# disabled : int
}

Taskauth *..  taskauthgwcode
Taskauth *..  taskauthsheet
Taskauth *..  taskauthbcode

class taskauthgwcode {
	# taicode : String
	# gwcode : String
}

class taskauthsheet {
	# taicode : String
	# sheetcode : String
}

class taskauthbcode {
	# taicode : String
	# bcode : String
}

@enduml

* 审批授权功能类图

@startuml
Title 审批授功能类图

TaskauthService ..>  DataLimAuthRecord
TaskauthService <|.. TaskauthServiceImpl

class TaskauthService {
	+ SqlExpr buildTaskauthFilter(String ucodeto);
	+ DataLimAuthRecord getTaskAuthRecord(String ucodeto);
}

class TaskauthServiceImpl {
//审批授功能实现
}

class DataLimAuthRecord {
	# toUcode : String
	# fromUcode : String
	# sheetcode : String
	# bcode : String
	# gwcode : String
	# bcodes : String[]
	# from : DataLimAuthRecord
}

@enduml

* 审批授权功能时序图

@startuml

ApprClientService -> ApprDataReadServiceImpl : [queryApprTaskUI(ApprDataQueryParams)] 审批工作台 查询服务
ApprDataReadServiceImpl -> ApprDataReadServiceImpl : [buildTaskFilter(ApprDataQueryParams)]构建查询过滤条件
ApprDataReadServiceImpl -> ApprDataReadServiceImpl : [getLimSqlFilter()]构建权限过滤条件
ApprDataReadServiceImpl -> TaskauthService : [buildTaskauthFilter(String)]根据被授权人构建授权过滤条件
TaskauthService -> ApprDataReadServiceImpl : 返回SqlExpr过滤条件
@enduml

@startuml

TaskPerformListener -> UIWorkflowService : [loadTaskAuthRecordByUCode(String)] 取审批授权数据
UIWorkflowService -> WorkflowService : [loadTaskAuthRecordByUCode(String)] 取审批授权数据
WorkflowService -> TaskauthService : [getTaskAuthRecord(String)] 取审批授权数据

@enduml


#### 审批描述UML

* 审批描述定义类图

@startuml
Title 审批描述定义类图

ApprClientDefService ..>  ApprDisplayParams
ApprClientDefService ..>  ApprDisplay
ApprClientDefService <|.. ApprClientDefServiceImpl

interface ApprClientDefService {
	+ List<ApprDisplay> queryDisplayUI(ApprDisplayParams param);
	+ void saveDisplayUI(List<ApprDisplay> records);
}

class ApprClientDefServiceImpl {
// 审批描述定义实现
}

class ApprDisplayParams {
	# apprdcode : String
	# apprdname : String
	# cuicode : String
}

class ApprDisplay {
	# apprdcode : String
	# apprdname : String
	# cuicode : String
	# keycolumns : String
	# keyformular : String
	# displaytemplate : String
	# callbackurl : String
}

@enduml

* 审批描述功能类图

@startuml
Title 审批描述功能类图

ApprClientService ..>  ApprDisplay
ApprClientService <|.. ApprClientServiceImpl

interface ApprClientService {
	+ ApprDisplay getApprDisplay(String apprdcode);
}

class ApprClientServiceImpl {
// 审批描述功能实现
}

class ApprDisplay {
	# apprdcode : String
	# apprdname : String
	# cuicode : String
	# keycolumns : String
	# keyformular : String
	# displaytemplate : String
	# callbackurl : String
}

@enduml

* 审批描述功能时序图

@startuml

ApprDataOrgServiceImpl -> ApprClientService : [getApprDisplay(String)] 获取审批描述定义对象
ApprClientService -> ApprDataOrgServiceImpl : 返回ApprDisplay对象
ApprDataOrgServiceImpl -> ApprDataOrgServiceImpl : [orgApprDataVO(ApprSheetVO , ApprDisplay )] 构建业务数据对象

@enduml



