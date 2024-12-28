# 缓存

|日期|作者|备注|
|------|------|------|
|2018-09-25|冀小雷|初稿|

## 缓存方式说明

- 软引用本地缓存
	- 配置类：起始时从配置文件中读取数据，提供监听处理数据更新。如：界面定义、字典、码表定义等
- 集中缓存+软引用本地缓存
	- 少量的数据库数据：起始时从数据库读取数据，通过时间戳判断是否从集中缓存中再次获取，更新数据时集中缓存中更新时间戳
如：权限、码表
	- 大数据需要整体缓存的数据：单独的主数据系统维护
如：客户、商品
- 弱引用本地缓存：类+方法的单例模式，需要大量实例时减少对象构建。

## 缓存说明

数据缓存（KVCacheManage：根据Group获取对应的KVCache）
- 内存缓存：使用软、弱连接实现：ReferenceKVCache
- Redis缓存：使用Redis服务及对应的客户端进行实现：RedisKVCache、SpringRedisKVCache

针对经常使用的配置数据，底层提供标准缓存机制：
- 缓存的设置、缓存的清空；
- 数据库缓存、ZK文件缓存；

## 数据库缓存

数据库缓存注意事项：
- 表号[tbldef.tblid]小于20000，或 设置缓存表标识[tbldef.localcache=1]：
  在数据库修改时，底层会自动清空符合缓存规则的缓存数据；
- 使用底层标准的缓存[KVCache]且group为表数据源；
- 数据库文件：
  cachekcols：缓存key涉及到的列
  cachekeym：缓存key的规则(PATTERN)

示例：
```

/*
 * SQL语句缓存：
 * group = 缓存表对应的数据源
 * key = SQLDATA.[tblname]|SQL[SqlValues]
 * value = SqlData
 */
try(Database db = DBUtils.newDatabaseByTable("tblname",true))
{
	String sql = "select xxx from tblname where xxx order by xx";
	`Object[][] values = db.queryCacheableValues(sql);`
	...
}

/*
 * 应用逻辑缓存：
 * group = 缓存表对应的数据源
 * `key = SQL.[tblname1][tblname2]|PATTERN`
 * value = Object
 */
KVCacheUtils.cache(dataSource,key,()->{
	...
	return Object;
});

```

## 缓存规则配置

1. 功能原因：底层标准缓存配置的缓存通知过于频繁且颗粒度过大；
1. 功能目标：缓存触发机制自定义：
	* 根据需要进行缓存通知；
	* 颗粒度尽可能细；
1. 缓存规则配置请参见对应的Schema文件【[CacheConfigs.xsd](do/BrowseSchema?schema=CacheConfigs.xsd)】；
1. 功能菜单【[缓存规则配置查询](uiinvoke/00/zh_CN/theme0/90.RuleCache.html)】；
1. Demo
```xml
<!--一对一关系-->
<CacheConfig tblname="busheet">
	<Ones>
		<RuleKey rule="{ops:'-',ups:'name,tblname,innerfld,outerfld,voclass,acsbean'}" key="SQL.[busheet]|${sheetcode}" remark="单据对象" />
		<RuleKey rule="{ops:'+-',ups:'name'}" key="SQLDATA.[busheet]|*from busheet*" remark="单据码表" />
		<RuleKey rule="{ops:'+-'}" key="SQL-All-Keys.[busheet]|All-" remark="数据库配置所有单据号" />
	</Ones>
</CacheConfig>
<!--多对多关系-->
<CacheConfig tblname="users">
	<Many>
		<RuleKey rule="{ops:'+-',ups:'username,limbcode'}" key="SQL_ArrayCodeValueMap.[users]|*from users*${cuicode}*" remark="用户码表" />
		<RuleKey rule="{ops:'+-',ups:'wcode'}" key="SQL-WU.[users]|${cuicode}" remark="人员用户关系" />
		<RuleKey rule="{ops:'+-',ups:'wcode,limbcode,ccode,vfmobile,vfemail'}" key="SQL-UW.[users]|${cuicode}" remark="用户人员关系" />
	</Many>
</CacheConfig>
```
1. 设计
@startuml
Title 缓存配置规则类图

RuleCache <-- RuleCacheImpl

RuleCacheVO <.left. RuleCache

interface RuleCache {
	+ void append(Database db, VOClassInfo info, Collection<VO> vos);
	+ QueryResults<RuleCacheVO> queryRuleCaches(RuleCacheParams params);
}

