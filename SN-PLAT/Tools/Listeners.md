##  常用监听/服务

### 常用监听

<table>
	<tr>
		<th align='center'>功能说明</th>
		<th align='center'>监听配置</th>
	<tr>
	<tr>
		<td>界面属性控制</td>
		<td>
			界面属性配置
			```
			snsoft.ui.optctrl.UIOptCtrlListener.new
			```
			<br/>单据权限+状态控制：有操作权限且单据状态在20以内才可以对界面进行操作<br/>
			```
			snsoft.ui.optctrl.UIOptCtrlListener.new?sheetcode=&stdUICtrl=true
			```
			<br/>单据权限控制：有操作权限才可以对界面进行操作<br/>
			```
			snsoft.ui.optctrl.UIOptCtrlListener.new?sheetcode=&onlyLimitCtrl=true
			```
			<br/>单据控制：指定界面指定属性<br/>
			```
			snsoft.ui.optctrl.UIOptCtrlListener.new?sheetcode=&stdUICtrl=true&optNames=
			```
			<br/>详细参见[界面属性控制](../../SN-CMC/OptCtrl/OptCtrl.md#UIOptCtrlListener)
		</td>
	</tr>
	<tr>
		<td>量价额</td>
		<td>
			客户端JS监听<br/>
			```
			#new snsoft.plat.bas.calc.UpricQtcListener({qtcColumn:'qtc',upricColumn:'upric',fcyColumn:'fcy'})
			```
			<br/>系统功能存盘监听<br/>
			```
			snsoft.plat.bas.calc.UpricQtcCheckListener.new?tblname=gtbl&qtcColumn=qtc&upricColumn=upric&fcyColumn=fcy
			```
		</td>
	</tr>
	<tr>
		<td>分摊</td>
		<td>
			客户端JS监听<br/>
			```
			#new snsoft.plat.bas.calc.DivideListener({mainTableName:'',sumColumn:'',detailTableName:'',refColumn:'',addColumn:''})
			```
			<br/>系统功能存盘监听<br/>
			```
			snsoft.plat.bas.calc.DivideCheckListener.new?mainTableName=&sumColumn=&detailTableName=&refColumn=&addColumn=
			```
		</td>
	</tr>
	<tr>
		<td>汇总-客户端</td>
		<td>
			客户端JS监听<br/>
			```
			#new snsoft.plat.bas.calc.SumListener({sumColumn:'',addColumn:''})
			```
			<br/>系统功能存盘监听<br/>
			```
			snsoft.plat.bas.calc.SumCheckListener.new?sumColumn=&addColumn=&tblname=
			```
		</td>
	</tr>
	<tr>
		<td>汇总-服务端</td>
		<td>
			系统功能存盘监听<br/>
			```
			snsoft.plat.bas.calc.SumListener.new?sumColumn=&addColumn=&tblname=
			```
		</td>
	</tr>
	<tr>
		<td>单据状态时间</td>
		<td>
			系统功能存盘监听
			```
			SN-PLAT.StatusTimeListener
			```
			<br/>用于处理提交时间[submitdate]、完成时间[performdate]、生效时间[ratifydate]
		</td>
	</tr>
	<tr>
		<td>级联码表存盘校验</td>
		<td>
			系统功能存盘监听
			```
			snsoft.plat.bas.service.impl.CasCodetblCheckSVListener.new?tblname=&colname=&sqlfilter=
			```
			<br/>用于校验码表的级联条件是否符合。
		</td>
	</tr>
	<tr>
		<td>重复校验</td>
		<td>
			系统功能存盘监听<br/>
			```
			snsoft.plat.bas.busi.service.impl.CheckDupSVListener.new?careCuicode=&checkCols=&checkNames=
			```
			<br/>用于校验指定的列值是否重复。
		</td>
	</tr>
	<tr>
		<td>编码引用</td>
		<td>
			系统功能存盘监听<br/>
			```
			snsoft.plat.bas.coderef.service.impl.CheckRefSVListener.new?refcode=&delResKey=&mdfResKey=&careCuicode=
			```
			<br/>用于校验列值是否被引用，参见[编码引用](CodeRef.md)。
		</td>
	</tr>
	<tr>
		<td>存盘检查</td>
		<td>
			系统功能存盘监听
			```
			snsoft.plat.bas.busi.service.impl.SaveCheckSVListener.new?incasinfo=tbl1:col1/tbl2:col2,+,-/tbl3:col3,+
			```
			<br/>用于检查是否可以存盘。
		</td>
	</tr>
	<tr>
		<td>核销值处理</td>
		<td>
			系统功能存盘监听
			```
			snsoft.plat.bas.busi.service.impl.CopyXcodeListener.new?xcodeInfo=
			```
			<br/>用于处理核销值拷贝。
		</td>
	</tr>
	<tr>
		<td>数据核销</td>
		<td>
			原始数据存盘监听
			```
			snsoft.plat.bas.cor.service.impl.CorSourceSaveListener.new?corcodes=
			```
			<br/>
			目标数据存盘监听
			```
			snsoft.plat.bas.cor.service.impl.CorTargetSaveListener.new?corcodes=
			```
			<br/>
			红冲核销存盘监听
			```
			snsoft.plat.bas.sheet.red.service.impl.SheetRedCorListener.new?corcodes=
			```
			<br/>参见[数据核销](DataCopy.md)
		</td>
	</tr>
	<tr>
		<td>单据编码规则</td>
		<td>
			存盘监听
			```
			snsoft.plat.bas.busi.service.impl.SheetAccodeSVListener.new?sheetCode=&colname=
			```
			<br/>
			UI监听
			```
			snsoft.plat.bas.busi.SheetAccodeUIListener.new?sheetCode=&colname=
			```
			<br/>
			JS监听
			```
			#new snsoft.plat.bas.busi.SheetAccodeJSListener({colname:''})
			```
			<br/>参见[单据编码规则](../../SN-CMC/Accode/Accode.md#单据编码规则)
		</td>
	</tr>
	<tr>
		<td>单据红冲</td>
		<td>
			系统功能存盘监听
			```
			SN-PLAT.SheetRedSVListener
			```
			<br/>参见[单据红冲](../Sheet/SheetRed.md)。
		</td>
	</tr>
	<tr>
		<td>版本备份</td>
		<td>
			系统功能存盘监听
			```
			SN-PLAT.VersionBackupFunctionListener
			```
			<br/>参见[版本备份](../Sheet/VersionBackup.md)。
		</td>
	</tr>
	<tr>
		<td>存盘值替换</td>
		<td>
			系统功能存盘监听
			```
			SN-PLAT.PlatRVSVListener
			```
			<br/>将服务端增量修改列带回客户端展示。
		</td>
	</tr>
	<tr>
		<td>提交检查</td>
		<td>
			系统功能存盘监听
			```
			SN-PLAT.SubmitCheckSVListener
			```
			<br/>用于处理提交前的VO注解合法性
		</td>
	</tr>
	<tr>
		<td>审批链-SV</td>
		<td>
			系统功能存盘监听
			```
			SN-APPR.ApprDataDelSVListener
			```
			<br/>单据主表删除时删除对应的审批数据。
		</td>
	</tr>
	<tr>
		<td>审批链-提交</td>
		<td>
			客户端JS监听
			<br/>业务数据提交至审批系统
			```
			#new snsoft.wf.busi.ApprSubmitCliListener({apprUiid:"SN-Appr.PlatTasklist"})
			```
			<br/>客户端错误检查
			```
			#new snsoft.plat.bas.busi.CheckErrorListener({})
			```
		</td>
	</tr>
	<tr>
		<td>审批链-审批</td>
		<td>
			UI监听：
			```
			snsoft.approval.ui.UITaskPerformListener.new
			```
			<br/>JS监听：
			```
			#new snsoft.wf.TaskPerformListener({})
			```
		</td>
	</tr>
	<tr>
		<td>明细翻页</td>
		<td>
			JS监听：
			```
			#new snsoft.plat.bas.busi.PageNavPaneJSListener({})
			```
			<br/>单据详情页面多行穿透时的翻页监听。
		</td>
	</tr>
	<tr>
		<td>明细页面标题</td>
		<td>
			JS监听：
			```
			#new snsoft.plat.bas.busi.PageTitleBarJSListener({})
			```
			<br/>单据详情页面标题显示状态及外码。
		</td>
	</tr>
	<tr>
		<td>参数面板枚举监听</td>
		<td>
			JS监听：
			```
			#new snsoft.plat.bas.busi.ShowRefreshPanelListener({})
			```
			<br/>单据入口状态参数横向展示。
		</td>
	</tr>
	<tr>
		<td>状态改变监听</td>
		<td>
			JS监听：
			```
			#new snsoft.plat.bas.busi.StatusJSListener({})
			```
			<br/>启用：10->70;
			<br/>取消启用：70->10
			<br/>停用：70->90;
			<br/>取消停用：90->70;
		</td>
	</tr>
	<tr>
		<td>行选中监听</td>
		<td>
			JS监听：
			```
			#new snsoft.plat.bas.calc.RefSelectRowListener({refColumn:'',valueColumn:''})
			```
			<br/>如待开票工作台：
			<br/>行选中时将待开票金额设置到开票金额上；
			<br/>开票金额超过待开票金额时将开票金额设置为待开票金额；
			<br/>开票金额清空时取消行选中；
		</td>
	</tr>
	<tr>
		<td>清空值监听</td>
		<td>
			JS监听：
			```
			#new snsoft.plat.bas.comm.CleanValueJSListener({tgtUINames:[],postInfos:[{}]})
			```
			<br/>值改变后清空对应的列值或行值。
		</td>
	</tr>
	<tr>
		<td>Grid表竖线监听</td>
		<td>
			JS监听：
			```
			#new snsoft.plat.bas.comm.UserCustomGridLineJSListener({})
			```
			<br/>用户定制Grid表格线JS监听。
		</td>
	</tr>
	<tr>
		<td>表刷新后定位监听</td>
		<td>
			JS监听：
			```
			#new snsoft.plat.bas.comm.LocateCellListener({})
			```
			<br/>记住表刷新前的行列，表刷新后定位到该行列。
		</td>
	</tr>
	<tr>
		<td>日期从到监听</td>
		<td>
			JS监听：
			```
			#new snsoft.plat.bas.comm.IncToDateListener({fmname:})
			```
			<br/>[日期从]值改变后对[日期到]进行赋值。
		</td>
	</tr>
	<tr>
		<td>列分组显示</td>
		<td>
			JS监听：
			```
			#new snsoft.plat.bas.comm.ColumnVisibleListener({colGrp:})
			```
			<br/>将界面列分成N组，每点击一次切换一组。
		</td>
	</tr>
	<tr>
		<td>级联查询监听</td>
		<td>
			UI监听：
			```
			snsoft.plat.bas.calc.CasLoadUIListener.new?loadUINames=
			```
			<br/>JS监听：
			```
			#new snsoft.plat.bas.calc.CasLoadJSListener({infos:[]})
			```
			<br/>详情页面查询主表时将子表、孙表一起带回到客户端（适用于子表较多影响展示效率的页面）。
		</td>
	</tr>
	<tr>
		<td>界面数据分组</td>
		<td>
			系统功能查询监听：
			```
			snsoft.plat.bas.grp.UIDataGroupQueryListener.new
			```
			<br/>UI监听：
			```
			snsoft.plat.bas.grp.UIDataGroupListener.new?grpcode=
			```
			<br/>JS监听：
			```
			#new snsoft.plat.bas.grp.UIDataGroupListener({})
			```
			<br/>界面数据分组监听（系统功能查询监听根据需要进行配置）。
		</td>
	</tr>
</table>


### 常用服务

#### 默认值

```java
DefaultValueService dvs  = SpringBeanUtils.getBeanByName(DefaultValueService.BeanName);
dvs.setDefaultValues(vo,true);
dvs.setDefaultValues(vo,true,true);
```

#### 分码服务

```
MakeCodeParam mcp = new MakeCodeParam(tblname,colname,"PREFIX____","a=1",0);
String code = MakeCodeService.impl.makeCode(mcp);

BatchCodeMaker codeMaker = MakeCodeService.impl.batchMacker(100,10,mcp);
String code = codeMaker.next();
```

#### 数据校验

```
ValidateService vs = SpringBeanUtils.getBeanByName(ValidateService.BeanName);
vs.assertValid(vo);
```

#### 配置加载

**注意：配置对象的加载是以缓存的方式进行返回的。**

```
// 批量加载:所有单据
List<BusiObject> list = ConfigResourceFactory.impl.loadConfigs("res/sheet/","*","xml",BusiObject.class);
// 批量加载:帮助中心所有单据
List<BusiObject> list = ConfigResourceFactory.impl.loadConfigs("res/sheet/","SN-HELP.*","xml",BusiObject.class);
// 单个加载：销售合同单据对象
BusiObject busiObject = ConfigResourceFactory.impl.loadConfig("res/sheet/SN-HELP.Salorder","xml",BusiObject.class);
// 单个加载：销售合同单据XML
String text = ConfigResourceFactory.impl.loadConfig("res/sheet/SN-HELP.Salorder","xml",String.class);
```