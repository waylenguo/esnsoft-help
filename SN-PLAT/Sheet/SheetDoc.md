## 单据文件存档管理

|日期|作者|备注|
|------|------|------|
|2018-09-29|张东|单据附件初稿|
|2019-05-15|张东|单据文档初稿|
|2019-09-26|张东|单据文档修改|
|2019-10-29|张东|整理|

### 说明

**单据文件存档管理总共有两个版本，分别为 单据附件 和 单据文档 ，项目中依情况使用。**
两者的主要区别在于：单据文档中的文档类型设计比单据附件的附件类型复杂，前者附件表为单据一对一，后者则是统一一张附件表维护。

### 单据附件指南
#### 单据附件功能说明

1. 此功能为业务底层的功能，需要依赖业务平台api及impl功能。
1. 附件类型不同于文件类型，附件类型一般指单据附件在对应单据的作用，比如：《电子版销售合同书》，配置格式参见【[AttachTypeDef.xsd](do/BrowseSchema?schema=AttachTypeDef.xsd)】。
1. 此功能为非必要功能，对应的单据附件中的字段也是非必填。
**注意：单据附件大小限制为服务端限制，如果使用代理转发服务器转发客户端请求可能还会存在转发服务的限制。**
**单据配置至少包括两项：表名  + 内码列名。**

#### 单据附件使用说明

单据附件分为两种应用场景：弹对话框/界面内嵌；

1. 嵌入方式

    * 说明
    
    通过将附件界面嵌入到当前窗口中进行维护。
    默认嵌入界面为：SN-PLAT.Attach.AttachEmbed
    
    ```xml
    <!-- 嵌入界面写法-->
    <include src="SN-PLAT.Attach.AttachEmbed#attach" mainui="salorder" />
    ```
    
    * 系统功能配置
    
    ```xml
    <Groups id="单据附件">
        <Functions>
            <Functype>SN-PLAT.SV</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>snsoft.plat.bas.sheet.attach.service.impl.SheetAttachEmbedSaveListener.new?sheetCode=[当前单据号]</Funcimpl>
            <Remark>单据附件：服务层调用附件服务</Remark>
        </Functions>
        <Functions>
            <Functype>SN-PLAT.UI</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>snsoft.plat.bas.sheet.attach.SheetAttachEmbedSaveListener.new</Funcimpl>
            <Remark>单据附件：UI层处理附件VO</Remark>
        </Functions>
    </Groups>
    ```

1. 弹出式

    * 说明
    
    通过界面按钮弹出附件维护对话框，只需要一个客户端监听即可。
    默认弹出界面为：SN-PLAT.Attach.AttachPopup
    
    * 配置
    
    ```xml
    <!--1、JS监听：配置弹出按钮的界面上，可以通过界面直接配置，也可以通过系统功能进行配置-->
    <!--界面配置-->
    <jslistener>
    #new snsoft.plat.bas.sheet.attach.SheetAttachPopupListener({sheetCode:'',attachmuiid:''})
    </jslistener>
    
    <!--系统功能配置-->
    <Groups group="单据附件">
        <Functions>
            <Functype>SN-PLAT.JS</Functype>
            <Subtype>2</Subtype>
            <Funcimpl>#new snsoft.plat.bas.sheet.attach.SheetAttachPopupListener({sheetCode:'',attachmuiid:''})
            </Funcimpl>
            <Remark>单据附件：弹出附件对话框</Remark>
        </Functions>
    </Groups>
    
    <!-- 2、SV监听：服务存盘监听，用于处理主表存盘后删除对应的附件记录-->
    <!-- 系统功能配置-->
    <Groups id="单据附件">
        <Functions>
            <Functype>SN-PLAT.SV</Functype>
            <Subtype>2</Subtype>
            <Funcimpl><![CDATA[
            snsoft.plat.bas.sheet.attach.service.impl.SheetAttachSaveListener.new?sheetCode=[当前单据号]
            ]]></Funcimpl>
            <Remark>单据附件：主表删除删除附件表</Remark>
        </Functions>
    </Groups>
    ```

### 单据附件手册
### 单据附件设计
#### 单据附件类图

@startuml
Title 附件类型类图

interface AttachTypeDefService {
	// 单据附件类型维护服务
	List<AttachTypeDef> queryUI();
	void saveUI();
	List<AttachType> queryDetailUI();
	void syncToDB(String atcode);
}
class xjs.PlatAttachAttachTypeListener {
	+ void itemAidInputing();
}
class ui.PlatAttachAttachTypeListener {
	+ Object getCodeNameMap();
}
interface AttachTypeService {
	+ String getAtcode(String sheetCode, String innerCode);
}
@enduml

@startuml
Title 弹出式单据附件类图

xjs.PlatAttachLoadListener <|-- xjs.PlatAttachPopupLoadListener

sv.PlatAttachService <.. sv.SheetAttachSaveListener
sv.PlatAttachService *--> sv.PlatAttachServiceListener

interface sv.PlatAttachService {
	QueryResults<PlatAttach> queryUI();
	SaveResults saveUI();
	void delete(String sheetCode, String innerCode);
	void update(String sheetCode, String innerCode);
}

class sv.PlatAttachServiceListener {
	// 附件服务系统功能监听类
	+ List<PlatAttach> executeQuery();
	+ void afterQuery();
	+ void beforeSave();
	+ void afterSave();
}

class sv.SheetAttachSaveListener {
	// 单据附件：单据系统功能监听类（弹出式）
	+ void afterSave();
}

class xjs.PlatAttachLoadListener {
	+ void dataLoaded();
}

class xjs.PlatAttachPopupLoadListener {
	+ void dataSetSaving();
}

class xjs.SheetAttachPopupListener {
	# String attachmuiid;
	# String sheetCode;
	
	# void oncmd_sheetattach();
}
@enduml

@startuml
Title 嵌入式单据附件类图

sv.SheetAttachSaveListener <|-- sv.SheetAttachEmbedSaveListener

xjs.PlatAttachLoadListener <|-- xjs.PlatAttachEmbedLoadListener

sv.PlatAttachService <.. sv.SheetAttachEmbedSaveListener

