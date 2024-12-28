## 单据打印

|日期|作者|备注|
|------|------|------|
|2018-09-29|张东|初稿|
|2019-10-12|张东|调整|

### 指南

#### 功能说明

1. 单据打印格式用于在单据号与打印格式之间建立一种关联关系，使得更加的方便实施。
1. 取数页面：要求必须配置，可以是单据详情页面，也可以是单独用于打印的界面（建议后者）。
1. 单据打印格式的编码【sysdefs.defid】必须以单据号为前缀；

#### 使用说明
##### 监听配置

1. 界面直接配置
    不使用系统功能时，直接配置在界面定义上：
    ```xml
    <uilisteners><![CDATA[
        snsoft.plat.bas.sheet.print.SheetPrintListener.new?sheetCode=[当前单据号]
    ]]></uilisteners>
    <jslistener><![CDATA[
        #new snsoft.plat.bas.sheet.print.SheetPrintListener({})
    ]]></jslistener>
    ```

1. 系统功能配置
    使用系统功能时配置在系统功能上：
    ```xml
    <Groups id="单据打印">
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>snsoft.plat.bas.sheet.print.SheetPrintListener.new?sheetCode=[当前单据号]</Funcimpl>
            <Remark>单据打印:UI监听</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.plat.bas.sheet.print.SheetPrintListener({})</Funcimpl>
            <Remark>单据打印:JS监听</Remark>
        </Functions>
    </Groups>
    ```

##### 关系配置

```properties
单据打印格式关系配置支持数据库+文件的方式。
维护界面：业务平台->功能组件->单据打印格式关系
```

* 配置文件

本配置归配置系统管理。
文件存放目录：res/sheetprint/单据号.xml

配置示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SheetPrintDef xmlns="http://www.snsoft.com.cn/schema/SheetPrintDef.xsd">
    <prtmuiid>SN-ORD.SalorderDetailAttachBtn</prtmuiid>
    <PrintDef>
        <idx>10</idx>
        <deftype>7</deftype>
        <defid>erp.order</defid>
        <ucode>U001</ucode>
        <utype>G</utype>
        <tacformula>333</tacformula>
        <bedate>2018-09-19</bedate>
        <ledate>2020-09-19</ledate>
    </PrintDef>
</SheetPrintDef>
```

* 数据库

直接在维护界面维护即可，维护界面**《业务平台》->《功能组件》->《单据打印格式关系》**

##### 在线预览

打印出的Office文档在线预览目前需要Office Online Server服务配合使用，并且Office服务器需要暴露在公网。

* 服务端（必须配置）：

```properties
系统选项 OfficeOnline.ServerURL=http://[host]
```

* 客户端：

```properties
#默认按钮：
系统选项配置 OfficeOnline.ViewAttach=true

#自定义按钮方式：
打印配置增加isprintviewmod=true
config.$set("isprintviewmod", true);
```

### 手册

打印格式定义见文档 **[打印格式](help.html?helpFile=help/SN-CMC/Print/PrintDef.md)**

### 类图

@startuml
Title 单据打印类图

sv.SheetPrintService <.. ui.SheetPrintListener
ui.SheetPrintListener <..> xjs.SheetPrintListener
ui.PrintDefDefnameListener <.. xjs.PrintDefDefnameListener
xjs.PrintDefDefnameListener <.. xjs.DefidRender

interface sv.SheetPrintDefService {
	// 单据打印定义服务
	+ QueryResults<SheetPrintmui> queryUI();
	+ QueryResults<SheetPrint> queryDetailUI();
	+ SaveResults saveUI();
}

interface sv.SheetPrintService {
	// 单据打印公共服务
	+ Map<Integer,List<String>> getPrintNames();
}

class ui.SheetPrintListener {
	// 单据打印UI功能监听
	+ void initComponent();
	+ void getPrintFormatNames();
}

class xjs.SheetPrintListener {
	// 单据打印JS功能监听
	+ void prepareLoadPrtfmtNames();
	+ void beforeTablePrint();
}

class ui.PrintDefDefnameListener {
	// 打印格式名称UI监听
	+ ReadDataSet onDataLoaded();
}
class xjs.PrintDefDefnameListener {
	// 打印格式名称JS监听
	+ void initComponent();
	+ void itemAidInputing();
}

class xjs.DefidRender {
	// 打印格式名称显示类
	+ Object cellRender();
}

@enduml

### 时序图

@startuml
Title 单据打印时序图

actor 客户端

participant jslis   as "xjs.SheetPrintListener"
participant uilis   as "ui.SheetPrintListener"
participant service as "sv.SheetPrintService"

客户端 -> jslis : 
activate jslis
note right of 客户端: 加载打印格式名称
	jslis -> jslis : [prepareLoadPrtfmtNames] \n 设置内码参数
	activate jslis
	deactivate jslis
	
	jslis -> uilis : [getPrintFormatNames] \n 获取打印格式
	activate uilis
		uilis -> service : [getPrintNames] \n 获取打印格式
		activate service
		service -> uilis :
		deactivate service
	uilis -> jslis :
	deactivate uilis
jslis -> 客户端 :
deactivate jslis

客户端 -> jslis : 
activate jslis
note right of 客户端: 打印
	jslis -> jslis : [beforeTablePrint] \n 设置打印页面
	activate jslis
	deactivate jslis
jslis -> 客户端 :
deactivate jslis
@enduml
