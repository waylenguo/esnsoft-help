## Redis组件

|日期|作者|备注|
|------|------|------|
|2018-12-11|宋建中|初稿|

### 封装组件

底层平台为了更方便的使用Redis的相关功能，对基础应用做了组件的封装。

- List(ListCache):实现List接口；
- Map(MapCache):实现Map接口；
- Set(SetCache):实现Set接口；
- ZSet(ZSetCache):实现可排序的Set；
- K-V(ValueCache):标准的K-V结构；
- 队列(QueueCache/QueueCaches):实现队列；支持逻辑队列内部散列的算法，减轻主线程的压力；支持支持队列后面或前面增加对象；
- 计数器 (CounterCache/CounterCaches):实现计数的功能，一般用于数量的统计；
- 分码器(CodeCache):生成唯一的字符串编码。
- 同步锁(LockCache/LockCaches):服务端统一的服务锁，确保同一时间的唯一操作，注意一般要加有效期，可以增加描述信息；
- 限流器(ThresholdCache/ThresholdCaches):实现令牌桶算法，一般用于限流的控制，确保TPS的阀值；
- 断路器(FuseCache/FuseCaches):断路器的熔断算法，连续X个未成功则返回断路；
- 标记器(MarkCache/MarkCaches):统一的标记信息，可以增加描述信息；

*** 注意事项 *** 
- 数据量非常大的时候不要取全部数据到本地。

### 使用说明
*  增加专有的SourceConfig用于获取CacheFactory，也可以根据需要使用底层提供的默认RedisConfig

```
@Configuration
@PropertySource(value = "classpath:/snsoft/core.inf", ignoreResourceNotFound = true)
public class XXXConfig<T,V extends Serializable> extends SourceConfig
{
	@Bean(name = "XXXXXXXXXX")
	public CacheFactory<V> cacheFactory()
	{
		RedisCacheFactory<V> cacheDataType = new RedisCacheFactory<>();
		RedisTemplate<String,V> template = redisTemplate();
		cacheDataType.setRedisTemplate(template);
		return cacheDataType;
	}
	@Bean(name = "XXXXXXXXXX")
	public RedisTemplate<String,V> redisTemplate()
	{
		return super.redisTemplate("XXXXXXXXXX");
	}	
}
```

*  应用系统中增加一个专门的服务，通过CacheFactory获取需要的Redis组件，每个组件对应明确的一个功能。

```
//接口
public interface XXXCacheService<V extends Serializable>
{
	/**
	 * 获取Redis缓存工厂
	 * @return
	 */
	public CacheFactory<V> getCacheFactory();
	/**
	 * 获取同步锁
	 * @return
	 */
	public LockCaches getLockCaches();	
	/**
	 * 获取限流器
	 * @return
	 */
	public ThresholdCache getThresholdCache();	
}

//实现
@Service(value = "XXXCacheService")
public class XXXCacheServiceImpl<V extends Serializable> implements XXXCacheService<V>
{
	@Resource(name = "XXXXXXXXXX")
	private CacheFactory<V>			cacheFactory;
	private LockCaches lockCaches;
	@Override
	public LockCaches getLockCaches()
	{
		if (this.lockCaches != null)
		{
			return this.lockCaches;
		}
		CacheFactory<V> cacheFactory = getCacheFactory();
		this.lockCaches = cacheFactory.getLockCaches("XXXXXXXXXXXXX");
		return this.lockCaches;
	}	
	private ThresholdCache thresholdCache;
	@Override
	public ThresholdCache getThresholdCache()
	{
		if (this.thresholdCache != null)
		{
			return this.thresholdCache;
		}
		CacheFactory<V> cacheFactory = getCacheFactory();
		this.thresholdCache = cacheFactory.getThresholdCache("XXXXXXXXXXXXX");
		return this.thresholdCache;
	}	
}
```