sv.PlatAttachService *--> sv.PlatAttachServiceListener

interface sv.PlatAttachService {
	QueryResults<PlatAttach> queryUI();
	SaveResults saveUI();
	void delete(String sheetCode, String innerCode);
	void update(String sheetCode, String innerCode);
}

class sv.PlatAttachServiceListener {
	// 附件服务系统功能监听类
	+ List<PlatAttach> executeQuery();
	+ void afterQuery();
	+ void beforeSave();
	+ void afterSave();
}

class sv.SheetAttachSaveListener {
	// 单据附件：单据系统功能监听类（弹出式）
	+ void afterSave();
}

class sv.SheetAttachEmbedSaveListener {
	// 单据附件：单据系统功能监听类（嵌入式）
	+ void afterSave();
}

class ui.SheetAttachEmbedSaveListener {
	// 单据附件：单据UI监听类（嵌入式）
	+ void onDataSaving();
}

class xjs.PlatAttachLoadListener {
	+ void dataLoaded();
}

class xjs.PlatAttachEmbedLoadListener {
	+ void dataSetRefreshing();
	+ void dataSetSaving();
}
@enduml

#### 单据附件时序图

@startuml
Title 单据附件弹出式时序图

actor 客户端

participant jslis    as "PlatAttachPopupLoadListener"
participant service  as "PlatAttachService"
participant platlis  as "PlatAttachServiceListener"
participant sheetlis as "SheetAttachSaveListener"

客户端 -> jslis : 附件数据加载
activate jslis
note right of 客户端: 加载附件数据
	jslis -> service : [queryUI]
	activate service
		service -> platlis : [executeQuery] \n校验权限+加载数据
		activate platlis
		platlis -> service
		deactivate platlis
		
		service -> platlis : [afterQuery] \n设置权限+附件类型编码
		activate platlis
		platlis -> service
		deactivate platlis
	service -> jslis
	deactivate service
jslis -> 客户端 :
deactivate jslis

客户端 -> jslis : [dataSetSaving] \n附件数据存盘 \n设置存盘参数
activate jslis
note right of 客户端: 保存附件数据
	jslis -> service : [saveUI]
	activate service
		service -> platlis : [beforeSave] \n校验权限+文件数据处理
		activate platlis
		platlis -> service
		deactivate platlis
		
		service -> platlis : [afterSave] \n设置客户端显示值
		activate platlis
		platlis -> service
		deactivate platlis
	service -> jslis
	deactivate service
jslis -> 客户端 :
deactivate jslis

客户端 -> sheetlis : [afterSave] \n单据主表存盘
activate sheetlis
note right of 客户端: 保存主表数据
	sheetlis -> service : [delete] \n主表删除则附件删除
	activate service
	service -> sheetlis
	deactivate service
	
	sheetlis -> service : [update] \n主表修改则附件修改
	activate service
	service -> sheetlis
	deactivate service
sheetlis -> 客户端 :
deactivate sheetlis
@enduml

@startuml
Title 单据附件嵌入式时序图

actor 客户端

participant jslis    as "PlatAttachEmbedLoadListener"
participant uilis as "ui.SheetAttachEmbedSaveListener"
participant svlis as "sv.SheetAttachEmbedSaveListener"
participant service  as "PlatAttachService"
participant platlis  as "PlatAttachServiceListener"

客户端 -> jslis : [dataSetRefreshing] \n设置查询参数
activate jslis
note right of 客户端: 加载附件数据
	jslis -> service : [queryUI]
	activate service
		service -> platlis : [executeQuery] \n校验权限+加载数据
		activate platlis
		platlis -> service
		deactivate platlis
		
		service -> platlis : [afterQuery] \n设置权限+附件类型编码
		activate platlis
		platlis -> service
		deactivate platlis
	service -> jslis
	deactivate service
jslis -> 客户端 :
deactivate jslis

客户端 -> jslis : [dataSetSaving] \n设置存盘参数
activate jslis
note right of 客户端: 保存业务数据
	jslis -> uilis : [onDataSaving] \n将附件VO设置为存盘参数
	activate uilis
		uilis -> svlis : [afterSave]
		activate svlis
			svlis -> service : [saveUI]
			activate service
				service -> platlis : [beforeSave] \n校验权限+文件数据处理
				activate platlis
				platlis -> service
				deactivate platlis
				
				service -> platlis : [afterSave] \n设置客户端显示值
				activate platlis
				platlis -> service
				deactivate platlis
			service -> svlis
			deactivate service
		svlis -> uilis
		deactivate svlis
	uilis -> jslis
	deactivate uilis
jslis -> 客户端 :
deactivate jslis
@enduml
### 单据附件场景

附件类型结构控制简单，使用底层统一表维护附件。

示例：见帮助中心《销售合同》示例。

### 单据文档指南

#### 文档类型定义

界面：[文档类型定义](uiinvoke/00/zh_CN/theme0/SN-PLAT.Doc.DocTypeDef.html)
单据号：SN-PLAT.DocTypeDef
权限字段：doctdcode 一般配置 %
作用：用于定义单据对应的文档类型。

**文档类型最终是为了出单据附属的附件，本功能要求每套功能（即一个单据）对应一套附件表，在文档类型适用单据的定义中配置好附件表对应的VO，程序根据配置的vo来处理字段和表名；除此之外，需要在附件表VO上使用@SheetInfo注解标识主单据（真实单据号），用于附件操作权限控制。**
**注意：附件VO需继承底层默认VO：snsoft.plat.bas.sheet.doc.vo.SheetDoc**

1. 初始化配置文件

* 配置文件位置：snsoft/init/doctype/xxx.xml
* 配置示例：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<DocTypeDef xmlns="http://www.snsoft.com.cn/schema/DocTypeDef" xsi:schemaLocation="http://www.snsoft.com.cn/schema/DocTypeDef http://www.snsoft.com.cn/schema/DocTypeDef.xsd"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<Doctdcode>S00001</Doctdcode>
	<DoctdName>默认使用的文档类型</DoctdName>
	<Status>70</Status>
	<Dftflag>1</Dftflag>
	<Remark></Remark>
	<DocTypes>
		<SortIdx>10</SortIdx>
		<DoctCode>001</DoctCode>
		<DoctName>手工上传附件</DoctName>
		<DoctType>30</DoctType>
		<ManualFlag>20</ManualFlag>
	</DocTypes>
