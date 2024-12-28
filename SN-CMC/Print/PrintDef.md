## 打印格式

|日期|作者|备注|
|------|------|------|
|2018-09-25|张东|初稿|
|2019-10-12|张东|调整|

### 功能说明

打印格式功能用于将数据输出成pdf、excel、word等文档。

### 功能种类

#### XLS系统打印

```
系统打印格式为数据库方式，系统提供特殊的在线编辑界面（位于管控中心的实施配置菜单中《打印格式定义【xls】》）
可输出PDF及Excel
```

#### 文档模板打印

```
可利用指定语法编写的Excel、Word模板来输出Excel和Word文档
```


### 打印格式类型

```
7：XLS系统打印格式
11：Excel模板打印格式
13：Word模板打印格式
```

** 使用较多的为7：系统打印格式 **


### 打印原理

整个打印过程是在服务端（UI层）执行的。
打印格式的使用时需要界面定义来实现的，界面定义担任的角色是用来查询组织数据结构，一般为ArrayDataSet；通过加载界面定义组件并且执行对应的查询来构建数据集，供打印格式取数用。
打印格式和正常的界面定义一样使用的也是ArrayDataSet来做数据集，只是打印格式服务端使用的为自己专用的DataSet，为：FilterReadDataSet，里面包含了码名映射等界面配置。

### XLS系统打印格式
#### XLS打印格式指南
**1、示例展示**

<img src='help/SN-CMC/Print/img/XLSDefDemo.png' />

**2、XLS界面及参数介绍**

<img src='help/SN-CMC/Print/img/XLSDef01.png' />

* 面板中的参数：

```
选择要编写打印格式的系统点击查询出现编辑界面。
此界面的编辑方式类似于Excel。
标识参数 为该打印格式在对应系统中的唯一标识。
名称参数 为打印格式的名称。
宏参数 为选择打印格式中可替换的宏的配置文件。（后面会有单独介绍）
使用条件参数 为打印格式使用的生效条件。
```

* 面板中的选项：
1、字体 2、字号 3、居左 4、左右居中 5、居右 6、居上 7、上下居中 8、居右 9、黑体 10、斜体 11、单元合并/取消合并 12、单元格属性 13、标尺

<img src='help/SN-CMC/Print/img/XLSDef02.png' />

**打印格式的取数逻辑可以使用本系统的tac语言。**

**3、XLS表格属性定义**

配置菜单使用鼠标右键即可查看，或者使用界面工具面板上预设的按钮。

* 报表属性

<img src='help/SN-CMC/Print/img/XLSDef03.png' />

```
####常用参数
启动公式：可以定义一些全局的变量等
纸张大小：指定打印纸张大小，A0,A1,A2,A3,A4,也可以自定义大小
字体：指定字体如，宋体、楷体等等，字体大小
页边距：上下左右页边距
打印方式：横向打印，满页打印，满页时不打空行
背景图片：打印时可以加背景图片
```

* 行属性

<img src='help/SN-CMC/Print/img/XLSDef04.png' />

```
####常用参数
行启动公式：循环当前行时启动公式
（循环）取数公式：当前行所需要的循环公式，填写getDetailPrintDataSet("名称")，如子表循环填写getDetailPrintDataSet("界面定义上的子表")，主表的行启动公式默认为printRS
循环行数：当前取数对象需要覆盖Xls打印格式的行数（注意这里的行数不是实际的数据行数，是你编辑界面所画的xls模板的行数，左侧有红线标注）
满页调整行：满页时调整行高
只对首页满页调整：只对首页满页时调整行高
换页：自动换页
打印时隐藏：打印时行隐藏
页头区域：当前行是不是页头部区域
页底区域：当前行是不是页底区域
新加行禁止插入删除：新加行禁止删除当前行
自动行高：随着内容的多少行高自动
禁止删除：当前行不允许删除
禁止插入行：不允许插入行
明细数据行：数据行数显示
```

* 列属性

<img src='help/SN-CMC/Print/img/XLSDef05.png' />

```
####常用参数
行号列：自动设置行号
```

* 单元属性

<img src='help/SN-CMC/Print/img/XLSDef06.png' />

