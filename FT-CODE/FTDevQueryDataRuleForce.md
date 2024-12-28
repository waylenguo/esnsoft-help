## 查询数据


### 公共要求

* 所有Grid表都要布局 当前行/总行数  
    
* 所有Grid表查询界面都要配置分页、且配置自动调整每页显示记录数监听
    
> 正例：
    
```
        <Groups id="界面属性">
                <Functions>
                    <Functype>SN-PLAT.JS</Functype>
                    <Subtype>2</Subtype>
                    <Funcimpl>#new snsoft.plat.bas.comm.PlatEntryAutoPageJSListener({})</Funcimpl>
                    <Remark>自动计算分页记录数</Remark>
                </Functions>
            </Groups>
```
*  所有Grid表查询界面都要配置【表格竖线显示控制】功能

> 正例：

```
    <Groups id="Grid表格竖线显示控制">
    	<Functions>
    		<Functype>SN-PLAT.UI</Functype>
    		<Subtype>2</Subtype>
    		<Funcimpl><![CDATA[
            snsoft.bas.ui.SysOptionUIListener.new?type=2&time=1&optids=UserCustom.EntryTableYLine
            ]]></Funcimpl>
    		<Remark>获取用户定制Grid表格竖线选项</Remark>
    	</Functions>
    	<Functions>
    		<Functype>SN-PLAT.JS</Functype>
    		<Subtype>2</Subtype>
    		<Funcimpl>#new snsoft.plat.bas.comm.UserCustomGridLineJSListener({})</Funcimpl>
    		<Remark>用户定制Grid表格竖线控制</Remark>
    	</Functions>
    </Groups>
```
*  所有界面都要配置界面属性功能

```
    <Groups id="界面属性">
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>snsoft.ui.optctrl.UIOptCtrlListener.new</Funcimpl>
            <Remark>界面属性控制</Remark>
        </Functions>
    </Groups>
```

* 带有选项功能的界面需要配置右键选项菜单监听

```
    <Functions>
    	<Functype>SN-PLAT.JS</Functype>
    	<Subtype>2</Subtype>
    	<Funcimpl><![CDATA[
    		#new snsoft.plat.bas.opt.ClsOptPopupMenuJSListener({sheetcode:"FT-TRD.TX.Ord.SeSMExpSalOrd",tgtUINames:["ft_trd_sod"]})
    	]]></Funcimpl>
    	<Remark>选项配置右键菜单监听</Remark>
    </Functions>
```

* 都要配置穿透监听

```
    <Functions>
        <Functype>SN-PLAT.JS</Functype>
        <Subtype>2</Subtype>
        <Funcimpl>
        <![CDATA[
        #new snsoft.plat.bas.viewdetail.ViewDetailListener({})
         ]]>
        </Funcimpl>
        <Remark>处理穿透逻辑</Remark>
    </Functions>
```
	

### 个性要求

#### 单据入口
1. 单据号：第一列通常布局“单据号”字段，“单据号”值需支持链接的方式，点击该链接直接打开当前单据；
1. 状态：第二列通常布局“状态”字段，虚列，显示名，若单据“状态”值为‘30待审’时，则取“审批节点”显示名，若“审批节点”为空则仍显示‘30待审’状态名；
1. 创建时间：最后列通常会布局“创建时间”字段，通常入口数据按“创建时间”倒序显示；
1. 创建人：“创建时间”列前通常布局“创建人”字段，显示名；
1. 凡是引用码表的字段，除“币种”列外，如无特殊说明都要设置成显示名，例如：客商、委托方、属主部门、属主人员、公司、创建人、修改人等；
1. 核算组：通常起始单据需布局该列，显示名，布局在“属主部门”后，若[启用核算组]选项打开（为商户选项），则显示，否则隐藏；单据是否需要核算组字段由各单据描述；
1. 入口表单如无特殊说明，均应设置为只读。

#### 查询
1. 查询工具+UI端查询，无特殊情况不使用服务查询以便实施人员更改
1. 合计行配置
    * 金额列配置本页合计+所有页合计
    * 所有页合计可使用监听配置
        >正例
        ```xml
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
            #new snsoft.ft.comm.query.FTQueryRenderSumFieldsListener({tgtBtnDomID:"ordToolbar",bottomSumCol:"salccode", rInvMethod:"FT-TRD.TX.SalOrdQueryUIService.querySalOrdGoodSumUI",tgtCols:[{tgtSumCol:'zusd',hasSum:false,delim:'$'},{tgtSumCol:'zcny',hasSum:false,delim:'¥'}]})
            ]]></Funcimpl>
            <Remark>工具条按钮金额绘制</Remark>
        </Functions>
        ```
1. 分析模板配置
    * 无特殊情况都使用服务端分组的配置方式 :groupMode=4
