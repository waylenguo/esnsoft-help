## 技术概念提纲

### 总纲

#### 常用索引
<table>
	<tr>
		<th align="center">发版说明</th>
		<th align="center">开发规范</th>
		<th align="center">常用中间件</th>
		<th align="center">常用配置</th>
		<th align="center">非开发工具</th>
	</tr>
	<tr>
		<td>
			[版本发布日志](help.html?helpFile=help/SN-HELP/Release/ReleaseLog.md)
		</td>
		<td>
			[开发步骤](../SN-HELP/DeveloperGuide/DeveloperStep.md)
		</td>
		<td>
			[MySQL](../SN-HELP/Deploy/MaintainDeploy.md#MySQL) [Oracle](../SN-HELP/Deploy/MaintainDeploy.md#Oracle)
		</td>
		<td>
			[System.properties](../SN-HELP/Deploy/System.md)
		</td>
		<td>
			[前端自动化测试](../SN-HELP/Standard/DevTest/WebAutoTest.md)
		</td>
	</tr>
	<tr>
		<td>
			[底层开发清单](help.html?helpFile=help/SN-HELP/Release/DevelopList.md)
		</td>
		<td>
			[项目开发规范](../SN-HELP/Standard/DevCode/DevCode.md)
		</td>
		<td>
			[Redis](../SN-HELP/Deploy/MaintainDeploy.md#Redis) [MongoDB](../SN-HELP/Deploy/MaintainDeploy.md#MongoDB)
		</td>
		<td>
			[logback.xml](../SN-HELP/Deploy/logback.md)
		</td>
		<td>
			[培训大纲](../SN-HELP/Trans/Outline.md)
		</td>
	</tr>
	<tr>
		<td>
			[版本发布说明](help.html?helpFile=help/SN-HELP/Release/VersionRelease.md)
		</td>
		<td>
			[开发测试规范](../SN-HELP/Standard/DevTest/DevTest.md)
		</td>
		<td>
			[Zookeeper](../SN-HELP/Deploy/MaintainDeploy.md#Zookeeper)
		</td>
		<td>
			[常用监听/服务](../SN-PLAT/Tools/Listeners.md)
		</td>
		<td>
			[效率优化](../SN-HELP/DeveloperGuide/Tune.md)
		</td>
	</tr>
</table>

#### 文档规范

1. 各模块的帮助文档尽可能仅存在一个，方便查阅；
1. 各模块的文档包含三部分：
	1）Guide(指南)：介绍功能干什么的，怎么使用的，初步接触的人员使用；
	2）Reference(手册)：当前模块的各个功能点及功能参数说明，具体使用人员或维护人员使用；
	3）Design(设计及理念)：功能开发维护人员使用；
	4）Best Practice(最佳实践)：结合具体场景，Step by Step；
1. 帮助文档的标题从二级开始；
1. 尽可能避免截图；
1. 界面定义根节点上挂帮助文档：
<pre>
uiprops.helpFile="help/xxx/xxx.md"
uiprops.helpFile="help/xxx/xxx.md#xxx"
</pre>

#### 发版规范

1. 原则上禁止任何项目使用Beta版本进行开发，只能使用上一个版本；
	**对于新增加的功能，无法及时封板，可以手工拷贝到上一个分支上供使用或测试**
	**为经测试的功能，可以发布4.n-SNAPSHOT，测试完成后发布4.n.m.RELEASE**
1. 原则上仅保留一个稳定版本；
	**4.1.x -> 4.2.x -> ... -> 4.n.x：仅维护最后一个稳定版本，新项目一律使用最后一个稳定版本**
1. 原则上稳定版本仅修改bug，不增加新功能；
	**<font color="red">每周四发版，如无修改则顺延!</font>**
	
#### 信息安全

[信息安全](Security/InformationSecurity.md)

### 分类索引

<div style="display:flex;flex-wrap:wrap;">
	<table style="display:inline-block;width:auto;margin-right:10px">
		<tr>
			<th colspan="5" align="center">UI层</th>
		</tr>
		<tr>
			<th align="center">展示</th>
			<th align="center">码表</th>
			<th align="center">界面技术</th>
			<th align="center">类文档</th>
			<th align="center">接入</th>
		</tr>
		<tr>
			<td>[界面定义](UI/UI.md)</td>
			<td>[码表定义](Codetbl/Codetbl.md)</td>
			<td>[DataSet值替换](XJS/ReplaceValues/ReplaceValues.md)</td>
			<td>[客户端核心逻辑](../SN-HELP/Xjs/CoreUML.md)</td>
			<td>[SSO](../SN-HELP/Product/SSO/SSO.md)
				[登录相关](../SN-HELP/Product/SSO/Login.md)
			</td>
		</tr>
		<tr>
			<td>[界面属性控制](OptCtrl/OptCtrl.md)
				[界面路由定义](MuiFunc/MuiFunc.md)
				[界面定义扩展](Muiext/Muiext.md)
			</td>
			<td>[辅助录入](../SN-HELP/Xjs/AidInput.md)</td>
			<td>[合计值](../SN-HELP/Xjs/Bottomval.md#合计值)
				[计算公式](../SN-HELP/Xjs/Bottomval.md#计算公式)
				[分组小计](../SN-HELP/Xjs/Bottomval.md#分组小计)
			</td>
			<td>[客户端常用类](../SN-HELP/Xjs/ClientClass.md)</td>
			<td>[远程调用](../SN-HELP/Xjs/Rinvoke/Rinvoke.md)</td>
		</tr>
		<tr>
			<td>[界面个性信息](UI/UserTblInfo.md)</td>
			<td>[字典](Dict/Dictdef.md)</td>
			<td>[Render](../SN-HELP/Xjs/Render/Render.md)
				[存储转换接口](../SN-HELP/Xjs/Render/Render.md)
			</td>
			<td>[客户端监听](XJS/DefListener/DefListener.md)
				[DefaultListener](../SN-HELP/Xjs/DefaultListener/DefaultListener.md)
			</td>
			<td>[UI处理](../SN-HELP/Server/UI/UICompBasService.md)</td>
		</tr>
		<tr>
			<td>[菜单定义](Menu/DevMenu.md)</td>
			<td>[构建动态列](BuildColumn/BuildColumn.md)</td>
			<td>[**共享数据集/存储**]()</td>
			<td>[JS工具类](../SN-HELP/Xjs/Tools/Tools.md)</td>
			<td>[UI监听](../SN-HELP/Server/UIListener/UIListener.md)</td>
		</tr>
		<tr>
			<td>[界面定义创建工具](Tool/UICREATEXML/UICREATEXML.md)</td>
			<td>[码表映射](CodeRela/CodeRela.md)</td>
			<td>[数据交叉](../SN-HELP/Xjs/Bottomval.md#数据交叉)</td>
			<td>[前端自动化测试](../SN-HELP/Standard/DevTest/WebAutoTest.md)</td>
			<td>[UI-Servlet](../SN-HELP/Server/UI/UIServlet.md)</td>
		</tr>
		<tr>
			<td>[DOM组件](../SN-HELP/Demo/UI/ModuleDemo.md)</td>
			<td></td>
			<td>[图形码](Graphiccode/Graphiccode.md)</td>
			<td></td>
			<td>[Websocket](Websocket/Websocket.md)
				[WebService](WebService/WebService.md)
			</td>
		</tr>
		<tr>
			<td>[国际化](International/International.md)
				[多语言](../SN-HELP/Demo/LangAll/LangAll.md)
			</td>
			<td></td>
			<td>[数据加载示例](../SN-HELP/Xjs/DataLoad/DataLoad.md)</td>
			<td></td>
			<td></td>
		</tr>
	</table>
	<table  style="display:inline-block;width:auto;margin-right:10px">
		<tr>
			<th align="center">开发规范</th>
		</tr>
		<tr>
			<td>[项目开发规范](../SN-HELP/Standard/DevCode/DevCode.md)</td>
		</tr>
		<tr>
			<td>[异常处理规范](../SN-HELP/Standard/DevException/DevException.md)</td>
		</tr>
		<tr>
			<td>[服务规范](../SN-HELP/Standard/DevService/DevService.md)
			[服务调用](../SN-HELP/Server/Service/Service.md)
			</td>
		</tr>
		<tr>
			<td>[开发测试规范](../SN-HELP/Standard/DevTest/DevTest.md)</td>
		</tr>
		<tr>
			<td>[客户端开发规范](../SN-HELP/Standard/DevXJS/DevXJS.md)</td>
		</tr>
		<tr>
			<td>[前后端分离开发](../SN-HELP/DeveloperGuide/DeveloperSeparate.md)</td>
		</tr>
		<tr>
			<td>[开发步骤](../SN-HELP/DeveloperGuide/DeveloperStep.md)</td>
		</tr>
		<tr>
			<td>[Java2JS](../SN-HELP/Environment/Java2Js/Java2Js.md)</td>
		</tr>
		<tr>
			<td>[三方交互规范](../SN-HELP/Standard/DevService/Interaction.md)</td>
		</tr>
		<tr>
			<td>[工具类](../SN-HELP/Server/Tools/Tools.md)</td>
		</tr>
		<tr>
			<td>[java8新特性](../SN-HELP/DevTools/Java/Java8.md)</td>
		</tr>
		<tr>
			<td>[实体类VO](Service/VO/VO.md)</td>
		</tr>
		<tr>
			<td>[读取配置](../SN-HELP/Server/ReadConfig/ReadConfig.md)</td>
		</tr>
	</table>
	<table  style="display:inline-block;width:auto;margin-right:10px">
		<tr>
			<th align="center">服务层</th>
		</tr>
		<tr>
			<td>[持久化](Service/Persistence.md)</td>
		</tr>
		<tr>
			<td>[　表达式](Service/Persistence.md#表达式)</td>
		</tr>
		<tr>
			<td>[　Database](Service/Persistence.md#DB)</td>
		</tr>
		<tr>
			<td>[　DAO](Service/Persistence.md#DAO)</td>
		</tr>
		<tr>
			<td>[　MongoDAO](Service/Persistence.md#MongoDAO)</td>
		</tr>
		<tr>
			<td>[　账套](Service/Persistence.md#账套)</td>
		</tr>
		<tr>
			<td>[　文件系统](FileSystem/FileSystem.md)</td>
		</tr>
		<tr>
			<td>[缓存](Cache/Cache.md)</td>
		</tr>
	</table>
	<table style="display:inline-block;width:auto;margin-right:10px">
		<tr>
			<th align="center" colspan="3">单据相关</th>
		</tr>
		<tr>
			<th align="center">核心</th>
			<th align="center">扩展</th>
			<th align="center">附件</th>
		</tr>
		<tr>
			<td>[单据定义](Busheet/Busheet.md)</td>
			<td>[单据复制](../SN-PLAT/Sheet/SheetCopy.md)</td>
			<td>[**单据附件**](../SN-PLAT/Sheet/SheetAttach.md)</td>
		</tr>
		<tr>
			<td>[单据权限](Limit/Datalimit.md#单据权限关系)</td>
			<td>[版本备份](../SN-PLAT/Sheet/VersionBackup.md)</td>
			<td>[单据打印](../SN-PLAT/Sheet/SheetPrint.md)</td>
		</tr>
		<tr>
			<td>[单据审批](../SN-APPR/APPR/APPR.md#单据审批关系)</td>
			<td>[单据撤单](../SN-PLAT/Sheet/Retract.md)</td>
			<td>[文档类型](../SN-PLAT/Sheet/SheetDoc.md)</td>
		</tr>
		<tr>
			<td>[单据编码规则](Accode/Accode.md#单据编码规则)</td>
			<td>[单据红冲](../SN-PLAT/Sheet/SheetRed.md)</td>
			<td>[打印格式](../SN-PLAT/Sheet/SheetDoc.md)</td>
		</tr>
		<tr>
			<td>[默认值](Service/VO/VOListener.md#默认值)
				[数据校验](Service/VO/VOListener.md#单据数据校验)
			</td>
			<td>[单据异步处理](../SN-PLAT/Async/AsyncDef.md)</td>
			<td>[OfficeView](../SN-HELP/DevTools/OfficeView/PreView.md)</td>
		</tr>
	</table>
	<table style="display:inline-block;width:auto;margin-right:10px">
		<tr>
			<th align="center">ERP基础</th>
		</tr>
		<tr>
			<td>[组织结构](Bcode/Bcode.md)</td>
		</tr>
		<tr>
			<td>[数据权限](Limit/Datalimit.md)/[注解](../SN-HELP/Server/AuthParam/AuthParam.md)/[概要](Limit/LimitOutline.md)
				[菜单权限](Limit/Menulimit.md)
			</td>
		</tr>
		<tr>
			<td>[流程审批](../SN-APPR/APPR/APPR.md)</td>
		</tr>
		<tr>
			<td>[编码规则](Accode/Accode.md)</td>
		</tr>
		<tr>
			<td>[打印格式](Print/PrintDef.md)</td>
		</tr>
	</table>
	<table style="display:inline-block;width:auto;margin-right:10px">
		<tr>
			<th align="center">常用工具</th>
		</tr>
		<tr>
			<td>[系统功能](../SN-PLAT/Tools/SystemFunction.md)</td>
		</tr>
		<tr>
			<td>[查询工具](../SN-PLAT/Tools/QueryToolDef.md)</td>
		</tr>
		<tr>
			<td>[穿透定义](../SN-PLAT/Tools/ViewDetail.md)</td>
		</tr>
		<tr>
			<td>[界面数据分组](../SN-PLAT/Tools/UIDataGroup.md)</td>
		</tr>
		<tr>
			<td>[编码引用](../SN-PLAT/Tools/CodeRef.md)</td>
		</tr>
		<tr>
			<td>[数据拷贝](../SN-PLAT/Tools/DataCopy.md)</td>
		</tr>
		<tr>
			<td>[数据核销](../SN-PLAT/Tools/CorDef.md)</td>
		</tr>
		<tr>
			<td>[脚本配置](Tool/Script.md)</td>
		</tr>
		<tr>
			<td>[数据格式转换](Tool/Conver/Conver.md)</td>
		</tr>
	</table>
	<table style="display:inline-block;width:auto;margin-right:10px">
		<tr>
			<th align="center" colspan='2'>管控中心</th>
		</tr>
		<tr>
			<th align="center">运维</th><th align="center">工具</th>
		</tr>
		<tr>
			<td>[配置管理](Configkit/Configkit.md)</td>
			<td>[管控功能权限](Limit/CMCLimit.md)</td>
		</tr>
		<tr>
			<td>[创建表结构](CreateTable/CreateTable.md)</td>
			<td>[系统选项](SysOpTionsFile/SYSOPTIONSFILE.md)</td>
		</tr>
		<tr>
			<td>[生成MDB索引](Mongo/MGCreateIndexes.md)</td>
			<td>[数据导入导出](EDI/DataExpImp.md)</td>
		</tr>
		<tr>
			<td>[表结构字典](../SN-PLAT/Tools/MetaDict.md)</td>
			<td>[Excel导入](BasTool/ExcelImport.md)</td>
		</tr>
		<tr>
			<td>[数据库分片](ShardingDB/ShardingDB.md)</td>
			<td>[SQL工具](SQL/Execsql.md)
				[Tac工具](Tac/Exectac.md)
				[MongoDB工具](Mongo/MongoDB.md)
			</td>
		</tr>
		<tr>
			<td>[静态资源发布](ReleaseSR/ReleaseStaticResource.md)</td>
			<td>[分词](Segment/Segment.md)</td>
		</tr>
		<tr>
			<td>[服务器监控](Server/ServerInfo.md)</td>
			<td>[数据处理定义](Tool/SysPatch/SysPatch.md)</td>
		</tr>
		<tr>
			<td></td>
			<td>[定时任务](TimeRtask/TIMERTASK.md)</td>
		</tr>
	</table>
	<table style="display:inline-block;width:auto;margin-right:10px">
		<tr>
			<th align="center">监听功能</th>
		</tr>
		<tr>
			<td>[动态显示列组](../SN-HELP/Xjs/ColumnVisible/ColumnVisible.md)</td>
		</tr>
		<tr>
			<td>[拖拽排序](../SN-HELP/Demo/Util/Switchsort.md)</td>
		</tr>
	</table>
	<table style="display:inline-block;width:auto;margin-right:10px">
		<tr>
			<th align="center" colspan="5">运维相关</th>
		</tr>
		<tr>
			<th align="center">系统</th>
			<th align="center">数据库</th>
			<th align="center">中间件</th>
			<th align="center">容器</th>
			<th align="center">其他</th>
		</tr>
		<tr>
			<td>[安装VMWare](../SN-HELP/Deploy/MaintainDeploy.md#安装VMWare)</td>
			<td>[MySQL](../SN-HELP/Deploy/MaintainDeploy.md#MySQL)</td>
			<td>[Zookeeper](../SN-HELP/Deploy/MaintainDeploy.md#Zookeeper)</td>
			<td>[Tomcat](../SN-HELP/Deploy/MaintainDeploy.md#Tomcat)</td>
			<td>[Git安装](../SN-HELP/Deploy/MaintainDeploy.md#Git安装)</td>
		</tr>
		<tr>
			<td>[创建虚拟机](../SN-HELP/Deploy/MaintainDeploy.md#创建虚拟机)</td>
			<td>[MongoDB](../SN-HELP/Deploy/MaintainDeploy.md#MongoDB)</td>
			<td>[Kafka](../SN-HELP/Deploy/MaintainDeploy.md#Kafka)</td>
			<td>[Nginx](../SN-HELP/Deploy/MaintainDeploy.md#Nginx)</td>
			<td>[SVN安装](../SN-HELP/Deploy/MaintainDeploy.md#SVN安装)</td>
		</tr>
		<tr>
			<td>[安装Linux](../SN-HELP/Deploy/MaintainDeploy.md#安装Linux)</td>
			<td>[ElasticSearch](../SN-HELP/Deploy/MaintainDeploy.md#ElasticSearch)</td>
			<td></td>
			<td></td>
			<td>[Maven安装](../SN-HELP/Deploy/MaintainDeploy.md#Maven安装)</td>
		</tr>
		<tr>
			<td>[JDK](../SN-HELP/Deploy/MaintainDeploy.md#JDK)</td>
			<td>[Redis](../SN-HELP/Deploy/MaintainDeploy.md#Redis)</td>
			<td></td>
			<td></td>
			<td>[Jenkins安装](../SN-HELP/Deploy/MaintainDeploy.md#Jenkins安装)</td>
		</tr>
	</table>
</div>
