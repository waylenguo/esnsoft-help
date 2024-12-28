## 国际化

|日期|作者|备注|
|------|------|------|
|2018-09-25|朱郁琼|初稿|
|2019-12-13|冀小雷|修改|

### 资源规范

#### 配置根标题

指在配置文件中配置<code>&quot;${}&quot;</code>，则到该配置对应的系统资源文件中查找指定的key作为标题值：
1. 资源文件规则：<code>ResBundle[sysid].inf</code>;
1. 资源key规则：<code>title_[cfg_code]</code>;

支持此规则的配置有(**所有继承AbstractDefService的配置都有此功能**)：

<table>
	<tr>
		<th align='center'>配置</th>
		<th align='center'>Demo</th>
		<th align='center'>说明</th>
	</tr>
	<tr>
		<td>界面定义</td>
		<td>
		界面定义号：SN-CMC.Muiid<br/>
		```
		<B title="${}"...>
		```
		</td>
		<td>在资源文件ResBundleSN-CMC.inf中查找【title_SN-CMC.Muiid】对应的值</td>
	</tr>
	<tr>
		<td>单据定义</td>
		<td>
		单据定义号：SN-CMC.Sheet<br/>
		```
		<name>${}</name>
		```
		</td>
		<td>在资源文件ResBundleSN-CMC.inf中查找【title_SN-CMC.Sheet】对应的值</td>
	</tr>
	<tr>
		<td>权限定义</td>
		<td>
		权限定义号：SN-CMC.Limit<br/>
		```
		<datalimdef limnm="${}"...>
		```
		</td>
		<td>在资源文件ResBundleSN-CMC.inf中查找【title_SN-CMC.Limit】对应的值</td>
	</tr>
	<tr>
		<td>编码规则</td>
		<td>
		规则编码：SN-CMC.Accode<br/>
		```
		<name>${}</name>
		```
		</td>
		<td>在资源文件ResBundleSN-CMC.inf中查找【title_SN-CMC.Accode】对应的值</td>
	</tr>
	<tr>
		<td>单据复制</td>
		<td>
		复制编码：SN-CMC.SheetCopy<br/>
		```
		<Title>${}</Title>
		```
		</td>
		<td>在资源文件ResBundleSN-CMC.inf中查找【title_SN-CMC.SheetCopy】对应的值</td>
	</tr>
	<tr>
		<td>版本备份</td>
		<td>
		备份编码：SN-CMC.VB<br/>
		```
		<Title>${}</Title>
		```
		</td>
		<td>在资源文件ResBundleSN-CMC.inf中查找【title_SN-CMC.VB】对应的值</td>
	</tr>
	<tr>
		<td>单据撤单</td>
		<td>
		撤单编码：SN-CMC.Retract<br/>
		```
		<Title>${}</Title>
		```
		</td>
		<td>在资源文件ResBundleSN-CMC.inf中查找【title_SN-CMC.Retract】对应的值</td>
	</tr>
	<tr>
		<td>单据红冲</td>
		<td>
		红冲编码：SN-CMC.Red<br/>
		```
		redname="${}"
		```
		</td>
		<td>在资源文件ResBundleSN-CMC.inf中查找【title_SN-CMC.Red】对应的值</td>
	</tr>
</table>

#### 配置表列标题

此规范用于VO注解及界面定义使用：
1. VO校验：
	1. 默认规则为：sysid#tblname.colname#anno
		* sysid：通过@ValidResource进行注明；
		* tblname.colname：通过VO类@Table及属性@Column进行注明；
		* anno：通过Validation注解名称进行注明；
	1. 通过注解@ResKey注明中间部分tblname.colname的替代key；
1. 界面定义：
	1. 根节点：res_sysid指定当前页面的资源系统号；
	1. 根节点：all_c_columns指定所有末级节点认为是表列，并通过表列获取标题；
	1. ${}：获取当前窗口标题：title_muiid；
	1. ${RES.T}：获取当前界面标题：title_muiid_uiname；
	1. ${RES.C}：获取当前单元标题：tblname.cellid；
		* 支持表、共享表列；
		* 支持参数（根据使用该参数的表获取）；
		* 支持辅表；
	1. ${RES.F}：获取当前单元固定值：cellid；
	1. ${RES.$.XX}：RES.$意思是从当前界面对应的系统资源文件中获取值；
	1. ${RES.XX}：默认规则查找（其中的【?默认值】不再支持）；

