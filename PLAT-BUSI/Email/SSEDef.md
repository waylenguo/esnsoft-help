## 【单据主动发送邮件配置】

设计人：【聂明笛】      设计日期  ：【2022-06-10】    宣讲日期 ：【2022-06-10】

### TAC使用说明

#### 启动公式tac
* 添加替换宏$2("key",value)//为正文中替换宏添加参数
* 添加抄送人$1(usercode)//动态添加抄送人

#### 邮箱主题tac

* 取主表中字段值可使用$主表字段名方式获取
取主表字段，如：客商
```
$ccode//界面上改字段的显示值。
$code$ccode//界面上客商字段的编码。
$nm$ccode//界面上客商的名称。
```
* 可用常量：
mainDataSet：主表数据集:当前单据的主表DataSet,可在tac中如下方式获取
取主表字段，如：客商
```
mainDataSet.getValue("ccode")//界面上改字段的显示值。
mainDataSet.getValue("code$ccode")//界面上客商字段的编码。
mainDataSet.getValue("nm$ccode")//界面上客商的名称。
```
* 取子表数据集$1(子表界面名称):可获取其他表的数据DataSet。
```
gDataSet  = 取子表数据集$1("salorderg")//获取到子表数据集
gDataSet.getValue("ccode")//界面上改字段的显示值。
gDataSet.getValue("code$ccode")//界面上客商字段的编码。
gDataSet.getValue("nm$ccode")//界面上客商的名称。
```
* 主题tac示例
例如【内贸销售发货单】发送邮件时主题规则为“TO:xx客户名称”
```
"TO:"+$salccode//写法1
"TO:"+$nm$salccode//写法2
```

#### 邮件内容

* 只支持内容的宏替换，且只能替换主表中信息
如:
```
{salccode},您好！
这是您的邮件
//生成的邮件内容会将{salccode}替换为具体的客户名称
```

#### 接收对象－接收客商(TAC)

* 用法和写法同“邮件主题TAC”
