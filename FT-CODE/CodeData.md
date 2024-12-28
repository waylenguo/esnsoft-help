## 码表配置

### 组织相关

<table><colgroup><col width="100"><col width="250"><col width="250"><col width="50"></colgroup>
<thead><tr><th>码表</th><th>入口参数</th><th>列表页面</th><th>录入页面</th></tr></thead>
<tbody><tr><td>部门</td><td>
```<c name="bcode" title="部门" sqltype="12" width="120" codedata="#FT-CODE.Bcode" cmparams.sheetcode="单据号" cmparams.opids="权限操作码" disableed="true" showname="true" selectMiddle="true" aidInputerBtn="true"/>```</td><td>
```<c name="bcode" title="部门" sqltype="12" width="120" codedata="#FT-CODE.Bcode" cmparams.sheetcode="单据号" cmparams.opids="权限操作码" showname="true" />```
<br>部门按级次显示属性：uiprops.showLvFullnm="[n1,n2]" showfname="true" n>0：正向第N级；n=0：当前组织编码；n<0：向上N级；</td><td>
```<c name="bcode" title="部门" sqltype="12" width="120" codedata="#FT-CODE.BWcode" cmparams.sheetcode="单据号" cmparams.opids="权限操作码" cmparams.status="70" disableed="true" showname="true" aidInputerBtn="true"```
```uiprops.renderer="new snsoft.ft.code.bcode.codedata```
```.BWcodeNameRender({})"/>```
```显示部门-人员render：uiprops.renderer="new snsoft.ft.code.bcode.codedata.BWcodeNameRender```
```({uiname:'xx',cuicodeCol:'',bcodeCol:'',wcodeCol:''})"```
```部门人员字段不叫bcode、wcode;aiprops.copyMap="{部门字段:'bcode',人员字段:'wcode'}"```</td></tr><tr><td>人员</td><td>
```<c name="wcode" title="登记人" sqltype="12" width="120" codedata="#FT-CODE.Wcode" disableed="true" showname="true" aidInputerBtn="true"/>```</td><td>
```<c name="wcode" title="登记人" sqltype="12" hidden="true" codedata="#FT-CODE.Wcode" disableed="true" showname="true" />```</td><td>
```<c name="wcode" title="登记人" sqltype="12" width="120" codedata="#FT-CODE.Wcode" cmparams.status="70"  disableed="true" showname="true" aidInputerBtn="true"/>```</td></tr><tr><td>公司</td><td>
```<c name="corpbcode" title="公司" sqltype="12" width="120" codedata="#FT-CODE.CorpBcode" cmparams.sheetcode="单据号" cmparams.opids="权限操作码" disableed="true" showname="true" aidInputerBtn="true"/>```</td><td>
```<c name="corpbcode" title="公司" sqltype="12" width="120" codedata="#FT-CODE.CorpBcode" cmparams.sheetcode="单据号" cmparams.opids="权限操作码" showname="true" />```</td><td>
```<c name="corpbcode" title="公司" sqltype="12" width="100" codedata="#FT-CODE.CorpBcode" cmparams.status="70" cmparams.sheetcode="单据号" cmparams.opids="权限操作码" cmprops.pmFromPane="{fromBcode:'bcode'}" cmparams.fromBtype="&quot;01&quot;" disableed="true" showname="true" aidInputerBtn="true"/>```<br><br>可拷贝的公司信息：<br>aiprops="copyMap:{sfcode:'sfcode',fax:'fax'}<br>fax:传真<br>sfcode:本位币种<br>表示根据界面上的bcode值过滤公司：cmprops.pmFromPane="{fromBcode:'bcode'}"<br>根据权限内部门过滤公司：cmparams.fromBtype="&quot;01&quot;"</td></tr><tr><td>记账部门</td><td>
```<c name="keptbcode" title="记账部门" sqltype="12" width="120" codedata="#FT-CODE.KeptBcode" cmparams.sheetcode="单据号" cmparams.opids="权限操作码" disableed="true" showname="true" aidInputerBtn="true"/>```</td><td>
```<c name="keptbcode" title="记账部门" sqltype="12" width="120" codedata="#FT-CODE.KeptBcode" cmparams.sheetcode="单据号" cmparams.opids="权限操作码" disableed="true" showname="true" aidInputerBtn="true"/>=```</td><td>
```<c name="keptbcode" title="记账部门" sqltype="12" width="120" codedata="#FT-CODE.KeptBcode" cmparams.sheetcode="单据号" cmparams.opids="权限操作码" cmparams.status="70" cmprops.pmFromPane="{fromBcode:'bcode',fromBcode2:'corpbcode'}" cmparams.fromBtype="&quot;01&quot;" cmparams.fromBtype2="&quot;02&quot;" disableed="true" showname="true" aidInputerBtn="true"/>```</td></tr><tr><td>核算组</td><td>
```<c name="costbcode" title="核算组" sqltype="12" width="120" codedata="#FT-CODE.CostBcode" cmparams.sheetcode="单据号" cmparams.opids="权限操作码" disableed="true" showname="true" aidInputerBtn="true"/>```</td><td>
```<c name="costbcode" title="核算组" sqltype="12" width="120" codedata="#FT-CODE.CostBcode" cmparams.sheetcode="单据号" cmparams.opids="权限操作码" disableed="true" showname="true" aidInputerBtn="true"/>```</td><td>
```<c name="costbcode" title="核算组" sqltype="12" width="120" codedata="#FT-CODE.CostBcode" cmparams.sheetcode="单据号" cmparams.opids="权限操作码" cmparams.status="70" cmprops.pmFromPane="{fromBcode:'bcode',fromWcode:'wcode'}" cmparams.fromBtype="&quot;01&quot;" disableed="true" showname="true" aidInputerBtn="true"/>```</td></tr><tr><td>库存组织</td><td>
```<c name="stockbcode" title="库存组织" sqltype="12" width="120" codedata="#FT-CODE.StockBcode" cmparams.sheetcode="单据号" cmparams.opids="权限操作码" disableed="true" showname="true" aidInputerBtn="true"/>```</td><td>
```<c name="stockbcode" title="库存组织" sqltype="12" width="120" codedata="#FT-CODE.StockBcode" cmparams.sheetcode="单据号" cmparams.opids="权限操作码" disableed="true" showname="true" aidInputerBtn="true"/>```</td><td>
```<c name="stockbcode" title="库存组织" sqltype="12" width="120" codedata="#FT-CODE.StockBcode" cmparams.sheetcode="单据号" cmparams.opids="权限操作码" cmparams.status="70" disableed="true" showname="true" aidInputerBtn="true"/>```</td></tr><tr><td>部门人员</td><td>
```<c name="wcode" title="部门人员" sqltype="12" width="120" codedata="#FT-CODE.BWcode" showname="true" disableed="true" aidInputerBtn="true"/>```</td><td>
```<c name="wcode" title="部门人员" sqltype="12" width="120" codedata="#FT-CODE.BWcode" showname="true" disableed="true" aidInputerBtn="true"/>```</td><td>
```<c name="wcode" title="部门人员" sqltype="12" width="120" codedata="#FT-CODE.BWcode" cmparams.status="70" cmparams.sheetcode="单据号" cmparams.opids="权限操作码" showname="true" disableed="true" aidInputerBtn="true"/>```<br><br>可拷贝的人员信息：vfmobile:手机<br>可拷贝的用户信息：email:邮箱，拷贝用户信息需配置cmparams.hasUser="true"<br></td></tr>
</tbody></table>





