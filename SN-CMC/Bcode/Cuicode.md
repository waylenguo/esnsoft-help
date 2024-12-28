## 商户

|日期|作者|备注|
|------|------|------|
|2019-03-14|冀小雷|初版|
|2019-11-08|郭鑫|修改|

### 指南

#### 概念说明


为了支持本司的SaaS服务，底层增加商户概念，即支持多少户在线。

通过系统选项【APP.StartCuicode=true】进行启用。

#### 使用说明

##### 多商户及非多商户通用功能

1. 表结构：
	* 列名称必须为【cuicode】；
	* 可以将商户作为缓存规则；
	* 生成数据库时根据选项判断是否生成商户列；
1. VO注解：
	* 由于根据选项进行判断是否启用商户，故VO中必须存在cuicode属性；
	* 通过【@Column】进行标注：
		* 启用商户时非空必录；
		* 没有启用商户时，使用VO进行查询则过滤该属性；
		* VOClassInfo中根据选项判断是否返回该属性；
	* 通过【@DefaultValue(value = "UCode:Cuicode")】进行标注：启用商户时设置默认值；
1. 强制过滤条件：
	* 为了防止权限扩大，在启用商户时必须强制拼商户过滤条件；
	* 使用【@CuicodeFilter】注解标注，设置在UI层的查询接口上即可，通过切面进行设置过滤条件；

```xml
<!--表结构Demo-->
	<!--配置表Demo-->
    <table id="00013" name="usergwcode" title="用户岗位表" datasrcid="UCODECFG"
           cachekcols="cuicode" localcache="1" cachekeym="*cuicode=*${cuicode}*" storegrp="SNSYSDATA_TBS">
        <column name="cuicode" title="商户" type="VARCHAR(SZCUICODE)" primkey="true"/>
        <column name="usercode" title="用户编码" type="VARCHAR(SZUCODE)" primkey="true"/>
        <column name="gwcode" title="岗位编码" type="VARCHAR(SZGWCODE)" primkey="true"/>
    </table>
    <!--业务表Demo-->
    <table id="32000" name="plat_salorder" title="销售合同" datasrcid="HELP" datasrcid2="HELP_VB" storegrp="ORD_TBS">
		<column id="10" name="salordicode" title="内码" type="VARCHAR(SZIBILL)" primkey="true" />
		<column id="20" name="salordcode" title="外码" type="VARCHAR(SZNBILL)" notnull="true" />
		<column id="50" name="cuicode" title="商户" type="VARCHAR(SZCUICODE)" notnull="true"/>
		...
	</table>
```

```java
// VO注解
	// 普通列
	@Column
	@Cuicode
	@DefaultValue(value = "UCode:Cuicode")
	private String				cuicode;
	// 主键
	@Id
	@Column
	@Cuicode
	@DefaultValue(value = "UCode:Cuicode")
	private String				cuicode;
```

```java
// 过滤条件注解
	@CuicodeFilter
	@AuthParam(sheetCode = "SN-HELP.Salorder", opids = { LimitConst.Opid_C, LimitConst.Opid_R })
	QueryResults<Salorder> queryOrderUI(SalorderParams params);
```

### 手册

#### 非多商户功能

有些通用功能不受商户选项控制，无论是否启用商户其自身功能不受影响，如界面定义等。

#### 仅多商户功能

如果贸服平台中的下单方和接单方功能，肯定是多商户功能，可以直接写死相关功能，而不必考虑商户选项。

#### 多商户及非多商户通用功能

有些功能在启用商户及不启用商户时都可以且必须保证功能正常使用，如组织结构、订单管理、编码规则等；
由于在启用商户与不启用商户时需要处理的结构及逻辑不通，故需要根据选项进行特殊处理，枚举如下：

### 实践

### 设计

