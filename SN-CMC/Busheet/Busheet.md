## 单据功能

### 单据定义	

|日期|作者|备注|
|------|------|------|
|2018-09-29|郭鑫|初稿|
|2018-10-23|冀小雷|修改|

#### 功能说明

1. 单据定义功能简化，仅定义表名、内外码、对应VO类、获取单据数据bean及说明文字。不包含其他任何功能。
1. 单据定义通过与产品组件建立关系来确定该单据拥有的具体功能。如：单据与编码规则关系、单据与权限关系。
1. 单据定义说明文字需要说明单据定义的权限编码，是否启用审批等涉及运维人员配置的功能；供运维人员配置。

#### 单据定义配置

单据定义配置分为文件配置和数据库配置，数据库配置优先级大于文件配置；在单据定义界面支持文件配置一键同步到数据库。

```
<?xml version="1.0" encoding="UTF-8"?>
<Sheet xmlns="http://www.snsoft.com.cn/schema/Sheet">
	<name>单据名称</name>
	<tblname>主表表名</tblname>
	<innerfld>主表内码字段</innerfld>
	<outerfld>主表外码字段</outerfld>
	<acsbean>当前单据访问服务</acsbean>
	<voclass>主表对应的VO类，必须继承自【snsoft.bas.sheet.busi.entity.BusiVO】</voclass>
	<remark><![CDATA[
	单据说明：
	1、启用单据编码规则；
	2、启用单据权限：
	（1）权限字段：xx；
	（2）操作编码：R，C；
	3、启用审批流程；
	（1）需要提供审批流程主表存取组件，必须实现{snsoft.approval.service.BusiSheetAccessService}；
	（2）主表VO必须实现{snsoft.approval.entity.BusiSheetVO}供审批链取数；
	4、启用部门人员：
		即主表有部门人员[bcode+wcode]字段；
	5、单据的其他注意事项（主要供实施参考）；
	6、负责人：冀小雷；
	]]></remark>
</Sheet>
```

#### 单据定义相关规范

1. 存放路径：res/sheet/*.xml；
1. 相关命名规范：
	单据号定义："[sysid].*"
	单据定义文件名称："[sheetcode].xml"

#### 相关程序

* 服务端获取单据定义对象BusiObject

```java
SheetService sheetService = SpringBeanUtils.getBeanByName("SN-Busi.SheetService");
BusiObject busiObj = sheetService.getBusiObject(sheetCode);
```		

* VO类单据默认值

```java
	@SheetInfo("单据号")
	public class OrderVO extends BusiVO 
	{
	}
	
	//需要有@SheetInfo注解,可以可以继承BusiVO类,也可以自己写
	@Column
	@NotNull(message = "[单据号]不能为空")
	@CodeTable(value = "90.sheet", message = "单据[%1$s]不存在")
	@DefaultValue(processBean = SheetInfoDefaultValue.class)
	private String				sheetcode;
```

*  单据定义示例：

\snsoft90\snsoft_adk\snadk-help\help-ui\cfg\res\sheet\SN-HELP.order.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Sheet xmlns="http://www.snsoft.com.cn/schema/Sheet">
	<name>销售合同</name>
	<tblname>help_erp_order</tblname>
	<innerfld>ordicode</innerfld>
	<outerfld>ordcode</outerfld>
	<voclass>snsoft.help.sheet.order.vo.ERPOrder</voclass>
	<remark><![CDATA[
	销售合同：
	1、启用单据编码规则；
	2、启用单据权限；
	3、启用业务流程；
	（1）审批独立部署，配置审批链工作流配置：
		* 审批流程主表存取组件:sheetservice="SN-APPROVAL.MDBBusiSheetAccessService"
		* 审批链监听：wflisteners="snsoft.approval.ui.WFAloneListener.new"；
	（2）配置业务流程定义：help_erp.order	
		* 业务流程取数组件：SN-HELP.ERPOrderService
		* 取审批人方法：getPerformRequest
		* 提交至审批系统方法：performSubmit
		* 审批系统回调方法：afterKeep 
	4、启用部门人员：bcode+wcode；
	]]></remark>
</Sheet>
```

### 单据状态定义

#### 状态定义功能说明	

业务单据一般包含多种状态，如 10：草拟;30：待审;70:生效。但是实际使用过程中，不同的单据可能状态不一致，为了码表及配置统一，底层添加了统一的单据状态码表，不同单据依据单据号过滤

#### 状态定义配置

```
[
	{
		"code": "10",
		"name": "草拟"
	},
	{
		"code": "15",
		"name": "驳回"
	},
	{
		"code": "30",
		"name": "待审批"
	},
	{
		"code": "70",
		"name": "完结"
	}
]
```

#### 状态定义规范

* 状态定义存放路径：res/status/*.json
* 状态定义相关命名规：[sheetcode].json

#### 状态定义码表

* 对应码表：90.status

* 界面配置

```xml
<c name="status" title="状态" sqltype="12"  width="160" initval="10" 
	codedata="#90.status" showname="true" cmparams.sheetcode="单据号"
	aidInputerBtn="true" droplist="true"/>
```

### 单据权限关系

参见[单据权限关系](help.html?helpFile=help/SN-CMC/Limit/Datalimit.md#单据权限关系)

### 单据编码规则关系

参见[单据/编码规则关系](help.html?helpFile=help/SN-CMC/Accode/Accode.md#单据编码规则)