class RuleCacheImpl {
}

class RuleCacheVO {
	+ String tblname;
	+ String rule;
	+ String keytbl;
	+ String key;
	+ String beanmtd;
	+ String macrotac;
	+ String remark;
	+ String xmlfile;
}
@enduml

## 文件缓存

```
// 文件缓存
return KVCacheUtils.cache(FileValueKey, sheetcode, () -> {
	return loadStatusValues(userSession, sheetcode);
});
```


## 数据库+文件缓存

```
/*
 * 单文件单对象：以单据定义为例
 */
public BusiObject getBusiObject(String sheetCode)
{
	String group = AppContext.getUserSession(true).getConfigDataSource(true);
	String key = "SQL.[busheet]|" + sheetCode;
	// 数据库加载并缓存
	BusiObject busiObject = KVCacheUtils.cache(group, key, () -> {
		DAO<BusiObject> dao = DefaultDAO.newInstance(BusiObject.class);
		return dao.queryByID(sheetCode);
	});
	// 文件装载并缓存（配置文件系统已经自行缓存，应用层不需要再次缓存）
	if (busiObject == null)
	{
		String location = "res/sheet/" + sheetCode;
		busiObject = ConfigResourceFactory.impl.loadConfig(location, "xml", BusiObject.class);
		if (busiObject != null)
		{
			busiObject.setSheetcode(sheetCode);
		}
	}
	return busiObject;
}

/*
 * 多个文件作为码表并结合数据库数据：以单据码表为例(snsoft.dx.codedata.UniteCodeDataLoader.UniteCodeData#getCodeData())
 */
String dataSource = def.getDataSource();
String key = AbsDatabase.buildSqlCacheKey(sql);
return KVCacheUtils.cache(dataSource, key, () -> {
	zkPath(dataSource, key);
	loadDBValue();
	loadFileValue();
	return cacheValue();
});
```


## KVCache

KVCache是底层提供的标准缓存容器，目前底层实现了ReferenceKVCache和RedisKVCache。
ReferenceKVCache：默认的缓存容器。
RedisKVCache：Redis缓存容器。当缓存group使用redis:{redisServer}时启用。目前不建议使用。
SpringRedisKVCache：Redis缓存容器。缓存group支持两种：
springredis:{redis对应的Springbean}时启用，只支持数据缓存，不支持对象缓存。
springredisobj:{redis对应的Springbean}时启用，支持数据+对象缓存。

注意：使用redis缓存数据时，数据改变底层不会自动清理，需要自己处理。

使用redis:{redisServer}时启用。目前不建议使用。

KVCache提供的原生方法示例：
```
//为数据库数据缓存的key定义的格式为：`SQL.[table1][table2]|{缓存数据分组id}`
//这样定义的key，可以在数据库db更新时，自动做缓存数据更新。
`String key = "SQL.[uictrl][uictrlopt][uictrlval]|" + uicode;`
KVCache<String,Object> kvCache = KVCache.factory.getKVCache(dataSource, true);
synchronized (key)
{
	List list = (List) kvCache.get(key);
	if (list == null)
	{
		list = new ArrayList();
		...
		kvCache.put(key, list);
	}

```

当开发人员有缓存数据库中数据的场景时，不推荐直接使用KVCache原生方法。而使用DataBase中缓存查询的方法。

## KVCacheUtils

缓存KVCache的工具类，开发人员只需要关注 cache(Object group,String key,Supplier<V> supplier) 方法。
当缓存对象不是从数据库中加载时，可以使用该方法。
使用示例如下：
```
    KVCacheUtils.cache(group, key, () -> {
		URL[] resources = ResourceUtils.getResources(null, "snsoft/ui/uimodel", "*.xml");
		Map<String,String> models = new HashMap<>();
		for (URL resource : resources)
		{
			String id = ResourceUtils.getFileName(resource, 1);
			String title = XMLUtils.getQValue(resource, "Title");
			models.put(id, title);
		}
		return models;
	});
```

## 缓存规范

- 数据库中数据的缓存
group：dataSource
key：SQL.[table1][table2]|{缓存数据分组id}

- redis缓存
groud：
1. springredis:{redis对应的Springbean}
1. springredisobj:{redis对应的Springbean}
key：模块.缓存对象类型|缓存数据分组id

- 其他缓存
group：系统号
key：模块.缓存对象类型|缓存数据分组id
