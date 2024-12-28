## 【组织默认值处理】

设计人：【郭鑫】      设计日期  ：【2019-06-17】    宣讲日期 ：【2019-06-17】

### 说明

组织相关字段默认值注解无法满足需求《FA_中电智云项目_需求设计方案_能力中心_公共描述（终版）》中对默认值的要求。
特此开发以下监听处理组织相关默认值；部门、人员、公司、核算组字段不再打默认值注解。

### 服务端

* 部门人员公司核算组关系验证

提交时验证部门人员公司核算组间关系是否正确；

如果需要设置核算组默认值添加参数：
hasCostCol=true
subTblName=子表表名
其他参数：
bcodeCol：默认bcode;
wcodeCol：默认wcode;
corpCol：默认corpbcode;
costCol：默认costbcode;

```xml
<Groups id="组织关系生效效验">
    <Functions>
        <Functype>SN-PLAT.SV</Functype>
        <Subtype>2</Subtype>
        <Funcimpl>
        <![CDATA[
        snsoft.ft.code.comm.service.impl.SheetBcodeCheckSVListener.new
        ]]>
        </Funcimpl>
        <Remark>组织关系生效效验</Remark>
    </Functions>
</Groups>
```
### UI层

#### UI监听

* 部门人员公司默认值

默认设置部门、人员、公司默认值；
单据号必配，需要根据权限查找有权限的部门人员；
如果需要设置核算组默认值添加参数：
hasCostCol=true
仅远程设置，不设置默认值
onlyRemoteable=true
无公司默认值：noCorpBcode=true
其他参数：
bcodeCol：默认bcode;
wcodeCol：默认wcode;
corpCol：默认corpbcode;
costCol：默认costbcode;

```xml
<Groups id="组织默认值处理">
    <Functions>
        <Functype>SN-PLAT.UI</Functype>
        <Subtype>2</Subtype>
        <Funcimpl>
        <![CDATA[
        snsoft.ft.code.comm.ui.SheetBcodeDefaultValueUIListener.new?tgtUINames=界面名称&sheetcode=单据号
        ]]>
        </Funcimpl>
        <Remark>部门、人员、公司、核算组默认值处理</Remark>
    </Functions>
</Groups>
```

#### JS监听

* 部门值改变公司值处理

```xml
<Groups id="部门值改变公司值处理">
    <Functions>
        <Functype>SN-PLAT.JS</Functype>
        <Subtype>2</Subtype>
        <Funcimpl>
        <![CDATA[
        #new snsoft.ft.code.comm.SheetCorpBcodeJSListener({})
        ]]>
        </Funcimpl>
        <Remark>部门值改变公司值处理</Remark>
    </Functions>
</Groups>
```

*  部门值改变核算组值处理

单据有核算组字段需要配置此监听，没这个字段就不要配了；

```xml
<Groups id="部门值改变核算组值处理">
    <Functions>
        <Functype>SN-PLAT.JS</Functype>
        <Subtype>2</Subtype>
        <Funcimpl>
        <![CDATA[
        #new snsoft.ft.code.comm.SheetCostBcodeJSListener({})
        ]]>
        </Funcimpl>
        <Remark>部门值改变核算组值处理</Remark>
    </Functions>
</Groups>
```

### 数据库添加字段核算组

```xml
<column name="costbcode" title="核算组" type="VARCHAR(SZBCODE)"/>
```

### VO添加字段

```java
	@Column
	@CodeTable(value = "FT-CODE.CostBcode")
	private String					costbcode;
	public String getCostbcode()
	{
		return costbcode;
	}

	public void setCostbcode(String costbcode)
	{
		this.costbcode = costbcode;
	}
```

### 界面

```xml
 <c name="costbcode" title="${RES.FT-TRD-PROT.costbcode?核算组}" width="${RES.FT-CODE.D.HR.CW.costbcode}" disableed="true" aidInputerBtn="true" codedata="#FT-CODE.CostBcode" showname="true"
            cmparams.sheetcode="FT-TRD.35.Prot.ExpProt" cmparams.opids="R,C" cmprops.pmFromPane="{fromBcode:'bcode',fromWcode:'wcode'}" cmparams.fromBtype="&quot;01&quot;" sqltype="12" hidden="true"/>
```
### 注意

1. 部门人员列需要配置属性
```xml
submitOnInput="true"
```
不配置选择部门不立即提交会导致点击其他单元格公司、核算组才会变值
1. 组织类型配置
```xml
cmparams.fromBtype="&quot;01&quot;"
```
不要配置 cmparams.fromBtype="01"，这个会转换成int类型 1

1. 核算组字段所在表配置客户端属性，支持动态显示
```xml
layoutOnClient="true"
```

1. 列数配置
添加核算组后原来默认的4或者配置的4不够了，各自调下
```xml
cellcols="5"
```




