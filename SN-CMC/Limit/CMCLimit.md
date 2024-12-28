## 管控权限规范

|日期|作者|备注|
|------|------|------|
|2019-04-30|冀小雷|初稿|

### 管控权限设计说明

**原则1：所有功能均可通过权限配置进行授权**
**原则2：权限操作范围 L<R<C**

管控功能的权限分为以下三类进行控制：
* 操作权限：如SQL工具、Tac工具等，不需要实际存在的权限字段；
* 内码数据权限：如数据分组、查询工具、穿透定义等，使用内码作为权限字段；
* 数据权限：如组织管理、编码规则等，使用具体的物理字段(bcode/wcode/corpbcode/limbcode等)作为权限字段；

### 管控权限列表说明

|完成|单据号|功能说明|权限字段|操作说明|
|-|-|-|-|-|
|√|SN-CMC.SQLTool|SQL工具|xx|L:打开页面<br>R:执行查询(select)<br>C:执行修改(DDL/DML/DGL等)|
|√|SN-CMC.TACTool|TAC工具|xx|L:打开页面<br>C:执行|
|√|SN-CMC.CreateDB|生成数据库<br>生成视图<br>生成MongoDB索引<br>生成ES索引|xx|L:打开页面<br>C:执行|
|√|SN-CMC.StaticHtml|页面静态化|xx|L:打开页面<br>C:执行|
|√|SN-CMC.DataCache|数据缓存|xx|L:打开页面<br>R:查询<br>C:清空|
|√|SN-CMC.QueryCode|分码查询|xx|L:打开页面<br>R:查询<br>C:重置|
|√|SN-CMC.UIOptCtrl|界面属性控制|uicode:编码|L:打开页面<br>R:查询<br>C:修改|
|√|SN-CMC.SysDict|系统字典|dicticode:字典号|L:打开页面<br>R:查询<br>C:修改|
|√|SN-CMC.ComDict|商户字典|dicticode:字典号|L:打开页面<br>R:查询<br>C:修改|
|√|SN-CMC.CodeTable|码表定义|id:码表名称|L:打开页面<br>R:查询<br>C:修改|
|√|SN-CMC.ResBundle|资源文件|xx|L:打开页面<br>R:查询|
|√|SN-CMC.CodeRela|码表映射|relacode|L:打开页面<br>R:查询<br>C:修改|
|√|SN-CMC.ExcelImportType|Excel导入类型|xx|L:打开页面<br>R:查询|
|√|SN-CMC.ExcelImport|Excel导入|xlscode:导入编码|L:打开页面<br>R:查询<br>C:修改|
|√|SN-CMC.UI|界面定义<br>界面结构树<br>界面列表|muiid:界面号|L:打开页面<br>R:查询<br>C:修改|
|√|01.USERTBLINFO|界面个性信息|ucode:使用用户<br>defucode:定义用户<br>bcode:适用部门|L:打开页面<br>R:加载<br>C:保存|
|√|SN-CMC.Sheet|单据定义<br>单据默认值查询<br>单据默认值配置<br>单据数据校验配置<br>单据状态|sheetcode:单据号|L:打开页面<br>R:查询<br>C:修改|
|√|SN-CMC.ScheduleTask|定时任务定义<br>定时任务查询|taskid:任务号|L:打开页面<br>R:查询<br>C:修改|
|√|SN-CMC.WebSocketDef|WebSocket定义|wscode:编码|L:打开页面<br>R:查询<br>C:修改|
|√|SN-CMC.WebSocketMan|WebSocket管理|wscode:编码|L:打开页面<br>R:查询<br>C:修改|
|√|SN-CMC.Accode|编码规则|limbcode:运维组织|L:打开页面<br>R:查询<br>C:修改|
|√|SN-CMC.Datalimitdef|操作权限定义|limid:权限ID|L:打开页面<br>R:查询<br>C:修改|
|√|SN-CMC.Busshlim|单据权限关系|limid:权限ID|L:打开页面<br>R:查询<br>C:修改|
|√|SN-CMC.LoggerQuery|日志查询|xx|L:打开页面<br>R:查询（校验）|
|√|SN-CMC.TempFile|临时文件|xx|L:打开页面<br>R:查询（校验）<br>UP:上传<br>DN:下载<br>C:删除|
|√|SN-CMC.TempXlsData|临时Xls数据|xx|R:查询（校验）<br>C:存盘|
|√|SN-CMC.DataExport|数据导出定义<br>数据导出:L/C<br>数据按SQL导出:L/C<br>导出数据查询:L/C|xx|L:打开页面<br>R:查询（校验）<br>C:存盘/导出|
|√|SN-CMC.DataImport|数据导入|xx|L:打开页面<br>C:导入|
|√|SN-CMC.TblDef|表结构定义<br>物理表结构定义:L/R|tblname:表名|L:打开页面<br>R:查询<br>C:修改|
|√|sheet.Users|日志诊断信息|xx|L:打开页面<br>R:查询（校验该sessionid对应的用户查询权限）|
|√|SN-CMC.LoggerLevel|日志级别|xx|L:打开页面<br>R:查询（校验）<br>C:设置|
|√|sheet.Users|在线用户查询|limbcode:运维部门|L:打开页面<br>R:查询（服务端过滤）<br>C:剔除（服务端判断）|
|√|sheet.Bcode|组织结构<br>菜单权限×<br>数据权限×<br>菜单权限查询：查询当前用户拥有的权限|limbcode:运维组织|R:查询<br>C:修改|
|√|SN-CMC.HelpFile|帮助文件|path：帮助文件路径|R:下载<br>C:保存|
|√|SN-PLAT.QueryTool|查询工具|qrycode：查询编码|R:查询<br>C:修改|
|√|SN-PLAT.UIDataGroup|数据分组|grpcode：分组编码|R:查询<br>C:修改|
|√|SN-PLAT.ViewDetail|穿透定义|colname：穿透列名|R:查询<br>C:修改|
|√|SN-PLAT.SheetCopyDef|单据复制|sheetcode:单据号？|R:查询<br>C:修改|
|√|SN-PLAT.SheetRetracts|单据撤单|sheetcode:单据号？|R:查询<br>C:修改|
|√|SN-PLAT.VersionBackupDef|单据版本备份|sheetcode:单据号？|R:查询<br>C:修改|
|√|SN-CMC.Image|图片管理|xx|R:查询|

### 配置功能列表说明
|功能名称|多商户|
|-|-|
|系统字典|×|
|商户字典|√|
|编码规则|√|
|单据编码规则|√|
|单据默认值|√？|
|单据数据校验|√？|
|单据复制|√？|
|单据撤单|√？|
|单据版本备份|√？|