```
####常用参数
类型：当前单元的类型，缺省值、数值、文本、日期
最小小数位：当前单元最小小数位个数
最大小数位：当前单元最大小数位个数
公式：当前单元取值，$bcode取界面上面bcode对应字段值，如果辅助录入，取得是名，$code$bcode取得对应的码
右边框、下边框：定义边框的宽度和颜色
背景颜色，前景颜色：定义当前单元格的背景颜色，前景颜色
图片单元：当前单元格存放的是否是图片
（与上行）相同值合并：当前单元格所在列，相邻行值相同时单元格合并
行间距：多行打印时的行间距
```

* 图片

```properties
选定单元格，打开单元属性，勾选 图片单元 选项

1. 文件上传方式：
    点击图片单元列辅助录入上传图片。
2. 路径加载：
    单元属性公式中使用snsoft.print.util.ImageBytes.toImageBytes('imagepath')方法加载图片对象。
    imagepath：可以为文件系统路径（例：snsoft.print.util.ImageBytes.toImageBytes("fs/STADOCROOT/UploadFiles/19-07/D961647C3626398FE555418BE0BD712F.png")）；可以为classpath路径（例：snsoft.print.util.ImageBytes.toImageBytes("classpath:snsoft/print/image/upimg-icon.png")）。
    路径可以从表中查询：
    path=getDatabaseByTable("comuser").query1("select logopath from comuser where cuicode='C000000007'")
    path=path.substring(0,path.lastIndexOf("|"))
    img=snsoft.print.util.ImageBytes.toImageBytes(path)
```
    
* 示例参考

完整的XLS系统打印格式示例请参见帮助系统销售管理示例中的 <a href="uiinvoke/00/zh_CN/theme0/SN-HELP.Ord.SalorderList.html" target="_blank">打印格式</a> 示例。

**4、XLS文件配置**

使用系统打印格式定义界面画出来的模板是存在数据库里的，为了实现线上 0 数据库配置导入，xls也支持xml配置文件的方式。
界面上的《导出配置》按钮会将数据库中的配置导出成配置文件。

配置文件位置：
cfg/ui/print/[sysid]/7/[打印格式标识].xml

** 注意此功能需要resources/snsoft/ui/sourcepath/UIResourceConfig[sysid].json配置配合使用。 **

<img src='help/SN-CMC/Print/img/XLSDef07.png' />

配置示例：
注意XLS的配置文件里为JSON格式的数据，因此基本是很难编辑。

<img src='help/SN-CMC/Print/img/XLSDef08.png' />

1. 打印格式名称
1. 打印格式使用的宏定义配置
1. 打印格式模板

**界面上还有一个配置导入功能，是用来将xml配置导入到数据库使用。**

#### XLS打印格式手册

* 动态修改打印格式的文件名称
```properties
curPrintDocName  ="销售合同【"+$ordcode+"】"
null
```

* Xls打印格式修改Sheet页面名称
```properties
if instanceOf(createDoc,"snsoft.print.xls.print.Xls2MSExcel")
    createDoc.setSheetName($salordcode)
end if
```
 
* 内置页码宏

```properties
页码：页号
总页数：页数
例：页数+"/"+页号  效果：10/1
```

* 内置打印Tac常用方法

```properties
1. 获取年月日
    getDateYear(date)
    getDateMonth(date)
    getDateDay(date)
    getDateHours(date)
    getDateMinutes(date)
    getDateSeconds(date)
2. 内置数据集对象说明：
   对象名的规则是rs$$为固定前缀，0、1、2 . . .为结尾，后缀是按打印格式定义模板从上到下的循环来取得，比如说主表使用循环取数，主表一定是在子表之上，所以主表就是rs$$0，子表就是rs$$1，以此类推；反之，如果主表不使用循环方式取数，子表使用，那么子表就是rs$$0
3. 行隐藏
    setPrintHideRow(true)
4. 根据uiname获取数据集
    getDetailPrintDataSet(uiname)
5. 判断当前行隐藏
    isPrintHideRow()
6. 获取database方式
    db=getDatabaseByTable(表名)
```

* 主表公式

```properties
    1. 方式一：取数公式：在行属性中填写启动公式
        registerPrintReadDataSet(printRS).nextRow()
        null
    2. 方式二：循环公式：在行属性中填写（循环）取数公式
        printRS
```
* 子表公式

```properties
    循环公式：在行属性中填写（循环）取数公式
    getDetailPrintDataSet("界面名") 
    例：getDetailPrintDataSet("ft_35_cdd_gi")
```

* 单元取数公式

```properties
    单元属性定义中公式列填写：$列名
    宏填写方式：直接写宏名称
```

