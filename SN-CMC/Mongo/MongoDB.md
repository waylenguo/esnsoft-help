## MongoDB(MDB)

|日期|作者|备注|
|------|------|------|
|2018-10-15|吕希兵|初稿|
|2019-10-11|吕希兵|优化|

### 指南

由C++编写，基于分布式文件存储的开源数据库

#### 概念说明

文档主要说明：
* Mongo shell的功能说明
* N9B中支持的MongoDB数据查询页面 [MongoDB工具](uiinvoke/00/zh_CN/theme0/90.EXECMongo.html)
* Mongo在java中使用说明

#### 使用说明

**Mongo Shell**

```
use xxx;切换到database xxx
db.collection.find({"name":"Scar"});查询name为Scar的数据
db.collection.insert(
    {
        name:"Scarlett",
        codes:["a","b"],
        size:[{h:180,w:80},{h:190,w:77}]
    }
);插入数据
db.collection.deleteMany({"name":"Scar"});删除name为Scar的数据
db.collection.update(
    {uid:"001"},
    {name:"anna"},
    {upsert:true,multi:true}
);把uid为001的数据中的name更新成anna
更多使用查看手册
```

**MongoDB工具页面语法**

```
find:'applog',
filter:{'level':'ERROR'}
该查询类似于SQL->select * from applog where level='ERROR'
该功能暂不支持增删改，更多用法查看手册
```

**Java中用法**

* N9B中支持的MGDatabase,推荐使用

```
mdb.query(QueryParams,Class<T>)
mdb.insert(T)
mdb.update(T)
mdb.delete(T)
更多查看snsoft.dx.mdb.MGDatabase的类注释
```

* MongoDB原生的Template，只做参考

```
template.insert(T,tableName)
template.query(Query,Class<T>,tableName)
template.remove(T,tableName)
template.updateMulti(Query,Update,Class<T>,tableName)
更多详细介绍查看手册
```

### 手册

#### MongoDB工具