</DocTypeDef>
```

##### 可用Tac方法

* TAC默认支持的方法：

```properties
（1）proc getDocTypeSubmit(refData)：
  若定义，执行该函数返回提交必须存在的文档类型编码，多个用逗号隔开，若“提交必须存在的文档类型”不为空，则二值需去重合并后做为提交必须存在的文档类型编码进行检查；

（2）proc getDocTypeFilter(refData)：
  若定义，执行该函数返回过滤掉的文档类型编码，多个用逗号隔开，若“过滤掉的文档类型”不为空，则二值需去重合并后做为过滤掉的文档类型编码在中单据中进行过滤后再显示；

（3）proc getDocTypeAutoSubmit(refData)：
  若定义，执行该函数返回提交自动生成的文档类型编码，多个用逗号隔开，若“提交自动生成的文档类型”不为空，则二值需去重合并后做为提交自动生成的文档类型编码；

（4）proc getDocTypeAutoEffect(refData)：
  若定义，执行该函数返回生效自动生成的文档类型编码，多个用逗号隔开，若“生效自动生成的文档类型”不为空，则二值需去重合并后做为生效自动生成的文档类型编码；

（5）proc executeForAppr(refData)：
  任何一点审批通过后调用（特殊要求可以在这里实现）；

（6）proc executeForStatus(refData)：
  单据状态改变后调用（特殊要求可以在这里实现）；

（7）proc beforPrint(refData,doctype,queryP)：
  打印格式类型附件打印前调用，入参为：refData 为当前业务数据(HashMap)；doctype 当前打印格式附件对应的文档类型编码；queryP打印格式主表数据查询条件(HashMap)，默认内置当前单据主键为查询条件；
  使用场景说明：

    注意：如果下游单据需要从当前单据查询对应附件，此时返回的条件中必须包含下游单据的单据号，没有下游单据时填写当前单据的单据号；

    a）只打印出一个附件：可以直接操作修改queryP，如若想清理参数可使用queryP.remove("字段名")删除，想要修改条件使用queryP.put("字段名","字段值")设置；若定义，执行该函数返回处理后的queryP；
    示例：snsoft.commons.util.StrUtils.isStrIn("005,006,007,008", doctype)=true/false 工具类方法，意思是判断doctype是否在"005,006,007,008"中
        proc beforPrint(refData,doctype,queryP)
            if "005".equals(doctype)
                mpticode = refData.get("mpticode")
                queryP.put("pticode",mpticode)
                queryP.put("sheetcode","下游单据的单据号")//此附件需要下游单据查询时必须设置
                return queryP
            end if
        end proc
    b)打印出多个附件（通常为打印某一单据的子表相关数据，一条对应一个附件）：返回每一条子表条件（结构为数组或List）；
    注意：打印格式是不能以单据的子表为取数主表来多次打印的，此时需要重新构建一个用于打印格式的取数界面，取数界面以当前单据的子表为主表。
    示例：
        proc beforPrint(refData,doctype,queryP)
        	if "005".equals(doctype)
                queryPs = newArrayList()
                //获取子表数据
        		gds = refData.get("salorderg")
                //获取迭代器
                it=it(gds)
                while it.hasNext()
                    qp = newHashMap()
                    //转换成Map
                	o=voToMap(it.next())
                    //设置条件
                    qp.put("salordgicode",o.get("salordicode"))
                    qp.put("sheetcode","下游单据的单据号")//此附件需要下游单据查询时必须设置
                    queryPs.add(qp)
                end while
                //返回组织好的子表数据查询条件List
        		return queryPs
        	end if
        end proc
（8）proc afterPrint(refData,doctype,data)：
  打印格式类型附件打印完成后调用，入参为：refData 为当前业务数据(HashMap)；doctype 当前打印格式附件对应的文档类型编码；data打印格式产生的文件(byte[])，若定义，执行该函数返回处理后的打印文件(byte[])；

refData为当前业务数据(HashMap)。
```

TAC方法示例：
```properties
proc getDocTypeSubmit(refData)
    ccode = refData.get("ccode")  
	doctypes = null
	if "10".equals(ccode)
		doctypes="003"
	end if
	if "20".equals(ccode)
		doctypes="005"
	end if
	return doctypes
end proc

proc executeForStatus(refData)
    ccode = refData.get("ccode")  
	println("状态改变为"+refData.get("status"))
	doctcodes = getDocTypeOther("Salorder.DocType.01","001")
	println(doctcodes)
end proc

proc afterPrint(refData,doctype,data)
	if "004".equals(doctype)
		params = com.google.common.collect.Lists.newArrayList()
		param = snsoft.plat.demo.ord.kgisignature.ISignatureParams.buildDefaultSealParam("合计")
		params.add(param)
		param = snsoft.plat.demo.ord.kgisignature.ISignatureParams.buildDefaultQfzParam()
		params.add(param)
		return snsoft.plat.demo.ord.kgisignature.PDFKGISignatureUtils.electronicSeal(data, params.toArray(new snsoft.plat.demo.ord.kgisignature.ISignatureParams[0]), true)
	end if
end proc
```

* TAC函数方法：
```properties
（1）getDocTypeOther(doctdcode,doctcodes)
        提供一TAC中可使用的函数，给定文档类型定义编码、不包括的文档类型明细编码，返回给定文档类型定义去除给定文档类型
        明细编码后的其他所有文档类型明细编码：
                参数：A：文档类型定义编码、B：不包括的文档类型明细编码（多个用逗号分隔）
                返回值：若A为空则返回空；取A定义的所有文档类型明细编码设置为C（多个用逗号分隔），若C为空则直接返回；若B为空则返回C，
                否则返回C扣除B中包含文档类型明细编码后的字符串（多个用逗号分隔，若C扣除B后未包含，则返回空）
