## 盈亏测算使用说明文档

### 盈亏测算配置

盈亏测算配置主表

|名称|说明|
|------|------|
|文件|勾选表名来自文件|
|系统下发|勾选说明来自系统默认配置|
|编码|编码|
|名称|名称|
|TAC依赖字段|定义〖适用单据〗中用到的主表字段，多个用半角逗号分隔，涉及的字段值修改时会触发源单据重新装载盈亏测算配置|
|状态|只用启用状态的才能被使用|


盈亏测算配置适用单据表

|名称|说明|
|------|------|
|单据类型|选择当前盈亏测算适用的单据类型，多个配置多行|
|适用公司|选择有效的公司，单选，可选中间级|
|适用部门|选择有效的部门，单选，可选中间级|
|TAC|通过当前单据VO（srcVO），实现其他过滤逻辑|



盈亏测算配置测算项目表

|名称|说明|
|------|------|
|序号|序号，用于排序|
|不启用|勾选后，此盈亏项不被加载|
|盈亏项目码|此盈亏项编码|
|盈亏项目名称|盈亏项目名称|
|固定测算项目|勾选时，此盈亏项固定加载|
|适用业务要素|选择业务要素码表，多选，固定测算项目未勾选时，根据单据业务要素List字段判断当前盈亏项是否适用|
|从上游单据拷贝|当单据拷贝生成，且当前项的值要拷贝上游单据的盈亏测算时勾选，默认拷贝盈亏项目码相同项|
|上游盈亏项目码|不为空时，拷贝此项目码对应上游盈亏项|
|默认比率|盈亏测算比率列默认值|
|默认币种|盈亏测算币种列默认值|
|只读人民币|勾选时盈亏测算人民币列只读|
|只读美元|勾选时盈亏测算美元列只读|
|只读本位币|勾选时盈亏测算本位币列只读|
|只读比率|勾选时盈亏测算比率列只读|
|只读币种|勾选时盈亏测算币种列只读|
|分类名称|为了便于定义分类计算公式，可将同分类测算项目归于同一大类，这样分类计算公式会自动将同一分类的测算值进行汇总，属于多个分类时逗号隔开|
|分类计算公式|按分类名称定义计算公式，格式为：[A]+[B]-[C]/10，中括弧括中的是分类合计值|
|取数计算公式|用于配置取数公式，支持Tac。例：取界面值：盈亏列=[get(表名.字段名)],取界面合计值：盈亏列=[getSum(表名.字段名)],取分类合计值：盈亏列=[分类名称_列名],Tac：见下例|
|赋值计算公式|用于盈亏测算执行后，对其他表单赋值，支持Tac。例：赋界面值：表名.字段名=[get(表名.字段名)]，赋盈亏项值：表名.字段名=[get(this.盈亏项列)]，赋分类合计值：表名.字段名=[分类名称_列名]，Tac：见下例|
|币种汇率赋值方案|
10：始终取单据币种及汇率 : 本测算项目“币种”和“汇率”始终获取单据中的币种及汇率，即始终同单据币种汇率保持一致
20：币种相同汇率取单据汇率：本测算项目第一次加载时默认获取单据的币种和汇率，币种修改后如果币种和单据币种不一致时，将从汇率库重新获取汇率，否则取单据汇率；
30：自定义币种从汇率库取汇率：本测算项目第一次加载时取“默认币种”值，币种修改后会自动根据该币种从汇率库获取汇率；
40：自定义币种手工录入汇率：本测算项目第一次加载时取“默认币种”值，币种值改变后不处理汇率。|
|屏蔽汇率折算金额公式|系统默认在原币或汇率改变后会重新计算本币以及折人民向和折美元，如果这些金额是根据取数公式得来的，可选中本列值，以屏蔽默认的计算公式|
|非空|源单据提交时需检查本测算项目的“本位币”值不得为空|
|保留小数位|正整数或0，定义本测算项目金额列的小数位数，缺省为2。例：scy:2,zcny:2|
|百分数字段|定义本测算项目需显示成百分数的金额字段。例：fcy,scy,zcny,zusd|
|监听字段|定义取数公式用到的字段，定义的字段在存盘时系统会比较其是否发生了修改，若其中只要一个字段值发生了改变，都会触发盈亏项目重新取数及进行运算。格式为：表名.字段名,表名.字段名|
|背景色|弹窗选择本测算项目在单据盈亏测算表中显示的背景颜色|
|说明|显示在单据中盈亏测算表中备注的文字|
|配置说明|配置备注|

*取数计算公式Tac例
```
	tac:
	proc calc(mainVO,profitVO)
		cmsList = mainVO.getProjectcmslist()
		if cmsList!=null&&cmsList.size()>0
			fcy = 0
			zusd = 0
			zcny = 0
			scy = 0
			for cms in cmsList
				if cms.getIsclet() != "Y"
					fcy = fcy+cms.getDarkcmsfcy()
					zusd = zusd+cms.getDarkcmszusd()
					zcny = zcny+cms.getDarkcmszcny()
					scy = scy+cms.getDarkcmsscy()
				end if
			end for
			setValue("fcy",fcy)
			setValue("zusd",zusd)
			setValue("zcny",zcny)
			setValue("scy",scy)
		end if
	end proc
```

*赋值计算公式Tac例
```
	tac:
	proc calc(mainVO,profitVO)
		scy = profitVO.getScy()
		mainVO.setScy(scy)
		mainVO.addStoredColumn("scy")
	end proc
```



### 盈亏测算单据监听说明

#### 盈亏测算UI监听
snsoft.ft.trd.comm.profit.ui.BusiSheetProfitUIListener.new?tgtUINames=主表,盈亏测算表
UI系统功能

#### 盈亏测算JS监听
#new snsoft.ft.trd.comm.profit.BusiSheetProfitCliListener({profitTblName:'盈亏测算表'})


#### 盈亏测算SV监听
snsoft.ft.trd.comm.profit.service.impl.BusiSheetProfitSVListener.new?sheetCode=FT-TRD.TX.Prj.CscdExpProject&profitTblName=ft_trd_prj_pft&frateDateName=ft_trd_prj.predate&fcodeName=ft_trd_prj_extse.fcode&sfcodeName=ft_trd_prj_extse.sfcode&fserateName=ft_trd_prj_extse.fserate&fcerateName=ft_trd_prj_extse.fcerate&fuerateName=ft_trd_prj_extse.fuerate


* sheetCode=单据号
* profitTblName=盈亏测算表名
* frateDateName=汇率日期所在表.汇率日期列
* fcodeName=币种所在表.币种
* sfcodeName=本位币种所在表.本位币种
* fserateName=折本位币汇率所在表.折本位币汇率
* fcerateName=折人民币汇率所在表.折人民币汇率
* fuerateName=折美元汇率所在表.折美元汇率



#### 盈亏测算复制监听
snsoft.ft.trd.comm.profit.service.impl.BusiSheetProfitCopyListener.new?profitTblName=盈亏测算表名
单据复制需要复制盈亏测算时配置到复制定义上

#### 盈亏测算版本修改监听
snsoft.ft.trd.comm.profit.service.impl.BusiSheetProfitVersionBackupListener.new?profitTblName=盈亏测算表名
单据版本备份需要备份盈亏测算时配置到版本备份定义上

