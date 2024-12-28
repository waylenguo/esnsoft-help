## 固定资产系统设计文档

设计记录：

|设计人|设计日期|宣讲日期|
|------|------|------|
|吴小海|2020-08-19|&nbsp;|

评审记录：

|评审日期|评审人|
|------|------|
|2020-08-19|&nbsp;|

变更记录：

|变更日期|变更说明|
|------|------|
|2020-08-19|&nbsp;|

### 总体设计

* 【功能说明】
> 此文档为固定资产系统设计文档

* 【主要结论】
> 固定资产系统 api、ui、xjs工程 代码位置放在在ft-sna总账系统下，不在单独新增工程 
> 总账系统包名sna下新建fa目录存放固定资产系统相关代码 例：snsoft.ft.sna.fa.*
> 新增固定资产系统impl工程 ft-fa-impl 依赖 fa-sna-api
* 【业务流程图】

### 工程及包结构

* 【工程规划】

<pre>
ft-sna[总账系统]
	|- comm[通用功能]
	|- config[系统配置]
	|- sna-api[总账系统API]
	|- sna-ui[总账系统UI]
	|- sna-xjs[总账系统XJS]
	|- fa-impl[固定资产系统Impl]
</pre>


* 【包结构及类规范】

```
包结构
	Module: 模块
	snsoft.ft.sna.fa.[Module].model
	snsoft.ft.sna.fa.[Module].service
	snsoft.ft.sna.fa.[Module].service.impl
	
类名规范：
	Fa+[Module]+Service：接口类
    Fa+[Module]+UIService：接口类
	Fa+[Module]+VO：VO

```

### 模块设计

#### 基础配置

##### 项目维护
###### 单据定义

* 单据号：FT-FA.CardDef
* 主表：fa_carddef
* 主表VO：snsoft.ft.sna.fa.carddef.vo.FaCardDef

  
###### 权限结构

权限定义
  * 编号：FT-FA.CardDef
  * 操作：
    C：操作；
  * 权限字段：
	cuicode：商户


###### 界面定义
   FT-FA.Config.CardDef

###### 实体类

* 【实体类】

|包名类名|名称|类型|主要功能|
|------|------|------|------|
|snsoft.ft.sna.fa.carddef.vo.FaCardDef|项目维护|VO类|项目维护定义|

* 项目维护定义 fa_carddef

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|------|
|fldname|项目名|VARCHAR|SZIBILL|是|||||
|cuicode|商户编码|VARCHAR|SZCUICODE|是|||||
|sortidx|序号|INTEGER|||||||
|flddisplayname|汉字含义|VARCHAR|SZNAME||||||
|fldtype|类型|INTEGER|||||||
|flddecimal|小数|INTEGER|||||||
|fldwidth|宽度|INTEGER|||||||
|fldchange|可变更|SMALLINT|||||||
|codedata|码表-定义|VARCHAR|64||||||
|cmprops|码表-属性|VARCHAR|64||||||
|cmparams|码表-参数|VARCHAR|64||||||
|options1|码表显示|INTEGER|||||||
|options2|码表录入方式|INTEGER|||||||
|aidinputer|辅助录入-定义|VARCHAR|64||||||
|aiprops|辅助录入-属性|VARCHAR|64||||||
|vprepare|创建人|VARCHAR|SZUCODE||||||
|predate|创建日期|DATE|||||||
|modifier|修改人|VARCHAR|SZUCODE||||||
|modifydate|修改日期|DATE|||||||

###### 接口设计

|包名类名|名称|主要功能|
|------|------|------|
|snsoft.ft.sna.fa.carddef.service.FaCardDefService|项目维护服务|项目维护服务|
|snsoft.ft.sna.fa.carddef.service.FaCardDefUIService|项目维护UI服务|主要用于权限合法性校验|

|接口名称|方法名|输入说明|输出说明|方法说明|
|------|------|------|------|------|
|FaCardDefService|quryList|查询参数|查询结果|项目维护服务列表查询|
|FaCardDefService|quryCuiDef||查询结果|查询当前商户项目维护定义(需缓存)|
|FaCardDefService|save|存盘对象|存盘结果|项目维护服务存盘|
|FaCardDefUIService|quryListUI|查询参数|查询结果|项目维护服务列表查询|
|FaCardDefUIService|saveUI|存盘对象|存盘结果|项目维护服务存盘|
|FaCardDefUIService|appendFld|||追加字段(只追加扩展字段)|

##### 资产类别

###### 单据定义

* 单据号：FT-FA.Tcode
* 主表：fa_tcode
* 主表VO：snsoft.ft.sna.fa.tcode.vo.FaTcode

###### 权限结构

权限定义
  * 编号：FT-FA.Tcode
  * 操作：
    C：操作；
  * 权限字段：
	cuicode：商户

###### 界面定义
   FT-FA.Config.Tcode

###### 实体类

* 【实体类】

|包名类名|名称|类型|主要功能|
|------|------|------|------|
|snsoft.ft.sna.fa.tcode.vo.FaTcode|资产类别|VO类|资产类别|

