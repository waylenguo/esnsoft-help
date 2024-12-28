## 单据校验

|日期|作者|备注|
|------|------|------|
|2020-7-8|赵玉柱|初稿|

### 指南

#### 单据校验说明
当单据提交时，需要根据配置来处理一些校验逻辑
也可以处理审批时单据提交的前置逻辑判断
1. 单据校验对应的Schema文件【[SheetCheckDef.xsd](do/BrowseSchema?schema=VersionBackupDef.xsd)】；
1. [单据方法校验配置](uiinvoke/00/zh_CN/theme0/SN-PLAT.Sheet.CheckRefDef.html)：一些公共的校验配置，比如某些业务场景下，需要计算金额，数量，单价等信息，这种情况下，可以将此校验逻辑抽出公共配置，供单据校验配置使用，需要实现 snsoft.plat.bas.sheet.chk.service.SheetCheckListener 接口
1. [单据校验配置](uiinvoke/00/zh_CN/theme0/SN-PLAT.Sheet.CheckDef.html)：
    1. 单据校验配置列表：单据校验启用条件及全局的参数设置以及分类设置
    1. 单据配置节点列表：当前单据校验的检查点，以及对应的序号。将按照已经启用的序号依次执行，每一个检查点仅支持一个检查配置，目前检查配置支持TAC公式，原型Bean，引用单据公共配置
    1. 单据检查关系：单据与单据校验配置之间的关系，一般的找关系方式为部门path匹配，公司path匹配，使用tac动态匹配，匹配方式为最优解方式，即谁的层级更深，谁为最优解
1. 配置文件路径：
    单据公共校验：res/sheetcheckref/[chkrefcode].xml
    单据校验配置：res/sheetcheck/[chkcode].xml
 
1. 参考样例[销售合同-入口](uiinvoke/00/zh_CN/theme0/SN-HELP.Ord.SalorderList.html)
1. 审批链集成参考样例[销售合同[审批]-入口](uiinvoke/00/zh_CN/theme0/SN-HELP.Ord.SalorderListAPPR.html)

#### 界面直接配置

```xml
<!--
JS监听类：
1、界面需要显示布局单据校验按钮，命令号=sheetCheck；
3、服务端调用完成之后，将会将提示信息返回到客户端供客户端自行处理；
-->
<jslistener><![CDATA[
	#new snsoft.plat.bas.sheet.chk.SheetCheckJSListener({})
]]></jslistener>
```


#### UI系统功能配置

```xml
<Groups id="单据校验">
	<Functions>
		<Functype>SN-PLAT.JS</Functype>
		<Subtype>2</Subtype>
		<Funcimpl>#new snsoft.plat.bas.sheet.chk.SheetCheckJSListener({})</Funcimpl>
		<Remark>单据校验：客户端监听类</Remark>
	</Functions>
</Groups>
```




#### Xml文件配置

##### 单据校验方法配置
样例1 在监听中提示简单的提示信息 SN-HELP.Salorder.xml 
```xml
<?xml version="1.0" encoding="utf-8" ?>
<SheetCheckRef
	xmlns="http://www.snsoft.com.cn/schema/SheetCheckDef"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetCheckDef http://www.snsoft.com.cn/schema/SheetCheckDef.xsd"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	chkrefname="子表单位一致" type="1"
>
	<TacFormula><![CDATA[
		#SN-HELP.SalOrderUnitSheetCheckListener?[{}]
	]]></TacFormula>
	<Remark><![CDATA[
	校验子表的单位必须一致
	]]></Remark>
</SheetCheckRef>
```

样例2 在监听中抛出异常  参考 SN-HELP.Salorder_lis_err.xml 
```xml
<?xml version="1.0" encoding="utf-8" ?>
<SheetCheckRef
	xmlns="http://www.snsoft.com.cn/schema/SheetCheckDef"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetCheckDef http://www.snsoft.com.cn/schema/SheetCheckDef.xsd"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	chkrefname="异常" type="1"
>
	<TacFormula><![CDATA[
		#SN-HELP.SalOrderErrSheetCheckListener?[{}]
	]]></TacFormula>
	<Remark><![CDATA[
	公共检查，直接抛出异常的方式，调用监听类
	]]></Remark>
</SheetCheckRef>

```

