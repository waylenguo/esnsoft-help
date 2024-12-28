## 实体类VO

|日期|作者|备注|
|------|------|------|
|2018-09-28|冀小雷|初稿|

### 综述

1. 实体类映射：基于SOA的架构，涉及到大量的格式化及非格式化数据的转换，为了提高开发效率系统开发了实体类映射的功能 MapperService，默认执行同名拷贝，也可以通过XML配置文件实现复杂的映射关系。
1. 实体类校验：为了解决相同的校验多处编写逻辑的历史问题，启用了Validation框架，将标准的数据校验通过注解的方式设置到对应的实体类上，通过底层框架将数据校验注解解析为当前场景中的校验规则，达到一次配置多处使用的目的。
1. 实体类数据权限：数据权限分为数据读权限和数据写权限，技术底层通过注解进行封装，在UI层进行权限处理：
	1. 读权限：将权限过滤条件设置到查询参数上；
	1. 写权限：根据权限配置判断是否有新增、删除、修改等权限；同时提供工具方法用于方法的判断或获取权限。

### snsoft.dx.VO
在与数据库进行数据存取操作时，我们要求使用VO对象方式编程。通过在VO对象中标注的一系列注解，平台底层可以统一管理VO对象的事务和VO对象的数据合法性校验。在将VO与数据库表进行映射时，要求使用JPA注解实现。在系统的表结构定义中，我们提供了生成VO功能，可以方便开发人员根据表结构快速生成VO实体类。

使用界面绑定服务进行存盘的VO必须继承此抽象类：
1. 通过属性{storedColumns}设置修改的列；
2. 通过属性{saveMode}设置存盘模式；

```
 * 用于提供逻辑标识功能：
 *     1、提供VO修改的字段；
 *     2、提供判断当前VO的操作类型；
 * 注意：相关属性及方法不参与序列化为Json及Xml，参见测试类{snsoft.dx.vo.VOTest}。
 * （1）屏蔽Spring持久化框架，在属性上添加注解{org.springframework.data.annotation.Transient}；
 * （2）屏蔽Json序列化，在属性上添加注解{com.fasterxml.jackson.annotation.JsonIgnore}；
 * （3）屏蔽Xml序列化，在get方法上添加注解{javax.xml.bind.annotation.XmlTransient}；
public abstract class VO implements java.io.Serializable
```
### 注解相关

#### JPA标准注解

JPA是Java Persistence API的简称，中文名Java持久层API，是描述对象－关系表的映射关系，并将实体对象持久化到数据库中。
底层持久化使用JPA标准注解，主要有如下注解：

```java
/*
 * @Table：表
 * 说明：类注解。标识此VO对应物理表。
 * 注解参数：name用于设置表名。
 */
@Table(name="users")

/*
 * @Id：主键
 * 说明：属性注解，标识此列为当前表主键列。
 */
/*
 * @Column：列
 * 说明：属性注解，使用该注解的属性对应数据库字段，持久化到数据库中，不使用该注解的属性不对应数据库字段，不持久化到数据库中。
 * 注解参数：name用于设置字段别名，设置了别名，数据库字段名称以别名为准。
 */
@Id
@Column
private String usercode;

/*
 * 下列注解在主表VO对象上使用，用于表示主子表对应关系。
 * @OneToOne：子对象注解
 * 说明：用于标识关联对象为一对一关系，往往与@JoinColumn或@JoinColumns注解成对出现。
 * @OneToMany：子对象注解
 * 说明：用于标识关联对象为一对多关系，往往与@JoinColumn或@JoinColumns注解成对出现。
 * @JoinColumn：子对象注解
 * 说明：用于设置单字段关联关系。
 * 注解参数：name用于设置子对象关联字段名称，referencedColumnName用于设置主对象关联字段名称。
 *          referencedColumnName可不设置，此时默认referencedColumnName=name
 * @JoinColumns：子对象注解
 * 说明：用于设置多字段关联关系。
 * 注解参数：@JoinColumn数组对象用于设置多个字段关联关系。
 */

@OneToOne
@JoinColumn(name="usercodde")
private Userdata data;

@OneToMany
@JoinColumn(name="usercode")
private List<Usersa> as;

@OneToOne
@JoinColumns({@JoinColumn(name="cuicode"),@JoinColumn(name="bcode")})
private Busibcode busibcode;
```