* 资产类别 fa_tcode

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|------|
|fatcode|资产类型编号|VARCHAR|SZIBILL|是|||||
|cuicode|商户编码|VARCHAR|SZCUICODE|是|||||
|fatname|资产类型名称|VARCHAR|SZNAME||是||||
|fadcode|折旧方法编码|VARCHAR|SZNBILL||||||
|path|路径|VARCHAR|SZTEXT||||||
|pntcode|父节点|VARCHAR|SZNBILL||||||
|sortidx|序号|INTEGER|||||||
|sortpath|排序路径|VARCHAR|SZTEXT||||||
|lifemonth|使用年限(月)|SMALLINT|4||||||
|crate|净残值率|NUMERIC|RATINT.RATDEC||||||
|qtyunit|计量单位|VARCHAR|SZHUNIT||||||
|mainacode|折旧费用科目|VARCHAR|SZNBILL|||||折旧借方科目|
|oriacode|原值科目|VARCHAR|SZNBILL|||||资产借方科目|
|accacode|累计折旧科目|VARCHAR|SZNBILL|||||折旧贷方科目|
|cardtype|资产大类|VARCHAR|SZIBILL||||||
|curmontyn|新增当月计提折旧|VARCHAR|2||||||
|vprepare|创建人|VARCHAR|SZUCODE||||||
|predate|创建日期|DATE|||||||
|modifier|修改人|VARCHAR|SZUCODE||||||
|modifydate|修改日期|DATE|||||||
	
###### 接口设计

|包名类名|名称|主要功能|
|------|------|------|
|snsoft.ft.sna.fa.tcode.service.FaTcodeService|资产类别服务|资产类别服务|
|snsoft.ft.sna.fa.tcode.service.FaTcodeUIService|资产类别维护UI服务|主要用于权限合法性校验|

|接口名称|方法名|输入说明|输出说明|方法说明|
|------|------|------|------|------|
|FaTcodeService|queryList|查询参数|查询结果|资产类别列表查询|
|FaTcodeService|save|存盘对象|存盘结果|资产类别存盘|
|FaTcodeUIService|queryListUI|查询参数|查询结果|资产类别列表查询|
|FaTcodeUIService|saveUI|存盘对象|存盘结果|资产类别存盘|

###### 码表定义

编号： FT-SNA.Tcode 

##### 核算方式

###### 单据定义

* 单据号：FT-FA.Ocode
* 主表：fa_ocode
* 主表VO：snsoft.ft.sna.fa.ocode.vo.FaOcode

###### 权限结构

权限定义
  * 编号：FT-FA.Ocode
  * 操作：
    C：操作；
  * 权限字段：
	cuicode：商户

###### 界面定义
   FT-FA.Config.Ocode

###### 实体类

* 【实体类】

|包名类名|名称|类型|主要功能|
|------|------|------|------|
|snsoft.ft.sna.fa.ocode.vo.FaOcode|核算方式|VO类|核算方式|

* 核算方式 fa_ocode

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|------|
|faocode|核算方式编码|VARCHAR|SZNBILL|是|||||
|cuicode|商户|VARCHAR|SZCUICODE|是|||||
|faoname|核算方式名称|VARCHAR|SZNAME||是||||
|faoname1|核算方式名称1|VARCHAR|SZNAME||||||
|faoname2|核算方式名称2|VARCHAR|SZNAME||||||
|faoname3|核算方式名称3|VARCHAR|SZNAME||||||
|faoname4|核算方式名称4|VARCHAR|SZNAME||||||
|vprepare|创建人|VARCHAR|SZUCODE||||||
|predate|创建日期|DATE|||||||
|modifier|修改人|VARCHAR|SZUCODE||||||
|modifydate|修改日期|DATE|||||||

###### 接口设计

|包名类名|名称|主要功能|
|------|------|------|
|snsoft.ft.sna.fa.ocode.service.FaOcodeService|核算方式服务|核算方式服务|
|snsoft.ft.sna.fa.ocode.service.FaOcodeUIService|核算方式UI服务|主要用于权限合法性校验|

|接口名称|方法名|输入说明|输出说明|方法说明|
|------|------|------|------|------|
|FaOcodeService|quryList|查询参数|查询结果|核算方式服务列表查询|
|FaOcodeService|save|存盘对象|存盘结果|核算方式存盘服务|
|FaOcodeUIService|quryListUI|查询参数|查询结果|核算方式列表查询|
|FaOcodeUIService|saveUI|存盘对象|存盘结果|核算方式存盘服务|
|FaOcodeUIService|initData|||核算方式存初始化|

###### 码表定义

定义编号： FT-SNA.Ocode 

##### 折旧方法

###### 单据定义

* 单据号：FT-FA.Dcode
* 主表：fa_dcode
* 主表VO：snsoft.ft.sna.fa.dcode.vo.FaDcode

###### 权限结构

权限定义
  * 编号：FT-FA.Dcode
  * 操作：
    C：操作；
  * 权限字段：
	cuicode：商户

###### 界面定义
   FT-FA.Config.Dcode

###### 实体类

* 【实体类】

|包名类名|名称|类型|主要功能|
|------|------|------|------|
|snsoft.ft.sna.fa.dcode.vo.FaDcode|折旧方法|VO类|折旧方法|