样例3  在监听中返回界面命令号，并设置对应参数 参考 SN-HELP.Salorder_lis_funcid.xml
```xml
<?xml version="1.0" encoding="utf-8" ?>
<SheetCheckRef
	xmlns="http://www.snsoft.com.cn/schema/SheetCheckDef"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetCheckDef http://www.snsoft.com.cn/schema/SheetCheckDef.xsd"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	chkrefname="根据funcid返回界面" type="1"
>
	<TacFormula><![CDATA[
		#SN-HELP.SalOrderFuncidUIInfoSheetCheckListener?[{funcid:"SN-HELP.Ord.SalorderList",height:400,width:900,initValues:{param:{salordcode:"10010"}}}]
	]]></TacFormula>
	<Remark><![CDATA[
	公共检查，根据funcid返回界面SN-HELP.Ord.SalorderList,高度400，宽度900，合同号10010
	]]></Remark>
</SheetCheckRef>

```

样例4 参数返回为空，此情况下直接进行下一条数据处理 参考  SN-HELP.Salorder_lis_funcid_d.xml

```xml

<?xml version="1.0" encoding="utf-8" ?>
<SheetCheckRef
	xmlns="http://www.snsoft.com.cn/schema/SheetCheckDef"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetCheckDef http://www.snsoft.com.cn/schema/SheetCheckDef.xsd"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	chkrefname="不返回functionid，走默认" type="1"
>
	<TacFormula><![CDATA[
		#SN-HELP.SalOrderFuncidUIInfoSheetCheckListener?[{height:400,width:900,initValues:{param:{salordcode:"10010"}}}]
	]]></TacFormula>
	<Remark><![CDATA[
	公共检查，不返回funcid，为了测试直接下一条记录
	]]></Remark>
</SheetCheckRef>
```



样例5 返回界面定义编号，并设置对应参数  参考 SN-HELP.Salorder_lis_muiid.xml
```xml
<?xml version="1.0" encoding="utf-8" ?>
<SheetCheckRef
	xmlns="http://www.snsoft.com.cn/schema/SheetCheckDef"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetCheckDef http://www.snsoft.com.cn/schema/SheetCheckDef.xsd"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	chkrefname="根据muiid返回界面" type="1"
>
	<TacFormula><![CDATA[
		#SN-HELP.SalOrderMuiidUIInfoSheetCheckListener?[{muiid:"SN-PLAT.Sheet.CheckRefDef",height:600,width:1000,initValues:{param:{chkrefcode:"SN-HELP.Salorder_lis_err"}}}]
	]]></TacFormula>
	<Remark><![CDATA[
	公共检查，根据muiid返回界面SN-PLAT.Sheet.CheckRefDef，高度600，宽度1000，配置编码SN-HELP.Salorder_lis_err
	]]></Remark>
</SheetCheckRef>
```

样例6 在TAC中返回信息 参考 SN-HELP.Salorder_tac_info.xml
```xml
<?xml version="1.0" encoding="utf-8" ?>
<SheetCheckRef
	xmlns="http://www.snsoft.com.cn/schema/SheetCheckDef"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetCheckDef http://www.snsoft.com.cn/schema/SheetCheckDef.xsd"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	chkrefname="tac方式返回信息" type="1"
>
	<TacFormula><![CDATA[
		proc check(main,extParams)
			remark = main.getRemark()
			println("remark="+remark)
			if (snsoft.commons.util.StrUtils.isEmpty(remark) || !remark.contains("{tac_info}"))
				println("check="+false)
				return null
			else
				info = newInfo()
				info.setMessage("tac方式返回信息")
				println("check="+true)
				return info
			end if
		end proc
	]]></TacFormula>
	<Remark><![CDATA[
	公共检查，根据muiid返回界面SN-PLAT.Sheet.CheckRefDef，高度600，宽度1000，配置编码SN-HELP.Salorder_lis_err
	]]></Remark>
</SheetCheckRef>

```

样例7 在TAC中返回多条信息 参考 SN-HELP.Salorder_tac_infos.xml