### 客商

码表测试页面：http://127.0.0.1:1043/N9BsnTrd/uiinvoke/00/zh_CN/theme1/FT-TRD.Ccode.TestCCodeCodeData.html

#### 客商码表
* 码表名称：FT-CODE.Ccode
* 码表参数：status='70' and CuicodeFilter
* 已设置属性：立即刷新
*注意：所有客商码表的数据都是Ccode码表的子集，所以后台建议直接用该码表
* 配置：
1.普通

```
<c name="xx" title="xx" width="165" sqltype="12" codedata="#FT-CODE.Ccode" showname="true" disableed="true" aidInputerBtn="true"/> 
```

2.根据部门人员过滤（及该业务员是否拥有该客商的权限）
cuicode,bcode,wcode默认当前用户
可过滤类型

```
<c name="bcode1" title="部门" sqltype="12" width="120" codedata="#FT-CODE.BWcode" showname="true" disableed="true" aidInputerBtn="true"/>
<c name="wcode" title="人员"  sqltype="12" width="120" codedata="#FT-CODE.Wcode" showname="true" disableed="true"/>
<c name="cd7" title="部门人员过滤客商"  width="120" sqltype="12" codedata="#FT-CODE.CcodeBWcode" showname="true" disableed="true" aidInputerBtn="true" 
	cmprops.pmFromPane="{bcode:'bcode1',wcode:'wcode'}" newLine="true"/>
``` 