**注：如下的其他未使用的标准注解暂不支持。**
```
@Entity：设置为实体类
@OrderBy(name = "group_name ASC, name DESC") :排序
@Column 中的多个设置支持，如长度、非空等
@Temporal(TemporalType.DATE)//设置为时间类型 
```

##### @Table
用于标注VO对象同数据库表的映射，name属性可选填写。当VO名与数据库表名不一致时必须指定，否则使用小写VO类名作为表名。
```java
@Table(name = "users")
public class User extends BcodeVO
{...}
```
##### @Column
用于标注VO对象的属性同数据库表列的映射关系，name属性可选填写。当属性名与列名不一致时必须指定，否则使用小写属性名作为列名。
```java
@Column
private int wadmin;
@Column
private Date bedate;
```
##### @Id
标识列为表的主键。
```java
@Table(name = "users")
public class User extends BcodeVO
{
@Id
@Column
private String usercode;
...
}
```
##### @OneToOne
##### @OneToMany
##### @JoinColumn
用于标识主子表关系的注解。
- OneToOne：一对一关系（扩展表）
- OneToMany：一对多关系（子表）
- JoinColumn：关连列（拷贝主表字段）

JoinColumn有若干属性需要关注：name 属性标识为目标表（子表）关系字段，referencedColumnName属性标识当前表（主表）关系字段。

**注：存盘使用 `snsoft.dx.DefaultDAO<V>`**

- 主对象（单字段关联）
```
// 详细使用请参见对应的单元测试类：snsoftx.test.adk.access.JoinColumnsTest
```
```
@Table(name = "main")	//指定表名为main，使用全小写    
public class Main implements java.io.Serializable
{
	@Id	//设置mid为主键
	@Column
	private String			mid;
	@Column
	private Date			odate;
	@Column
	private BigDecimal		fcy;
	@Column(name = "info")	
	private String			remark;
	@OneToOne
	@JoinColumn(name = "subid", referencedColumnName = "mid")
	private SubOne			SubOne;
	@OneToMany
	@JoinColumn(name = "mid")
	private List<SubMany>	subManys;
}
```

- 子对象（单字段关联）

```
@Table(name = "subone")
public class SubOne implements java.io.Serializable
{
	@Id
	@Column
	private String			subid;
	@Column
	private BigDecimal		fcy;
}

@Table(name = "submany")
public class SubMany implements java.io.Serializable
{
	@Id
	@Column(name = "id")    //设置subid对应的字段为id
	private String			subid;
	@Column
	private String			mid;	
	@Column
	private BigDecimal		fcy;
}
``` 
- 主对象（多字段关联）
```
@Table(name = "main")	//指定表名为main，使用全小写    
public class Main implements java.io.Serializable
{
	@Id	//设置mid为主键
	@Column(name="id")
	private String			mid;
	@Id	//设置smid为主键
	@Column
	private String			smid;
	@Column
	private Date			odate;
	@Column
	private BigDecimal		fcy;
	@Column(name = "info")	
	private String			remark;
	@OneToOne
	@JoinColumns({@JoinColumn(name = "subid", referencedColumnName = "id"), @JoinColumn(name = "subsid", referencedColumnName = "smid")})
	private SubOne			SubOne;
	@OneToMany
	@JoinColumns({@JoinColumn(name = "id"), @JoinColumn(name = "smid")})
	private List<SubMany>	subManys;
}
```

- 子对象（多单字段关联）

```
// 详细使用请参见对应的单元测试类：snsoftx.test.adk.access.JoinColumnsTest
```
```
@Table(name = "subone")
public class SubOne implements java.io.Serializable
{
	@Id
	@Column
	private String			subid;
	@Id
	@Column
	private String			subsid;
	@Column
	private BigDecimal		fcy;
}

@Table(name = "submany")
public class SubMany implements java.io.Serializable
{
	@Id
	@Column    //设置subid对应的字段为id
	private String			subid;
	@Column(name = "id")
	private String			mid;	
	@Column
	private String			smid;	
	@Column
	private BigDecimal		fcy;
}
``` 