```xml
<?xml version="1.0" encoding="utf-8" ?>
<SheetCheckRef
	xmlns="http://www.snsoft.com.cn/schema/SheetCheckDef"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetCheckDef http://www.snsoft.com.cn/schema/SheetCheckDef.xsd"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	chkrefname="tac方式返回信息" type="1"
>
	<TacFormula><![CDATA[
		proc check(main,extParams)
			remark = main.getRemark()
			println("remark="+remark)
			if (snsoft.commons.util.StrUtils.isEmpty(remark) || (!remark.contains("{tac_info}")&&!remark.contains("{tac_info1}")))
				println("check="+false)
				return null
			else
				list = newArrayList()
				if(remark.contains("{tac_info}"))
					info = newInfo()
					info.setMessage("tac方式返回信息0")
					println("check="+true)
					info
					list.add(info)
				end if
				if(remark.contains("{tac_info1}"))
					info = newInfo()
					info.setMessage("tac方式返回信息1")
					println("check="+true)
					info
					list.add(info)
				end if
				return list
			end if
		end proc
	]]></TacFormula>
	<Remark><![CDATA[
	公共检查，根据muiid返回界面SN-PLAT.Sheet.CheckRefDef，高度600，宽度1000，配置编码SN-HELP.Salorder_lis_err
	]]></Remark>
</SheetCheckRef>
```
样例8 将校验方法同一个界面定义号合并 参考 SN-HELP.Salorder_multip1.xml

```xml
<?xml version="1.0" encoding="utf-8" ?>
<SheetCheckRef xmlns="http://www.snsoft.com.cn/schema/SheetCheckDef" xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetCheckDef http://www.snsoft.com.cn/schema/SheetCheckDef.xsd"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" chkrefname="多个合并1" type="1" union="true">
	<TacFormula><![CDATA[
		#SN-HELP.SalOrderUnitSheetCheckListener?[{options0:8,options1:2,infotype1:1}]
		#SN-HELP.SalOrderFuncidUIInfoSheetCheckListener?[{chktype:1,infotypeArgs:{"uiname":"tbl_10_S001"},options:"0x8+0x4",funcid:"SN-HELP.SheetChk.SheetCheckDialog1",height:'50%',width:'70%',initValues:{demoname:"10010"}}]
		#SN-HELP.SalOrderFuncidUIInfoSheetCheckListener?[{chktype:0,infotypeArgs:{"uiname":"tbl_10_S002"},options:"0x8+0x4",funcid:"SN-HELP.SheetChk.SheetCheckDialog1",height:'500px',width:'70%',initValues:{demoname:"10010"}}]
		#SN-HELP.SalOrderMuiidUIInfoSheetCheckListener?[{chktype:1,infotypeArgs:{"uiname":"tbl_10_S001"},options:16,muiid:"SN-HELP.SheetChk.SheetCheckDialog1",height:600,width:1000,initValues:{demoname:"10010"}}]
		#SN-HELP.SalOrderMuiidUIInfoSheetCheckListener?[{chktype:0,infotypeArgs:{"uiname":"tbl_10_S002"},options:16,muiid:"SN-HELP.SheetChk.SheetCheckDialog1",height:600,width:1000,initValues:{demoname:"10010"}}]
		#SN-HELP.SalOrderInfoDealByOtherOpsSheetCheckListener?[{otherOptions:0,message:"测试confirm"}]
		#SN-HELP.SalOrderMsgInfoSheetCheckListener?[{chktype:0,infotypeArgs:{"tabname":"tab_tip_20_S001"},muiid:"SN-HELP.SheetChk.SheetCheckDialog1",message:"<h1>我是标题信息</h1>",msgOptions:0}]
		#SN-HELP.SalOrderMsgInfoSheetCheckListener?[{chktype:0,infotypeArgs:{"tabname":"tab_tip_20_tac"},muiid:"SN-HELP.SheetChk.SheetCheckDialog1",message:"<h1>我是另一个标题信息</h1>",msgOptions:0}]
		//外加tac
		proc check(main,extParams)
			remark = main.getRemark()
			println("remark="+remark)
			if (snsoft.commons.util.StrUtils.isEmpty(remark) || !remark.contains("{tac_info}"))
				println("check="+false)
				return null
			else
				info = newInfo()
				info.setMessage("tac方式返回信息")
				println("check="+true)
				info.setMuiid("SN-HELP.SheetChk.SheetCheckDialog1")
				map = newHashMap()
				map.put("tabname","tab_tip_20_tac")
				info.setInfotypeArgs(map)
				return info
			end if
		end proc
	]]></TacFormula>
	<Remark><![CDATA[
	    校验点中的校验方法输出结果  按照muiid或funcid进行分组合并 并在客户端对应到一个界面定义上
	    逻辑参考 SheetCheckCommDialog.xml
	]]></Remark>
</SheetCheckRef>
```