(2) voToMap(vo)
        提供TAC中可使用的函数，给定VO数据对象，返回一个Map结构。
(3) getVOList(vo,tablename)
        提供TAC中可使用的函数，给定vo数据对象和物理表明，从vo数据对象中查找指定物理表的vo说句对象。
```

* TAC内置变量
```properties
(1) main：当前业务数据主子VO数据，要想明确其VO列值的get/set方法，需要对应的业务单据提供VO结构。
```


#### 单据文档功能说明
    很多单据需要管理一些附件文档，如人员新建时需保存本人的签章图片；法人新建时需管理企业的一些合同章、法人章等；商品管理时需上传商品的正面、反面图片等；需要审批的单据提交待审时需要调用审批单打印格式自动生成审批单；合同等单据新建时需要手动上传一些其他辅助审批的附件等；还有些公司要求单据生效后自动调用特定打印格式打印留档。
    为了能够实现对这些附件的规范管理，系统引入了单据文件存档管理功能，可配置单据对应的附件文档类型，也可配置这些文档的生成方式（手动上传；调用打印格式手动生成还是自动生成，是在提交时自动生成还是生效后自动生成；配置时上传好的附件供用户阅读，如规章制度、合同范本等），还可以定义某种类型的文档在提交时必须存在。
    为实现同一类单据附件类型配置可以共享，系统将附件类型适用单据设计为子表；还支持什么状态下可操作该附件类型；还支持不同公司通过TAC灵活配置一些控制程序。
    文档类型定义需分商户在数据库进行管理，系统标准的文档类型定义会预设在文件中（编码按S+5位流水号定义），需通过《初始化》功能导入到商户数据库才起作用；5、系统缺省提供一“默认使用”选中的文档类型，通过《初始化》文件导入到数据库，如果某单据未匹配到特定的文档类型定义，则使用“默认使用”选中的文档类型。
#### 单据文档使用说明

##### 单据文档

1. 单据内标准存档附件

    布局结构为二级Tab页，一级名称为 打印及附件 ；二级为三个Tab页：打印、附件上传、范本；每个二级Tab页（范本除外）均含左右布局的两个表，且均为单据主表的子表
    
    ![](help/SN-PLAT/Sheet/img/SheetDoc.png)
    
    界面引用配置：
    ```xml
    <m:T title="打印及附件">
        <H name="sheetPrt" title="打印" uiprops.leftSize="430">
            <GroupPane name="torder_dylx" title="文档类型" align="left">
                <include src="SN-PLAT.Doc.SheetDocResPub#printToolbar" />
                <include src="SN-PLAT.Doc.SheetDocResPub#prtdoctype" mainui="salorder" />
            </GroupPane>
            <GroupPane name="torder_dyfj" title="存档附件" align="left">
                <include src="SN-PLAT.Doc.SheetDocResPub#prtplat_sheetdoc" mainui="salorder" />
            </GroupPane>
        </H>
        <H title="附件上传" uiprops.leftSize="385">
            <GroupPane name="torder_fjlx" title="文档类型" align="left">
                <include src="SN-PLAT.Doc.SheetDocResPub#atdoctype" mainui="salorder" />
            </GroupPane>
            <GroupPane name="torder_fjfj" title="存档附件" align="left">
                <include src="SN-PLAT.Doc.SheetDocResPub#atplat_sheetdoc" mainui="salorder" />
            </GroupPane>
        </H>
        <P title="范本">
            <GroupPane name="torder_fblx" title="文档类型" align="left">
                <include src="SN-PLAT.Doc.SheetDocResPub#moldoctype" mainui="salorder" />
            </GroupPane>
        </P>
    </m:T>
    ```
    
    系统功能监听：
    ```xml
    <Functions>
        <Functype>SN-PLAT.JS</Functype>
        <Subtype>2</Subtype>
        <Funcimpl>#new snsoft.plat.bas.sheet.doc.DocTypeResBtnJSListener({tgtUINames:["xxx"],sheetCode:'XXX'})</Funcimpl>
        <Remark>单据文档类型JS监听</Remark>
    </Functions>
    <!--客户端监听
    aidColumn：上传按钮辅助录入列。
    isOlViewImg：是否展示图片。
    imgCell：展示图片的列。
    autoSave：是否自动存盘，默认true
    -->
    <Functions>
        <Functype>SN-PLAT.JS</Functype>
        <Subtype>2</Subtype>
        <Funcimpl>#new snsoft.plat.bas.sheet.doc.SheetDocBtnJSListener({tgtUINames:["xxx"],sheetCode:'XXX',aidColumn:'docpath',isOlViewImg:true,autoSave:false})</Funcimpl>
        <Remark>单据文档附件JS监听</Remark>
    </Functions>
    <!--UI监听sheetCodes：配置单据号，统一界面多个单据附件时对应配置多个单据号，','分割-->
    <Functions>
        <Functype>SN-PLAT.UI</Functype>
        <Subtype>2</Subtype>
        <Funcimpl>
            <![CDATA[
        snsoft.plat.bas.sheet.doc.SheetDocSaveUIListener.new?tgtUINames=xxx&sheetCodes=XXX,XXX
        ]]>
        </Funcimpl>
        <Remark>单据文档存盘监听</Remark>
    </Functions>
    <!--服务端存盘监听-->
    <Functions>
        <Functype>SN-PLAT.SV</Functype>
        <Subtype>2</Subtype>
        <Funcimpl><![CDATA[
        snsoft.plat.bas.sheet.doc.service.impl.SheetDocSaveSVListener.new
        ]]></Funcimpl>
        <Remark>单据文档存盘监听</Remark>
    </Functions>
    ```

1. 单据内单独操作界面（非标准界面，一般为单独的GridTable，可能包含自定义列）
    
    如果有扩展字段，或者界面展示列不同需要拷贝默认界面去自定义，并且扩展了字段需要继承默认的VO：snsoft.plat.bas.sheet.doc.vo.SheetDoc，并继承接口snsoft.plat.bas.sheet.doc.service.SheetDocBasUIService<M>重写附件服务。
    界面配置：直接拷贝标准界面：SN-PLAT.Doc.SheetDocResPub#atplat_sheetdoc，**注意需要修改界面表名**
    服务配置：UI层
    **如果有扩展字段时需要开发自己写查询服务service，接口继承SheetDocBasUIService，给出VO泛型；SheetDocBasAbsUIService。**
    示例：
    ```java
    public interface SalorderDocUIService extends SheetDocBasUIService<SalorderSheetDoc>
    {
    	@Override
    	QueryResults<SalorderSheetDoc> queryUI(SheetDocService.SheetDocQueryParams params);
    
    	@Override
    	SaveResults saveUI(SaveParams<SalorderSheetDoc> saveParams);
    }
    @Service("SN-HELP.SalorderDocUIService")
    public class SalorderDocUIServiceImpl extends SheetDocBasAbsUIService<SalorderSheetDoc> implements SalorderDocUIService
    {
    }
    ```
    
    系统功能监听：
    ```xml
    <!--客户端操作列监听 使用操作列时需要配置此监听，并且操作列为固定的cmds:["add", "upload", "search", "delete"]-->
    <Functions>
       <Functype>SN-PLAT.JS</Functype>
       <Subtype>2</Subtype>
       <Funcimpl>#new snsoft.plat.bas.sheet.doc.render.SheetDocAidCellOperateJSListener({tgtUINames:["xxx"],ctrlTable:true});</Funcimpl>
       <Remark>单据文档非标准附件操作列JS监听</Remark>
   </Functions>
    <!--客户端监听
    aidColumn：上传按钮辅助录入列。
    isOlViewImg：是否展示图片。
    imgCell：展示图片的列。
    autoSave：是否自动存盘，默认true
    -->
    <Functions>
        <Functype>SN-PLAT.JS</Functype>
        <Subtype>2</Subtype>
        <Funcimpl>#new snsoft.plat.bas.sheet.doc.SheetDocBtnJSListener({tgtUINames:["xxx"],sheetCode:'XXX',aidColumn:'docpath',isOlViewImg:true,autoSave:false})</Funcimpl>
        <Remark>单据文档附件JS监听</Remark>
    </Functions>
    <!--UI监听sheetCodes：配置单据号，统一界面多个单据附件时对应配置多个单据号，','分割-->
    <Functions>
        <Functype>SN-PLAT.UI</Functype>
        <Subtype>2</Subtype>
        <Funcimpl>
            <![CDATA[
        snsoft.plat.bas.sheet.doc.SheetDocSaveUIListener.new?tgtUINames=xxx&sheetCodes=XXX,XXX
        ]]>
        </Funcimpl>
        <Remark>单据文档存盘监听</Remark>
    </Functions>
    <!--服务端存盘监听-->
    <Functions>
        <Functype>SN-PLAT.SV</Functype>
        <Subtype>2</Subtype>
        <Funcimpl><![CDATA[
        snsoft.plat.bas.sheet.doc.service.impl.SheetDocSaveSVListener.new
        ]]></Funcimpl>
        <Remark>单据文档存盘监听</Remark>
    </Functions>
    ```
    
    默认从主表取bcode和corpbcode，不符合条件需要重写SheetDocBtnJSListener中的方法：
    ```java
        protected String getCorpBcodeRefreshing()
        {
            return null;
        }
        protected String getBcodeForRefreshing()
        {
            return null;
        }
    ```
    
##### 多附件表配置
在同一个界面需要展示多个附件表时，每张表都需要对应一个单据，没有的需要虚拟一个单据号（注意单据号里配置母单据的主表配置，用于做权限判断），专门用来配置文档类型。
多个附件表就配置多个监听，给出对应的单据号即可：
```xml
<!--客户端操作列监听 使用操作列时需要配置此监听，并且操作列为固定的cmds:["add", "upload", "search", "delete"]-->
<Functions>
   <Functype>SN-PLAT.JS</Functype>
   <Subtype>2</Subtype>
   <Funcimpl>#new snsoft.plat.bas.sheet.doc.render.SheetDocAidCellOperateJSListener({tgtUINames:["xxx"],ctrlTable:true});</Funcimpl>
   <Remark>单据文档非标准附件操作列JS监听</Remark>
