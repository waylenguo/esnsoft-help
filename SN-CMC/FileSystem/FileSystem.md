## 文件系统

|日期|作者|备注|
|------|------|------|
|2018-09-25|张东|初稿|
|2019-05-24|张东|重构|
|2019-10-10|张东|调整|
|2021-07-22|张东|调整|

### 功能说明

在开发中，经常会涉及到附件的功能，有的时候文件存储在数据库，有的时候存储在服务器的某个目录，有的时候存储在ftp上。为了开发便于进行文件上传下载的开发，底层提供了一套完善的文件系统服务。

附件上传功能摒弃原来的先上传至临时表再进行存储的方式，改为直接使用fs进行存储，原来的方式任然保留兼容。

* 默认两种宏：
    * DYNDOCROOT权限
    * STADOCROOT非权限
* 路径存储方案：使用文件MD5散列三级目录存储
* 配置方式：FSID.DYNDOCROOT=xxxxxx ； FSID.STADOCROOT=xxxxxx
* 文件存储模式：fs/DYNDOCROOT/md5|xxx.doc
* 文件下载模式：fs/DYNDOCROOT/md5|xxx.doc?atblname=&ainnercode=&anamecol=
* UI层根据单据号及单据内码查询是否有权限
    实现：使用atblname+ainnercode+anamecol查询存储的url与下载url是否匹配(select sheetcode,anamecol,innerfld from atblname where ainnercode='ainnercode')

** 文件存储不压缩。**

### 服务类型

底层按文件的存储位置提供了不同的FileSystem。

#### Ftp文件服务

```
类：snsoft.commons.file.impl.FtpFileSystem
文件存储在Ftp上，底层会默认使用此文件系统。
文件系统文件下载url规则：ftp://wsp!FSID/dir1/dir2/.../文件名?_downloadmode=2&nocache=true&_rnd=随机数&filenamex=URL编码的文件名(一般用于处理中文)
参数依次解释：wsp账套id；FSID配置的文件宏；dir[i]文件目录
例：fs/00!DYNDOCROOT/OfficeOl/Office00000001.docx?_downloadmode=2&amp;nocache=true&amp;_rnd=1540798748804&amp;filenamex=0062006F006F0074002D00720075006E73AF5883914D7F6E002E0064006F00630078
```

#### 服务器目录服务

```
类：snsoft.commons.file.impl.LocalFileSystem
文件存储在服务器某个目录，底层会默认使用此文件系统。
文件系统文件下载url规则：ftp://wsp!dir1/dir2/.../文件名?_downloadmode=2&nocache=true&_rnd=随机数&filenamex=URL编码的文件名(一般用于处理中文)
参数依次解释：wsp账套id；dir[i]文件目录
例：fs/00!TMP/Office00000016.xlsx?_downloadmode=2&nocache=true&_rnd=1540797901453&filenamex=95EE989870B9002E0078006C00730078
```

#### 数据库文件服务

```
类：snsoft.file.dx.DxFileSystem
文件存储在数据库中的时候，底层会默认使用此文件系统。
文件系统文件下载url规则：fs/wsp!dx:table:keyColumns:zsizeColumn:dateColumn:valueColumn:options/文件名?_downloadmode=2&_nocache=true&_rnd=随机数
参数依次解释：wsp账套id；table表名；keyColumns主码.文件名列；zsizeColumn压缩后的文件 大小；dateColumn日期列；valueColumn数据列；options是否压缩
例：fs/00!dx:filetest:icode.filename:::filedata:1/Office00000005?_downloadmode=2&_nocache=true&_rnd=1540797905186
```

#### 文件临时存储服务

**临时表（tmpfiledata）方式废除**

```
接口：snsoft.data.service.TmpFileAccessService

# 临时文件存储目录方式：snsoft.data.service.impl.FSTmpFileAccessServiceImpl
开发环境默认目录：${SN.ConfigPath}/fsroot
线上环境默认目录：FSID.STADOCROOT，FSID.STADOCROOT未配置时 与开发环境目录相同 
修改默认配置：（System.properties）
    配置示例：
        TmpFileFS=TmpFile ：表示临时文件存储位置为 FSID.TmpFile
        FSID.TmpFile=file:///D:\snsoft90\snsoft_adk_iws\fsroot
    也可以只配置 FSID.TmpFile=file:///D:\snsoft90\snsoft_adk_iws\fsroot
配置目录是本地目录
注意：当使用本地目录时，集群/分布式环境需要配置为共享磁盘目录
临时文件默认有效期为半小时

# 临时表方式（此方式废除）：snsoft.data.service.impl.TmpFileAccessServiceImpl
底层提供了一个临时文件表tmpfiledata，一般用来做上传时文件的临时存储，存储时间默认为30分钟。
文件系统文件下载url规则：fs/wsp!tmpfiledata/[icode]/文件名?_downloadmode=2&nocache=true&_rnd=随机数&filenamex=URL编码的文件名(一般用于处理中文)
参数依次解释：wsp账套id；tmpfiledata临时表名；文件在表中的id
例：fs/00!tmpfiledata/0000000000000010/销售合同.docx?_downloadmode=2&nocache=true&_rnd=1540797901453&filenamex=95EE989870B9002E0078006C00730078
```