* 核算方式 fa_dcode

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|------|
|fadcode|折旧方法编码|VARCHAR|SZNBILL|是|||||
|cuicode|商户|VARCHAR|SZCUICODE|是|||||
|fromsys|系统下发|SMALLINT|1||||||
|fadname|折旧方法名称|VARCHAR|SZNAME||是||||
|mdratetac|月折旧率公式|VARCHAR|SZTAC||||||
|tdeprtac|月折旧公式|VARCHAR|SZTAC||||||
|tyn|是否提折旧|SMALLINT|1|||||是：进行折旧计算|
|status|状态|VARCHAR|SZSTATUS||是||||
|vprepare|创建人|VARCHAR|SZUCODE||||||
|predate|创建日期|DATE|||||||
|modifier|修改人|VARCHAR|SZUCODE||||||
|modifydate|修改日期|DATE|||||||
	
###### 接口设计

|包名类名|名称|主要功能|
|------|------|------|
|snsoft.ft.sna.fa.dcode.service.FaDcodeService|折旧方法服务|折旧方法服务|
|snsoft.ft.sna.fa.dcode.service.FaDcodeUIService|折旧方法UI服务|主要用于权限合法性校验|

|接口名称|方法名|输入说明|输出说明|方法说明|
|------|------|------|------|------|
|FaDcodeService|queryList|查询参数|查询结果|折旧方法列表查询|
|FaDcodeService|save|存盘对象|存盘结果|折旧方法存盘服务|
|FaDcodeUIService|queryListUI|查询参数|查询结果|折旧方法列表查询|
|FaDcodeUIService|saveUI|存盘对象|存盘结果|折旧方法存盘服务|
|FaDcodeUIService|syncToDbUI|折旧编号,商户编号||折旧方法同步到数据库|

###### 码表定义

定义编号： FT-SNA.Dcode 

##### 使用状态

###### 单据定义

* 单据号：FT-FA.Scode
* 主表：fa_scode
* 主表VO：snsoft.ft.sna.fa.scode.vo.FaScode

###### 权限结构

权限定义
  * 编号：FT-FA.Scode
  * 操作：
    C：操作；
  * 权限字段：
	cuicode：商户

###### 界面定义
   FT-FA.Config.Scode

###### 实体类

* 【实体类】

|包名类名|名称|类型|主要功能|
|------|------|------|------|
|snsoft.ft.sna.fa.scode.vo.FaScode|资产状态|VO类|资产状态|

* 核算方式 fa_scode

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|------|
|fascode|资产状态编码|VARCHAR|SZNBILL|是|||||
|cuicode|商户|VARCHAR|SZCUICODE|是|||||
|fasname|资产状态名称|VARCHAR|SZNAME||是||||
|tyn|是否提折旧|TINYINT|1||||||
|status|状态|VARCHAR|SZSTATUS||是||||
|vprepare|创建人|VARCHAR|SZUCODE||||||
|predate|创建日期|DATE|||||||
|modifier|修改人|VARCHAR|SZUCODE||||||
|modifydate|修改日期|DATE|||||||
	
###### 接口设计

|包名类名|名称|主要功能|
|------|------|------|
|snsoft.ft.sna.fa.scode.service.FaScodeService|资产状态服务|资产状态服务|
|snsoft.ft.sna.fa.scode.service.FaScodeUIService|资产状态UI服务|主要用于权限合法性校验|

|接口名称|方法名|输入说明|输出说明|方法说明|
|------|------|------|------|------|
|FaScodeService|queryList|查询参数|查询结果|资产状态查询服务|
|FaScodeService|save|存盘对象|存盘结果|资产状态服务存盘服务|
|FaScodeUIService|queryListUI|查询参数|查询结果|资产状态查询服务|
|FaScodeUIService|saveUI|存盘对象|存盘结果|资产状态服务存盘服务|
|FaScodeUIService|syncToDBUI|编码,商户编号||同步到数据库|

###### 码表定义

定义编号： FT-SNA.Scode

#### 业务功能

##### 资产期初录入月份设置

###### 单据定义

* 单据号：FT-FA.CardInit

###### 权限结构

权限定义
  * 编号：FT-FA.CardInit
  * 操作：
    C：操作；
  * 权限字段：
	xxx：任意字段

###### 界面定义

   FT-FA.Busi.CardInit
   
###### 接口设计

|包名类名|名称|主要功能|
|------|------|------|
|snsoft.ft.sna.fa.card.service.FaCardInitService|固定资产初始化服务|固定资产初始化服务|
|snsoft.ft.sna.fa.card.service.FaCardInitUIService|固定资产初始化UI服务|主要用于权限合法性校验|

|接口名称|方法名|输入说明|输出说明|方法说明|
|------|------|------|------|------|
|FaCardInitService|doKeepInit|初始化参数|初始化结果|固定资产期初始化服务|
|FaCardInitUIService|doKeepInitUI|初始化参数|存盘结果|固定资产期初始化UI服务|


###### 核心算法

固定资产 monthlogb 含义整理：
        期初录入   monthlogtype=20    initflag=1    flags=1|2  
        月初始化   monthlogtype=20    initflag=0    flags=2
        月记账：   monthlogtype=20    initflag=0  flags=flags|1
                
相关检查：
  待详细整理？？？

相关处理：
  待详细整理？？？？

##### 固定资产卡片建立

###### 单据定义

* 单据号：FT-FA.Card
* 主表：fa_card
* 内码列名：cardicode
* 外码列名：fcode
* 主表VO：snsoft.ft.sna.fa.card.vo.FaCard