参见[官方文档](https://docs.mongodb.com/v3.6/reference/command/)

##### 查询

```
find:'dcl_decl',//表名
projection:{decl_no:1,rx_filling_date:1},//显示列，
filter:{'trade_country_code':'840'},//条件,多个逗号隔开
skip:100,//跳过xx条，主要用于分页
limit:100,//限制查询条数，主要用于分页
sort:{"rx_filling_date":-1}//排序，1为正序，-1为倒序，多个逗号隔开
```

* 显示列属性说明

```
默认全显示，该属性配置后只显示{}中值为1的列
_id是默认显示列，不需要的话需要手动置0
```

* 条件属性说明

```
$eq		等于，一般不使用，可直接{'code':'aa'}
$gt		大于
$gte	大于等于，{'age':{$gte:10}}
$in		in条件
$nin	not in条件,{'code':{$nin:['aa','bb']}}
$lt		小于
$lte	小于等于，类似大于等于
$ne		不等于，{'code':{$ne:'aa'}}
多个条件逗号隔开{'code':'aa','code':{$nin:['aa','bb']}}
模糊匹配：'message' : { '$regex' : '.*^李.*'} 李字开头
模糊匹配：'message' : { '$regex' : '.*.李.*'} 模糊匹配李 
```
##### 计数

```
count:'dcl_decl',//需要计数的表名
query:{'trade_country_code':'840'},//条件
skip:0,
limit:100
```

##### 去重

```
distinct:'dcl_decl',//表名
key:'trade_country_code',//需要去重的列
query:{}//条件
```

##### 聚合

```
aggregate:'dcl_decl',//表名
pipeline://属性
[
	{$unwind:'$goods'},//按列拆分,参照聚合unwind属性说明
	{$match:{'trade_country_code':{$in:['840','152']}}},//条件，多个逗号隔开
	{$group:{_id:{'ctry_code':'$trade_country_code'},cnt:{$sum:1}}},//分组聚合属性列
	{$project:{trade_country_code:1,cnt:1}},//显示列
	{$sort:{'_id.ctry_code':1}},
	{$skip:0},
	{$limit:100}
],
explain:false//为true时会打印执行这个聚合查询时的一些参数，不会返回具体结果，默认false
```

* 聚合unwind属性说明

```
按列拆分，通常是子表，$不能省略
{
	"_id":ObjectId("5b1f67b785af2f11147a91d6"),
	"name":"Scarlett",
	"list":[
				{"code":"a0001",name:"WTF001"},
				{"code":"a0002",name:"WTF002"}
	]
}
{$unwind:'$list'}之后：
{"_id":ObjectId("5b1f67b785af2f11147a91d6"),"name":"Scarlett","list":[
	{"code":"a0001",name:"WTF001"}
]},
{"_id":ObjectId("5b1f67b785af2f11147a91d6"),"name":"Scarlett","list":[
	{"code":"a0002",name:"WTF002"}
]}
```

* 组聚合列属性说明

```
{$group:{_id:{'ctry_code':'$trade_country_code'},cnt:{$sum:1}}}
_id中放的是分组列，cnt是聚合列
分组列中ctry_code是别名，或者这么写_id:{'$trade_country_code'}意思是没有别名，默认别名为_id
聚合列中cnt同时也是聚合列的别名，可以自定义,
常用聚合函数
$avg			求平均值
$first			取第一个值
$last			取最后一个值
$max			取最大值
$min			取最小值
$sum			求和
```

##### 分组

```
group:
{
	ns:'dcl_decl',//表名
	key:{trade_country_code:1,org_code:1},//显示列
	initial:{total:0},//返回结果前执行的函数，该写法意义是将total置成0
	$reduce:'function(curr,result){result.total+=1}',//返回的结果执行该js脚本,注意不能省略$
	cond:{'rx_filling_date':{$gt:{$date:'2017-10-01T00:00:00.000Z'}},'trade_country_code':{$ne:null},'org_code':{$ne:null}}//条件列
}
```

##### MapReduce

```
功能说明，将大批量的工作（数据）分解（MAP）执行，然后再将结果合并成最终结果（REDUCE）
mapReduce:'dcl_decl',//表名
map:'function(){emit({trade_country_code:this.trade_country_code,org_code:this.org_code},1)}',
reduce:'function(key,values){return Array.sum(values)}',
out:'result',//统计结果存放的集合名 (不指定则使用临时集合,在客户端断开后自动删除)。
query:{'rx_filling_date':{$gt:{$date:'2017-10-01T00:00:00.000Z'}},'trade_country_code':{$ne:null},'org_code':{$ne:null}},//条件
sort:{trade_country_code:1,org_code:1},
limit:100
```

* MapReduce映射函数map说明

```
映射函数 (生成键值对序列,作为 reduce 函数参数)。
简单用法：
map:function(){emit(this.code,this.amount)}		emit可以理解为循环函数
含义：返回一组键值对，键是表的code字段，值是表的amount字段。可以理解为以code分组，去求amount的和
MapReduce中的用法:
map:'function(){emit({trade_country_code:this.trade_country_code,org_code:this.org_code},1)}'
以表中trade_country_code字段(别名为trade_country_code)和org_code字段(别名为org_code)分组，求每组的数据量
```

* MapReduce统计函数reduce说明

```
统计函数，reduce函数的任务就是将key-values变成key-value，也就是把values数组变成一个单一的值value
reduce:'function(key,values){return Array.sum(values)}',
上例意思是说求和
```

* MapReduce统计结果文档说明

```
{
        "result" : "mapresult",
        "timeMillis" : 184,
        "counts" : {
                "input" : 2,
                "emit" : 2,
                "reduce" : 0,
                "output" : 2
        },
        "ok" : 1
}
result:存储结果的集合名
timeMillis:执行花费的时间,单位毫秒
input:满足条件被发送map函数的文档个数
emit：在map函数中emit被调用的次数
output:结果集合中的文档个数
ok:是否成功，成功为1
err:如果失败打印错误信息

这个结果是返回的执行参数，具体的结果需要到result参数的集合中查询
```

参考：<https://docs.mongodb.com/manual/reference/command/nav-aggregation/>

##### 语法对比

MongoDB查询工具中，查询语句与关系数据库Sql对比，方便实施人员学习。

* 查询全部

```
Sql:select * from applog
MongoDB:find:'applog'
```

* 查询带过滤条件

```
Sql:select * from applog where level='ERROR'
MongoDB:
	find:'applog',
	filter:{'level':'ERROR'}
```

* 查询指定列

```
Sql:select username,logtime,level,message from applog where level='ERROR'
MongoDB:
	find:'applog',
	projection:{username:1,logtime:1,level:1,logger:1,message:1},
	filter:{'level':'ERROR'}
```

* 统计

```
Sql:select count(*) from applog where level='ERROR'
MongoDB:
	count:'applog',
	query:{'level':'WARN'}
```

#### MGDatabase

底层封装的MDB工具类snsoft.dx.mdb.MGDatabase，**推荐使用**

```
{
	@Bean
	public MongoTemplate mongoTemplate()
	{
		//System.properties中配置属性SN-xxx.Comm.mongo，值是MDB地址
		return snsoft.commons.spring.SourceConfig.mongoTemplate("SN-xxx.Comm.mongo");
	}

	@Bean(name = "SN-xxx.xxxMongoDatabase")
	public MGDatabase<T> mongoDatabase()
	{
		MongoTemplate template = mongoTemplate();
		return template == null ? null : new MGDatabaseImpl<T>(template);
	}
}
```
具体方法请查看类MGDatabase说明注释

#### MongoTempleta

指的是MDB在Spring-data-mongo中的应用，不建议使用，只供参考，请使用 #MGDatabase

##### 创建
```
//地址的写法，前缀固定，ip是127.0.0.1，端口号27017，database是N9B
//启用安全验证，username代表用户名，password代表密码[可选属性]
//启用集群的话，replicaSet是集群名[可选属性]
uri = "mongodb://username:password@127.0.0.1:27017/N9B?replicaSet=sinolink"

//连接MDB的一些属性,可选属性，无则走默认配置
MongoClientOptions.Builder builder = new MongoClientOptions.Builder();
builder.maxConnectionIdleTime(300000);
builder.connectionsPerHost(100);
builder.socketKeepAlive(true);

com.mongodb.MongoClientURI clientURI = new MongoClientURI(uri[,builder]);
org.springframework.data.mongodb.core.SimpleMongoDbFactory mdbFactory = new SimpleMongoDbFactory(clientURI);
//mapCoverter中可以加一些将取过来的文档进行类型转换或者格式化之类的操作，需要用的话可参考SourceConfig
org.springframework.data.mongodb.core.MongoTemplate.MongoTemplate template = new MongoTemplate(mdbFactory[, mapConverter]);
```

* 连接MDB属性

```
maxConnectionIdleTime最大闲置时间
maxConnectionLifeTime最大生存时间
maxConnectionsPerHost最大连接数
minConnectionsPerHost最小连接数
maxWaitTime最大等待可用连接的时间
connectTimeout连接超时时间
requiredReplicaSetName为集群设置所需的副本集名称
更多属性参照com.mongodb.MongoClientOptions.Builder
```

##### 使用

* 增

```
Model m = new Model();
m.setCode("a001");
m.setName("Anna");
template.insert(m,"model");
```

//VO:可传入实体类，若实体类上有配置注解@Document(collection = "tableName")，可不传表名，不建议
template.insert(VO[, tableName])

* 查

```
Query q = new Query(new Criteria("name").is("Anna"))
template.find(q,Model.class,"model");
查询model中name等于Anna的数据

Criteria c1 = Criteria.orOperator(new Criteria("name").is("Anna"),new Criteria("name").is("Scarlett"));
Query q = new Query(Criteria.andOperator(c1,new Criteria("age"),gte(10)))
template.find(q,Model.class,"model");
查询model中name等于Anna或者Scarlett 同时age大于等于10的数据
```

```
template.find(Query,class[,tableName])
template.findOne(Query,class[,tableName])//只返回第一条数据
template.exists(Query,class[,tableName])//是否存在，返回值boolean
template.findAll(class[,tableName])//查询所有数据
template.findById(Object Id,class[,tableName])//根据ObjectId查询

Query 参照 #Query说明
class 一般情况下是实体类接，也可以用Map,
tableName:实体类配置注解@Document(collection = "tableName")的情况下可以省略，但不建议
```

*  Query说明
```
new org.springframework.data.mongodb.core.query.Query(CriteriaDefinition criteriaDefinition)
简单用法
new Query(new Criteria(key).is(value))
多个条件
new Query(List<Criteria>)
包含and,or的情况
Criteria.andOperator(List<Creteria>)
Criteria.orOperator(List<Creteria>) //返回值均是Criteria，可以嵌套
```

* Critera用法
```
new org.springframework.data.mongodb.core.query.Criteria.Criteria(String key);//key是字段名
Criteria c = new Criteria(<field>);
c.is(value);//等于value
c.ne(value);//不等于
c.lt(value);//小于
c.lte(value);//小于等于
c.gt(value);//大于
c.gte(value);//大于等于
c.in(Collection);//在值中
c.nin(Collection);//不在值中
c.regex(Collection);//正则匹配，多用于模糊匹配，regex("^A001$", "i"))第二个属性i表示忽略大小写
```

* 删

```
Model m = template.findById("",Model.class,"model");
template.remove(m);
```

//根据查询条件删除内容
template.remove(Query,class[,tableName])
//直接传入实体类，必须有_id
template.remove(Object[,tableName])

* 改

```
Query q = new Query(new Criteria("name").is("Anna"));
Update u = new Update();
u.set("code","a002");
u.inc("age",10);
template.updateMulti(q,u,Model.class,"model");
将model表中name等于Anna的数据，更新code为a002，age加10
```

template.updateFirst(Query,Update,class[,tableName])//修改符合条件的第一条数据
template.updateMulti(Query,Update,class[,tableName])//修改多条数据
Query 参照 #Query说明
Update 参照 #Upadte说明

**Upadte说明**
```
org.springframework.data.mongodb.core.query.Update u = new Update();
u.set(key,value);//设置键的值
u.unset(key,value);//删除键
u.inc(key,value);//给数值键增加相应的值
u.push(key,value);//数组中添加值
。。。
更多属性参照org.springframework.data.monogdb.core.query.Update 
```

* 索引相关

```
Index index = new Index();
index.on("code", Direction.ASC).unique().named("code_01");
template.indexOps("model").ensureIndex(index);
在表model上创建code升序的唯一性索引，索引名为code_01

```
IndexOperations o = template.indexOps(tableName)
IndexOperations o = template.indexOps(className)

o.ensureIndex(IndexDefinition) //创建索引
o.dropIndex(name) //根据索引名删除索引
o.dropAllIndexes() //删除所有索引，默认的_id索引不会删除
o.getIndexInfo() //获取该表的索引信息

**IndexDefinition写法**

```
Index index = new Index();
index.on("name", org.springframework.data.domain.Sort.Direction.ASC).unique().named(name);
on第一个参数：字段名，第二个参数升序或者降序
unique():是否为唯一性所以
named(name):给索引起名，无则默认字段名下划线加排序顺序
```

* aggregate分组聚合

```
List<AggregationOperation> ops = new ArrayList<>();
ops.add(Aggregation.match(Criteria.where("code").is("a001")));
ops.add(Aggregation.project("code","name","age"));
ops.add(Aggregation.unwind("$list"));
ops.add(Aggregation.group("$list"));
ops.add(Aggregation.group("level").count().as("totals"));
ops.add(Aggregation.sort(Direction.DESC, "_id"));
ops.add(Aggregation.skip(100));
ops.add(Aggregation.limit(100));
Aggregation aggregation = Aggregation.newAggregation(ops);
AggregationResults<AggSum> result = template.aggregate(aggregation, tableName, AggSum.class);
List<AggSum> = result.getMappedResults();
```

具体属性的含义参照 #分组聚合aggregate

* MapReduce使用

template.mapReduce([Query query,] String inputCollectionName, String mapFunction,String reduceFunction[, MapReduceOptions mapReduceOptions], Class<T> entityClass)

```
String map = "function() {emit(this.author,1);}";
String reduce = "function(key,values) {
	var sum = 0;
	for (var i = 0; i < values.length; i++)
        sum += values[i];
    return sum;} "

MapReduceOptions options = MapReduceOptions.options();
options.outputCollection("Article_MapReduce");
options.outputTypeReduce();
options.finalizeFunction("classpath:finalize.js");
MapReduceResults<ValueObject> reduceResults =
mongoTemplate.mapReduce("article_info",  "classpath:map.js",                "classpath:reduce.js", options, ValueObject.class);//用脚本文件的方式
 mongoTemplate.mapReduce("article_info",  map,reduce, options, ValueObject.class);
reduceResults.forEach(System.out::println);

```

具体参数说明 参见 #Mongo shell MapReduce

* executeCommand

对于复杂的处理，或者无法用以上的方法实现的，可以用这个方法，拼脚本语言去实现

**find格式**

```
{
   "find": <collection>,//表名
   "filter": <document>,//条件
   "sort": <document>,//排序
   "projection": <document>,//显示列
   "skip": <int>,//跳过
   "limit": <int>//限制
}
更多属性参见 <https://docs.mongodb.com/manual/reference/command/find/>
```

**insert格式**

```
{
   insert: <collection>,//表名
   documents: [ <document>, <document>, <document>, ... ]//数据
}
更多属性参见<https://docs.mongodb.com/manual/reference/command/insert/>
```

**delete格式**

```
{
   delete: <collection>,//表名
   deletes: [
      { q : <query>, limit : <integer>},//q条件，limit限制
      { q : <query>, limit : <integer>},
      { q : <query>, limit : <integer>},
      ...
   ]
}
更多属性参见<https://docs.mongodb.com/manual/reference/command/delete/>
```

**update格式**

```
{
   update: <collection>,//表名
   updates: [
      { q: <query>, u: <update>, upsert: <boolean>, multi: <boolean>,//q：条件；u:更新；upsert:无则新增；multi:多条
        collation: <document>, arrayFilters: <array> },
      { q: <query>, u: <update>, upsert: <boolean>, multi: <boolean>,
        collation: <document>, arrayFilters: <array> },
      { q: <query>, u: <update>, upsert: <boolean>, multi: <boolean>,
        collation: <document>, arrayFilters: <array> },
      ...
   ]
}
更多属性参见<https://docs.mongodb.com/manual/reference/command/update/>
```

**aggregate格式**

```

{
  aggregate: "<collection>" || 1,//表名
  pipeline: [ <stage>, <...> ],//管道
  explain: <boolean>//描述
}

count格式
{
  count: <collection or view>,//表名
  query: <document>,//条件
  limit: <integer>,//限制
  skip: <integer>//跳过
}

distinct格式
{
  distinct: "<collection>",//表名
  key: "<field>",//列
  query: <query>//条件
}
更多属性参见<https://docs.mongodb.com/manual/reference/command/nav-aggregation/>
```

**用法实例**

```
DBObject update = new BasicDBObject();
update.put("q", new BasicDBObject("code", "a001"));
update.put("u", new BasicDBObject("$set", new BasicDBObject("model03.$[ele].name03", "aa")));
update.put("multi", true);
DBObject[] array = new DBObject[1];
array[0] = new BasicDBObject("ele.code03", "abc");
update.put("arrayFilters", array);
List<DBObject> updates = new ArrayList<>();
updates.add(update);
BasicDBObject command = new BasicDBObject("update", tableName);
command.append("updates", updates);
System.err.println(command.toJson());
template.executeCommand(command);

实现功能类似db.model.update({"code":"a001"}, {$set: {"model03.$[ele].name03": "ooo"}}, {arrayFilters:[{"ele.code03": "abc"}],multi:true})
```

#### Mongo shell

利用cmd连接mongo后，操作命令

##### 常用命令
```
show dbs:查看所有的database
use xxx:切换到database xxx
db.dropDatabase():切换到对应库之后，执行此命令删除database
show tables/show collections:查看所有的表
db.collection.drop():删除表collection
db.collection.count(<query>):统计符合条件的数据条数，<query> 参见 #查询数据find 的查询条件说明
```

##### 查询find
```
db.collection.find(<query>,<projection>)
db.collection.findOne(<query>,<projection>)
<query>:查询条件，参见 #查询条件写法
<projection>:显示列，该属性配置后只显示{}中值为1的列，默认全显示，_id是默认显示列，不需要的话需要手动置0
db.collection.find({code:"anna"},{_id:0,code:1})
```

* 查询条件写法

```
常用写法{field1: <value>, field2: <value> ...},类似field1=<value> and field2=<value>
默认是and
$or的用法：
{$and:[{field1: <value>}, {field2: <value>},{
	$or:[{field3: <value>}, {field4: <value>}]
	}]}
条件中可以用比较器，参见 #比较器说明
例{code:"aa",age:{$gte:10}}
```

* 比较器说明

|参数|说明|
|------|------|
|$eq|等于，一般不使用，可直接{'code':'aa'}|
|$gt|大于|
|$gte|大于等于，{'age':{$gte:10}}|
|$in|in条件|
|$nin|not in条件,{'code':{$nin:['aa','bb']}}|
|$lt|小于|
|$lte|小于等于，类似大于等于|
|$ne|不等于，{'code':{$ne:'aa'}}|


##### 添加insert
```
db.collection.insertOne(
	{
		name:"Scarlett",
		codes:["a","b"],
		size:[{h:180,w:80},{h:190,w:77}]
	}
)
db.collection.insertMany([<document 1> ,<document 2> ,...])
db.collection.insert()既可以插入多个，也可以插入单个
<document>指一条数据
```

##### 删除delete
```
db.collection.deleteOne(<filter>)删除符合条件的第一条数据
db.collection.deleteMany(<filter>)删除符合条件的所有数据
<filter>写法参照 #查询数据find 的查询条件说明
```

##### 修改update
```
db.collection.update(<query>,<update>,{upsert:<boolean>,multi:<boolean>})
db.collection.updateOne(<query>,<update>,{upsert:<boolean>})
db.collection.updateMany(<query>,<update>,{upsert:<boolean>})
db.collection.save(<document>);有则更新无则添加

<query>：查询参数，写法参照 #查询数据find 的查询条件说明
<update>：要更新的内容，写法参照 #更新参数写法
upsert：默认false，为true的时候，有则更新，无则添加
multi：默认false，为true的时候，更新符合条件的所有数据，否则只更新第一条
<document>:一条数据，若是更新，则必须有内码_id
```

```
db.collection.update(
	{uid:"001"},
	{name:"anna"},
	{upsert:true,multi:true}
)
或者db.collection.update({uid:"001"},{name:"anna"},true,true}
)
```

* 子表文档定位修改器
在需要对数组中的值进行操作的时候，可通过位置或者定位操作符（"$"）.数组是0开始的，可以直接将下标作为键来选择元素。
	db.collection.update({"uid":"001"},{$inc:{"comments.0.size":1}})//更新子表文档第一条
	db.collection.update({"comments.name":"t1"},{$set:{"comments.$.size":1}})//更新子表文档中符合条件的第一条，无法更新多条
	
* 更新多个子表文档
只支持版本3.6+
	db.collection.update({"code":"a001"}, {$set: {"model03.$[ele].name03": "ooo"}}, {arrayFilters:[{"ele.code03": "abc"}],multi:true})
	ele是自定义变量，可随意取
	第一个参数含义：主表code等于a001的数据
	第二个参数含义：更新子表文档符合条件的name03字段为ooo
	第三个参数含义：子表文档code03值为abc的数据才更新
	第四个参数含义：更新多条

* 更新参数写法
参数对的方式，类似{name:"Anna",age:18}
修改器说明：
```
$inc:可以对文档的某个值为数字型（只能为满足要求的数字）的键进行增减的操作
	db.collection.update({"uid" : "201203"},{"$inc":{"size" : 1}})
$set:用来指定一个键并更新键值，若键不存在并创建.对于内嵌文档在使用$set更新时，使用"."连接的方式。
	可省略，即db.collection.update({"uid" : "201203"},{"size" : 1})
$unset:删除键。
	db.collection.update({"uid" : "20120002"},{"$unset":{"num":0}})
	使用修改器$unset时，不论对目标键使用1、0、-1或者具体的字符串等都是可以删除该目标键。
$push:数组修改器,向文档的某个数组类型的键添加一个数组元素，不过滤重复的数据。添加时键存在，要求键值类型必须是数组；键不存在，则创建数组类型的键。
	 db.collection.update({"name" : "toyota"},{$push:{"title":"t1"}})
$ne/$addToSet:类似push，但不会重复
	主要给数组类型键值添加一个元素时，避免在数组中产生重复数据，$ne在有些情况是不通行的。
$pop:从数组的头或者尾删除数组中的元素（0，1是尾部；-1是头部）
	db.collection.update({"name" : "toyota"},{$pop:{"title":1}})
$pull:从数组中删除满足条件的元素
。。。
更多参见官方文档
```

##### 索引index

```
db.collection.createIndex(<key>, <options>)
db.collection.createIndexes([<key1>,<key2>,...],<options>)

<key>的写法：
	db.collection.createIndex({"code":1})	//1:升序;-1:降序
<options>常用参数：
	unique:索引是否唯一；默认false
	name:索引的名称，若未指定，MDB会按字段名加下划线排序顺序生成一个索引
	v:索引权重值，数值1到99999之间，表示该索引相对于其他索引字段的得分权重
	default_language：对于文本索引，该参数决定了停用词及词干和词器的规则的列表，默认英语
	更多属性参照官网
	
db.collection.createIndex({open: 1, close: -1}, {unique: true})
```

##### 分组聚合aggregate

```
db.collection.aggregate(<pipeline>, <options>)
db.orders.aggregate(
						[
							{project:{status:1}},
							{$unwind:{"$list"}},
							{$match: { status: "A" } },
							{$group: { _id: "$cust_id", total: { $sum: "$amount" } } },
							{$limit:100},
							{$skip:10},
							{$sort: { total: -1 } }
						],
						{
							explain: true
						}
					)
```

* 管道说明pipeline

```
$project：显示列，该属性配置后只显示{}中值为1的列，默认全显示
$match：用于过滤数据，参见 #查询数据find 的查询条件说明
$limit：用来限制MDB聚合管道返回的文档数。
$skip：在聚合管道中跳过指定数量的文档，并返回余下的文档。
$unwind：将文档中的某一个数组类型字段拆分成多条，每条包含数组中的一个值。注:该属性必须在group前面 #聚合unwind属性说明 
$group：将集合中的文档分组，可用于统计结果。 #分组聚合列属性说明
$sort：将输入文档排序后输出。
。。。
更多参见官方文档
```

* 聚合unwind属性说明

```
按列拆分，通常是子表，$不能省略
{
	"_id":ObjectId("5b1f67b785af2f11147a91d6"),
	"name":"Scarlett",
	"list":[
				{"code":"a0001",name:"WTF001"},
				{"code":"a0002",name:"WTF002"}
	]
}
{$unwind:'$list'}之后：
{"_id":ObjectId("5b1f67b785af2f11147a91d6"),"name":"Scarlett","list":[
	{"code":"a0001",name:"WTF001"}
]},
{"_id":ObjectId("5b1f67b785af2f11147a91d6"),"name":"Scarlett","list":[
	{"code":"a0002",name:"WTF002"}
]}
```


*  分组聚合列属性说明

```
{$group:{_id:{'ctry_code':'$trade_country_code'},cnt:{$sum:1}}}
_id中放的是分组列，cnt是聚合列
分组列中ctry_code是别名，或者这么写_id:{'$trade_country_code'}意思是没有别名，默认别名为_id
聚合列中cnt同时也是聚合列的别名，可以自定义,
常用聚合函数
$avg			求平均值
$first			取第一个值
$last			取最后一个值
$max			取最大值
$min			取最小值
$sum			求和
。。。
更多参见官方文档
```

* 属性说明options

```
explain:指定返回有关管道处理的信息，默认false
maxTimeMS:限制处理操作的时间
hint：强制使用索引
。。。
更多参见官方文档
```

##### MapReduce

主要用于大规模数据分析

```
db.collection.mapReduce(map, reduce, {<out>, <query>, <sort>, <limit>})
map:
	映射函数 (生成键值对序列,作为 reduce 函数参数)。
	简单用法：
	function(){emit(this.code,this.amount)}		emit可以理解为循环函数
	含义：返回一组键值对，键是表的code字段，值是表的amount字段。可以理解为以code分组，去求amount的和
	function(){emit({code:this.code,name:name},1)}
	以表中code字段(别名为code)和name字段(别名为name)分组，求每组的数据量
reduce:
	统计函数，reduce函数的任务就是将key-values变成key-value，也就是把values数组变成一个单一的值value
	function(key,values){return Array.sum(values)}:求和
out:'result',//统计结果存放的集合名 (不指定则使用临时集合,在客户端断开后自动删除)。
query:{<filter>},//条件 参见 #查询数据find 的查询条件说明
sort:{code:1,name:1},排序
limit:100

db.collection.mapReduce(
	function(){emit({code:this.code,name:name},1)},
	function(key,values){return Array.sum(values)},
	{
		out:"result",
		query:{"fname":"Anna"},
		sort:{code:1,name:1},
		limit:100
	}
)//以code和name作为分组项，求fname是Anna的100条数据每个分组的数据量，结果输出到result中
```

* MapReduce统计结果文档说明

```
{
        "result" : "mapresult",
        "timeMillis" : 184,
        "counts" : {
                "input" : 2,
                "emit" : 2,
                "reduce" : 0,
                "output" : 2
        },
        "ok" : 1
}
result:存储结果的集合名
timeMillis:执行花费的时间,单位毫秒
input:满足条件被发送map函数的文档个数
emit：在map函数中emit被调用的次数
output:结果集合中的文档个数
ok:是否成功，成功为1
err:如果失败打印错误信息

这个结果是返回的执行参数，具体的结果需要到result参数的集合中查询
```

### 实践

#### MDB 安装与连接

各版本下载链接或者直接在官网下载
http://dl.mongodb.org/dl/win32/x86_64

MDB的一大特点，安装简便，一般直接下zip安装包，然后解压缩就算安装好了

##### MDB启动

```
启动方式1：mongod 后面添加参数的方式，具体参数参见 #常用MDB参数
mongod --dbpath "D:\soft\mongodb\data\db" --logpath "D:\soft\mongodb\Server\3.2\etc\monogdb.log" --auth
启动方式2：配置文件方式，可以将参数都写在文件mongodb.conf中，方便修改
mongod --config D:/soft/mongodb/Server/3.2/etc/mongodb.conf
注：最好添加环境变量xx/bin,方便在cmd中启动连接
```

也可以在window中添加服务的方式启动，添加后每次开机都会自动启动MDB
类似前面的启动方式，后面添加参数--install和服务名--serviceName "xxx"
mongod --config D:/soft/mongodb/Server/3.2/etc/mongodb.conf --install --serviceName "MDB"
注：以上名字在cmd中执行，若未添加环境变量，则需要到对应安装路径的bin文件下执行
** 安装服务时务必【以管理员身份运行】

##### 常用MDB参数
```
1.verbose = true
vvvv = true
启动verbose冗长信息，它的级别有 vv~vvvvv，v越多级别越高，在日志文件中记录的信息越详细。
2.port = 27017
端口。默认27017，MDB的默认服务TCP端口，监听客户端连接。要是端口设置小于1024，比如1021，则需要root权限启动
一台电脑可安装多个MDB，port参数设置不同即可，可用来本地测试副本集，主从服务等。
3.maxConns = 100
最大连接数。默认值：取决于系统（即的ulimit和文件描述符）限制。MDB中不会限制其自身的连接。当设置大于系统的限制，则无效，以系统限制为准。注意：不能设置该值大于20000。
4.logpath=/var/log/MDB/MDB.log
指定日志文件，该文件将保存所有的日志记录、诊断信息。除非另有指定，mongod将所有的日志信息输出到标准输出。如果没有指定logappend，重启则日志会进行覆盖操作。
5.logappend=true
写日志的模式：设置为true为追加。默认是覆盖。如果未指定此设置，启动时MDB的将覆盖现有的日志文件。
5.1 logRoate=reopen
设置每个日志文件大小，避免单个日志文件过大，该选项必须和--logappend一同使用，可以使rename或者reopen。
6.auth = true
noauth = true
用户认证，默认false。不需要认证。当设置为true时候，进入数据库需要auth验证，
7.dbpath=/var/lib/MDB
数据存放目录。默认：/data/db/
8.directoryperdb = ture
设置为true，修改数据目录存储模式，每个数据库的文件存储在DBPATH指定目录的不同的文件夹中。
9.replSet
使用此设置来配置复制副本集。指定一个副本集名称作为参数，所有主机都必须有相同的名称作为同一个副本集。
10.slavedelay
设置从库同步主库的延迟时间，用于从设置，默认为0。
slavedelay = 60     延迟60s同步主数据
```
更多参数参见：https://docs.mongodb.com/manual/reference/configuration-options/


##### 添加用户权限

安装完成后，启动的时候，不要带安全验证，此时连接进来，执行命令show dbs；会发现只有一个local，这是是正确的，3.x都是这样的，之后可以开始添加用户并设置权限。

* 执行 `use admin`，先在这个库中创建一个全局性的管理员用户，语句为：

```
db.createUser( 
{ 
	user: "dba",  
	pwd: "dba", 
	roles: [ { role: "userAdminAnyDatabase", db:"admin" } ]
	}
)
user:用户名
pwd:密码
roles:权限码，多个是在[]里面逗号接{}
role:权限码
db:数据库名字
```

* 权限码

```
read：允许用户读取指定数据库
readWrite：允许用户读写指定数据库
dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile
userAdmin：允许用户向
system.users集合写入，可以找指定数据库里创建、删除和管理用户 clusterAdmin：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限。
readAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读权限 readWriteAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读写权限
userAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限
dbAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限。
root：只在admin数据库中可用。超级账号，超级权限
```

* 权限码对应的角色

```
1. 数据库用户角色：read、readWrite;
2. 数据库管理角色：dbAdmin、dbOwner、userAdmin；
3. 集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager；
4. 备份恢复角色：backup、restore；
5. 所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase
6. 超级用户角色：root // 这里还有几个角色间接或直接提供了系统超级用户的访问（dbOwner 、userAdmin、userAdminAnyDatabase）
7. 内部角色：__system
```

* 需要注意事项：

```
1.首先需要在admin里面创建一个userAdminAnyDatabase用户，用来创建用户，此用户可以在任何库里登录
2.在需要创建用户的库里登录该账户，例：我在本地use sinolink，然后创建了一个readWrite权限的用户sinolink；
特别注意： #必须在相应库里创建相应的用户，哪里创建哪里认证。
```
##### MDB连接
cmd执行mongo命令，即可连接到本地MDB，同时支持启动参数

|参数|说明|
|------|------|
|--host|服务ip，一般配合port实现远程连接|
|--port|端口号|
|-h[--help]|mongo的帮助|
|--version|打印当前MDB的版本|
|-u[--username]|启用安全验证后的用户名|
|-p[--password]|启用安全验证后的密码|


#### 数据导入导出

##### 数据导出

数据导出命令mongoexport
```
mongoexport -d MyDb -c TestCreateTime -q '{age:{"$gte":20}}' -o MyDb.dat
```
常用参数说明

|参数|说明|
|------|------|
|-h[--host]|主机ip|
|-port|端口|
|-u[--username]|用户名|
|-p[--password]|密码|
|-d[--db]|db[database]|
|-c[--collection]|表名[collection]|
|-f[--fields]|列，指定导出某些列|
|-q[--query]|过滤条件|
|--csv|输出csv格式的文件，默认是json格式|
|-o[--out]|输出文件的路径[dat,json,csv]|
|--limit|限制导出数据的条数|
|--skip|跳过xx条数据开始导出|
|--sort|排序|
|--help|显示帮助信息|

##### 数据导入
数据导入命令mongoimport
```
mongoimport -d MyDb -c TestCreateTime --type csv --headerline --file MyDb.csv
```

常用参数说明

```
--help,-h,--port,-u,-p,-d,-c 与导出类似
```
|参数|说明|
|------|------|
|-f[--fields]|列，指定导出某些列|
|-type|导入文件格式，json,csv,tsv,默认json|
|--file|导入的文件，带路径|
|--drop|是否先删除表中的数据|
|--upsert|如果已经存在，则插入或者更新对象|
|--headerline|csv/tsv文件第一行是头|

#### 数据备份恢复

##### 数据备份
数据备份命令mongodump
mongodump -d MyDb -c TestCreateTime
```
--help,-h,--port,-u,-p,-d,-c,-o,-q与导出类似
--repair	尝试恢复crashed的数据库
```
##### 数据恢复
数据恢复命令mongorestore
mongorestore -d MyDb ./dump/MyDb/
参数参见	#数据导入

#### MDB副本集

准备三个MDB，配好配置文件，可以在一台机子上装三个，将port指向不同端口即可
启动项中添加属性	replSet = xxx/127.0.0.1:37017
//xxx是副本集名称，同一个副本集名称得相同，/后指向另一副本的ip和端口号

```
副本1 ：--replSet snsoft/127.0.0.1:3333
副本2 ： --replSet snsoft/127.0.0.1:2222
```

##### 初始化副本集

任意进入其中一个节点，必须进入admin库
db.runCommand({"replSetInitiate":{"_id":"snsoft", "members":[{"_id":1, "host":"127.0.0.1:2222"}, {"_id":2, "host":"127.0.0.1:3333"}]}});
初始化完成后，就出现了主节点和副本结点，这个类似于普通主从，主节点CURD数据，从节点不能修改数据。

##### 设置仲裁节点

可以不设置
通常副本集的个数设置为奇数个，这样便于选出主节点，但是如果剩下了偶数个结点，由于存在选举不超过半数的的情况，这样就会打架。仲裁结点就是解决这种问题的。

仲裁节点配置--replSet snsoft/127.0.0.1:2222
然后把自己作为仲裁结点加入snsoft副本集群中：(在2222的admin中执行)
rs.addArb("127.0.0.1:4444")

查看集群状态：

```
snsoft:PRIMARY> rs.status();
{
        "set" : "snsoft",
        "date" : ISODate("2016-06-14T13:05:56Z"),
        "myState" : 1,
        "members" : [
                {
                        "_id" : 1,
                        "name" : "127.0.0.1:2222",
                        "health" : 1,
                        "state" : 1,
                        "stateStr" : "PRIMARY",
                        "uptime" : 332,
                        "optime" : Timestamp(1465909505, 1),
                        "optimeDate" : ISODate("2016-06-14T13:05:05Z"),
                        "electionTime" : Timestamp(1465909307, 1),
                        "electionDate" : ISODate("2016-06-14T13:01:47Z"),
                        "self" : true
                },
                {
                        "_id" : 2,
                        "name" : "127.0.0.1:3333",
                        "health" : 1,
                        "state" : 2,
                        "stateStr" : "SECONDARY",
                        "uptime" : 255,
                        "optime" : Timestamp(1465909505, 1),
                        "optimeDate" : ISODate("2016-06-14T13:05:05Z"),
                        "lastHeartbeat" : ISODate("2016-06-14T13:05:55Z"),
                        "lastHeartbeatRecv" : ISODate("2016-06-14T13:05:55Z"),
                        "pingMs" : 0,
                        "syncingTo" : "127.0.0.1:2222"
                },
                {
                        "_id" : 3,
                        "name" : "127.0.0.1:4444",
                        "health" : 1,
                        "state" : 7,
                        "stateStr" : "ARBITER",
                        "uptime" : 51,
                        "lastHeartbeat" : ISODate("2016-06-14T13:05:55Z"),
                        "lastHeartbeatRecv" : ISODate("2016-06-14T13:05:56Z"),
                        "pingMs" : 0
                }
        ],
        "ok" : 1
}
```

##### 设置副本可读

副本结点不具有读文档的权限，需要设置slaveOk=true才行
db.getMongo().setSlaveOk();
