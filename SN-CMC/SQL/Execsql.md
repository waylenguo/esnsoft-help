### SQL工具

|日期|作者|备注|
|------|------|------|
|2018-09-25|冀小雷|初稿|

注意：此功能仅超级管理员可以使用。

### DDL
数据库模式定义语言DDL(Data Definition Language)，是用于描述数据库中要存储的现实世界实体的语言。
#### 表结构
```
--创建
create table xxx
(
	id varchar(32) not null
)
--删除
drop table xxx;
```

#### 索引
```
--创建
create index xx on table_name(field_name[,field_name1...]);
--删除
drop index xx;
```

#### 列
```
--增加
alter table add xx varchar(16);
--修改
alter table xxx modify id varchar(64);
--删除
alter table drop column id;
```

#### 约束
```
--创建
--删除
```

### DML
数据操纵语言（Data Manipulation Language, DML）是SQL语言中，负责对数据库对象运行数据访问工作的指令集，以INSERT、UPDATE、DELETE三种指令为核心，分别代表插入、更新与删除，是开发以数据为中心的应用程序必定会使用到的指令，因此有很多开发人员都把加上SQL的SELECT语句的四大指令以“CRUD”来称呼。

#### 增加
```
--所有列按照列序插入
insert into xxx values(?,?,...);

--指定列按照列序插入
insert into xxx(field1,field2,field3) values(?,?,?);
```

#### 修改
```
update table_name set field1=?,field2=?,,,
where condition
```

#### 删除
```
delete from table_name where condition;
```

### SQL

结构化查询语言(Structured Query Language)SQL，是一种特殊目的的编程语言，是一种数据库查询和程序设计语言，用于存取数据以及查询、更新和管理关系数据库系统名。

#### 格式
```
select
from 
where 
group by 
having 
order by 
```

#### 结果说明
如果是针对单表查询且存在主键，则可以在界面上直接修改并存盘。

#### 列配置
数组JSON格式，用于配置多个列，详细属性可以参考 xjs.table.TableColumn
实现包含码名映射功能简单的界面查询功能，配合“保存参数”实现复用。
```
[
	 {name:'列名1',title:'列标题1',width:100,codedata:'#xx'}
	,{name:'列名2',title:'列标题2',width:100,codedata:'#xx'}
	...
	,{name:'列名n',title:'列标题n',width:100,codedata:'#xx'}
]
```

#### 前Tac
在SQL查询前执行，可以对SQL语句进行修改，有三个全局变量：
* e：snsoft.ui.UIEvent；
* db：snsoft.dx.Database，对于参数数据源ID中选中的数据库连接；
* sql： 将要执行的SQL

Tac语法参见[Tac语法](help.html?helpFile=help/SN-CMC/Tac/Exectac.md)

#### 后Tac
在SQL查询后执行，可以对查询结果进行加工处理，有三个全局变量
* e：snsoft.ui.UIEvent；
* db：snsoft.dx.Database，对于参数数据源ID中选中的数据库连接；
* ars：查询结果的数据集
```
it = it(ars.getValues())
while it.hasNext()
	rowValues = it.next()
	...
end while
```
如果没有返回值，则直接返回原数据集，可以返回Collection或二维数组作为结果数据，但是列数及列序必须与原数据集保持一致。

Tac语法参见[Tac语法](help.html?helpFile=help/SN-CMC/Tac/Exectac.md)