###### 权限结构

权限定义
  * 编号：FT-FA.Card
  * 操作：
    R：查询
    C：操作
  * 权限字段：
	cuidode：商户
	vprepare：用户
		
###### 界面定义

   FT-FA.Busi.Card

###### 实体类

* 【实体类】

|包名类名|名称|类型|主要功能|
|------|------|------|------|
|snsoft.ft.sna.fa.card.vo.FaCard|资产卡片|VO类|资产卡片|
|snsoft.ft.sna.fa.card.vo.FaCardBcode|卡片部门表|VO类|卡片部门表|
|snsoft.ft.sna.fa.card.vo.FaCardSubEquipt|资产卡片附属设备|VO类|资产卡片附属设备|


* 资产卡片 fa_card

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|------|
|cardicode|资产卡片ID|VARCHAR|SZNBILL|是|||||
|year|年|SMALLINT|4||||||
|month|月|SMALLINT|2||||||
|iyear|建卡年份|SMALLINT|4||||||
|imonth|建卡月份|SMALLINT|2||||||
|kyear|记帐年份|SMALLINT|4||||||
|kmonth|记帐月份|SMALLINT|2||||||
|status|单据状态|VARCHAR|SZSTATUS|||||10:制单;20:提交;70:完结|
|busistatus|内部状态|SMALLINT|2|||||1:资产折旧;21:资产折旧;2:固定资产卡片增加;19:卡片其他修改;|
|fcode|资产编号|VARCHAR|SZNBILL||||||
|fname|资产名称|VARCHAR|SZNAME||||||
|vid|凭证内码|VARCHAR|SZIBILL||||||
|vno|凭证号|VARCHAR|SZNBILL||||||
|fatcode|资产类别|VARCHAR|SZNBILL|||||码表|
|fascode|使用状态|VARCHAR|SZNBILL|||||码表|
|faocode|核算方式|VARCHAR|SZNBILL|||||码表|
|model|规格型号|VARCHAR|SZTEXT||||||
|place|存放地点|VARCHAR|SZTEXT||||||
|qtyunit|计量单位|VARCHAR|SZHUNIT|||||码表|
|qty|数量|NUMERIC|QTYINT.QTYDEC||||||
|fadcode|折旧方法|VARCHAR|SZNBILL|||||码表|
|lifetime|使用年限(年)|SMALLINT|4||||||
|lifemonth|使用年限(月)|SMALLINT|4||||||
|bdate|始用日期|DATE|||||||
|usemonth|已用月数|SMALLINT|4||||||
|ovalue|原值|NUMERIC|MNYINT.MNYDEC||||||
|mdrate|月折旧率|NUMERIC|RATINT.RATDEC||||||
|mdepr|月折旧额|NUMERIC|MNYINT.MNYDEC||||||
|thedepr|本月折旧|NUMERIC|MNYINT.MNYDEC||||||
|tdepr|累计折旧|NUMERIC|MNYINT.MNYDEC||||||
|tdeprj|本季折旧|NUMERIC|MNYINT.MNYDEC||||||
|nvalue|净值|NUMERIC|MNYINT.MNYDEC||||||
|ovalue0|折旧原值|NUMERIC|MNYINT.MNYDEC||||||
|lifetime0|折旧使用年限(年)|SMALLINT|4||||||
|lifemonth0|折旧使用年限(月)|SMALLINT|4||||||
|ptworks0|折旧预计工作量|NUMERIC|QTYINT.QTYDEC||||||
|faflag|变更选项|SMALLINT|4|||||0:不改变用于折旧计算的要素(每期计提折旧保持不变);1:按调整值改变用于折旧计算的要素;2:以固定资产帐面净值为原值,剩余使用期间数为预计使用期间 ???变更操作使用 可废弃 添加到卡片变更表|
|wunit|工作量单位|VARCHAR|12||||||
|flags|卡片选项|SMALLINT|4|||||平均年限法-单选|
|ptworks|预计工作量|NUMERIC|QTYINT.QTYDEC||||||
|deprpw|每工作量折旧|NUMERIC|QTYINT.QTYDEC||||||
|thework|本月工作量|NUMERIC|QTYINT.QTYDEC||||||
|theworkj|本季工作量|NUMERIC|QTYINT.QTYDEC||||||
|tworks|累计工作量|NUMERIC|QTYINT.QTYDEC||||||
|crate|残值率|NUMERIC|RATINT.RATDEC||||||
|cvalue|预计残值|NUMERIC|MNYINT.MNYDEC||||||
|adddate|补提日期|DATE||||||??? 数量变更用到了|
|remark|备注|VARCHAR|SZTEXT||||||
|iskept|是否记帐|SMALLINT|||||||
|sheetcode|单据码|VARCHAR|SZSHEET||||||
|usemonth0|计提已用月数|SMALLINT|4||||||
|tdepry|本年折旧|NUMERIC|MNYINT.MNYDEC||||||
|thesubvalue|本月减值准备|NUMERIC|MNYINT.MNYDEC||||||
|tsubvalue|累计减值准备|NUMERIC|MNYINT.MNYDEC||||||
|price|购买价|NUMERIC|MNYINT.MNYDEC||||||
|evaovalue|原值（评估前）|NUMERIC|MNYINT.MNYDEC||||||
|evatdepr|累计折旧(评估前)|NUMERIC|MNYINT.MNYDEC||||||
|evalifetime0|折旧年限(评估前)|SMALLINT|4||||||
|evacrate|残值率（评估前）|NUMERIC|RATINT.RATDEC||||||
|evacvalue|预计残值（评估前）|NUMERIC|MNYINT.MNYDEC||||||
|purcode|供应商|VARCHAR|SZNBILL||||||
|purname|供应商名称|VARCHAR|SZTEXT||||||
|vtypelimit|可使用凭证类别|VARCHAR|100||||||
|keptbcode|财务组织|VARCHAR|SZBCODE||||||
|bcode|业务组织|VARCHAR|SZBCODE||||||
|jcode|成本利润中心|VARCHAR|SZBCODE||||||
|wcode|属主人员|VARCHAR|SZWCODE||||||
|mainacode|折旧科目|VARCHAR|SZNBILL||||||
|viddesc|凭证描述字段|VARCHAR|SZTEXT||||||
|voucherflag|凭证制作状态|SMALLINT|2||||||
|upicode|上级单据ID|VARCHAR|SZIBILL||||||
|upsheetcode|上级单据号|VARCHAR|SZNBILL||||||
|caldeprflag|卡片折旧标识|SMALLINT|4||||||
|vprepare|创建人|VARCHAR|SZUCODE||||||
|predate|创建日期|DATE|||||||
|modifier|修改人|VARCHAR|SZUCODE||||||
|modifydate|修改日期|DATE|||||||
|checker|审核人|VARCHAR|SZUCODE||||||
|checkdate|审核日期|DATE|||||||
|barcode|条形码|VARCHAR|64||||||
|econuse|经济用途|VARCHAR|SZTEXT||||||
|srcvno|原始凭证字号|VARCHAR|64||||||
|dealcost|处理费用|NUMERIC|MNYINT.MNYDEC||||||
|evacincome|残值收入|NUMERIC|MNYINT.MNYDEC||||||
|dealreason|处理原因|VARCHAR|SZTEXT||||||
|dealdate|处理日期|DATE|||||||
|mtpic|图片路径|VARCHAR|SZIMGPATH||||||
|frozen|冻结人|VARCHAR|SZUCODE||||||
|gvcode|物料类别|VARCHAR|SZNBILL||||||
|manufacturername|制造商名称|VARCHAR|SZTEXT||||||
|cardtype|资产类型|VARCHAR|20|||||资产类型定义拷贝|
|cuicode|商户|VARCHAR|SZCUICODE||||||
|fa_var0|预留字符串0|VARCHAR|64||||||
|fa_var1|预留字符串1|VARCHAR|64||||||
|fa_var2|预留字符串2|VARCHAR|64||||||
|fa_var3|预留字符串3|VARCHAR|64||||||
|fa_var4|预留字符串4|VARCHAR|64||||||
|fa_var5|预留字符串5|VARCHAR|64||||||
|fa_var6|预留字符串6|VARCHAR|64||||||
|fa_var7|预留字符串7|VARCHAR|64||||||
|fa_var8|预留字符串8|VARCHAR|64||||||
|fa_var9|预留字符串9|VARCHAR|64||||||
|fa_date0|预留日期0|DATE|||||||
|fa_date1|预留日期1|DATE|||||||
|fa_date2|预留日期2|DATE|||||||
|fa_date3|预留日期3|DATE|||||||
|fa_date4|预留日期4|DATE|||||||
|fa_num0|预留数值0|NUMERIC|MNYINT.MNYDEC||||||
|fa_num1|预留数值1|NUMERIC|MNYINT.MNYDEC||||||
|fa_num2|预留数值2|NUMERIC|MNYINT.MNYDEC||||||
|fa_num3|预留数值3|NUMERIC|MNYINT.MNYDEC||||||
|fa_num4|预留数值4|NUMERIC|MNYINT.MNYDEC||||||
|addtax|增值税|NUMERIC|MNYINT.MNYDEC|||||???|
|transferfcodeinfo|转移到其他记账部门资产信息|VARCHAR|SZTEXT|||||固定资产卡片减少（转移到其他记账部门）|
	