</Functions>
<!--一张附件表配置一个此监听-->
 <Functions>
    <Functype>SN-PLAT.JS</Functype>
    <Subtype>2</Subtype>
    <Funcimpl>#new snsoft.plat.bas.sheet.doc.SheetDocBtnJSListener({tgtUINames:["xxx"],sheetCode:'XXX',aidColumn:'docpath',isOlViewImg:true})</Funcimpl>
    <Remark>单据文档附件JS监听</Remark>
</Functions>
<!--多个附件表配置一个此监听，单据号','分割配置在sheetCodes参数上-->
<Functions>
    <Functype>SN-PLAT.UI</Functype>
    <Subtype>2</Subtype>
    <Funcimpl>
        <![CDATA[
    snsoft.plat.bas.sheet.doc.SheetDocSaveUIListener.new?tgtUINames=salorder&sheetCodes=XXX,XXXg
    ]]>
    </Funcimpl>
    <Remark>单据文档存盘监听</Remark>
</Functions>
<!--服务端存盘监听-->
<Functions>
   <Functype>SN-PLAT.SV</Functype>
   <Subtype>2</Subtype>
   <Funcimpl><![CDATA[
   snsoft.plat.bas.sheet.doc.service.impl.SheetDocSaveSVListener.new
   ]]></Funcimpl>
   <Remark>单据文档存盘监听</Remark>