3.根据部门人员过滤境外客商

```
<c name="bcode1" title="部门" sqltype="12" width="120" codedata="#FT-CODE.BWcode" showname="true" disableed="true" aidInputerBtn="true"/>
<c name="wcode" title="人员"  sqltype="12" width="120" codedata="#FT-CODE.Wcode" showname="true" disableed="true"/>
<c name="cd8" title="部门人员过滤境外客商"  width="120" sqltype="12" codedata="#FT-CODE.CcodeBWcode" showname="true" disableed="true" aidInputerBtn="true" 
	cmprops.pmFromPane="{bcode:'bcode1',wcode:'wcode'}" cmparams.ccodearea="20"/> 
```

4.境外收货人
可传入参数cuicode和trustcuicode，trustcuicode不传入，默认当前商户，cuicode不传入不处理
拓展参数flag,目前之前'1','2'。1代表前端，为默认值，trustcuicode为空的时候默认当前cuicode，2代表后端，trustcuicode为空的时候，默认查所有trustcuicode不为空的数据

```
<c name="cd12" title="境外收货人" width="120" sqltype="12" codedata="#FT-CODE.TrustCcode" showname="true" disableed="true" aidInputerBtn="true"
	cmprops.pmFromPane="{cuicode:'cuicode',trustcuicode:'trustcuicode'}"/>
```

5.按照类型过滤客商

```
<c name="cd4" title="商户类型过滤10,20" width="120" sqltype="12" codedata="#FT-CODE.CcodeBWcode" showname="true" disableed="true" aidInputerBtn="true" 
	cmparams="ccodetypes:[10,12]" newLine="true"/>
<c name="cd5" title="商户类型过滤20" width="120" sqltype="12" codedata="#FT-CODE.CcodeBWcode" showname="true" disableed="true" aidInputerBtn="true" 
	cmparams="ccodetypes:[12]" newLine="true"/>
```

#### 录入页面并带出信息：
* 码表名称：FT-CODE.CcodeExt
* 码表参数：status='70' and CuicodeFilter
* 可配置的过滤参数【客商分类】【客商地域】
* 已设置属性：立即刷新
* 配置：
1.带出主表信息
	
```
<c name="cd2" title="带出主表信息"  width="120" sqltype="12" codedata="#FT-CODE.CcodeExt" showname="true" disableed="true" aidInputerBtn="true" 
	aiprops.copyMap="{'addr':'addr'}"/>
```

2.带出子表信息
	根据部门人员过滤，默认当前部门人员，可过滤客商分类

```
<c name="cd3" title="带出联系人子表信息" sqltype="12" codedata="#FT-CODE.CcodeLMExt" showname="true" disableed="true" aidInputerBtn="true" 
	newLine="true" aiprops.copyMap="{'addr':'addr'}" aiprops.initParasVales="{ccodetypes:'10'}" aiprops.dlgParam="{bcode:'bcode1',wcode:'wcode'}"/>
```
    	
#### 境外客商码表
* 码表名称：FT-CODE.Ccode
* 码表参数：status='70'
* 已设置属性：立即刷新
* 配置：

```
<c name="cd6" title="境外客商"  width="120" sqltype="12" codedata="#FT-CODE.Ccode" cmparams.SQLFILTER="ccodearea='20'" showname="true" disableed="true" aidInputerBtn="true" />
```

