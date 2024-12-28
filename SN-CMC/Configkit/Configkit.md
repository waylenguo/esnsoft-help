## 配置管理

|日期|作者|备注|
|------|------|------|
|2018-09-25|张东|初稿|
|2019-09-16|张东|重构|
|2019-10-10|张东|调整|
|2021-04-09|张东|改造|

### 功能说明

1. 通过 **Zookeeper** 集中管理系统中的配置项，配置管理相关操作界面集成在 **管控中心** 中。
1. 全系统可以统一使用一套配置版本，也可以针对不同分组服务分为多个配置包，配置系统可以同时管理多个系统、多个版本的配置包。
1. 配置系统支持配置的版本管理，方便在生产环境下的配置发布、更新和回滚操作。
1. **配置系统只适用于分布式的开发和部署，对于非分布式（一个包启动）的服务结构是不需要使用配置系统的，因为配置全在jar包里。**

### 工程结构

配置管理系统工程为**snadk-configkit**，一共有**common**,**client**两个子工程。
```
client 是其他系统使用配置管理的需依赖的Jar包，提供了相关的客户端方法。
common 工程中封装了配置系统内部使用的异常信息和若干字符串Util类。
```

### 开发

#### 开发指南

1. 非分布式(单服务)开发配置

    ```properties
       统一加载jar包中的资源。
    ```

1. 分布式开发环境配置
    
    - 开发环境需要配置`core.inf`，其中指定了以下几个参数
    ```properties
    #配置文件的装载模式，可以选择的值为 file/zk，对应从文件系统还是zookeeper中装载配置文件。
    cfg.priority=file #开发环境应该配置为file，生产环境下默认为zk
    #配置文件的基准目录，可以配置按照','分隔定义多个目录
    cfg.file.root=/snsoft90/snsoft_adk
    ```
    - 开发环境需要配置`cfg.list`，指定当前应用需要的配置文件列表。
    ```properties
    #优先级上面的高于下面的，基本逻辑应该是应用层放上面底层放下面
    #以'/'开头标示绝对路径,否则是相对cfg.file.root的路径
    #snadk-core
    snadk-core/snadk-bas/src/main/resources
    snadk-core/snadk-dx/src/main/resources
    snadk-ui/snadk-ui/src/main/resources
    ```
    **这两个文件是统一的选项配置放在[sysid]-config工程中，不需要每个开发单独设置，此工程应当在工程结构设计及创建结构统一配置。**
    
    - 相关系统选项
    
    ```properties
    需要配置系统选项中的zookeeper连接字符串：
    snsoft.zk.connectString=127.0.0.1:2181
    注意：这是一个底层公共的配置，不需要单独为配置系统设置。
    ```

    - 新增配置项
    
    ```properties
       要增加新的配置管理项，开发人员需要遵守如下的原则：
       1.选择合适的配置存放根目录。要求所有配置放于ui工程中，使用应用系统的简称作为应用系统配置根目录名称。例如cms系统的配置路径统一放在"/cfg/cms"中。
         在根目录下，依照具体的业务需求自行规划子目录结构。
       2.选择合适的配置文件类型。建议开发人员首选xml格式为配置类型，xml格式支持xsd约束校验，并且使用了第三方工具自动绑定。
         当选择了xml格式时，需按照文档描述的规范进行添加。
       3.修改"cfg.list"文件，在文件中增加应用系统目录。例如cms系统需增加的目录为：product-cms/cms-ui/src/main/resources。
       4.在代码中使用配置工厂获取配置。
       5.配置加载一律使用ConfigResourceFactory.impl中的方法。
    ```
   
#### 开发手册

**以下说明均为分布式服务开发为基础，非分布式(单服务)开发不适用。**