#### Validation

参见[有效性校验](DataValid.md)。

#### 列配置注解
常用的注解校验与客户端界面定义有对应属性对应，如下：
1、数值
```
a. 最大小数位：
	i. 南北：maxdeci，超出四舍五入（客户端）；
	ii. 注解：@VOField
b. 最小小数位：
	i. 南北：mindeci，不足补零（客户端）；
c. 范围：
	i. 南北：xprops.valueRange（客户端）；
	ii. 注解：
		1. @Size(min='',max='')
		2. @DecimalMin 及 @DecimalMax
		3. @Max + @Min
```
2、文本
```
a. 限制长度
	i. 南北：uiprops.maxCharsCnt，超出后不可录入（客户端）；
	ii. 注解：@Length(max='')；
b. 格式
	i. 南北：xprops.checkRegexp（正则）（客户端），uiprops.validPrompt（提示）（客户端）；
	ii. 注解：@Pattern(regexp='',message='')；
```
3、默认值
* 详细参见[默认值帮助文档](help.html?helpFile=help/SN-CMC/Service/VO/VOListener.md#默认值)
* 配置列默认值可用注解@DefaultValue("")，例如：@DefaultValue("10")，用的比较多的时候是用词注解来配置编码规则。

4、码表校验
```
当某列的值为码表值得时候，为了校验此列的码是否正确，可以使用@CodeTable("")注解来校验。

/**客户*/
@Column
@CodeTable(value = "erp.ccode", message = "{SYSID#tblname.ccode#codetable}")
@Length(max = 10)
private String				ccode;

value为码表ID，message为提示信息。
此时就会将ccode的值拿去码表erp.ccode中校验查看有没有，有则通过，没有则提示message中的信息。
```

5、非空
```
a. 非空强制录入：
	i. 南北：noblank="true"；
	ii. 注解：@NotNull(message="{SYSID#tblname.colname#notnull}")
b. 非空提交检查
	i. 南北：noblankOnSubmit="true"；
	ii. 注解：@NotNull(message="{SYSID#tblname.colname#notnull}", groups = SubmitGroup.class)
c. 存盘存在子表记录
	i. 注解：@Size(min=1)
d. 提交存在子表记录
	i. 注解：@NotEmpty(message="${SYSID#fieldname#notempty}", groups = SubmitGroup.class)
```

**以上的注解可以通过UI监听：snsoft.bas.sheet.VOListener将注解转换为列配置，不用再额外配置这些属性。**
**参考VO示例：snsoft.help.sheet.code.vo.ERPCcode；**
**参考界面示例：<a target="_blank" href="uiinvoke/00/zh_CN/theme0/91.Erp.Ccode.html">客户维护示例</a>；**
**完整示例见帮助系统 功能示例模块 的 进销存 案例。**

#### 屏蔽类注解
当我们使用VO对象同Spring-data、Jackson、Xml转换时，有些字段我们并不需要转换到相应数据结构中。此时就需要使用一些屏蔽类的注解来实现该功能。
- 屏蔽spring-data的注解：spring.Transient，设置在字段上
- 屏蔽Jackson的注解：JsonIgnore，设置在字段上及get方法上
- 屏蔽XML的注解：XmlTransient，设置在get方法上，同时不能出现在类注解XmlType的propOrder属性中

### 报文转VO
#### XML
VO与XML之间的转换使用JAXB规范，常用注解如下：
- XmlTransient：不参与转换；
- XmlRootElement：指定XML根节点；
- XmlAccessorType：指定转换访问方式；
- XmlElement：用于指定元素名称；
- XmlJavaTypeAdapter：用于指定读写转换类；

**注：使用Bean{jaxb2Marshaller:org.springframework.oxm.jaxb.Jaxb2Marshaller}**

```
@XmlAccessorType(XmlAccessType.FIELD)
@XmlType(name = "AccessConfig")
@XmlRootElement(name = "Config")
public class AccessConfig
{
	private String		id;

	@XmlElement(name = "Name", required = true)
	@XmlJavaTypeAdapter(value = TrimStringAdapter.class)
	protected String	name;

	@XmlElement(name = "ReqClass", required = true)
	@XmlJavaTypeAdapter(value = TrimStringAdapter.class)
	protected String	reqclass;

	@XmlElement(name = "Validate")
	protected boolean	validate;

	@XmlElement(name = "SavBean", required = true)
	@XmlJavaTypeAdapter(value = TrimStringAdapter.class)
	protected String	savbean;

	@XmlElement(name = "MaxThrdCount")
	protected int		maxthrdcount;

	@XmlElement(name = "Remark")
	@XmlJavaTypeAdapter(value = TrimStringAdapter.class)
	protected String	remark;
}
```
#### JSON
VO与JSON之间的转换使用Jackson框架，常用注解如下：
- JsonIgnore：不参与转换；
- JsonPropertyOrder：用于指定转换JSON的顺序；
- JsonProperty：用于指定JSON属性名称；
- JsonDeserialize：指定反序列化类；
- JsonSerialize：指定序列化类；

**注：使用Bean{objectMapper:com.fasterxml.jackson.databind.ObjectMapper}**

```
@XmlAccessorType(XmlAccessType.FIELD)
@XmlRootElement(name = "Person")
@JsonPropertyOrder(alphabetic = true)
public class Person extends VO
{
	/**
	 * 名字
	 */
	@JsonProperty(value = "Name")
	@XmlElement(name = "Name")
	private String	name;
	/**
	 * 年龄
	 */
	@JsonProperty(value = "Age")
	@XmlElement(name = "Age")
	private int		age;
	@JsonProperty(value = "Birth")
	@XmlElement(name = "Birth")
	private Date	birth;
}
```
```
// 详细使用请参见对应的单元测试类：snsoftx.test.adk.voconvert.mapper.VOMapperTest
```

#### VO映射
VO映射也叫VO拷贝，在实际业务中，两个VO实体进行上下游数据拷贝是频繁出现的业务场景。底层框架支持同名拷贝，个性化的对应关系可以通过`vomapper.xml` XML文件进行配置，该功能没有引入三方，
自行实现，参见{snsoft.dx.vo.convert.service.MapperService}

**注：使用Bean{SN-CORE.MapperService:snsoft.dx.vo.convert.service.MapperService}**

说明：
```
简单对象间可以直接使用MapperService进行字段值拷贝（只会同名拷贝）。参见测试类：snsoftx.test.adk.voconvert.mapper.SameNameCopyTest.testSameNameCopy()。
复杂对象的拷贝需要配置文件，配置文件给出VO字段的映射关系，同时支持默认值、码表码名转换、时间宏、同名拷贝（字段不配置默认同名拷贝）、同名不拷贝、不同名拷贝等功能。配置文件可参见vomapper_signatureE.xml和vomapper_signatureR.xml，前面提到的所有功能的配置例子。
上面的配置文件最终会转换为ClassMapper对象来存储映射关系，配置稳健的文件名没有要求，见名知意即可，MapperService在映射的时候是通过两个VO的Class全名来定位配置的。
另外，如果VO结构并不是特别复杂，不想写配置稳健的话可以自己生成ClassMapper来进行转换，见测试用例：snsoftx.test.adk.voconvert.mapper.NotSameNameCopyTest等同包下的测试用例。
// 详细使用请参见对应的单元测试类：snsoftx.test.adk.voconvert.mapper.VOMapperTest
// snsoftx.test.adk.voconvert.mapper包下的测试用例为单个功能的测试。
```

##### 映射定义
平台中会扫描如下目录中的xml文件，将对应配置加载到缓存中进行VO对象拷贝操作。
```
snsoft/res/vomapper/vomapper*.xml
```

##### 映射标签
- `<mapper>`：定义拷贝映射的实体class
- `<m>`：定义实体中字段映射关系
以上拷贝映射在定义时支持嵌套。

关于vomapper.xml的定义方式可参见列子：`vomapper_template.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- 类名的全名与简写名称同等使用 -->
<mapper xmlns="http://www.snsoft.com.cn"
fromClass="a.vo.AClass" toClass="a.appvo.BClass">
<!-- 同名不拷贝：只定义fm -->
<m fm="field0" />

<!-- 同名拷贝（默认，可以不配置） -->
<m fm="field" to="field" />

<!-- 非同名拷贝 -->
<m fm="field1" to="field2" />

<!-- 码表：转换为名称 -->
<m fm="field3" to="field3" codeDefId="#xxx" />

<!-- 码表：转换为名称，指定分隔符分隔多值 -->
<m fm="field4" to="field4" codeDefId="#xxx" codeDeli="," />

<!-- 默认值：常量 -->
<m to="field5" dftValue="1" />

<!-- 日期、时间类默认值：参见SimpleDateFormat 将宏的返回值转换为字段的值。 -->
<m to="field6" dftMacro="YYYY-MM-DD" />

<!-- VO字段对应关系 -->
<m fm="vfield1" to="vfield2">
<mapper>
<m fm="xx" to="yy" />
</mapper>
</m>

<!-- 集合字段对应关系 -->
<m fm="cfield1" to="cfield2">
<mapper fromClass="a.vo.A1Class" toClass="a.appvo.B1Class">
<!-- 子表拷贝主表字段 -->
<m fm="AClass.field1" to="field2" />
</mapper>
</m>
</mapper>
```
##### Mapper实现
在程序中，我们可以使用MapperService来实现VO对象拷贝：

```java
/**
*@param F 拷贝源VO
*@param T 目标VO
*/
snsoft.dx.vo.convert.service.MapperService.mapper(F, T)
```

### VO校验
VO校验采用Java标准Validation方案，在需要校验的Field上打上javax.validation.constraints 中注解。
例如：
```java
public class User extends BcodeVO
{
@NotNull
private String usercode;
...
}
```
注解校验的实现方法可参见：
```java
	#Service
	snsoft.dx.vo.validate.service.ValidateService
	#以及其实现类
	snsoft.dx.vo.validate.service.impl.ValidateServiceImpl
	#测试类
	snsoftx.test.adk.validation.ValidationTest
```
### VO转报文

#### VO与XML转换
在定义XML时，我们要求先编写xsd文件，后根据xsd文件生成其对应的pojo类，注意不要手动新建与xml对应的实体类。手动新建极容易出错，且不易排查。
使用JAXB进行转换，示例参考帮助中心。如下是简单的说明：
```java
//解析xml的类
org.springframework.oxm.jaxb.Jaxb2Marshaller
//解绑
Jaxb2Marshaller marshaller = new Jaxb2Marshaller();
marshaller.setMappedClass(SignatureType.class);
try (InputStream in = new ClassPathResource("snsoft/convert/vomapper_signature.xml").getInputStream())
{
Source source = new StreamSource(in);
signatureType = (SignatureType) marshaller.unmarshal(source);
System.out.println(signatureType);
}
DecMain decMainout = new DecMain();
mapperService.mapper(signatureType, decMainout);
//绑定
Jaxb2Marshaller marshaller = new Jaxb2Marshaller();
marshaller.setClassesToBeBound(SignatureType.class);
Result result = new StreamResult(System.out);
marshaller.marshal(signatureType, result);
```
#### VO与JSON转换
平台使用Jackson框架进行json数据转换，示例参考帮助中心。如下是简单说明：
```java
	#解析json的类
	com.fasterxml.jackson.databind.ObjectMapper
	Person person =...
	//绑定
	ObjectMapper mapper = new ObjectMapper();
	String json = mapper.writeValueAsString(person);
	//绑定
	Person p = mapper.readValue(json, Person.class);
```
#### 导入转换VO
Excel、DBF、TXT导入转换VO通过ReadDataSetFactory获得对应数据转换对象操作。如下是简单说明：
```java
//dbf
ReadDataSetFactory.impl.newDbfReadDataSetImpl
//txt
ReadDataSetFactory.impl.newTxtReadDataSet
//xls
ReadDataSetFactory.impl.newXlsReadDataSet
```

**测试用例snsoftx.test.adk.VOConvertDemo为同时使用MapperService、Validator、json、xml转换的例子。**
