## 界面属性扩展

|日期|作者|备注|
|------|------|------|
|2019-11-25|郭鑫|初稿|


### 指南

#### 概念说明

通过文件或数据库配置，实现在现有界面上的二次开发，添加或覆盖原有的界面、字段属性；

#### 使用说明

配置示例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Muiext muiname="界面标题"
        uiprops.helpFile="help/SN-CMC/Muiext/Muiext.md"
        xmlns="http://www.snsoft.com.cn/schema/Muiext"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.snsoft.com.cn/schema/Muiext http://www.snsoft.com.cn/schema/Muiext.xsd">
    <remark>啦啦啦</remark>
    <!--Zero or more repetitions:-->
    <Uiext uiname="help_solr">
        <uiprops>
            <![CDATA[
 dsprops:{pageRows:5}
]]>
        </uiprops>
    </Uiext>
    <Uiext uiname="help_solr#bcode">
        <uiprops>
            <![CDATA[
noblank:true
]]>
        </uiprops>
    </Uiext>
    <Uiext uiname="help_solr#title">
        <uiprops>
            <![CDATA[
initval:"初值"
]]>
        </uiprops>
    </Uiext>
</Muiext>
```

### 手册

1. 管控中心->开发配置->界面配置->[界面属性扩展](uiinvoke/00/zh_CN/theme0/SN-HELP.UI.Table.GridTableForExt.html)
界面属性控制如果针对的是某一个界面，则要求文件存放结构跟界面定义配置文件的存放结构相同并使用相同的文件名称

1. 配置文件位置:UI工程下，cfg/ui/muiid/**/*.xml

1. 配置说明参照[Muiext.xsd](do/BrowseSchema?schema=Muiext.xsd)

### 实践

### 设计


