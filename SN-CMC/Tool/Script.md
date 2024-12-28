## 引用脚本配置

### 指南

#### 概念说明

用于集中定义数据库级别的脚本，包括Tac、JavaScript及其他；
一般脚本配置如Tac比较大，都是用CLOB进行定义，但是过多的CLOB定义会降低数据库性能（MySQL），且并不是所有的Tac代码块都会很大；故将所有的脚本列大小使用一个宏【SZTAC(512)】，当定义的脚本超出该大小后，叫脚本内容定义到脚本配置中，通过【[include]taccode】规则进行应用即可。

**注意**：如果项目上想尽可能便面使用此功能，可以通过数据库宏属性配置文件，将SZTAC设置到尽可能大（如Oracle支持设置到4000等）；

#### 使用说明

**服务端使用**

由于目标列大小为512，可能存储的是脚本，也有可能存储的是【[include]taccode】，故服务端提供方法进行解析
```java
snsoft.tac.TacUtils.parseTacText(o.getXXFormula())
```
凡是支持此功能的服务端程序，都要通过该方法进行解析转换。

**客户端使用**

客户端定义列需要配置专门的辅助录入，该辅助录入会进行判断：
	1. 如果脚本大小超过SZTAC，则将脚本存储到脚本配置中；
	1. 如果脚本大小低于SZTAC，则将脚本直接存储在目标列中；
辅助录入类：Xjs.ui.SelectScriptDialog -- 废弃
列配置方式：
```xml
<c name="xxx" title="xx脚本xx" sqltype="12" width="180" tipIfOverflow="true" 
	aidInputerBtn="true" aidInputableIfRdonly="true" disableed="true" uiprops.disableDel="true"
    mutipleLine="true"	
/>
```

### 手册

#### 辅助录入说明

* 脚本列配置说明
```
	aiprops="sheetcode:'xx'"：引用脚本配置的功能单据号，用于读写脚本配置时判断权限使用；
	disableed="true"：配置了脚本配置辅助录入的列，不允许编辑，必须通过辅助录入进行编辑，否则可能产生垃圾数据；
	uiprops.disableDel="true"：禁止删除，删除必须通过脚本配置辅助录入，否则可能产生垃圾数据。
	tipIfOverflow="true"：漂浮显示脚本内容或脚本配置引用编码；
```
* 脚本配置编码规则：tblname.scriptcolname:key1Value#key2Value
	* tblname：引用脚本配置的表名；
	* scriptcolname：引用脚本配置的列名（因为一张表中可能存在多个脚本列，如Excel导入定义等，故必须使用列名进行区分）；
	* key1Value#key2Value：内码列名升序后的列值；
* 引用脚本配置的表说明
	* 如果为主表，则主表必须存在修改时间且布局页面上，在通过脚本配置辅助录入更新脚本内容时，需要通过修改时间更新主表用于通知缓存；

#### 服务端使用说明

* 服务端使用通过调用【snsoft.tac.TacUtils.parseTacText(o.getXXFormula())】进行使用；
	* [include]支持嵌套
* 如果服务端脚本对象需要缓存，则主表必须存在修改时间列，用于做缓存通知使用。

### 设计

