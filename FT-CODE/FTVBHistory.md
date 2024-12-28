## 单据详情版本备份配置说明

### 界面按钮配置

1. 《版本修改》按钮
	```
	 <ToolbarBtn name="ft_trd_prj_btn_vbBackup"    noClientComponent="true" title="${RES.$.title.F.btn.vbbackup}" uiprops.cellClassName="ui-head-btn"        xprops.iconClassName="icons-btn-version" />
	```
	> 正例：
<img src='help/FT-CODE/images/FTVBHistory/0001.png' />
1. 《取消版本修改》按钮
	```
	<ToolbarBtn name="ft_trd_prj_btn_vbCancel"    noClientComponent="true" title="${RES.$.title.F.btn.vbcancel}"      uiprops.cellClassName="ui-head-btn" xprops.iconClassName="icons-btn-version" /> 
	```
	> 正例：
<img src='help/FT-CODE/images/FTVBHistory/0002.png' />
1. 表《其他属性》配置
	```
	<attrs>
            <attr type="203" name="vbBackup" title="${RES.$.title.F.btn.vbbackup}" _rlog="true" />
            <attr type="203" name="vbCancel" title="${RES.$.title.F.btn.vbcancel}" _rlog="true" />
        </attrs>
	```
1. 《界面属性控制》配置
	```
	《版本备份按钮》显示条件：操作权限+生效状态+正常版本
	<UIOpts>
		<uiname>ft_trd_prj</uiname>
		<button>vbBackup</button>
		<sheetcode>FT-TRD.TX.Prj.AgcyExpProject</sheetcode>
		<dataOpids>C</dataOpids><!-- 注意使用dataOpids -->
		<ctrlFields>hidden</ctrlFields>
		<setValue>false</setValue>
		<UIVals>
			<uiname>ft_trd_prj</uiname>
			<colname>status</colname>
			<sqltype>12</sqltype>
			<matchValue>70</matchValue>
		</UIVals>
		<UIVals>
			<uiname>ft_trd_prj</uiname>
			<colname>vsnflag</colname>
			<sqltype>4</sqltype>
			<matchValue>0</matchValue>
		</UIVals>
	</UIOpts>
```
```
	《取消版本备份》按钮显示条件：操作权限+修改状态+新版本
	<UIOpts>
		<uiname>ft_trd_prj</uiname>
		<button>vbCancel</button>
		<sheetcode>FT-TRD.TX.Prj.AgcyExpProject</sheetcode>
		<dataOpids>C</dataOpids><!-- 注意使用dataOpids -->
		<ctrlFields>hidden</ctrlFields>
		<setValue>false</setValue>
		<UIVals>
			<uiname>ft_trd_prj</uiname>
			<colname>status</colname>
			<sqltype>12</sqltype>
			<matchValue>11,15</matchValue>
		</UIVals>
		<UIVals>
			<uiname>ft_trd_prj</uiname>
			<colname>vsnflag</colname>
			<sqltype>4</sqltype>
			<matchValue>2</matchValue>
		</UIVals>
	</UIOpts>
	```
	```
	《删除》按钮控制条件：操作权限+修改状态+新版本<!-- 注意版本备份数据不允许删除，只允许取消版本备份 -->
	<UIOpts>
		<uiname>ft_trd_prj</uiname>
		<sheetcode>FT-TRD.TX.Prj.AgcyExpProject</sheetcode>
		<dataOpids>C</dataOpids> <!-- 注意使用dataOpids -->
		<ctrlFields>delable</ctrlFields>
		<setValue>true</setValue>
		<UIVals>
			<uiname>ft_trd_prj</uiname>
			<colname>status</colname>
			<sqltype>12</sqltype>
			<matchMode>4</matchMode>
			<matchValue>20</matchValue>
		</UIVals>
		<UIVals>
			<uiname>ft_trd_prj</uiname>
			<colname>vsnflag</colname>
			<negmatch>1</negmatch>
			<sqltype>4</sqltype>
			<matchValue>2</matchValue>
		</UIVals>
	</UIOpts>
	```
	
### 界面字段配置

* 详情界面查询参数
	```
<c name="vsn" title="${RES.$.vsn}" sqltype="4" />
	```
* 基本信息【系统信息】
	```
<c name="vsn" title="${RES.$.vsn}"   sqltype="4" width="${RES.FT-CODE.D.BR.CW.vsn}" rdonly="true" />
<c name="vsnflag" title="${RES.$.vsnflag}"    sqltype="4" width="60" rdonly="true" hidden="true" />
	```

### 单据状态配置

* 单据状态码表
	```
必须配置版本备份状态【11：修改】
	```

### 系统功能监听配置

* 系统功能UI
```
 <Groups id="版本备份">
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.ft.comm.vnbk.FTVersionBackupListener({sheetCode:'FT-TRD.TX.Prj.AgcyExpProject'})</Funcimpl><!--sheetCode:单据编号-->
            <Remark>版本备份</Remark>
        </Functions>
    </Groups>
```
* 系统功能SV
```
    <Functions>
        <Functype>SN-PLAT.SV</Functype>
        <Subtype>1</Subtype>
        <Funcimpl><![CDATA[
        SN-PLAT.VersionBackupFunctionListener
        ]]></Funcimpl>
        <Remark>版本备份</Remark>
    </Functions>
```