### 权限配置

在需要严格控制下载动作的权限时，配置如下：

```properties
以后文件系统的配置默认为：FSID.DYNDOCROOT和FSID.STADOCROOT
需要控制权限的文件使用FSID.DYNDOCROOT并配置DYNDOCROOT.Permission=snsoft.ui.controller.DynAccessPermission.new;
不需要控制权限的使用FSID.STADOCROOT。
```
**注意：snsoft.ui.controller.DynAccessPermission.new 参考示例，应用层自己根据需求参考使用。**


### 文件上传
#### 文件上传指南

界面定义配置示例：

```html
#服务器目录/ftp
<c name="filepath" title="文件" sqltype="12" width="200" disableed="true" disableEnterEd="true" aidInputerBtn="true" attachment="true" aiprops.sheetCode="xxxx" aiprops.attached="fs/DYNDOCROOT/"/>

#可选择多个文件
<c aidinputer="Xjs.table.util.FileUpload.new" name="attach02" sqltype="12" title="文件上传" aiprops.sheetCode="xxxx" aiprops.attached="fs/DYNDOCROOT/" />

#数据库
<c name="filename" sqltype="12" title="附件名称" aidInputerBtn="true" aiprops.attached="filedata" attachment="true" />
<c name="filedata" sqltype="12" title="数据库文件" width="300" />
```

** 主要配置为：aiprops.sheetCode="xxxx" aiprops.attached="fs/DYNDOCROOT/" **

#### 上传存储扩容

文件存储出现容量不够的情况时，上传文件支持扩容配置：
示例：
```properties
#扩容前配置：
FSID.DYNDOCROOT=file:///xxx/xxxx/xxxxx/
扩容后配置
FSID.DYNDOCROOT.Curr=FSID.DYNDOCROOT01
FSID.DYNDOCROOT01=file:///yyy/yyyy/yyyyy/
FSID.DYNDOCROOT=file:///xxx/xxxx/xxxxx/
```

#### 文件上传手册

配置说明：
```
fs/：标准路径
DYNDOCROOT：文件存储地址配置，在System.properties里边配置，如：FSID.DYNDOCROOT="ftp://..."
路径存储方式为 DYNDOCROOT/+文件MD5散列三级目录。
```

系统底层支持默认的文件上传服务，文件上传流程为：点击上传按钮弹出上传对话框，选择需要上传的文件，点击上传，此时会将文件存到定义的文件服务器中
* 使用service组件做为界面定义查询存储服务：
  在使用业务平台时，单据提供了单据附件功能，见：<a target="_blank" href="help.html?helpFile=help/SN-PLAT/Sheet/SheetAttach.md">单据附件</a>。
  
  参考示例：《<a target="_blank" href="https://n.esnsoft.cn/N9Bhelp/">帮助中心</a>》-->《销售管理》-->《销售合同》-->附件示例
* 使用基本的界面定义存储查询：
  大师默认处理。
  参考示例：《<a target="_blank" href="https://n.esnsoft.cn/N9Bhelp/">帮助中心</a>》-->《界面搭建》-->基础组件-->文件存储示例

#### 文件上传时序图

@startuml
Title 文件上传时序图

actor 客户端

participant jslis           as "AbsSelectAttachment"
participant comp            as "UICompDataSaveService"
participant utils           as "AttachmentUtils"
participant fileservice     as "ftp/db/tmpdir"

客户端 -> jslis : 客户端点击上传
note right of 客户端: 调用远程:st-snsoft.ui.util.AttachmentUtils.upload
activate jslis
	jslis -> utils : [uploadFs] \n根据请求参数处理文件
	activate utils
		utils -> fileservice :  [saveFS] \n将文件存入文件系统
	    fileservice -> utils :  [return id] \n返回记录id
	deactivate utils
	utils -> jslis : [filePath] \n返回文件存储地址
deactivate jslis
jslis -> 客户端 : 返回给客户端
@enduml

### 文件下载
#### 文件下载指南

文件下载与文件上传其实是配套的，配置好文件上传辅助录入后，上传列上传文件后可以进行鼠标点击上传的文件，也可以通过客户端监听编写下载按钮触发上传列的辅助录入，或使用业务平台已有的下载功能。

** 示例同文件上传。 **

#### 文件下载手册