### 国际化【资源文件】
#### 资源配置
```
配置文件格式：ResBundle[SysID](_[lang]).inf
SysID：系统ID
lang：语言，诸如zh,zh_CN,en,en_US等。
系统加载配置时会加载UI工程对应cfg/resbundle文件夹ResBundle*.inf文件，比如：
ResBundle*_en.inf文件：英文资源文件
ResBundle*_zh.inf文件：中文资源文件
```

00系统英文配置文件 ： ResBundle00_en.inf
```
SNSOFT=SNSOFT
NEWWINDOW=New Window
OPEN=Open
...
```
00系统中文配置文件 ：ResBundle00_zh.inf
```
SNSOFT=南北软件
NEWWINDOW=新窗口
OPEN=打开
...
```

#### 相关程序
```
获取指定系统的国际化资源：
ResBundle snsoft.commons.util.ResBundle.Factory.getResBundle(String sysid)
ResBundle snsoft.commons.util.ResBundle.Factory.getResBundle(String sysid,String local)
ResBundle snsoft.commons.util.ResBundle.Factory.getResBundle(String dataSource,String sysid,String local)
获取配置管理资源信息：
Object[][] snsoft.commons.resbundle.DefaultResBundle.getResource()
查询国际化资源服务：
snsoft.cmc.resbundle.service.CMCResbundleUIService
```
#### 简单应用
```
String	SNSOFT			= ResBundle.factory.getResBundle("00").getString("SNSOFT");
```
#### 异常资源配置

```
配置文件格式：ResBundleERR[SysID](_[lang]).inf
SysID：系统ID
lang：语言，诸如zh,zh_CN,en,en_US等。
系统加载配置时会加载UI工程对应cfg/resbundle文件夹ResBundleERR*.inf文件，比如：
ResBundleERR*_en.inf文件：英文资源文件
ResBundleERR*_zh.inf文件：中文资源文件
```

文件中配置：
```
配置格式：code=支持参数替换的字符串
0030=网络连接(%0)错误
0400=未定义数据源:%0
(%0)是由方法里传入的值
```

在代码中抛出异常时，使用BaseException对象，传入SysID和异常编码，就可以获得对应的异常信息。

```
以ResBundleERR00_zh.inf为例：
throw BaseException.newExceptionByErrCode("00", "0400", dsid)
```

### 国际化【校验信息】

#### 资源配置
ValidationMessages*.inf
```
配置文件格式：ValidationMessages[SysID](_[lang]).inf
SysID：系统ID
lang：语言，诸如zh,zh_CN,en,en_US等。
```

```
users.username.error=用户名不能为空
users.password.error=用户密码不能为空
gwcode.gwname.error=岗位名不能为空
ocode.otype.error=组织机构类型不能为空
ocode.oname.error=组织机构名称不能为空
ocode.opath.error=组织机构路径不能为空
ocode.sortpath.error=组织机构排序路径不能为空
```
#### 相关规范
```
系统加载配置时会加载工程对应src/main/resources/validation文件夹ValidationMessages*.inf文件
```
#### 相关程序
```
注册效验信息：
ReloadableResourceBundleMessageSource snsoft.dx.vo.config.VOConfig.messageSource()
LocalValidatorFactoryBean snsoft.dx.vo.config.VOConfig.validator()
获取效验器服务：
snsoft.dx.vo.validate.service.impl.ValidateServiceImpl
```
#### 简单应用
```
实体类VO属性text配置：
@NotNull(message = "{users.username.error}") 
text 
获取效验信息：
String info = validateService.getValidateMessage(VO);
```

### 语言代码表