1. 配置工厂
    配置管理对外提供了若干工厂方法，以下工厂方法禁止开发直接调用，仅仅用于开发学习配置系统实现原理。
    ```
    snsoft.cfg.client.factory.ConfigToolkitFactory#getConfigPaths：取指定配置路径下的所有配置文件路径。
    snsoft.cfg.client.factory.ConfigToolkitFactory#getConfigContent：取指定配置文件的内容，根据指定的class，可以返回自动装箱的pojo，也可以返回文件内容字符串。
    snsoft.cfg.client.factory.ConfigToolkitFactory#getDictConfigContent：取指定字典号的字典信息。
    ```
    开发人员调用的方法是经过封装的 ConfigResourceFactory，在ConfigResourceFactory中会先从配置管理系统中获得配置内容，如果没有取到则会从classpath中再取一遍。该方法应用示例如下：
    ```
    AccessConfig config = ConfigResourceFactory.impl.loadConfig(path, "xml", AccessConfig.class);
    或者
    CodedataListType listType = ConfigResourceFactory.impl.loadConfig("codedata/CodeData" + prefix, "xml", CodedataListType.class);
    ```

1. 配置应用规范
    关于配置系统开发规范约束如下：
    
    - 应用系统发布后固定不变的配置必须放在classpath中（main/resource资源目录下），直接应用程序打包，无需使用配置管理系统。
         例如：xsd文件、ORM配置、表结构。
    - 应用系统中需重启服务才能生效或上线后调整较多的配置项不要放入配置管理系统中进行管理。例如：系统选项。
    - 应用系统中调整无需重启服务并且相对固定的配置项都使用配置系统（需统一放在UI工程下的cfg目录中）。
    
    开发人员在配置管理系统中增加配置项时，要根据实际应用场景，选取合适的配置存放路径和解析方式。

1. 配置存放路径规范
    应用配置系统的配置文件必须放在UI工程的cfg文件夹下，文件层级尽量要少一些。
    目前底层配置直接放在cfg目录下，使用的配置目录为：
    ```
    codedata:码表定义
    dict:字典定义
    image:图片处理定义
    res:单据定义/编码规则/工作流...
    ui:界面定义
    ```
    ```
    应用系统增加专有配置时，目录使用小写字母的系统号作为根目录，根目录下按照业务要求自己规划。
    例如 CMS系统使用 cfg/cms作为配置文件根目录。根目录下按业务分为了 site/tpl/widget等子目录。
    ```

1. 支持配置文件类型
    目前系统中的配置文件使用了 xml,inf,txt,json,sql 这四种解析方式。
    其中字典信息的json文件在zk中是按照解析后的List存储，其他类型的文件是以文本形式存储在zookeeper中。
    在获取配置时，配置系统采用了两种加载机制。
    -从文件中直接加载
    -从zookeeper加载
    开发环境中根据配置项 `cfg.priority`启用本地加载机制。
    根据`snsoft.config.file.contenttype.ContentType`中的映射配置获得不同文件类型的解析方法。
    ```properties
    #xml文件
    snsoft.cfg.client.file.contenttype.XmlContentType
    xml文件的默认解析方法是由spring的Jaxb2Marshaller提供，这就要求一个xml格式的配置文件必须有符合xml规范的约束文件和注解配置。
    默认解析器会按照实体类中的xml注解直接返回对应实体对象。
    xml配置文件的定义可参见xml以及xsd约束相关文档要求。
    一般建议开发使用xml格式的配置方式，可以通过xsd约束规范配置文件的格式。
    
    #inf文件
    snsoft.cfg.client.file.contenttype.PropertiesContentType
    inf文件是格式为properties形式键值对的文本文件，因此是不需要解析的，只要符合按行定义的键值对规则即可。
    默认解析器会直接返回文本内容的String。
    一个典型的properties的应用是系统的多语言资源定义文件：ResBundle*.inf
    不建议开发人员使用。
    
    #txt+sql文件
    snsoft.cfg.client.file.contenttype.TxtContentType
    文本文件，因此是不需要解析的，目前系统没有应用，不建议开发人员使用。
    
    #json文件
    snsoft.cfg.client.file.contenttype.JsonContentType
    json格式定义的配置文件，默认解析方法为jackson提供的ObjectMapper，同样会根据传入的Class解析json字符串，返回实体对象。
    当需要定义的配置记录数较少，且配置的属性也比较少时使用。复杂的配置建议使用xml格式。
    一个典型的json应用为部门类型的定义：btype.json
    
    #字典
    snsoft.cfg.client.file.contenttype.DictContentType
    字典本质上还是json文件类型，因为应用时，需要考虑实施人员添加字典内项目，因此单独提供了字典内容的合并方法。
    字典解析后返回 DictInfos 对象。
    ```