1. 服务入口
服务的入口使用的是Spring MVC的Controller，固定的url地址为ROOTPATH/fs/**。
```
Class：snsoft.ui.controller.FileSystemController
UrlPath：/fs/**
文件存储地址配置：FSID.* = 文件地址（可以是ftp、服务器目录等。）
```

1. 客户端构建下载地址
xjs.table.util.AbsSelectAttachment.buildDownloadURL(value, table, columnName, r, inlineAttachment)
参数解释依次为：地址列值、table、列名、行号、downloadmode是否为下载（一般为true，true为1，false为2）。
一般配合cellRender使用。

#### 文件下载时序图

@startuml
Title 文件下载时序图

actor 客户端

participant controller      as "FileSystemController"
participant service         as "FileSystem"
participant xxservice       as "xxFileSystem"
participant fileservice     as "ftp/db/tmpdir"

客户端 -> controller : 客户端get请求
activate controller
	controller -> service : [parseResource]
	service -> controller : [Resource]
	activate service
		service -> xxservice :  [getFileSystem] \n根据请求解析所使用的文件系统
	    xxservice -> service :  返回对应的FileSystem
	deactivate service
	service -> controller : [fs]
	controller -> xxservice : [openFile(file)]  \n打开文件
	xxservice -> fileservice : [loadData/openRead/new FileInputStream(f)]获取文件
	fileservice -> xxservice : 返回文件
	xxservice -> controller : 拷贝成byte[]
deactivate controller
controller -> 客户端 : 返回给客户端
@enduml

### 文件系统类图

@startuml
Title 文件系统类图

FileSystem <|-- LocalFileSystem
FileSystem <|-- FtpFileSystem
FileSystem <|-- DxFileSystem

interface FileSystem {
	factory;
	InputStream openFile(String fileName);
	File getFile(String fileName);
	void writeFile(String fileName,InputStream data,long time,boolean gzip);
	void  writeFile(String fileName,byte[] data,long time);
	boolean mkdir(String path);
	boolean  deleteFile(String path,boolean deleteNonEmptyDir);
}

class LocalFileSystem {
    LocalFileSystem(String rootPath);
    File getFile(String fileName);
    boolean deleteFile(String path,boolean deleteNonEmptyDir);
    boolean mkdir(String path);
    InputStream openFile(String fileName);
    InputStream openFile(File file);
    void writeFile(String fileName,InputStream data,long time,boolean gzip);
    void  writeFile(String fileName,byte[] data,long time);
}

class FtpFileSystem {
    FtpFileSystem(String url);
    FtpClient newFtpClient(String host, int port, String user, String password, String protocol);
    FtpClient getFtpClient();
    File[] listFiles(String path, boolean subdir);
    boolean deleteFile(String fileName, boolean deleteNonEmptyDir);
    InputStream openFile(String fileName);
    InputStream openFile(File file);
     writeFile(String fileName, InputStream data, long time);
}

class DxFileSystem {
    DxFileSystem(String url);
    File[] listFiles(String path, boolean subdir);
    boolean deleteFile(String path, boolean deleteNonEmptyDir);
    boolean mkdir(String path);
    InputStream openFile(String fileName);
    void writeFile(String fileName, InputStream data, long time);
}

@enduml

@startuml

TmpFileAccessService <|-- TmpFileAccessServiceImpl
TmpFileAccessService <|-- FSTmpFileAccessServiceImpl

interface TmpFileAccessService {
    impl;
    void setDataAsTempFile(FileContentData fileData, InputStream is, String fsIDAndPath, String fnameFormat, boolean gzip);
    String saveTmpFile(String id, String fileName, int validTime, byte data[], boolean zip);
    TmpResource openTmpFile(String fileName);
    byte[] loadTmpFile(String id, boolean unzip);
    void deteleTmpFile(String id);
    String buildDownloadURL(String id, String fileName, int downloadMode);
}

class TmpFileAccessServiceImpl {
    TmpFileAccessServiceImpl();
    void setDataAsTempFile(FileContentData fileData, InputStream is, String fsIDAndPath, String fnameFormat, boolean gzip);
    String saveTmpFile(String id, String fileName, int validTime, byte data[], boolean zip);
    byte[] loadTmpFile(String id, boolean unzip);
    void deteleTmpFile(String id);
    String buildDownloadURL(String id, String fileName, int downloadMode);
}

class FSTmpFileAccessServiceImpl {
    FSTmpFileAccessServiceImpl(String fsIdAndPath);
    FSTmpFileAccessServiceImpl(String fsID, String rootPath);
    void setDataAsTempFile(FileContentData fileData, InputStream is, String fsIDAndPath, String fnameFormat, boolean gzip);
    String saveTmpFile(String id, String fileName, int validTime, byte data[], boolean zip);
    TmpResource openTmpFile(String fileName);
    byte[] loadTmpFile(String id, boolean unzip);
    void deteleTmpFile(String id);
    String buildDownloadURL(String id, String fileName, int downloadMode);
}

@enduml

### 文件监控

用于监控文件系统的资源使用情况。

** 注意：此功能需要使用snsoft.dx.filesres.domain.FileSystemConn作为文件系统的代理对象。 **

#### 监控列表
```
文件地址、监控、执行次数、成功次数、异常次数、连接次数、连接、成功率
```
#### 清空当前监控信息

点击《清空当前监控信息》按钮清空当前选中记录的监控信息。

#### 清空所有监控信息

点击《清空所有监控信息》按钮清空所有的监控信息。

#### 启动监控

System.properties中配置FS.Pool.Monitor=true，开启监控。

#### 关闭监控

System.properties中配置FS.Pool.Monitor=false，关闭监控。