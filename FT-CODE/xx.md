# 1. 强制开发规范（不可犯错）

## 1.建模规范

1. 数据库字段审核机制：开发人员添加字段要找李朋+石宏宇+小雷审核。
1. 所有VO，选择码表的字段都要标注@CodeTable注解进行校验
1. order by 使用规范
	* 创建视图不允许使用order by语句
	* 分页SQL不支持【order by 数字】，必须是【order by 列名/别名】
1. 单据定义所指定的主VO如果含有商户字段那么要增加  sheetflags=4,查询的单据定义也如此。

## 2.代码规范

1. 程序中需要创建对话框的，统一使用界面定义配置，如不用则需要申请。
1. 权限相关
	> 一个单据一般对应两个权限编码，一个为单据主表权限编码用于配置单据主表的数据权限分配方式，另一个为扩大共享权限编码用于配置单据的扩大共享数据权限分配方式。两个权限编码对应的操作权限编码应该一致。
	1. 编码命名规范
           * 单据主表权限编码：一般为单据号；
           * 扩大共享权限编码：单据主表权限编码.Share
           * 扩大共享权限名称：单据主表权限名称-扩大共享 。 
    1. 所有UI服务都要有权限。
    1. 所有的单据都要有权限字段。
    1. 商户字段不应该作为权限字段，商户是程序中添加的固定条件--UIService ->@CuicodeFiler
    1. 操作权限：如SQL工具、Tac工具等，不需要实际存在的权限字段；
    1. 内码数据权限：如数据分组、查询工具、穿透定义等，使用内码作为权限字段；
    1. 数据权限如组织管理、编码规则等，使用具体的物理字段(bcode/wcode/corpbcode/limbcode等)作为权限字段；
    1. 权限码
		* R：查询
		* C：操作
		* B：撤单
		* AD：附件删除
		* AU：附件上传
		* P：红冲

	1. 编码作为权限字段、且显示成树状结构的单据，权限匹配约定为：编码权限匹配时，设置某个节点的权限，则具有了其父节点及其下级节点的相同权限
	1. 所有弹出框插入值，处理规范。
	> 正例
	```
	一、如果是服务端完成的插入值，分以下情况处理：
	1)未插入任何内容时，不要刷新页面
	2）若有插入内容时，刷新页面后将光标定位到插入内容最后一行上（如【部门与人员关系】中《新增人员》、【业务员与用户关系】中《添加用户》）
	二、如果是客户端完成的插入值，分以下情况处理：
	1）未插入任何内容时，界面焦点不变
	2）若有插入内容时，将光标定位到插入内容最后一行上（【合同审批单】）
	```
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

## 3.缓存规范

## 4.文档规范

## 5.界面规范

1. 所有界面标题、列标题、按钮标题、提示语等等出现中文文字的地方都要支持国际化，即使用资源文件方式配置。[参见帮助中心国际化配置](https://n.esnsoft.cn/N9Bhelp/help.html?helpFile=/help/SN-CMC/International/International.md)
1. 单据定义标题、权限定义标题、编码规则标题、单据复制、版本备份、撤单定义、单据红冲、单据说明标题要统一
	> 正例
	```
	资源文件中配置title_单据号/界面号/xx，无特殊情况尽量保障上述定义中的编号一致。
	```
1. 打开单据入口标签页标题为规则为：[单据标题]-入口
1. 菜单打开界面配置方式使用宏定义；如：title="@UI?客商"
1. 打开单据详情/普通码表/普通配置界面的标签页标题规则为：[单据标题]
1. 配置穿透定义时不要指定标题，特殊情况除外；
1. 所有界面当超出当前页面显示范围后都要带滚动条(横向，纵向)
1. 配置组织相关码表在FT中必须统一使用FT的码表；
1. 对于入口即为维护界面的，在删除行后不需要关闭当前页签，需要在界面主表上配置：uiprops.closeTabOnDelete="false"；
1. 对于按钮工具栏无新建按钮，但操作列中有新增按钮的界面，默认都要增加一个新行（配置界面允许插入属性）；
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
1. 界面小数位数约定
	* 最大小数使用VO定义，
	> 正例
	```
	@VOField(maxdeci = "${OPTS.Decimal.ERate}")
	@Positive
	private BigDecimal			freightfcerate;
	```
	* 最小小数在界面xml中定义
1. Grid表列对齐规则约定（有个别字段特殊约定的除外）
	* 值等长居中（日期、币种、成交方式等）
	* 值不等长字符居左（客户、时间、外码等）
	* 数值居右（数量、月、金额等）
1. 所有查询参数中日期/时间带有从xxx到xxx格式配置的标题都统一使用"到",而不是"至"。
1. 所有需要在对话框中显示的页面，都必须配置【options0="32"】，否则静态话后，打不开。
1. 所有参数中辅助录入可选择所有数据（含停用、禁用、失效数据，不含小于70）,例如字典禁用和非禁用都可以选择。特殊辅助录入除外。
1. 所有界面只要引用非本单据的其他单据号，都要配置穿透监听（红冲单据要同时显示被红冲、红冲、新单据3个，并按创建时间到排序）。
1. 所有带分页的查询界面，如果配置了排序字段尤其是日期排序的，都要配置一个强制最后排序字段(一般使用唯一码)。
	> 正例
	```
		dsprops.lastSort="['ccode']"
	```
1. 对于Grid表中列少于5列时（操作列不算）组织类字段（如：公司、部门、记账部门、核算组、业务员、库存组织、上报单位）宽度宏请使用RES.FT-CODE.E.G.CW.200
1. 对于入口新建对话框中单列的情况宽度宏请使用RES.FT-CODE.E.G.CW.200
1. 所有带操作列的界面，操作列都要定位在窗口左部
1. 所有选择码表返回编码的字段，默认都要编辑禁止，如无特殊要求都要显示名（币种、商品编码）。
1. 所有日期类型字段，如无特殊要求则都要可以编辑。


## 10.单据功能规范

1. 单据定义所指定的主VO如果含有商户字段那么要增加  sheetflags=4,查询的单据定义也如此。

## 11.查询功能规范

## 12.码表功能规范

## 13.配置功能规范

## 30.前端规范