#### 开发使用场景

**对于开发阶段，配置系统只适用于分布式的开发，而非分布式（一个包启动）的服务结构是不需要使用配置系统的，因为配置全在jar包里。**

#### 系统设计

@startuml

Title 配置文件类型类图

ContentType <|.. BinaryContentType
ContentType <|.. JsonContentType
JsonContentType <|-- DictContentType
ContentType <|.. PropertiesContentType
ContentType <|.. TxtContentType
ContentType <|.. XmlContentType

ContentTypes o--> ContentType

interface ContentType {
	+ byte[] merge(List<byte[]> dataList, String encoding);
	+ T resolve(byte[] data, String encoding, Class<T> c);
}

class BinaryContentType {
	// 二进制配置
}

class JsonContentType {
	// JSON配置
}

class DictContentType {
	// 字典配置
}

class PropertiesContentType {
	// 属性配置
}

class TxtContentType {
	// 文本配置
}

class XmlContentType {
	// XML配置
}

class ContentTypes {
	+ Class<ContentType<T>> get(String contentTypeName);
	+ boolean containsType(String contentTypeName);
}
@enduml

@startuml

Title 配置文件内容类图


interface ConfigContent<T> {
	+ T get();
	+ String getText();
	+ List<String> list();
	+ boolean save();
}

@enduml

@startuml

Title 配置对象加载时序图

actor 调用者

participant rscfac as "ConfigResourceFactory"
participant tktfac as "ConfigToolkitFactory"

调用者 -> rscfac : [loadConfig]
activate rscfac
	rscfac -> tktfac : [getConfigContent]
	activate tktfac
		
	tktfac -> rscfac
	deactivate tktfac
rscfac -> 调用者
deactivate rscfac

@enduml

### 运维部署

配置管理维护菜单在 《管控中心》 -> 《系统运维》 -> 《服务器配置管理》

**此维护界面的配置需要在第一次服务部署成功后进行配置，一般情况下无特殊服务部署改动，此配置保持不变。**

#### 运维指南

##### 配置发布

**说明：**首先需要说明的是，为了兼容集群+热部署环境，这里引入了服务分组的概念，意思是讲集群服务器分组（一般为两组），来保证不停机更新服务。

* 维护界面

维护界面分为三大部分：服务器分组配置、配置版本列表、配置版本明细；服务器分组配置又分为三个主子孙界面，左边为分组主表，用来定义分组服务器的组别ID，中间的子表为当前组别中，含有相同配置的服务器的分类，右边的孙表为含有当前分类的服务器；
配置版本列表为历史发布的配置版本；配置版本明细为每个配置版本所包含的配置信息。

![服务器配置管理](help/SN-CMC/Configkit/Configkit/1.png)

##### 维护流程

**1、定义分组配置**

第一次部署成功后，在此界面进行分组信息的定义：
定义服务分组ID -> 定义配置分类 -> 使用《选择服务器》按钮配置服务器ID

**2、配置发布**

中间的配置分类表上有一个《配置版本合并并发布》按钮，上述步骤完成后，点击发布按钮弹出如下对话框：

![发布对话框](help/SN-CMC/Configkit/Configkit/2.png)

1. 录入版本名称：名称为一类版本的名字，不小加版本号，发布时系统会自动再名称后边加 #x.x 版本信息。
1. 选择升级类型：默认为升小版本，此选项决定了上述提到的版本后缀#x.x中自增小数点前一位还是小数点后一位。
1. 删除配置版本保留数量：发布完成后会自动执行历史配置版本删除操作，录入的数量为保留最新的配置版本的数量，-1：表示不删除。
    选项：CfgVsn.DeletionKeepQuantity：用于配置默认删除保留个数，会自动带到弹出框。