索引： 账部门、年、月 联合索引

* 卡片部门表 fa_cardb

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|------|
|cardbicode|ID|VARCHAR|SZIBILL|是|||||
|cardicode|资产卡片ID|VARCHAR|SZNBILL||||||
|year|年|SMALLINT|4||||||
|month|月|SMALLINT|2||||||
|fcode|资产编号|VARCHAR|||||||
|bcode|业务组织|VARCHAR|||||||
|jcode|成本利润中心|VARCHAR|||||||
|wcode|属主人员|VARCHAR|||||||
|bl|使用比例|NUMERIC|||||||
|qty|数量|NUMERIC|||||||
|ovalue|原值|NUMERIC|||||||
|mdrate|月折旧率|NUMERIC|||||||
|mdepr|月折旧额|NUMERIC|||||||
|thedepr|本月折旧|NUMERIC|||||||
|tdepr|累计折旧|NUMERIC|||||||
|tdeprj|本季折旧|NUMERIC|||||||
|nvalue|净值|NUMERIC|||||||
|ovalue0|折旧原值|NUMERIC|||||||
|ptworks0|折旧预计工作量|NUMERIC|||||||
|ptworks|预计工作量|NUMERIC|||||||
|deprpw|每工作量折旧|NUMERIC|||||||
|thework|本月工作量|NUMERIC|||||||
|theworkj|本季工作量|NUMERIC|||||||
|tworks|累计工作量|NUMERIC|||||||
|cvalue|预计残值|NUMERIC|||||||
|thesubvalue|本月减值准备|NUMERIC|||||||
|tsubvalue|累计减值准备|NUMERIC|||||||
|n6key|n6主表内码|VARCHAR||||||????|
|cuicode|商户|VARCHAR||||||&nbsp;|