##### 单据校验配置
参考 S0001.xml
```xml
<?xml version="1.0" encoding="utf-8" ?>
<SheetCheckDef
	xmlns="http://www.snsoft.com.cn/schema/SheetCheckDef"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetCheckDef http://www.snsoft.com.cn/schema/SheetCheckDef.xsd"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	chkname="测试Salorder" chktype="SN-APPR.10" sheetcode="SN-HELP.Salorder"
>
	<TacFormula><![CDATA[
		proc start(main)
			remark = main.getRemark()
			if (snsoft.commons.util.StrUtils.isEmpty(remark) || !remark.contains("{start}"))
				return false
			else
				return true
			end if
		end proc
		proc prepare(main)
			return newHashMap()
		end proc
	]]></TacFormula>
	<Remark><![CDATA[
		1、开发负责人：赵玉柱
		2、功能说明：Salorder单据提交测试
	]]></Remark>
	<Nodes dtname="公共校验提示信息-多个" chkrefcode="SN-HELP.Salorder">
		<TacFormula><![CDATA[
		]]></TacFormula>
		<Remark><![CDATA[
		测试公共校验提示信息-多个
		]]></Remark>
	</Nodes>
	<Nodes dtname="公共TAC提示信息" chkrefcode="SN-HELP.Salorder_tac_info">
		<TacFormula><![CDATA[
		]]></TacFormula>
		<Remark><![CDATA[
		公共TAC提示信息
		]]></Remark>
	</Nodes>
	<Nodes dtname="公共TAC提示信息List" chkrefcode="SN-HELP.Salorder_tac_infos">
		<TacFormula><![CDATA[
		]]></TacFormula>
		<Remark><![CDATA[
		公共TAC提示信息List
		]]></Remark>
	</Nodes>
	<Nodes dtname="使用公共抛异常" chkrefcode="SN-HELP.Salorder_lis_err"  stopflag="1">
		<TacFormula><![CDATA[
			proc start(main)
				remark = main.getRemark()
				if (snsoft.commons.util.StrUtils.isEmpty(remark) || !remark.contains("{isCheck_0030}"))
					return false
				else
					return true
				end if
			end proc
		]]></TacFormula>
		<Remark><![CDATA[
		使用公共校验-直接抛出异常
		]]></Remark>
	</Nodes>
	<Nodes dtname="使用公共funcid" chkrefcode="SN-HELP.Salorder_lis_funcid">
		<TacFormula><![CDATA[
			proc start(main)
				remark = main.getRemark()
				if (snsoft.commons.util.StrUtils.isEmpty(remark) || !remark.contains("{isCheck_0040}"))
					return false
				else
					return true
				end if
			end proc
		]]></TacFormula>
		<Remark><![CDATA[
		使用公共校验-返回funcid,根据remark的是否包含{isCheck_0040}判定是否走0040的校验,无prepare方法
		]]></Remark>
	</Nodes>
	<Nodes dtname="使用公共funcid空" chkrefcode="SN-HELP.Salorder_lis_funcid_d">
		<TacFormula><![CDATA[
			proc start(main)
				remark = main.getRemark()
				if (snsoft.commons.util.StrUtils.isEmpty(remark) || !remark.contains("{isCheck_0045}"))
					return false
				else
					return true
				end if
			end proc
			proc prepare(main)
				return null
			end proc
		]]></TacFormula>
		<Remark><![CDATA[
		使用公共校验返回空的funcid，直接跳过下一个,根据remark的是否包含{isCheck_0045}判定是否走0045的校验,prepare返回空
		]]></Remark>
	</Nodes>
	<Nodes dtname="使用公共muiid" chkrefcode="SN-HELP.Salorder_lis_muiid">
		<TacFormula><![CDATA[
			proc start(main)
				remark = main.getRemark()
				if (snsoft.commons.util.StrUtils.isEmpty(remark) || !remark.contains("{isCheck_0050}"))
					return false
				else
					return true
				end if
			end proc
			proc prepare(main)
				return main
			end proc
		]]></TacFormula>
		<Remark><![CDATA[
		使用公共校验-返回muiid,根据remark的是否包含{isCheck_0050}判定是否走0050的校验，并在prepare返回原数据
		]]></Remark>
	</Nodes>
</SheetCheckDef>
```


#### 单据审批提交集成说明

在审批链提交时，会先调用单据校验配置进行数据校验，并将数据校验结果返回到客户端
在对应<span style='color:red'>[审批提交定义](uiinvoke/00/zh_CN/theme0/SN-APPR-CLIENT.SheetSubmitDef.html)</span>的js监听替换为 #new snsoft.plat.sheet.chk.SheetCheckApprJSListener({})