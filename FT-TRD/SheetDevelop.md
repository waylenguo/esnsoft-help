## 【自营进口合同】标准开发文档

|日期|作者|备注|
|------|------|------|
|2021-06-01|吴小海|初稿|
|2021-06-XX|冀小雷 陈泰林|评审|


### 概述

> 此文档以【自营进口敞口销售合同审批单】为案例,详细阐述标准单据开发规范及步骤

### 开发准备

已完成单据开发相关设计工作，包含：单据号划分、工程划分、包名划分、表结构设计等

#### 设计规划

单据号：FT-TRD.TX.Prj.SeSOImpProject
单据名称: 自营进口敞口销售合同审批单
工程划分：trd-api trd-impl trd-ui trd-xjs
包名划分：snsoft.ft.trd.tx.prj.sesoimp
VO划分：SeSOImpProject
Service划分：SeSOImpProjectService SeSOImpProjectUIService
表名：ft_trd_prj

#### 相关术语

API工程：trd-api
IMPL工程：trd-impl
UI工程：trd-ui
XJS工程：trd-xjs



### 开发规范要求

#### 包结构及类规范

包结构：

	snsoft.[System].[Module].[Busi].model         实体类(API工程)
    snsoft.[System].[Module].[Busi].vo            VO实体类(API工程)
    snsoft.[System].[Module].[Busi].comm          常量、通用类(API、IMPL、UI工程)
    snsoft.[System].[Module].[Busi].util          工具类(IMPL、UI工程)
    snsoft.[System].[Module].[Busi].ui            界面UI监听(UI工程)
    snsoft.[System].[Module].[Busi].service       Service接口(API、UI工程)
    snsoft.[System].[Module].[Busi].service.impl  Service接口实现(IMPL、UI工程) 
    snsoft.[System].[Module].[Busi].cli           XJS监听(XJS工程)

类名规范：

	[System]+[Module]+[Busi]+Service：API接口
    [System]+[Module]+[Busi]+ServiceImpl：API接口实现 
    [System]+[Module]+[Busi]+UIService：UI接口
    [System]+[Module]+[Busi]+UIServiceImpl：UI接口实现
    [System]+[Module]+[Busi]+QueryParams：API接口查询参数
    [System]+[Module]+[Busi]+VO：VO类
    [System]+[Module]+[Busi]+[Func]+Listener：XJS监听
    [System]+[Module]+[Busi]+[Func]+UIListener：UI监听

范例：

     snsoft.trd.bill.pay.model.TrdPayBillQueryParams  查询参数
     snsoft.trd.bill.pay.vo.TrdPayBillVO  VO类
     snsoft.trd.bill.pay.service.TrdPayBillService  API接口
     snsoft.trd.bill.pay.service.impl.TrdPayBillServiceImpl  API接口实现
     snsoft.trd.bill.pay.service.TrdPayBillUIService  UI接口
     snsoft.trd.bill.pay.service.impl.TrdPayBillUIServiceImpl  UI接口实现
     snsoft.trd.bill.pay.cli.TrdPayBillSubmitListener  XJS 提交监听
     snsoft.trd.bill.pay.cli.TrdPayBillSaveListener  XJS 存盘监听


注:
1.[System]系统简写  [Module]模块缩写  [Busi]业务缩写  [Func]功能缩写
2.如果项目具体业务划分更加详细,可灵活扩展[Busi]层次
3.注意IMPL包结构需严格按照规范,否则会导致API服务找不到
4.实际开发过程中,存在通用功能封装 包名、类名规范尽量向该规范靠拢


#### 数据库规范

1. 表结构定义字段，谨慎使用Char类型，如果必须使用Char类型必须保证数据存储长度同表结构定义长度
 例：表结构定义字段长度char(2),界面定义字典长度1,Oracle环境存储长度为1数据,char类型会在后面补充一个空白
    当我们从数据库读取配数据的时候，数据多一个空白会导致自己写的处理程序有问题

#### 配置规范

##### 单据配置

命名规范：

	[System].[Module].[Busi].xml   XML命名

命名范例：

    FT-TRD.Bill.Pay.xml

注：
1.此处 [System] 完整系统号, [Module] [Busi]首字母大写 采用驼峰命名方式
2.单据相关配置，除系统配置合并在一个文件以外，都采用该命名规范
3.部分配置不是.xml文件 需根据实际情况自行调整

##### 系统功能配置

命名规范：

	[System].[Module].[Busi].Entry.UI.xml   入口UI
    [System].[Module].[Busi].Entry.SV.xml   入口SV
    [System].[Module].[Busi].UI.xml   详情UI
    [System].[Module].[Busi].SV.xml   详情SV

命名范例：

    FT-TRD.Bill.Pay.Entry.UI.xml  入口UI
    FT-TRD.Bill.Pay.Entry.SV.xml  入口SV 
    FT-TRD.Bill.Pay.UI.xml   详情UI
    FT-TRD.Bill.Pay.SV.xml   详情SV


##### UI配置

命名规范：

	[System].[Module].[Busi]Entry.xml   入口界面
    [System].[Module].[Busi]Detail.xml   详情界面
    [System].[Module].[Busi]Dialog.xml   弹框界面

命名范例：

    FT-TRD.Bill.PayEntry.xml  入口界面
    FT-TRD.Bill.PayDetail.xml  详情界面
    FT-TRD.Bill.PayDialog.xml  弹框界面

注:
1.此处UI配置只是为了展示目录层级结构,实际情况下应该为具体目录结构 如 ui/FT-TRD/Bill/PayEntry.xml


#### 代码规范

1. 严禁代码中写死常量字符串，需抽取相关常量类
2. 严禁相关API、UI、组件服务 声明Spring组件命名为字符串，应该抽取常量类来 方便引用
例：
   
   正例:
   @Service(ValidService.BeanName)
   public class ValidServiceImpl implements ValidService
   
   反例:
   @Service("SN-CORE.ValidService")
   public class ValidServiceImpl implements ValidService
3. 数据库操作尽量通过DAO方式操作，如特殊情况才允许使用DB操作

4. 严禁在循环中执行DB操作

5. 客户端操作获取系统选项，不允许单独发送远程请求获取 应该采用 SysOptionUIListener监听配置 客户端通过 table.getOption 获取

6. 频繁读取配置 需要根据实际情况分析是否使用缓存 参考   [缓存处理](help.html?helpFile=help/SN-CMC/Cache/Cache.md) [Redis组件](help.html?helpFile=help/SN-CMC/Database/Redis/RedisCache.md)

### 单据开发

#### 表结构定义

1. 在trd-ui工程下创建src/main/resources/cfg/sndata/CreateDatabaseFT-TRD-30_TX.xml
1. XML配置添加单据表配置

```
<table id="33040" name="ft_trd_prj" title="项目表" datasrcid="FT-TRD_W" rdatasrcid="FT-TRD_R"  storegrp="SNFTTRDDATA_TBS">
        <column name="prjicode" title="项目内码" type="VARCHAR(SZIBILL)" primkey="true"/>
        <column name="prjcode" title="项目号" type="VARCHAR(SZNBILL)" notnull="true"/>
        <column name="status" title="状态" type="VARCHAR(SZSTATUS)"/>
        <column name="wfcode" type="VARCHAR(SZWFCODE)" title="审批编码" />
        <column name="wfuid" type="VARCHAR(SZWFUID)" title="审批节点" />
        <column name="cuicode" title="商户" type="VARCHAR(SZCUICODE)"/>
        <column name="bcode" title="业务员部门" type="VARCHAR(SZBCODE)"/>
        <column name="wcode" title="业务员" type="VARCHAR(SZWCODE)"/>
        <column name="corpbcode" title="公司" type="VARCHAR(SZBCODE)"/>
        <column name="costbcode" title="核算组" type="VARCHAR(SZBCODE)"/>
        <column name="sheetcode" title="单据号" type="VARCHAR(SZSHEET)"/>
        <column name="ratifydate" title="生效时间" type="DATE"/>
        <column name="curratifydate" title="本版本生效时间" type="DATE"/>
        <column name="submitdate" title="提交时间" type="DATE"/>
        <column name="performdate" title="审核时间" type="DATE"/>
        <column name="vsn" title="版本号" type="INTEGER"/>
        <column name="vsnflag" title="版本标记" type="INTEGER"/>
        <column name="remark" title="备注" type="VARCHAR(SZTEXT)"/>
        <column name="vprepare" title="创建人" type="VARCHAR(SZUCODE)"/>
        <column name="predate" title="创建时间" type="DATE"/>
        <column name="modifier" title="修改人" type="VARCHAR(SZUCODE)"/>
        <column name="modifydate" title="修改时间" type="DATE"/>
        <column name="businessucode" title="商务" type="VARCHAR(SZCCODE)"/>
        <column name="prjname" title="项目名" type="VARCHAR(SZNAME)"/>
        <column name="prjdate" title="立项日期" type="DATE"/>
        <column name="mordcode"  title="主合同号" type="VARCHAR(SZNBILL)" />
        <column name="mordicode"  title="主合同内码" type="VARCHAR(SZIBILL)" />
        <column name="mordsheetcode"  title="主合同单据号" type="VARCHAR(SZSHEET)" />
        <column name="upsheetcode" title="上游单据类型" type="VARCHAR(SZSHEET)" />
        <column name="upicode" title="上游单据内码" type="VARCHAR(SZIBILL)"  />
        <column name="upcode" title="上游单据号" type="VARCHAR(SZNBILL)"  />
        <column name="scmcode" title="创建方式编号" type="VARCHAR(SZSCMCODE)"/>
        <column name="nstype" title="单据创建方式大类" type="VARCHAR(SZTYPE)"/>
        <column name="busielemcodelist" title="业务要素List" type="VARCHAR(SZBUSIELEMCODELIST)"/>
        <column name="pntprjcode" title="父项目号" type="VARCHAR(SZIBILL)"/>
        <column name="pntprjicode" title="父项目内码" type="VARCHAR(SZNBILL)" />
        <column name="tradetype" title="贸易类型" type="VARCHAR(SZTYPE)"/>
        <column name="trademode" title="贸易方式" type="VARCHAR(SZTYPE)"/>
        <column name="busimode" title="经营方式" type="VARCHAR(SZTYPE)"/>
        <column name="psmode" title="购销方式" type="VARCHAR(SZTYPE)"/>
        <column name="purmode" title="加工方式" type="VARCHAR(SZTYPE)"/>
        <column name="bcfcode" title="业务分类定义编号" type="VARCHAR(SZBCFCODE)" />
        <column name="bcfcodelist" title="业务分类定义编号List" type="VARCHAR(SZBCFCODELIST)" />
        <column name="pftcode" title="盈亏测算编号" type="VARCHAR(SZPFTCODE)"/>
        <column name="ccodetrust" title="委托方" type="VARCHAR(SZCCODE)"/>
        <column name="isformprj" title="是否形式项目" type="CHAR(SZYN)"/>
        <column name="quotaappcode" title="额度申请单号" type="VARCHAR(SZQUOTAAPPCODE)"/>
        <column name="quotaappicode" title="额度申请单内码" type="VARCHAR(SZIBILL)"/>
        <column name="issettsps" title="启用结算盈余" type="CHAR(SZYN)"/>
        <column name="extcstype" title="扩展改单类型" type="VARCHAR(SZTYPE)"/>
        <column name="agcytaxrbtmode" title="合作转代理退税方式" type="VARCHAR(SZTYPE)"/>
        <column name="aimpsifcmode" title="代理进口销售开票流程控制" type="VARCHAR(SZTYPE)"/>
        <column name="remark" title="备注" type="VARCHAR(SZTEXT)"/>
        <column name="vprepare" title="创建人" type="VARCHAR(SZUCODE)"/>
        <column name="predate" title="创建时间" type="DATE"/>
        <column name="modifier" title="修改人" type="VARCHAR(SZUCODE)"/>
        <column name="modifydate" title="修改时间" type="DATE"/>
        <!-- 扩展字段 start -->
        <column name="txt00" title="预留文本字段00" type="VARCHAR(SZPRETXT)" />
        <column name="txt01" title="预留文本字段01" type="VARCHAR(SZPRETXT)" />
        <column name="txt02" title="预留文本字段02" type="VARCHAR(SZPRETXT)" />
        <column name="txt03" title="预留文本字段03" type="VARCHAR(SZPRETXT)" />
        <column name="txt04" title="预留文本字段04" type="VARCHAR(SZPRETXT)" />
        <column name="txt05" title="预留文本字段05" type="VARCHAR(SZPRETXT)" />
        <column name="txt06" title="预留文本字段06" type="VARCHAR(SZPRETXT)" />
        <column name="txt07" title="预留文本字段07" type="VARCHAR(SZPRETXT)" />
        <column name="txt08" title="预留文本字段08" type="VARCHAR(SZPRETXT)" />
        <column name="txt09" title="预留文本字段09" type="VARCHAR(SZPRETXT)" />
        <column name="ltxt01" title="预留大文本字段01" type="VARCHAR(SZPRELONGTXT)" />
        <column name="ltxt02" title="预留大文本字段02" type="VARCHAR(SZPRELONGTXT)" />
        <column name="dat00"  title="预留日期字段00" type="DATE" />
        <column name="dat01"  title="预留日期字段01" type="DATE" />
        <column name="dat02"  title="预留日期字段02" type="DATE" />
        <column name="int00"  title="预留整数字段00" type="INTEGER" />
        <column name="int01"  title="预留整数字段01" type="INTEGER" />
        <column name="int02"  title="预留整数字段02" type="INTEGER" />
        <column name="num00"  title="预留数值字段00" type="NUMERIC(SZNUMINT.SZNUMDEC)" />
        <column name="num01"  title="预留数值字段01" type="NUMERIC(SZNUMINT.SZNUMDEC)" />
        <column name="num02"  title="预留数值字段02" type="NUMERIC(SZNUMINT.SZNUMDEC)" />
        <column name="stsext00" title="统计扩充字段00" type="VARCHAR(SZSTSEXT)" />
        <column name="stsext01" title="统计扩充字段01" type="VARCHAR(SZSTSEXT)" />
        <column name="stsext02" title="统计扩充字段02" type="VARCHAR(SZSTSEXT)" />
        <column name="stsext03" title="统计扩充字段03" type="VARCHAR(SZSTSEXT)" />
        <column name="stsext04" title="统计扩充字段04" type="VARCHAR(SZSTSEXT)" />
        <column name="stsext05" title="统计扩充字段05" type="VARCHAR(SZSTSEXT)" />
        <column name="stsext06" title="统计扩充字段06" type="VARCHAR(SZSTSEXT)" />
        <!-- 扩展字段 end -->
        <index name="ft_trd_prj_predate" fields="predate" storegrp="SNFTTRDINDX_TBS" />
        <index name="ft_trd_prj_ratifydate" fields="ratifydate" storegrp="SNFTTRDINDX_TBS" />
        <index name="ft_trd_prj_up" fields="upsheetcode,upicode" storegrp="SNFTTRDINDX_TBS" />
    </table>
```

#### VO&Service&XJS

##### 单据VO

1. VO包名：snsoft.ft.trd.tx.prj.sesoimp.vo
1. VO类名：SeSOImpProject
1. VO类位置：API工程 src/main/java
1. 根据包名、类名、在API工程下新建单据VO
1. 配置说明【[实体类VO](help.html?helpFile=help/SN-CMC/Service/VO/VO.md)】
1. VO参考代码

```
@Table(name = ProjectConstants.PRJ_M_TABNAME)
@ValidResource(ProjectConstants.TRDIMP_RESOURCE_ID)
@SheetInfo(value = SeSOImpProject.SheetCode)
public class SeSOImpProject extends SeImpProject implements BusiElemAble
{
	private static final long			serialVersionUID	= 8698724885393879864L;
	/**
	 * 单据号
	 */
	public static final String			SheetCode			= "FT-TRD.TX.Prj.SeSOImpProject";
	/**状态*/
	@Column
	@NotNull
	@DefaultValue(ApprConstants.STATUS_DRAFT)
	@CodeTable(value = "90.status", params = "sheetcode:'FT-TRD.TX.Prj.SeSOImpProject'")
	private String						status;
	/**
	 * 主表销售要素辅表
	 */
	@Valid
	@OneToOne
	@JoinColumn(name = ProjectConstants.PRJ_INNER_NAME)
	private SeSOImpProjectExtSe			projectextse;
	/**
	 * 主表销售要素收汇方式
	 */
	@Valid
	@OneToMany
	@JoinColumn(name = ProjectConstants.PRJ_INNER_NAME)
	@ResKey(value = "title.F.grp.settsd")
	private List<SeSOImpProjectSeSett>	projectsesettlist;
	/**
	 * 主表销售商品
	 */
	@Valid
	@OneToMany
	@JoinColumn(name = ProjectConstants.PRJ_INNER_NAME)
	@ResKey(value = "title.F.tab.goods")
	private List<SeSOImpProjectSeGood>	projectgoodlist;
	
}
```

```
@Table(name = ProjectConstants.PRJ_M_TABNAME)
@ValidResource(ProjectConstants.TRD_RESOURCE_ID)
public abstract class SeImpProject extends Project
{
    private static final long serialVersionUID = -6028553086818996272L;

	/**贸易类型*/
	@Column
	@CodeTable(value = "DT_FT-CODE.TradeType")
	@NotEmpty(groups = { SubmitGroup.class })
	private String				tradetype;
	/**贸易方式*/
	@Column
	@CodeTable(value = "DT_FT-CODE.TradeMode")
	@NotEmpty(groups = { SubmitGroup.class })
	private String				trademode;
	/**经营方式*/
	@Column
	@CodeTable(value = "DT_FT-CODE.Busimode")
	@NotEmpty(groups = { SubmitGroup.class })
	private String				busimode;
	/**购销方式*/
	@Column
	@CodeTable(value = "DT_FT-CODE.PsMode")
	@NotEmpty(groups = { SubmitGroup.class })
	private String				psmode;
	/**加工方式*/
	@Column
	@CodeTable(value = "DT_FT-CODE.PurMode")
	@NotEmpty(groups = { SubmitGroup.class })
	private String				purmode;
	
}
```

```
@Table(name = ProjectConstants.PRJ_M_TABNAME)
@ValidResource(ProjectConstants.TRD_RESOURCE_ID)
public abstract class Project extends FTSheetVO implements VersionBackupVO, BusiClassifyAble
{
    private static final long serialVersionUID = -9086179112512661143L;
    /**
     * 项目内码
     */
    @Id
    @NotNull(groups = {SubmitGroup.class})
    @DefaultValue("Accode:FT-TRD.InnerCode")
    @Column
    private String prjicode;
    /**
     * 项目号
     */
    @NotNull(groups = {SubmitGroup.class})
    @Column
    @DefaultValue("SheetAccode:@SheetInfo")
    private String prjcode;
    /**
     * 状态
     */
    @Column
    @NotNull
    @DefaultValue(ApprConstants.STATUS_DRAFT)
    private String status;
    /**
     * 商户
     */
    @DefaultValue("UCode:Cuicode")
    @Column
    @Cuicode
    @NotEmpty(groups = {SubmitGroup.class})
    private String cuicode;
    /**
     * 业务员部门
     */
    @Column
    @CodeTable(codetbl = "FT-CODE.Bcode")
    @NotEmpty(groups = {SubmitGroup.class})
    private String bcode;
    /**
     * 业务员
     */
    @Column
    @NotEmpty(groups = SubmitGroup.class)
    @CodeTable(codetbl = "FT-CODE.Wcode")
    private String wcode;
    /**
     * 公司
     */
    @Column
    @CodeTable(value = "FT-CODE.CorpBcode")
    @NotEmpty(groups = {SubmitGroup.class})
    private String corpbcode;
    /**
     * 核算组
     */
    @Column
    @CodeTable(value = "FT-CODE.CostBcode")
    private String costbcode;
    /**
     * 单据号
     */
    @Column
    @NotEmpty(groups = {SubmitGroup.class})
    @DefaultValue(processBean = "SN-CORE.SheetInfoDefaultValue")
    private String sheetcode;
    /**
     * 生效时间
     */
    @Column
    private Date ratifydate;
    /**
     * 本版本生效时间
     */
    @Column
    private Date curratifydate;
    /**
     * 提交时间
     */
    @Column
    private Date submitdate;
    /**
     * 审核时间
     */
    @Column
    private Date performdate;
    /**
     * 版本号
     */
    @Column
    @DefaultValue("1")
    @NotNull()
    private Integer vsn;
    /**
     * 版本标记列名
     */
    @Column
    @DefaultValue("0")
    private Integer vsnflag;
    /**
     * 备注
     */
    @Column
    private String remark;
    /**
     * 项目名
     */
    @Column
    private String prjname;
    /**
     * 立项日期
     */
    @Column
    private Date prjdate;
    /**
     * 合同号
     */
    @Column
    private String mordcode;
    /**
     * 合同内码
     */
    @Column
    private String mordicode;
    /**
     * 合同单据号
     */
    @Column
    @CodeTable(value = "90.sheet")
    private String mordsheetcode;
    /**
     * 创建方式编号
     */
    @Column
    @NotNull
    private String scmcode;
    /**
     * 父项目号
     */
    @Column
    private String pntprjcode;
    /**
     * 父项目内码
     */
    @Column
    private String pntprjicode;
    /**
     * 贸易类型
     */
    @Column
    @CodeTable(value = "DT_FT-CODE.TradeType")
    private String tradetype;
    /**
     * 贸易方式
     */
    @Column
    @CodeTable(value = "DT_FT-CODE.TradeMode")
    private String trademode;
    /**
     * 经营方式
     */
    @Column
    @CodeTable(value = "DT_FT-CODE.Busimode")
    private String busimode;
    /**
     * 购销方式
     */
    @Column
    @CodeTable(value = "DT_FT-CODE.PsMode")
    private String psmode;
    /**
     * 加工方式
     */
    @Column
    @CodeTable(value = "DT_FT-CODE.PurMode")
    private String purmode;
    /**
     * 业务分类定义编号
     */
    @Column
    @NotNull
    private String bcfcode;
    /**
     * 业务分类定义编号List
     */
    @Column
    private String bcfcodelist;
    /**
     * 盈亏测算编号
     */
    @Column
    private String pftcode;
    /**
     * 委托方
     */
    @Column
    @CodeTable(codetbl = "FT-CODE.Ccode")
    private String ccodetrust;
    /**
     * 商务
     */
    @Column
    @CodeTable(codetbl = "FT-CODE.RBWUcode")
    private String businessucode;
    /**
     * 业务要素list
     */
    @Column
    @CodeTable(value = "DT_FT-TRD.BusiElem", delim = ",")
    private String busielemcodelist;
    /**
     * 是否形式项目
     */
    @Column
    @DefaultValue(value = ProjectConstants.DT_YN_N)
    @CodeTable(value = "DT_90.YN")
    private String isformprj;
    /**
     * 单据创建方式大类
     * 10:入口手工新建
     * 20:入口拷贝新建
     * 30:自动生成
     */
    @DefaultValue(value = "10")
    @NotNull
    @Column
    private String nstype;
    /**
     * 上游单据类型
     */
    @Column
    private String upsheetcode;
    /**
     * 上游单据内码
     */
    @Column
    private String upicode;
    /**
     * 上游单据号
     */
    @Column
    private String upcode;
    /**
     * 扩展改单类型
     * 10：A方案改单
     */
    @Column
    @DefaultValue(value = "00")
    private String extcstype;

}
```

```
public abstract class FTSheetVO extends BasVO implements
		// 审批接口
		ApprSheetVO,
		// 单据号+状态接口
		SheetStatusVO,
		// 单据组织结构接口
		SheetBcodeVO
{
	private static final long	serialVersionUID	= -6248577591320367701L;
	/**商户编码*/
	@Column
	@Cuicode
	@DefaultValue(value = "UCode:Cuicode", uivalue = SnsoftConst.NullValue)
	protected String			cuicode;
	@Column
	@CodeTable("FT-CODE.Bcode")
	@NotNull(message = "{bcode}", groups = SubmitGroup.class)
	protected String			bcode;
	@Column
	@CodeTable("FT-CODE.Wcode")
	@NotNull(message = "{wcode}", groups = SubmitGroup.class)
	protected String			wcode;
	@Column
	@DefaultValue(processBean = "SN-CORE.SheetInfoDefaultValue")
	@NotNull(message = "{sheetcode}")
	protected String			sheetcode;
	@Column
	@DefaultValue("10")
	@NotNull(message = "{status}")
	protected String			status;

}
```

注：
1.该VO案例因代码过多只展示部分关键内容
2.标准单据应该继承BasSheetVO,此处样例因历史原因单独封装了FTSheetVO????
2.单据VO头部需要添加@Table @ValidResource @SheetInfo注解,其中@Table注解标识VO对应的具体表结构定义,@ValidResource标识单据VO校验信息资源配置文件 @SheetInfo标识VO对应单据
3.@Column注解标识单据业务字段，对应表结构定义字段
4.@Id @DefaultValue("Accode:FT-TRD.InnerCode") 标识单据主键字段以及主键字段内码生成规则
5.@NotNull @NotEmpty标识单据字段非空 配合@ValidResource使用，灵活配置单据非空校验提示信息
6.@CodeTable 标识单据字段是字典或者码表，用于单据存盘字段数据校验
7.注意VO需要生成序列化ID，分布式环境部署对象序列化使用