</Functions>
```

##### 孙表附件

**当附件表作为孙表的时候需要特殊处理**

1. 表结构扩展单据主表主键列，列名与单据主表主键列名相同。
1. 附件表VO实现接口SheetDocGrandSon。

由于本规范发布较晚，在此之前的数据可依靠下列升级程序进行升级：
```properties
//附件表名  附件表父表（这里为单据子表）表名  主表主键列名   子表主键列名
proc upgrade(docTblName, gtblName, miColName, giColName)
	db=getDatabaseByTable(docTblName)
	sql = "select innercode,docicode from " + docTblName
	values=db.query3(sql)
	if length(values) > 0
		ks = newHashMap()
		for value in values
			list=ks.get(value[0])
			if list == null
				ks.put(value[0],list=newArrayList())
			end if
			list.add(value[1])
		end for
		sql2 = "select " + miColName + " from " + gtblName + " where " + giColName + "=?"
		db.beginTrans()
		rollback = true
		try
			it=it(ks)
			while it.hasNext()
				v = it.next()
				micode = db.query1(sql2, new snsoft.sql.SqlValue(v.getKey()))
				if micode != null
					updateData = new snsoft.dx.UpdateData()
					for docicode in v.getValue()
						data = newHashMap()
						data.put(miColName, micode)
						data.put("docicode", docicode)
						updateData.addUpdate(docTblName, data)
					end for
					println(updateData)
					db.updateData(updateData, true)
				end if
			end while
			 rollback=false
		catch ex
			println("处理异常："+ex)
		finally
			db.commitTrans(rollback)
		end try
	end if
end proc
```

##### VO单据关系

每个附件表都有与其对应的单据号，这些单据号有可能是真实存在的，也有可能是虚拟的，在做好文档类型定义的配置后，需要而外配置附件VO与单据对应关系，此配置只支持文件，用于绑定附件表与单据关系；
* 配置位置：cfg/res/doctype/voref/VORef[sisyd].xml
* 配置示例：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<SheetDocTypeVoRef xmlns="http://www.snsoft.com.cn/schema/SheetDocTypeVoRef.xsd">
	<TypeVoRef sheetcode="SN-HELP.Salorder" refvoclass="snsoft.plat.demo.ord.doc.vo.SalorderSheetDoc" />
	<TypeVoRef sheetcode="SN-HELP.Salorderg" refvoclass="snsoft.plat.demo.ord.doc.vo.SalordergSheetDoc" />
</SheetDocTypeVoRef>
```

##### 其他监听
1. 附件功能（存盘/查询）监听 选项

    ```xml
    <SysOption>
        <Key>DATA.SheetDocListeners</Key>
        <Desc>
            附件监听
        </Desc>
        <DefaultValue><![CDATA[
            [SYSID].XXXXXXDocFuncListener
        ]]></DefaultValue>
    </SysOption>
    ```
    * **父类：snsoft.plat.bas.sheet.doc.func.SheetDocFuncListener，监听必须为Spring Bean**
    * **此功能目前简单支持城系统选项，并且支持支Bean的方式，监听内部需要自己判断是否满足执行条件。**

1. 提交监听
    
    ```xml
    <Functions>
        <Functype>SN-PLAT.SV</Functype>
        <Subtype>2</Subtype>
        <Funcimpl><![CDATA[
        snsoft.plat.bas.sheet.doc.service.impl.SheetDocWfSVListener.new?attachCfg=tblname1:sheetcode1#doctbl1,tblname2:sheetcode2#doctbl2
        ]]></Funcimpl>
        <Remark>单据文档提交监听</Remark>
    </Functions>
    ```
    * tblname1:sheetcode1#doctbl1,tblname2:sheetcode2#doctbl2
    * 附件表父表表名：tblname1
    * 附件表名：doctbl1
    * 附件对应单据号:sheetcode1

1. 生效接口

    ```properties
    生效一般为异步处理，提供公共的接口方法调用：snsoft.plat.bas.sheet.doc.service.SheetDocService.toGenComplete 参数详情见接口注释
    ```

1. 版本备份

    ```xml
    <Listeners><![CDATA[
        snsoft.plat.bas.sheet.doc.service.impl.SheetDocVersionBackupListener.new?attachCfg=tblname1:sheetcode1#doctbl1,tblname2:sheetcode2#doctbl2
    ]]></Listeners>
    ```
    * tblname1:sheetcode1#doctbl1,tblname2:sheetcode2#doctbl2
    * 附件表父表表名：tblname1
    * 附件表名：doctbl1
    * 附件对应单据号:sheetcode1

1. 单据复制

    ```xml
    <Listeners>
        <![CDATA[
        snsoft.plat.bas.sheet.doc.service.impl.SheetDocCopyListener.new?attachCfg=plat_salorder:SN-HELP.Salorder#plat_salorder_doc,plat_salorderg:SN-HELP.Salorderg#plat_salorderg_doc
        ]]>
    </Listeners>
    ```
    * tblname1:sheetcode1#doctbl1,tblname2:sheetcode2#doctbl2
    * 附件表父表表名：tblname1
    * 附件表名：doctbl1
    * 附件对应单据号:sheetcode1


1. 审批监听

    ```xml
    <Listeners>
        <![CDATA[
        snsoft.plat.bas.sheet.doc.service.impl.SheetDocApprMsgListener
        ]]>
    </Listeners>
    ``` 
    * 配合Tac使用：executeForAppr(refData)：任何一点审批通过后调用（特殊要求可以在这里实现）
    
##### 权限扩展

1. 共用接口

    标准服务接口：snsoft.plat.bas.sheet.doc.service.SheetDocLimitService；
    抽象实现类（默认实现）：snsoft.plat.bas.sheet.doc.service.AbsSheetDocLimitService
    
    示例：
    
    ```java
       public class FTSheetDocLimitServiceImpl extends AbsSheetDocLimitService implements FTSheetDocLimitService
       {
           @Override
           public Map<String,Object> hasLimit(SheetDocLimitParams params, String op)
           {
               ...
           }   
       }   
    ```
    
1. 标准服务名称 SN-PLAT.SheetDocLimitService

    采用服务Bean替换的方式自定义权限。
    
1. 服务Bean替换方式

    修改项目组件中的applicationContext-[sysid].xml配置文件如下（示例）：
    
    ```xml
    <!--单据权限替换-->
    <bean id="SN-PLAT.SheetDocLimitService" class="snsoft.ft.comm.limit.service.impl.FTSheetDocLimitServiceImpl" />
    ```

**注意：权限扩展针对标准版附件**

#### 图片相关

