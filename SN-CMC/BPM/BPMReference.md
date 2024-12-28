## BPM

|日期|作者|备注|
|------|------|------|
|2020-06-19|冀小雷|初版|

### 指南

#### 概念说明

#### 使用说明

### 手册

### 实践

### 设计

**BPM配置主表**

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|------|
|cuicode|商户|VARCHAR|SZCUICODE|是|是||||
|bpmcode|BPM编码|VARCHAR|32|是|是||||
|bpmname|BPM名称|VARCHAR|64||||||
|<font color='red'>sheetcode?</font>|单据类型|VARCHAR|32||||||
|vprepare|创建人|VARCHAR|SZUCODE||||||
|predate|创建日期|DATE|||||||
|modifier|修改人|VARCHAR|SZUCODE||||||
|modifydate|修改日期|DATE|||||||

**BPM节点子表**

|属性|名称|数据类型|大小|主键|非空|非存储|校验|说明|
|------|------|------|------|------|------|------|------|------|------|
|cuicode|商户|VARCHAR|SZCUICODE|是|是||||
|bpmcode|BPM编码|VARCHAR|32|是|是||||
|uid|节点编码|VARCHAR|64||||||
|uname|节点名称|VARCHAR|64||||||
|vprepare|创建人|VARCHAR|SZUCODE||||||
|predate|创建日期|DATE|||||||
|modifier|修改人|VARCHAR|SZUCODE||||||
|modifydate|修改日期|DATE|||||||