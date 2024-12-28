## 系统选项

|日期|作者|备注|
|------|------|------|
|2018-09-25|周鹏辉|初稿|
|2018-10-10|吕希兵|文档更新|
|2020-08-03|张东|重构|

### 指南

####选项类型分为三类

1. 系统选项：系统级选项  DB+文件+默认值 （含商户）
1. 用户选项：用户级选项  DB+文件+默认值
1. 组织选项：组织级选项  DB+文件+默认值（部门/公司/财务）

#### XML配置文件

使用XML文件进行配置作为默认值。
有些系统选项可同时配置数据库，底层平台会优先使用数据库配置。

#### 配置文件命名

```
系统选项（含商户）：
    格式：SysOption[sysid].xml
    以SysOption开头加系统模块号；
    如SysOption（固定）+SN-HELP.xml（模块号）=》SysOptionSN-HELP.xml
用户选项：
    格式：UcodeOption[sysid].xml
    以UcodeOption开头加系统模块号；
    如UcodeOption（固定）+SN-HELP.xml（模块号）=》UcodeOptionSN-HELP.xml
组织选项：
    格式：BcodeOption[sysid].xml
    以BcodeOption开头加系统模块号；
    如BcodeOption（固定）+SN-HELP.xml（模块号）=》BcodeOptionSN-HELP.xml
```

#### 文件存放路径

```
当前模块的UI工程resources/snsoft/app/下
示例：
    /snadk-dx/src/main/resources/snsoft/app/SysOption00.xml
    /snadk-dx/src/main/resources/snsoft/app/UcodeOption00.xml
    /snadk-dx/src/main/resources/snsoft/app/BcodeOption00.xml
```

#### 内容说明

**三种配置文件的格式相同**

```
<?xml version="1.0" encoding="UTF-8"?>
<SysOptions xmlns="http://www.snsoft.com.cn/schema/SysOption">
	<SysOption>
        <Key>SN-HELP.INITIALDATE</Key>	//键值
        <Desc>系统上线初始化日期</Desc>		//这个系统选项描述，注意：一定要有清晰的描述说明
        <SqlType>91</SqlType>			//系统选项数据类型
        <Flags>6</Flags>       	 		//1:生产环境检查(生产环境下重点检查的系统选项)
        								//2:需要重启(此系统选项需要重启服务才能生效，直接修改不生效)
        								//4:文件选项(此系统选项仅支持XML配置文件不支持数据库配置)
        <AidInfo>						//辅助录入
            <aidinputer>Xjs.ui.DateAidInputer.getDefaultInputer</aidinputer>
        </AidInfo>
    </SysOption>
	<SysOption>
		<Key>SN-HELP.MONEY</Key>		//键值
		<Desc>小数点</Desc>				//这个系统选项描述
		<SqlType>12</SqlType>			//系统选项数据类型
        <Flags>6</Flags>		
		<DefaultValue>2</DefaultValue>	//默认值，注意：要设置一个尽量合理的默认值
	</SysOption>
</SysOptions>
```

#### 系统选项配置界面

**系统选项**
说明：可以查看系统选项文件配置和数据库配置的值，但是不能修改，只能修改文件才能对选项值修改
<img src='help/SN-CMC/SysOpTionsFile/img/SYSOPTIONSFILE1.png' />
说明：可以查看系统选项数据库的值，可以直接修改
<img src='help/SN-CMC/SysOpTionsFile/img/SYSOPTIONSFILE2.png' />
**系统选项（商户）**
<img src='help/SN-CMC/SysOpTionsFile/img/SYSOPTIONSFILE3.png' />
**系统选项（用户）**
<img src='help/SN-CMC/SysOpTionsFile/img/SYSOPTIONSFILE4.png' />
**系统选项（组织）**
<img src='help/SN-CMC/SysOpTionsFile/img/SYSOPTIONSFILE5.png' />

### 优先级说明

1. 数据库
1. System.getProperties()+System.getenv();
1. *Option*.xml;

### 手册

#### 客户端使用

