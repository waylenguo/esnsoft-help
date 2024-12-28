## 菜单权限

|日期|作者|备注|
|------|------|------|
|2018-09-25|郭鑫|初稿|
|2019-11-14|郭鑫|修改|

### 指南

#### 概念说明

菜单权限分为三种分配方式： 岗位菜单权限、用户菜单权限、部门菜单权限。
1. 在实际运维中强烈建议使用岗位菜单权限分配，不建议使用用户菜单权限、部门菜单权限。
1. 菜单权限是根据对应的菜单命令号 cmdid 分配权限的，菜单命令号cmdid相同则权限相同；cmdid为空则默认拥有该菜单权限。
1. 一般使用窗口号作为菜单命令号 cmdid。
1. 超级管理员有所有菜单的权限。

#### 使用说明

### 手册

#### 菜单权限操作

* 批量操作[含下级]

批量勾选菜单（可以是中间级）后，点击批量操作按钮选择设置权限或取消权限。
注意：
1. 系统“菜单权限”是根据“菜单命令号”设置权限的，所以对某个菜单设置权限相当于设置了所有“菜单命令号”相同菜单的权限。
1. “批量操作”除了设置当前选中行外同时设置下级所有菜单的权限。


* 清理菜单权限数据

在实施过程中，可能会出现修改命令行的情况，这时会造成菜单权限表中存在垃圾数据。
点击该按钮后将菜单权限表中的所有垃圾数据清空。

#### 菜单位置

* 管控中心->实施配置->菜单权限配置-> [岗位菜单权限](uiinvoke/00/zh_CN/theme0/90.lim.MenulimsForGwcode.html)
* 管控中心->实施配置->菜单权限配置-> [岗位菜单权限(跨商户)](uiinvoke/00/zh_CN/theme0/90.lim.MenulimsForGwcodeCuicode.html)
**注意：未启用多商户时，此功能不可用**
* 管控中心->实施配置->菜单权限配置-> [用户菜单权限](uiinvoke/00/zh_CN/theme0/90.lim.MenulimsForUser.html)
* 管控中心->实施配置->菜单权限配置-> [部门菜单权限](uiinvoke/00/zh_CN/theme0/90.lim.MenulimsForBcode.html)

#### 自定义菜单权限过滤

1. 默认菜单权限过滤器：snsoft.bas.config.impl.AppMenuLoader.LimMemuFilter
1. 自定义权限过滤器实现java.util.function.Predicate<Menu>接口
1. 添加系统选项APP.MENUFILTER配置自定义过滤器（多个过滤器用逗号分隔）
**注意**：菜单权限分配之后，如果用户自己指定URL打开没有权限的界面也是可以打开的,这里只控制用户登录后能看到的菜单，不会对打开地址做拦截。

#### 菜单权限工具

* 管控中心->实施配置->菜单权限配置-> [菜单权限分配查询](uiinvoke/00/zh_CN/theme0/90.lim.MenulimsSetQuery.html)

查询指定菜单分配给了哪些岗位、用户、部门

* 管控中心->实施配置->菜单权限配置-> [菜单权限数据查询](uiinvoke/00/zh_CN/theme0/90.lim.MenulimsQuery.html)

查询物理存储的菜单权限数据。

* 管控中心->实施配置->菜单权限配置-> [用户菜单权限查询](uiinvoke/00/zh_CN/theme0/90.lim.UserMenuDataLimQuery.html)

以某用户为主线，查询该用户的菜单权限，可以用于核对和测试某用户权限是否正确。

### 实践

### 设计


* 菜单时序图

@startuml
actor  用户
用户 -> HomePageService : [loadHomePageInfo]进入主页，加载主页信息
HomePageService -> HomePageImpl : [loadHomePageInfo]取主页信息
HomePageImpl -> AppMenuLoader : [loadMenu] 获取菜单信息
AppMenuLoader -> MenuLimitProcess : [accept] 过滤权限数据
AppMenuLoader -> HomePageImpl : 返回有权限菜单
HomePageImpl -> HomePageService : 返回菜单信息
HomePageService -> 用户 : 展示菜单给用户
@enduml
