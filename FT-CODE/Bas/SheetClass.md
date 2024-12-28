## 【单据分类】

设计人：【冀小雷】      设计日期  ：【2019-05-08】    宣讲日期 ：【2019-03-7】

评审记录：

|评审日期|评审/确认人|
|------|------|
|*|*|

变更记录：

|变更日期|变更说明|
|------|------|
|XX|XX|

### 总体设计

* 【功能说明】
	用于对单据进行归类，并支持单据数据的选择。
* 【主要结论】
	
* 【业务流程图（时序图或ER图）】
	
### 工程结构

工程名称：ft-code-api、ft-code-impl、ft-code-ui、ft-code-xjs
工程包名：snsoft.ft.code.bas.ss

### 模块设计

#### 【区】

##### 单据定义

1. 单据定义：
	* 单据号：FT-CODE.Bas.SS；
	* 主表：ft_cd_ss；
	* 内码列名：sscode；
	* 外码列名：sscode；
	* 主表VO：snsoft.ft.code.bas.ss.vo.SheetClass；

##### 权限结构

1. 权限定义：
	* 编号：FT-CODE.Bas.SS；
	* 操作：
	    R：查询；
		C：增删改；
	* 权限字段：
		sscode：单据分类编码；
1. 单据权限关系：
    * 单据号：FT-CODE.Bas.SS；
    * 权限编号：FT-CODE.Bas.SS；

##### 界面

1. 列表：FT-CODE.Bas.SS；
1. 命令号：FT-CODE.Bas.SS

##### 系统功能监听

```xml
<Groups id="核算单据分类">
	<Functions>
		<Functype>SN-PLAT.UI</Functype>
		<Subtype>2</Subtype>
		<Funcimpl>snsoft.ft.code.bas.ss.ui.SSUIListener.new?sscode=00</Funcimpl>
		<Remark>核算单据分类UI监听</Remark>
	</Functions>
	<Functions>
		<Functype>SN-PLAT.JS</Functype>
		<Subtype>2</Subtype>
		<Funcimpl>#new snsoft.ft.code.bas.ss.SSJSListener({})</Funcimpl>
		<Remark>核算单据分类JS监听</Remark>
	</Functions>
</Groups>
```

##### 实体类

* 【实体类】

|包名类名|名称|类型|主要功能|
|------|------|------|------|
|vo.SheetClass|单据分类|VO类|分类表头|
|vo.SheetDetail|单据分类明细|VO类|分类明细|

* 默认区文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SheetClass ssname="" xmlns="http://snsoft.cn/schema/FTSheetClass.xsd">
	<Remark></Remark>
	<Details sheetcode="" codetbls="" codetblg="">
		<saiprops></saiprops>
		<gaiprops></gaiprops>
	</Details>
</SheetClass>
```

* 分类配置：ft_cd_ss

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|sscode|编码|VARCHAR|SSCODE|是|是||是||
|ssname|名称|VARCHAR|SZNAME||是||是||
|remark|说明|VARCHAR|SZTEXT||是||是||
|vprepare|创建人|VARCHAR|SZUCODE|||||默认值：缺省当前登录用户|
|predate|创建时间|DATE||||||默认值：缺省当前系统时间|
|modifier|修改人|VARCHAR|SZUCODE|||||默认值：缺省当前登录用户|
|modifydate|修改时间|DATE||||||默认值：缺省当前系统时间|

* 分类明细：ft_cd_ssg

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|sscode|编码|VARCHAR|SSCODE|是|是||是||
|sheetcode|名称|VARCHAR|SZSHEET||是||是||
|sortidx|序号|SMALLINT|0||||||
|codetbls|单据码表|VARCHAR|SZCODETBL||||||
|saiprops|单据对话框属性|VARCHAR|SNTEXT||||||
|codetblg|商品码表|VARCHAR|SZCODETBL||||||
|gaiprops|商品对话框属性|VARCHAR|SNTEXT||||||
|modifier|修改人|VARCHAR|SZUCODE|||||默认值：缺省当前登录用户|
|modifydate|修改时间|DATE||||||默认值：缺省当前系统时间|


##### 接口设计

* 【UI端接口】

|包名类名|名称|主要功能|
|------|------|------|
|service.SSUIService|服务接口|数据维护|
|service.SSUIListener|UI功能监听|功能监听|
|service.SSJSListener|JS功能监听|功能监听|

|接口名称|方法名|输入说明|输出说明|
|------|------|------|------|------|
|SSUIService|queryUI|查询参数|列表数据|
|SSUIService|saveUI|存盘参数|存盘结果|
