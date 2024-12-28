## 合约条款使用说明文档

### 条款表结构说明
各个单据都有自己的条款表，结构基本相同，见下
合约条款明细表

|字段名|名称|类型|说明|
|------|------|------|------|
|sticode|条款明细内码|VARCHAR(SZIBILL)|条款明细内码|
|innercode|条款所属表内码|VARCHAR(SZIBILL)|条款所属表的内码，一般为单据内码，如果条款为孙表时，为条款所在表的内码|
|termtype|条款类型|CHAR(SZTERMTYPE)|10：商务条款，20：法务条款|
|termtitle|条款标题|VARCHAR(SZTERMTITLE)|条款标题|
|idx|序号|INTEGER|序号|
|flags|标识|SMALLINT|根据配置定义当前条款是否可修改删除：(flags & 1) == 1时可修改，(flags & 2) == 2时可删除|
|tflags|选择结果标识|SMALLINT|当单据操作条款时用于标记操作类型：(tflags & 1) == 1时为修改，(tflags & 2) == 2时为删除，(tflags & 4) == 4时为手动新增|
|sterm|原条款内容|VARCHAR(SZTERMTEXT)|用于存放配置定义的条款内容，不会改变和宏定义替换|
|eterm|修改条款内容|VARCHAR(SZTERMTEXT)|用于存放修改和新增的条款内容，宏定义不会被替换|
|fterm|最终条款内容|VARCHAR(SZTERMTEXT)|用于存放最终的条款内容，宏定义替换后|
|senterm|原英文条款内容|VARCHAR(SZTERMTEXT)|启用英文条款时，用于存放配置定义的英文条款内容，不会改变和宏定义替换|
|eenterm|最终英文条款内容|VARCHAR(SZTERMTEXT)|启用英文条款时，用于存放修改和新增的英文条款内容，宏定义不会被替换|
|fenterm|最终条款内容|VARCHAR(SZTERMTEXT)|启用英文条款时，用于存放最终的英文条款内容，宏定义替换后|
|termtablename|条款所属表名|VARCHAR(SZTERMTEXT)|用于定义当前条款所属的表，一般为单据主表，如果单据里有多种条款时，为条款对应的主表表名（审批单）|


合约条款填空表

|字段名|名称|类型|说明|
|------|------|------|------|
|stficode|条款填空内码|VARCHAR(SZIBILL)|条款填空内码|
|innercode|条款所属表内码|VARCHAR(SZIBILL)|条款所属表内码|
|idx|序号|INTEGER|序号|
|stftitle|填空项|VARCHAR(SZNAME)|填空项|
|stfvalue|填空值|VARCHAR(256)|填空值|
|flags|标识|SMALLINT|控制填空效果标识：(flags & 1) == 1：禁止编辑， (flags & 2) == 2：允许为空|
|termtablename|条款所属表名|VARCHAR(SZTERMTEXT)|用于定义当前条款所属的表，一般为单据主表，如果单据里有多种条款时，为条款对应的主表表名（审批单）|


合约条款单据条款字段

|字段名|名称|类型|说明|
|------|------|------|------|
|ttcode|合约条款模板编号|VARCHAR(SZTERMTEMPLATECODE)|用于存放条款模板定义编号|
|tmcode|合约条款公式编号|VARCHAR(SZTERMMACROCODE)|用于存放条款宏定义编号|
|termflags|合约条款标识|SMALLINT|((termflags & 1) == 1)：可手动添加条款，(termflags & 2) == 2：启用英文条款|


### 条款单据配置说明
用于定义各个单据上条款的具体配置信息：
配置文件路径（开发级别）：sn_ft\ft-trd\trd-ui\src\main\resources\cfg\res\sheetterm\SheetTermTblRef.xml

|字段名|名称|说明|
|------|------|------|------|
|sheetcode|单据类型|使用条款的单据类型|
|termtablename|条款所属表名|用于定义当前条款所属的表，一般为单据主表，如果单据里有多种条款时，为条款对应的主表表名（审批单）|
|relationcolname|条款所属表关系字段|条款上的innercode存放的是条款所属表中此字段的值，一般为表内码|
|termdetailtablename|当前条款明细表名|用于定义当前条款明细的物理表名，因为每个单据的每个条款所属表对应的条款明细表是不一样的|
|termfilltablename|当前条款填空表名|用于定义当前条款填空的物理表名|
|termsheetcodecolname|条款单据类型列名|条款所属表中用于匹配配置用的单据类型所在列名，如审批单的采购条款的适用单据类型列名为：ordsheetcode|
|ccodecolname|条款客商列名|条款所属表中用于匹配配置用的客商所在列名，如审批单的采购条款的适用客商列名为：purccode|



### 条款单据监听说明

#### 条款UI监听
snsoft.ft.trd.comm.term.ui.SheetTermUIListener.new?sheetCode=单据号&tgtUINames=监听UI名&termCfg=条款配置信息
UI系统功能
* sheetCode=单据号
* tgtUINames=监听UI名
* termCfg=条款配置信息（条款所属表名:条款明细表名,条款填空表名;） 如：ft_trd_prj_extse:ft_trd_termdetail,ft_trd_termfill;ft_trd_prj_pe:ft_trd_termdetail_A,ft_trd_termfill_A

#### 条款JS监听
#new snsoft.ft.trd.comm.term.SheetTermJSListener({sheetHistoryTermUI:选择历史条款的弹框UI,termTableName:条款所属表名, termTableUIName:条款所属表UI名})
UI系统功能，多套条款配置多个
* sheetHistoryTermUI:选择历史条款的弹框UI
* termTableName:条款所属表名
* termTableUIName:条款所属表UI名（表名和UI名可能不一致）
* ccodeColname:UI名.客商列名，
* termDetailTableName:条款明细UI名
* termFillTableName:条款填空UI名

#### 条款SV监听
snsoft.ft.trd.comm.term.service.impl.SheetTermSVListener.new
直接配置SV系统功能


#### 条款复制监听
snsoft.ft.trd.comm.term.service.impl.SheetTermCopyListener.new
单据复制需要复制条款时配置到复制定义上

#### 条款版本修改监听
snsoft.ft.trd.comm.term.service.impl.SheetTermVersionBackupListener.new
单据版本备份需要备份条款时配置到版本备份定义上


### 条款常用工具说明

#### 条款填空宏Tac

|方法名|参数|返回值|说明|
|------|------|------|------|
|getTermMainVo|无|当前条款所属表VO（级联）|返回条款所属表的VO，如审批单的采购条款时，返回的是对应供应商子表VO|
|getSheetMainVo|无|当前单据VO（级联）|返回单据的VO|
|getValue|String 表名, String 字段名|Object|根据指定表名和列名获取对应值，如果对应表为多行，返回第一行值|
|getSumValue|String 表名, String 字段名|Object|根据指定表名和列名获取对应合计值，只支持数值列|
|getName|String 码表名, Object 码值|String|根据指定码表和码值获取对应名称|
|percent|Number 值, int 小数位|String|转化为百分数|
|getDetailList|String 表名|List<VO>|获取表的List信息（只限于主表的子表）|
|getCodeDataColValue|String 码表名, String 商户（null为当前商户）, Object 码值, String 指定列|Object|获取码表中某一列的值，主要用于实施获取码表其他的显示名|