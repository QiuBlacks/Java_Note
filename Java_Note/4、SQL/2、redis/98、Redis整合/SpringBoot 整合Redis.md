# SpringBoot 整合Redis

# 一、基础知识

## 1、基本介绍

SpringBoot 操作数据使用的是spring-data       jpa jdbc mongodb redis  

SpringBoot操作Redis有两种中间件：jedis  和  lettuce  

在 SpringBoot2.x 之后，原来使用的jedis 被**替换为了 lettuce**  



**注意：**redis存取对象需要将**对象序列化**

RedisTemplate默认的序列化为JDK自带的JdkSerializationRedisSerializer



## 2、操作Redis的两种

jedis : 采用的直连，多个线程操作的话，是不安全的，如果想要避免不安全的，使用 jedis pool 连接
池！ 更像 BIO 模式

lettuce : 采用netty，实例可以再多个线程中进行共享，不存在线程不安全的情况！可以减少线程数据
了，更像 NIO 模式  





## 3、基本语法

redisTemplate 操作不同的数据类型，api和我们的指令是一样的

- opsForValue 操作字符串 类似String

- opsForList 操作List 类似List

- opsForSet

- opsForHash

- opsForZSet

- opsForGeo

- opsForHyperLogLog

  

  

除了基本的操作，我们常用的方法都可以直接通过redisTemplate操作，比如事务，和基本的CRUD获取redis的连接对象

```
RedisConnection connection =redisTemplate.getConnectionFactory().getConnection();
connection.flushDb();
connection.flushAll();
```





# 二、源码分析

自动配置类：RedisAutoConfiguration

自动配置类绑定的配置文件：RedisProperties

```
@EnableConfigurationProperties(RedisProperties.class)
```



## 1、RedisAutoConfiguration类

默认redisTemplate，我们可以自定义配置

```java
@Bean
// 我们可以自己定义一个redisTemplate来替换这个默认的！
@ConditionalOnMissingBean(name = "redisTemplate")
public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory)
    throws UnknownHostException {
    // 默认的 RedisTemplate 没有过多的设置，redis 对象都是需要序列化！
	// 两个泛型都是 Object, Object 的类型，我们后使用需要强制转换 <String, Object>
    RedisTemplate<Object, Object> template = new RedisTemplate<>(); 
    template.setConnectionFactory(redisConnectionFactory);
    return template;
}

@Bean
@ConditionalOnMissingBean
// 由于 String 是redis中最常使用的类型，所以说单独提出来了一个bean！
public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory)
    throws UnknownHostException {
    StringRedisTemplate template = new StringRedisTemplate();
    template.setConnectionFactory(redisConnectionFactory);
    return template;
}
```



## 2、RedisTemplate类

序列化配置

```
@SuppressWarnings("rawtypes") private @Nullable RedisSerializer keySerializer = null;
@SuppressWarnings("rawtypes") private @Nullable RedisSerializer valueSerializer = null;
@SuppressWarnings("rawtypes") private @Nullable RedisSerializer hashKeySerializer = null;
@SuppressWarnings("rawtypes") private @Nullable RedisSerializer hashValueSerializer = null;
```

默认序列化为JDK自带的JdkSerializationRedisSerializer

```
if (defaultSerializer == null) {

    defaultSerializer = new JdkSerializationRedisSerializer(
    classLoader != null ? classLoader : this.getClass().getClassLoader());
}

```





![image-20200923160312800](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200923160321.png)



# 三、整合测试

## 1、连接测试

### 1.1、导入依赖

```
<!-- 操作redis -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```



### 1.2、配置连接文件

```
# 配置redis
spring.redis.host=127.0.0.1
spring.redis.port=6379
```



### 1.3、测试

/test/java/com.qiu.

跟Jedis差不多

```java
@SpringBootTest
class RedisSpringbootApplicationTests {

    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    void contextLoads() {
        // redisTemplate 操作不同的数据类型，api和我们的指令是一样的
    // opsForValue 操作字符串 类似String
    // opsForList 操作List 类似List
    // opsForSet
    // opsForHash
    // opsForZSet
    // opsForGeo
    // opsForHyperLogLog
    // 除了基本的操作，我们常用的方法都可以直接通过redisTemplate操作，比如事务，和基本的CRUD
        
    // 获取redis的连接对象
    // RedisConnection connection =redisTemplate.getConnectionFactory().getConnection();
    // connection.flushDb();
    // connection.flushAll();

        redisTemplate.opsForValue().set("mykey", "关注狂神说公众号");
        System.out.println(redisTemplate.opsForValue().get("mykey"));
    }

}
```



## 4、序列化测试

### 2.1 定义一个User：注意一定要序列化

```
@Component
@NoArgsConstructor
@AllArgsConstructor
@Data
public class User implements Serializable {

    private String name;
    private int age;
}
```



### 2.2 测试

除了实现Serializable，也可以用fackjson模拟序列化

```java
@SpringBootTest
class RedisSpringbootApplicationTests {

    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    void contextLoads() throws JsonProcessingException {

        User user = new User("qiu",22);

        //可以用fackjson进行序列化
        //String userjson = new ObjectMapper().writeValueAsString(user);

        redisTemplate.opsForValue().set("user",user);
        System.out.println(redisTemplate.opsForValue().get("user"));

    }


}
```



# 四、自定义redisTemplate

## 1、序列化模板

Json序列化配置和String 的序列化

主要对**key, hashkey, value, hashvalue进行序列化定义**

```java
public class RedisConfig {
    
    // 这是我给大家写好的一个固定模板，大家在企业中，拿去就可以直接使用！
    // 自己定义了一个 RedisTemplate
    @Bean
    @SuppressWarnings("all")
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory  factory) {
        
        // 我们为了自己开发方便，一般直接使用 <String, Object>
        RedisTemplate<String, Object> template = new RedisTemplate<String, Object>();
        template.setConnectionFactory(factory);

        // Json序列化配置
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new
                Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        
        // String 的序列化
        StringRedisSerializer stringRedisSerializer = new  StringRedisSerializer();
        
        
        // key采用String的序列化方式
        template.setKeySerializer(stringRedisSerializer);
        // hash的key也采用String的序列化方式
        template.setHashKeySerializer(stringRedisSerializer);
        // value序列化方式采用jackson
        template.setValueSerializer(jackson2JsonRedisSerializer);
        // hash的value序列化方式采用jackson
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        template.afterPropertiesSet();

        return template;

    }
}
```

所有的redis操作，其实对于java开发人员来说，十分的简单，更重要是要去理解redis的思想和每一种数据结构的用处和作用场景！  



## 2、RedisUtil工具类

F:\java\Util\RedisUtil.java

也可以自己定义个工具类，使用跟redis一样的常用Api

```java
@SpringBootTest
class RedisSpringbootApplicationTests {
    
    @Autowired
    private RedisUtil redisUtil;

    @Test
    void contextLoads() throws JsonProcessingException {
        redisUtil.set("user","qiu");
    }
    
}
```







