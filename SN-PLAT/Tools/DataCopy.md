## 数据拷贝

|日期|作者|备注|
|------|------|------|
|2018-10-22|冀小雷|初稿|

### 单据复制说明

1. 数据拷贝配置请参见对应的Schema文件【[CopyDef.xsd](do/BrowseSchema?schema=CopyDef.xsd)】；
2. 配置文件路径：res/datacopy/*.xml
3. 数据拷贝仅提供对应的数据拷贝服务，不提供统一的监听；

### 服务调用方式

```java
CopyServiceFactory<?,?,?,?> factory = SpringBeanUtils.getBeanByName("SN-PLAT.CopyServiceFactory");
CopyService<?,?,?,?> copyService = factory.newCopyService("数据拷贝配置编码");
copyService.copy(params);
```

### XML配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<CopyDef xmlns="http://snsoft.cn/schema/CopyDef.xsd">
	<Name></Name>
	<CopyType>SN-PLAT.01</CopyType>
	<Remark><![CDATA[
	]]></Remark>
	<Attrs>
		<Aname></Aname>
		<Stdvalue></Stdvalue>
	</Attrs>
</CopyDef>
```

### 类图

@startuml
Title 数据拷贝定义类图

CopyType *-right-> CopyAttr
CopyAttr <-left-* CopyDef

CopyTypeService .up.> CopyType
CopyDefService o-left-> CopyTypeService
CopyDefService .up.> CopyDef
CopyDefService .up.> CopyAttr

class  CopyDef {
	# String copycode;
	# String copytype;
	# List<CopyAttr> attrs;
}

class CopyType {
	# String copytype;
	# String name;
	# String bean;
	# List<CopyAttr> attrs;
}

class CopyAttr {
	# String aname;
	# String title;
	# String stdvalue;
}

interface CopyDefService {
	List<CopyDef> queryUI();
	List<CopyAttr> queryCopyAttr();
	void saveUI(List<CopyDef> records);
	void syncToDB(String copycode);
}

interface CopyTypeService {
	List<CopyType> query();
	CopyType getCopyType(String copytype);
}
@enduml

@startuml
Title 数据拷贝功能类图

CopyServiceFactory .right.> CopyDef
CopyServiceFactory .left.> CopyService
CopyService <|.. CopyServiceImpl
CopyServiceImpl ..> CopyDef
CopyServiceImpl "1" o--> "n" DataCopyListener

CopyServiceImpl ..> ExecuteCopyService
ExecuteCopyService <|-- ExecuteCopyService01
ExecuteCopyService01 <|.. ExecuteCopyService01Impl


class  CopyDef {
	# String copycode;
	# String copytype;
	# List<CopyAttr> attrs;
}

interface CopyServiceFactory {
	CopyDef getCopyDef(String copycode);
	CopyService<P,S,T,R> newCopyService(String copycode);
}

interface CopyService {
	R copy(P params);
}

class CopyServiceImpl {
	# CopyDef copyDef;
	- List<DataCopyListener> listeners;
}

interface DataCopyListener<P,S,T,R> {
	void beforeLoad(P param);
	void afterLoad(P param, S source);
	void beforeMapper(S source);
	void afterMapper(S source, T target);
	void beforeSave(T target);
	void afterSave(T target, R result);
}

interface  ExecuteCopyService<P,S,T,R> {
	S load(P param);
	T mapper(S source);
	R save(T target);
}

interface ExecuteCopyService01 {
	
}

class ExecuteCopyService01Impl {

}
@enduml

### 时序图

@startuml
Title 数据拷贝时序图

actor 服务调用者

participant fac as "CopyServiceFactory"
participant ser as "CopyService"
participant exe as "ExecuteCopyService"
participant lis as "DataCopyListener"

服务调用者 -> fac : [newCopyService] \n创建拷贝服务
activate fac
fac -> 服务调用者 
deactivate fac

服务调用者 -> ser : [copy] \n执行拷贝逻辑
activate ser
	ser -> lis : [beforeLoad]
	ser -> exe : [load]
	ser -> lis : [afterLoad]
	ser -> lis : [beforeMapper]
	ser -> exe : [mapper]
	ser -> lis : [afterMapper]
	ser -> lis : [beforeSave]
	ser -> exe : [save]
	ser -> lis : [afterSave]
ser -> 服务调用者  : 拷贝逻辑处理结果
deactivate ser
@enduml