### 表结构

1. 建表文件
	* 表结构强制存在字段
```
<column name="vsn" title="版本号" type="SMALLINT"/>
<column name="vsnflag" title="版本标记" type="SMALLINT"/>
<c name="ratifydate" title="生效时间"  sqltype="91" rdonly="true" newLine="true" hidden="true"/>
<c name="curratifydate" title="生效时间"  sqltype="91" rdonly="true"/>
```
	* 版本备份表表结构
```
	 <table id="33040" name="ft_trd_prj_vb" title="项目 " datasrcid="FT-TRD_W" rdatasrcid="FT-TRD_R"   storegrp="SNFTTRDDATA_TBS">
           <columns cpFromTable="ft_trd_prj"/> 
    </table>
```
1. 字段说明
	* 版本备份表表结构vsn说明	
	```
	代表本条数据的版本号
	```
	* 版本备份表表结构vsnflag说明	
	```
	0：正常版本
	1：老版本
	2：新版本
	3：历史版本
	6：自动备份（禁止手工取消备份）（也属于新版本）
	```		
	* ratifydate字段说明	
	```
	版本号为1的数据生效时间
	```
	* curratifydate字段说明	
	```
	当前版本的数据生效时间
	```		
	* 版本备份常量类
	```
	snsoft.plat.bas.sheet.vnbk.service.VBConst
	```
1. VO实体类
	* 实体类
```
必须实现接口snsoft.plat.bas.sheet.vnbk.vo.VersionBackupVO
vsn:默认值[1],vsnflag:默认值[0]
重写方法getVsnflag()，setVsnflag(Integer vsnflag)
```
	* 查询服务接口
```
查询服务接口参数对象必须实现接口snsoft.plat.bas.sheet.vnbk.service.VBVersionParams
重写方法getInnerCode()
```

### XML配置的配置说明
1. 版本备份配置文件
	* 配置文件目录
```
src/main/resources/cfg/res/vnbk
```
	* 配置文件名称
```
单据编号.xml
```
	* 配置文件样例
```
<?xml version="1.0" encoding="UTF-8"?>
<VersionBackupDef xmlns="http://www.snsoft.com.cn/schema/VersionBackupDef" xsi:schemaLocation="http://www.snsoft.com.cn/schema/VersionBackupDef http://www.snsoft.com.cn/schema/VersionBackupDef.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Title>标题</Title>
    <Listeners><![CDATA[
        版本备份监听
    ]]></Listeners>
    <TacFormula><![CDATA[
        版本备份Tac监听
    ]]></TacFormula>
    <Remark><![CDATA[
        版本备份说明
    ]]></Remark>
    <Details>
        <NoBackupTableName>不备份表名</NoBackupTableName>
        <NoBackupColumnName>不备份列名</NoBackupColumnName>
    </Details>
    <Details>
        <NoBackupTableName>不备份表名</NoBackupTableName>
        <NoBackupColumnName>*</NoBackupColumnName>
    </Details>
</VersionBackupDef>
```
	* 业务层单据必须配置的Listeners处理
```
 #SN-PLAT.StatusVersionBackupListener<!--处理单据状态-->
 #SN-APPR.ApprVersionBackupListener<!--处理单据审批信息-->
  snsoft.plat.bas.sheet.doc.service.impl.SheetDocVersionBackupListener.new? attachCfg=主表表名:单据编号#附件表表名<!--需要处理单据附件信息时必须配置-->
  snsoft.ft.trd.comm.term.service.impl.SheetTermVersionBackupListener.new<!--需要处理单据条款信息时必须配置-->
```

## 单据详情《版本》页签配置说明

### 版本比对界面

* 界面引用配置
	```
<m:B title="版本">
<include src="SN-PLAT.VBVersion.VBHistoryEmbed#vbhistory" mainui="主界面名称"/>
</m:B>
	```
	> 正例：
<img src='help/FT-CODE/images/FTVBHistory/0003.png' />

### 单据详情查询参数配置

* 详情界面查询参数
	```
<c name="oldvsn" title="历史版本号" sqltype="4" />
	```
> 正例：
<img src='help/FT-CODE/images/FTVBHistory/0004.png' />

### 版本比对系统功能监听配置

* 系统功能UI
```
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
		#new snsoft.plat.bas.sheet.vnbk.CompareVersionJSListener({mUiName:'ft_trd_prj',ignoCmpTbls:'ft_trd_pft,apprdata'})
		]]></Funcimpl>
            <Remark>版本比对客户端监听</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
		snsoft.plat.bas.sheet.vnbk.CompareVersionUIListener.new
		]]></Funcimpl>
            <Remark>版本比对UI监听</Remark>
        </Functions>
```
* 系统功能SV
```
    	<Functions>
            <Functype>SN-PLAT.SV</Functype>
            <Subtype>1</Subtype>
            <Funcimpl>FT-TRD.COMM.VersionStatusTimeListener</Funcimpl>
            <Remark>生效时间赋值</Remark>
        </Functions>
```
	
## 底层版本备份功能及参数说明

* 参考文档
[参考文档](https://n.esnsoft.cn/N9Bhelp/help.html?helpFile=help/SN-PLAT/Sheet/VersionBackup.md)

* 参考实际业务单据
```
合作出口审批单
```