#### PDF打印字体相关
1. 示例：
    ```properties
    #分号分隔，分别为 常规;黑体;斜体;黑斜体
    #0：英文；1：中文；2：其他
    
    #字体嵌入pdf(非嵌入时浏览器可能显示异常)
    Font.embedded=true
    
    宋体#0.fonts=$/simsun.ttf;$/simsun.ttf;$/simsun.ttf;$/simsun.ttf
    宋体#1.fonts=$/simsun.ttf;$/simsun.ttf;$/simsun.ttf;$/simsun.ttf
    宋体#2.fonts=$/simsun.ttf;$/simsun.ttf;$/simsun.ttf;$/simsun.ttf
    
    仿宋#0.fonts=$/simfang.ttf;$/simfang.ttf;$/simfang.ttf;$/simfang.ttf
    仿宋#1.fonts=$/simfang.ttf;$/simfang.ttf;$/simfang.ttf;$/simfang.ttf
    仿宋#2.fonts=$/simfang.ttf;$/simfang.ttf;$/simfang.ttf;$/simfang.ttf
    
    黑体#0.fonts=$/simhei.ttf;$/simhei.ttf;$/simhei.ttf;$/simhei.ttf
    黑体#1.fonts=$/simhei.ttf;$/simhei.ttf;$/simhei.ttf;$/simhei.ttf
    黑体#2.fonts=$/simhei.ttf;$/simhei.ttf;$/simhei.ttf;$/simhei.ttf
    
    幼圆#0.fonts=$/simyou.ttf;$/simyou.ttf;$/simyou.ttf;$/simyou.ttf
    幼圆#1.fonts=$/simyou.ttf;$/simyou.ttf;$/simyou.ttf;$/simyou.ttf
    幼圆#2.fonts=$/simyou.ttf;$/simyou.ttf;$/simyou.ttf;$/simyou.ttf
    
    楷体#0.fonts=$/simkai.ttf;$/simkai.ttf;$/simkai.ttf;$/simkai.ttf
    楷体#1.fonts=$/simkai.ttf;$/simkai.ttf;$/simkai.ttf;$/simkai.ttf
    楷体#2.fonts=$/simkai.ttf;$/simkai.ttf;$/simkai.ttf;$/simkai.ttf
    
    隶书#0.fonts=$/simli.ttf;$/simli.ttf;$/simli.ttf;$/simli.ttf
    隶书#1.fonts=$/simli.ttf;$/simli.ttf;$/simli.ttf;$/simli.ttf
    隶书#2.fonts=$/simli.ttf;$/simli.ttf;$/simli.ttf;$/simli.ttf
    ```

1. 配置文件位置：
    放在与System.properties同级目录；
   