##### QueryParams

1. 在API工程下创建接口对应的查询参数类继承QueryParams
1. 查询参数包名：snsoft.ft.trd.tx.prj.bas.service
1. 查询参数类名：ProjectEntryParams
1. 配置说明【[服务介绍](help.html?helpFile=help/SN-HELP/Server/Service/Service.md)】
1. 参考代码

```
@Nmpre.Nmpres({
		//审批单客户表
		@Nmpre(nmpre = "filter_se", pJoinColumn = "ft_trd_prj.prjicode", joinColumn = ProjectConstants.PRJ_INNER_NAME, tblname = ProjectConstants.PRJ_SE_TABNAME),
		//审批单供应商表
		@Nmpre(nmpre = "filter_pe", pJoinColumn = "ft_trd_prj.prjicode", joinColumn = ProjectConstants.PRJ_INNER_NAME, tblname = ProjectConstants.PRJ_PE_TABNAME),
		//销售合同商品表
		@Nmpre(nmpre = "filter_salordg", pJoinColumn = "ft_trd_prj.prjicode", joinColumn = ProjectConstants.PRJ_INNER_NAME, tblname = ProjectConstants.SALORD_G_TABNAME),
		//采购合同商品表
		@Nmpre(nmpre = "filter_purordg", pJoinColumn = "ft_trd_prj.prjicode", joinColumn = ProjectConstants.PRJ_INNER_NAME, tblname = ProjectConstants.PURORD_G_TABNAME) })
public class ProjectEntryParams extends QueryParams
{
	private static final long	serialVersionUID	= -5448215850058000035L;
	/**
	 * 合作-审批单号/外部合同号[代理+非额度]
	 */
	@SqlColumn(filterBean = FTFilterConstans.FTMixCodeFilterBean, column = "IC:prjcode,ft_trd_prj_extse.outordcode")
	private String				coagoutcode;
	/**
	 * 采购-审批单号/外部合同号[代理+非额度]
	 */
	@SqlColumn(filterBean = FTFilterConstans.FTMixCodeFilterBean, column = "IC:prjcode,ft_trd_prj_extpe.outordcode")
	private String				sepooutcode;
	/**
	 * 审批单号/额度申请单/外部合同号[额度]
	 */
	@SqlColumn(filterBean = FTFilterConstans.FTMixCodeFilterBean, column = "IC:prjcode,IC:quotaappcode,ft_trd_prj_extse.outordcode")
	private String				cscdoutcode;
	/**
	 * 项目号【审批单号】
	 */
	@SqlColumn(sqlop = SqlExpression.LIKE)
	private String				prjcode;
	/**
	 * 委托方
	 */
	@SqlColumn
	private String				ccodetrust;
	/**
	 * 公司
	 */
	@BcodeColumn(btype = "02")
	private String				corpbcode;
	/**
	 * 人员
	 */
	@SqlColumn
	private String				wcode;
	/**
	 * 部门
	 */
	@BcodeColumn
	private String				bcode;
	/**
	 * 状态
	 */
	@SqlColumn
	private String				status;
	/**
	 * 商户
	 */
	@SqlColumn
	private String				cuicode;
	@SqlColumn
	private String				sheetcode;
	@SqlColumn
	private String[]			sheetcodes;
	/**
	 * 经营方式
	 */
	@SqlColumn
	private String				busimode;
	/**
	 * 采购要素用途
	 */
	@SubColumn(pJoinColumn = "ft_trd_prj.prjicode", joinColumn = ProjectConstants.PRJ_INNER_NAME, table = ProjectConstants.PRJ_PUREXT_TABNAME, sqlColumn = @SqlColumn(column = "purpose"))
	private String				purpose;
	/**
	 *销售 客户
	 */
	//	@SubColumn(pJoinColumn = ProjectConstants.PRJ_INNER_NAME, table = ProjectConstants.PRJ_SALEXT_TABNAME)
	@SqlColumn
	private String				salccode;
	/**
	 * 采购合同号
	 */
	@SubColumn(pJoinColumn = "ft_trd_prj.prjicode", joinColumn = ProjectConstants.PRJ_INNER_NAME, table = ProjectConstants.PURORD_G_TABNAME, sqlColumn = @SqlColumn(column = "purordcode"))
	private String				purordcode;
	/**
	 * 采购合同号
	 */
	@SubColumn(pJoinColumn = "ft_trd_prj.prjicode", joinColumn = ProjectConstants.PRJ_INNER_NAME, table = ProjectConstants.SALORD_G_TABNAME, sqlColumn = @SqlColumn(column = "salordcode"))
	private String				salordcode;
	/**
	 * 综合关联合同号查询参数
	 */
	@SubColumn.SubColumns({
			@SubColumn(pJoinColumn = "ft_trd_prj.prjicode", joinColumn = ProjectConstants.PRJ_INNER_NAME, table = ProjectConstants.PURORD_G_TABNAME, sqlColumn = @SqlColumn(column = "purordcode"), grp = 1),
			@SubColumn(pJoinColumn = "ft_trd_prj.prjicode", joinColumn = ProjectConstants.PRJ_INNER_NAME, table = ProjectConstants.SALORD_G_TABNAME, sqlColumn = @SqlColumn(column = "salordcode"), grp = 0) })
	private String				ordcodes;
	/**
	 * 创建人
	 */
	@SqlColumn
	private String				vprepare;
	/**
	 * 额度申请单号
	 */
	@SqlColumn(sqlop = SqlExpression.LIKE)
	private String				quotaappcode;
	/**
	 * 单据外码集合
	 */
	@SqlColumn(sqlop = SqlExpression.LIKE, column = "prjcode,quotaappcode", ignorecase = true)
	private String				sheetoutcode;
	/**
	 * 出口审批单供应商
	 */
	@SubColumn(pJoinColumn = "ft_trd_prj.prjicode", joinColumn = ProjectConstants.PRJ_INNER_NAME, table = ProjectConstants.PRJ_PE_TABNAME, sqlColumn = @SqlColumn(column = ProjectConstants.PURCCODE_COL))
	private String				salpurccode;
	/**
	 * 内贸审批单客户-采购单据的客户
	 */
	@SubColumn(pJoinColumn = "ft_trd_prj.prjicode", joinColumn = ProjectConstants.PRJ_INNER_NAME, table = ProjectConstants.PRJ_SE_TABNAME, sqlColumn = @SqlColumn(column = ProjectConstants.SALCCODE_COL))
	private String				pursalccode;
	/**
	 * 采购辅表供应商
	 */
	@SqlColumn(column = "purccode")
	private String				extpepurccode;
	/**
	 * 创建日期
	 */
	@SqlColumn
	private Date				predate;
	/**
	 * 签约日期从
	 */
	@SqlColumn(column = "signdate", sqlop = SqlExpression.GE)
	private Date				signdatefrom;
	/**
	 * 到
	 */
	@SqlColumn(column = "signdate", sqlop = SqlExpression.LE)
	private Date				signdateto;
	/**
	 * 创建日期从
	 */
	@SqlColumn(column = "predate", sqlop = SqlExpression.GE)
	private Date				predatefrom;
	/**
	 * 到
	 */
	@SqlColumn(column = "predate", sqlop = SqlExpression.LE)
	private Date				predateto;
	/**
	 * 贸易类型
	 */
	@SqlColumn
	private String				tradetype;
	/**
	 * 贸易方式
	 */
	@SqlColumn
	private String				trademode;
	/**
	 * 购销方式
	 */
	@SqlColumn
	private String				psmode;
	/**
	 * 加工方式
	 */
	@SqlColumn
	private String				purmode;
}
```

注：

1. 查询参数对象需要继承QueryParams对象
2. @SqlColumn注解标识查询服务匹配数据库字段

##### API Service

1. 配置说明【[服务介绍](help.html?helpFile=help/SN-HELP/Server/Service/Service.md)】
1. Service包名：snsoft.ft.trd.tx.prj.sesoimp.service
1. Servic类名：SeSOImpProjectService
1. Service类位置：API 工程 src/main/java
1. Service Impl包名： snsoft.ft.trd.tx.prj.sesoimp.service.impl  
1. Servic Impl类名：SeSOImpProjectServiceImpl
1. Service Impl类位置：trd-impl 工程 src/main/java
1. 根据包名、类名、类位置分别在在API IMPL工程下新建单据API Service以及实现
   

Service参考代码

```
@SpringBean(name = SeSOImpProjectService.BeanName)
@Funccode("FT-TRD.TX.Prj.SeSOImpProject.SV")
@SheetInfo(value = SeSOImpProject.SheetCode)
public interface SeSOImpProjectService extends FTBusiAccessService<SeSOImpProject>
{
	public static String BeanName = "FT-TRD.TX.SeSOImpProjectService";

	/**
	 * 查询入口
	 *
	 * @param params
	 * @return
	 */
	QueryResults<SeSOImpProject> queryEntry(ProjectEntryParams params);

	/**
	 * 查询单据详情
	 * @param params
	 * @return
	 */
	QueryResults<SeSOImpProject> queryProject(ProjectPageNavParams params);

	/**
	 * 保存详细信息
	 * @param params
	 * @return
	 */
	SaveResults saveProject(SaveParams<SeSOImpProject> params);

	/**
	 * 查询销售要素付款方式记录
	 * @param params
	 * @return
	 */
	QueryResults<SeSOImpProjectSeSett> queryProjectSeSett(ProjectParams params);

	/**
	 * 查询销售商品记录
	 * @param params
	 * @return
	 */
	QueryResults<SeSOImpProjectSeGood> queryProjectSeGood(ProjectSeParams params);
}
```

```
public interface FTBusiAccessService<T> extends BusiAccessService<T>
{
	/**
	 * <pre>
	 * 业务提交检查接口，可以同时处理数据写操作，返回值可以带回到界面。
	 * </pre>
	 * @param innerCode
	 * @return 
	 */
	T beforeSubmit(T main);
}
```

注：
1.API Service接口头部添加 @SpringBean注解 用于暴露该API服务，供UI服务调用
2.API Service接口头部添加 @SheetInfo 用于标识单据服务
3.API Service接口需继承BusiAccessService接口
4.API Service接口头部添加 @Funccode注解 用于系统功能配置服务调用
5.标准单据API服务需提供入口查询、单据详情、单据保存、查询子表 相关服务


Service Impl参考代码

```
public class SeSOImpProjectServiceImpl extends FTBusiAccessServiceImpl<SeSOImpProject> implements SeSOImpProjectService
{
	@Override
	public QueryResults<SeSOImpProject> queryEntry(ProjectEntryParams params)
	{
		if (StrUtils.isEmpty(params.getSheetcode()))
		{
			params.setSheetcode(SeSOImpProject.SheetCode);
		}
		return super.uiQuery(params, null);
	}

	@Override
	public QueryResults<SeSOImpProject> queryProject(ProjectPageNavParams params)
	{
		return super.uiQuery(params, null);
	}

	@Override
	public SaveResults saveProject(SaveParams<SeSOImpProject> params)
	{
		return super.uiSave(params);
	}

	@Override
	public QueryResults<SeSOImpProjectSeSett> queryProjectSeSett(ProjectParams params)
	{
		return funcQuery(params, null, TrdCommUtils.getDetailClass(SeSOImpProject.SheetCode, SeSOImpProjectSeSett.class));
	}

	@Override
	public QueryResults<SeSOImpProjectSeGood> queryProjectSeGood(ProjectSeParams params)
	{
		return funcQuery(params, null, TrdCommUtils.getDetailClass(SeSOImpProject.SheetCode, SeSOImpProjectSeGood.class));
	}
}

```

```
public class FTBusiAccessServiceImpl<V extends VO> extends DefaultFunctionService<V> implements FTBusiAccessService<V>
{
	@Override
	public V beforeSubmit(V o)
	{
		return newDAO().trans(dao -> {
			V main = ObjectUtils.deepClone(o);
			if (main == null)
			{
				throw ExceptionUtils.notExists();
			}
			List<FunctionSaveListener<V>> list = getSaveListeners(getFunccode());
			List<String> debugs = new ArrayList<>();
			for (FunctionSaveListener<V> lis : list)
			{
				if (lis instanceof FTFunctionSaveListener)
				{
					long start = System.currentTimeMillis();
					((FTFunctionSaveListener<V>) lis).beforeSubmit(main);
					long gap = (System.currentTimeMillis() - start);
					if (gap > 0)
					{
						debugs.add(String.format("[%1$05d]", gap) + lis.getClass().getSimpleName());
					}
				}
			}
			if (logger.isDebugEnabled() && !debugs.isEmpty())
			{
				logger.debug(getClass().getSimpleName() + "=>beforeSubmit");
				for (String debug : debugs)
				{
					logger.debug(debug);
				}
			}
			//
			if (isChanged(main))
			{
				main.setUpdate();
				((BasVO) main).setModifier(AppContext.getUserSession(true).getUserCode());
				((BasVO) main).setModifydate(DateUtils.getServerDate());
				main.addStoredColumn(ModifydateVO.modifierColumn);
				main.addStoredColumn(ModifydateVO.modifydateColumn);
				save(main);
			}
			return main;
		});
	}
}
```

注：
1.API Service Impl 需要实现 API Service接口
2.API Service Impl 需要实现 API Service接口需要继承 DefaultFunctionService

##### UI Service

1. UI Service包名：snsoft.ft.trd.tx.prj.sesoimp.service
1. UI Servic类名：SeSOImpProjectUIService
1. UI Service类位置：UI 工程 src/main/java
1. UI Service Impl包名：snsoft.ft.trd.tx.prj.sesoimp.service.impl
1. UI Servic Impl类名：SeSOImpProjectUIServiceImpl
1. Service Impl类位置：UI 工程 src/main/java
1. 根据包名、类名、类位置在UI工程下新建单据UI Service以及实现

UI Service参考代码

```
public interface SeSOImpProjectUIService
{
	/**
	 * 查询入口
	 *
	 * @param params
	 * @return 
	 */
	@AuthParam(sheetCode = SeSOImpProject.SheetCode, opids = { LimitConst.Opid_R, LimitConst.Opid_C })
	@CuicodeFilter
	QueryResults<SeSOImpProject> queryEntryUI(ProjectEntryParams params);

	/**
	 * 查询单据详情
	 * @param params
	 * @return
	 */
	@AuthParam(sheetCode = SeSOImpProject.SheetCode, opids = { LimitConst.Opid_R, LimitConst.Opid_C })
	@CuicodeFilter
	QueryResults<SeSOImpProject> queryProjectUI(ProjectPageNavParams params);

	/**
	 * 保存详细信息
	 * @param params
	 * @return
	 */
	@AuthParam(sheetCode = SeSOImpProject.SheetCode, opids = { LimitConst.Opid_C })
	SaveResults saveProjectUI(SaveParams<SeSOImpProject> params);

	/**
	 * 查询付款方式详情
	 * @param params
	 * @return
	 */
	@AuthParam(sheetCode = SeSOImpProject.SheetCode, opids = { LimitConst.Opid_R, LimitConst.Opid_C }, joinColumn =ProjectConstants.PRJ_INNER_NAME)
	QueryResults<SeSOImpProjectSeSett> queryProjectSeSettUI(ProjectParams params);

	/**
	 * 查询销售商品
	 */
	@AuthParam(sheetCode = SeSOImpProject.SheetCode, opids = { LimitConst.Opid_R, LimitConst.Opid_C }, joinColumn = ProjectConstants.PRJ_INNER_NAME)
	QueryResults<SeSOImpProjectSeGood> queryProjectSeGoodUI(ProjectSeParams params);
}
```

注:
1.UI Service 方法、参数同API Service
1.UI Service 方法添加 @AuthParam @CuicodeFilter注解,其中@AuthParam用于单据查询、存盘相关操作调用UI服务权限校验 @CuicodeFilter注解标识服务过滤商户数据
3.UI Service 方法名 需要以API Service 相同方法+"UI"命名 (强制规范,否则会导致权限注解失效 可参考：snsoft.bas.aspect.service.SpringBeanAspect) 


UI Service Impl 参考代码

```
@Service("FT-TRD.TX.SeSOImpProjectUIService")
public class SeSOImpProjectUIServiceImpl implements SeSOImpProjectUIService
{
	@Resource(name = SeSOImpProjectService.BeanName)
	protected SeSOImpProjectService service;

	@Override
	public QueryResults<SeSOImpProject> queryEntryUI(ProjectEntryParams params)
	{
		return service.queryEntry(params);
	}

	@Override
	public QueryResults<SeSOImpProject> queryProjectUI(ProjectPageNavParams params)
	{
		return service.queryProject(params);
	}

	@Override
	public SaveResults saveProjectUI(SaveParams<SeSOImpProject> params)
	{
		return service.saveProject(params);
	}

	@Override
	public QueryResults<SeSOImpProjectSeSett> queryProjectSeSettUI(ProjectParams params)
	{
		return service.queryProjectSeSett(params);
	}

	@Override
	public QueryResults<SeSOImpProjectSeGood> queryProjectSeGoodUI(ProjectSeParams params)
	{
		return service.queryProjectSeGood(params);
	}
}

```

注:
1.UI Service Impl 需添加@Service 注册Bean服务


##### XJS监听

1. 单据部分业务需要实现客户端相关功能，此时需要新建XJS监听，挂载到对应界面UI系统功能上
1. XJS工程新建Cli监听 位置： XJS工程 src/main/java 根据具体业务划分包名、类名
1. 配置Xjs文件：build/J2SLibBuild(*).Args 位置：XJS工程 build目录
1. 配置说明【[Java2Js](help.html?helpFile=help/SN-HELP/Environment/Java2Js/Java2Js.md)】
1. XJS监听配置

```
/*#
 lib=snsoft/ft/trd/comm.js
#*/
package snsoft.ft.trd.comm.busi;

import js.JSObject;
import snsoft.ft.code.gcode.comm.SelectGcodeDialog;
import snsoft.ft.trd.comm.util.TrdCommUtils;
import snsoft.plat.bas.busi.SystemFunctionListener;
import xjs.dx.DataSet;
import xjs.table.GridTable;
import xjs.table.Table;
import xjs.table.TableColumn;
import xjs.table.TableEvent;
import xjs.ui.InputField;
/**
 * <p>标题：选择商品按钮动态构建对话框JS监听[依赖编码列要配置码表]</p>
 * <p>功能：</p>
 * <p>
 * 其他说明：
 * </p>
 * <p>作者：FZZ</p>
 * <p>审核：</p>
 * <p>重构：</p>
 * <p>创建日期：2019年11月15日下午1:49:49</p>
 * @see 
 */
public class TrdBusiSelectGcodeBtnListener extends SystemFunctionListener
{
	private final String	GCODE_COL;
	/**
	 * 是否设置列表也
	 */
	private final boolean	isSetMultiRow;

	public TrdBusiSelectGcodeBtnListener(JSObject<Object> params)
	{
		super(params);
		this.GCODE_COL = $orAsString(params.$get("gcodecol"), "gcode");
		this.isSetMultiRow = $orAsBool(params.$get("isSetMultiRow"), true);
	}

	/**
	 * 	枷锁，区分操作
	 */
	boolean lock = false;

	/**
	 *    选择商品
	 */
	void oncmd_getGoods(Table table)
	{
		lock = true;
		//进行主表的检查
		Table rTable = table.getRootTable();
		DataSet dataSet = rTable.dataSet;
		dataSet.saveChanges();
		if (dataSet.isNewRow() && !$bool(dataSet.getValue(dataSet.getCuInnerfld())))
		{
			rTable.checkCurRowDataNonBlank(dataSet);
		}
		int indexOfColumn = table.indexOfColumn(GCODE_COL);
		if (indexOfColumn < 0)
		{
			return;
		}
		//先跑到最后一行去插入
		table.dataSet.postRow();
		table.dataSet.insertRow(3);
		table.setSelectedColumn(indexOfColumn, true);
		TableColumn gcodeColumn = table.getColumn(GCODE_COL);
		if ($bool(gcodeColumn))
		{
			InputField component = (InputField) gcodeColumn.getEditComponent(table);
			component.forTblColumn = (xjs.ui.Cell) ((Object) gcodeColumn);
			component.doAidInput();
		}
	}

	@Override
	public void itemAidInputing(Table table, TableEvent e)
	{
		super.itemAidInputing(table, e);
		if ($ieq(GCODE_COL, e.forTblColumn.name) && this.isSetMultiRow)
		{
			SelectGcodeDialog aid = (SelectGcodeDialog) e.forTblColumn.aidInputer;
			aid.$set("_multiRow", lock && true);
			aid.addListener("onShowing", this.bindAsFunctionCall((js.IFunction_V1<SelectGcodeDialog>) this::_onShowing, new Object[] {}));
			if (lock)
			{
				//按钮弹框取按钮名称
				aid.setTitle(getAidTitle(table, null, "getGoods", true));
				aid.selOptions |= 4;
				lock = false;
			} else
			{
				//列的辅助录入要求使用列标题
				aid.setTitle(getAidTitle(table, e.forTblColumn.name, null, false));
				aid.selOptions = aid.selOptions & ~4;
			}
		}
	}

	/**
	 * 按钮标题存放集合
	 */
	JSObject<String> titleCach = $o();

	/**
	 * 获取名称
	 * @param table
	 * @param col
	 * @param btn
	 * @param isBtn
	 */
	protected String getAidTitle(Table table, String col, String btn, boolean isBtn)
	{
		if (isBtn)
		{
			String btntitle = titleCach.$get(btn);
			if ($bool(btntitle))
			{
				return btntitle;
			}
			btntitle = TrdCommUtils.getToolbarBtnText(table, btn);
			titleCach.$set(btn, btntitle);
			return btntitle;
		}
		String title = titleCach.$get(col);
		if ($bool(title))
		{
			return title;
		}
		TableColumn column = table.getColumn(col);
		if (!$bool(column))
		{
			return null;
		}
		titleCach.$set(col, column.title);
		return column.title;
	}

	/**
	 * 
	 * @param diag
	 * @param flag
	 */
	void _onShowing(SelectGcodeDialog diag)
	{
		Table table = diag.table;
		boolean flag = diag.$getAsBool("_multiRow");
		table.rowMutiSelectable = flag;
		((GridTable) table).setLeftFixedColWidth(flag ? 30 : 0);
		table.render(1 + 2 + 256);
	}
}

```

注：

1.XJS监听代码具体根据模块、功能划分代码位置
2.XJS监听需要在具体界面对应的UI系统功能上挂载
3.XJS监听生效需要使用JAVA2JS工具编译生成具体js代码才生效

#### 单据定义

1. 在UI程下创建 src/main/resources/cfg/res/sheet/FT-TRD.TX.Prj.SeSOImpProject.xml
1. 配置说明【[单据定义](help.html?helpFile=help/SN-CMC/Busheet/Busheet.md)】
1. 参考配置

```
<?xml version="1.0" encoding="UTF-8"?>
<Sheet sheetflags="4"  xmlns="http://www.snsoft.com.cn/schema/Sheet" xsi:schemaLocation="http://www.snsoft.com.cn/schema/Sheet http://www.snsoft.com.cn/schema/Sheet.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<name>${}</name>
	<tblname>ft_trd_prj</tblname>
	<innerfld>prjicode</innerfld>
	<outerfld>prjcode</outerfld>
	<voclass>snsoft.ft.trd.tx.prj.sesoimp.vo.SeSOImpProject</voclass>
	<acsbean>FT-TRD.TX.SeSOImpProjectService</acsbean>
	<remark><![CDATA[
	自营进口敞口销售合同审批单
	1、负责人：付壮志
	2、启用单据编码规则；
	3、启用单据权限；
		权限字段：	
			astn：关系类型
			bcode：部门
			wcode：人员
			corpbcode：公司
			vprepare：创建人
		操作编码：
			R：查询
			C：操作
	4、启用审批链；
	5、启用版本备份；
	]]></remark>
</Sheet> 

```

注：
1.单据配置文件名称同单据号
2.Sheet节点下name节点定义 单据名称配置${},配合资源文件ResBundleFT-TRD-COMM_zh_CN.inf中配置 title_FT-TRD.TX.Prj.SeSOImpProject=自营进口敞口销售合同审批单
3.voclass节点配置上一步骤中VO的具体包名、类名
4.acsbean配置上一步中API Servcie头部注解BeanID
5.remark 需要具体描述单据相关信息

#### 单据状态定义

1. 在UI工程下创建src/main/resources/cfg/res/status/FT-TRD.TX.Prj.SePMImpProject.json
2. 参考配置

```
[
	{
		"code": "10",
		"name": "草拟"
	},
	{
		"code": "11",
		"name": "修改"
	},
	{
		"code": "15",
		"name": "驳回"
	},
	{
		"code": "30",
		"name": "待审"
	},
	{
		"code": "69",
		"name": "待生效"
    },
	{
		"code": "70",
		"name": "生效"
	},
    {
        "code": "75",
        "name": "终结"
    },
	{
		"code": "90",
		"name": "作废"
	}
]

```

注：
1.配置文件名称命名规范 单据号.json

#### 编码规则定义

1. 在UI工程下创建src/main/resources/cfg/res/accode/单据号.xml
1. 配置说明【[编码规则](help.html?helpFile=help/SN-CMC/Accode/Accode.md)】
1. 参考配置