* 卡片附属设备表 fa_cardeq

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|------|
|cardeqicode|附属设备内码|VARCHAR|SZIBILL|是|||||
|cardicode|资产卡片ID|VARCHAR|SZNBILL||||||
|cardcicode|资产卡片变更单ID|VARCHAR|SZNBILL||||||
|year|年|SMALLINT|4||||||
|month|月|SMALLINT|2||||||
|fcode|资产编号|VARCHAR|SZNBILL||||||
|idx|序号|INTEGER|||||||
|eqcode|设备编号|VARCHAR|64||||||
|eqname|设备名称|VARCHAR|SZTEXT||||||
|odate|登记日期|DATE|||||||
|model|规格型号|VARCHAR|SZTEXT||||||
|qunit|计量单位|VARCHAR|8||||||
|qty|数量|NUMERIC|||||||
|fcy|金额|NUMERIC|||||||
|place|存放地点|VARCHAR|SZTEXT||||||
|remark|备注|VARCHAR|SZTEXT||||||
|model|规格型号|VARCHAR|SZTEXT||||||

###### 接口设计

|包名类名|名称|主要功能|
|------|------|------|
|snsoft.ft.sna.fa.card.service.FaCardService|固定资产卡片服务|固定资产卡片服务|
|snsoft.ft.sna.fa.card.service.FaCardUIService|固定资产卡片UI服务|主要用于权限合法性校验|

|接口名称|方法名|输入说明|输出说明|方法说明|
|------|------|------|------|------|
|FaCardService|quryList|查询参数|查询结果|固定资产卡片列表查询服务|
|FaCardService|quryDetail|查询参数|存盘结果|固定资产卡片详情查询服务|
|FaCardService|quryBcodeDetail|查询参数|存盘结果|固定资产卡片-部门查询服务|
|FaCardService|quryEquiptDetail|查询参数|存盘结果|固定资产卡片-附属设备查询服务|
|FaCardService|save|存盘参数|存盘结果|固定资产卡片存盘服务|
|FaCardUIService|quryListUI|查询参数|查询结果|固定资产卡片UI列表查询服务|
|FaCardUIService|quryDetailUI|查询参数|存盘结果|固定资产卡片UI详情查询服务|
|FaCardUIService|quryBcodeDetailUI|查询参数|存盘结果|固定资产卡片-部门UI查询服务|
|FaCardUIService|quryEquiptDetailUI|查询参数|存盘结果|固定资产卡片-附属设备UI查询服务|
|FaCardUIService|saveUI|存盘参数|存盘结果|固定资产卡片UI存盘服务|


##### 月初始化界面

###### 单据定义

* 单据号：FT-FA.CardMonthInit

###### 权限结构

权限定义
  * 编号：FT-FA.CardMonthInit
  * 操作：
    C：操作
  * 权限字段：
	xxx：任意字段
		
###### 界面定义

   FT-FA.Busi.CardMonthInit
 
###### 接口设计

|包名类名|名称|主要功能|
|------|------|------|
|snsoft.ft.sna.fa.card.service.FaCardInitUIService|固定资产初始化服务|固定资产初始化服务|
|snsoft.ft.sna.fa.card.service.FaCardInitUIService|固定资产卡片UI服务|主要用于权限合法性校验|

|接口名称|方法名|输入说明|输出说明|方法说明|
|------|------|------|------|------|
|FaCardInitService|doMonthInit|初始化参数|初始化结果|固定资产初始化服务|
|FaCardInitUIService|doMonthInitUI|初始化参数|存盘结果|固定资产初始化UI服务|
  
###### 核心算法

选定记账部门、年、月 执行月初始化操作操作

相关检查：
   待详细整理
   
相关处理：
   待详细整理

##### 资产增加

###### 核心算法
此功能同 **固定资产卡片建立** 区别在于 status 为 2:固定资产卡片增加
同步新增数据到 **卡片变更** 申请单中，统一审核

###### 界面定义

   FT-FA.Busi.CardAdd
   
##### 资产其他修改

###### 核心算法
此功能同 **固定资产卡片建立** 区别在于 status 为 19:卡片其他修改
同步新增数据到 **卡片变更** 申请单中，统一审核

###### 界面定义

   FT-FA.Busi.CardOther
   
##### 卡片变更

###### 单据定义

* 单据号：FT-FA.CardChange
* 主表：fa_cardc
* 内码列名：cardcicode
* 外码列名：fcode
* 主表VO：snsoft.ft.sna.fa.card.vo.FaCardChange

###### 权限结构

权限定义
  * 编号：FT-FA.CardChange
  * 操作：
    R：查询
    C：操作
  * 权限字段：
	cuidode：商户
	vprepare：用户
		
