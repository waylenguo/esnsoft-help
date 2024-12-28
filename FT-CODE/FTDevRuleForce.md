## 强制开发规范（不可犯错）

[腾讯文档开发规范](https://docs.qq.com/sheet/DYkZnSUpSenVSYnlC?tab=BB08J2)
[开发流程图](https://www.processon.com/view/link/5f97fa0de401fd06fda66852)
[SonarQube检测地址](http://10.77.49.4:9000/sessions/new)

### 开发工作流程

| 项目         | 流程 | 备注 |
| ----| ---- | ---- |
| 资源编号 | 向李朋、石宏宇申请后添加 | 不符合默认约定之外的需要申请<br />例如：界面定义、单据定义等标题的资源编号是有默认约定规则的，这类不需要再申请了。 |
| 数据库字段 | 向李朋、石宏宇申请后添加 |      |
| 数据库字段长度宏 | 向李朋、石宏宇申请后添加 |      |
| 界面列宽宏 | 向聂明笛、李朋、石宏宇申请后添加 | 主要是那些公共字段（有些本界面特有字段不需要申请） |



### 公共规范

1. 所有界面标题、列标题、按钮标题、提示语等等出现中文文字的地方都要支持国际化，即使用资源文件方式配置。[参见帮助中心国际化配置](https://n.esnsoft.cn/N9Bhelp/help.html?helpFile=/help/SN-CMC/International/International.md)
1. 标题统一
	* 单据标题、权限标题、编码规则标题、单据复制、版本备份、撤单定义、单据红冲、入口标题(菜单标题)、详情标题、单据说明；
	* 其中菜单是打开单据入口的规则为：[单据标题]-入口
	* 打开单据入口的标签页标题规则为：[单据标题]-入口
	* 打开单据详情/普通码表/普通配置界面的标签页标题规则为：[单据标题]
	* 标题统一到界面配置方式：[参见帮助中心国际化配置](https://n.esnsoft.cn/N9Bhelp/help.html?helpFile=/help/SN-CMC/International/International.md)
	* 注意配置界面中表名的标题时不能出现一些特殊符号;如下（如需求中要求的显示名出现类似情况找李朋、石宏宇讨论如何解决）：
     
        > 名称中不能包含以下字符：【:\/?*[]】                                               
        > 名称中的第一个或者最后一个字符不能是单引号
        
	> 正例：
	```
	*菜单标题：@UI；
	*穿透：不设置标题;
	```
    > 反例： 
    ```$xslt
    title_xxxDetail=合作/代理出口销售合同 --(违反了特殊符号的规则)
    ```
	* 带下拉按钮弹出框标题统一采用”一级按钮标题|二级按钮标题|x级按钮标题“方式展示。
   
   
1. 公共按钮资源
    ```
    # 公共按钮标题：${RES.UI.cmd_syncToDB}、${RES.UI.cmd_exportToFile}
    cmd_syncToDB=同步到数据库
    cmd_exportToFile=导出到文件
    cmd_initToDB=初始化
    ```
1. 所有界面当超出当前页面显示范围后都要带滚动条(横向，纵向)
1. 组织相关码表在FT中必须统一使用FT的码表；
1. 对于那种Grid表的维护界面，删除行后不需要关闭当前页签，需要在界面上配置：uiprops.closeTabOnDelete="false"
1. 对于按钮工具栏无新建按钮，但操作列中有新增按钮的界面，默认都要增加一个新行（可插入）。
1. 按钮样式
	* （1）单据入口、配置、码表、以及工作台界面按钮使用文字+框的模式。
		> 正例：
		<img src='help/FT-CODE/images/FTDevRuleForce/0001.png' />
	* （2）标题后面的按钮使用 图标+文字的方式
		> 正例：
		<img src='help/FT-CODE/images/FTDevRuleForce/0002.png' />
	* （3）操作列按钮分为两类，一类为图标；二类为文字；当操作列按钮不可用时要统一显示不可用图标
		> 正例：
		<img src='help/FT-CODE/images/FTDevRuleForce/0003.png' />
		<img src='help/FT-CODE/images/FTDevRuleForce/0004.png' />

1. 所有界面字段布局要求
	* （1）GRID:值等长居中（长度适合，日期、币种、成交方式等）、不等长字符居左（客户、时间、外码等），数值居右（数量、月、金额等）
	* （2）小数位：根据VO注解走，最小小数位

1. 所有参数中日期/时间带有从xxx到xxx格式配置的标题都统一使用"到",而不是"至"。	
1. 弹出框中的页面，都必须配置【options0="32"】，否则静态话后，打不开
1. 所有参数中辅助录入可选择所有数据（含停用、禁用、失效数据，不含小于70）,例如字典禁用和非禁用都可以选择。特殊辅助录入除外。
1. 所有界面只要引用非本单据的其他单据号，都要配置穿透监听（红冲单据要同时显示被红冲、红冲、新单据3个，并按创建时间到排序）。
1. 权限配置
    > 一个单据一般对应两个权限编码，一个为单据主表权限编码用于配置单据主表的数据权限分配方式，另一个为扩大共享权限编码用于配置单据的扩大共享数据权限分配方式。两个权限编码对应的操作权限编码应该一致。
    ```html
        1、编码命名规范
           * 单据主表权限编码：一般为单据号；
           * 扩大共享权限编码：单据主表权限编码.Share
           * 扩大共享权限名称：单据主表权限名称-扩大共享 。 
        2、所有UI服务都要有权限。
        3、所有的单据的权限字段标题要同界面标题一致，使用同一资源。
        4、商户字段不应该作为权限字段，商户是程序中添加的固定条件--UIService ->@CuicodeFiler
        5、操作权限：如SQL工具、Tac工具等，不需要实际存在的权限字段；
        6、内码数据权限：如数据分组、查询工具、穿透定义等，使用内码作为权限字段；
        7、数据权限如组织管理、编码规则等，使用具体的物理字段(bcode/wcode/corpbcode/limbcode等)作为权限字段；
        8、权限码
           * R：查询
           * C：操作
           * B：撤单
           * P：红冲
           * AP	打印附件
           * AC	上传附件
           * AV	预览附件(外部用户)
           * AD	下载附件(外部用户)
       9、编码作为权限字段、且显示成树状结构的单据，权限匹配约定为：编码权限匹配时，设置某个节点的权限，则具有了其父节点及其下级节点的相同权限
    ``` 
	
1. 所有界面中字段宽度都使用宏定义方式（4.4版本之后）
	> 单据入口\查询\配置\码表【Grid】表字段宽度规则：
	```
		一、公共说明
			单价、数量、单位：宽度＝100；金额：宽度＝120；
			通用字段宽度为100；宏：E.G.CW(其他字典码表字段：60(四个字)、80（五个字）、100（7个字）不等，根据内容的最大长度而定尽量标题不要折行。??)
		配置界面参数字段宏（用于按钮同参数在同一行的情况）：C.Q.CW=120
		配置界面参数字段宏（用于按钮同参数在同一行的情况）：C.Q.4CW=120,120,120,120
		入口中如果找不到对应的宏则使用详情中的宏
		二、明细说明
		单据编号 150＋居左；宏：E.G.CW.outcode
		状态：70＋居左；宏：E.G.CW.status
		日期：90＋居中；宏：E.G.CW.date
		时间：135+居左；宏：E.G.CW.time
		客商(引用客商码表)：180＋居左；宏：E.G.CW.ccode
		币种：45＋居中；宏：E.G.CW.fcode
		金额：120＋居右；宏：E.G.CW.fcy
		国别：80＋居左；宏：E.G.CW.ncode
		用户：80＋居左；宏：G.CW.ucode
 		业务员/人员：80＋居左；宏：G.CW.wcode
		部门： 100＋居左；宏：E.G.CW.bcode
		公司： 180＋居左；宏：E.G.CW.bcode
		其余组织字段：要求同部门配置。
		商品编码：100+居左；宏：E.G.CW.gcode
		商品类目：130+居左；宏：E.G.CW.gvcode
		商品中文品名：130+居左；宏：E.G.CW.gnamedesc
		商品英文品名：130+居左；宏：E.G.CW.gnamedesc
		银行：150+居左；宏：E.G.CW.bankcode
		银行账号：135+居左；宏：E.G.CW.bankacccode
		海关编码：120+居左；宏：E.G.CW.hscode
		退税商品编码：120+居左；宏：E.G.CW.tgcode
        序号：70+居右；宏：E.G.CW.idx
		备注：150+大文本+居左；宏：E.G.CW.remark
		其他要求：
		（1）grid尽量布局为1屏显示；
		（2）grid表底部：
		－－无特殊说明，使用标准分页。
		－－无特殊说明，不要设置多层表头。
	```
	> 单据详情\配置\码表【Record】表字段宽度规则：
	```
		一、公共说明
			通用字段宽度为：150；宏：D.R.CW
			详情分组布局按4个一行单个宽度为150，如：clayoutwids="150,150,150,150"；宏：D.R.4CW
			所有字段标题全部居左
			详情页中标题+字段内容等整体宽度不能超过1100
		二、头部说明
			单据号：150+居左；宏：D.HR.CW.outcode
			状态：80；宏：D.HR.CW.status
			人员：80+居左+隐藏；宏：D.HR.CW.wcode
			部门：100+居左+隐藏；宏：D.HR.CW.bcode
			业务员：150+居左；宏：D.HR.CW.bwcode
			公司：150+居左；宏：D.HR.CW.corpbcode
			核算组：150+居左+隐藏；宏：D.HR.CW.costbcode
		三、底部说明
			版本号：30+居中；宏：D.BR.CW.vsn
			日期：100+居左；宏：D.BR.CW.date
			时间：135+居左；宏：D.BR.CW.time
			创建人、修改人：80+居左；宏：D.BR.CW.ucode
			生效时间:135+居左；宏：D.BR.CW.time
	```
	
	> 单据详情【Grid】表字段宽度规则：
	
	```
		一、公共要求： 明细规范中没有定义的参见单据入口【Grid】表字段宽度规则
			数字居右、文本居左
			小数位：根据VO注解走
			单价：100；宏：D.G.CW.upric
			数量：100；宏：D.G.CW.qty
			单位:80；宏：D.G.CW.unit
			金额：100；宏：D.G.CW.fcy
			xx率的字段（汇率、利润率、增值税率、退税率、佣金率），换算比例：宽度 80 ；宏：D.G.CW.rate
			货号属性： 100  ;宏：D.G.CW.gattr
			库存属性： 100  ;宏：D.G.CW.sgattr
			中文品名:130;宏：D.G.CW.gcnamedesc
			英文品名:130;宏：D.G.CW.genamedesc
			单位毛重(KG)、单位净重(KG)、单位体积(M3)、总毛重(KG)、总净重(KG)、总体积(M3)：95；宏：D.G.CW.gpackqunit
			包装数量、包装单位、内装数量:80;宏：D.G.CW.gpack
  		    业务员/人员：80＋居左；宏：G.CW.wcode
            用户（通用：创建人，修改人，商务，等）：80＋居左；宏：G.CW.ucode
			通用字段宽度为100；宏：D.G.CW
		二、明细说明
			单据编号 150＋居左；宏：D.G.CW.outcode
			状态：70＋居左；宏：D.G.CW.status
			日期：90＋居中；宏：D.G.CW.date
			时间：135+居左；宏：D.G.CW.time
			客户：140＋居左；宏：D.G.CW.ccode
			币种：50＋居中；宏：D.G.CW.fcode
			汇率：90+居右；宏：D.G.CW.rate
			金额：100＋居右；宏：D.G.CW.fcy
			部门： 100＋居左；宏：D.G.CW.bcode
			公司： 100＋居左；宏：D.G.CW.corpbcode
			序号：70+居右；宏：D.G.CW.idx
			序号（RGrid）：60+居右；宏：D.RG.CW.idx
			其余组织字段：要求同部门配置。
			业务类型：70+居左；宏：D.G.CW.busitype
	```
1. 对于Grid表中列少于5列时（操作列不算）组织类字段（如：公司、部门、记账部门、核算组、业务员、库存组织、上报单位）宽度宏请使用RES.FT-CODE.E.G.CW.200
1. 部门名称中路径显示级次约定
```
所有界面中配置FT-CODE.Bcode和FT-CODE.BWcode码表的列需要有以下属性
showfname="true" tipIfOverflow="true" uiprops.textDir="rtl"
更多参数中的动态参数“部门” 配置方式：
    a.屏蔽掉bcode
    b.增加bcode_ex字段
例如：
<c name="bcode" title="${RES.F?部门}" sqltype="12" width="${RES.$.E.Q.CW}"  codedata="#FT-CODE.Bcode" disableed="true" showfname="true" tipIfOverflow="true" uiprops.textDir="rtl"  aidInputerBtn="true" selectMiddle="true"/>
```
1. 所有带分页的查询界面，如果配置了排序字段尤其是日期排序的，都要配置一个强制最后排序字段(一般使用唯一码)。
	> 正例
	```
		dsprops.lastSort="['ccode']"
	```
	
1. ~~所有VO，选择码表的字段都要标注@CodeTable注解进行校验~~
1. 操作列定位在窗口左部
1. 所有弹出框插入值，处理规范。

	```
	一、如果是服务端完成的插入值，分以下情况处理：
	1)未插入任何内容时，不要刷新页面
	2）若有插入内容时，刷新页面后将光标定位到插入内容最后一行上（如【部门与人员关系】中《新增人员》、【业务员与用户关系】中《添加用户》）
	二、如果是客户端完成的插入值，分以下情况处理：
	1）未插入任何内容时，界面焦点不变
	2）若有插入内容时，将光标定位到插入内容最后一行上（【合同审批单】）
	```
1. 所有选择码表返回编码的字段，默认都要编辑禁止，如无特殊要求都要显示名（币种、商品编码）。
1. 所有日期类型字段，如无特殊要求则都要可以编辑。
1. 程序中需要用到商户的优先原则使用cuicode作为参数传递，实在取不到则使用UserSession
1. 所有工作台配置分析模板时字段的求和方式约定如下：
> 求和方式配置分三种：分组、求和、唯一汇总；（如无特殊说明都可以使用唯一汇总）
1. 编码规则定义规范
    * 配置类、基础资料类的在文件中定义
    * 业务单据类的不要在文件中缺省手工录入
1. 引用检查提示语规范
    * 如文档无特殊说明，提示语统一使用“当前码表/配置单据名称+‘编码值’已被引用，禁止操作！”
1. 提示语中的状态统一不用加单引号。

### 数据库规范

1. 数据库字段审核机制：开发人员添加字段要找李朋+石宏宇+小雷审核。
1. order by 使用规范
	```
	1、创建视图不允许使用order by语句
	2、分页SQL不支持【order by 数字】，必须是【order by 列名/别名】
	```


### 单据功能规范

#### 单据定义配置

1. 单据定义所指定的主VO如果含有商户字段那么要增加  sheetflags=4,查询的单据定义也如此。

#### 单据公共功能

> 所有功能都会对应一个单据定义，所以此公共规范不局限于类似合同等标准单据，也适用于一些配置、码表类的单据

1. 为方便项目二次开发的标准规范(参见ReferenceFunctionService.java)

	* 获取标准中的VO.class统一使用工具类获取，不再使用VO.class的方式
	> 正例:
	
	```
		//获取销售合同主表VO的Class
		Class<SalOrd>   ordClass = SheetUtils.getVOClass(sheetcode);
		VOClassInfo     ordVOClassInfo = SheetUtils.getVOClassInfo(sheetcode);
		Class<SalOrd>   ordClass = ordVOClassInfo.getRelaClassByTable("主表表名");
		//获取此对象中的子表/附表Class
		VOClassInfo   ordVOClassInfo = SheetUtils.getVOClassInfo(sheetcode);
		Class<SalOrdGood>   ordClass = ordVOClassInfo.getRelaClassByTable("子表表名");
	```
	
	* 创建主/子表/附表的VO对象，统一采用VOClassInfo.newInstance(表名)的方式
	
	> 正例：
	
	```
		//创建销售合同主表VO对象
		VOClassInfo   ordVOClassInfo = SheetUtils.getVOClassInfo(sheetcode);
		SalOrd  salord = ordVOClassInfo.newInstance() or  ordVOClassInfo.newInstance("主表表名");
		//创建子表VO对象
		SalOrdGood   goods = ordVOClassInfo.newInstance("子表表名");
		//创建附表VO对象
		SalOrdExt   salordext = ordVOClassInfo.newInstance("附表表名");
	```
	
	* 数据读/写操作使用DAO实例方式
	
	> 正例：
	
	```
		//创建DAO 
		DAO  dao = SheetUtils.newDAO(sheetcode)
		
	```
	
	* 数据读操作使用DB实例方式
	
	> 正例：
	
	```
		DBUtils.read("table", (db)->{
			Class<SalOrd>   ordClass = SheetUtils.getVOClass(sheetcode);
			SalOrd[]  salords =	db.query(ordClass, queryParams);
		});
	```

#### 单据入口-查询面板

* 参见[开发规范(查询面板篇)](FTDevQueryPaneRuleForce.md)

#### 单据入口-按钮面板

1. 按钮样式
    > 采用文字+框的模式
1. 单据入口\查询\配置\码表维护按钮布局顺序：
    > 《保存》《新建》（空白单据）按钮、《数据拷贝》按钮、《复制》按钮、《打开》、《其他按钮N》、《审批情况》按钮
1. 无特殊说明，入口按钮都配置方式为可点击条件
1. 《新建》按钮权限说明
	> 使用当前单据的 C权，若为单据入口则配置显示条件；否则配置点击条件
	> * 正例：
	```
	界面属性控制中配置：
	<UIOpts>
        <uiname>ft_trd_prj</uiname>
        <button>newSheet</button>
        <sheetcode>FT-TRD.TX.Prj.CscdExpProject</sheetcode>
        <btnOpids>C</btnOpids><!-- 注意使用btnOpids -->
        <ctrlFields>disabled</ctrlFields>
        <setValue>false</setValue>
    </UIOpts>
	```
	
1. 《打开》按钮权限说明
	> 使用当前单据的C,R权限
	> * 正例：
	```
	 <UIOpts>
        <uiname>ft_trd_prj</uiname>
        <button>openSheet</button>
        <sheetcode>FT-TRD.TX.Prj.CscdExpProject</sheetcode>
		<dataOpids>R,C</dataOpids><!-- 注意使用dataOpids -->
        <ctrlFields>disabled</ctrlFields>
        <setValue>false</setValue>
        <UIVals>
			<uiname>ft_trd_prj</uiname>
			<colname>prjicode</colname>
			<sqltype>12</sqltype>
			<matchMode>6</matchMode>
		</UIVals>
    </UIOpts>
	```
    
1. 《打开》按钮功能描述
	* 1、单据入口数据为单选方式：若存在当前行，点击该按钮后打开当前行单据；
	* 2、单据入口为多选方式：
		- （1）入口未勾选单据时，若存在当前行，点击该按钮后打开当前行单据；
		- （2）入口勾选了单据时，点击该按钮后打开所有勾选择行单据；
	
1. 《复制》按钮权限说明
	* 使用当前单据的C权限
	> 正例：
	```
	界面属性控制中配置：
	<UIOpts>
        <uiname>ft_trd_prj</uiname>
        <button>sheetCopy</button>
        <sheetcode>FT-TRD.TX.Prj.CscdExpProject</sheetcode>
        <dataOpids>C</dataOpids><!-- 注意使用dataOpids -->
        <ctrlFields>disabled</ctrlFields>
        <setValue>false</setValue>
	</UIOpts>
	```
1. 查询服务权限配置：R,C(详情同理)
	> 正例：
	```
	在对应的UIService方法中添加注解如下：
	@AuthParam(sheetCode = "FT-SET.Off.AdjPay", opids = { LimitConst.Opid_R, LimitConst.Opid_C })
	QueryResults<AdjPayOff> queryOffUI(RecPayOffEntryParams params);
	```
	
#### 单据入口-列表

1. 入口数据* 参见[开发规范(查询数据篇)](FTDevQueryDataRuleForce.md)

1. 单据入口状态、单据明细状态（标题后面的）值背景颜色要求
	* （1）除‘驳回’外的可修改状态：紫色；
	* （2）'驳回'状态（15）：红色；
	* （3）‘提交’/‘待审’状态（>=20且<70):黄色;
	* （4）‘生效’状态（70）：绿色;
	* （5）‘终结’状态（75）：无；
	* （6）撤证/作废/失效/停用（90）：灰色。
	
	> 正例：
	<img src='help/FT-CODE/images/FTDevRuleForce/1002.png' />
	<img src='help/FT-CODE/images/FTDevRuleForce/1003.png' />

1. 如果单据有红冲功能，则列信息需要显示【被红冲】勾选项
	>正例：
	```
	<c name="redflag" sqltype="4" title="红冲" width="50" hidden="true" uiprops.valueAccess="new snsoft.ft.comm.sheetred.FTSheetRedFlagValueAccess({})" />
	<c name="redflag#5" sqltype="4" title="被红冲" width="55" />
	```

#### 单据详情


1. 无特殊说明，单据头部按钮都配置为可显示条件；
1. 无特殊说明，单据内GRID表中的按钮都配置为可点击条件；
1. 单据详情表头区域单据号、状态、业务员、公司、核算组（如启用则存在）并且全部非空，核算组不启用时不要非空
	> 正例：
	<img src='help/FT-CODE/images/FTDevRuleForce/1004.png' />
	
1. 单据详情、配置录入、码表录入界面内字段带有：状态、创建人、创建时间、修改人、修改时间、统一为非空，并带有默认值。如存在版本号的则版本号也要非空。修改时间后面布局生效时间允许为空
	> 正例：
	<img src='help/FT-CODE/images/FTDevRuleForce/1005.png' />
1. 单据详情TAB页权限配置？
1. 《提交》  [提交说明](help.html?helpFile=help/FT-CODE/FTSheetSubmit.md)
1. 《版本修改》 [版本备份配置说明](help.html?helpFile=help/FT-CODE/FTVBHistory.md)
1. 《红冲》？
1. 《红冲生成新单据》？
1. 《撤销红冲》？
1. 〖审批情况〗？
1. 配置穿透定义，用于其他单据穿透打开本单据（包括本单据入口）
	* 穿透时返回funcid，用于支持界面路由功能；
	* 注意穿透中：不设置标题；
	* 穿透定义打开界面时，都要判断一下权限
1. 单据生效后根据当前单据自动生成的下级单据“创建人”、“修改人”拷贝当前单据“创建人”字段值；
1. 单据服务实现规则
	```
		单据服务接口必须继承 FTBusiAccessService
		单据服务实现必须继承 FTBusiAccessServiceImpl
		单据继承BusiAccessService的service接口必须打SheetInfo注解
	```
1. 提高单据的提交效率,单据中提交时不需要做客户端非空检查的 表（表非空和字段非空）请配置 disableCheckNonBlank="true"属性
1.  record表的 xxxcodelist要添加  aidInputableIfRdonly="true"  mutipleLine="true" aidInputerBtn="true" tipIfOverflow="true"  属性！
1. 单据内主表刷新请使用 table.refreshTable(true, 0, null) 方法，避免多单打开时刷新跳回第一条！
1. 单据号处理规则

	* 若定义了编码规则，根据分码规则上是否允许修改，动态设置是否只读；否则设置为可改；
	* 新建空白单据时，未定义编码规则直接赋空值；
	* 单据复制时若未定义编码规则，则直接赋值‘请输入单据号’，否则按编码规则自动分配；
	* 单据拷贝新建时若未定义编码规则且编码为空，则直接赋值‘请输入单据号’，否则按编码规则自动分配；
	
	> 正例
		```
		单据拷贝配置监听：#FT-COMM.FTOutCodeSheetCopyListener?[{}]
		可参见：FT-TRD.TX.Prj.AgcyExpProject.xml
		```
	* 单据提交时若单据外码值为‘请输入单据号’，则提示错误“请修改XXX！”（XXX代表单据外码标题）；
	
	> 正例
		```
		提交检查监听：#FT-COMM.FTOutCodeCheckSVListener?[{}]
		```
1. 单据详情配置【单据默认值监听】以处理商户单据默认值，
    > 正例
    * JS监听,建议配置在系统功最前面（至少在OperateJslistener之前）
    ```
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.code.comm.SheetCuiDefaultValueJSListener({tgtUINames:["*"]})</Funcimpl>
            <Remark>商户默认值</Remark>
        </Functions>
    ```
    * UI监听，此监听要放到所有监听之后
    ```
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl>FT-CODE.SheetCuiDefaultValueUIListener?[{'sheetCode':'FT-TRD.TX.Prj.AcPPImpProject'}]</Funcimpl>
            <Remark>商户默认值</Remark>
        </Functions>
    ```
1. 单据详情配置要求Tab页切换时，界面不要抖动（主要是滚动条引起）配置参考合同审批单。

1. 单据详情基础信息界面当前页面已经能够显示全的情况下不允许纵向滚动条可操作。

1. 单据详情内按钮规范（表头除外）
```
1)显示条件：根据文档中的业务要求控制；
2)可点击条件：无特殊说明，都要根据权限控制；
3)执行条件：要根据权限控制
```

### 码表功能规范

1. 码表弹出界面需配置 disableTblEnterEd=true 属性，否则会影响双击效果！
1. 提供的码表辅助录入默认可选择所有有效数据（不含停用、禁用、失效数据，不含小于70），但要提供可配置参数能够选择所有数据（含停用、禁用、失效数据，不含小于70，参见字典辅助录入）
1. 对于状态虚列（bedate：生效日期,ledate：失效日期），使用字典：FT-CODE.EffectiveStatus,，并用公共方法：xxx? 
1. 所有码表数据维护界面都要配置snsoft.bas.sheet.VOListener.new
1. 所有码表数据维护界面都要配置创建人、创建时间、修改人、修改时间且统一为非空
1. 系统中同一类的码表要保持统一，不要因为某些条件或则显示列不同而做多个。（例如：仓库，整个系统都要选择同一个仓库码表）
1. 辅助录入焦点定位原则
    ```
       一、总体原则：辅助录入弹出时，焦点定位到第一个查询参数上，按回车查询并焦点定位到查询数据的第一行，若存在多选的则将已选中的打钩
       二、按回车键处理方式：
           1.不分级
               a.单选  选中关闭
               b.多选  回车选择当前行或取消当前行，采用ctrl+enter 关闭
           2.分级
               树形-单选：
                   a.中间级  若未展开，则展开并调到子节点第一个，若已经展开则调到子节点第一个。（按“-+”键进行收缩及展开子节点？）
                   b.末级  选中并关闭
               树形-多选：（回车键或xx键选中当前行）
                   a.中间级  若未展开，则展开并调到子节点第一个，若已经展开则调到子节点第一个。（按“-+”键进行收缩及展开子节点？）
                   b.末级  回车选择当前行或取消当前行，采用ctrl+enter 关闭
               非树形-单选：
                   a.中间级  进入到其子节点界面
                   b.末级   选中并关闭
               非树形-多选：
                   a.中间级  进入到其子节点界面
                   b.末级   回车选择当前行或取消当前行，采用ctrl+enter 关闭
   ```
   
### 对话框规范

1. 针对对话框，《确定》按钮是否永远可点击的规范:都统一为《确定》按钮始终可点击，点击的时候做对应的检查。
1. 《新建》对话框《确定》按钮检查规范：

	* 若界面中存在分组标题的，统一提示为：
	```    
    〖分组标题〗
    以下列禁止为空：xxx,xxx,...
    ```
    * 若界面中不存在分组标题的，统一提示为：
    ```
      以下列禁止为空：xxx,xxx,...
    ```
    * 若只有Grid表的，则提示“请录入数据！”
1. 程序中需要创建对话框的，统一使用界面定义配置，如不用则需要申请。
1. 对于入口新建对话框中单列的情况宽度宏请使用E.ND.CW=150,E.ND.4CW=150

    
### 系统选项添加规范

1. 添加系统选项时要同步在选项配置中添加，选项配置中的分类参考：[选项分类](FTDevRuleOptionClass.md)
1. 对于带有是否类码表的系统选项，都要配置增加默认值，且要配置成按钮方式（有特殊说明的除外,如：开关方式)；

### 查询功能规范

### 国际化规范

1. [参见帮助中心国际化配置](https://n.esnsoft.cn/N9Bhelp/help.html?helpFile=/help/SN-CMC/International/International.md)
1. 界面属性支持keyDupTip用于指定底层判断主键重复时的提示语
	
	* 方式1：直接配置提示语(用于不需要国际化的场景)
	
	* 方式2：指定具体重复列方式(优先采用此方式)
		> 使用场景
		```
			一般常用于配置表、码表，这类表的主键是有具体含义的。当主键重复时如果提示：’主键‘值重复，就不太合适了。这时应该指出具体哪个列重复。
		```
		> 正例
		```
	     	  规则：keyDupTip:'TipKeyColumns=col1,col2,...'
	     	  dstoreprops="keyDupTip:'TipKeyColumns=hscode'"
	     	      当主键重复时提示：‘海关编码’ 值重复
		```
1. 在定义非界面资源（如：提示语、错误信息、进度条打印信息等）要定义到ResBundleERRXX.inf中，否则定义到ResBundleXX.inf中
1. 对于启用多语的码表/配置名称匹配的规范
    * 在维护界面，用“名称”做参数时，应只匹配当前启用语种 名称 列值;
    * 在选择码表对话框中，用“名称”做参数时，应按当前语种匹配对应语种名称列值; 


### 配置功能规范

1. 所有配置类列表界面都要配置snsoft.bas.sheet.VOListener.new
1. 所有配置类列表界面都要配置创建人、创建时间、修改人、修改时间且统一为非空

### 缓存相关规范

1. 为兼容数据库排序规则，特制定以下强制缓存规范：

	> 正例
	
	```
	1.加载数据库数据并缓存，使用内存排序；
	2.Demo：
	Object[][] a = KVCacheUtils.cache(dataSource,key,()->{
		try(Database db = DBUtils.newDatabase(dataSource))
		{
			Object[][] values = db.query3("select code,name from tblname");
			Arrays.sort(values,ObjectArrayComparator.cmp1);// 使用内存排序
			return values;
		}
	});
	```

### 效率相关规范

* [开发规范(效率篇)](FTDevSpeedRuleForce.md)

### 代码相关规范

* [开发规范(代码篇)](FTDevCodeRuleForce.md)

### 资源文件

1. 资源文件扩展方式

    * 引用方式：﻿$PARENT$=FT-SNACOMM
        ```
        有点：可把其他文件中的内容引入到本文件中，避免重复定义，或则改写被引用文件中的宏对应的值。
        缺点：对于直接使用 FT-SNACOMM.key 的宏此方式无法被改写
        ```
    * 覆盖方式：Resource目录配置cfg_priority.properties
        ```
        优点：可在本工程中整体覆盖掉同名的资源文件
        缺点：可继承性差。
        ```
    * 增量覆盖方式：DiffResBundle_[sysid]_[lang].inf
        ```
        优点：可在本工程中在需要覆盖的文件名称前加Diff来命名资源文件,放到资源目录同名路径下，可增量覆盖。
        缺点：暂无。
        ```
1. 资源文件中定义宽度宏时必须定义在ResBundle[系统号]-WIDTH.inf文件中。
    ```
    解决问题：主要为了解决多语言翻译的时候不会翻译宽度的，因此要解决在使用其他语言时始终能够找到宽度的宏。
   ```