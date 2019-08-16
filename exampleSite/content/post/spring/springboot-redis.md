+++
title = "SpringBoot项目使用Redis做缓存"
description = ""
tags = [
    "SpringBoot",
    "Redis"
]
date = "2019-07-12"
categories = [
	"Java"
]
+++
# SpringBoot项目使用Redis做缓存

> 最近打算将blog项目重构，由原来的blade框架搭建的tale博客重构为SpringBoot+Freemarker+MyBatis+mysql的项目，并且将原有项目中自实现的缓存替换为中间件Redis缓存
>
> [项目地址](https://github.com/Heemooo/blog)

## 第一步、引入Redis依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

## 第二部、加入配置

这里版本为spring-boot-starter-data-redis-2.1.6

```properties
# Redis数据库索引（默认为0）
spring.redis.database=1
# Redis服务器地址
spring.redis.host=127.0.0.1
# Redis服务器连接端口
spring.redis.port=6379
# Redis服务器连接密码（默认为空）
spring.redis.password=
# 连接池最大连接数（使用负值表示没有限制）
spring.redis.jedis.pool.max-active=1000
# 连接池最大阻塞等待时间（使用负值表示没有限制）
spring.redis.jedis.pool.max-wait=-1
# 连接池中的最大空闲连接
spring.redis.jedis.pool.max-idle=10
# 连接池中的最小空闲连接
spring.redis.jedis.pool.min-idle=2
# 连接超时时间（毫秒）
spring.redis.timeout=0
```

## 第三步、定义缓存操作接口

```java
public interface RedisCache<K, V> {
    /**
     * 添加缓存
     * @param key   键
     * @param value 值
     */
    void set(K key, V value);

    /**
     * 添加缓存并设置过期时间
     * @param key   键
     * @param value 值
     * @param time  时间（秒）time要大于0 如果time小于等于0 将设置无限期
     */
    void set(K key, V value, long time);

    /**
     * 获取缓存
     * @param key 键
     * @return V 缓存值
     */
    V get(K key);

    /**
     * 递增
     * @param key 键
     * @return 递增后的值
     */
    long incr(K key, long delta);

    /**
     * 递减
     * @param key 键
     * @return 递减后的值
     */
    long decr(K key, long delta);

    /**
     * 移除缓存
     * @param key 键
     */
    boolean remove(K key);

    /**
     * 是否存在key
     * @param key 键
     */
    boolean hasKey(K key);

    /**
     * 指定缓存失效时间
     * @param key  键
     * @param time 时间(秒)
     */
    void expire(K key, long time);

    /**
     * 根据key 获取过期时间
     * @param key 键 不能为null
     * @return 时间(秒) 返回0代表为永久有效
     */
    long getExpire(K key);
}
```

实现字符串操作的缓存

```java
@Component
public class StringCache implements RedisCache<String, String> {
    /**
     * 操作key-value都是字符串
     */
    private final StringRedisTemplate redisTemplate;

    public StringCache(StringRedisTemplate redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

    @Override
    public void set(String key, String value) {
        redisTemplate.opsForValue().set(key, value);
    }

    @Override
    public void set(String key, String value, long time) {
        redisTemplate.opsForValue().set(key, value, time);
    }

    @Override
    public String get(String key) {
        return redisTemplate.opsForValue().get(key);
    }

    @Override
    public void incr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递增因子必须大于0");
        }
        redisTemplate.opsForValue().increment(key, delta);
    }

    @Override
    public void decr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递减因子必须大于0");
        }
        redisTemplate.opsForValue().decrement(key, delta);
    }

    @Override
    public boolean remove(String key) {
        var success = redisTemplate.delete(key);
        return Objects.nonNull(success) ? success : false;
    }

    @Override
    public boolean hasKey(String key) {
        var has = redisTemplate.hasKey(key);
        return Objects.nonNull(has) ? has : false;
    }

    @Override
    public void expire(String key, long time) {
        redisTemplate.expire(key, time, TimeUnit.SECONDS);
    }

    @Override
    public long getExpire(String key) {
        var time = redisTemplate.getExpire(key, TimeUnit.SECONDS);
        return Objects.nonNull(time) ? time : 0;
    }
}
```