**注：** 版本名称会自动获取上一次发布的名称，没有发布过则为空。

录入完成后点击确定开始发布配置，发布规则 ： 在挡墙发布的组别中读取每一个配置分类，并逐一加载配置分类中的服务器，如果同一配置分类中的某一台加载到配置，则停止此配置分类的发布，继续读取下一条配置分类进行发布。

**3、配置版本列表**

配置版本合并发布后，在配置版本列表界面可以查询到刚刚发布的配置，在配置版本明细界面可以查看发布的配置版本的明细数据。

![配置版本列表](help/SN-CMC/Configkit/Configkit/3.png)

**4、服务分组明细**

该界面可产看所有服务分组的配置信息。

![配置版本列表](help/SN-CMC/Configkit/Configkit/4.png)

**4、应用配置版本**

** 推荐使用方式二 **

* 方式一

```
修改每台应用服务器的 "System.properties" 文件中的配置项：
"cfg.ver" 修改为对应版本号
修改完成后，需要重启对应的应用服务器。
```

* 方式二

在分组配置界面，勾选需要配置版本的组别，点击《选择配置版本》按钮选择要使用的版本，选择后，启用按钮会自动勾选，启用的数据会只读，以防止误操作，如需要修改分组配置，点击启用按钮取消启用即可。

**5、版本回退**

在当前版本配置出现重大问题，需要将配置还原为上一个版本时，我们只需要在《服务器配置管理》维护界面将应用服务器版本号修改为上一个版本即可。

##### 注意事项

**正式版底层从4.5.0.RELEASE 开始 配置管理和配置加载由ZK迁移至DB，因此配置管理界面需要做数据迁移，维护界面新增按钮《数据迁移》用于手工迁移界面数据，并且此按钮只存在4.5.0.RELEASE版本；数据迁移完成后需要重新发布配置版本。**

![数据迁移](help/SN-CMC/Configkit/Configkit/5.png)

#### 运维手册

#### 运维使用场景

**此功能只在需要分布式服务配置集中管理时使用，对于非分布式(单服务)的服务(包含集群)程序是直接读取对应服务器程序包丽丽的配置资源。**

### 自动化TAC脚本

```html
#版本清理
#时间排序保留指定最新个数的版本配置
/**
 * @param cnt 保留个数
 */
deployer = getBean("SN-CMC.Deployer")
deployer.deleteCfg(5)

#按服务组发布配置
#注意：此接口一次只允许传入一组服务分组
/*
 * @param version 分配的版本号，不为空时不发布配置版本，直接使用此版本。
 * @param groupID 分组ID
 * @param inctype 0 表示升大版本；1 表示升小版本。
 */
deployer = getBean("SN-CMC.Deployer")
deployer.deployCfg(null,"HelpGrp01",1)

```

### 管理工具

在管控中心提供了两个zookeeper相关的开发辅助工具：

 - ZK信息查询
 - ZK信息监控
这两个工具实现的查询功能都可以在zookeeper服务中使用命令实现。
提供工具为了方便开发人员理解zookeeper数据结构和简单的数据查询。

#### ZK信息查询

ZK信息查询为我们提供了一个直观查看当前服务器连接的zookeeper中的节点信息。
我们可以直接查看根节点下的数据，也可以根据录入的节点路径查看其子节点的数据。目前系统中可以查到的节点有：