|代码|语言|
|-|-|
|af|南非公用荷兰语|
|sq|阿尔巴尼亚语|
|ar-sa|阿拉伯语（沙特阿拉伯）|
|ar-iq|阿拉伯语（伊拉克）|
|ar-eg|阿拉伯语（埃及）|
|ar-ly|阿拉伯文（利比亚）|
|ar-dz|阿拉伯文（阿尔及利亚）|
|ar-ma|阿拉伯语（摩洛哥）|
|ar-tn|阿拉伯语（突尼斯）|
|ar-om|阿拉伯文（阿曼）|
|ar-ye|阿拉伯文（也门）|
|ar-sy|阿拉伯文（叙利亚）|
|ar-jo|阿拉伯文（约旦）|
|ar-lb|阿拉伯语（黎巴嫩）|
|ar-kw|阿拉伯文（科威特）|
|ar-ae|阿拉伯语（阿联酋）|
|ar-bh|阿拉伯语（巴林）|
|ar-qa|阿拉伯文（卡塔尔）|
|eu|巴斯克语|
|bg|保加利亚语|
|be|白俄罗斯语|
|ca|加泰罗尼亚语|
|zh-tw|中文（台湾）|
|zh-cn|中文（中华人民共和国）|
|zh-hk|中文（香港特区）|
|zh-sg|中文（新加坡）|
|hr|克罗地亚语|
|cs|捷克语|
|da|丹麦语|
|nl|荷兰语（标准）|
|nl-be|荷兰语（比利时）|
|en|英语|
|en-us|英语（美国）|
|en-gb|英语（英国）|
|en-au|英语（澳大利亚）|
|en-ca|英语（加拿大）|
|en-nz|英语（新西兰）|
|en-ie|英国（爱尔兰）|
|en-za|英语（南非）|
|en-jm|英语（牙买加）|
|en|英语（加勒比海）|
|en-bz|英语（伯利兹）|
|en-tt|英语（特立尼达和多巴哥）|
|et|爱沙尼亚语|
|fo|法罗语|
|fa|波斯语|
|fi|芬兰语|
|fr|法语（标准）|
|fr-be|法语（比利时）|
|fr-ca|法语（加拿大）|
|fr-ch|法语（瑞士）|
|fr-lu|法语（卢森堡）|
|gd|盖尔语（苏格兰）|
|ga|爱尔兰语|
|de|德语（标准）|
|de-ch|德语（瑞士）|
|de-at|德语（奥地利）|
|de-lu|德语（卢森堡）|
|de-li|德语（列支敦士登）|
|el|希腊|
|he|希伯来语|
|hi|印地文|
|hu|匈牙利语|
|is|冰岛语|
|id|印度尼西亚语|
|it|意大利语（标准）|
|it-ch|意大利语（瑞士）|
|ja|日语|
|ko|韩语|
|ko|韩文（韓文）|
|lv|拉脱维亚语|
|lt|立陶宛语|
|mk|马其顿语（前南马其顿）|
|ms|马来西亚语|
|mt|马耳他语|
|no|挪威文（巴克摩）|
|no|挪威文（耐诺斯克）|
|pl|波兰语|
|pt-br|葡萄牙语（巴西）|
|pt|葡萄牙语（葡萄牙）|
|rm|里托罗曼斯文|
|ro|罗马尼亚语|
|ro-mo|罗马尼亚语（摩尔多瓦共和国）|
|ru|俄语|
|ru-mo|俄罗斯（摩尔多瓦共和国）|
|sz|萨米语（拉普语）|
|sr|塞尔维亚语（西里尔文）|
|sr|塞尔维亚语（拉丁）|
|sk|斯洛伐克语|
|sl|斯洛文尼亚语|
|sb|索布语|
|es|西班牙语（西班牙）|
|es-mx|西班牙语（墨西哥）|
|es-gt|西班牙语（危地马拉）|
|es-cr|西班牙语（哥​​斯达黎加）|
|es-pa|西班牙语（巴拿马）|
|es-do|西班牙文（多米尼加共和国）|
|es-ve|西班牙语（委内瑞拉）|
|es-co|西班牙语（哥​​伦比亚）|
|es-pe|西班牙语（秘鲁）|
|es-ar|西班牙语（阿根廷）|
|es-ec|西班牙语（厄瓜多尔）|
|es-cl|西班牙语（智利）|
|es-uy|西班牙语（乌拉圭）|
|es-py|西班牙语（巴拉圭）|
|es-bo|西班牙语（玻利维亚）|
|es-sv|西班牙语（萨尔瓦多）|
|es-hn|西班牙语（洪都拉斯）|
|es-ni|西班牙语（尼加拉瓜）|
|es-pr|西班牙语（波多黎各）|
|sx|苏图语|
|sv|瑞典语|
|sv-fi|瑞典语（芬兰）|
|th|泰国语|
|ts|特松加语|
|tn|茨瓦纳语|
|tr|土耳其语|
|uk|乌克兰语|
|ur|乌尔都语|
|ve|文达语|
|vi|越南语|
|xh|科萨语|
|ji|意第绪语|
|zu|祖鲁语|