禁止客户端发远程获取系统选项值，正确的方法为：
1、界面配置UI监听：snsoft.bas.ui.SysOptionUIListener.new；
2、客户端通过表进行获取：xjs.Table.getOption...；
3、参见Dmeo[客户端系统选项](uiinvoke/00/zh_CN/theme0/SN-HELP.Util.SysOptions.html)；

#### 开发规范

##### Key命名规则

([sysid].)[module].name
[sysid]：系统号；
[moduel]：模块号；
name：名称

##### 配置文件

```XML
...
<SysOption>
	<Key></Key>
	<SqlType></SqlType>
	<DefaultValue></DefaultValue>
	<Flags></Flags>
	<AidInfo options1="" options2="" options4="">
		<codedata></codedata>
		<aidinputer></aidinputer>
		<cmprops></cmprops>
		<aiprops></aiprops>
	</AidInfo>
	<Desc></Desc>
</SysOption>
...

```
* Key：系统选项关键字
* SqlType：选项值类型，参见java.sql.Types
	* 12：文本
	* 4：整数
	* 2：数值
	* 16：布尔
	* 91：日期
	* 93：时间
* Flags：标识位
	* 1=生产环境检查：生产环境需要特别注意检查此选项的配置；
	* 2=需要重启：修改后需要重启才能生效；
	* 4=文件选项：仅支持在文件中定义，数据库中定义不起作用；
* DefaultValue：选项默认值
* Desc：选项描述
* AidInfo：辅助录入

```
options1:
	默认显示名称：
	0x10 16 - 显示码
	0x20 32 - 显示名
	0x40 64 - 显示全名
options2:
	1: 下拉方式 ;
	2: 按钮方式  ;
	4: 大文本(辅助录入);
	8: 多选;
options4:
	1: 分级
	2: 可选中间级

codedata：
码表定义，格式为：
	0.常量：
	  10:男人;20:女人;30:男妖;40:女妖;50:双性
	1.带内码参数：
	  #dictinfo;dicticode=xx.yyyy
	      或 #DT_xx.yyyy
	2.theme0码表；
	  #wcode
	3.配置辅助录入【js:new ...】；
	4.配置辅助录入【js:type:...】；

aidinputer：
辅助录入：
	1、日期：Xjs.ui.DateAidInputer.getDefaultInputer()；
	2、时间：Xjs.ui.DateAidInputer.getDefaultInputer(7)；
	3、双日期：new Xjs.ui.Date2AidInputer()；
	4、大文本：new Xjs.ui.MutilineTextInputer.newInputer()；
	5、弹出框：new Xjs.ui.SelectCodeDialog({selOptions:65537})；
	6、下拉框：new Xjs.ui.ComboAidInputer({selOptions:65537})；
	7、分级框：new Xjs.ui.SelectLevlCodeDialog()；
	8、颜色：renderer:{cellRender:Xjs.ui.ColorAidInputer.colorRenderer},aidInputer:Xjs.ui.ColorAidInputer.newAidInputer()；

cmprops：
码表参数，使用JSON格式，如：
	type:"Hello",num:1
	不需要外部的大括号，程序中自动添加。

aiprops：
辅助录入属性，使用JSON格式，如：
	type:"Hello",num:1
	不需要外部的大括号，程序中自动添加。
	
用法实例：
>
<codedata id="servers" ui.options4="0x020"  expl="所有的服务ID组成的码表" 
		ui.aiprops="selOptions2:8,promptInfo:'',selOptions:1" ui.cmprops="num:1" aidinputer="">
		<tacloader>
			<![CDATA[
				proc loadCodeData(def,params)
					list = snsoft.commons.util.BasConfig.getAllServerIDs()
					a = new java.lang.String[list.size()][2]
					it = it(list)
					while it.hasNext()
						id = it.next()
						a[it.idx][0] = id
						a[it.idx][1] = id
					end while
					return a
				end proc
			]]>
		</tacloader>
		<column name="id" type="12" title="服务ID" width="200" primkey="true" />
		<column name="name" type="12" title="服务名称" width="300" />
	</codedata>
```
### 相关类图

