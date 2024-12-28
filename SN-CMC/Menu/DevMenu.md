## 菜单模版

|日期|作者|备注|
|------|------|------|
|2018-09-25|郭鑫|初稿|
|2019-11-11|郭鑫|修改|

### 指南

#### 概念说明

菜单配置是用来配置系统所拥有的菜单命令号、对应界面、功能作者、菜单标题的功能。其配置方式分为配置文件和数据库配置两种，数据库配置优先级大于文件配置。
注意：开发使用配置文件。

#### 使用说明

##### 菜单配置

* 菜单配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
		<menumodel id="91" title="帮助中心"xmlns="http://www.snsoft.com.cn/schema/Menu">
			<!--第一级菜单-->
			<menu title="功能示例" iconres="system11">
				<!--第二级菜单-->
				<menu title="项目管理1" iconres="menu24">
					<!--第三级菜单-->
					<menu title="项目管理">
						<!--第四级菜单-->
						<menu title="项目管理" cmd="ui:项目管理" cmdid="项目管理" author="测试" />
					</menu>
				</menu>
				<!--第二级菜单-->
				<menu title="项目管理2" iconres="menu24">
					<!--第三级菜单-->
					<menu title="项目管理">
						<!--第四级菜单-->
						<menu title="项目管理" cmd="ui:项目管理" cmdid="项目管理" author="测试" />
					</menu>
				</menu>
				<!--引入其他菜单定义 subMenu 引入二级菜单，该引入方式与当前二级菜单平级-->
				<include id="90" sysid="90" subMenu="true"/>
			</menu>
			<!--引入其他菜单定义-->
			<include id="引入的menumodel-id" sysid="引入的menumodel-id" title="引入其他菜单定义" />
	</menumodel>
```

* 菜单引入配置

menumodel-include-id： id并没有被使用，跟sysid保持一致就好了
menumodel-include-sysid：嵌入其他模块菜单的menumodel.id
menumodel-include-title：描述文字
menumodel-include-subMenu:引入二级菜单，根菜单不引入

##### 图标资源

```
菜单图标资源存放位置在home-icons.css文件中，对应的配置列为  menumodel-menu-iconres，
/**系统图标 **/

i[class^="icol-system"] {
	display: inline-block;
	width: 54px;
	height: 54px;
	background: url(../res/images/icons/home-sysmenu.png) no-repeat;
	background-clip: border-box;
	vertical-align: top;
}

i.icol-system00 {
	background-position: -148px -74px
}

/*领导审批*/
i.icol-system01{
	background-position:0px 0px;
}
/*基础资料*/
i.icol-system02{
	background-position:-74px 0px;
}
```

首页系统图标

![](help/SN-CMC/UI/img/main-uidoc-src45.png)

更多参见[首页系统图标：](mainpage/css/system-icons.html)

首页菜单图标

![](help/SN-CMC/UI/img/main-uidoc-src46.png)

更多参见[首页菜单图标：](mainpage/css/left-menu-icons.html)

##### 相关参数配置

```
配置登录页：LoginPage=IRMP-Login.html，默认Login.html
免密登录：Diag._IgnorePWD=true
验证码：Login.Authcode="*"
默认样式theme0：UI.Theme=0
语言配置：APP.Languages=en_US,zh_CN
默认使用语言：Login.DefautLanguage=zh_CN
登录成功后的跳转页面配置：HomePage=IRMP-MainPage.html，默认MainPage.html
系统标题：SN.TITLE=智慧进出口工业品风险管理信息化平台
```

##### 相关程序

1. 获取系统模块：snsoft.bas.config.impl.AppConfigLoader
1. 获取菜单模块：snsoft.bas.config.impl.AppMenuLoader
1. 加载系统菜单方法：snsoft.bas.config.impl.AppMenuLoader.loadMenu（1、缓存加载2、解析属性）
1. 加载系统菜单过程中过滤权限的方法：snsoft.bas.config.impl.AppMenuLoader.getLimitMenu
1. 菜单过滤权限默认过滤器：snsoft.bas.config.impl.AppMenuLoader.LimMemuFilter
1. 菜单加载过程中处理cmd属性值： snsoft.bas.config.impl.AppMenuLoader.alterCommand
1. 菜单显示页面：MainPage.html
1. 菜单显示页面处理对象：snsoft.mainpage.MainHomePage
1. 菜单显示页面加载初始化数据方法：snsoft.bas.ui.service.homepage.HomePageService.loadHomePageInfo
1. 打开界面设置默认值方法：xjs.ui.util.InitValues.get
1. 菜单显示页面打开界面方法：snsoft.mainpage.MainHomePage.openItemMenu

### 手册

#### 菜单配置

* 数据库配置对应菜单位置：管控中心-开发配置-[菜单模板【配置文件】](uiinvoke/00/zh_CN/theme0/90.Menu.DevMenu.html)

* 配置文件对应菜单位置：管控中心-开发配置-[菜单模板【数据库】](uiinvoke/00/zh_CN/theme0/90.Menu.Menu.html)

1. 配置文件存放位置：菜单配置文件存放UI工程，资源目录src/main/resource下面snsoft/menu/Menu*.xml
1. 配置文件命名要求："Menu"+系统号+".xml"，如 MenuSN-MQ.xml
1. 菜单定义需增加作者，用于快速找到对应的开发人员

文件格式参见【[Menu.xsd](do/BrowseSchema?schema=Menu.xsd)】

#### 菜单权限

菜单可按照用户、岗位分配权限
超级管理员具有所有的菜单权限
注意：分配菜单时不能看父级勾选状态，需要展开到最末级具体菜单进行分配
更多参见[菜单权限：](help.html?helpFile=help/SN-CMC/Limit/MenuLimit.md)

### 实践

### 设计