```
<?xml version="1.0" encoding="UTF-8"?>
<Codelike xmlns="http://www.snsoft.com.cn/schema/Accode" xsi:schemaLocation="http://www.snsoft.com.cn/schema/Accode http://www.snsoft.com.cn/schema/Accode.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <name>${}</name>
    <remark><![CDATA[
    ]]></remark>
    <codeinfo><![CDATA[
    proc  getCodeLike(refData)
        return inc(5)
    end proc
    
    proc getExtFilter(refData)
        if startCuicode()
            userSession = snsoft.context.AppContext.getUserSession(true)
            cuicode = userSession.getUserCuicode()
            filter = eq("cuicode",cuicode)
        end if
        return filter
    end proc
    ]]></codeinfo>
</Codelike>
```

注：
1.此业务单据编号手动录入，不需要单据编码规则，故选取其他业务示例
2.单据编码规则包含内码和外码编码规则，需要在单据VO具体字段头部添加@DefaultValue配合使用
 内码：@DefaultValue("Accode:FT-TRD.InnerCode") 
 外码：@DefaultValue("SheetAccode:@SheetInfo")

#### 单据/编码规则关系

1. 在UI工程下创建src/main/resources/cfg/res/sheetaccode/单据号.xml
1. 参考配置

```
[
	{
		"accode": "FT-TRD.AO.Cost",
		"colname": "costicode",
		"refcolumns": "cuicode"
	}
]
```

注:
1.此业务单据编号手动录入，不需要单据编码规则，故选取其他业务示例
2.次功能适用于多个单据号引用相同编码规则定义,如不存在此情况不需要使用该配置


#### 权限定义

1. 在UI工程下创建src/main/resources/cfg/res/limit/FT-TRD.TX.Prj.SeSOImpProject.xml
1. 配置说明【[权限定义](help.html?helpFile=help/SN-CMC/Limit/Datalimit.md)】
1. 参考配置

```
<?xml version="1.0" encoding="UTF-8"?>
<datalimdef limnm="${}" xmlns="http://www.snsoft.com.cn/schema/Datalimdef" xsi:schemaLocation="http://www.snsoft.com.cn/schema/Datalimdef http://www.snsoft.com.cn/schema/Datalimdef.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <datalimop opid="R" opnm="查询" />
    <datalimop opid="C" opnm="操作" />
    <datalimflds fldname="astn" fldtitle="关系类型" fldtype="12" fldumatch="#SN-PLAT.BWcodeFieldMatcher"
                 codedata="#SN-PLAT.RBWUType" options1="32" tcprops="{editable:false}"/>
    <datalimflds fldname="sheetcode" fldtitle="单据类型" fldtype="12"
		tcprops="{nonBlank:true,defaultValue:'FT-TRD.TX.Prj.SeSOImpProject',visible:false}" />
    <datalimflds fldname="bcode" fldtitle="部门" fldtype="12" btype="01" fldopts="1" />
    <datalimflds fldname="wcode" fldtitle="人员" fldtype="12" />
    <datalimflds fldname="corpbcode" fldtitle="公司" fldtype="12" btype="02" fldopts="1" />
    <datalimflds fldname="vprepare" fldtitle="创建人" fldtype="12" />
</datalimdef>

```

注：
1.权限定义字段必须是表结构定义字段
2.权限定义码R：查询,C：操作 配合UI服务权限注解以界面属性控制配合使用

#### 单据权限关系

1. 在UI工程下创建src/main/resources/cfg/res/sheetlim/XX.json
1. 参考配置

```
[
    {
        "sheetcode": "FT-TRD.TX.Prj.SePMImpProject",
        "limid": "FT-TRD.TX.Prj.SePOImpProject"
    },
    {
        "sheetcode": "FT-TRD.TX.Prj.SePMImpProject",
        "limid": "FT-TRD.TX.Prj.SePSImpProject"
    }
]

```

注：
1.配置文件命名一般按照系统号命名，系统中所有配置合并在一起
2.如果单据权限定义存在多个单据引用，可以使用此关联关系，减少重复定义

#### 单据默认值

