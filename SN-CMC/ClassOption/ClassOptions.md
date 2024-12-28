## 系统选项分类控制

|日期|作者|备注|
|------|------|------|
|2019-12-16|冯继亮|整理|

### 指南

#### 配置文件命名

```
格式：ClassOption[sysid].xml
以ClassOption开头加系统模块号；
如ClassOption（固定）+SN-HELP.xml（模块号）=》ClassOptionSN-HELP.xml
```

#### 文件存放路径

```
当前模块的UI工程resources/cfg/res/options/下
示例：/help-ui/src/main/resources/cfg/res/options/ClassOptionSN-HELP.xml
```

#### 内容说明

```
<?xml version="1.0" encoding="UTF-8"?>
<ClassOptions xmlns="http://www.snsoft.com.cn/schema/tool-options" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.snsoft.com.cn/schema/tool-options http://www.snsoft.com.cn/schema/tool-options.xsd" >
	<!--UserGroup:用户选项  PlatGroup:平台选项 CMGroup:商户选项 CMBGroup:商户部门系统选项-->
	<CMGroup>
		<!-- grpcode:分组编码（多级按.截取组织树状结构） title：分组名称 -->
		<Groups grpcode="test" title="商户选项树"></Groups>  
		<Groups grpcode="test.btn" title="按钮选项">
			<!-- optid:系统选项编码（和sysoption配置保持一致） title：选项名称  sheetcode：适用单据，多个用,号分割 -->
			<Options optid="Test.btn.b1" title="常量-按钮-单选-文本" sheetcodes="SN-HELP.Salorder"></Options>
			<Options optid="Test.btn.b2" title="常量-按钮-多选-文本" sheetcodes="SN-HELP.Salorder"></Options>
		</Groups>
	</CMGroup>
</ClassOptions>
```

#### 系统功能

界面右键监听【选项配置】:查看适用当前单据的系统选项

```
 <Groups id="选项分类">
    <Functions>
        <Functype>SN-PLAT.JS</Functype>
        <Subtype>2</Subtype>
        <Funcimpl><![CDATA[
                 #new snsoft.plat.bas.opt.ClsOptPopupMenuJSListener({sheetcode:"单据号",tgtUINames:["uiname"]})
                 ]]></Funcimpl>
        <Remark>选项配置右键菜单监听</Remark>
    </Functions>
</Groups>
```

### 手册

#### 系统选项分类界面

**选项分类**
说明：查看所有选项分类组
界面：[选项分类](uiinvoke/00/zh_CN/theme0/90.Option.ClassOptionGrp.html)
**选项定义**
说明：查看所有选项定义明细（包括配置方式、辅助录入、默认值等），及选项的适用单据
界面：[选项定义](uiinvoke/00/zh_CN/theme0/90.Option.ClassOptionDef.html)
**选项配置【平台】**
说明：设置平台系统选项，保存到系统选项表
界面：[选项配置【平台】](uiinvoke/00/zh_CN/theme0/90.Option.ClassOptionPlat.html)
**选项配置【商户】**
说明：设置商户系统选项，保存到系统选项表
界面：[选项配置【商户】](uiinvoke/00/zh_CN/theme0/90.Option.ClassOptionCui.html)
**选项配置【商户+部门+公司】**
说明：设置商户部门系统选项，保存到系统选项表
界面：[选项配置【商户+部门+公司】](uiinvoke/00/zh_CN/theme0/90.Option.ClassOptionCuiB.html)
**选项展示**
说明：单据右键菜单【选项配置】打开页面，按单据过滤出相关选项
界面：[选项展示](uiinvoke/00/zh_CN/theme0/90.Option.ClassOptionUser.html)

<span style="color:red">注：用户系统选项默认需要在主框架的右上角展示（需要修改主框架页面）</span>
<img src='help/SN-CMC/ClassOption/img/UserOptSetting.png' />

### 设计

#### 背景说明

1.管控中的系统选项，按系统划分展示所有系统选项，部分选项主要是由开发设置的，运维人员及用户在检索、查看、设置时不需要看到所有系统选项。
1.用户在做单时需要看到影响当前单据的系统选项。

#### 配置说明

1.根据不同的使用范围（用户、系统、商户、商户部门）对系统选项进行分组。对单据界面需要查看的系统选项，需要配置上适用单据。
1.不同的用户根据角色不同可以设置不同组的系统选项，如平台管理员设置系统级系统选项，商户管理员设置商户及商户部门选项等。