###### 界面定义

   FT-FA.Busi.CardChange

###### 实体类

* 【实体类】

|包名类名|名称|类型|主要功能|
|------|------|------|------|
|snsoft.ft.sna.fa.card.vo.FaCardChange|卡片变更操作表|VO类|卡片变更操作表|
|snsoft.ft.sna.fa.card.vo.FaCardChangeBcode|卡片部门变更表|VO类|卡片部门变更表|

* 卡片变更操作表 fa_cardc

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|------|
|cardcicode|变更单内码|VARCHAR|SZIBILL|是|||||
|cardicode|资产卡片ID|VARCHAR|SZNBILL||||||
|status|状态|VARCHAR|SZSTATUS|||||10:制单;20:审核中;70：完结|
|busistatus|内部状态|SMALLINT|2|||||4:原值增加;5:原值减少;6:累计折旧增加;7:累计折旧减少;8:计提减值准备;16:状态调整;18:使用年限调整;17:部门转移;31:固定资产资产减少;|
|fcode|资产编号|VARCHAR|SZNBILL||||||
|fname|资产名称|VARCHAR|SZNAME||||||
|qty|数量|NUMERIC|QTYINT.QTYDEC|||||opttype：31|
|qtyopt|数量变更|NUMERIC|QTYINT.QTYDEC|||||opttype：31|
|dealcost|处理费用|NUMERIC||||||opttype：31|
|evacincome|残值收入|NUMERIC||||||opttype：31|
|dealreason|处理原因|VARCHAR|SZTEXT|||||opttype：31|
|dealdate|处理日期|DATE||||||opttype：31|
|ovalue|原值|NUMERIC|MNYINT.MNYDEC|||||opttype：4、5|
|ovalueopt|原值变更|NUMERIC|MNYINT.MNYDEC|||||opttype：4、5|
|faflag|变更选项|SMALLINT|4|||||opttype：4、5|
|adddate|补提日期|DATE||||||opttype：4、5|
|tdepr|累计折旧|NUMERIC|MNYINT.MNYDEC|||||opttype：6、7|
|tdepropt|累计折旧变更|NUMERIC|MNYINT.MNYDEC|||||opttype：6、7|
|thesubvalue|本月减值准备|NUMERIC|MNYINT.MNYDEC|||||opttype：8|
|thesubvalueopt|本月减值准备变更|NUMERIC|MNYINT.MNYDEC|||||opttype：8|
|place|存放地点|VARCHAR|SZTEXT|||||opttype：17|
|placeopt|存放地点变更|VARCHAR|SZTEXT|||||opttype：17|
|lifetime|使用年限(年)|SMALLINT|4|||||opttype：18|
|lifetimeopt|使用年限(年)变更|SMALLINT|4|||||opttype：18|
|lifemonth|使用年限(月)|SMALLINT|4|||||opttype：18|
|lifemonthopt|使用年限(月)变更|SMALLINT|4|||||opttype：18|
|mtpic|图片路径|VARCHAR|SZIMGPATH|||||opttype：16|
|mtpicopt|图片路径|VARCHAR|SZIMGPATH|||||opttype：16|
|scode|使用状态|VARCHAR|SZNBILL|||||opttype：16|
|scodeopt|使用状态|VARCHAR|SZNBILL|||||opttype：16|
|remark|备注|VARCHAR|SZTEXT||||||
|sheetcode|单据码|VARCHAR|SZSHEET||||||
|cuicode|商户|VARCHAR|SZCUICODE||||||
|checker|审核人|VARCHAR|SZUCODE||||||
|checkdate|审核时间|VARCHAR|SZUCODE||||||
|vprepare|创建人|VARCHAR|SZUCODE||||||
|predate|创建日期|DATE|||||||
|modifier|修改人|VARCHAR|SZUCODE||||||
|modifydate|修改日期|DATE|||||||

* 卡片变更部门表 fa_cardbc

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|------|
|cardbcicode|ID|VARCHAR|SZIBILL|是|||||
|cardbicode|资产卡片部门ID|VARCHAR|SZIBILL||||||
|cardicode|资产卡片ID|VARCHAR|SZNBILL||||||
|bcode|业务组织|VARCHAR|||||||
|jcode|成本利润中心|VARCHAR|||||||
|wcode|属主人员|VARCHAR|||||||
|bl|使用比例|NUMERIC|||||||

###### 接口设计

|包名类名|名称|主要功能|
|------|------|------|
|snsoft.ft.sna.fa.card.service.FaCardChangeUIService|卡片变更服务|卡片变更服务|
|snsoft.ft.sna.fa.card.service.FaCardChangeUIService|卡片变更UI服务|主要用于权限合法性校验|

|接口名称|方法名|输入说明|输出说明|方法说明|
|------|------|------|------|------|
|FaCardChangeService|quryList|查询参数|查询结果|卡片变更查询服务|
|FaCardChangeService|quryBcodeChangeList|查询参数|查询结果|卡片变更-部门变更查询服务|
|FaCardChangeService|save|存盘参数|存盘结果|卡片变更存盘服务|
|FaCardChangeUIService|quryListUI|查询参数|查询结果|卡片变更查询UI服务|
|FaCardChangeUIService|saveUI|存盘参数|存盘结果|卡片变更存盘UI服务|