#### 境外收货人码表
* 码表名称：FT-TRD.TrustApply
* 码表参数：trustcuicode is not null and status = '70'
* 已设置属性：
* 配置：
1.普通配置

```
<c name="cd9" title="境外收货人" width="120" sqltype="12" codedata="#FT-TRD.TrustApply" showname="true" disableed="true" aidInputerBtn="true" newLine="true"/>
```

#### 客商银行账号码表
* 码表名称：FT-CODE.CcodeBankAccount
* 码表参数：ft_cd_cc_cc.status='70' and ft_cd_cc_ba.status='70' and CuicodeFilter
* 已设置属性：立即刷新
* 配置：

```
<c name="baicode2" title="内码" width="120" sqltype="12"/>
<c name="bankaccount" title="银行账号" width="120" sqltype="12" codedata="#FT-CODE.CcodeBankAccount" showname="true" disableed="true" aidInputerBtn="true"
	aiprops.initParasVales="{rdonlyColumns:'ccode,fcode'}" aiprops.dlgParam="{ccode:'baicode2'}"/>
<c name="baicode" title="内码" width="120" sqltype="12"/>
<c name="name" title="银行账号名称" width="120" sqltype="12"/>
```

### 商品

####海关计量单位

1. 码表名称：FT-CODE.HscodeUnit
1. 码表参数：无
1. 查询显示：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.HscodeUnit" showname="true" disableed="true" />`</pre> 
1. 录入页面：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.HscodeUnit" showname="true" aidInputerBtn="true" disableed="true" />`</pre> 

####海关计量单位

1. 码表名称：FT-CODE.TaxGcode
1. 码表参数：无
1. 查询显示：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.TaxGcode" showname="true" disableed="true" />`</pre> 
1. 录入页面：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.TaxGcode" showname="true" aidInputerBtn="true" disableed="true" />`</pre> 

####海关商品码表
1. 码表名称：FT-CODE.Hscode
1. 辅助录入属性：<pre>拷贝字段：`ignoreDftCopy:true/false(忽略默认拷贝，默认false)`</pre>
1. 默认拷贝字段：<pre>`declarelist:'declarelist'`</pre>
1. 查询显示：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.Hscode" showname="true" disableed="true" />`</pre> 
1. 录入页面：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.Hscode" showname="true" aidInputerBtn="true" disableed="true" />`</pre> 

####监管条件码表
1. 码表名称：FT-CODE.MonitorCondition
1. 码表参数：无
1. 查询显示：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.MonitorCondition" showname="true" disableed="true" />`</pre> 
1. 录入页面：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.MonitorCondition" showname="true" aidInputerBtn="true" disableed="true" />`</pre> 

####系统计量单位
1. 码表名称：FT-CODE.SysUnit
1. 码表参数：无
1. 查询显示：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.SysUnit" showname="true" disableed="true" />`</pre> 
1. 录入页面：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.SysUnit" showname="true" aidInputerBtn="true" disableed="true" />`</pre> 

####货号商品属性
1. 码表名称：FT-CODE.Gattrs
1. 码表参数：无
1. 查询显示：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.Gattrs" showname="true" disableed="true" />`</pre> 
1. 录入页面：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.Gattrs" showname="true" aidInputerBtn="true" disableed="true" />`</pre> 

####货号商品属性值
1. 码表名称：FT-CODE.GattrData
1. 码表参数：无
1. 查询显示：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.GattrData" showname="true" disableed="true" />`</pre> 
1. 录入页面：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.GattrData" showname="true" aidInputerBtn="true" disableed="true" />`</pre> 

####库存商品属性
1. 码表名称：FT-CODE.StockGattrs
1. 码表参数：无
1. 查询显示：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.StockGattrs" showname="true" disableed="true" />`</pre> 
1. 录入页面：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.StockGattrs" showname="true" aidInputerBtn="true" disableed="true" />`</pre> 

