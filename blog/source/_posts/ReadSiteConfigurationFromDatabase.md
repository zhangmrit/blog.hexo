title: 从数据库读取网站配置
date: 2017-05-09 15:04:13
categories: spring
tags: [JAVA]
---
近期遇到一个算是比较少见的需求，有点搞脑子，如果你也有类似的疑问，那么可以看看这篇文章。需求如下：
- 要经常改变网站的一些配置
- 这些配置除了显示也没什么用
- 通常可能会在全局使用
- 或许是一些分布式的配置
- 需要将配置存放在数据库

**如果你的问题满足上述所述需求全部或者部分，不防阅读一下以下解决策略**
<!-- more -->

因为以前的策略是把配置内容放在`resources` 下的`*.properties` 里面，而且为了省资源，在启动的时候把配置内容都加载到内存里，如果需求合理确实也没什么缺陷，但是最近运营推广上面有投放广告的需求，第三方推广公司对公司审核（某日头条、X60推广等），需要频繁修改网站名和公司名，这使得运维小哥非常恼火，而且重启容器确实不是一个很好的方案。那么，问题来了，这个锅我们服务端得背啊，额……不是，是身先士卒，能者多劳。经过一天的苦思冥想，大概有了下列几个方案：
1. 放在数据库，加载到缓存，再加载到内存，刷新内存是一个问题
2. 放在数据库，加载到缓存，不加载到内存，大量的修改和使用，工程量浩大
3. 动态修改配置文件，需重启或更换读取方式
4. 上下文监听器，可选重启，或者每个链接都刷新（缺点是所有请求都会触发，如图片）
5. 文件监听器，需运维修改
6.	……

上述方案中，或多或少存在一些问题，和目前需求上也不是完全吻合，自然都被pass掉了。最终整理代码，还是想到了一个比较适合目前也可解决未来存在的潜在需求的方案，废话不多少，上方案。

1. 把配置文件放到数据库中，可支持分布式读取
2. 业务层和数据层增加缓存，减少数据库访问
3. 读取配置加载到内存，并设置过期时间
4. JSP中导入工具类，读取就好了

那么问题又来了，工具类是静态的，如果业务层也是分布式，如dubbo等，配置从Service读取，存在一定的麻烦，好在之前就已经遇到过了，前面的文章里也讲过如何处理，看具体代码：
```
	/** 读取配置，放入值内存中 */
    private static final Map<String, String> siteMap    = Maps.newHashMap();

    /** 读取配置，放入读取时间到内存中 */
    private static final Map<String, Long>   timeMap    = Maps.newHashMap();

    /** 超时毫秒数*/
    private static final long                EXPRE_TIME = 5 * 60 * 1000;

    @Autowired
    private SiteConfigService                siteConfigService;

    private static SiteConfig                siteConfig;

    @PostConstruct
    public void init()
    {
        siteConfig = this;
        siteConfig.siteConfigService = this.siteConfigService;
    }

	 /**
     * 根据关键词读取配置
     * @param key 关键词
     * @return
     */
    public static String read(String key)
    {
        Long time = timeMap.get(key);
        if (null == time || expre(time))
        {
            // 刷新
            SiteConfigModel config = siteConfig.siteConfigService.findByKey(key);
            if (null != config)
            {
                String value = config.getValue();
                logger.info("读取了配置:" + key + "=" + value);
                siteMap.put(key, value);
                timeMap.put(key, System.currentTimeMillis());
                return value;
            }
            return "";
        }
        else
        {
            return siteMap.get(key);
        }
    }

	/**
     * 判断是否过期
     * @param time 加入map的时间戳
     * @return
     */
    private static boolean expre(long time)
    {
        if (System.currentTimeMillis() - time > EXPRE_TIME)
        {
            return true;
        }
        return false;
    }
```
前端使用就不讲了，再也不用要求改这改那的，你们爱改啥改啥，有时候，创新只是来源于偷懒！