@startuml
interface SysConfig {
   + Object getSysOption(String optid);
   + void setParentConfig(SysConfig parent); 
}
interface UcodeConfig {
   + Object getByUserCode(String usercode, String optid);
}
interface BcodeConfig {
   + Object getByBusiBcode(String cuicode, String busibcode, String optid);
   + Object getByCorpBcode(String cuicode, String corpbcode, String optid);
   + Object getByKeptBcode(String cuicode, String keptbcode, String optid);
   + Object getByBusiCorp(String cuicode, String busibcode, String corpbcode, String optid);
   + Object getByBusiKept(String cuicode, String busibcode, String keptbcode, String optid);
   + Object getByCorpKept(String cuicode, String corpbcode, String keptbcode, String optid);
}
class DefaultSysConfig {
	# MapEntry<String,Object>[] loadThisOptions(String configDataSource);// 从文件获取选项
}
class BasConfig {
	# DefaultSysConfig basSysConfig;
}
class AppSysOptions {
	# MapEntry<String,Object>[] loadThisOptions(String configDataSource);// 从数据库获取选项
}
class AppUcodeOptions {
	# MapEntry<String,Object>[] loadThisOpts(Database configDB, String typecode);// 从数据库获取选项
}
class AppBcodeOptions {
	# MapEntry<String,Object>[] loadThisOpts(Database configDB, String typecode);// 从数据库获取选项
}

note as N1
	 应用启动时将从配置文件中解析的选项
	 设置到basSysConfig中，包括：
	1.System.properties;
	2.Spring-Configs*.xml;
	3.SysConfig*.xml;
end note

BasConfig .. N1

SysConfig <-- DefaultSysConfig
IBusiConfig <-- AppSysOptions
DefaultSysConfig <-- AppSysOptions
IBusiConfig <-- UcodeConfig
UcodeConfig <-- AppUcodeOptions
IBusiConfig <-- BcodeConfig
BcodeConfig <-- AppBcodeOptions
BasConfig ..> DefaultSysConfig
AppUcodeOptions ..> DefaultSysConfig
AppBcodeOptions ..> DefaultSysConfig

@enduml

### 开发流程

1. 添加系统选项配置文件，注意：配置文件必须添加，不能加个系统选项的Key就结束了，另外配置文件中要有清晰的说明(Desc)并正确设置标识位(Flags)。
1. 添加针对某个应用的系统选项服务，每个方法对应一个系统选项，方便查找系统选项的使用情况，不要直接使用底层提供的获取系统选项的方法。
1. 在应用中使用系统选项服务获取选项值。

#### 系统选项【商户】

**注意**
```properties
配置文件SysOption节点含有属性type，用于指定选项类型，用于区分系统选项和商户选项，为C时表示选项为商户选项，为S或者空时表示选项为系统选项。
此属性仅用于维护界面数据过滤使用。
```
<img src='help/SN-CMC/SysOpTionsFile/img/type.png' />


查询和配置当前商户的系统选项。未启动商户选项，该功能不能使用。
```
配置文件位置：resources/snsoft/app/SysOption**.xml
结构和标准系统选项相同，支持默认值
```
功能调用：
```
	/**
	 * 获取商户选项
	 * @param cuicode 商户码，不能为空
	 * @param optid 系统选项key，不能为空
	 * @return
	*/
	snsoft.commons.config.SysConfig.impl.getCuiSysOption(String cuicode, String optid)
```

#### 系统选项【用户】

选项针对用户

```
配置文件位置：resources/snsoft/app/UcodeOption**.xml
结构和标准系统选项相同，支持默认值
```
功能调用：
```
	/**
	 * 获取商户选项
	 * @param cuicode 商户码，不能为空
	 * @param optid 系统选项key，不能为空
	 * @return
	*/
	snsoft.commons.config.UcodeConfig.impl.getByUserCode(String usercode, String optid)
```


#### 系统选项【组织】

查询和配置当前商户不同组织的系统选项。未启动商户选项，则只针对组织。