##### 内嵌图片
应用在母单据上，用于专门展示属性不为空的文档类型的图片附件；30：图片 类型只展示图片，其他展示分组图片。
界面定义配置：
```xml
<B title="单据图片" uiprops.domStyle="{overflow:'auto'}">
    <m:P name="sheet_doc_image" title="图片" region="north" csslink="${theme}/css/ui-other/uploadgroup.css"></m:P>
    <include src="SN-PLAT.Doc.SheetDocResPub#plat_sdembedimg" mainui="salorder" />
</B>
```
系统功能监听：
```xml
<!--
参数说明：
autoSave：是否自动存盘，默认true
titleRegion：标题位置 top、bottom、left
grpValues：单据文档定义属性分组值，不传值时默认‘30’：图片
showGrpBtn：是否显示分组按钮，多个分组值时默认显示
-->
<Functions>
    <Functype>SN-PLAT.JS</Functype>
    <Subtype>2</Subtype>
    <Funcimpl>#new snsoft.plat.bas.sheet.doc.SheetDocEditImgEmbedJSListener({tgtUINames:["plat_sdembedimg"],sheetCode:'xxx',autoSave:false,titleRegion:'top',grpValues:'10,20'})</Funcimpl>
    <Remark>单据文档附件JS监听</Remark>
</Functions>
<!--UI监听sheetCodes：配置单据号，统一界面多个单据附件时对应配置多个单据号，','分割-->
<Functions>
   <Functype>SN-PLAT.UI</Functype>
   <Subtype>2</Subtype>
   <Funcimpl>
       <![CDATA[
   snsoft.plat.bas.sheet.doc.SheetDocSaveUIListener.new?tgtUINames=xxx&sheetCodes=XXX,XXX
   ]]>
   </Funcimpl>
   <Remark>单据文档存盘监听</Remark>
</Functions>
<!--服务端存盘监听-->
<Functions>
   <Functype>SN-PLAT.SV</Functype>
   <Subtype>2</Subtype>
   <Funcimpl><![CDATA[
   snsoft.plat.bas.sheet.doc.service.impl.SheetDocSaveSVListener.new
   ]]></Funcimpl>
   <Remark>单据文档存盘监听</Remark>
</Functions>
```
监听SheetDocEditImgEmbedJSListener额外提供了方法clickGroBtned(attr)，点击分组按钮后触发，参数为当前分组值。
客户端监听可扩展接口方法：
```java
    protected String getGrpBtnTip()
    protected String getCuicode()
    /**
     * 供重写使用
     * --
     * 点击分组按钮时调用
     * --
     */
    protected void clickGroBtned(String attr)
    /**
     * 用于初始化选中图片展示的当前选中的分组按钮
     */
    protected void initDoctAttr(String attr)
```

##### 弹窗图片

1. 操作列按钮样式：
1. JS监听：
    需要引用JS @jslib/sortable/Sortable.min.js
    #new snsoft.plat.bas.sheet.doc.SheetDocImgPopDgListener({viewCell:'',sheetCode:''})
    viewCell：预览列列名
    sheetCode：单据号

##### 图片预览

1. Render：
        uiprops.renderer="new snsoft.plat.bas.sheet.doc.render.SheetDocImgRender({})"
        界面需要引js：@jslib/swiper/swiper.min.js
1. RGrid表：
    ```java
    String imgsCellName = "imagepaths";
    //
    TableColumn imageCell = table.getColumn(imgsCellName);
    if (!$bool(imageCell))
    {
    	return;
    }
    HTMLElement imagepaths = DOM.findById("UIA_" + table.name + "_" + imgsCellName, gr.dom);
    if ($bool(imagepaths))
    {
    	DOM.removeAllChild(imagepaths);
    	//
    	String className = imagepaths.className;
    	imagepaths.className = className + TableImagesCellViewDom.tdPaddingClass;
    	//
    	int idx = table.dataSet.columnAt(imgsCellName);
    	String value = (String) gr.rowData[idx];
    	HTMLElement div = SheetDocImgRender.appendImageDomAndBindFc(SheetDocImgRender.splitImages(value), true);
    	imagepaths.appendChild(div);
     }
    ```
1. 图片补数监听：
        snsoft.plat.bas.sheet.doc.SheetDocLoadImagesUIListener.new?expImgCellName=xx&sheetCode=xx&innerfldCellName=xx
        expImgCellName：图片展示lie列名
        sheetCode：单据号
        innerfldCellName：主表主键列名


### 单据文档手册
#### 文档类型定义
##### 文档类型主表
主表用于构建文档类型以及单据之间的关系，相当于整套配置的表头。
##### 文档类型明细
用于定义成套的文档类型，这对某些单据。
##### 适用单据
用于配置前面定义的文档类型的所对应的单据，将两者之间构建关系。每一个单据（一条配置记录）都对应一套当前单据可以使用的文档类型，包括各个状态的控制。
#### 字典说明
1. 文档类别：DT_SN-PLAT.DoctType
10：范本、20：打印、30：手工上传
2. 文档类型状态：DT_SN-PLAT.DocTypeStatus
10：草拟、70：启用、90：停用
3. 自动打印类型：DT_SN-PLAT.DocPrintType
10：提交；20：生效
4. 文档类型属性(可扩展字典)：DT_SN-PLAT.DocTypeAttr
10：非多证合一；20：多证合一；30：图片
5. 允许手工上传：DT_SN-PLAT.DocTypeManualflag
10：允许一个；20：允许多个；30：不允许

#### 码表说明
1. 文档类型码表：SN-PLAT.DocType
	* 码表参数FilterPrt，文档类型是否过滤打印类型的编码，默认true过滤；
	* 码表参数DefFilter，是否使用文档类型定义（过滤及Tac）过滤配置，默认true过滤 （暂时未启用此参数）；
	* 码表参数sheetcode逻辑非空，辅助录入过滤参数；
	* 码表参数bcode逻辑非空，辅助录入过滤参数；
	* 码表参数corpbcode逻辑非空，辅助录入过滤参数；
2. 打印格式码表：SN-PLAT.DefName
    * 无入参，查询sysdefs和sysdefb以及配置文件。
    * 用于文档类型定义打印格式录入。
    