####库存商品属性值
1. 码表名称：FT-CODE.StockGattrData
1. 码表参数：无
1. 查询显示：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.StockGattrData" showname="true" disableed="true" />`</pre> 
1. 录入页面：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.StockGattrData" showname="true" aidInputerBtn="true" disableed="true" />`</pre> 

####货号商品属性组
1. 码表名称：FT-CODE.GattrGrps,FT-CODE.GattrGrps2(包含停用数据)
1. 码表参数：无
1. 查询显示：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.GattrGrps" showname="true" disableed="true" />`</pre> 
1. 录入页面：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.GattrGrps" showname="true" aidInputerBtn="true" disableed="true" />`</pre> 

####库存商品属性组
1. 码表名称：FT-CODE.StockGattrGrps,：FT-CODE.StockGattrGrps2(包含停用数据)
1. 码表参数：无
1. 查询显示：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.StockGattrGrps" showname="true" disableed="true" />`</pre> 
1. 录入页面：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.StockGattrGrps" showname="true" aidInputerBtn="true" disableed="true" />`</pre> 

####商品类目
1. 码表名称：FT-CODE.Gvcodes
1. 辅助录入属性：<pre>拷贝字段：`ignoreDftCopy:true/false(忽略默认拷贝，默认false)`</pre>
1. 默认拷贝字段：<pre>`qtyunit:'qtyunit',gtrgrpcode:'gtrgrpcode',sgtrgrpcode:'sgtrgrpcode',has_me:'has_me'`</pre>
1. 查询显示：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.Gvcodes" showname="true" disableed="true" />`</pre> 
1. 录入页面：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.Gvcodes" showname="true" aidInputerBtn="true" disableed="true" />`</pre> 

####明细商品
1. 码表名称：FT-CODE.Gcodes
1. 辅助录入属性（可选）：<pre>拷贝字段：`ignoreDftCopy:true/false(忽略默认拷贝，默认false),copyMap:{'brand':'品牌','specifidesc':'规格型号'}`</pre>
1. 默认拷贝字段：<pre>`gvcode:'gvcode',qtyunit:'qtyunit',qtcunit:'qtcunit',hscode:'hscode',declarelist:'declarelist',sgtrgrpcode:'sgtrgrpcode',has_me:'has_me',sgattr00:'sgattr00',sgattr01:'sgattr01',sgattr02:'sgattr02',sgattr03:'sgattr03',sgattr04:'sgattr04',sgattr05:'sgattr05',sgattr06:'sgattr06',sgattr07:'sgattr07',sgattr08:'sgattr08',sgattr09:'sgattr09'`</pre>
1. 查询显示：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.Gcodes" showname="true" disableed="true" />`</pre> 
1. 录入页面：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.Gcodes" showname="true" ignoreDftCopy="true" aidInputerBtn="true" disableed="true" />`</pre> 

####统计分类类别
1. 码表名称：FT-CODE.Stcodes
1. 码表参数：无
1. 查询显示：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.Stcodes" showname="true" disableed="true" />`</pre> 
1. 录入页面：<pre>`<c name="xx" title="xx" width="xx" sqltype="12" codedata="#FT-CODE.Stcodes" showname="true" aidInputerBtn="true" disableed="true" />`</pre> 


### 基础码表

参考样例界面：[CodeDataDemo](uiinvoke/00/zh_CN/theme0/FT-CODE.Bas.CodeDataDemo.html)
列表页多为显示作用，为只读界面，所以不加disableed属性