```
配置文件位置：resources/snsoft/app/BcodeOption**.xml
结构和标准系统选项相同，支持默认值
```
功能调用：
```
    /**
     * <pre>
     * 获取部门选项
     * </pre>
     * @param busibcode 部门编码
     * @param optid
     * @return
     */
    snsoft.commons.config.BcodeConfig.impl.getByBusiBcode(String busibcode, String optid);

	/**
	 * <pre>
	 * 获取部门选项
	 * </pre>
	 * @param cuicode 商户编码
	 * @param busibcode 部门编码
	 * @param optid
	 * @return
	 */
	snsoft.commons.config.BcodeConfig.impl.getByBusiBcode(String cuicode, String busibcode, String optid);

	/**
	 * <pre>
	 * 获取公司选项
	 * </pre>
	 * @param corpbcode
	 * @param optid
	 * @return
	 */
	snsoft.commons.config.BcodeConfig.impl.getByCorpBcode(String corpbcode, String optid);

	/**
	 * <pre>
	 * 获取公司选项
	 * </pre>
	 * @param cuicode
	 * @param corpbcode
	 * @param optid
	 * @return
	 */
	snsoft.commons.config.BcodeConfig.impl.getByCorpBcode(String cuicode, String corpbcode, String optid);

	/**
	 * <pre>
	 * 获取财务选项
	 * </pre>
	 * @param keptbcode
	 * @param optid
	 * @return
	 */
	snsoft.commons.config.BcodeConfig.impl.getByKeptBcode(String keptbcode, String optid);

	/**
	 * <pre>
	 * 获取财务选项
	 * </pre>
	 * @param cuicode
	 * @param keptbcode
	 * @param optid
	 * @return
	 */
	snsoft.commons.config.BcodeConfig.impl.getByKeptBcode(String cuicode, String keptbcode, String optid);

	/**
	 * <pre>
	 * 获取部门公司选项
	 * </pre>
	 * @param busibcode
	 * @param corpbcode
	 * @param optid
	 * @return
	 */
	snsoft.commons.config.BcodeConfig.impl.getByBusiCorp(String busibcode, String corpbcode, String optid);

	/**
	 * <pre>
	 * 获取部门公司选项
	 * </pre>
	 * @param cuicode
	 * @param busibcode
	 * @param corpbcode
	 * @param optid
	 * @return
	 */
	snsoft.commons.config.BcodeConfig.impl.getByBusiCorp(String cuicode, String busibcode, String corpbcode, String optid);

	/**
	 * <pre>
	 * 获取部门财务选项
	 * </pre>
	 * @param busibcode
	 * @param keptbcode
	 * @param optid
	 * @return
	 */
	snsoft.commons.config.BcodeConfig.impl.getByBusiKept(String busibcode, String keptbcode, String optid);

	/**
	 * <pre>
	 * 获取部门财务选项
	 * </pre>
	 * @param cuicode
	 * @param busibcode
	 * @param keptbcode
	 * @param optid
	 * @return
	 */
	snsoft.commons.config.BcodeConfig.impl.getByBusiKept(String cuicode, String busibcode, String keptbcode, String optid);

	/**
	 * <pre>
	 * 获取公司财务选项
	 * </pre>
	 * @param corpbcode
	 * @param keptbcode
	 * @param optid
	 * @return
	 */
	snsoft.commons.config.BcodeConfig.impl.getByCorpKept(String corpbcode, String keptbcode, String optid);

	/**
	 * <pre>
	 * 获取公司财务选项
	 * </pre>
	 * @param cuicode
	 * @param corpbcode
	 * @param corpbcode
	 * @param optid
	 * @return
	 */
	snsoft.commons.config.BcodeConfig.impl.getByCorpKept(String cuicode, String corpbcode, String keptbcode, String optid);
```


### 系统选项分类

由于系统选项包含系统运行过程中需要的所有系统配置，部分配置需要开发设置，部分配置由运维或用户自行设置，统一在系统选项页面展示并不友好，查询设置并不方便。
系统选项分类提取部分系统选项， 分类、分场景展示，不同角色用户在不同界面设置相关选项。

配置说明【[系统选项分类](help.html?helpFile=help/SN-CMC/ClassOption/ClassOptions.md)】