#### 文档附件表结构

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|------|
|docicode|主键|VARCHAR|SZIBILL|是|是||||
|cuicode|商户|VARCHAR|SZCUICODE||是||||
|tblname|表名|VARCHAR|SZTABLE||是||||
|innercode|内码|VARCHAR|SZIBILL||是||||
|sheetcode|单据号|VARCHAR|SZSHEET||是||||
|sortidx|序号|INTEGER|||是||||
|doctcode|文档类型/图片类型|VARCHAR|SZDOCTCODE|||||文档类型码表|
|doctdcode|文档类型定义主表编码|VARCHAR|SZDOCTDCODE||||||
|imgtype|图片处理类型|VARCHAR|SZTYPE||||||
|docttype|类别|CHAR|SZDOCTTYPE|||||字典|
|doctattr|文档类型属性|VARCHAR|SZSTATUS||||||
|defflag|自动打印|INTEGER|||||||
|docname|附件名称|VARCHAR|SZTEXT||||||
|docpath|文件路径|VARCHAR|SZFILEADDR||||||
|docsize|文件大小|INTEGER|||||||
|remark|备注|VARCHAR|SZTEXT||||||
|vprepare|上传人|VARCHAR|SZUCODE||||||
|predate|上传时间|DATE|||||||
|uploadnum|上传次数|INTEGER||||||||

* 图片处理

图片表为文档表的子表，专门用于处理图片使用，在使用附件功能上传图片类型文档时使用子表。

1. 方案设计：

    ```properties
    文档主表：文档类型表增加图片处理类型列，并支持使用配置文件方式做图片类型(文档类型)与图片处理类型做对应关系。
    
    主表表结构说明：
        doctcode 图片类型 使用文档类型
        docname 图片名称 
        docpath 图片路径

    服务调用关系：上传时附件服务调用图片处理服务返回图片处理信息，图片信息存盘。
    ```

1. 子表表结构设计：

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|------|
|imgicode|主键|VARCHAR|SZIBILL|是|是||||
|docicode|文档主表内码|VARCHAR|SZCODE||是||||
|imgname|图片名称|VARCHAR|SZTEXT||||||
|imgpath|图片路径|VARCHAR|SZFILEADDR||||||
|imgtype|图片处理类型|VARCHAR|SZTYPE||||||
|handle|处理方式|VARCHAR|SZHANDLE|||||||
    
#### 完整引用界面
公共引用界面：SN-PLAT.Doc.SheetDocResPub
### 单据文档设计
* 【文档类型】

@startuml

Title 文档类型定义管控

DocTypeDefService .right.> DocTypeDef
DocTypeDefService .left.> SheetDocType
DocTypeDef ..> DocType
DocTypeDef ..> DocSuitSheet
DocTypeDefService ..> DocTypeDefCopySVListener
DocTypeDefService ..> DocTypeDefKeepAndNoKeepSVListener
DocTypeDefService ..> DocTypeDefUnKeepAndUnNoKeepSVListener
DocTypeDefService ..> DocTypeDefFileSyncToDBListener

interface DocTypeDefService {
    QueryResults<DocTypeDef> queryDocTypeDefUI(DocTypeDefQueryParams params);
    QueryResults<DocType> queryDocTypeUI(DocTypeQueryParams params);
    QueryResults<DocSuitSheet> queryDocSuitSheetUI(DocSuitSheetQueryParams params);
    SaveResults saveUI(SaveParams<DocTypeDef> params);
    void syncToDB(String doctdcode);
    void copyDocTypeDef(String doctdcode);
    void operateKeep(String doctdcode);
    void operateNoKeep(String doctdcode);
    void operateUnKeep(String doctdcode);
    void operateNoUnKeep(String doctdcode);
}

class CopyDocTypeDefSVListener{
    void beforeSave(...);
}

class EnuseAndUnEnuseSVListener{
    void beforeSave(...);
}

class UnuseAndUnUnuseSVListener{
    void beforeSave(...);
}

class CheckCiteSVListener{
    void beforeSave(...);
}

class FileSaveSVListener{
    void afterSave(...);
}

class DocTypeDef{
}

class DocType{
}

class DocSuitSheet{
}

class SheetDocType{
}

@enduml

@startuml

Title 文档类型定义公共
DocTypeService .right.> DocType

interface DocTypeService {
    String getDoctdcodeBySheet(String sheetCode);
    DocTypeDef getDocTypeDef(String doctdcode);
    QueryResults<DocType> querySheetDocType(SheetDocTypeQueryParams params);
    List<DocType> getSheetDocTypeByDoct(SheetDocTypeQueryParams params);
    List<DocType> getDocTypeSubExist(String sheetcode, String bcode, String corpbcode, Object data);
    List<DocType> getDocTypeSubGen(String sheetcode, String bcode, String corpbcode, Object dat);
    List<DocType> getDocTypeRatifyGen(String sheetcode, String bcode, String corpbcode, Object data);
    String getDocTypeOther(String doctdcode,String doctcode);
}
@enduml

* 【存档附件】

@startuml

Title 存档附件

SheetDocService .right.> SheetDoc
SheetDocService ..> SheetDocSVListener

interface SheetDocPrintService <SheetDoc> {
    void batchPrintUI(String[] doctcodes,String sheetcode);
}

interface SheetDocService <SheetDoc>{
    QueryResults<SheetDoc> queryUI(SheetDocQueryParams params);
    SaveResults saveUI(SaveParams<SheetDoc> params);
    void batchPrint(BatchPrintParams[] params);
    void upLoadDoc(SheetDoc[] sheetDocs, String docTblName, String docImgTblName);
}

class SheetDocBtnSVListener{
    void beforeSave(...);
}

class SheetDocSVListener{
    List<SheetDoc> executeQuery(...);
    void afterQuery(...);
    void beforeSave(...);
    void afterSave(...);
}

class SheetDoc{
}

@enduml
### 单据文档场景
文档类型结构复杂，附件表不共用情况。
示例：见帮助中心《销售合同》示例。

### 系统选项

文档存储路径：FSID.STADOCROOT
附件大小限制（单位/kb）：Attach.FileSize
OfficeOnline服务地址：OfficeOnline.ServerURL