##### 卡片审核

###### 单据定义

* 单据号：FT-FA.CardChangeAudit
* 主表：fa_cardc
* 内码列名：cardcicode
* 外码列名：fcode
* 主表VO：snsoft.ft.sna.fa.card.vo.FaCardChange

###### 权限结构

权限定义
  * 编号：FT-FA.CardChangeAudit
  * 操作：
    R：查询
    C：操作
  * 权限字段：
	cuidode：商户
	vprepare：用户
		
###### 界面定义

   FT-FA.Busi.CardChangeAudit

###### 实体类

* 【实体类】

|包名类名|名称|类型|主要功能|
|------|------|------|------|
|snsoft.ft.sna.fa.card.vo.FaCardChange|卡片变更操作表|VO类|卡片变更操作表|

###### 接口设计

|包名类名|名称|主要功能|
|------|------|------|
|snsoft.ft.sna.fa.card.service.FaCardChangeAuditService|卡片变更审核服务|卡片变更审核|
|snsoft.ft.sna.fa.card.service.FaCardChangeAuditUIService|卡片变更审核UI服务|主要用于权限合法性校验|

|接口名称|方法名|输入说明|输出说明|方法说明|
|------|------|------|------|------|
|FaCardChangeAuditService|doAuidit|审核参数|审核结果|卡片变更审核服务|
|FaCardChangeAuditUIService|doAuiditUI|审核参数|审核结果|卡片变更审核UI服务|

###### 核心算法

更新变更单状态为70，记录审核人、审核时间

##### 记账

###### 单据定义

* 单据号：FT-FA.CardKeept

###### 权限结构

权限定义
  * 编号：FT-FA.CardKeept
  * 操作：
    C：操作
  * 权限字段：
	xxx：任意字段
		
###### 界面定义

   FT-FA.Busi.CardKeept
 
###### 接口设计

|包名类名|名称|主要功能|
|------|------|------|
|snsoft.ft.sna.fa.kept.service.FaCardKeepService|固定资产记账服务|固定资产记账服务|
|snsoft.ft.sna.fa.kept.service.FaCardKeepUIService|固定资产记账UI服务|主要用于权限合法性校验|

|接口名称|方法名|输入说明|输出说明|方法说明|
|------|------|------|------|------|
|FaCardKeepService|doKeep|记账参数|记账结果|固定资产记账服务|
|FaCardKeepUIService|doKeepUI|初始化参数|存盘结果|固定资产记账UI服务|
  
###### 核心算法

相关检查：
   待详细整理
   
相关处理：
   1、处理卡片变更
   2、计算折旧
   3、处理原值、卡片增加减少
   4、设置记账标识
   5、存盘
   6、按部门分摊原值折旧
   
#### 统计查询
   
##### 卡片查询

###### 单据定义

* 单据号：FT-FA.CardCreate
* 主表：fa_card
* 内码列名：cardicode
* 外码列名：fcode
* 主表VO：snsoft.ft.sna.fa.card.vo.FaCard

###### 权限结构

权限定义
  * 编号：FT-FA.CardCreate
  * 操作：
    R：查询
    C：操作
  * 权限字段：
	cuidode：商户
	vprepare：用户
		
###### 界面定义

   FT-FA.Query.CardQuery
   
###### 其他说明

同卡片新建单据查询功能

##### 变更明细

###### 单据定义

* 单据号：FT-FA.CardChange
* 主表：fa_cardc
* 内码列名：cardcicode
* 外码列名：fcode
* 主表VO：snsoft.ft.sna.fa.card.vo.FaCardChange

###### 权限结构

权限定义
  * 编号：FT-FA.CardChange
  * 操作：
    R：查询
    C：操作
  * 权限字段：
	cuidode：商户
	vprepare：用户
		
###### 界面定义

   FT-FA.Query.CardChangeList
   
###### 其他说明

同变更单据查询功能
   
### 核心算法

### 数据库宏说明

### 字典说明

### 码表说明

资产类别： FT-SNA.Tcode
核算方式： FT-SNA.Ocode
折旧方法： FT-SNA.Dcode
资产状态： FT-SNA.Scode
### 提示码说明

### 系统选项

1420100020-原值下限
1420100040-从业务系统生成的卡片,只能在业务系统制凭证

1420200005-使用年限变更后是否当月生效
1420200010-已分摊过折旧的月份不能做月初始化
1420200015-默认变更选项
1420200030-对本月以前的月份能否跨月做月初始化
1420200045-资产增加、减少、变更时是否需要审核环节
1420200050-资产卡片制凭证后禁止操作附属设备表
1420200060-固定资产是从业务系统生成的是否允许修改删除
1420200060-审核资产卡片操作人和审核人可以相同 --暂无引用
1420200065-月初始化发现上月有未生成凭证的记录时如何处理
1420200066-月初始化发现本月有固定资产减少及变更单据的凭证未被删除如何处理
1420200067-月初始化时检查本月总账系统是否已结账
1420200070-初始化时需检查上一个月的哪些凭证（以逗号分隔）
1420200075-初始化时需检查本月的哪些凭证（以逗号分隔）
1420200080-是否核算到核算组
1420200083-是否核算到人员
1420200085-存放地点录入方式
1420200087-最大会计月

### 商户选项

### 遗留问题