<table><colgroup><col width="150"><col width="250"><col width="250"><col width="100"></colgroup>
<thead><tr><th>码表</th><th>入口参数</th><th>列表页面</th><th>录入页面</th></tr></thead><tbody><tr><td>大洲</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Continent" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Continent" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Continent" showname="true" disableed="true" cmparams.SQLFILTER="status='70'" />```</td></tr><tr><td>国别地区</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Ncode" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Ncode" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Ncode" showname="true" disableed="true" cmparams.SQLFILTER="status='70'" />```</td></tr><tr><td>省</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Province" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Province" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Province" showname="true" disableed="true" />```
```可通过界面国别地区字段过滤，配置:cmprops="pmFromPane:{ncode:'ncode'}" cmparams="SQLFILTER:&quot;ncode='${ncode}'&quot;"```</td></tr><tr><td>省（多列）</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.CtryProv" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.CtryProv" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.CtryProv" showname="true" disableed="true" />```
```可通过界面国别地区字段过滤，配置:cmprops="pmFromPane:{ncode:'ncode'}" cmparams="SQLFILTER:&quot;ncode='${ncode}'&quot;"```</td></tr><tr><td>市及地区</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.SNCity" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.SNCity" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.SNCity" showname="true" disableed="true" />```
```可通过界面省字段过滤，配置:cmprops="pmFromPane:{provcode:'provcode'}" cmparams="SQLFILTER:&quot;provcode='${provcode}'&quot;"```</td></tr><tr><td>市及地区（显示名：省+市及地区）</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.ProvCity" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.ProvCity" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.ProvCity" showname="true" disableed="true" />```
```可通过界面省字段过滤，配置:cmprops="pmFromPane:{provcode:'provcode'}" cmparams="SQLFILTER:&quot;provcode='${provcode}'&quot;"```</td></tr><tr><td>区</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Area" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Area" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Area" showname="true" disableed="true" />```
```可通过界面市及地区字段过滤，配置:cmprops="pmFromPane:{sncitycode:'sncitycode'}" cmparams="SQLFILTER:&quot;sncitycode='${sncitycode}'&quot;"```</td></tr><tr><td>区（显示名：省+市及地区+区）</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.PCArea" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.PCArea" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.PCArea" showname="true" disableed="true" />```
```可通过界面市及地区字段过滤，配置:cmprops="pmFromPane:{sncitycode:'sncitycode'}" cmparams="SQLFILTER:&quot;sncitycode='${sncitycode}'&quot;"```</td></tr><tr><td>街道</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Street" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Street" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Street" showname="true" disableed="true" />```
```可通过界面区字段过滤，配置:cmprops="pmFromPane:{areacode:'areacode'}" cmparams="SQLFILTER:&quot;areacode='${areacode}'&quot;"```</td></tr><tr><td>港口</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Port" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Port" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Port" showname="true" disableed="true" />```</td></tr><tr><td>报关口岸</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.DecPort" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.DecPort" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.DecPort" showname="true" disableed="true" />```</td></tr><tr><td>海关关区</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Custom" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Custom" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Custom" showname="true" disableed="true" />```</td></tr><tr><td>境内货源地</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.District" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.District" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.District" showname="true" disableed="true" />```</td></tr><tr><td>币种</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Fcode" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Fcode" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Fcode" showname="true" disableed="true" />```</td></tr><tr><td>汇率类型</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.FrateType" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.FrateType" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.FrateType" showname="true" disableed="true" />```</td></tr><tr><td>外部银行</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Bank" showname="true" disableed="true" selectMiddle="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Bank" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Bank" showname="true" disableed="true" />```
```选择总行配置： cmparams.SQLFILTER="banktype='10'",选择外部银行配置： cmparams.SQLFILTER="bankio='O'"```</td></tr><tr><td>外部银行账号</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.BankAccount" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.BankAccount" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.BankAccount" showname="true" disableed="true" />```</td></tr><tr><td>银行联行号</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Cnaps" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Cnaps" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Cnaps" showname="true" disableed="true" />```</td></tr><tr><td>仓库</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Scode" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Scode" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.Scode" showname="true" disableed="true" />```</td></tr><tr><td>收付款方式</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.RPMode" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.RPMode" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#FT-CODE.RPMode" showname="true" disableed="true" />```
```可按照境内外，支付类型，系统收付款方式进行过滤：cmparams.SQLFILTER="domabr='' and paytype='' and rptype=''"```</td></tr><tr><td>业务员类型</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#SN-PLAT.RBWUType" showname="true" disableed="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#SN-PLAT.RBWUType" showname="true" />```</td><td>
```<c name="xx" title="xx" sqltype="12" width="xx" codedata="#SN-PLAT.RBWUType" showname="true" disableed="true" />```</td></tr></tbody></table>

