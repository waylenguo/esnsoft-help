## 查询面板


### 公共要求
    
    * 参见【FA_9B标准版_需求设计方案_能力中心_公共描述.doc】(2.1,2.2章节)
    * 本规范适用于所有界面的参数面板（特殊需求的除外）

### 个性要求

1. 查询按钮要求
    * （2） 缺省进入单据入口时，显示的为基本查询页面，在〖基本查询〗点《高级查询》可切换到〖更多参数〗页；同理在〖更多参数〗点《基本查询》可切换到〖基本查询〗；
    * （3）〖基本查询〗与〖更多参数〗页参数共同起作用，也就是说点《查询》按钮时另一个隐藏页面的条件设置了也会起作用，同理点《重置》时两边查询条件一起重置；
    > 正例:
        * <img src='help/FT-CODE/images/FTDevRuleForce/基本参数.png' />
        * <img src='help/FT-CODE/images/FTDevRuleForce/更多参数.png' />

1. 参数-列布局要求
	* 单据参数布局要求一行4列，且列不要指定列宽。
	* 入口条件4个一排，出于美观或者“类同含义放在一起”的需要，某行条件可以只有3个甚至2个，但需经过产品人员同意。
	> 正例：
	
	```
	 cellcols="4" clayoutwids="${RES.FT-CODE.E.Q.4CW}" 
	```

1. 参数-业务员部门

	* （1）弹出部门人员选择对话框进行选择，支持选择中间级，不允许多选，显示名；
	* （2）选择权限范围内的部门人员（不区分有效期）；单据号配置本单据的SheetCode，权限码配置C,R
	* （3）若选择到末级人员，则将其对应的上级部门赋值到本参数中，将选择到的人员赋值到对应“属主人员”参数（若存在）中。
	> 正例：
	```
	<c name="bcode" title="业务员部门" sqltype="12" codedata="#FT-CODE.BWcode" cmparams="status:'~10'" titleLeft="true" cmparams.sheetcode="FT-TRD.TX.Prj.CscdExpProject" cmparams.opids="R,C" disableed="true" showname="true" selectMiddle="true" aidInputerBtn="true"/>
	```

1. 参数-业务员
	* （1）弹出人员选择对话框进行选择，不允许多选，显示名；
	* （2）选择权限范围内的人员（不区分状态）。单据号配置本单据的SheetCode，权限码配置C,R
	> 正例：
	```
<c name="wcode" title="业务员" sqltype="12" codedata="#FT-CODE.Wcode" cmparams="status:'~10'" disableed="true" showname="true" aidInputerBtn="true" cmparams.sheetcode="FT-TRD.TX.Prj.CscdExpProject" cmparams.opids="R,C"/>
	```

1. 参数-公司
	* （1）弹出公司选择对话框进行选择，支持选择中间级，不允许多选，显示名；
	* （2）选择权限范围内的公司（不区分有效期）；单据号配置本单据的SheetCode，权限码配置C,R
	> 正例：
	```
	<c name="corpbcode" title="公司" sqltype="12" codedata="#FT-CODE.CorpBcode" cmparams="status:'~10'" cmparams.sheetcode="FT-TRD.TX.Prj.CscdExpProject" cmparams.opids="R,C" disableed="true" showname="true" selectMiddle="true" aidInputerBtn="true"/>
	```

1. 参数-状态
	* （1）在单据入口参数中单据“状态”通常独立布局一行，直接平铺布局该单据的所有状态值（包含‘全部’）
	* （2）默认‘全部’，显示名；
		> 正例：
		<img src='help/FT-CODE/images/FTDevRuleForce/1001.png' />
	* （3）点击不同状态值，无需再点《查询》直接查询出满足条件的数据。

1. 更多参数
```
更多参数中的动态参数原则， 之前定的是所有的固定参数及文档描述了但界面布局不下的参数，都要能够在动态参数中选择，并且根据匹配符，做出对应的过滤筛选
在实际应用中，往往使用场景很复杂，导致会有出错或者是过滤不到的问题，现针对以上的问题，满足下面原则的参数不需要在更多参数中体现；
原则：
1、某个参数根据不同的情况，有的时候过滤的是主表、有的时候过滤的是子表的，如制凭证界面的“制证选项”；
2、某个参数本身就是根据一些选项或者是其他参数选择动态布局的，如制凭证界面的“年、月”；
3、某个参数的选择范围只有两项的；
4、其他有特殊处理的参数。
```



 