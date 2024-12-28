## 单据配置创建工具

|日期|作者|备注|
|------|------|------|
|2019-4-1|聂明笛|初稿|
|2019-10-12|聂明笛|修改|
|2019-12-25|吴小海|修改|

### 指南	

#### 概念说明

生成规范文件辅助功能，旨在帮助开发人员快速生成N9B开发相关代码、配置、监听等；
   (1)配置包含：编码规则、权限配置、系统功能、单据复制、版本备份、撤单配置、界面定义、界面控制、数据校验、单据定义
   (2)代码包含：VO、入口、详情查询服务以及参数、客户端监听等
   注：其中相关配置可根据实际情况灵活勾选是否生成、已经存在的文件会被覆盖；

#### 使用说明

注意：使用此工具前需要确定相关关系型数据库已经编写了数据库生成文件CreateDatabaseXXX.xml，并生成了相关物理表结构；
使用界面：[生成规范文件](uiinvoke/00/zh_CN/theme0/SN-PLAT.CreateFiles.html)

### 手册	

#### 参数说明

1. **工程名称**
    对应工程名称，例：help
1. **单据号**
    对应单据号，例：SN-HELP.SalorderNew
1. **VO结构定义**
    对应VO结构，例：
```json
{
    "tblname":"plat_salorder",
    "voname":"SalorderNewVO",
    "details":[
        {
            "tblname":"plat_salorderg",
            "voname":"SalorderNewGVO",
            "multiple":true,
            "fldname":"salorderg",
            "details":[
                {
                    "tblname":"plat_salorderp",
                    "voname":"SalorderNewPVO",
                    "multiple":true,
                    "fldname":"salorderp"
                }
            ]
        }
    ]
}
```
    tblname：表名
    voname：VO类名
    detail：子表信息
    multiple：主子对应关系，默认true，即为一对多
    fldname：主VO中子表信息对应的字段名
包名：对应包名，例：snsoft.plat.demo.order
配置文件选项：勾选需要生成的配置文件
是否生成代码：勾选生成代码，包括VO，接口，实现，UI接口，UI实现，JS监听

### 实践

### 设计

(1)生成相关代码主要问题存在于VO相关注解的添加，底层默认提供标准模板，也支持具体项目自定义模板；
各系统可在定义模板中定义相关公共字段注解配置 如果：是否非空、默认值、CodeData、小数位宏等
生成VO代码时候会参考模板定义、匹配上具体字段按照定义添加相关注解功能

 ```xml
<VOCreateDef>
    <!-- 主表-父类 -->
    <MTblSupperClass>FTSheetVO</MTblSupperClass>
    <!--  子表-父类 -->
    <CTblSupperClass>VO</CTblSupperClass>
    
    <!-- 字段 -->
    <VOField>
        <FldName>XXX</FldName>
        <NotNull>true</NotNull>
        <CodeData>123456</CodeData>
        <DefaultValue>10</DefaultValue>
        <MaxDeciValue>1</MaxDeciValue>
    </VOField>
    
</VOCreateDef>
 ```
(2)标准XML配置文件除界面定义外差异性不是很大、为此系统底层提供默认标准模板，也支持具体项目自定义模板；

(3)相关配置文件路径

VO模板：  

cfg\res\createfile\vo\SN-PLAT.VOConfigs.xml

XML模板： 

cfg\res\createfile\xml\SN-PLAT.accode.xml            --- 编码规则
cfg\res\createfile\xml\SN-PLAT.limit.xml             --- 权限定义
cfg\res\createfile\xml\SN-PLAT.optctrl.xml           --- 界面属性控制
cfg\res\createfile\xml\SN-PLAT.retract.xml           --- 撤单定义
cfg\res\createfile\xml\SN-PLAT.sheet.xml             --- 单据定义
cfg\res\createfile\xml\SN-PLAT.sheetcopy.xml         --- 单据拷贝
cfg\res\createfile\xml\SN-PLAT.sysfuncEntryQR.xml    ----入口查询
cfg\res\createfile\xml\SN-PLAT.sysfuncEntryUI.xml    ----入口UI
cfg\res\createfile\xml\SN-PLAT.sysfuncQR.xml         ----详情查询  
cfg\res\createfile\xml\SN-PLAT.sysfuncSV.xml         ----详情SV  
cfg\res\createfile\xml\SN-PLAT.sysfuncUI.xml         ----详情UI
cfg\res\createfile\xml\SN-PLAT.ui.xml                ----界面定义  
cfg\res\createfile\xml\SN-PLAT.validate.xml          ----数据校验
cfg\res\createfile\xml\SN-PLAT.vnbk.xml              ----版本备份
cfg\res\createfile\xml\SN-PLAT.wfsub.xml             ----审批配置

(4)扩展 
    如 XXX系统(以SN-HELP为例) 需要扩展以上配置 可在SN-HELP UI工程相同目录下 新建文件命名 SN-HELP.XXXX.xml
    自行修改配置文件 
    新建单据配置根据系统号SN-HELP先读取SN-HELP工程配置  如读取不到则读取SN-PLAT平台默认配置