1. 字体文件[XLS打印格式(PDF)字体文件](https://n.esnsoft.cn/download/xlsfnts_zip)，下载后文件后缀改为.zip解压缩。 

1. 字体文件位置：
    在于System.properties同级目录新建fonts目录；
    或者`FontLibPath=绝对路径`指定；
   

### Excel模板打印格式

#### Excel打印格式指南

* 数据库维护(Excel)

这种方式有专门的维护界面如下：

<img src='help/SN-CMC/Print/img/DOCDef01.png' />

选择系统机打印类别点击 查询 填写对应字段并上传打印格式模板文档即可。

* 配置文件维护(Excel)

这种方式归配置系统管理，配置目录为：

<img src='help/SN-CMC/Print/img/DOCDef02.png' />

模板配置为指定模板的配置，内容类似于XLS配置文件，但含义不同。
* 示例：

```
<?xml version="1.0" encoding="UTF-8"?>
<PrintDef>
	<Defname>销售合同【Excel】</Defname>
	<Aiddefid>erp.order</Aiddefid>
	<Defcontent>
	<![CDATA[
	file:销售合同.xls //file:指使用模板文件，值表示模板文件的位置。
	]]>
	</Defcontent>
</PrintDef>
```

* 使用方式(Excel)

<img src='help/SN-CMC/Print/img/Sysdefb1.png' />
```
区域1：主表单纯取数方式（一般应对主表只有一条记录时）：
     :=
     registerPrintReadDataSet(printRS).nextRow()
     null
     主表循环方式（一般应对主表也为多条记录，此时取数区域必须为表格）：
     :=loopControl(printRS,10,"r1")
     循环主表数据，第一个参数循环数据，第二个参数循环行数，第三个参数为数据集内部id，不重要，可以任意（不重复）
区域2：
     :=
     g=getDetailPrintDataSet("salorderg")
     loopControl(g,1,"r2")
	 循环子表数据
区域3：输出文档名称修改
     :=
     curPrintDocName  ="销售合同-"+$salordcode
     null
区域4：
     :=
     rs$$1.getSumValue("fcy")
	 对fcy列求和
区域5：输出图片
    :=snsoft.print.util.ImageBytes.createImage("classpath:snsoft/print/image/upimg-icon.png")
```

#### Excel打印格式手册

* 主表公式

```properties
    1. 方式一：取数公式：
        registerPrintReadDataSet(printRS).nextRow()
        null
    2. 方式二：循环公式：
        loopControl(printRS,10,"r1")
```
* 子表公式

```properties
    循环公式：
    gds=getDetailPrintDataSet("界面名")
    loopControl(gds,3,"r2")
    例：
    gds=getDetailPrintDataSet("ft_35_cdd_gi")
    loopControl(gds,3,"r2")
```

* 单元取数公式
```properties
    对应列中填写：:=$列名
    宏填写方式：:=宏名称
```

* 宏定义如下图：

<img src='help/SN-CMC/Print/img/SysdefbMacro1.png' />


### Word模板打印格式
#### Word打印格式指南

* 数据库维护(Word)

这种方式有专门的维护界面如下：

<img src='help/SN-CMC/Print/img/DOCDef01.png' />

选择系统机打印类别点击 查询 填写对应字段并上传打印格式模板文档即可。

* 配置文件维护(Word)

这种方式归配置系统管理，配置目录为：

<img src='help/SN-CMC/Print/img/DOCDef03.png' />

模板配置为指定模板的配置，内容类似于xls配置文件，但含义不同。
示例：

```
<?xml version="1.0" encoding="UTF-8"?>
<PrintDef>
	<Defname>销售合同【docx】</Defname>
	<Aiddefid>erp.order</Aiddefid>
	<Defcontent>
	<![CDATA[
	file:销售合同.docx //file:指使用模板文件，值表示模板文件的位置。
	]]>
	</Defcontent>
</PrintDef>
```

* 使用方式(Word)

<img src='help/SN-CMC/Print/img/Sysdefb2.png' />
```
区域1 ${Wordmain}
	一般为主表数据，启动公式宏，对应替换值为（createDoc为doc数据打印对象）：
	主表单纯取数方式（一般应对主表只有一条记录时）：
	rs = registerPrintReadDataSet(printRS) //printRS：主表数据集对象；
    rs.nextRow()
    null//表示此公式不输出结果
    主表循环方式（一般应对主表也为多条记录，此时取数区域必须为表格）：
	createDoc.loopTable(printRS,0,8) //printRS：主表数据集对象；0：从模板第0行开始循环覆盖；8：循环覆盖模板8行。
    null //表示此公式不输出结果
区域2 ${标题} 取数宏 对应替换为：
    curPrintDocName  ="销售合同【"+$ordcode+"】"
区域3 ${Worddetail}
    子表数据，启动公式宏，对应替换为：（外层主表使用循环取数方式时必须在主表循环行数覆盖范围内，此时取数区域必须为表格）
    gRS=getDetailPrintDataSet("orderg")  //获取子表数据集对象
    createDoc.loopTable(gRS,1,1) //启动数据循环
区域4 ${行号} 取数宏 对应替换为：
    $sortidx
区域5 ${rs$$1.getSumValue(rs$$1.columnAt("fcy"))} 取合计值：
    rs$$[index]按角标顺序模板从上依次向下（主子孙等嵌套顺序）构建过的数据集取数对象，即rs$$1==gRS，一般多使用子表，可以在子表循环覆盖范围之外使用。
区域6 ${PNG} 取数宏 对应替换为：
    此区域为图片：new snsoft.print.util.ImageBytes("classpath:snsoft/ui/print/pic/logo.png")
    读取一张图片将其转化为imagebytes对象即可输出图片。
```

#### Word打印格式手册

* 主表公式

```properties
    1. 方式一：取数公式：
        registerPrintReadDataSet(printRS).nextRow()
        null
    2. 方式二：循环公式：
        createDoc.loopTable(printRS,0,10)
```

* 子表公式

```properties
    循环公式：
    gds=getDetailPrintDataSet("界面名")
    createDoc.loopTable(gds,1,1)
    例：
    gds=getDetailPrintDataSet("ft_35_cdd_gi")
    createDoc.loopTable(gds,1,1)
```

* 单元取数公式

```properties
    对应列中填写：${$列名}
    宏填写方式：${宏名称}
```

* 宏定义如下图：

<img src='help/SN-CMC/Print/img/SysdefbMacro1.png' />


### 打印格式宏定义

宏定义是为了简洁的替换模板上的取数公式，时模板阅读更加清晰。
宏定义支持数据库配置，有维护界面；还支持文件配置，配置文件类型为xml。

#### 宏定义指南
##### 数据库

维护界面：

<img src='help/SN-CMC/Print/img/PrintDefMacro.png' />

打印格式宏定义参数：

```
标识：宏定义唯一标识；
名称：宏定义名称；
宏名称：使用的宏的名称；
宏内容：使用的宏的真正替换内容。
```
fileresmonitor
界面上有两个按钮分别是《文件转库》用来将配置文件形式的宏定义存入数据库；《库转文件》用来将数据库中的配置生成为配置文件。

##### 配置文件

推荐开发直接使用配置文件。

* XSD：<a href="do/BrowseSchema?schema=PrintMacro.xsd" target="_blank">PrintMacro.xsd</a>

示例：
```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<PrintMacro xmlns="http://www.snsoft.com.cn/schema/PrintMacro">
    <!--打印格式宏名称-->
	<Defname>销售合同</Defname>
	<!--打印格式宏定义-->
	<details>
		<key>启动公式</key>
		<children>
			<key>Word</key>
			<children>
	            <!--key 这里写宏的名称--->
				<key>Wordmain</key>
				<!--value 这里写宏的替换值-->
				<value>
				<![cdata[
				...
				]]>
				</value>
			</children>
		</children>
	</details>
	<details>
		<children>
			<key>合同号</key>
			<value>$salordcode</value>
		</children>
		<children>
			<key>sumqtc</key>
			<value><![CDATA[
			...
			]]>
			</value>
		</children>
	</details>
</PrintMacro>
```

* 宏定义配置文件存放位置

<img src='help/SN-CMC/Print/img/PrintDefMacroConfig.png' />

**配置文件的名字和打印格式定义标识相同。**

#### 宏定义手册

### 打印格式使用

打印格式客户端可以使用默认的工具条按钮；
也可以使用自定义按钮，自定义界面配置按钮样式：xprops.iconClassName='icons-btn-print'

#### 使用指南

##### 标准按钮

使用工具栏上的打印按钮如下：
<img src='help/SN-CMC/Print/img/PrintDefBtn.png' />

或者界面自定义按钮[tablename]_btn_print

以下配置配置在界面定义主表grid/record表标签里。
printfmt="help.printtest%"
msexcelfmt="help.printtest"
mswordfmt="help.printtest"

```xml
<m:RecordTable title="销售合同" name="order" sqlexpr="help_erp_order" title_en="order" title_aa="aaa"
    mainui="param"
    ...
    printfmt="erp.order%"
    msexcelfmt="erp.order"
    mswordfmt="erp.order">
...
</m:RecordTable>
```

属性说明：
```
printfmt：打印格式定义标识。
msexcelfmt：msexcel格式打印格式定义标识。
mswordfmt：msword格式打印格式定义标识。
这些配置在使用默认按钮的时候起作用，在自定义监听中，需要给prtname_[i]参数设置打印格式
```

默认服务调用规则：
客户端请求：runmethod = "ui~"+rootuiid+"."+table.name+".!print"；
服务端调用入口：UICompPrintService.doPrint()；

##### 自定义按钮

* 使用示例

代码示例：

```
##XJS：
void oncmd_printxls2(Component comp)
{
	JSObject params = createParams();
	params.$set("uiid", "91.print.XlsPrintTest");
	ProgressParam progressParam = new ProgressParam();
    progressParam.size = new js.Size(700, 300);
    progressParam.title = "打印格式测试";
    progressParam.options = 1 + 2;// 自动开始+自动关闭
    progressParam.onRunCall = new js.FunctionCall(this.$getAsFunction("onRunReturn"), this);
    progressParam.runMethod = "printxls2";
    comp.uiInvoke(progressParam, params);
}
void onRunReturn(String url)
{
	if (url != null)
	{
		window.open(Xjs.ROOTPATH + url);
	}
}
JSObject createParams()
{
	String picode = (String) this.dataSet.getValue("picode");
	JSObject params = new JSObject();
	//构造参数
	params.$set("options##0x200000", true);//生成压缩文件
	params.$set("prtname_0", "91##7##help.printtest");//打印格式
	params.$set("prtname_1", "91##7##help.printtest1");//打印格式
	params.$set("excelfmt_0", true);//第一个生成Excel
	params.$set("excelfmt_1", false);//第二个不生成Excel
	params.$set("MaxNPrtNms", 2);
	//
	JSObject queryP = new JSObject();
	queryP.$set("picode", picode);
	params.$set("queryP", queryP);//打印数据查询参数
	return params;
}

##ui：
public String printxls2(UIEvent event, Map<String,Object> parameter)
{
	UICompPrintService printService = new UICompPrintService(event.uiComponent, parameter);//此处的uicompent可以使用自定义打印界面，默认传入的是当前按钮主界面。
	Map<String,Object> result = printService.doPrint();
	return (String) result.get("url");
}
```

** 详情见帮助中心打印格式自定义按钮示例：基础实例 -> 销售合同管理。**

#### 使用手册

* 参数说明

```
######使用较多：
sysprt2pdf：是否为系统打印（pdf），boolean值，一般为true。
prtname_[i]：打印指令（printDefname）,用于解析打印类型及界面：SN-HELP#7#help.printtest（系统号sysid##打印类型deftype##打印格式标识defid）
excelfmt_[i]：第i个打印格式为excel类型？boolean值。
queryP={}：数据查询参数
MaxNPrtNms：打印个数
options：
	options##0x200000 : 生成 压缩包：
	options##0x100000 : 套打
	options##0x00400000 : 合并excel文件（多个excel格式，合并到一个excel文件的 sheet页中）
mprtdocname：合并Excel时生成的的文件名称。
```

界面按钮xjs+ui监听触发打印格式组件UICompPrintService.doPrint()方式；
ProgressPane类型界面定义，通过进度调方式执行默认!print调用，例：runMethod = "ui~"+rootUiid+"."+table.name+".!print";

### 打印在线预览

#### 打印在线预览指南

打印出的Office文档在线预览目前需要Office Online Server服务配合使用，并且Office服务器需要暴露在公网。

服务端（必须配置）：

```xml
系统选项 OfficeOnline.ServerURL=http://[host]
```


客户端：

```xml
#默认按钮：
系统选项配置 OfficeOnline.ViewAttach=true

#自定义按钮方式：
打印配置增加isprintviewmod=true
config.$set("isprintviewmod", true);
```

#### 打印在线预览手册


### 右键配置导出

1. 支持右键数据导出，将当前配置界面的有效数据（启用）导出成一个文件（XML/南北标准配置文件），实施人员在数据库中配置标准数据，通过本功能将这些标准配置数据导出成文件用来打包发布。

1. 配置存放目录：
    打印格式定义【XLS】：snsoft/archive/init/print/xls/xxx.zip
    打印格式定义【模板】：snsoft/archive/init/print/doc/xxx.zip
    打印宏定义：snsoft/archive/init/print/macro/xxx.zip

### 右键初始化

配置表数据为空时才可以初始化数据，数据来源是右键导出的配置。

### 打印格式类图

@startuml
Title 打印格式类图

interface ContentOutput

class XlsDocument
class Xls2PDF
class TemplatePOIExcelOutput
class TemplateMSDocxOutput

ContentOutput <|-- XlsDocument
XlsDocument <|-- Xls2PDF
ContentOutput <|-- TemplatePOIExcelOutput
ContentOutput <|-- TemplateMSDocxOutput

interface ContentOutput{
	void output(OutputStream out);
}

class XlsDocument{
    XlsPage[] print(XlsData xlsData, PrintScriptExecutor tac);
    boolean executeFormula(XlsData xlsData, PrintScriptExecutor tac, int flags);
}

class Xls2PDF{
    Xls2PDF(XlsData xlsDataA[],PrintScriptExecutor tac);
    output(OutputStream out);
    XlsPage[] print(XlsData xlsData, PrintScriptExecutor tac)
    boolean executeFormula(XlsData xlsData, PrintScriptExecutor tac, int flags);
}

class TemplatePOIExcelOutput{
    void setScript(ScriptExecutor script);
    output(OutputStream out);
}

class TemplateMSDocxOutput{
    void setScript(ScriptExecutor script);
    output(OutputStream out);
}

@enduml


### 打印格式时序图

@startuml
Title 标准方式时序图

actor 客户端

participant tablePrint        as "Xjs.table.TablePrint"
participant progresspane      as "ProgressPane"
participant compsave          as "UICompDataSaveService"
participant compbasservice    as "UICompBasService"
participant compprintservice  as "UICompPrintService"
participant contentoutput     as "ContentOutput"
participant xxxservice        as "xService"

客户端 -> tablePrint : 点击标准打印按钮
note right of 客户端: 调用TablePrint的doPrint方法
activate tablePrint
	tablePrint -> compsave : [save] \n客户端存盘
	activate compsave
		compsave -> xxxservice :  调用service组件存盘
	    xxxservice -> compsave :  [return saveresult]
	    note right of compsave: 返回值
	    compsave -> tablePrint : [return saveresult]
	    note right of tablePrint: 返回值返回到客户端
	deactivate compsave
	tablePrint -> compbasservice : [!loadprtfmtnms]
	note right of tablePrint: UICompBasService.loadPrtfmtNames加载打印格式
	compbasservice -> tablePrint : 返回打印格式
    tablePrint -> 客户端 : 弹出对话框(ProgressPane)
deactivate tablePrint

客户端 -> progresspane : 勾选要打印的打印格式点击运行
activate progresspane
    progresspane -> compprintservice : 调用打印服务
    activate compprintservice
        compprintservice -> contentoutput : [output]
        note right of compprintservice: 根据参数调用正确的ContentOutput
    compprintservice -> progresspane : 返回打印格式文档下载地址
    deactivate compprintservice
    progresspane -> 客户端 : 构建下载链接
deactivate progresspane
@enduml

@startuml
Title 自定义方式时序图

actor 客户端

participant xjslistener        as "Xjs监听"
participant uilistener         as "UI监听"
participant progresspane       as "ProgressPane"
participant compsave           as "UICompDataSaveService"
participant compprintservice   as "UICompPrintService"
participant contentoutput      as "ContentOutput"
participant xxxservice         as "xService"

客户端 -> xjslistener : 点击自定义打印按钮
note right of 客户端: 调用自定义的客户端按钮
activate xjslistener
    xjslistener -> compsave : [save] \n客户端存盘
	activate compsave
		compsave -> xxxservice :  调用service组件存盘
	    xxxservice -> compsave :  [return saveresult]
	    note right of compsave: 返回值
	    compsave -> xjslistener : [return saveresult]
	    note right of xjslistener: 返回值返回到客户端
	deactivate compsave
	xjslistener -> uilistener : [xxprint]
	note right of xjslistener: 调用ui监听的打印方法
    activate uilistener
    uilistener -> compprintservice : 调用打印服务
    activate compprintservice
        compprintservice -> contentoutput : [output]
        note right of compprintservice: 根据参数调用正确的ContentOutput
    compprintservice -> uilistener : 返回打印格式文档下载地址
    deactivate compprintservice
    uilistener -> xjslistener : 返回打印格式文档下载地址
    deactivate uilistener
    xjslistener -> 客户端 : 构建下载链接
deactivate xjslistener
@enduml

### 内置TAC


1. 根据条码/二维码配置编码和条码/二维码宏 生成一个二维码文件

要求在[条码样式配置](uiinvoke/00/zh_CN/theme0/90.BarCode.Style.html)界面可以根据参数bccode(配置编码)能找到对应已经启用的配置
barMap 参数 一般为条码内容的宏和描述信息的宏为key的键值对
```java
    snsoft.print.util.ImageBytes newBarCode(String bccode, Map<String,Object> barMap)
```

### 预定义TAC

#### 预定义TAC指南

1. 用于在系统中提供一个灵活的脚本定义并可以被其他程序调用或单独使用；
1. [预定义Tac](uiinvoke/00/zh_CN/theme0/90.prt.DefTac.html)
	* 需要[SN-CMC.PrintDef[R/C]]权限；
1. [执行预定义Tac](uiinvoke/00/zh_CN/theme0/90.prt.ExeTac.html)
	* 需要[SN-CMC.PrintDef[C]]权限；
	* 在Tac脚本定义中使用的变量，可以通过**全局变量**参数传递；
1. 程序中调用：
```java
// sysid 系统号
// defid 标识
// vars Tac需要的全局变量
snsoft.tac.TacUtils.executeDefTac(sysid,defid,vars)
```

#### 预定义TAC手册
