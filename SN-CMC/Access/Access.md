## 文件访问

|日期|作者|备注|
|------|------|------|
|2018-11-12|陈泰林|初稿|
|2019-10-22|陈泰林|初稿|

### 指南

#### 概念说明

针对当前可以加载到的文件进行查询与修改操作，用于操作XML、JSON文件或者操作MDB。

#### 使用说明

继承对应的VOAccess接口，即可直接使用查询和修改操作

**注意事项**MDB使用的基本的查询与修改功能，可用DefaultMGDataAccessService类替代查询使用

### 手册

snsoft.help.dx.access.service.impl.XMLVOAccessServiceImpl
snsoft.help.dx.access.service.impl.JSONVOAccessServiceImpl

### 设计

#### 相关程序

文件访问类：snsoft.bas.access.FileAccess<T, P extends QueryParams>
VO接口：snsoft.bas.access.VOAccess<T, P extends QueryParams>
JSON文件转换类：snsoft.bas.access.JSONAccess<T, P extends QueryParams>
XML文件转换类：snsoft.bas.access.XMLAccess<T, P extends QueryParams>
MDB文件转换类：snsoft.bas.access.MongoDBAccess<T, P extends QueryParams>

#### 类图

@startuml
Title 功能类图

VOAccess <|.. FileAccess
FileAccess <|.. JSONAccess
FileAccess <|.. MongoDBAccess
FileAccess <|.. XMLAccess

class VOAccess{
 + public abstract QueryResults<T> query(P params)
 + public abstract void save(SaveParams<T> saveRecords)
}


@enduml