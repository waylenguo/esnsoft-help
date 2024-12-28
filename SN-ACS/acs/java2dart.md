## Java实体转Dart实体工具

|日期|作者|备注|
|------|------|------|
|2021-3-11|赵玉柱|初稿|

## 主要功能

### 【Model转DartModel】 
* 将java实体类转换成Dart类
* 自动添加 JsonSerializable 的json注解
* 自动添加 json_annotation 的包
* 自动添加类中的引用 非常规类需要写[配置文件](#配置文件)
* 自动添加toJson方法
* 自动添加fromJson方法
### 【Service转DartService】 
* 将JavaService转换成DartService
* 自动添加dio
* 自动添加acscode 方法注释第二行需要是接入编号
* 自动添加await和async关键字（异步）
* 自动添加CancelToken变量（页面关闭自动取消网络请求）
* 自动转换成通用方法体
### 【生成Api】 
* 将JavaService内的方法转换成DartApi常量
* 自动转换常量
* 自动添加变量hostName 引用AppConfig.hostname;
* 自动添加变量baseUrl 引用AppConfig.apiHost
* 自动添加变量COMMON_MobileUrl <span style="color:red">此值需要手动修改，或上面三行不拷贝</span> <span style="color:blue">由于JVM编译后会抹掉注释信息，所以此功能依赖JAVA源文件</span>
* 自动将Service的方法名转换成变量名，根据${COMMON_MobileUrl}+"/"+[方法注释的第二行] 设置为参数值<span style="color:blue">由于JVM编译后会抹掉注释信息，所以此功能依赖JAVA源文件</span>

### 【生成接入配置】 
* 将javaSewrvice转换成接入配置
* 自动生成标准xml格式
* 自动添加接入编号<span style="color:blue">由于JVM编译后会抹掉注释信息，所以此功能依赖JAVA源文件</span>
* 自动添加调用信息${BeanName}.${methodName}()

### 其他说明

按照默认规范
java为以下包路径
1. [baspkg]/[pkg] ... 功能包
  1. model ... Model包 无s
  1. service ... Service 包
  1. constants ... 常量类包
  1. utils ... 工具类包

dart为以下包路径
1. [baspkg]/[pkg] ... 功能包
  1. models ... Model包 有s
  1. service ... Service包
  1. constants ... 常量类包
  1. utils ... 工具类包

若同时点选项[Model转DartModel](#【Model转DartModel】)和[Service转DartService](#【Service转DartService】)则需要配置[java类/包/文件/文件夹路径]为其上级路径，即[baspkg]/[pkg]


## 录入说明

### java类/包/文件/文件夹路径
 
 1. java类路径
  若录入为java类路径，则表示仅仅转换某一个类为dart对象，由于经过java编译之后的class文件不包含注释信息，所以生成的dart类也不包含注释信息
  注意，需要当前项目能访问到对应的class
  eg: snsoft.acs.vo.AccessClient 本地代码
  eg：org.slf4j.event.SubstituteLoggingEvent jar包内文件
  
  
 1. 包
  若录入为java包路径，则表示将某一个包下所有的java类转换为dart对象，由于经过java编译之后的class文件不包含注释信息，所以生成的dart类也不包含注释信息
  注意：需要当前启动项目能访问到对应class
  eg: snsoft.acs.vo
  
 1. java源文件
  若录入为java源文件路径，此时还该文件还未经过java编译，生成的dart文件将自动追加注释信息
  eg: D:\snsoft90\snadk_product\product-acs\acs-api\src\main\java\snsoft\acs\vo\AccessClient.java
  
 1. 文件夹路径
  若录入为文件夹路径，此时将该文件夹下所有的java源文件转换为dart文件，自动追加注释信息
  eg: D:\snsoft90\snadk_product\product-acs\acs-api\src\main\java\snsoft\acs\vo
  
 1. 接入配置|请求方
 生成接入配置使用，可不配置
 1. 接入配置|响应方
 生成接入配置使用，可不配置
### 目标路径
    
    转换成dart文件后的输出位置，配合选项文件输出使用
    
### 位置选项 
 
 1. 控制台输出  将转换后的结果输出到控制台上 依赖参数 [java类/包/文件/文件夹路径]
 1. 文件输出  将转换后的结果输出到目标路径下的dart文件中，依赖参数[java类/包/文件/文件夹路径]，[文件|目标路径]

### 其他选项
1. [Model转DartModel](#【Model转DartModel】)
* 必选参数 无
* 可选参数 无
1. [Service转DartService](#【Service转DartService】)
* 必选参数<span style="color:red">[生成Service|引用Model路径],生成方法使用</span>
* 可选参数 无
1. [生成Api](#【生成Api】)
* 必选参数<span style="color:red">[生成Service|引用Model路径],生成方法使用</span>,
* 可选参数 无
1. [生成接入配置](#【生成接入配置】)
* 必须参数 无
* 可选参数 <span style="color:red">[接入配置|请求方]</span>，<span style="color:red">[接入配置|响应方]</span>
 
## 配置文件
 
 默认路径为当前启动项根目录下的resources/snsoft/j2d/
 
 j2dCCfg.list java类对应的dart文件
 j2dDCfg.list dart对应的dart文件
 
 说明 由于此工具的java生成dart和查找引用均为一个java类对应一个dart文件，且对应规则为java的驼峰命名转换为dart的下划线命名，
 故存在引用的情况下，会追加默认引用，若在j2dCCfg.list文件中指定某个类的引用地址，则优先使用此地址，
 若j2dDCfg.list文件中配置dart规则指定的地址，则其次使用此地址，若找不到对应，则使用默认
 eg:当前转换类为 snsoft.acs.vo.AccessData 继承 snsoft.acs.vo.AccessParam
 有属性 snsoft.acs.vo.AccessHeader		head
 则在生成dart文件时，将自动追加对AccessData和AccessHeader的引用
 并且由于为了将dart文件集中，将AccessParam生成的dart类和AccessHeader生成的dart类放在access/access_comm.dart文件中
 则可选以下两种配置中任意一种
 1. j2dCCfg.list 文件下追加
 snsoft.acs.vo.AccessData=package:access/access_comm.dart
 snsoft.acs.vo.AccessHeader=package:access/access_comm.dart
 
 2. j2dDCfg.list 文件中追加
 
  access_data=package:access/access_comm.dart
  access_header=package:access/access_comm.dart