1. 在UI工程下创建src/main/resources/cfg/res/defaultvalue/FT-TRD.TX.Prj.SeSOImpProject.xml
1. 配置说明【[单据默认值](help.html?helpFile=help/SN-CMC/Service/VO/VOListener.md#默认值)】
1. 参考配置

```
<?xml version="1.0" encoding="UTF-8"?>
<SheetDefaultValueDef xmlns="http://www.snsoft.com.cn/schema/SheetDefaultValueDef" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetDefaultValueDef http://www.snsoft.com.cn/schema/SheetDefaultValueDef.xsd">
    <Remark>进口国际运保费发票</Remark>
    <Defaults tblname="ft_trd_purfeeinv" colname="invoicer">
        <DefaultValue>10</DefaultValue>
        <Remark>发票抬头</Remark>
    </Defaults>
    <Defaults tblname="ft_trd_purfeeinv" colname="fcode">
        <DefaultValue>CNY</DefaultValue>
        <Remark>CNY</Remark>
    </Defaults>
</SheetDefaultValueDef>
```

注：
1.此功能同VO注解 @DefaultValue("1") 类似，主要解决非开发环境单据默认值调整
2.允许在不修改VO Class、不重启服务情况下修改单据默认值
3.需结合实际业务判断是否使用此功能

#### 单据校验

1. 在UI工程下创建src/main/resources/cfg/res/validate/FT-TRD.TX.Prj.SeSOImpProject.xml
1. 配置说明【[单据校验](help.html?helpFile=help/SN-CMC/Service/VO/VOListener.md#单据数据校验)】
1. 参考配置

```
<?xml version="1.0" encoding="UTF-8"?>
<SheetValidationDef xmlns="http://www.snsoft.com.cn/schema/SheetValidationDef" xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetValidationDef http://www.snsoft.com.cn/schema/SheetValidationDef.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<Remark>付款初始化校验规则</Remark>
    <Validations tblname="ft_set_pag" colname="padate">
        <ValidRule>[{type:"NotNull","groups":["SubmitGroup"]}]</ValidRule>
        <Remark>实际支付日期</Remark>
    </Validations>
    <Validations tblname="ft_set_pag" colname="fserate">
        <ValidRule>[{type:"NotNull","groups":["SubmitGroup"]}]</ValidRule>
        <Remark>本位币汇率</Remark>
    </Validations>
</SheetValidationDef>
```

注：
1.此功能同VO注解 @NotNull @NotEmpty 类似，主要解决非开发环境单据校验调整
2.允许在不修改VO Class、不重启服务情况下修改单据校验规则
3.需结合实际业务判断是否使用此功能

#### 系统功能

1. 配置说明【[系统功能](help.html?helpFile=help/SN-PLAT/Tools/SystemFunction.md)】

##### 入口系统功能

1. 在UI工程下创建src/main/resources/cfg/res/sysfunc/FT-TRD.TX.Prj.SeSOImpProject.Entry.UI.xml
1. 参考配置
```
<?xml version="1.0" encoding="UTF-8"?>
<SystemFunctions xmlns="http://www.snsoft.com.cn/schema/plat-sysfunc" xsi:schemaLocation="http://www.snsoft.com.cn/schema/plat-sysfunc http://www.snsoft.com.cn/schema/plat-sysfunc.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<Name>自营进口敞口销售合同审批单入口UI</Name>
	<Remark><![CDATA[
	]]></Remark>
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl>SN-PLAT.PlatEntryListener?[{sheetCode:'FT-TRD.TX.Prj.SeSOImpProject'}]</Funcimpl>
            <Remark>入口</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.plat.bas.busi.PlatEntryListener({})</Funcimpl>
            <Remark>入口</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
    #new snsoft.plat.bas.opt.ClsOptPopupMenuJSListener({sheetcode:"FT-TRD.TX.Prj.SeSOImpProject",tgtUINames:["ft_trd_prj"]})
    ]]></Funcimpl>
            <Remark>选项配置右键菜单监听</Remark>
        </Functions>
        
           <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
			#new snsoft.ft.trd.comm.busi.TrdBusiUnionSheetEntryCopyListener({sheetCode:"FT-TRD.TX.Prj.SeSOImpProject"})
			]]></Funcimpl>
            <Remark>入口JS监听：单据复制</Remark>
        </Functions>
        
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl><![CDATA[
             FT-TRD.COMM.BusiSheetEntryUIListener?[{sheetCodes:'FT-TRD.TX.Prj.SeSOImpProject',limitSheetCode:'FT-TRD.TX.Prj.SeSOImpProject'}]
             ]]></Funcimpl><Remark>动态创建入口按钮</Remark>
        </Functions>
         <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
			#new snsoft.ft.trd.comm.busiclassify.BusiSheetEntryListener({sheetCode:"FT-TRD.TX.Prj.SeSOImpProject",toolbarId:"proToolbar"})
			]]></Funcimpl>
            <Remark>入口JS监听：动态创建入口按钮</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.plat.bas.busi.ShowRefreshPanelListener({itemName:'status2'})</Funcimpl>
            <Remark></Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.code.wf.FTApprSubmitJSListener({"tgtUINames":["ft_trd_prj"]})</Funcimpl>
            <Remark>审批链-打开审批详情</Remark>
        </Functions>
    	<Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl><![CDATA[
            SN-UI.SysOptionUIListener?[{'type':2,'time':1,'optids':'UserCustom.EntryTableYLine'}]
            ]]></Funcimpl>
            <Remark>获取用户定制Grid表格竖线选项</Remark>
        </Functions>
    	<Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.plat.bas.comm.UserCustomGridLineJSListener({})</Funcimpl>
            <Remark>用户定制Grid表格竖线控制</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.plat.bas.comm.PlatEntryAutoPageJSListener({"tgtUINames":["ft_trd_prj"]})</Funcimpl>
            <Remark>用户定制Grid表页面设置</Remark>
        </Functions>
     <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl>SN-UI.UIOptCtrlListener</Funcimpl>
            <Remark>界面属性控制</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl>FT-CODE.SheetBcodeDefaultValueUIListener?[{onlyRemoteable:true}]</Funcimpl>
            <Remark>取核算组系统选项</Remark>
        </Functions>
         <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>
            <![CDATA[
            #new snsoft.ft.trd.comm.query.TrdCostBcodeColQueryCtrlListener({})
             ]]>
            </Funcimpl>
            <Remark>核算组控制</Remark>
        </Functions>
</SystemFunctions>
```

注：
1.命名规范 [单据号].Entry.UI.xml
2.系统功能分为UI系统功能和SV系统功能,UI系统功能用于界面监听挂载,SV系统功能用于服务调用挂载

##### 详情系统功能

详情页系统功能包含 UI和SV 2套配置
1. 在UI工程下创建src/main/resources/cfg/res/sysfunc/FT-TRD.TX.Prj.SeSOImpProject.SV.xml
1. 在UI工程下创建src/main/resources/cfg/res/sysfunc/FT-TRD.TX.Prj.SeSOImpProject.UI.xml
1. UI系统功能配置
```
<?xml version="1.0" encoding="UTF-8"?>
<SystemFunctions xmlns="http://www.snsoft.com.cn/schema/plat-sysfunc" xsi:schemaLocation="http://www.snsoft.com.cn/schema/plat-sysfunc http://www.snsoft.com.cn/schema/plat-sysfunc.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Name>自营进口敞口销售合同审批单单详情UI</Name>
    <Remark><![CDATA[
	]]></Remark>
    <!--<Groups id="版本比较">-->
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
		#new snsoft.plat.bas.sheet.vnbk.CompareVersionJSListener({mUiName:'ft_trd_prj',ignoCmpTbls:'ft_trd_termdetail,ft_trd_termfill,ft_trd_pft,apprdata,unionorders',hasVsnFlagCell:{},ignoCmpCell:{'busielemg_340':'gattr00,gattr01,gattr02,gattr03,gattr04,gattr05,gattr06,gattr07,gattr08,gattr09,gattr10,gattr11,gattr12,gattr13,gattr14,gattr15,gattr16,gattr17,gattr18,gattr19'}})
		]]></Funcimpl>
            <Remark>版本比较客户端监听</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl><![CDATA[
		SN-PLAT.CompareVersionUIListener?[{}]
		]]></Funcimpl>
            <Remark>版本比较UI监听</Remark>
        </Functions>
    <!--</Groups>-->
    <!--<Groups id="版本备份">-->
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.plat.bas.sheet.vnbk.VersionBackupListener({sheetCode:'FT-TRD.TX.Prj.SeSOImpProject'})</Funcimpl>
            <Remark>版本备份</Remark>
        </Functions>
    <!--</Groups>-->
    <!--<Groups id="界面相关">-->
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl>SN-UI.VOListener?[{'sheetCode':'FT-TRD.TX.Prj.SeSOImpProject'}]</Funcimpl>
            <Remark>默认值</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl>
                <![CDATA[
                FT-TRD.COMM.BusiSheetDetailUIListener?[{sheetCode:'FT-TRD.TX.Prj.SeSOImpProject'}]
                 ]]></Funcimpl>
            <Remark>单据配置加载</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
            #new snsoft.ft.trd.comm.busiclassify.BusiSheetDetailListener({tgtUINames:["ft_trd_prj"],gTableName:"busielemg_340"})
            ]]></Funcimpl>
            <Remark>JS监听：动态创建子表生成按钮</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl><![CDATA[
            SN-UI.SheetListener?[{'sheetCode':'FT-TRD.TX.Prj.SeSOImpProject'}]
            ]]></Funcimpl>
            <Remark>单据监听：界面控制</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl><![CDATA[
            SN-UI.UIOptCtrlListener?[{'sheetCode':'FT-TRD.TX.Prj.SeSOImpProject','stdUICtrl':true}]
            ]]></Funcimpl>
            <Remark>界面属性控制</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.plat.sheet.chk.ApprCheckErrListener({})</Funcimpl>
            <Remark>错误件查监听</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.plat.bas.busi.PageTitleBarJSListener({})</Funcimpl>
            <Remark>页面标题条JS监听</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
    #new snsoft.plat.bas.opt.ClsOptPopupMenuJSListener({sheetcode:"FT-TRD.TX.Prj.SeSOImpProject",tgtUINames:["ft_trd_prj","ft_trd_prj_bas"]})
    ]]></Funcimpl>
            <Remark>选项配置右键菜单监听</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
                      #new snsoft.plat.bas.busi.PageNavPaneJSListener({sheetcode:"FT-TRD.TX.Prj.SeSOImpProject",tgtUINames:["ft_trd_prj"]})
                     ]]></Funcimpl>
            <Remark>多选界面处理监听</Remark>
        </Functions>

        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.sheet.comm.OperateJSListener({tgtUINames:["ft_trd_prj_se_sett"],'cmds':['add','delete']})
            </Funcimpl>
            <Remark><![CDATA[
            		界面操作列监听JS
            ]]></Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.plat.bas.comm.CleanValueJSListener({tgtUINames:["ft_trd_prj"],cleanIfNull:false,postInfos:[{uiname:"ft_trd_prj",colnames:["bcode","wcode"],infos:[{colnames:["salccode","salncode","salsncitycode","sallinkmanname","sallinkmanmobile"]}]}]})</Funcimpl>
            <Remark>
                属性控制值改变清空值
            </Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
			#new snsoft.plat.bas.sheet.copy.SheetCopyListener({tgtUINames:["ft_trd_prj"],sheetCode:"FT-TRD.TX.Prj.SeSOImpProject"})
			]]></Funcimpl>
            <Remark>入口JS监听：单据复制</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.trd.comm.busi.TrdBusiSelectGcodeBtnListener({tgtUINames:["busielemg_340"]})</Funcimpl>
            <Remark><![CDATA[
           		 增加选择商品按钮
            ]]></Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.trd.comm.bm.BatchModifyForPricTypeJSListener({tgtUINames:["busielemg_340"],cellNames:"cnamedesc,brand,specifidesc,goodsdesc,qtc,qtcunit,upric,natupric,deliverydate"})</Funcimpl>
            <Remark><![CDATA[
            ]]></Remark>
        </Functions>

        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.trd.tx.prj.bas.ProjectSEGoodsOperateListener({"tgtUINames":["busielemg_340"],"cmds":['add','edit','copy','delete'],"cellCols":4,"cellWidth":120,"labelAlign":"right","opt":2,"uncopyCols":"hisflag,deliverydate","tableCtrlListeners":["snsoft.ft.code.gcode.comm.FTXGattrBuildJSListener({srcTblName:'busielemg_340',buildTblname:'edit_busielemg_340_sharedialog'}).new","snsoft.ft.trd.tx.prj.bas.ProjectShareDialogCommCtrlListener({'remCtrlField':['editable'],'isOnlyByCstype':true,'changeItemCols':'gcode'}).new","snsoft.ft.trd.comm.changesheetctrl.VBChangeSheetCtrlOperateListener({}).new","snsoft.ft.trd.comm.unit.UnitRateConvertCliListener({'flags':6}).new"]})</Funcimpl>
            <Remark>共享对话框监听</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.trd.tx.prj.bas.ProjectSEGCommButtonListener({"tgtUINames":["busielemg_340"]})</Funcimpl>
            <Remark>销售要素子表按钮监听</Remark>
        </Functions>

        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.trd.comm.unionorder.TrdUnionOrderListener({"tgtUINames":["unionorders"]})</Funcimpl>
            <Remark><![CDATA[
            1）关联合同处理
            ]]></Remark>
        </Functions>
        
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.trd.comm.busi.TrdBusiSetRelatedColsValueListener({"tgtUINames":["ft_trd_prj_bas"],"sourCaseCols":"overshiprate,shortshiprate",relaCols:[{sourCol:'overshiprate',tgtCol:'shortshiprate'},{sourCol:'shortshiprate',tgtCol:'overshiprate'}]})</Funcimpl>
            <Remark><![CDATA[
            	值改变控制
            ]]></Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.trd.comm.busi.TrdBusiCommBusiBtnsListener({"tgtUINames":["ft_trd_prj"]})</Funcimpl>
            <Remark><![CDATA[
				审批单公共按钮统一处理监听
			]]></Remark>
        </Functions>
    <!--</Groups>-->

    <!--<Groups id="客户端计算公式">-->
        
         <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new  snsoft.ft.trd.comm.formula.TrdQtcQtyUnitListener({tgtUINames:["busielemg_340"]})</Funcimpl>
            <Remark><![CDATA[
            计算比率
            ]]></Remark>
        </Functions>

          <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new  snsoft.ft.trd.comm.formula.TrdDomSalFcyNatAddByPrictypeListener({tgtUINames:["busielemg_340"],isCalcAddtax:true})</Funcimpl>
            <Remark><![CDATA[
            根据定价方式计算含税金额、不含税金额、增值税额
            ]]></Remark>
        </Functions>
    <!--</Groups>-->
    <!--<Groups id="审批">-->
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.code.wf.FTApprSubmitJSListener({tgtUINames:["ft_trd_prj"],"taskTblName":"apprdata"})</Funcimpl>
            <Remark>审批链-提交</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.wf.TaskPerformListener({tgtUINames:["ft_trd_prj"]})</Funcimpl>
            <Remark>审批链-提交</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl>
             SN-APPR.UITaskPerformListener?[{}]
            </Funcimpl>
            <Remark>审批链-审批</Remark>
        </Functions>
    <!--</Groups>-->
    <!--<Groups id="条款">-->
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
            snsoft.ft.trd.comm.term.ui.SheetTermUIListener.new?sheetCode=FT-TRD.TX.Prj.SeSOImpProject&tgtUINames=ft_trd_prj&termCfg=ft_trd_prj_extse:ft_trd_termdetail,ft_trd_termfill
             ]]></Funcimpl>
            <Remark><![CDATA[
            
            ]]></Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
            #new snsoft.ft.trd.comm.term.SheetTermJSListener({sheetHistoryTermUI:"FT-TRD.TX.Prj.ProjectCommSheetTermExtSeDialog",ccodeColname:"salccode",termTableName:"ft_trd_prj_extse", termTableUIName:"ft_trd_prj"})
            ]]></Funcimpl>
            <Remark><![CDATA[
            ]]></Remark>
        </Functions>
    <!--</Groups>-->
    <!--<Groups id="业务相关">-->
		<Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.trd.comm.busi.TrdBusiCheckAidsNotNullListener({"tgtUINames":["ft_trd_prj_bas","busielemg_340"],"aidCaseCols":"salccode",checkAids:[{tgtMuiName:'ft_trd_prj_bas',aidCol:'salccode',checkCol:'ft_trd_prj.wcode'}]})</Funcimpl>
            <Remark><![CDATA[
            	界面辅助录入限制
            ]]></Remark>
        </Functions> 
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.trd.comm.busi.TrdBusiCheckAidsNotNullListener({"tgtUINames":["ft_trd_prj_bas","busielemg_340"],"aidCaseCols":"sallinkmanname",checkAids:[{tgtMuiName:'ft_trd_prj_bas',aidCol:'sallinkmanname',checkCol:'ft_trd_prj.salccode'}]})</Funcimpl>
            <Remark><![CDATA[
                界面辅助录入限制
            ]]></Remark>
        </Functions> 
    <!--</Groups>-->
    <!--<Groups id="单据文档">-->
        <!--UI监听sheetCodes：配置单据号，统一界面多个单据附件时对应配置多个单据号，','分割-->
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl>
                <![CDATA[
     SN-PLAT.SheetDocSaveUIListener?[{'tgtUINames':'ft_trd_prj','sheetCodes':'FT-TRD.TX.Prj.SeSOImpProject'}]
     ]]>
            </Funcimpl>
            <Remark>单据文档存盘监听</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.plat.bas.sheet.doc.DocTypeResBtnJSListener({tgtUINames:["prtdoctype"],sheetCode:'FT-TRD.TX.Prj.SeSOImpProject'})</Funcimpl>
            <Remark>单据文档类型JS监听</Remark>
        </Functions>

        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.plat.bas.sheet.doc.SheetDocBtnJSListener({tgtUINames:["prtplat_sheetdoc"],sheetCode:'FT-TRD.TX.Prj.SeSOImpProject',aidColumn:'docpath',isOlViewImg:false,autoSave:false})
            </Funcimpl>
            <Remark>单据文档附件JS监听</Remark>
        </Functions>
    <!--</Groups>-->

    <!--<Groups id="组织默认值处理">-->
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl>
                <![CDATA[
        FT-CODE.FTCorpInfoDefalutUIListener?[{'tgtUINames':'ft_trd_prj','sheetcode':'FT-TRD.TX.Prj.SeSOImpProject','hasCostCol':true,'defaultCols':'sfcode:sfcode,fcode:sfcode'}]
        ]]>
            </Funcimpl>
            <Remark>部门、人员、公司、核算组默认值处理</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>
                <![CDATA[
        #new snsoft.ft.code.comm.SheetCorpBcodeJSListener({"tgtUINames":["ft_trd_prj"]})
        ]]>
            </Funcimpl>
            <Remark>部门值改变公司值处理</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>
                <![CDATA[
        #new snsoft.ft.code.comm.SheetCostBcodeJSListener({"tgtUINames":["ft_trd_prj"]})
        ]]>
            </Funcimpl>
            <Remark>部门值改变核算组值处理</Remark>
        </Functions>
    <!--</Groups>-->
    <!--<Groups   id="单据编码规则相关">-->
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl><![CDATA[
        SN-PLAT.SheetAccodeUIListener?[{'tgtUINames':'ft_trd_prj','sheetCode':'FT-TRD.TX.Prj.SeSOImpProject','colname':'prjcode'}]
    ]]></Funcimpl>
            <Remark>单据编码规则初始化</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.plat.bas.busi.SheetAccodeJSListener({"tgtUINames":["ft_trd_prj"],colname:'prjcode'})</Funcimpl>
            <Remark>单据编码规则逻辑处理</Remark>
        </Functions>
    <!--</Groups>-->

    <!--<Groups id="级联查询">-->
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl>
              SN-PLAT.CasLoadUIListener?[{'loadUINames':'ft_trd_prj_se_sett'}]
            </Funcimpl>
            <Remark>UI监听</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.plat.bas.calc.CasLoadJSListener({})</Funcimpl>
            <Remark>JS监听</Remark>
        </Functions>
    <!--</Groups>-->

    <!--<Groups id="盈亏测算">-->
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl><![CDATA[
              FT-TRD.COMM.BusiSheetProfitUIListener?[{'tgtUINames':'ft_trd_prj,ft_trd_pft'}]
            ]]></Funcimpl>
            <Remark>UI监听</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.trd.comm.profit.BusiSheetProfitCliListener({profitTblName:'ft_trd_prj_pft'})</Funcimpl>
            <Remark>JS监听</Remark>
        </Functions>
    <!--</Groups>-->
    <!--<Groups id="收付款">-->
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl><![CDATA[
               FT-TRD.COMM.TrdPayModeCtrlUIListener?[{'tgtUINames':'ft_trd_prj_se_sett','domabr':'10'}]
            ]]></Funcimpl>
            <Remark>UI监听</Remark>
        </Functions>   
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.trd.comm.ctrl.TrdPayModeCtrlCliListener({"tgtUINames":["ft_trd_prj_se_sett"],"domabr":"10"})</Funcimpl>
            <Remark>JS监听</Remark>
        </Functions>
    <!--</Groups>-->
    <!--<Groups id="单位换算比例">-->
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.trd.comm.unit.UnitRateConvertCliListener({"tgtUINames":["busielemg_340"],"busimode":"10","flags":7})</Funcimpl>
            <Remark>单位换算比例</Remark>
        </Functions>
         <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl><![CDATA[
                FT-TRD.COMM.UnitRateConvertUIListener?[{'tgtUINames':'busielemg_340'}]
            ]]></Funcimpl>
            <Remark>单位换算比例UI监听</Remark>
        </Functions>
    <!--</Groups>-->
    <!--<Groups id="录入控制">-->
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.trd.comm.ctrl.TrdDeliveryCtrlListener({"tgtUINames":["ft_trd_prj_bas"],gTblName:"busielemg_340"})</Funcimpl>
            <Remark>合同交期方式</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.trd.comm.ctrl.TrdPortInputCtrlListener({"tgtUINames":["ft_trd_prj_bas"],gTblName:"busielemg_340"})</Funcimpl>
            <Remark>港口录入方式</Remark>
        </Functions>
    <!--</Groups>-->
    <!--<Groups id="作废相关">-->
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.plat.bas.busi.StatusJSListener({"tgtUINames":["ft_trd_prj"]})</Funcimpl>
            <Remark>作废数据处理</Remark>
        </Functions>
    <!--</Groups>-->

    <!--<Groups id="穿透监听">-->
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.plat.bas.viewdetail.ViewDetailListener({"tgtUINames":["busielemg_340","ft_trd_prj_bas"]})</Funcimpl>
            <Remark>穿透处理</Remark>
        </Functions>
    <!--</Groups>-->
    <!--<Groups id="项目卷宗">-->
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>1</Subtype>
            <Funcimpl>
                FT-CODE.BusiNavTreeOpenUIListener?[{sheetCode:"FT-TRD.TX.Prj.SeSOImpProject",tgtUINames:"ft_trd_prj"}]
            </Funcimpl>
            <Remark>业务卷宗-ui</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.code.busitree.cli.BusiNavTreeOpenJSListener({"tgtUINames":["ft_trd_prj"]})</Funcimpl>
            <Remark>业务卷宗-js</Remark>
        </Functions>
    <!--</Groups>-->
      <!--<Groups id="Excel导入">-->
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.bastool.excel.ExcelStrategyImportListener({tgtUINames:["busielemg_340"],xlscode:'FT-TRD.Ft_trd_prj_simp_so_seg',strategyEnables:'7',runParam:{"prjicode":"T#ft_trd_prj#prjicode","Cuicode":"T#ft_trd_prj#cuicode","cuicode":"T#ft_trd_prj#cuicode"}})
            </Funcimpl>
            <Remark>Excel导入[配置]：根据Gcode进行匹配</Remark>
        </Functions>
    <!--</Groups>-->
    
     <!--<Groups id="商品属性动态控制">-->
		<Functions> 
	      <Functype>SN-PLAT.UI</Functype>  
	      <Subtype>1</Subtype>  
	      <Funcimpl><![CDATA[
	         SN-PLAT.QueryToolUIListener?[{'tgtUINames':'busielemg_340','qrycode':'FT-TRD.Gcode.GcodeColumnQR'}]
	        ]]></Funcimpl>  
	      <Remark>商品属性补数(货号属性组、货号属性、启用库存辅助单位标识)</Remark> 
	    </Functions> 
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
        #new snsoft.ft.code.gcode.comm.FTXGattrBuildJSListener({tgtUINames:["busielemg_340"],buildTblname:'busielemg_340'})
        ]]></Funcimpl>
            <Remark>商品申报要素控制</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
         snsoft.ft.code.gcode.comm.FTXGattrBuildUIListener.new?tgtUINames=busielemg_340&mainUIName=ft_trd_prj&sheetCode=FT-TRD.TX.Ord.SeSOImpDomSalOrd&activeCols=sgtrgrpcode,gtrgrpcode&loadOnDataLoaded=true&sgtrgrpFld=sgtrgrpcode
        ]]></Funcimpl>
            <Remark>商品申报要素控制</Remark>
        </Functions>
    <!--</Groups>-->
</SystemFunctions>
``` 
1. SV系统功能配置

```
<?xml version="1.0" encoding="UTF-8"?>
<SystemFunctions xmlns="http://www.snsoft.com.cn/schema/plat-sysfunc" xsi:schemaLocation="http://www.snsoft.com.cn/schema/plat-sysfunc http://www.snsoft.com.cn/schema/plat-sysfunc.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<Name>自营进口敞口销售合同审批单存盘功能</Name>
	<Remark><![CDATA[
	]]></Remark>
	  <!--<Groups id="单据存盘值替换">-->
        <Functions>
            <Functype>SN-PLAT.SV</Functype>
            <Subtype>1</Subtype>
            <Funcimpl><![CDATA[
           SN-PLAT.PlatRVSVListener
            ]]></Funcimpl>
            <Remark>单据存盘值替换SV监听</Remark>
        </Functions>
    <!--</Groups>-->
<!--<Groups id="单据相关">-->
    <Functions>
        <Functype>SN-PLAT.SV</Functype>
        <Subtype>1</Subtype>
        <Funcimpl><![CDATA[
        SN-PLAT.SubmitCheckSVListener
        ]]></Funcimpl>
        <Remark>提交检查</Remark>
    </Functions>
    <Functions>
        <Functype>SN-PLAT.SV</Functype>
        <Subtype>1</Subtype>
        <Funcimpl><![CDATA[
        FT-CODE.CcodeSheetControlSubmitSVListener
        ]]></Funcimpl>
        <Remark>单据启用客商权限控制</Remark>
    </Functions>
    
    <Functions>
        <Functype>SN-PLAT.SV</Functype>
        <Subtype>1</Subtype>
        <Funcimpl><![CDATA[
        SN-PLAT.VersionBackupFunctionListener
        ]]></Funcimpl>
        <Remark>版本备份</Remark>
    </Functions>
	<Functions>
        <Functype>SN-PLAT.SV</Functype>
        <Subtype>1</Subtype>
        <Funcimpl><![CDATA[
      SN-PLAT.SheetDocSaveSVListener
        ]]></Funcimpl>
        <Remark>单据附件：附件上传</Remark>
    </Functions>
    <Functions>
        <Functype>SN-PLAT.SV</Functype>
        <Subtype>1</Subtype>
        <Funcimpl><![CDATA[
        SN-PLAT.SheetDocWfSVListener?[{'attachCfg':'ft_trd_prj:FT-TRD.TX.Prj.SeSOImpProject#ft_trd_prj_doc'}]
        ]]></Funcimpl>
        <Remark>单据文档提交检查</Remark>
    </Functions>
         <Functions>
        <Functype>SN-PLAT.SV</Functype>
        <Subtype>1</Subtype>
        <Funcimpl><![CDATA[
        SN-PLAT.SaveCheckSVListener?[{'incasinfo':'ft_trd_prj:bcfcode,scmcode,busielemcodelist,extcstype/ft_trd_prj_doc:*,+,-'}]
        ]]></Funcimpl>
        <Remark>存盘检查</Remark>
    </Functions>
    
    <Functions>
    <Functype>SN-PLAT.SV</Functype>
    <Subtype>1</Subtype>
    <Funcimpl><![CDATA[
    SN-PLAT.SheetAccodeSVListener?[{'sheetCode':'FT-TRD.TX.Prj.SeSOImpProject','colname':'prjcode'}]
    ]]></Funcimpl>
    <Remark>单据编码规则校验</Remark>
</Functions>
    <!--</Groups>-->
    <!--<Groups id="条款相关">-->
    <Functions>
        <Functype>SN-PLAT.SV</Functype>
        <Subtype>1</Subtype>
        <Funcimpl><![CDATA[
        FT-TRD.COMM.SheetTermSVListener
        ]]></Funcimpl>
        <Remark>条款</Remark>
    </Functions>
    <!--</Groups>-->
        <!--<Groups id="生效时间">-->
        <Functions>
            <Functype>SN-PLAT.SV</Functype>
            <Subtype>1</Subtype>
            <Funcimpl>FT-TRD.COMM.VersionStatusTimeListener</Funcimpl>
            <Remark>生效时间赋值</Remark>
        </Functions>
        <!--</Groups>-->
        
    <!--<Groups id="组织关系生效效验">-->
        <Functions>
            <Functype>SN-PLAT.SV</Functype>
            <Subtype>1</Subtype>
            <Funcimpl>
                <![CDATA[
       FT-CODE.SheetBcodeCheckSVListener?[{'hasCostCol':true}]
        ]]>
            </Funcimpl>
            <Remark>组织关系生效效验</Remark>
        </Functions>
    <!--</Groups>-->
	<!--<Groups id="盈亏测算">-->
		<Functions>
			<Functype>SN-PLAT.SV</Functype>
			<Subtype>1</Subtype>
			<Funcimpl><![CDATA[
			FT-TRD.COMM.BusiSheetProfitSVListener?[{'sheetCode':'FT-TRD.TX.Prj.SeSOImpProject','profitTblName':'ft_trd_prj_pft','frateDateName':'ft_trd_prj.predate','fcodeName':'ft_trd_prj_extse.fcode','sfcodeName':'ft_trd_prj_extse.sfcode','fserateName':'ft_trd_prj_extse.fserate','fcerateName':'ft_trd_prj_extse.fcerate','fuerateName':'ft_trd_prj_extse.fuerate'}]
			]]></Funcimpl>
			<Remark>盈亏测算</Remark>
		</Functions>
	<!--</Groups>-->
	
    <!--<Groups id="计算公式">-->
		<Functions>
			<Functype>SN-PLAT.SV</Functype>
			<Subtype>2</Subtype>
			<Funcimpl><![CDATA[
			snsoft.ft.code.bas.frate.service.impl.FrateOperationSVListener.new?odateColumn=predate.ft_trd_prj&fcodeColumn=fcode.ft_trd_prj_extse&rateColumn=fserate.ft_trd_prj_extse,fcerate.ft_trd_prj_extse,fuerate.ft_trd_prj_extse&tgfcodeColumn=sfcode.ft_trd_prj_extse,@CNY,@USD&corpbcodeColumn=corpbcode.ft_trd_prj
            ]]></Funcimpl>
			<Remark>主表汇率计算</Remark>
		</Functions> 	 
        
        <Functions>
            <Functype>SN-PLAT.SV</Functype>
            <Subtype>1</Subtype>
            <Funcimpl><![CDATA[
           FT-TRD.TX.SeSOImpProjectBusiFormulaListener?[{}]
            ]]></Funcimpl>
            <Remark><![CDATA[
            贸易计算公式
            ]]></Remark>
        </Functions>
        
		  <Functions>
            <Functype>SN-PLAT.SV</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
            snsoft.ft.trd.comm.formula.TrdUpricQtcCheckListener.new?tblname=ft_trd_prj_seg
            ]]></Funcimpl>
            <Remark><![CDATA[
            贸易单价数量含税金额以含税金额为准验证监听SV
            ]]></Remark>
        </Functions>
        
        <Functions>
            <Functype>SN-PLAT.SV</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
            snsoft.ft.trd.comm.formula.TrdDomSalFcyNatAddByPrictypeCheckListener.new?tblname=ft_trd_prj_seg
            ]]></Funcimpl>
            <Remark><![CDATA[
             验证含税金额、不含税金额、增值税额
            ]]></Remark>
        </Functions>
	<!--</Groups>-->
    
    <!--<Groups id="盈亏测算">-->
        <Functions>
            <Functype>SN-PLAT.SV</Functype>
            <Subtype>1</Subtype>
            <Funcimpl><![CDATA[
            FT-TRD.COMM.BusiSheetProfitSVListener?[{'priority':110,'sheetCode':'FT-TRD.TX.Prj.SeSOImpProject','profitTblName':'ft_trd_prj_pft','frateDateName':'ft_trd_prj.predate','fcodeName':'ft_trd_prj_extse.fcode','sfcodeName':'ft_trd_prj_extse.sfcode','fserateName':'ft_trd_prj_extse.fserate','fcerateName':'ft_trd_prj_extse.fcerate','fuerateName':'ft_trd_prj_extse.fuerate'}]
            ]]></Funcimpl>
            <Remark>盈亏测算</Remark>
        </Functions>
    <!--</Groups>-->
   
	<!--<Groups id="业务单据服务">-->
		<Functions>
			<Functype>SN-PLAT.SV</Functype>
			<Subtype>1</Subtype>
			<Funcimpl><![CDATA[
				FT-TRD.COMM.BusiSheetSVListener?[{}]
			]]></Funcimpl>
			<Remark>业务单据服务</Remark>
		</Functions>
	<!--</Groups>-->
	<!--<Groups id="收汇方式">-->
		<Functions>
			<Functype>SN-PLAT.SV</Functype>
			<Subtype>1</Subtype>
			<Funcimpl><![CDATA[
			FT-TRD.COMM.TrdPayModeSVListener?[{'sortCol':'idx','rpTblName':'ft_trd_prj_se_sett','mstTblName':'ft_trd_prj_extse','isCheck':false}]
			]]></Funcimpl>
			<Remark>收汇校验</Remark>
		</Functions>
	<!--</Groups>-->
	<!--<Groups id="合同交期方式">-->
		<Functions>
			<Functype>SN-PLAT.SV</Functype>
			<Subtype>1</Subtype>
			<Funcimpl><![CDATA[
			FT-TRD.COMM.TrdDeliveryCtrlSVListener?[{'mTblName':'ft_trd_prj_extse','gTblName':'ft_trd_prj_seg'}]
			]]></Funcimpl>
			<Remark>合同交期方式</Remark>
		</Functions>
	<!--</Groups>-->
	<!--<Groups id="港口录入方式">-->
		<Functions>
			<Functype>SN-PLAT.SV</Functype>
			<Subtype>1</Subtype>
			<Funcimpl><![CDATA[
			FT-TRD.COMM.TrdPortInputCtrlSVListener?[{'mTblName':'ft_trd_prj_extse','gTblName':'ft_trd_prj_seg'}]
			]]></Funcimpl>
			<Remark>港口录入方式</Remark>
		</Functions>
	<!--</Groups>-->
	<!--<Groups id="单据外码提交检查">-->
		<Functions>
			<Functype>SN-PLAT.SV</Functype>
			<Subtype>1</Subtype>
			<Funcimpl><![CDATA[
			FT-COMM.FTOutCodeCheckSVListener?[{}]
			]]></Funcimpl>
			<Remark>单据外码提交检查</Remark>
		</Functions>
	<!--</Groups>-->
	
<!--<Groups id="提交检查">--> 
		<Functions>
			<Functype>SN-PLAT.SV</Functype>
			<Subtype>1</Subtype>
			<Funcimpl><![CDATA[
			FT-TRD.COMM.FTBusiElemsCheckListener?[{}]
			]]></Funcimpl>
			<Remark>业务要素检查</Remark>
		</Functions>
<!--</Groups>-->
	<!--<Groups id="作废-取消作废">-->
		<Functions>
			<Functype>SN-PLAT.SV</Functype>
			<Subtype>1</Subtype>
			<Funcimpl><![CDATA[
			FT-TRD.TX.ProjectStatusStopSVListener?[{}]
			]]></Funcimpl>
			<Remark>作废-取消作废功能特殊处理</Remark>
		</Functions>
	<!--</Groups>-->
	<!--<Groups id="撤单备份审批信息">-->
		<Functions>
			<Functype>SN-PLAT.SV</Functype>
			<Subtype>1</Subtype>
			<Funcimpl>SN-APPR.ApprDataBackupSVListener</Funcimpl>
			<Remark>撤单备份审批信息</Remark>
		</Functions>
	<!--</Groups>-->
	
	 <!--<Groups id="商品属性检查處理">-->
		<Functions>
		<Functype>SN-PLAT.SV</Functype>
		<Subtype>2</Subtype>
			<Funcimpl><![CDATA[
				snsoft.ft.code.gcode.comm.service.impl.FTXGattrSubmitCheckSVListener.new?checkOpt=2
		]]></Funcimpl>
		<Remark>商品属性检查處理</Remark>
	</Functions>
    <!--</Groups>-->
    <!--<Groups id="改单检查">-->
        <Functions>
            <Functype>SN-PLAT.SV</Functype>
            <Subtype>1</Subtype>
            <Funcimpl>FT-TRD.COMM.BusiChangeSheetCtrlSaveListener</Funcimpl>
            <Remark>改单保存检查</Remark>
        </Functions>
    <!--</Groups>-->
</SystemFunctions>

```

注:
1.API Service头部需要添加SV系统功能号
2.界面定义上需挂载监听，参数为UI系统功能号
    例：snsoft.plat.bas.busi.PlatFunctionListener.new?funccode=FT-TRD.TX.Prj.SeSOImpProject.UI;

#### 界面定义

1. 在UI工程下src/main/resources/cfg/ui/res/ 创建界面定义
1. 配置说明【[界面定义](help.html?helpFile=help/SN-CMC/UI/UI.md)】

##### 入口界面定义

1. 在UI工程下src/main/resources/cfg/ui/res/FT-TRD/TX/Prj/SeSOImpProjectEntry.xml
1. 参考配置

```
<?xml version="1.0" encoding="UTF-8"?>
<B title="${RES.FT-TRD.title_FT-TRD.TX.Prj.SeSOImpProjectEntry?自营进口敞口销售合同审批单-入口}" fullPage="true"
   xmlns="http://www.snsoft.com.cn/schema/UI"
   xmlns:m="http://www.snsoft.com.cn/schema/UI"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://www.snsoft.com.cn/schema/UI http://www.snsoft.com.cn/schema/UI.xsd">
	<m:DialogPane name="query" title="查询面板" region="north" uiprops.backInitValues="true" xprops.showRefreshPane="true" xprops.showFoldPaneBtn="true" xprops.showAdvancedQueryBtn="true" xprops.btnsLayoutMode="3" uiprops.fixhByContent="1">
		<uilisteners>
			<![CDATA[
				snsoft.ft.code.bcode.bcode.FTUcodeWcodeInitValueListener.new;
                snsoft.ft.comm.busi.FTBusiFilterDateDefaultUIListener.new?dateColumns=predatefrom
				]]>
		</uilisteners>
		<P name="p1" layoutm="grid" cellcols="4" uiprops.className="basic-query-con" clayoutwids="${RES.$.E.Q.4CW}" uiprops.subCompOpts="2">
			<c name="coagoutcode" sqltype="12" suffixMatch="true" prefixMatch="true" title="" uiprops.bgLabel="${RES.$.ft_trd_prj.query_sheetoutcode2}" uiprops.cellClassName="td-input-align" nmpre="filter" />
			<c name="prjcode" sqltype="12" suffixMatch="true" prefixMatch="true" title="${RES.$.prjcode}" toUpper="true" titleLeft="true" hidden="true" nmpre="filter" />
			<c name="salccode" sqltype="12" disableed="true" codedata="#FT-CODE.CcodeLMExt" showname="true" aidInputerBtn="true" title="${RES.$.ft_trd_prj_extse.salccode}" aiprops="initParasVales:{ccodetypes:'12',ccodearea:'10',status:''}" nmpre="filter" />
			<c name="bcode" title="${RES.$.bcode}" sqltype="12" codedata="#FT-CODE.BWcode" cmparams="status:'~10'" titleLeft="true" cmparams.sheetcode="FT-TRD.TX.Prj.SeSOImpProject" cmparams.opids="R,C" disableed="true" showname="true" selectMiddle="true" aidInputerBtn="true" nmpre="filter" />
			<c name="wcode" title="${RES.$.wcode}" sqltype="12" codedata="#FT-CODE.Wcode" cmparams="status:'~10'" disableed="true" showname="true" aidInputerBtn="true" cmparams.sheetcode="FT-TRD.TX.Prj.SeSOImpProject" cmparams.opids="R,C" nmpre="filter" />
			<c name="busimode" title="${RES.$.busimode}" sqltype="12" aidInputerBtn="true" cmparams="JSONFILTER_FIX:null" codedata="#DT_FT-CODE.Busimode" showname="true" disableed="true" hidden="true" nmpre="filter" />
			<c name="corpbcode" title="${RES.$.corpbcode}" sqltype="12" codedata="#FT-CODE.CorpBcode" cmparams="status:'~10'" cmparams.sheetcode="FT-TRD.TX.Prj.SeSOImpProject" cmparams.opids="R,C" disableed="true" showname="true" selectMiddle="true" aidInputerBtn="true" nmpre="filter" />
			<c name="vprepare" sqltype="12" title="${RES.$.vprepare}" codedata="#FT-CODE.Users" cmparams="status:'~10'" aidInputerBtn="true" disableed="true" cmparams.sheetcode="FT-TRD.TX.Prj.SeSOImpProject" cmparams.opids="R,C" showname="true" nmpre="filter" />
			<c name="signdatefrom" title="${RES.$.ft_trd_prj.signdatefrom}" sqltype="91" aidInputerBtn="true" nmpre="filter" />
			<c name="predatefrom" title="${RES.$.predatefm}" sqltype="91" aidInputerBtn="true" nmpre="filter" />
			<c name="signdateto" title="${RES.$.dateto}" sqltype="91" dateDelayToNextDay="true" aidInputerBtn="true" hidden="true" nmpre="filter" />
			<c name="cuicode" title="${RES.$.cuicode}" sqltype="12" hidden="true" nmpre="filter" />
			<c name="status" hidden="true" title="${RES.$.status}" sqltype="12" codedata="#90.status" cmparams="sheetcode:'FT-TRD.TX.Prj.SeSOImpProject'" droplist="true" aidInputerBtn="true" droplistWithSearchPane="true" disableed="true" aiprops="promptInfo:'',cellTextFmt:['${code}-${name}']" listSBoxOnTop="true" nmpre="filter" />
			<c hidden="true" name="status2" sqlexpr="status" title="${RES.$.status}：" sqltype="12" codedata="#90.status" cmparams="sheetcode:'FT-TRD.TX.Prj.SeSOImpProject'" cliInitAllCodes="true" nmpre="filter" />
		</P>
		<P name="p2" uiprops.className="advanced-query-con">
			<P name="more0" layoutm="grid" cellcols="4" clayoutwids="${RES.$.E.Q.4CW}" uiprops.subCompOpts="2">
				<c name="salordcode" title="${RES.$.salordcode}" sqltype="12" toUpper="true" startLine="true" prefixMatch="true" suffixMatch="true"  nmpre="filter_salordg"  />
				<c name="busimode" title="${RES.$.busimode?经营方式}" sqltype="12" cmparams="JSONFILTER_FIX:null" codedata="#DT_FT-CODE.Busimode" showname="true" aidInputerBtn="true" disableed="true" nmpre="filter" />
				<c name="psmode" title="${RES.$.psmode}" sqltype="12" aidInputerBtn="true" cmparams="JSONFILTER_FIX:null" codedata="#DT_FT-CODE.PsMode" disableed="true" showname="true" nmpre="filter" />
				<c name="trademode" title="${RES.$.trademode}" sqltype="12" aidInputerBtn="true" cmparams="JSONFILTER_FIX:null" codedata="#DT_FT-CODE.TradeMode" disableed="true" showname="true" nmpre="filter" />
				<c name="tradetype" title="${RES.$.tradetype}" sqltype="12" aidInputerBtn="true" cmparams="JSONFILTER_FIX:null" codedata="#DT_FT-CODE.TradeType" showname="true" hidden="true" disableed="true" nmpre="filter" />
				<c name="purmode" title="${RES.$.purmode}" sqltype="12" aidInputerBtn="true" cmparams="JSONFILTER_FIX:null" cliInitAllCodes="true" codedata="#DT_FT-CODE.PurMode" showname="true" disableed="true" nmpre="filter" />
			</P>
			<QParamsEditor1 name="more1" title="高级查询" titleHidden="true" uiprops.cellColCount="4" uiprops.fixVisFldCount="4" uiprops.cellWidths="${RES.$.E.Q.3CW.M}" uiprops.subCompOpts="2" uiprops.hideDelIcon="true" uiprops.layoutInCopyFm="more0">
				<c name="prjcode" sqltype="12" title="${RES.$.prjcode}"  toUpper="true"  nmpre="filter" />
                <c name="outordcode" sqltype="12" title="${RES.$.ft_trd_prj_extse.outordcode}"  sqlexpr="ft_trd_prj_extse.outordcode"  nmpre="filter"/>
				<invoke method="snsoft.plat.bas.tools.ui.XMLUICompLoaderTools.addQParamEditorExComps" cpfrom="p1,more0" exclude="coagoutcode,signdatefrom,predatefrom,signdateto"/>
				<c name="signdate_ex" sqlexpr="signdate" title="${RES.$.ft_trd_prj_se.signdate}" sqltype="91" aidInputerBtn="true" opComp="true" nmpre="filter" />
				<c name="predate_ex" sqlexpr="predate" title="${RES.$.preday}" sqltype="91" aidInputerBtn="true" opComp="true" dateDelayToNextDay="true" nmpre="filter" />
			</QParamsEditor1>
		</P>
	</m:DialogPane>
	<m:Toolbar name="proToolbar" region="north" uiprops.cellClassName="toolbar-panel">
		<ToolbarBtn name="ft_trd_prj_btn_openSheet" title="${RES.$.title.F.btn.opensheet}" noClientComponent="true"/>
		<ToolbarBtn name="ft_trd_prj_btn_sheetCopy" title="${RES.$.title.F.btn.sheetcopy}" noClientComponent="true"/>
		<ToolbarBtn name="ft_trd_prj_btn_queryappr" title="${RES.$.title.F.btn.queryappr}" noClientComponent="true"/>
	</m:Toolbar>
	<m:GridTable mainui="query" name="ft_trd_prj"  nmpre="filter,filter_salordg"   region="center" sqlexpr="ft_trd_prj" mutiple="true" cascadeflds="ft_trd_prj_extse" title="${RES.$.title_FT-TRD.TX.SeSOImpProject}" rdonly="true" noCollapseQPaneAfterRefresh="true"
				 uiprops.markSeledRow="1" dsprops.pageRows="10" autoRefresh="true" disableSave="true" xprops.LoadDataService="FT-TRD.TX.SeSOImpProjectUIService#queryEntryUI">
		<uilisteners>
			snsoft.plat.bas.busi.PlatFunctionListener.new?funccode=FT-TRD.TX.Prj.SeSOImpProject.Entry.UI
		</uilisteners>
		<jslistener>#new snsoft.plat.bas.busi.PlatFunctionListener({})</jslistener>
		<attrs>
			<attr type="203" name="openSheet" title="${RES.$.title.F.btn.opensheet}" _rlog="true"/>
			<attr type="203" name="sheetCopy" title="${RES.$.title.F.btn.sheetcopy}" _rlog="true"/>
			<attr type="203" name="queryappr" title="${RES.$.title.F.btn.queryappr}" _rlog="true"/>
		</attrs>
		<c name="prjcode" sqltype="12" title="${RES.$.prjcode}" width="${RES.$.E.G.CW.outcode}"/>
		<c name="sheetcode" sqltype="12" title="${RES.C}" hidden="true" width="${RES.$.E.G.CW}"/>
		<c name="status" sqltype="12" tipIfOverflow="true" title="${RES.$.status}" fixedLeft="true" codedata="#90.status" cmparams="sheetcode:'FT-TRD.TX.Prj.SeSOImpProject'" showname="true" width="${RES.$.E.G.CW.status}" uiprops.renderer="new snsoft.plat.bas.comm.render.StatusCellRender({})"/>
		<c name="wfcode" title="${RES.$.wfcode}" sqltype="12" hidden="true" width="${RES.$.E.G.CW.status}"/>
		<c name="wfuid" title="${RES.$.wfuid}" sqltype="12" hidden="true" width="${RES.$.E.G.CW.status}" codedata="#SN-APPR.wfunit" showname="true" tipIfOverflow="true" xprops.CodeData.KeyNames="wfcode"/>
		<c name="signdate" title="${RES.C}" sqltype="91" width="${RES.$.E.G.CW.date}" sqlexpr="ft_trd_prj_extse.signdate" cascadeflds="signdate" align="center"/>
		<c name="salccode" sqltype="12" tipIfOverflow="true" sqlexpr="ft_trd_prj_extse.salccode" cascadeflds="salccode" title="${RES.C}" disableed="true" codedata="#FT-CODE.Ccode" showname="true" aidInputerBtn="true" width="${RES.$.E.G.CW.ccode}"/>
		<c name="fcode" sqltype="12" title="${RES.$.fcode}" disableed="true" width="${RES.$.E.G.CW.fcode}" align="center" sqlexpr="ft_trd_prj_extse.fcode" cascadeflds="fcode"/>
		<c name="fcy" sqltype="2" title="${RES.$.ft_trd_prj_extse.fcyD}" width="${RES.$.E.G.CW.fcy}" sqlexpr="ft_trd_prj_extse.fcy" cascadeflds="fcy" mindeci="2"/>
		<c name="outordcode" title="${RES.C}" sqlexpr="ft_trd_prj_extse.outordcode" cascadeflds="outordcode" sqltype="12" tipIfOverflow="true" width="${RES.$.E.G.CW.outcode}"/>
		<c name="busimode" title="${RES.$.busimode}" sqltype="12" width="${RES.$.E.G.CW.busimode}" aidInputerBtn="true" codedata="#DT_FT-CODE.Busimode" showname="true" disableed="true"/>
		<c name="psmode" title="${RES.$.psmode}" sqltype="12" width="${RES.$.E.G.CW.psmode}" tipIfOverflow="true" aidInputerBtn="true" codedata="#DT_FT-CODE.PsMode" disableed="true" showname="true"/>
		<c name="trademode" title="${RES.$.trademode}" sqltype="12" width="${RES.$.E.G.CW.trademode}" aidInputerBtn="true" codedata="#DT_FT-CODE.TradeMode" disableed="true" showname="true"/>
		<c name="tradetype" title="${RES.$.tradetype}" sqltype="12" width="${RES.$.E.G.CW.busimode}" codedata="#DT_FT-CODE.TradeType" showname="true" tipIfOverflow="true" hidden="true"/>
		<c name="purmode" title="${RES.$.purmode}" sqltype="12" width="${RES.$.E.G.CW.busimode}" codedata="#DT_FT-CODE.PurMode" showname="true" tipIfOverflow="true"/>
		<c name="wcode" sqltype="12" title="${RES.$.wcode}" codedata="#FT-CODE.Wcode" showname="true" width="${RES.$.E.G.CW.wcode}" tipIfOverflow="true"/>
		<c name="bcode" sqltype="12" title="${RES.$.bcode}" codedata="#FT-CODE.Bcode" showname="true" tipIfOverflow="true" width="${RES.$.E.G.CW.bcode}"/>
		<c name="costbcode" title="${RES.$.costbcode}" width="${RES.FT-CODE.D.HR.CW.costbcode}" disableed="true" aidInputerBtn="true" codedata="#FT-CODE.CostBcode" showname="true" cmparams.sheetcode="FT-TRD.TX.Ord.SeSOExpSalOrd" cmparams.opids="C"
		   cmprops.pmFromPane="{fromBcode:'bcode',fromWcode:'wcode'}" cmparams.fromBtype="&quot;01&quot;" sqltype="12" hidden="true" tipIfOverflow="true"/>
		<c name="corpbcode" title="${RES.$.corpbcode}" sqltype="12" width="${RES.$.E.G.CW.bcode}" aidInputerBtn="true" codedata="#FT-CODE.CorpBcode" cmparams.sheetcode="FT-TRD.TX.Prj.SeSOImpProject" cmparams.fromBtype="&quot;01&quot;" cmparams.opids="R,C" showname="true" tipIfOverflow="true"/>
		<c name="vprepare" title="${RES.$.vprepare}" sqltype="12" width="${RES.$.E.G.CW.ucode}" codedata="#FT-CODE.Users" showname="true" tipIfOverflow="true"/>
		<c name="predate" title="${RES.$.predate}" sqltype="91" width="${RES.$.E.G.CW.time}" descOrd="true"/>
		<c name="scmcode" title="${RES.$.scmcode}" sqltype="12" width="${RES.$.E.G.CW}" hidden="true"/>
		<c name="nstype" title="${RES.$.nstype}" sqltype="12" hidden="true" width="${RES.$.E.G.CW}"/>
		<c name="cuicode" title="${RES.$.cuicode}" sqltype="12" hidden="true"/>
		<c name="prjicode" sqltype="12" title="${RES.C}" width="${RES.$.E.G.CW}" hidden="true" sqlexpr="ft_trd_prj.prjicode" descOrd="true"/>
	</m:GridTable>
</B>
```

注:

1.xprops.LoadDataService属性调用UI服务入口查询方法
2.需添加PlatFunctionListener 系统功能号：入口系统功能UI
3.界面字段、标题需要抽取出资源文件，实际用法可参考**多语言资源**

##### 详情定义

1. 在UI工程下src/main/resources/cfg/ui/res/FT-TRD/TX/Prj/SeSOImpProjectDetail.xml
1. 参考配置

```
<m:PageBody title="${RES.$.title_FT-TRD.TX.Prj.SeSOImpProjectDetail?自营进口敞口销售合同审批单}"  name="pagebody" xprops.hasFixToolbar="true" xprops.showPageTitle="true"  xprops.PageClsName="ui-page-scryvis"
	xmlns="http://www.snsoft.com.cn/schema/UI" 
	xmlns:m="http://www.snsoft.com.cn/schema/UI" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/UI http://www.snsoft.com.cn/schema/UI.xsd">
    <m:DialogPane name="param" title="参数"   hidden="true" region="north" layoutm="grid">
        <c name="prjicode" title="内码"    sqltype="12" noblank="true" />
        <c name="vsn" title="版本" sqltype="4" />
        <c name="oldvsn" title="历史版本号" sqltype="4" />
    </m:DialogPane>
    <m:Toolbar name="ft_trd_prj_toolbar"      title="工具条"    uiprops.cellClassName="head-toolbar-panel">
        <ToolbarBtn name="ft_trd_prj_btn_save"      noClientComponent="true" title="${RES.$.title.F.btn.save}"    uiprops.cellClassName="ui-head-btn" xprops.iconClassName="icons-btn-saves" />
        <ToolbarBtn name="ft_trd_prj_btn_trefresh"    noClientComponent="true" title="${RES.$.title.F.btn.trefresh}"        uiprops.cellClassName="ui-head-btn" xprops.iconClassName="icons-btn-refresh" />
        <ToolbarBtn name="ft_trd_prj_btn_submitappr"  noClientComponent="true" title="${RES.$.title.F.btn.submitappr}"     uiprops.cellClassName="ui-head-btn" xprops.iconClassName="icons-btn-submit" />
        <ToolbarBtn name="ft_trd_prj_btn_backsubmit"  noClientComponent="true" title="${RES.$.title.F.btn.backsubmit}"   uiprops.cellClassName="ui-head-btn"  xprops.iconClassName="icons-btn-recall" />
        <ToolbarBtn name="ft_trd_prj_btn_ratify"      noClientComponent="true"  title="${RES.$.title.F.btn.ratify}"  uiprops.cellClassName="ui-head-btn"        xprops.iconClassName="icons-btn-approve" />
        <ToolbarBtn name="ft_trd_prj_btn_checkError"  noClientComponent="true" title="${RES.$.title.F.btn.checkerror}"       uiprops.cellClassName="ui-head-btn" xprops.iconClassName="icons-btn-checkerror" />
        <ToolbarBtn name="ft_trd_prj_btn_delete"      noClientComponent="true" title="${RES.$.title.F.btn.delete}"     uiprops.cellClassName="ui-head-btn" xprops.iconClassName="icons-btn-delete" />
        <ToolbarBtn name="ft_trd_prj_btn_vbBackup"    noClientComponent="true" title="${RES.$.title.F.btn.vbbackup}"  uiprops.cellClassName="ui-head-btn"        xprops.iconClassName="icons-btn-version" />
        <ToolbarBtn name="ft_trd_prj_btn_vbCancel"    noClientComponent="true" title="${RES.$.title.F.btn.vbcancel}"      uiprops.cellClassName="ui-head-btn" xprops.iconClassName="icons-btn-version" /> 
   		<ToolbarBtn name="ft_trd_prj_btn_sheetCopy"   noClientComponent="true" title="${RES.$.title.F.btn.sheetcopy}"      uiprops.cellClassName="ui-head-btn" xprops.iconClassName="icons-btn-copy" />
   		<ToolbarBtn name="ft_trd_prj_btn_statusStop"   noClientComponent="true"  title="${RES.$.title.F.btn.invalid}"       uiprops.cellClassName="ui-head-btn" xprops.iconClassName="icons-btn-stop" />
   		<ToolbarBtn name="ft_trd_prj_btn_statusCancelStop"   noClientComponent="true"   title="${RES.$.title.F.btn.cancelinvalid}"      uiprops.cellClassName="ui-head-btn" xprops.iconClassName="icons-btn-nstop" />
		<ToolbarBtnGroup name="relquery"   noClientComponent="true" title="${RES.$.title.F.btn.relquery}" uiprops.className="ui-btn-group head" xprops.dftBtnCls="ui-head-btn" xprops.dftBtnIconCls="icons-btn-query"  >
			<ToolbarBtn name="ft_trd_prj_btn_openBusiNavTree"   noClientComponent="true" title="${RES.$.title.F.btn.openbusinavtree}" uiprops.cellClassName="ui-head-btn" />
			<ToolbarBtn name="ft_trd_prj_btn_openPrjBusiQuery" noClientComponent="true" title="${RES.$.title.F.btn.busifundsquery}" uiprops.cellClassName="ui-head-btn"/>
		      <ToolbarBtn name="ft_trd_prj_btn_openPftcQuery" noClientComponent="true" title="${RES.$.title.F.btn.openPftcQuery}" uiprops.cellClassName="ui-head-btn"/>
        </ToolbarBtnGroup>
	</m:Toolbar>
    <m:RecordTable title="公共信息"   layoutOnClient="true"   name="ft_trd_prj" sqlexpr="ft_trd_prj"   cascadeflds="ft_trd_prj_extse"  mainui="param"     skipRdOnlyCellOnEnterKey="true" region="center" layoutm="grid"  dsprops.forceSaveColumns="['bcfcode','scmcode','busielemcodelist']"
        align="center" uiprops.resetKeyFilterOnRefresh="2" autoRefresh="true"  cellcols="5"  xprops.LoadDataService="FT-TRD.TX.SeSOImpProjectUIService#queryProjectUI"   xprops.SaveDataService="FT-TRD.TX.SeSOImpProjectUIService#saveProjectUI" >
         <uilisteners><![CDATA[
                snsoft.plat.bas.busi.PlatFunctionListener.new?funccode=FT-TRD.TX.Prj.SeSOImpProject.UI;
                ]]></uilisteners>
        <jslistener><![CDATA[
                #new snsoft.plat.bas.busi.PlatFunctionListener({});
            ]]></jslistener> 
        <attrs>
            <attr type="203" name="submitappr"  title="${RES.$.title.F.btn.submitappr}" _rlog="true" />
            <attr type="203" name="checkError"  title="${RES.$.title.F.btn.checkerror}" _rlog="true" />
            <attr type="203" name="vbBackup" title="${RES.$.title.F.btn.vbbackup}" _rlog="true" />
            <attr type="203" name="vbCancel" title="${RES.$.title.F.btn.vbcancel}" _rlog="true" />
            <attr type="203" name="ratify" title="${RES.$.title.F.btn.ratify}" _rlog="true" />
            <attr type="203" name="backsubmit" title="${RES.$.title.F.btn.backsubmit}"  _rlog="true" />
            <attr type="203" name="sheetCopy" title="${RES.$.title.F.btn.sheetcopy}"  _rlog="true" />
            <attr type="203" name="statusStop"  title="${RES.$.title.F.btn.invalid}"  _rlog="true" />
            <attr type="203" name="statusCancelStop"  title="${RES.$.title.F.btn.cancelinvalid}" _rlog="true" />
			<attr type="203" name="openBusiNavTree"  title="${RES.$.title.F.btn.openbusinavtree}" _rlog="true" />
			<attr type="203" name="openPrjBusiQuery" title="${RES.$.title.F.btn.busifundsquery}" _rlog="true"/>
             <attr type="203" name="openPftcQuery" title="${RES.$.title.F.btn.openPftcQuery?预算实际对比查询}" _rlog="true"/>
        </attrs>
        <c name="prjicode" title="${RES.$.prjicode}"  sqltype="12" hidden="true"  sqlexpr="ft_trd_prj.prjicode" cascadeflds="prjicode"/>
        <c name="prjcode" title="${RES.$.prjcode}" sqltype="12" width="${RES.$.D.HR.CW.outcode}" rdonly="true"  noblankOnSubmit="true" toUpper="true" />
        <c name="cuicode" title="${RES.$.cuicode}"     sqltype="12" hidden="true" />
        <c name="status" tipIfOverflow="true" title="${RES.$.status}"   sqltype="12" width="${RES.$.D.HR.CW.status}" rdonly="true" codedata="#90.status" showname="true"  cmparams="sheetcode:'FT-TRD.TX.Prj.SeSOImpProject'" />
         <c name="wfcode" title="${RES.$.wfcode}" sqltype="12" width="${RES.$.D.HR.CW.status}" rdonly="true"  hidden="true"/>
		<c name="wfuid" title="${RES.$.wfuid}" sqltype="12" hidden="true" width="${RES.$.D.HR.CW.status}" rdonly="true" codedata="#SN-APPR.wfunit" showname="true" tipIfOverflow="true" xprops.CodeData.KeyNames="wfcode"/>
        <c name="bcode" title="${RES.$.wcode}" submitOnInput="true" sqltype="12"   width="${RES.$.D.HR.CW.bwcode}" codedata="#FT-CODE.BWcode" disableed="true" showname="true" selectMiddle="false"   aidInputerBtn="true"  uiprops.renderer="new snsoft.ft.code.bcode.codedata.BWcodeNameRender({})"  disableDelIfAI="true" 	cmparams="hasUser:true,sheetcode:'FT-TRD.TX.Prj.SeSOImpProject',opids:'C'"  aiprops="copyMap:{wcode:'wcode'}" noblankOnSubmit="true" />
        <c name="wcode" title="${RES.$.wcode}"  submitOnInput="true" sqltype="12" width="80" noblankOnSubmit="true" rdonly="true" codedata="#FT-CODE.Wcode"     showname="true" hidden="true" />
        <c name="corpbcode" title="${RES.$.corpbcode}" aidInputerBtn="true" disableed="true" showname="true" codedata="#FT-CODE.CorpBcode" cmparams.sheetcode="FT-TRD.TX.Prj.SeSOImpProject" cmparams.opids="C" cmparams.status="70" aiprops="copyMap:{sfcode:'sfcode',fcode:'sfcode'}"    cmprops.pmFromPane="{fromBcode:'bcode'}" cmparams.fromBtype="&quot;01&quot;" sqltype="12" noblankOnSubmit="true" width="${RES.$.D.HR.CW.corpbcode}"   disableDelIfAI="true"/>
        <c name="costbcode" title="${RES.$.costbcode}" width="${RES.$.D.HR.CW.costbcode}" disableed="true" aidInputerBtn="true" codedata="#FT-CODE.CostBcode" showname="true"   cmparams.sheetcode="FT-TRD.TX.Prj.SeSOImpProject" cmparams.opids="C" cmprops.pmFromPane="{fromBcode:'bcode',fromWcode:'wcode'}" cmparams.fromBtype="&quot;01&quot;" sqltype="12" hidden="true"/>
        <c name="sheetcode" title="${RES.$.sheetcode}"   sqltype="12" hidden="true" />
        <c hidden="true" name="vprepare" title="${RES.$.vprepare}" sqltype="12"     width="80" rdonly="true" codedata="#FT-CODE.Users" showname="true" />
        <c hidden="true" name="predate" title="${RES.$.predate}" sqltype="91"    width="130" rdonly="true" />
        <c hidden="true" name="modifier" title="${RES.$.modifier}" sqltype="12"    width="80" rdonly="true" codedata="#FT-CODE.Users" showname="true"    modifierColumn="true" />
        <c hidden="true" name="modifydate" title="${RES.modifydate}" sqltype="91"   width="130" rdonly="true" modifydateColumn="true" />
        <c name="busielemcode" title="所属业务要素" sqltype="12" width="120"  sqlexpr="ft_trd_prj_extse.busielemcode" cascadeflds="busielemcode"  hidden="true"/>
		<c name="ttcode" title="合约条款模板编号" sqltype="12" width="120"  sqlexpr="ft_trd_prj_extse.ttcode" cascadeflds="ttcode" hidden="true"/>
		<c name="tmcode" title="合约条款公式编号" sqltype="12" width="120"  sqlexpr="ft_trd_prj_extse.tmcode" cascadeflds="tmcode" hidden="true"/>
		<c name="termflags" title="合约条款标识" sqltype="4" width="120" sqlexpr="ft_trd_prj_extse.termflags" cascadeflds="termflags" hidden="true"/>
		<c name="sfcode" title="本位币种"  sqltype="12"  sqlexpr="ft_trd_prj_extse.sfcode"  cascadeflds="sfcode" codedata="#FT-CODE.Fcode" showcode="true" disableed="true" disableDelIfAI="true" hidden="true"/>
   		<c name="bcfcode" title="${RES.$.bcfcode}" sqltype="12"  width="120"  hidden="true" />	
   		<c name="bcfcodelist" title="${RES.$.bcfcodelist}" sqltype="12" width="120" hidden="true"/>
   		<c name="scmcode" title="${RES.$.scmcode}" sqltype="12"  width="120"  hidden="true" />
    	<c name="busielemcode" title="业务要素" sqlexpr="ft_trd_prj_extse.busielemcode" cascadeflds="busielemcode"  sqltype="12" width="120" hidden="true"/>
    	<c name="busielemcodelist" title="业务要素list" sqltype="12" width="120" hidden="true" />
    	<c name="isformprj" title="形式项目"   sqltype="12"  hidden="true"/>
   		<c name="businessucode"  title="${RES.C}"   sqltype="12"  hidden="true"/>
   		<c name="ordprop"  title="合同性质"   sqltype="12"    hidden="true"     sqlexpr="ft_trd_prj_extse.ordprop" cascadeflds="ordprop" />
		<c name="deliverymode" title="合同交期方式"  sqltype="12"  cliInitAllCodes="true"    sqlexpr="ft_trd_prj_extse.deliverymode" cascadeflds="deliverymode" showname="true" codedata="#DT_FT-TRD.DeliveryMode"  disableed="true" disableDelIfAI="true" hidden="true" />
       	<c name="portinputmode" title="港口录入方式"  sqltype="12"  cliInitAllCodes="true"    sqlexpr="ft_trd_prj_extse.portinputmode" cascadeflds="portinputmode" showname="true" codedata="#DT_FT-TRD.PortInputMode"  disableed="true" disableDelIfAI="true" hidden="true" />
        <c name="isfmtord" title="是否格式合同" sqltype="12"  cliInitAllCodes="true"    sqlexpr="ft_trd_prj_extse.isfmtord" cascadeflds="isfmtord"  showname="true" codedata="#DT_90.YN" disableDelIfAI="true" hidden="true" />   		
    	<c name="ccodetrust"  title="委托方"   sqlexpr="ft_trd_prj.ccodetrust"     sqltype="12" hidden="true"/>
    	<c name="pftcode" title="盈亏测算编号" sqltype="12" hidden="true" />
    	<c name="nstype"  title="${RES.$.nstype}"  sqltype="12" hidden="true" />
    	<c name="tradetype" title="${RES.$.tradetype}"  sqltype="12" hidden="true" codedata="#DT_FT-CODE.TradeType"  showname="true"   cliInitAllCodes="true"/>
    	<c name="trademode" title="${RES.$.trademode}"  sqltype="12" hidden="true" codedata="#DT_FT-CODE.TradeMode"  showname="true"   cliInitAllCodes="true"/>
    	<c name="psmode" title="${RES.$.psmode}"  sqltype="12" hidden="true" codedata="#DT_FT-CODE.PsMode"  showname="true"   cliInitAllCodes="true"  />
    	<c name="purmode"  title="${RES.$.purmode}"   sqltype="12" hidden="true" codedata="#DT_FT-CODE.PurMode"  showname="true"   cliInitAllCodes="true"/>
    	<c name="busimode"  title="${RES.$.busimode}"  sqltype="12" hidden="true"  cliInitAllCodes="true"   codedata="#DT_FT-CODE.Busimode" showname="true"/>
    	<c name="extcstype" title="改单流程类型"   sqltype="12" hidden="true" />
    	<c name="hisflag" title="历史"  sqltype="4"  sqlexpr="ft_trd_prj_extse.hisflag"   cascadeflds="hisflag" rdonly="true" showname="true"  disableed="true" align="center" codedata="#DT_90.YESNO" hidden="true"/>
   		<c name="salccode"  title="${RES.C}"   codedata="#FT-CODE.CcodeLMExt"   showname="true"  sqlexpr="ft_trd_prj_extse.salccode"   cascadeflds="salccode"    sqltype="12"    hidden="true"/>
    	<c hidden="true" name="loadingport" title="装运港" sqltype="12" codedata="#FT-CODE.Port" showname="true" disableed="true"   sqlexpr="ft_trd_prj_extse.loadingport"   cascadeflds="loadingport" />
    	<c hidden="true" name="dischargeport" title="目的港" sqltype="12"  codedata="#FT-CODE.Port" showname="true" disableed="true"   sqlexpr="ft_trd_prj_extse.dischargeport"   cascadeflds="dischargeport"/>
   		<c name="ordsheetcode" title="要素合同单据号" sqltype="12"    sqlexpr="ft_trd_prj_extse.ordsheetcode" cascadeflds="ordsheetcode" hidden="true"/>
    </m:RecordTable>

    <m:T name="tab">
     <m:P name="ft_trd_prj_basbas" title="${RES.$.title.F.tab.bas}">
        <m:RecordTable title="${RES.$.title.F.tab.bas}"    name="ft_trd_prj_bas" mainui="ft_trd_prj">
            <GroupPane name="ft_trd_prj_busi"     title="${RES.$.title.F.grp.bas}"   layoutm="grid" cellcols="4" clayoutwids="${RES.$.D.R.4CW}"    uiprops.subCompOpts="2">
               <c name="signdate"  title="${RES.C}"    sqltype="91" aidInputerBtn="true"  sqlexpr="ft_trd_prj_extse.signdate"  cascadeflds="signdate"  noblankOnSubmit="true"/>
               <c name="signsite"  title="${RES.C}"   noblankOnSubmit="true"  sqlexpr="ft_trd_prj_extse.signsite" cascadeflds="signsite"  sqltype="12"    aidInputerBtn="true"     mutipleLine="true" />
               <c name="outordcode" title="${RES.C}"   sqlexpr="ft_trd_prj_extse.outordcode"  cascadeflds="outordcode"     sqltype="12"   tipIfOverflow="true"/>
               <c name="salsncitycode" title="${RES.C}" sqltype="12"  sqlexpr="ft_trd_prj_extse.salsncitycode"   cascadeflds="salsncitycode" codedata="#FT-CODE.ProvCity" showname="true" disableed="true"  rdonly="true" noblankOnSubmit="true"/>
               <c name="salccode"  title="${RES.C}"   codedata="#FT-CODE.CcodeLMExt"    disableed="true" showname="true" aidInputerBtn="true"  sqlexpr="ft_trd_prj_extse.salccode"   cascadeflds="salccode"    sqltype="12"   noblankOnSubmit="true"  disableDelIfAI="true" layoutwidth="2" 
                 aiprops="initParasVales:{ccodetypes:'12',ccodearea:'10'},dlgParam:{bcode:'ft_trd_prj.bcode',wcode:'ft_trd_prj.wcode'},copyMap:{sallinkmanname:'linkmanname',sallinkmanmobile:'mobile',salncode:'ncode',salsncitycode:'sncitycode'}" />
               <c name="sallinkmanname" title="${RES.C}"  sqltype="12"  sqlexpr="ft_trd_prj_extse.sallinkmanname"   cascadeflds="sallinkmanname"  codedata="#FT-CODE.CcodeLinkManBWcode" aidInputerBtn="true"   aiprops="dlgParam:{ccode:'ft_trd_prj.salccode',bcode:'ft_trd_prj.bcode',wcode:'ft_trd_prj.wcode'},copyMap:{sallinkmanname:'linkmanname',sallinkmanmobile:'phone'}" />
               <c name="sallinkmanmobile" title="${RES.C}"  sqltype="12"  sqlexpr="ft_trd_prj_extse.sallinkmanmobile"   cascadeflds="sallinkmanmobile"/>
          	   <c name="ccodetrust"   title="${RES.$.ccodetrust}"    sqlexpr="ft_trd_prj.ccodetrust"   sqltype="12"  hidden="true"/>
               <c name="salncode"  hidden="true" title="${RES.C}"  sqltype="12"  sqlexpr="ft_trd_prj_extse.salncode"   cascadeflds="salncode" codedata="#FT-CODE.Ncode" showname="true"  rdonly="true"  noblankOnSubmit="true"/>
               <c name="remark"  title="${RES.$.remark}"   sqlexpr="ft_trd_prj.remark"   sqltype="12" aidInputerBtn="true"     mutipleLine="true" tipIfOverflow="true" aidInputableIfRdonly="true" layoutwidth="2" />
<!--               <c name="busimode"  title="${RES.$.busimode}"  codedata="#DT_FT-CODE.Busimode" showname="true"   sqltype="12"  rdonly="true"  noblankOnSubmit="true"/>-->
<!--               <c name="psmode" title="${RES.$.psmode}" sqltype="12" tipIfOverflow="true" codedata="#DT_FT-CODE.PsMode" disableed="true" showname="true" rdonly="true" noblankOnSubmit="true"/>-->
               <c name="ratifydate"  title="${RES.$.ratifydate}"  sqltype="91"  rdonly="true" newLine="true" hidden="true"/>
                <c name="stsext00" title="${RES.C}" sqltype="12"  hidden="true"/>       
                <c name="stsext01" title="${RES.C}" sqltype="12"  hidden="true"/>   
                <c name="stsext02" title="${RES.C}" sqltype="12"  hidden="true"/>   
                <c name="stsext03" title="${RES.C}" sqltype="12"  hidden="true"/>   
                <c name="stsext04" title="${RES.C}" sqltype="12"  hidden="true"/>   
                <c name="stsext05" title="${RES.C}" sqltype="12"  hidden="true"/>   
                <c name="stsext06" title="${RES.C}" sqltype="12"  hidden="true"/>
            </GroupPane>
            
          <GroupPane name="ft_trd_prj_busielemcode"   hidden="true"  title="业务要素"   layoutm="grid"  clayoutwids="${RES.$.D.R.4CW}"    uiprops.subCompOpts="2" >
               <c name="busielemcodelist" title="业务要素" sqltype="12"    hidden="true" />
            </GroupPane>
             <GroupPane name="ft_trd_prj_fcy"    title="${RES.$.title.F.grp.money}"    layoutm="grid" border="true" layoutwidth="4" cellcols="4"  clayoutwids="${RES.$.D.R.4CW}"    uiprops.subCompOpts="2">
               <c name="fcode" title="${RES.$.fcode}"  sqltype="12"  sqlexpr="ft_trd_prj_extse.fcode"  cascadeflds="fcode" codedata="#FT-CODE.Fcode" cmparams.SQLFILTER="status='70'"  showcode="true" disableed="true" disableDelIfAI="true" noblankOnSubmit="true" rdonly="true" submitOnInput="true"/>
               <c name="sfcode" title="${RES.C}"  sqltype="12"  sqlexpr="ft_trd_prj_extse.sfcode"  cascadeflds="sfcode" codedata="#FT-CODE.Fcode" showcode="true" disableed="true" disableDelIfAI="true" hidden="true"/>
               <c name="fserate" title="${RES.C}"  sqltype="2"  sqlexpr="ft_trd_prj_extse.fserate"  cascadeflds="fserate" rdonly="true" noblankOnSubmit="true" hidden="true"/>
               <c name="fcerate" title="${RES.C}"  sqltype="2"  sqlexpr="ft_trd_prj_extse.fcerate"  cascadeflds="fcerate"  noblankOnSubmit="true" hidden="true"/>
               <c name="fuerate" title="${RES.C}"  sqltype="2"  sqlexpr="ft_trd_prj_extse.fuerate"   cascadeflds="fuerate"   noblankOnSubmit="true" hidden="true"/>
               <c name="fcy" title="${RES.$.ft_trd_prj_extse.fcyD}" sqltype="2" mindeci="2"  rdonly="true"  sqlexpr="ft_trd_prj_extse.fcy"   cascadeflds="fcy"  noblankOnSubmit="true" />
               <c name="zusd" title="${RES.C}" sqltype="2" mindeci="2" rdonly="true"  sqlexpr="ft_trd_prj_extse.zusd"   cascadeflds="zusd"   hidden="true"/>
               <c name="zcny" title="${RES.C}" sqltype="2" mindeci="2"  rdonly="true"  sqlexpr="ft_trd_prj_extse.zcny"   cascadeflds="zcny"  hidden="true"/>
               <c name="scy" title="${RES.C}" sqltype="2" mindeci="2"  rdonly="true"  sqlexpr="ft_trd_prj_extse.scy"   cascadeflds="scy"   hidden="true"/>
               <c name="internalscy" title="国内费用本位币" sqltype="2" mindeci="2" rdonly="true"  sqlexpr="ft_trd_prj_extse.internalscy"   cascadeflds="internalscy"    hidden="true"/>
             </GroupPane>
            <GroupPane name="ft_trd_prj_priceterm"   title="${RES.$.title.F.grp.basterm}"  layoutm="grid" border="true" layoutwidth="8" cellcols="8"      clayoutwids="${RES.$.D.R.8CW}"   uiprops.subCompOpts="2">
				<c name="deliverydate" title="${RES.C}" sqltype="91" aidInputerBtn="true"  sqlexpr="ft_trd_prj_extse.deliverydate"   cascadeflds="deliverydate"  noblankOnSubmit="true"   layoutwidth="2"/>
				<c name="loadingport" title="${RES.C}" sqltype="12" codedata="#FT-CODE.Port" showname="true" disableed="true"   sqlexpr="ft_trd_prj_extse.loadingport"   cascadeflds="loadingport"  layoutwidth="2"  aiprops="copyMap:{loadingportdesc:'enportname'}" hidden="true"/>
				<c name="loadingportdesc" title="${RES.C}" sqltype="12"  sqlexpr="ft_trd_prj_extse.loadingportdesc"   cascadeflds="loadingportdesc" aidInputerBtn="true" tipIfOverflow="true" mutipleLine="true"  layoutwidth="2" hidden="true"/>
				<c name="dischargeport" title="${RES.C}" sqltype="12"  codedata="#FT-CODE.Port" showname="true" disableed="true"   sqlexpr="ft_trd_prj_extse.dischargeport"   cascadeflds="dischargeport"   layoutwidth="2" aiprops="copyMap:{dischargeportdesc:'enportname'}" hidden="true" />
				<c name="dischargeportdesc" title="${RES.C}" sqltype="12"  sqlexpr="ft_trd_prj_extse.dischargeportdesc"   cascadeflds="dischargeportdesc" aidInputerBtn="true" tipIfOverflow="true" mutipleLine="true"  layoutwidth="2"  hidden="true"/>
             	<c name="overshiprate" title="${RES.C}" sqltype="2" percent="true" sqlexpr="ft_trd_prj_extse.overshiprate" cascadeflds="overshiprate"  noblankOnSubmit="true"   layoutwidth="2"/>
             	<c name="shortshiprate" title="${RES.C}" sqltype="2" percent="true" sqlexpr="ft_trd_prj_extse.shortshiprate" cascadeflds="shortshiprate"   noblankOnSubmit="true" layoutwidth="2"/>
             	<c name="ordsheetcode" title="要素合同单据号" sqltype="12"    sqlexpr="ft_trd_prj_extse.ordsheetcode" cascadeflds="ordsheetcode" hidden="true"  />
             	<c name="deliverymode" title="${RES.C}"  sqltype="12"   sqlexpr="ft_trd_prj_extse.deliverymode" cascadeflds="deliverymode" showname="true" codedata="#DT_FT-TRD.DeliveryMode" noblankOnSubmit="true" disableed="true" disableDelIfAI="true" hidden="true"  />
            	<c name="portinputmode" title="${RES.C}"  sqltype="12"   sqlexpr="ft_trd_prj_extse.portinputmode" cascadeflds="portinputmode" showname="true" codedata="#DT_FT-TRD.PortInputMode" noblankOnSubmit="true" disableed="true" disableDelIfAI="true" hidden="true"  />
            	<c name="isfmtord" title="${RES.C}" sqltype="12"   sqlexpr="ft_trd_prj_extse.isfmtord" cascadeflds="isfmtord"  showname="true" codedata="#DT_90.YN" disableDelIfAI="true" hidden="true"  />
             </GroupPane>
           </m:RecordTable>
           <m:GroupPane name="ft_trd_prj_bas4"   title="${RES.$.title.F.grp.settsd}">
              <m:GridTable title="${RES.$.title.F.grp.settsd}" titleHidden="true" name="ft_trd_prj_se_sett" sqlexpr="ft_trd_prj_se_sett"   mainui="ft_trd_prj" skipRdOnlyCellOnEnterKey="true"   uiprops.minVisibleRows="3" uiprops.fitInDOMHeight="0"  uiprops="hideVBar:true" 
                   all_c_columns="true" xprops.LoadDataService="FT-TRD.TX.SeSOImpProjectUIService#queryProjectSeSettUI" dsprops="masterColumnsOnRefresh:['prjicode']" >
                <Operate fixedLeft="true" name="operate" title="${RES.$.operate}" width="60" sqlexpr="null"    disableEnterEd="true" staticText="true" align="center"   bottomval="recind"/>
       			<c name="prjicode" title="项目内码" sqltype="12"  width="120"  xprops.cpmastercol=":prjicode" hidden="true"  rdonly="true"/>
       			<c name="busielemcode"  title="业务要素" sqltype="12"  width="120"  xprops.cpmastercol=":busielemcode" hidden="true"  rdonly="true"/>
       			<c name="sesicode" title="内码" sqltype="12"  width="120"  rdonly="true" hidden="true"/>
        		<c name="idx" title="${RES.C}" sqltype="4"  rdonly="true" hidden="true"/>
        		<c name="paymode" title="${RES.$.ft_trd_prj_se_sett.paymodeD}" sqltype="12"   codedata="#FT-CODE.RPMode" cmparams.SQLFILTER="domabr='20'" cmparams="sheetcode:'${sheetcode}'" cmprops="pmFromPane:{sheetcode:'ft_trd_prj.ordsheetcode'}" showname="true" disableed="true"  width="120"  noblankOnSubmit="true"  disableDelIfAI="true"/>
        		<c name="abgoods" title="货前后" sqltype="12"   codedata="#DT_FT-CODE.ABGoods"  disableDelIfAI="true"  showname="true" disableed="true" width="80"  noblankOnSubmit="true"/>
        		<c name="days" title="天数" sqltype="4"  width="80"/>
        		<c name="fardays" title="远期天数" sqltype="4" width="100"/>
        		<c name="payrate" title="比例" sqltype="2" percent="true" width="${RES.$.D.G.CW.rate}" noblankOnSubmit="true" bottomval="*"/>
				<c name="fcy" title="${RES.$.ft_trd_prj_se_sett.fcyD}" sqltype="2" mindeci="2"  width="${RES.$.D.G.CW.fcy}"  noblankOnSubmit="true"  bottomval="*"/>
				<c name="remark" title="${RES.$.remark}"  sqltype="12"   width="${RES.$.D.G.CW.remark}"  aidInputerBtn="true" mutipleLine="true" tipIfOverflow="true" aidInputableIfRdonly="true"/>
				<c name="hisflag" title="历史"  sqltype="4" rdonly="true" showname="true"  disableed="true" align="center" codedata="#DT_90.YESNO" width="80" hidden="true"/>
    			</m:GridTable>
			</m:GroupPane>
<!--        <m:RecordTable name="ft_trd_prj_bas2"  mainui="ft_trd_prj"  title="${RES.$.title.F.tab.bas}">-->
<!--            <GroupPane name="prj_system"    title="${RES.$.title.F.grp.sys}"  layoutm="grid" border="true" layoutwidth="6" cellcols="6">-->
<!--                <c name="vsn" title="${RES.$.vsn}"   sqltype="4" width="${RES.$.D.BR.CW.vsn}"      rdonly="true" />-->
<!--                <c name="vprepare" title="${RES.$.vprepare}"   sqltype="12" width="${RES.$.D.BR.CW.ucode}"    showname="true" rdonly="true" codedata="#FT-CODE.Users" />-->
<!--                <c name="predate" title="${RES.$.predate}"  sqltype="91" width="${RES.$.D.BR.CW.time}"   rdonly="true" />-->
<!--                <c name="modifier" title="${RES.$.modifier}"    sqltype="12" width="${RES.$.D.BR.CW.ucode}"   rdonly="true" codedata="#FT-CODE.Users" showname="true"     modifierColumn="true" />-->
<!--                <c name="modifydate" title="${RES.$.modifydate}"    sqltype="91" width="${RES.$.D.BR.CW.time}" rdonly="true"   modifydateColumn="true" />-->
<!--                <c name="vsnflag" title="${RES.$.vsnflag}"    sqltype="4" width="100" rdonly="true" hidden="true" />-->
<!--                <c name="curratifydate" title="${RES.$.curratifydate}" sqltype="91"  rdonly="true"  width="${RES.$.D.BR.CW.time}" />-->
<!--            </GroupPane>-->
<!--        </m:RecordTable>-->
		 <include src="FT-TRD.Comm.BusiClassifyRelaInfo#ft_trd_salprj_busifysys_bas" mainui="ft_trd_prj"/>
        </m:P>
        
        <m:P name="ft_trd_prj_seg_bas" title="${RES.$.title.F.tab.goods}">
				<m:P>
				<ToolbarBtn name="busielemg_340_btn_getGoods" noClientComponent="true" title="${RES.$.title.F.btn.selgoods}" xprops.iconClassName="icons-btn-add"/>
				<ToolbarBtn name="busielemg_340_btn_XLSIMP_FT-TRD_Ft_trd_prj_simp_so_seg" noClientComponent="true" title="${RES.$.title.F.btn.importgoods}" xprops.iconClassName="icons-btn-import"/>
				<ToolbarBtn name="busielemg_340_btn_batchModify" noClientComponent="true" title="${RES.$.title.F.btn.batchmodify}" xprops.iconClassName="icons-btn-batch_edit"/>
				<ToolbarBtn name="busielemg_340_btn_genGoods" noClientComponent="true" title="${RES.$.title.F.btn.savegood}"  xprops.iconClassName="icons-btn-insert"/>
				</m:P>
				<m:GridTable title="${RES.$.title.F.tab.goods}" all_c_columns="true"  titleHidden="true" mutiple="true" name="busielemg_340" sqlexpr="ft_trd_prj_seg" mainui="ft_trd_prj"   xprops.LoadDataService="FT-TRD.TX.SeSOImpProjectUIService#queryProjectSeGoodUI"  skipRdOnlyCellOnEnterKey="true"   uiprops.minVisibleRows="10"  uiprops.fitSizeOnWinResize="{flags:0x15,oheight:10}"   dsprops="masterColumnsOnRefresh:['cuicode','bcode','corpbcode'],forceSaveColumns:['busielemcode']">
				<attrs>
				<attr type="203" name="getGoods" title="${RES.$.title.F.btn.selgoods}" _rlog="true"/>
				<attr type="203" name="XLSIMP_FT-TRD_Ft_trd_prj_simp_so_seg" title="${RES.$.title.F.btn.importgoods}" _rlog="true"/>
				<attr type="203" name="genGoods" title="${RES.$.title.F.btn.savegood}" _rlog="true"/>
				<attr type="203" name="batchModify" title="${RES.$.title.F.btn.batchmodify}" _rlog="true"/>
				</attrs>
				<Operate fixedLeft="true" name="operate" title="${RES.$.operate}" width="110" sqlexpr="null" disableEnterEd="true" staticText="true" align="center"   bottomval="recind"/>
				<c name="prjicode" title="项目内码" sqltype="12" xprops.cpmastercol=":prjicode" hidden="true"/>
				<c name="srcgicode" title="源商品内码" sqltype="12" hidden="true"/>
				<c name="segicode" title="内码" sqltype="12" hidden="true"/>
				<c name="idx" title="${RES.C}" sqltype="4" width="${RES.$.D.G.CW.idx}" asceOrd="true"  noblankOnSubmit="true"  align="center"/>
				<c name="gcode" title="${RES.$.gcode}" sqltype="12" disableDelIfAI="true" disableed="true" noblankOnSubmit="true" codedata="#FT-CODE.Gcodes" tipIfOverflow="true" showname="false" aidInputerBtn="true" width="${RES.$.D.G.CW.gcode}"   fixedLeft="true"
				aiprops="__$thisCfg:true,dlgParam:{'sheetcode':'ft_trd_prj.ordsheetcode','corpbcode':'ft_trd_prj.corpbcode'},ignoreDftCopy:true,multiRow:true,copyMap:{gcode:'gcode',gvcode:'gvcode',qtcunit:'qtcunit',qtyunit:'qtyunit',specifidesc:'specifidesc',cnamedesc:'gname',brand:'brand',sgtrgrpcode:'sgtrgrpcode',sgattr00:'sgattr00',sgattr01:'sgattr01',sgattr02:'sgattr02',sgattr03:'sgattr03',sgattr04:'sgattr04',sgattr05:'sgattr05',sgattr06:'sgattr06',sgattr07:'sgattr07',sgattr08:'sgattr08',sgattr09:'sgattr09',sgattr10:'sgattr10',sgattr11:'sgattr11',sgattr12:'sgattr12',sgattr13:'sgattr13',sgattr14:'sgattr14',sgattr15:'sgattr15',sgattr16:'sgattr16',sgattr17:'sgattr17',sgattr18:'sgattr18',sgattr19:'sgattr19',addtaxrate:'addtaxrate_g',gtrgrpcode:'gtrgrpcode',gattr00:'gattr00',gattr01:'gattr01',gattr02:'gattr02',gattr03:'gattr03',gattr04:'gattr04',gattr05:'gattr05',gattr06:'gattr06',gattr07:'gattr07',gattr08:'gattr08',gattr09:'gattr09',gattr10:'gattr10',gattr11:'gattr11',gattr12:'gattr12',gattr13:'gattr13',gattr14:'gattr14',gattr15:'gattr15',gattr16:'gattr16',gattr17:'gattr17',gattr18:'gattr18',gattr19:'gattr19'}"/>
				<c name="gvcode" title="${RES.$.gvcode}" sqltype="12" disableed="true" selectMiddle="false" tipIfOverflow="true" hidden="true" codedata="#FT-CODE.Gvcodes" showname="true" aidInputerBtn="true" width="${RES.$.D.G.CW.gvcode}"  aiprops="ignoreDftCopy:true"/>
				<c name="cnamedesc" title="${RES.$.cnamedesc?商品名称}" sqltype="12"  width="${RES.$.D.G.CW.gcnamedesc}"  mutipleLine="true" tipIfOverflow="true" aidInputerBtn="true" noblankOnSubmit="true"/>
				<c name="brand" title="品牌" sqltype="12"  width="${RES.$.D.G.CW.brand}" mutipleLine="true"  tipIfOverflow="true"  aidInputerBtn="true" />
				<c name="specifidesc" title="规格型号" sqltype="12"  width="${RES.$.D.G.CW.specifidesc}" mutipleLine="true"  tipIfOverflow="true"  noblankOnSubmit="true" aidInputerBtn="true" />
				<c name="gtrgrpcode" title="货号属性组" sqltype="12" width="${RES.$.D.G.CW}"  sqlexpr="null" hidden="true" rdonly="true"/>
				<c name="gattr00" title="货号要素00" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr01" title="货号要素01" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr02" title="货号要素02" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr03" title="货号要素03" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr04" title="货号要素04" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr05" title="货号要素05" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr06" title="货号要素06" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr07" title="货号要素07" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr08" title="货号要素08" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr09" title="货号要素09" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr10" title="货号要素10" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr11" title="货号要素11" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr12" title="货号要素12" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr13" title="货号要素13" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr14" title="货号要素14" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr15" title="货号要素15" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr16" title="货号要素16" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr17" title="货号要素17" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr18" title="货号要素18" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				<c name="gattr19" title="货号要素19" sqltype="12"  width="${RES.$.D.G.CW.gattr}"  sqlexpr="null" hidden="true"  rdonly="true"/>
				
				<c name="goodsdesc" title="货物描述" sqltype="12" width="${RES.$.D.G.CW.goodsdesc}"  tipIfOverflow="true"  mutipleLine="true"   aidInputerBtn="true"/>
				<c name="tgvattr00" title="商品类目属性" sqltype="12" width="${RES.$.D.G.CW}" hidden="true"/>
				<c name="sgtrgrpcode" title="库存属性组" sqltype="12" width="${RES.$.D.G.CW}" hidden="true" codedata="#FT-CODE.StockGattrGrps" showname="true" disableed="true" rdonly="true"/>
				<c name="sgattr00" title="库存要素00" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr01" title="库存要素01" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr02" title="库存要素02" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr03" title="库存要素03" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr04" title="库存要素04" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr05" title="库存要素05" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr06" title="库存要素06" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr07" title="库存要素07" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr08" title="库存要素08" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr09" title="库存要素09" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr10" title="库存要素10" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr11" title="库存要素11" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr12" title="库存要素12" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr13" title="库存要素13" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr14" title="库存要素14" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr15" title="库存要素15" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr16" title="库存要素16" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr17" title="库存要素17" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr18" title="库存要素18" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="sgattr19" title="库存要素19" sqltype="12" width="${RES.$.D.G.CW.sgattr}" hidden="true"/>
				<c name="qtc" title="签约数量" sqltype="2"  width="${RES.$.D.G.CW.qty}"  bottomval="*" noblankOnSubmit="true"/>
				<c name="qtcunit" title="签约单位"  disableDelIfAI="true" sqltype="12" aidInputerBtn="true" showname="true" disableed="true" codedata="#FT-CODE.SysUnit"  width="${RES.$.D.G.CW.unit}"  noblankOnSubmit="true"/>
				<c name="qcyrate" title="换算比例" sqltype="2"  width="${RES.$.D.G.CW.rate}"   noblankOnSubmit="true"/>
				<c name="qty" title="统计数量" sqltype="2"  width="${RES.$.D.G.CW.qty}"  bottomval="*" noblankOnSubmit="true"/>
				<c name="qtyunit" title="统计单位" sqltype="12" aidInputerBtn="true" rdonly="true" showname="true" disableed="true" codedata="#FT-CODE.SysUnit"  width="${RES.$.D.G.CW.unit}"    disableDelIfAI="true" noblankOnSubmit="true"/>
				<c name="upric" title="${RES.$.ft_trd_prj_seg.upricD}" sqltype="2"  width="${RES.$.D.G.CW.upric}"  noblankOnSubmit="true"/>
				<c name="fcy" title="${RES.$.ft_trd_prj_seg.fcyD}" sqltype="2" mindeci="2"  width="${RES.$.D.G.CW.fcy}" bottomval="*" noblankOnSubmit="true"/>
				<c name="zusd" title="美元金额" sqltype="2" mindeci="2" width="${RES.$.D.G.CW.fcy}" bottomval="*" rdonly="true" hidden="true"/>
				<c name="scy" title="本位币金额" sqltype="2" mindeci="2" width="${RES.$.D.G.CW.fcy}" bottomval="*" rdonly="true" hidden="true"/>
				<c name="zcny" title="人民币金额" sqltype="2" mindeci="2" width="${RES.$.D.G.CW.fcy}" bottomval="*" rdonly="true" hidden="true"/>
				<c name="natupric" title="不含税单价" sqltype="2"  width="${RES.$.D.G.CW.upric}"   noblankOnSubmit="true" rdonly="true"/>
				<c name="natscy" title="不含税金额" sqltype="2" mindeci="2" width="${RES.$.D.G.CW.fcy}"   noblankOnSubmit="true"  rdonly="true"  bottomval="*"   />
				<c name="addtaxrate" title="增值税率" sqltype="2" width="${RES.$.D.G.CW.rate}" percent="true"  noblankOnSubmit="true"/>
				<c name="addtaxscy" title="增值税金额" sqltype="2" mindeci="2" width="${RES.$.D.G.CW.fcy}"  rdonly="true" noblankOnSubmit="true"  bottomval="*"   />
				<c name="deliverydate" title="交货日期" sqltype="91"   aidInputerBtn="true" width="${RES.$.D.G.CW.date}"  hidden="true"/>
				<c name="loadingport" title="装运港" sqltype="12" codedata="#FT-CODE.Port" showname="true" disableed="true"    width="${RES.$.D.G.CW}"  hidden="true"/>
				<c name="dischargeport" title="目的港" sqltype="12"  codedata="#FT-CODE.Port" showname="true" disableed="true"    width="${RES.$.D.G.CW}"   hidden="true"/>
				<c name="hisflag" title="历史"  sqltype="4" rdonly="true" showname="true"  disableed="true" align="center" codedata="#DT_90.YESNO" width="80" hidden="true"/>
                <c name="has_me" title="启用申报要素属性标识" sqltype="12" width="${RES.$.D.G.CW.YN}"  hidden="true"/>
				<c name="busielemcode"  title="业务要素" sqltype="12" width="${RES.$.D.G.CW}" hidden="true"/> 
				<c name="prictype" title="定价方式" sqltype="12"  width="${RES.$.D.G.CW.aidCol}"  disableed="true" showname="true" codedata="#DT_FT-TRD.PricType"  hidden="true"/>
			
			</m:GridTable>
			</m:P>
	 <m:B  title="${RES.$.title.F.tab.ordterm}">
            <include src="FT-TRD.Comm.SheetTerm#btn" mainui="ft_trd_prj" />
            <include src="FT-TRD.Comm.SheetTerm#ft_trd_termdetail"    mainui="ft_trd_prj" />
            <include src="FT-TRD.Comm.SheetTerm#ft_trd_termfill"   mainui="ft_trd_prj" />
        </m:B>
		  <m:B  title="${RES.$.title.F.tab.profit}">
			 <include src="FT-TRD.Comm.BusiSheetProfit#ft_trd_pft" mainui="ft_trd_prj" />
		</m:B>
       
         <m:B  title="${RES.$.title.F.tab.unionorders}">
      <include src="FT-TRD.Comm.UnionOrders#unionorders"    mainui="ft_trd_prj" />
		</m:B>
	<m:T title="${RES.$.title.F.tab.print}" uiprops.cellClassName="ui-tabpane-level2">
        <H name="sheetPrt"  title="${RES.$.title.F.tab.prt}"  uiprops.leftSize="430" height="450">
            <GroupPane name="trd_prj_dylx" title="文档类型" align="left">
                <include src="SN-PLAT.Doc.SheetDocResPub#printToolbar" />
                <include src="SN-PLAT.Doc.SheetDocResPub#prtdoctype" mainui="ft_trd_prj" />
            </GroupPane>
            <GroupPane name="trd_prj_dyfj" title="存档附件" align="left">
                <include src="SN-PLAT.Doc.SheetDocResPub#prtplat_sheetdoc" mainui="ft_trd_prj" />
            </GroupPane>
        </H>
        <H title="${RES.$.title.F.tab.docup}" uiprops.leftSize="355" height="450">
            <GroupPane name="trd_prj_fjlx" title="文档类型" align="left">
                <include src="SN-PLAT.Doc.SheetDocResPub#atdoctype" mainui="ft_trd_prj" />
            </GroupPane>
            <GroupPane name="trd_prj_fjfj" title="存档附件" align="left">
                <include src="SN-PLAT.Doc.SheetDocResPub#atplat_sheetdoc" mainui="ft_trd_prj" />
            </GroupPane>
        </H>
        <P title="${RES.$.title.F.tab.template}">
            <GroupPane name="trd_prj_fblx" title="文档类型" align="left">
                <include src="SN-PLAT.Doc.SheetDocResPub#moldoctype" mainui="ft_trd_prj" />
            </GroupPane>
        </P>
	</m:T>
        <m:P  title="${RES.$.title.F.tab.appr}">
        <m:DialogPane hidden="true" layoutm="grid" name="taskParam" region="north" title="审批结果">
            <c name="taskid" noblank="true" sqltype="12" />
        </m:DialogPane>
        <m:T region="center" name="taskTbl"  uiprops.cellClassName="ui-tabpane-level2">
            <include src="SN-APPR.PlatTasklist#apprdata" mainui="taskParam"/>
            <m:P title="${RES.$.title.F.tab.taskflow}" name="taskflow"></m:P>
            <m:P title="${RES.$.title.F.tab.taskroute}" name="taskroute"></m:P>
        </m:T>
    	</m:P>
    	  <m:B title="${RES.$.title.F.tab.vbhistory}">
			 <include src="SN-PLAT.VBVersion.VBHistoryEmbed#vbhistory"   mainui="ft_trd_prj" />
        </m:B> 
    </m:T>

</m:PageBody>

```


注:

1.xprops.LoadDataService属性调用UI服务详情页查询方法
2.需添加PlatFunctionListener 系统功能号：详情页系统功能UI
3.界面字段、标题需要抽取出资源文件，实际用法可参考**多语言资源**

#### 多语言资源

配置文件路径：UI工程 src/main/resources/cfg/resbundle/ResBundle[*].inf
命名规范：

     ResBundle[System]-[Model]_zh_CN.inf     资源文件
     ResBundleERR[System]-[Model]_zh_CN.inf  异常信息资源文件

命名示例：
    
     ResBundleFT-TRD-COMM_zh_CN.inf     资源文件(通用模块)
     ResBundleFT-TRD_zh_CN.inf          资源文件
     ResBundleERRFT-TRD-COMM_zh_CN.inf  异常信息资源文件(通用模块)
     ResBundleERRFT-TRD_zh_CN.inf       异常信息资源文件

参考配置：

```
## 贸易系统公共资源文件
$PARENT$=FT-TRD-WIDTH

#贸易固定列
datetype=日期类型
datefrom=日期从

#界面标题
##单据定义
title_FT-TRD.Doc.CusDecl.ExpRtnApp=出口退运申请单
title_FT-TRD.Doc.Customs.Review=关务复核时间配置
title_FT-TRD.Doc.ExpCusDecl.Draft=出口报关草单共用权限码
title_FT-TRD.Doc.CeCusDecl.Draft=合作出口报关草单
title_FT-TRD.Doc.ExpCusDecl.DraftDialog=出口报关草单
title_FT-TRD.Doc.Customs.Cusabn=关务异常

```

说明：

1.[System] 系统编号  [Model] 模块编号 其中根据项目资源文件大小 模块编号可以没有或者拆分多个
2.VO校验关联资源文件

```

  @ValidResource(ProjectConstants.TRDIMP_RESOURCE_ID)
  public class SeSOImpProject extends SeImpProject implements BusiElemAble
  {
  }
  
```
3.配置说明【[国际化](help.html?helpFile=help/SN-CMC/International/International.md)】
4.服务端 ResBundle.factory.getResBundle(SnaConst.sysid).get("xxx");
5.客户端 ResBundle.getString("FT-CODE","switchUp")



#### 单据穿透定义

1. 在UI工程下创建src/main/resources/cfg/res/viewdetail/ViewDetail系统.xml
1. 配置说明【[穿透配置](help.html?helpFile=help/SN-PLAT/Tools/ViewDetail.md)】
1. 参考配置

```
<?xml version="1.0" encoding="UTF-8"?>
<ViewDetails xmlns="http://www.snsoft.com.cn/schema/tool-viewdetail" xsi:schemaLocation="http://www.snsoft.com.cn/schema/tool-viewdetail http://www.snsoft.com.cn/schema/tool-viewdetail.xsd"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Details>
        <Colname>prjcode</Colname>
        <TACBlock><![CDATA[
        prjicode = dataSet.get("prjicode")
        if prjicode == null
            return null
        end if
        tableName="ft_trd_prj"
        assertExists(tableName,prjicode)
        sheetCode = dataSet.get("sheetcode")
        sheetEntry = dataSet.get("sheetEntry")
        if sheetCode==null || (sheetEntry!=true && prjicode!="<NULL>")
       		flag=(!prjicode.contains(",")) && prjicode.contains("<") && prjicode.contains(">")
            if flag
                tableName=tableName+"_vb"
            end if
    		sheetCode = getSheetCode(tableName,prjicode)
		end if
		if sheetCode=="FT-TRD.TX.Prj.AgcyExpProject"
        info.funcid = "FT-TRD.TX.Prj.AgcyExpProjectDetail"
        elif  sheetCode=="FT-TRD.TX.Prj.CscdExpProject"
        info.funcid = "FT-TRD.TX.Prj.CscdExpProjectDetail"
         elif  sheetCode=="FT-TRD.TX.Prj.ConsExpProject"
        info.funcid = "FT-TRD.TX.Prj.ConsExpProjectDetail"
        elif  sheetCode=="FT-TRD.TX.Prj.SeSPExpProject"
        info.funcid = "FT-TRD.TX.Prj.SeSPExpProjectDetail"
        elif  sheetCode=="FT-TRD.TX.Prj.SeSSExpProject"
        info.funcid = "FT-TRD.TX.Prj.SeSSExpProjectDetail"
         elif  sheetCode=="FT-TRD.TX.Prj.SeSOExpProject"
        info.funcid = "FT-TRD.TX.Prj.SeSOExpProjectDetail"
         elif  sheetCode=="FT-TRD.TX.Prj.SePOExpProject"
        info.funcid = "FT-TRD.TX.Prj.SePOExpProjectDetail"
         elif  sheetCode=="FT-TRD.TX.Prj.SeSPDomProject"
        info.funcid = "FT-TRD.TX.Prj.SeSPDomProjectDetail"
         elif  sheetCode=="FT-TRD.TX.Prj.SeSSDomProject"
        info.funcid = "FT-TRD.TX.Prj.SeSSDomProjectDetail"
        elif  sheetCode=="FT-TRD.TX.Prj.SeSODomProject"
        info.funcid = "FT-TRD.TX.Prj.SeSODomProjectDetail"
        elif  sheetCode=="FT-TRD.TX.Prj.SePODomProject"
        info.funcid = "FT-TRD.TX.Prj.SePODomProjectDetail"
         elif  sheetCode=="FT-TRD.TX.Prj.SePSDomProject"
        info.funcid = "FT-TRD.TX.Prj.SePSDomProjectDetail"
        elif  sheetCode=="FT-TRD.TX.Prj.SeSPOvsProject"
        info.funcid = "FT-TRD.TX.Prj.SeSPOvsProjectDetail"
        elif  sheetCode=="FT-TRD.TX.Prj.SeSOOvsProject"
        info.funcid = "FT-TRD.TX.Prj.SeSOOvsProjectDetail"
        elif  sheetCode=="FT-TRD.TX.Prj.SePOOvsProject"
        info.funcid = "FT-TRD.TX.Prj.SePOOvsProjectDetail"
         elif  sheetCode=="FT-TRD.TX.Prj.AcPPImpProject"
        info.funcid = "FT-TRD.TX.Prj.AcPPImpProjectDetail"
        elif  sheetCode=="FT-TRD.TX.Prj.SePSImpProject"
        info.funcid = "FT-TRD.TX.Prj.SePSImpProjectDetail"
        elif  sheetCode=="FT-TRD.TX.Prj.SePOImpProject"
        info.funcid = "FT-TRD.TX.Prj.SePOImpProjectDetail"
         elif  sheetCode=="FT-TRD.TX.Prj.SeSOImpProject"
        info.funcid = "FT-TRD.TX.Prj.SeSOImpProjectDetail"
         elif  sheetCode=="FT-TRD.TX.Prj.AgenExpProject"
        info.funcid = "FT-TRD.TX.Prj.AgenExpProjectDetail"
         elif  sheetCode=="FT-TRD.TX.Prj.CoSSExpProject"
        info.funcid = "FT-TRD.TX.Prj.CoSSExpProjectDetail"
          elif  sheetCode=="FT-TRD.TX.Prj.CoSPExpProject"
        info.funcid = "FT-TRD.TX.Prj.CoSPExpProjectDetail"
        end if
        urldefvalues = dataSet.get("urldefvalues")
        if  urldefvalues!=null
        for jskey in urldefvalues.keySet()
        info.pm.put("InitValue.ft_trd_prj."+jskey,urldefvalues.get(jskey))
        end for
        end if
        info.pm.put("AutoRefresh",1)
        info.pm.put("InitValue.prjicode",prjicode)
        return info
        ]]></TACBlock>
        <Remark><![CDATA[
		合同审批单穿透定义
        ]]></Remark>
    </Details>
</ViewDetails>

```

注：
1.该配置用于单据入口双击打开详情页,其中：info.funcid = "FT-TRD.TX.Prj.SeSOImpProjectDetail" 指定打开详情页
1.双击穿透功能需要入口界面系统功能添加单据入口监听服务,可参考: **入口系统功能**

#### 界面属性控制

1. 界面属性控制与界面定义是一一对应的，主要用于界面以及按钮相关控制
1. 详情页界面定义 UI工程下src/main/resources/cfg/ui/res/FT-TRD/TX/Prj/SeSOImpProjectDetail.xml
1. 详情页界面属性控制配置 在UI工程下创建src/main/resources/cfg/ui/optctrl/FT-TRD/TX/Prj/SeSOImpProjectDetail.xml
1. 配置说明【[界面属性控制](help.html?helpFile=help/SN-CMC/OptCtrl/OptCtrl.md)】
1. 参考配置

```
<?xml version="1.0" encoding="UTF-8"?>
<UIOptCtrl xmlns="http://www.snsoft.com.cn/schema/OptsCtrl" xsi:schemaLocation="http://www.snsoft.com.cn/schema/OptsCtrl http://www.snsoft.com.cn/schema/OptsCtrl.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

	<!-- 按钮控制 【权限】 -->
	<UIOpts>
		<uiname>ft_trd_prj</uiname>
		<button>save</button>
		<sheetcode>FT-TRD.TX.Prj.SeSOImpProject</sheetcode>
		<dataOpids>C</dataOpids>
		<ctrlFields>disabled</ctrlFields>
		<setValue>false</setValue>
	</UIOpts>
	<!-- 版本修改/取消版本修改按钮控制 -->
	<UIOpts>
		<uiname>ft_trd_prj</uiname>
		<button>vbBackup</button>
		<sheetcode>FT-TRD.TX.Prj.SeSOImpProject</sheetcode>
		<dataOpids>C</dataOpids>
		<ctrlFields>hidden</ctrlFields>
		<setValue>false</setValue>
		<UIVals>
			<uiname>ft_trd_prj</uiname>
			<colname>status</colname>
			<sqltype>12</sqltype>
			<matchValue>70</matchValue>
		</UIVals>
		<UIVals>
			<uiname>ft_trd_prj</uiname>
			<colname>nstype</colname>
			<sqltype>12</sqltype>
			<matchMode>0</matchMode>
			<matchValue>10</matchValue>
		</UIVals>
		<UIVals>
			<uiname>ft_trd_prj</uiname>
			<colname>vsnflag</colname>
			<sqltype>4</sqltype>
			<matchValue>0</matchValue>
		</UIVals>
	</UIOpts>
	<UIOpts>
		<uiname>busielemg_340</uiname>
		 <colname>operate</colname>
        <operateid>operate_edit</operateid>
		<sheetcode>FT-TRD.TX.Prj.SeSOImpProject</sheetcode>
		<dataOpids>C</dataOpids>
		<ctrlFields>disabled</ctrlFields>
		<setValue>false</setValue>
		<UIVals>
			<uiname>ft_trd_prj</uiname>
			<colname>status</colname>
			<sqltype>12</sqltype>
			<matchMode>5</matchMode>
			<matchValue>20</matchValue>
		</UIVals>
	</UIOpts>
	
    <!-- 主表主要字段 -->
	<UIOpts>
		<uiname>ft_trd_prj</uiname>
		<colname>wcode,bcode,corpbcode,costbcode</colname>
		<ctrlFields>readOnly</ctrlFields>
		<setValue>true</setValue>
		<UIVals>
			<uiname>ft_trd_prj</uiname>
			<colname>hisflag</colname>
			<sqltype>4</sqltype>
			<matchValue>1</matchValue>
		</UIVals>
	</UIOpts>
	
	<UIOpts>
		<uiname>ft_trd_prj</uiname>
		<colname>prjcode</colname>
		<ctrlFields>readOnly</ctrlFields>
		<setValue>true</setValue>
		<UIVals>
			<uiname>ft_trd_prj</uiname>
			<colname>prjcode</colname>
			<sqltype>12</sqltype>
			<matchValType>snsoft.ft.trd.comm.vnbk.TrdVBSheetColMatchValue</matchValType>
		</UIVals>
	</UIOpts>
    <!-- 客户 -->
	<UIOpts>
		<uiname>ft_trd_prj_bas</uiname>
		<colname>salccode</colname>
		<ctrlFields>readOnly</ctrlFields>
		<setValue>true</setValue>
		<UIVals>
			<uiname>ft_trd_prj</uiname>
			<colname>extcstype</colname>
			<sqltype>12</sqltype>
			<matchValue>10</matchValue>
		</UIVals>
		<UIVals>
			<uiname>ft_trd_prj</uiname>
			<colname>hisflag</colname>
			<sqltype>4</sqltype>
			<matchValue>1</matchValue>
		</UIVals>
	</UIOpts>
	<!-- 销售商品子表 -->
	<UIOpts>
		<uiname>busielemg_340</uiname>
		<ctrlFields>delable,addable</ctrlFields>
		<setValue>false</setValue>
		<UIVals>
			<uiname>ft_trd_prj</uiname>
			<colname>extcstype</colname>
			<sqltype>12</sqltype>
			<matchValue>10</matchValue>
		</UIVals>
	</UIOpts>
	<UIOpts>
		<uiname>busielemg_340</uiname>
		<ctrlFields>editable</ctrlFields>
		<setValue>false</setValue>
		<UIVals grp="0">
			<uiname>busielemg_340</uiname>
			<colname>hisflag</colname>
			<sqltype>4</sqltype>
			<matchValue>1</matchValue>
		</UIVals>
		<UIVals grp="1">
			<uiname>ft_trd_prj</uiname>
			<colname>extcstype</colname>
			<sqltype>12</sqltype>
			<matchValue>10</matchValue>
		</UIVals>
	</UIOpts>
    
     <UIOpts>
		<uiname>busielemg_340</uiname>
		<colname>natupric,natscy</colname>
		<ctrlFields>readOnly</ctrlFields>
		<setValue>true</setValue>
		<UIVals>
			<uiname>busielemg_340</uiname>
			<colname>prictype</colname>
			<sqltype>12</sqltype>
			<matchValue>10</matchValue>
		</UIVals>
		</UIOpts>
		<UIOpts>
		<uiname>busielemg_340</uiname>
		<colname>upric,fcy</colname>
		<ctrlFields>readOnly</ctrlFields>
		<setValue>true</setValue>
		<UIVals>
			<uiname>busielemg_340</uiname>
			<colname>prictype</colname>
			<sqltype>12</sqltype>
			<matchValue>20</matchValue>
		</UIVals>
	</UIOpts> 
</UIOptCtrl>
```

注：
1.界面属性控制 有数据权限(dataOpids) 和 按钮权限(btnOpids) 2种情况
  其中数据权限是根据查询出来的数据来判断满足条件，控制显示或隐藏按钮
  其中按钮权限是根据用户是否满足操作权限一说，控制显示或隐藏按钮
  通俗说法：一个是数据查询出来后才去设置按钮显示隐藏,一个是界面打开以后就判断了权限设置了显示隐藏
  添加界面按钮相关属性控制，需要结合具体业务判断使用哪种情况
2.UIVals 节点支持 grp属性，默认一致表示并且逻辑 ，不同分组表示或逻辑

#### 菜单定义

1. 在UI工程下创建src/main/resources/cfg/menu/Menu系统号.xml
1. 配置说明【[菜单定义](help.html?helpFile=help/SN-CMC/Menu/DevMenu.md)】
1. 参考配置 

```
<?xml version="1.0" encoding="UTF-8"?>
<menumodel id="FT-TRD" title="35号文及外综系统" xmlns="http://www.snsoft.com.cn/schema/Menu" xsi:schemaLocation="http://www.snsoft.com.cn/schema/Menu http://www.snsoft.com.cn/schema/Menu.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<menu id="FT-TRD" title="贸易管理">

        <menu title="自营进口" >
                <menu title="审批单">
                    <menu cmd="ui:FT-TRD.TX.Prj.SePMImpProjectEntry" cmdid="FT-TRD.TX.Prj.SePMImpProjectEntry" title="@UI?自营进口合同审批单-入口" author="石宏宇" flags="0x080"/>
                    <menu cmd="ui:FT-TRD.TX.Prj.SeSOImpProjectEntry" cmdid="FT-TRD.TX.Prj.SeSOImpProjectEntry" title="@UI?自营进口敞口销售合同审批单-入口" author="石宏宇" flags="0x080"/>
                </menu>
                 
         </menu>

	</menu>
</menumodel>
```

注：
1.菜单标题需要使用@UI 支持多语言



#### 单据复制

1. 在UI工程下创建src/main/resources/cfg/res/sheetcopy/FT-TRD.TX.Prj.SeSOImpProject.xml
1. 配置说明【[单据复制](help.html?helpFile=help/SN-PLAT/Sheet/SheetCopy.md)】
1. 参考配置

```
<?xml version="1.0" encoding="UTF-8"?>
<SheetCopyDef  copyflags="1"  xmlns="http://www.snsoft.com.cn/schema/SheetCopyDef" xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetCopyDef http://www.snsoft.com.cn/schema/SheetCopyDef.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Title>${}</Title>
   <Remark><![CDATA[
	业务说明：自营进口敞口销售合同审批单
			1、负责人：付壮志
			2、生成草拟单据
			3、附件图片数据进行复制
	]]></Remark>
    
    <Listeners><![CDATA[
    	#FT-COMM.FTOutCodeSheetCopyListener?[{}]
        #FT-TRD.TX.ProjcetSalSheetCopyListener?[{'dealtable':{'ft_trd_prj_extse':{'touchCols':'fcode'},'ft_trd_prj_seg':{'isHGattr':false}}}]
    ]]></Listeners>
    <ForceCopyColumns><![CDATA[
    ]]></ForceCopyColumns>
     <TacFormula><![CDATA[
    proc beforeSave(event)
    	oldMain = event.getOldMain()
		if "30".compareTo(oldMain.getStatus())>0 || !"10".equals(oldMain.getNstype())
		throw new snsoft.commons.util.BaseException("FT-TRD.E0000000021",snsoft.commons.util.ExceptionUtils.getErrTextByModuleCode("FT-TRD", "FT-TRD.E0000000021"))
		end if
	end proc
    ]]></TacFormula>
    <Details>
        <NotCopyTableName>ft_trd_prj</NotCopyTableName>
        <NotCopyColumnName>prjicode</NotCopyColumnName>
    </Details>
     <Details>
        <NotCopyTableName>ft_trd_prj</NotCopyTableName>
        <NotCopyColumnName>extcstype</NotCopyColumnName>
    </Details> 
    <Details>
        <NotCopyTableName>ft_trd_prj</NotCopyTableName>
        <NotCopyColumnName>status</NotCopyColumnName>
    </Details>
     <Details>
        <NotCopyTableName>ft_trd_prj</NotCopyTableName>
        <NotCopyColumnName>wfcode</NotCopyColumnName>
    </Details>
    <Details>
        <NotCopyTableName>ft_trd_prj</NotCopyTableName>
        <NotCopyColumnName>wfuid</NotCopyColumnName>
    </Details>
    <Details>
        <NotCopyTableName>ft_trd_prj_extse</NotCopyTableName>
        <NotCopyColumnName>signdate</NotCopyColumnName>
    </Details>
    <Details>
        <NotCopyTableName>ft_trd_prj_extse</NotCopyTableName>
        <NotCopyColumnName>outordcode</NotCopyColumnName>
    </Details>
    <Details>
        <NotCopyTableName>ft_trd_prj</NotCopyTableName>
        <NotCopyColumnName>submitdate</NotCopyColumnName>
    </Details>
     <Details>
        <NotCopyTableName>ft_trd_prj</NotCopyTableName>
        <NotCopyColumnName>performdate</NotCopyColumnName>
    </Details>
    <Details>
        <NotCopyTableName>ft_trd_prj</NotCopyTableName>
        <NotCopyColumnName>ratifydate</NotCopyColumnName>
    </Details>
     <Details>
        <NotCopyTableName>ft_trd_prj</NotCopyTableName>
        <NotCopyColumnName>curratifydate</NotCopyColumnName>
    </Details>
     <Details>
        <NotCopyTableName>ft_trd_prj</NotCopyTableName>
        <NotCopyColumnName>vsn</NotCopyColumnName>
    </Details>
     <Details>
        <NotCopyTableName>ft_trd_prj</NotCopyTableName>
        <NotCopyColumnName>vsnflag</NotCopyColumnName>
    </Details>
    
     <Details>
        <NotCopyTableName>ft_trd_prj_extse</NotCopyTableName>
        <NotCopyColumnName>hisflag</NotCopyColumnName>
    </Details>
    
    <Details>
        <NotCopyTableName>ft_trd_prj_se_sett</NotCopyTableName>
        <NotCopyColumnName>hisflag</NotCopyColumnName>
    </Details>
    <Details>
        <NotCopyTableName>ft_trd_prj_seg</NotCopyTableName>
        <NotCopyColumnName>hisflag</NotCopyColumnName>
    </Details>
    <Details>
        <NotCopyTableName>ft_trd_prj</NotCopyTableName>
        <NotCopyColumnName>pftcode</NotCopyColumnName>
    </Details>
    <Details>
        <NotCopyTableName>ft_trd_prj_extse</NotCopyTableName>
        <NotCopyColumnName>deliverydate</NotCopyColumnName>
    </Details>
    <Details>
        <NotCopyTableName>ft_trd_prj_seg</NotCopyTableName>
        <NotCopyColumnName>deliverydate</NotCopyColumnName>
    </Details>
    
      <Details>
        <NotCopyTableName>ft_trd_prj_extse</NotCopyTableName>
        <NotCopyColumnName>prjicode</NotCopyColumnName>
    </Details>
    <Details>
        <NotCopyTableName>ft_trd_prj_seg</NotCopyTableName>
        <NotCopyColumnName>segicode</NotCopyColumnName>
    </Details>
    <Details>
        <NotCopyTableName>ft_trd_prj_seg</NotCopyTableName>
        <NotCopyColumnName>prjicode</NotCopyColumnName>
    </Details> 
    <Details>
        <NotCopyTableName>ft_trd_prj_se_sett</NotCopyTableName>
        <NotCopyColumnName>sesicode</NotCopyColumnName>
    </Details>
     <Details>
        <NotCopyTableName>ft_trd_prj_se_sett</NotCopyTableName>
        <NotCopyColumnName>prjicode</NotCopyColumnName>
    </Details>
    <Details>
        <NotCopyTableName>ft_trd_prj</NotCopyTableName>
        <NotCopyColumnName>vprepare</NotCopyColumnName>
    </Details>
    <Details>
        <NotCopyTableName>ft_trd_prj</NotCopyTableName>
        <NotCopyColumnName>predate</NotCopyColumnName>
    </Details>
    <Details>
        <NotCopyTableName>ft_trd_prj</NotCopyTableName>
        <NotCopyColumnName>modifier</NotCopyColumnName>
        <CopyTableName>ft_trd_prj</CopyTableName>
        <CopyColumnName>vprepare</CopyColumnName>
    </Details>
    <Details>
        <NotCopyTableName>ft_trd_prj</NotCopyTableName>
        <NotCopyColumnName>modifydate</NotCopyColumnName>
        <CopyTableName>ft_trd_prj</CopyTableName>
        <CopyColumnName>predate</CopyColumnName>
    </Details>
    
</SheetCopyDef>

```

注：

#### 版本备份

1. 在UI工程下创建src/main/resources/cfg/res/vnbk/FT-TRD.TX.Prj.SeSOImpProject.xml
1. 需要在详情页UI系统功能添加相关监听,参考**详情页UI系统功能**
1. 配置说明【[版本备份](help.html?helpFile=help/SN-PLAT/Sheet/VersionBackup.md)】
1. 配置参考

```
<?xml version="1.0" encoding="UTF-8"?>
<VersionBackupDef xmlns="http://www.snsoft.com.cn/schema/VersionBackupDef" xsi:schemaLocation="http://www.snsoft.com.cn/schema/VersionBackupDef http://www.snsoft.com.cn/schema/VersionBackupDef.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<Title>${}</Title>
	<Listeners><![CDATA[
		#FT-TRD.COMM.VersionStatusCheckListener?[{}]
        #SN-PLAT.StatusVersionBackupListener
        #SN-APPR.ApprVersionBackupListener
        snsoft.ft.trd.comm.busiclassify.service.impl.BusiSheetVersionBackupListener.new?sheetCode=FT-TRD.TX.Prj.SeSOImpProject
        snsoft.ft.trd.tx.prj.bas.service.impl.ProjectVersionBackupListener.new?isHasLrp=false
        snsoft.plat.bas.sheet.doc.service.impl.SheetDocVersionBackupListener.new?attachCfg=ft_trd_prj:FT-TRD.TX.Prj.SeSOImpProject#ft_trd_prj_doc
        snsoft.ft.trd.comm.term.service.impl.SheetTermVersionBackupListener.new
        snsoft.ft.trd.comm.profit.service.impl.BusiSheetProfitVersionBackupListener.new?profitTblName=ft_trd_prj_pft
	]]></Listeners>
	
	<TacFormula><![CDATA[
	proc onBackup(event,main)
	if !"70".equals(main.getStatus())
	throw new snsoft.commons.util.BaseException("FT-TRD.E0000000048",snsoft.commons.util.ExceptionUtils.getErrTextByModuleCode("FT-TRD", "FT-TRD.E0000000048"))
	end if
	if !"10".equals(main.getNstype())
	throw new snsoft.commons.util.BaseException("FT-TRD.E0000000047",snsoft.commons.util.ExceptionUtils.getErrTextByModuleCode("FT-TRD", "FT-TRD.E0000000047"))
	end if
	end proc
	]]></TacFormula>
	<Remark><![CDATA[
	版本备份说明：自营进口敞口销售合同审批单版本备份
			1、负责人：付壮志
			2、生成状态11【修改】单据
			3、11【修改】的数据禁止使用删除按钮
	]]></Remark>
	<Details>
        <NoBackupTableName>ft_trd_prj</NoBackupTableName>
        <NoBackupColumnName>curratifydate</NoBackupColumnName>
    </Details>
    <Details>
        <NoBackupTableName>ft_trd_prj</NoBackupTableName>
        <NoBackupColumnName>wfcode</NoBackupColumnName>
    </Details>
    <Details>
        <NoBackupTableName>ft_trd_prj</NoBackupTableName>
        <NoBackupColumnName>wfuid</NoBackupColumnName>
    </Details>
</VersionBackupDef>

```

注；


#### 单据审批

1. 单据VO需实现ApprSheetVO
1. 入口和详情页部署审批按钮,参考入口和详情界面
1. 系统功能添加审批监听,参考**详情页UI系统功能**
1. 配置说明【[审批链开发手册](help.html?helpFile=/help/SN-APPR/APPR/APPR-Dev.md)】
1. 配置说明【[审批链定义](help.html?helpFile=/help/SN-APPR/APPR/APPR-Dev.md)】


#### 单据附件

1. 配置说明【[单据文档](help.html?helpFile=help/SN-PLAT/Sheet/SheetDoc.md)】

#### 业务配置

##### 单据创建方式

1. 在UI工程下创建src/main/resources/cfg/res/init/sheetcreatemode/S0001.xml
1. 配置说明【[单据创建方式配置使用说明文档](help.html?helpFile=help/FT-TRD/SheetCreateModeHelp.md)】
1. 配置参考

```
<?xml version="1.0" encoding="UTF-8"?>
<SheetCreateModeDef xmlns="http://www.snsoft.com.cn/schema/SheetCreateModeDef" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.snsoft.com.cn/schema/SheetCreateModeDef SheetCreateModeDef.xsd ">
  <scmcode>S0001</scmcode>
  <scmname>手动新建合作出口审批单</scmname>
  <scmname1>手动新建合作出口审批单1</scmname1>
  <scmname2>手动新建合作出口审批单2</scmname2>
  <status>70</status>
  <sheetcode>FT-TRD.TX.Prj.ConsExpProject</sheetcode>
  <nstype>10</nstype>
  <nodetype>10</nodetype>
  <srcsheetcode></srcsheetcode>
  <gentac></gentac>
  <gentiming></gentiming>
  <gensheetstatus></gensheetstatus>
  <cpuid></cpuid>
  <cpexpfilter></cpexpfilter>
  <cpexpgrp></cpexpgrp>
  <gencpid></gencpid>
  <srcstatusfrom></srcstatusfrom>
  <srcstatusto></srcstatusto>
  <remark>合作出口审批单手工新建</remark>
</SheetCreateModeDef>

```
注：
1.需要配合监听使用
 UI：snsoft.ft.trd.comm.busiclassify.ui.BusiSheetEntryUIListener
 Xjs：snsoft.ft.trd.comm.busiclassify.BusiSheetEntryListener

##### 业务分类定义

1. 在trdaexp-ui工程下创建src/main/resources/cfg/res/init/busiclassify/S0001.xml
1. 配置参考

```
<?xml version="1.0" encoding="UTF-8"?>

<BusiClassifyDef xmlns="http://www.snsoft.com.cn/schema/BusiClassifyDef" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.snsoft.com.cn/schema/BusiClassifyDef BusiClassifyDef.xsd ">
  <bcfcode>S0001</bcfcode>
  <bcfname>合作出口正式协议流程</bcfname>
  <status>70</status>
  <tradetype>10</tradetype>
  <trademode>01</trademode>
  <busimode>30</busimode>
  <psmode>10</psmode>
  <purmode>10</purmode>
  <echecktype>20</echecktype>
  <dftovershiprate>0</dftovershiprate>
  <dftshortshiprate></dftshortshiprate>
  <issettsps>Y</issettsps>
  <protextfilter></protextfilter>
  <dftbcm>10</dftbcm>
  <btnresid>title.F.btn.enewbtnS0001</btnresid>
  <remark>合作出口正式协议流程</remark>
  <BusiClassifyDefDetails>
    <bcfdicode>S0001001</bcfdicode>
    <pbcfdicode></pbcfdicode>
    <nodetype>10</nodetype>
    <bcfdname>合作出口项目</bcfdname>
    <bcfdname1>合作出口项目1</bcfdname1>
    <bcfdname2>合作出口项目2</bcfdname2>
    <bcfcode>S0001</bcfcode>
    <sortidx>10</sortidx>
    <path>S0001001</path>
    <sortpath>00000010</sortpath>
    <sheetcode>FT-TRD.TX.Prj.ConsExpProject</sheetcode>
    <scmcode>S0001</scmcode>
    <remark></remark>
    
  </BusiClassifyDefDetails>
  <BusiClassifyDefDetails>
    <bcfdicode>S0001002</bcfdicode>
    <pbcfdicode>S0001001</pbcfdicode>
    <nodetype>20</nodetype>
    <bcfdname>合作出口合同</bcfdname>
    <bcfdname1>合作出口合同1</bcfdname1>
    <bcfdname2>合作出口合同2</bcfdname2>
    <bcfcode>S0001</bcfcode>
    <sortidx>20</sortidx>
    <path>S0001001.S0001002</path>
    <sortpath>00000010.00000020</sortpath>
    <sheetcode>FT-TRD.TX.Ord.ConsExpSalOrd</sheetcode>
    <scmcode>S0002</scmcode>
    <remark></remark>
    <Attrs>
        <aname>20.iscslock</aname>
        <stdvalue>Y</stdvalue>
        <remark>改单是否冻结</remark>
    </Attrs>
    <Attrs>
        <aname>20.iscsckstatus</aname>
        <stdvalue>N</stdvalue>
        <remark>改单是否检查状态</remark>
    </Attrs>
  </BusiClassifyDefDetails>
  <BusiClassifyDefDetails>
    <bcfdicode>S0001003</bcfdicode>
    <pbcfdicode>S0001002</pbcfdicode>
    <nodetype>25</nodetype>
    <bcfdname>合作出口采购合同</bcfdname>
    <bcfdname1>合作出口采购合同1</bcfdname1>
    <bcfdname2>合作出口采购合同2</bcfdname2>
    <bcfcode>S0001</bcfcode>
    <sortidx>30</sortidx>
    <path>S0001001.S0001002.S0001003</path>
    <sortpath>00000010.00000020.00000030</sortpath>
    <sheetcode>FT-TRD.TX.Ord.ConsExpDomPurOrd</sheetcode>
    <scmcode>S0003</scmcode>
    <remark></remark>
    <Attrs>
        <aname>25.iscslock</aname>
        <stdvalue>Y</stdvalue>
        <remark>改单是否冻结</remark>
    </Attrs>
    <Attrs>
        <aname>25.iscsckstatus</aname>
        <stdvalue>N</stdvalue>
        <remark>改单是否检查状态</remark>
    </Attrs>
  </BusiClassifyDefDetails>
  <BusiClassifyDefDetails>
    <bcfdicode>S0001004</bcfdicode>
    <pbcfdicode>S0001002</pbcfdicode>
    <nodetype>30</nodetype>
    <bcfdname>合作出口发货单</bcfdname>
    <bcfdname1>合作出口发货单1</bcfdname1>
    <bcfdname2>合作出口发货单2</bcfdname2>
    <bcfcode>S0001</bcfcode>
    <sortidx>40</sortidx>
    <path>S0001001.S0001002.S0001004</path>
    <sortpath>00000010.00000020.00000040</sortpath>
    <sheetcode>FT-TRD.TX.Ship.ConsExpSalShip</sheetcode>
    <scmcode>S0004</scmcode>
    <extscmcodes>S0030</extscmcodes>
    <remark></remark>
    <Attrs>
        <aname>30.iscslock</aname>
        <stdvalue>Y</stdvalue>
        <remark>改单是否冻结</remark>
    </Attrs>
    <Attrs>
        <aname>30.iscsckstatus</aname>
        <stdvalue>Y</stdvalue>
        <remark>改单是否检查状态</remark>
    </Attrs>
  </BusiClassifyDefDetails>
  
  <!-- <BusiClassifyDefDetails>
    <bcfdicode>S0001005</bcfdicode>
    <pbcfdicode>S0001002</pbcfdicode>
    <nodetype>30</nodetype>
    <bcfdname>合作出口发货单</bcfdname>
    <bcfdname1>合作出口发货单1</bcfdname1>
    <bcfdname2>合作出口发货单2</bcfdname2>
    <bcfcode>S0001</bcfcode>
    <sortidx>50</sortidx>
    <path>S0001001.S0001002.S0001005</path>
    <sortpath>00000010.00000020.00000050</sortpath>
    <sheetcode>FT-TRD.TX.Ship.ConsExpSalShip</sheetcode>
    <scmcode>S0030</scmcode>
    <Attrs>
        <aname>30.iscslock</aname>
        <stdvalue>Y</stdvalue>
        <remark>改单是否冻结</remark>
    </Attrs>
    <Attrs>
        <aname>30.iscsckstatus</aname>
        <stdvalue>Y</stdvalue>
        <remark>改单是否检查状态</remark>
    </Attrs>
    <remark></remark>
  </BusiClassifyDefDetails> -->
  <BusiClassifyDefDetails>
    <bcfdicode>S0001006</bcfdicode>
    <pbcfdicode>S0001004</pbcfdicode>
    <nodetype>65</nodetype>
    <bcfdname>合作出口采购发票</bcfdname>
    <bcfdname1>合作出口采购发票1</bcfdname1>
    <bcfdname2>合作出口采购发票2</bcfdname2>
    <bcfcode>S0001</bcfcode>
    <sortidx>60</sortidx>
    <path>S0001001.S0001002.S0001004.S0001006</path>
    <sortpath>00000010.00000020.00000040.00000060</sortpath>
    <sheetcode>FT-TRD.Inv.ConsExpPurInv</sheetcode>
    <scmcode>S0005</scmcode>
    <remark></remark>
    <Attrs>
        <aname>65.iscslock</aname>
        <stdvalue>N</stdvalue>
        <remark>改单是否冻结</remark>
    </Attrs>
    <Attrs>
        <aname>65.iscsckstatus</aname>
        <stdvalue>Y</stdvalue>
        <remark>改单是否检查状态</remark>
    </Attrs>
  </BusiClassifyDefDetails>
  <!-- <BusiClassifyDefDetails>
    <bcfdicode>S0001007</bcfdicode>
    <pbcfdicode>S0001006</pbcfdicode>
    <nodetype>55</nodetype>
    <bcfdname>成本</bcfdname>
    <bcfdname1>成本1</bcfdname1>
    <bcfdname2>成本2</bcfdname2>
    <bcfcode>S0001</bcfcode>
    <sortidx>10</sortidx>
    <path>S0001001.S0001002.S0001004.S0001006.S0001007</path>
    <sortpath>00000010.00000020.00000040.00000060.00000010</sortpath>
    <sheetcode>FT-TRD.AO.Cost</sheetcode>
    <scmcode>S0022</scmcode>
    <remark></remark>
  </BusiClassifyDefDetails> -->
  <BusiClassifyDefDetails>
    <bcfdicode>S0001008</bcfdicode>
    <pbcfdicode>S0001004</pbcfdicode>
    <nodetype>70</nodetype>
    <bcfdname>往来</bcfdname>
    <bcfdname1>往来1</bcfdname1>
    <bcfdname2>往来2</bcfdname2>
    <bcfcode>S0001</bcfcode>
    <sortidx>70</sortidx>
    <path>S0001001.S0001002.S0001004.S0001008</path>
    <sortpath>00000010.00000020.00000040.00000070</sortpath>
    <sheetcode>FT-SET.Lrp.Lrpclient</sheetcode>
    <scmcode>S0023</scmcode>
    <remark></remark>
  </BusiClassifyDefDetails>
  
  <BusiClassifyDefDetails>
    <bcfdicode>S0001009</bcfdicode>
    <pbcfdicode>S0001006</pbcfdicode>
    <nodetype>70</nodetype>
    <bcfdname>往来</bcfdname>
    <bcfdname1>往来1</bcfdname1>
    <bcfdname2>往来2</bcfdname2>
    <bcfcode>S0001</bcfcode>
    <sortidx>20</sortidx>
    <path>S0001001.S0001002.S0001004.S0001006.S0001009</path>
    <sortpath>00000010.00000020.00000040.00000060.00000020</sortpath>
    <sheetcode>FT-SET.Lrp.Lrpclient</sheetcode>
    <scmcode>S0032</scmcode>
    <remark></remark>
  </BusiClassifyDefDetails>
  
  
  <BusiClassifyDefDetails>
    <bcfdicode>S0001010</bcfdicode>
    <pbcfdicode>S0001004</pbcfdicode>
    <nodetype>80</nodetype>
    <bcfdname>合作出口结算单A</bcfdname>
    <bcfdname1>合作出口结算单A1</bcfdname1>
    <bcfdname2>合作出口结算单A2</bcfdname2>
    <bcfcode>S0001</bcfcode>
    <sortidx>70</sortidx>
    <path>S0001001.S0001002.S0001004.S0001010</path>
    <sortpath>00000010.00000020.00000040.00000070</sortpath>
    <sheetcode>FT-TRD.Sett.ConsExpSettA</sheetcode>
    <scmcode>S0034</scmcode>
    <remark></remark>
    <Attrs>
        <aname>80.iscslock</aname>
        <stdvalue>N</stdvalue>
        <remark>改单是否冻结</remark>
    </Attrs>
    <Attrs>
        <aname>80.iscsckstatus</aname>
        <stdvalue>Y</stdvalue>
        <remark>改单是否检查状态</remark>
    </Attrs>
  </BusiClassifyDefDetails>
  
  <BusiClassifyDefDetails>
    <bcfdicode>S0001011</bcfdicode>
    <pbcfdicode>S0001004</pbcfdicode>
    <nodetype>80</nodetype>
    <bcfdname>合作出口结算单B</bcfdname>
    <bcfdname1>合作出口结算单B1</bcfdname1>
    <bcfdname2>合作出口结算单B2</bcfdname2>
    <bcfcode>S0001</bcfcode>
    <sortidx>80</sortidx>
    <path>S0001001.S0001002.S0001004.S0001011</path>
    <sortpath>00000010.00000020.00000040.00000080</sortpath>
    <sheetcode>FT-TRD.Sett.ConsExpSettB</sheetcode>
    <scmcode>S0035</scmcode>
    <remark></remark>
    <Attrs>
        <aname>80.iscslock</aname>
        <stdvalue>N</stdvalue>
        <remark>改单是否冻结</remark>
    </Attrs>
    <Attrs>
        <aname>80.iscsckstatus</aname>
        <stdvalue>Y</stdvalue>
        <remark>改单是否检查状态</remark>
    </Attrs>
  </BusiClassifyDefDetails>
  
  <BusiClassifyDefDetails>
    <bcfdicode>S0001012</bcfdicode>
    <pbcfdicode>S0001004</pbcfdicode>
    <nodetype>80</nodetype>
    <bcfdname>合作出口结算单C</bcfdname>
    <bcfdname1>合作出口结算单C1</bcfdname1>
    <bcfdname2>合作出口结算单C2</bcfdname2>
    <bcfcode>S0001</bcfcode>
    <sortidx>90</sortidx>
    <path>S0001001.S0001002.S0001004.S0001012</path>
    <sortpath>00000010.00000020.00000040.00000090</sortpath>
    <sheetcode>FT-TRD.Sett.ConsExpSettC</sheetcode>
    <scmcode>S0036</scmcode>
    <remark></remark>
    <Attrs>
        <aname>80.iscslock</aname>
        <stdvalue>N</stdvalue>
        <remark>改单是否冻结</remark>
    </Attrs>
    <Attrs>
        <aname>80.iscsckstatus</aname>
        <stdvalue>Y</stdvalue>
        <remark>改单是否检查状态</remark>
    </Attrs>
  </BusiClassifyDefDetails>
  
  
</BusiClassifyDef>

```

#### 测试类

1. 在IMPL工程test目录下创建对应的测试类
1. 配置说明【[测试介绍](help.html?helpFile=help/SN-HELP/Standard/DevTest/DevTest.md)】


### 单据性能

目的：优化单据界面加载性能，减少界面无用远程请求

操作：使用谷歌浏览器打开单据界面 F12进入开发人员调试界面  选择NetWork --> XHR  按键Ctrl+Shift+R 强制刷新单据界面 重新加载页面 检查网络请求

#### 效率关注点

* 单据入口
1. 打开
* 单据详情
1. 打开
1. 新建
1. 草拟
1. 生效
1. 存盘
1. 提交

#### 目标要求

* 请求次数
    1. 打开（草拟、新建、生效），不能大于2次；
    1. 存盘，不能大于1次；
    1. 提交，不能大于2次；
* 请求返回数据量的大小
    1. 不该返回的不要返回；
    1. 减少重复的返回值；
    1. 值为空的不要返回，例如：null，""；
    1. 返回JSON数组的，使用新的数据格式MapArray,减少key=value中的key占用空间；
* 服务端日志：看时间是否存在不在同一个数量级的监听；
    1. InitComponent中的监听要求在1毫秒内完成
    1. LoadData等要求如存在查询要求200毫秒内，如不存在不要超过100毫秒。
* 所有工作台、单据入口都要增加日期默认值当前日期倒推30天，并为该日期字段添加索引。（切记这个日期字段必须是一个散列的）
* 所有大数据量的码表并且会变化的（客商）需要使用增量模式缓存
* 所有配置表配置表的表号要大于20000，采用缓存的添加缓存标记的方式定义
* 所有关联列都要增加索引
* 主表附表模式的要把常用的字段放到主表不常用的放到附表


>[帮助中心效率开发](https://n.esnsoft.cn/N9Bhelp/help.html?helpFile=/help/SN-HELP/Standard/DevCode/PerformCode.md)

### 快速开发工具

#### 快速创建VO

1. 此功能的主要目的是开发生成表结构后快速创建VO
1. VO快速创建工具【[表结构定义-创建VO](uiinvoke/00/zh_CN/theme0/90.TBLDEF.html)】

#### 快速开发单据

1. 此功能的主要目的是开发快速创建规范的单据相关配置文件
1. 操作文档【[阅读](help.html?helpFile=help/SN-PLAT/Tools/CreateFiles.md)】
1. 功能位置【[单据配置创建工具](uiinvoke/00/zh_CN/theme0/SN-PLAT.CreateFiles.html)】

#### 快速开发界面

1. 此功能的主要目的是帮助开发快速开发标准的XML界面
1. 操作文档【[阅读](help.html?helpFile=help/SN-CMC/Tool/UICREATEXML/UICREATEXML.md)】
1. 功能位置【[界面定义创建工具](uiinvoke/00/zh_CN/theme0/90.UICREATEXML.html)】
