## 表结构字典

设计人：【冀小雷】      设计日期  ：【2019-01-18】    宣讲日期 ：【2019-01-17】

评审记录：

|评审日期|评审/确认人|
|------|------|
|*|*|

变更记录：

|变更日期|变更说明|
|------|------|
|2019-01-22|李朋、石宏宇希望可以全公司各产品各项目集中维护一套支持增量的数据库字典|

### 总体设计

* 【功能说明】
	用于提供列名、类型、大小宏及业务含义说明。
* 【主要结论】
	* 给列起名时，应该在字典中具有相同含义的列，找到则使用；
	* 整个系统中同一个列名应该是同一个含义，同一个含义的列应该同名；
* 【业务流程图（时序图或ER图）】

### 工程结构

工程名称：snadk-cmc-ui,snadk-cmc-xjs
工程包名：snsoft.cmc.tools.metadict；

### 模块设计

#### 【基础资料】

##### 单据定义

	无

##### 权限结构

	仅管理员可查、超级管理员可改。

##### XML格式

路径：res/metadict/MetaDict[sysid].xml

```xml
<MetaDictDef xmlns="http://snsoft.cn/schema/MetaDictDef.xsd">
	<Dicts name="" title="" sqltype="" size="" decimal="">
		<Remark>说明</Remark>
	</Dicts>
</MetaDictDef>
```

##### 界面

1. 列表：SN-PLAT.MetaDictQuery；
	* 参数：列名、标题（模糊）、说明（模糊）
1. 菜单命令号：SN-PLAT.MetaDict；

##### 实体类

* 【实体类】

|包名类名|名称|类型|主要功能|
|------|------|------|------|
|vo.MetaDictDef|表头|VO类|结构需要|
|vo.MetaDict|字典明细|VO类|列字典|

* MetaDict

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|
|sysid|系统|VARCHAR|SZSYSID||是||||
|name|列名|VARCHAR|SZFIELDNAME|是|是||||
|title|标题|VARCHAR|64||是||||
|sqltype|类型|VARCHAR|32||是||||
|fldsize|大小|VARCHAR|32||||||
|flddec|小数位|VARCHAR|32||||||
|remark|备注|VARCHAR|512||||||
|vprepare|创建人|VARCHAR|SZUCODE|||||默认值：缺省当前登录用户|
|predate|创建时间|DATE|0|||||默认：缺省当前系统时间|
|modifier|修改人|VARCHAR|SZUCODE|||||默认值：缺省当前登录用户|
|modifydate|修改时间|DATE|0|||||默认：缺省当前系统时间|

##### 接口设计

* 【接口】

|包名类名|名称|主要功能|
|------|------|------|
|service.MetaDictService|表结构字典服务|对外提供服务|
|service.MetaDictUIService|表结构字典UI服务|提供界面服务|

|接口名称|方法名|输入说明|输出说明|
|------|------|------|------|------|
|MetaDictService|query|参数对象|字典数据|
|MetaDictUIService|queryUI|参数对象|字典数据|

#### 类调用关系

【类图】

@startuml

Title 表结构字典类图

MetaDictUIService o--> MetaDictService
MetaDictService ..> MetaDict
MetaDictDef *--> MetaDict

interface MetaDictService {
	+ List<MetaDict> query(QueryParams params);
}

interface MetaDictUIService{
    + List<MetaDict> queryUI(QueryParams params);
}

class MetaDictDef{
    - List<MetaDict> dicts;
}

class MetaDict {
}

@enduml

### 核心算法

无

### 数据库宏说明

	无

### 字典说明

	无

### 码表说明

	无

### 系统选项

	无

### 遗留问题

1. 支持导入，支持录入；
1. 数据库优先级高于文件优先级，界面查询为混合方式；
1. 数据库宏查询：宏名称、宏标题、宏大小；