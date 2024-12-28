## 生成MDB索引

|日期|作者|备注|
|------|------|------|
|2018-09-28|吕希兵|初稿|
|2018-10-10|吕希兵|功能调整更新|
|2019-11-13|郭鑫|修改|

### 指南

#### 概念说明

Mongo插入数据前，创建Mongo索引以提升数据查询效率。

#### 使用说明

##### VO注解

1. 加类注解@Document(org.springframework.data.mongodb.core.mapping.Document)
	属性collection：集合名称(等价于该VO对应的关系数据库表名)

2. 在需要生成索引的字段上加@Indexed(org.springframework.data.mongodb.core.index.Indexed)
    属性value：这个索引的方向，IndexDirection.ASC(升序)，IndexDirection.DESC(降序), IndexDirection.BOTH(两者)	默认为 升序
		name:被创建的索引的名称
		unique:创建一个唯一索引, 默认为：false
		dropDups：此参数表明，当为某个字段创建唯一索引时，删除其他相同值的记录。只保留第一条记录。
				true:删除重复， false:不删除重复（当有重复值时唯一索引创建失败）；默认为false.

##### 配置文件

示例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<db id="00" title="基础表"
	xmlns="http://www.snsoft.com.cn/schema/CreateMGIndex" xsi:schemaLocation="http://www.snsoft.com.cn/schema/CreateMGIndex http://www.snsoft.com.cn/schema/CreateMGIndex.xsd"  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

	<table name="applog" beanname="mongoDatabase" tpltname="mongoTemplate" title="日志类" clazz="snsoft.dx.mdb.log.Applog" >
	</table>
</db>
```


### 手册

* 菜单位置：管控中心->系统工具->数据库工具-> [生成MDB索引](uiinvoke/00/zh_CN/theme0/90.db.CREATEMGINDEX.html)

1. MDB索引配置文件路径：resources/cfg/snsoft/sndata/*.xml （*代表系统号）
1. MDB索引配置请参见对应的Schema文件【[CreateMGIndex.xsd](do/BrowseSchema?schema=CreateMGIndex.xsd)】；


### 实践

### 设计
