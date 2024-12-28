## 界面路由定义

|日期|作者|备注|
|------|------|------|
|2018-06-05|张东|初稿|
|2018-10-23|冀小雷|修订|
|2019-10-10|张东|调整|

### 指南

#### 概念说明

在同一个系统中，不同的业务组织(部门)和商户(启用商户时)使用同一功能菜单，但要求各自打开的界面样式不尽相同时，需要根据部门和商户匹配不同的界面定义，可以使用此定义配置；
此功能同时支持数据库和配置文件，由于子表定义存在组织、商户这些经常变化的配置项，所有配置文件只支持主表不支持子表。

#### 使用说明

1. 数据库

    **菜单维护地址：开发配置-界面配置-界面路由定义**
    ```properties
    左右为主子表，左为主表[muifunc]，右为子表[muifuncg]
    ```

1. 配置文件

    ```properties
    界面路由定义的配置文件属于配置系统管理范围，配置文件方式一般用于开发阶段使用，** 注意： **配置文件只支持路由号窗口号一对一配置，不区分商户部门。
    配置文件位置：ui/func/MuiFunc[sysid].xml
    ```
   
1. 示例：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <MuiFuncs xmlns="http://www.snsoft.com.cn/schema/MuiFunc.xsd">
        <MuiFunc funcid="90.Users1" funcname="用户维护1" muiid="90.Users1" />
        <MuiFunc funcid="90.Users2" funcname="用户维护2" muiid="90.Users2" />
    </MuiFuncs>
    ```

### 手册

1. 文件配置
```properties
    funcid：界面路由号，唯一标识当前菜单界面的路由号。
    funcname：路由名称。
    muiid：路由对应的窗口号。
```
1. 数据库配置
```properties
#主表
    文件：标识此行配置是通过XML配置文件定义的；
    路由编号：对应某个菜单功能；
    路由名称：定义时使用菜单名称；
    默认窗口号(muiid)：通过子表匹配无法找到一个窗口号时默认的窗口号，如果没有定义返回“匹配编号”作为窗口号；
#子表
    窗口号：某个匹配条件对应的窗口号；
    商户：启用商户的状态下，用于配合部门来唯一确定窗口号；
    部门：用于配合启用商户状态下的商户来唯一确定窗口号；
    TAC公式：根据当前登录人判断应该打开那个界面。

    主表定义匹配编号，配合子表定义来根据商户（启用）和部门匹配多个窗口号，最后对应到某个菜单上使用。
```

1. 菜单配置
菜单模板上，要想开启使用界面路由，要在菜单模板配置的cmd（路由调用）属性配置里加上前缀【&#64;】，后边跟路由号即可。
示例：
```xml
	<menu title="用户维护" cmd="@90.Users1" cmdid="90.Users" />
```

### 服务方式

功能还提供了配置服务Bean的方式返回业务逻辑的界面号：
1. 配置格式：sv:SN-UI.FuncService?['xx']
1. 菜单配置方式
```xml
	<menu title="销售合同" cmd="sv:SN-HELP.SalorderFuncPrototypeService?[{xxx:'xxxx'}]" cmdid="SN-HELP"/>
```
1. 服务实现标准接口：
```java
//服务需要为原型模式：ConfigurableBeanFactory.SCOPE_PROTOTYPE
snsoft.bas.config.muifunc.service.FuncPrototypeService
//重写方法
String getUiid();
```
### 实践