/cfg:配置节点，用于南北系统的配置维护功能。在这个节点下存储南北系统所有的配置数据。
当开发人员怀疑某项配置未生效时，可在此节点下查看对应的配置内容。
```
例如当怀疑CodeData定义出现问题时，可以查看节点 /cfg/appx/1.0.0/codedata/CodeData 中的内容。
在Value中以String方式存放了CodeData.xml文件的所有内容。
```
/cfg树节点说明
```
/cfg/appx #配置管理根节点，其子节点为上传的配置包版本号
    /cfg/appx/版本1
        ...
    /cfg/appx/版本2
        /cfg/appx/版本2/DictInfos #字典节点，其子节点为配置系统管理的字典列表
        /cfg/appx/版本2/access #接入系统配置管理节点。
        /cfg/appx/版本2/cms #cms系统配置管理节点。
        /cfg/appx/版本2/codedata #码表定义存储节点，其子节点为码表定义文件列表
            /cfg/appx/版本2/codedata/CodeData01 #01系统码表定义文件
            ...
        /cfg/appx/版本2/filesres #文件资源路径定义存储节点，其子节点为文件资源路径定义文件
        /cfg/appx/版本2/image #图片处理配置定义存储节点，其子节点为图片处理配置定义文件列表
        /cfg/appx/版本2/res #单据相关资源文件存储节点，其子节点为单据相关定义列表
            /cfg/appx/版本2/res/accode #编码规则定义
            /cfg/appx/版本2/res/sheet #单据定义
            /cfg/appx/版本2/res/status #单据状态定义
            /cfg/appx/版本2/res/workflow #工作流定义
        /cfg/appx/版本2/resbundle #多语言资源存储节点，其子节点为多语言资源文件列表
            /cfg/appx/版本2/resbundle/ResBundle00_zh #00系统中文资源文件
        /cfg/appx/版本2/ui/res #界面定义存储节点，其子节点为界面定义号
            /cfg/appx/版本2/ui/res/00/TBLEXPORT #例如界面 00.TBLEXPORT
            ...

```

/dubbo:dubbo注册中心节点，用于dubbo注册和订阅服务的provider和consumer。
```
在节点 /dubbo 下查看目前注册在dubbo中所有的Service名称。在每个Service下注册所有的provider和consumer。
通过provider和consumer节点可以看到目前注册成功的Service请求URL。
注意：这个节点是由dubbo进行维护的，这里只是把dubbo维护的内容查询出来。
例如节点：
/dubbo/snsoft.acs.service.AccessUIService #AccessUIService服务注册节点
    /dubbo/snsoft.acs.service.AccessUIService/configurators #服务配置节点，存储服务的dubbo配置，一般为空
    /dubbo/snsoft.acs.service.AccessUIService/consumers #消费者节点，其子节点为消费者列表
    /dubbo/snsoft.acs.service.AccessUIService/providers #生产者节点，其子节点为生产者列表
    /dubbo/snsoft.acs.service.AccessUIService/routers #dubbo服务路由，一般为空
```
/sncmc:管控节点，用于南北系统服务名注册功能，在/sncmc/snid节点下存储南北系统所有活跃的应用服务名称。
```
可以根据这个节点下的内容判断某个ID的应用服务是否成功启动。
例如：
/sncmc/snid/SN-UI01
/sncmc/snid/SN-Service01
...
标识目前应用服务启动了 SN-UI01、SN-Service01 两台。
```

#### ZK信息监控

ZK信息监控为我们提供了一个查看zookeeper状态的界面。
在查询界面中列表展示了当前应用服务器连接的所有zookeeper服务器以及服务器对应的监控指标。
指标包括：

- 连接数
- 主从状态
- 存储的节点数
- 网络延迟
- zoo.cfg配置
- 客户端连接列表
- 客户端WatchPath

其中，核心的zookeeper健康度指标为连接数/zoo.cfg配置
```
连接数：zookeeper单个服务的客户端连接数，一般生产环境中设置在6000~10000。生产环境中，经过一段时间运行，客户端连接数应该在200左右，
如果远超出200，则说明zookeeper服务出现异常，需要根据服务的日志排查问题。

zoo.cfg配置：查看当前zookeeper服务的配置信息，方便开发人员查看zookeeper的连接数配置、心跳时间、session超时设置。
```
其他指标只是展示一下zookeeper服务的状态，对服务健康度的查看帮助不大。