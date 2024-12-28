## 常用功能说明

### 界面属性控制

* 单据权限+状态控制：有操作权限且单据状态在20以内才可以对界面进行操作
	```
	SN-UI.UIOptCtrlListener?[{sheetcode:'',stdUICtrl:true}]
	```
* 单据权限控制：有操作权限才可以对界面进行操作
	```
	SN-UI.UIOptCtrlListener?[{sheetcode:'',onlyLimitCtrl:true}]
	```
* 单据控制：指定界面指定属性
	```
	SN-UI.UIOptCtrlListener?[{sheetcode:'',stdUICtrl:true,optNames:''}]
	```

### 单据默认值及校验

* 单据默认值
	```
	SN-UI.VOListener?[{sheetCode:'',defOption:2}]
	```
* 单据校验
	```
	SN-UI.VOListener?[{sheetCode:'',defOption:1}]
	```

### 代码审核规范

|功能点|说明|
|-|-|
|系统|各系统应该提供系统级别的常量供当前系统引用（如FTCodeConstants.SysId=&quot;FT-CODE&quot;）;供程序中引用|
|XXVO|主VO中声明public static final String SheetCode=&quot;&quot;;供程序中引用|
|XXService|接口服务中声明BeanName;供程序中引用|
|XXUIService|接口服务中声明BeanName;供程序中引用|
|配置|单据定义、系统功能、界面定义、界面属性配置、编码规则、权限定义、单据状态、单据复制、版本备份、单据撤单等|
|按钮|码表、配置入口单据按钮不能设置图标（边框中使用文字）|


### 组织结构

### 汇率

### 贸易公共
[公共文档-字典说明](help.html?helpFile=help/FT-TRD/Outline.md)