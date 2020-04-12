# 一、添加缓存

## 1、使用SpringBoot自带缓存功能

### 1、基本使用

1. 在主类上添加注解**@EnableCaching**

2. 在业务方法上添加**@Cacheable(cacheNames = "xxx")**注解

这样即可实现基础的缓存功能

### 基本流程：

@Cacheable:

1. 方法执行之前，先去查询Cache（缓存组件），按照CacheNames指定的名字获取，（CacheManager先获取相应的缓存），第一次获取缓存如果没有Cache组件会自行创建

2. 去Cache中查找缓存的内容，使用一个key，默认就是方法的参数

   key是按照某种策略生成的，默认hi使用keyGenerator生成的。默认使用SimplekeyGenerator生成key：

   ​			如果有一个参数，key=参数的值

   ​			如果没有参数，key=new SimpleKey();

   ​			如果有多个参数，key=new SimpleKey(params);

3. 没有查到缓存就去调用目标方法；

4. 方法查询结束后，将目标方法返回的结果放入到缓存中。

总结：

​	@Cacheable标注的方法执行之前先来检查缓存中有没有这个数据，默认按照参数的值作为key去查询缓存，如果缓存中没有，就执行方法并将结果放入到缓存中；以后再来调用就可以直接将缓存中的数据进行返回。

核心：

1. 使用CaCheManager【ConCurrentMapCacheManager】按照名字得到Cache【ConcurrentMapCache】组件
2. key是使用keyGenerator生成的，默认是SimpleKeyGenerator



主类：

```java
@SpringBootApplication
@MapperScan(value = "com.jcq.cache.mapper")
@EnableCaching  //这个注解不要忘了
public class Springboot09CacheApplication {

    public static void main(String[] args) {
        SpringApplication.run(Springboot09CacheApplication.class, args);
    }

}
```



业务方法

```java
@Service
public class EmployeeService {
    @Resource
    private EmployeeMapper employeeMapper;

    @Cacheable(cacheNames = "emp",key = "#root.methodName+'['+#id+']'")
    public Employee getEmp(Integer id){
        return employeeMapper.selEmpByid(id);
    }
}
```



### 2、其他属性



1. keyGenerator：key的生成器，可以自己指定key的生成器组件的id ，key/keyGenerator二选一使用。

2. cacheManager:指定缓存管理器，或者cacheResolver指定获取解析器。

3. condition：满足条件是放入缓存。

4. unless：满足条件是不放入缓存，与condition正好相反。

5. sync：默认为false，true为开启异步缓存。若开启异步，unless注解无效

   

### 3、其他注解

**@CachePut注解**：方法运行完后，将数据放入缓存中

**@CachePut**与**@Cacheable**的区别：

​		后者在方法运行前先到缓存总查询，前者在方法执行后，将数据放入缓存中。

注意：

​		只有保持数据的key相同，才能实现数据的同步更新。

```java
 @CachePut(cacheNames = "emp",key = "#employee.id")
    public Employee update(Employee employee){
        employeeMapper.updEmp(employee);
        return employee;
    }
```



@**CacheEvict**:清除缓存数据

**allEntries**:清空所有缓存，默认为false

**beforeInvocation**：在方法执行前清空缓存，默认为false

注意：保持数据key同一，才可以保持数据同步。

```java
 //测试清除缓存中的数据
    @CacheEvict(cacheNames = "emp",key = "#id")
    public void delEmpCache(Integer id){
        System.out.println("清除缓存");
    }
```

@**Caching**：可以将多个注解组合

```java
//测试Caching的使用
    @Caching(cacheable = {
          @Cacheable(value = "emp", key = "#lastName")
        },
          put = {@CachePut(value = "emp", key = "#result.id"),
                 @CachePut(value = "emp",key = "#result.email")
        }
    )
    public Employee selBylastName(String lastName){
       return employeeMapper.selBylastName(lastName);
    }

```

## 2、整合redis

### 1、redis命令

[常用命令网址](http://www.redis.cn/commands.html#)

### 2、步骤

1. 在pom.xml中引入下列依赖

```xml
<!--引入redis启动器-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```

2. 配置文件如下

```properties
#配置redis
spring.redis.host=192.168.91.128
```

3. SpringBoot把对redis的操作封装在**RedisTemplate** 和 **StringRedisTemplate**中

```java
@SpringBootTest
class Springboot09CacheApplicationTests {

    @Autowired
    private RedisTemplate redisTemplate;
    @Autowired
    private StringRedisTemplate stringRedisTemplate;
    @Autowired
    private EmployeeService employeeService;
    @Test
    public void testRedis(){
        //添加字符串
//        stringRedisTemplate.opsForValue().append("msg","helloworld");
        //添加list
//        stringRedisTemplate.opsForList().leftPushAll("mylist","1","2","3");
        //取出list
//        String str = stringRedisTemplate.opsForList().leftPop("mylist");
//        System.out.println(str);

        Employee emp = employeeService.getEmp(1);
        //添加对象
        redisTemplate.opsForValue().set("emp",emp);

    }
```

### 3、添加自定义组件，将对象转化为json字符串存储

```java
@Configuration
public class MyRedisTemplate {

    @Bean
    public RedisTemplate<Object, Employee> redisTemplate(RedisConnectionFactory redisConnectionFactory) throws UnknownHostException {
        RedisTemplate<Object, Employee> template = new RedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        template.setDefaultSerializer(new Jackson2JsonRedisSerializer(Employee.class));
        return template;
    }
}
```



而在服务启动过程中，使用的CacheManager依旧默认按照序列化的方式进行对象存储，所以还需要添加组件来达到将对象用json存储的目的

```java
 /**
     * 缓存管理器
     */
    @Bean
    public CacheManager cacheManager(RedisConnectionFactory redisConnectionFactory) {
        //初始化一个RedisCacheWriter
        RedisCacheWriter redisCacheWriter = RedisCacheWriter.nonLockingRedisCacheWriter(redisConnectionFactory);
        //设置CacheManager的值序列化方式为json序列化
        RedisSerializer<Object> jsonSerializer = new GenericJackson2JsonRedisSerializer();
        RedisSerializationContext.SerializationPair<Object> pair = RedisSerializationContext.SerializationPair
                .fromSerializer(jsonSerializer);
        RedisCacheConfiguration defaultCacheConfig=RedisCacheConfiguration.defaultCacheConfig()
                .serializeValuesWith(pair);
        //设置默认超过期时间是30秒
        defaultCacheConfig.entryTtl(Duration.ofSeconds(30));
        //初始化RedisCacheManager
        return new RedisCacheManager(redisCacheWriter, defaultCacheConfig);
    }
```



# 二、SpringBoot与消息

## 1、使用docker安装rabbitmq

```linux
//下载rabbitmq
docker pull rabbitmq:3-management
//运行
docker run -d -p 5672:5672 -p 15672:15672 --name myrabbitmq id
```

默认用户名密码均为：guest

转换器类型：

1. direct:只有key相同的队列才可以收到消息
2. fanout:任意队列都能收到消息
3. atguigu.#     : #表示0或多个词
4. *.news : *表示0或一个单词

## 2、SpringBoot整合Rabbitmq

### 1、基础使用

配置文件：**

```properties
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
spring.rabbitmq.host=192.168.91.128
#默认为5672，不需要配置
#spring.rabbitmq.port=5672
```

**测试代码：**

```java
 /**
     * 单播
     */
    @Test
    void contextLoads() {

        Map<String, Object> map = new HashMap<>();
        map.put("name", "张三");
        map.put("age", 18);
        rabbitTemplate.convertAndSend("exchange.direct","atguigu.news",map);
    }
```

**但是这样发送的消息，会以默认的序列化方式进行存储。效果**

![image-20200310195943079](E:\笔记\SpringBoot\springboot高级\SpringBoot高级.assets\image-20200310195943079.png)

**接收消息：**

```java
   /**
     * 接收消息
     */
    @Test
    void recieve(){
        Object o = rabbitTemplate.receiveAndConvert("atguigu.news");
        System.out.println(o.getClass());
        System.out.println(o);
    }
```

**自定义组件，使用json格式进行对象转化**

```java
@Configuration
public class MyAMQPConfig {
    @Bean
    public MessageConverter messageConverter(){
        return new Jackson2JsonMessageConverter();
    }
}
```

![image-20200310201323308](E:\笔记\SpringBoot\springboot高级\SpringBoot高级.assets\image-20200310201323308.png)

**广播测试代码**

```java
 /**
     * 广播
     */
    @Test
    void sendMsg(){
        rabbitTemplate.convertAndSend("exchange.fanout","",new Book("SpringMVC","张佳明"));
    }
```

### 2、消息监听

1. 主类上添加@**EnableRabbit注解**

```java
@EnableRabbit
@SpringBootApplication
public class SpringBoot10AmqpApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBoot10AmqpApplication.class, args);
    }

}
```

2. 业务代码如下：

```java
@Service
public class BookService {
    @RabbitListener(queues = "atguigu.news")
    public void bookLinstener(Book book){
        System.out.println("接收到消息"+book);
    }
}
```

### 3、使用AmqpAdmin管理工具

```java
	@Autowired
    private AmqpAdmin amqpAdmin;

    @Test
    void createRabbit(){
        //创建转换器
//      amqpAdmin.declareExchange(new DirectExchange("AmqpAdmin.exchange"));
//      System.out.println("转换器创建成功");
        //创建消息队列
//      amqpAdmin.declareQueue(new Queue("AmqpAdmin.news"));
//      System.out.println("消息队列创建成功");
        //创建绑定规则
        amqpAdmin.declareBinding(new Binding("AmqpAdmin.news", Binding.DestinationType.QUEUE, "AmqpAdmin.exchange", "AmqpAdmin.haha", null));
        System.out.println("创建绑定成功");
        //删除队列
        amqpAdmin.deleteQueue("AmqpAdmin.news");
        System.out.println("删除队列");
        amqpAdmin.deleteExchange("AmqpAdmin.exchange");
        System.out.println("删除转换器");
    }
```



# 三、SpringBoot与检索

## ElasticSearch

### 1、搭建环境

```linux
1、下载es
docker pull elasticsearch

2、启动命令
[root@localhost ~]# docker run -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -d -p 9200:9200 -p 9300:9300 --name ES01 5acf0e8da90b
7d33661ebabc32621173c4c9648047016e6ede9fc625c51340692736b7fdf170

其中:
ES_JAVA_OPTS="-Xms256m -Xmx256m"   :-Xms 初始堆内存大小   -Xmx  最大使用内存
9200：es默认web通信使用9200端口
9300：当分布式情况下es多个节点之间通信使用9300端口


```

文档地址：

https://www.elastic.co/guide/cn/elasticsearch/guide/current/index-doc.html

SpringBoot默认支持两种技术和ElasticSearch交互



一、Jest（默认不生效）

需要导入jest的工具包（io.serachbox.chlient.JestClient）

需要将默认的SpringBoot-data-Elasticsearch注释掉

导入jest依赖

但是jest已经过时，不演示了

二、SpringData	

1. Client节点信息clusterNodes,clusterName
2. ElasticsearchTemplate来操作es

3. 编写一个ElasticsearchRepository子接口来操作es

因版本问题，暂时没有解决SpringBoot2.X版本整合es的方法，使用会报错

# 四、SpringBoot与任务

## 1、异步任务

**1、在主类添加@EnableAsync注解**

2、在业务层添加@Async注解即可实现异步任务

```java
@Async
@Service
public class AsyncService {

    public void testAsync(){
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("方法执行完毕");
    }
}
```



## 2、定时任务

cron表达式：

Cron表达式的格式：秒 分 时 日 月 周 年(可选)。
字段名        允许的值           允许的特殊字符 
秒           	 0-59               			, - * / 
分         	   0-59             			   , - * / 
小时       	 0-23            			  , - * / 
日          	 1-31              			  , - * ? / L W C
月          	 1-12 or JAN-DEC       , - * / 
周几           1-7 or SUN-SAT     	 , - * ? / L C # 
年(可选字段)   empty              	1970-2099 , - * /

具体的用法：https://www.cnblogs.com/lazyInsects/p/8075487.html

```java
  @Scheduled(cron = "0 * * * * MON-FRI")  周一到周五，没整分运行一次
//    @Scheduled(cron = "0/4 * * * * MON-FRI")  没整分，间隔4秒执行
    @Scheduled(cron = )
    public void testAsync(){

        System.out.println("方法执行完毕");
    }
}
```

在方法上加上**@Scheduled**注解

在主类添加**@EnableSchelding**注解

主类代码如下

```java
//@EnableAsync
@EnableScheduling
@SpringBootApplication
public class SpringBoot12TaskApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBoot12TaskApplication.class, args);
    }

}

```

## 3、邮件任务

配置文件

```properties
spring.mail.username=1036658425@qq.com
spring.mail.password=kersbnhzzuhfbdih
spring.mail.host=smtp.qq.com
```

```java
@Autowired
    JavaMailSenderImpl mailSender;

    @Test
    void contextLoads() {
        //创建简单邮件对象
        SimpleMailMessage massage = new SimpleMailMessage();
        //设置主题
        massage.setSubject("简单测试");
        //设置内容
        massage.setText("测试简单邮件发送");
        //设置收件方
        massage.setTo("594082079@qq.com");
        //设置发送方
        massage.setFrom("1036658425@qq.com");

        mailSender.send(massage);
    }

    @Test
    public void test02() throws MessagingException {
        //测试复杂邮件发送
        MimeMessage mimeMessage = mailSender.createMimeMessage();
        MimeMessageHelper helper = new MimeMessageHelper(mimeMessage,true);
        helper.setSubject("复杂发送");
        helper.setText("<b style='color:red'>测试复杂邮件发送</b>",true);
        helper.addAttachment("a.jpg",new File("E:\\笔记\\SpringBoot\\springboot高级\\SpringBoot高级.assets\\image-20200310195943079.png"));
        helper.addAttachment("b.jpg",new File("E:\\笔记\\SpringBoot\\springboot高级\\SpringBoot高级.assets\\image-20200310201323308.png"));
        helper.setTo("594082079@qq.com");
        helper.setFrom("1036658425@qq.com");
        mailSender.send(mimeMessage);

    }

```



# 五、SpringBoot与安全

## 1、业务部分代码

编写配置类

```java
@EnableWebSecurity
public class MySecurityConfig extends WebSecurityConfigurerAdapter {

    //添加认证功能
    @Override
    protected void configure(HttpSecurity http) throws Exception {
//        super.configure(http);

        http.authorizeRequests().mvcMatchers("/").permitAll()
                .mvcMatchers("/level1/**").hasRole("VIP1")
                .mvcMatchers("/level2/**").hasRole("VIP2")
                .mvcMatchers("/level3/**").hasRole("VIP3");

        //开启登录认证页面
        http.formLogin();

        //开启自动配置注销功能
        http.logout().logoutSuccessUrl("/");
        //访问/logout  清除session

        //开启自动配置记住我功能
        http.rememberMe();
    }

    //添加登录授权功能
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
//        super.configure(auth);
        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
                .withUser("zhangsan").password(new BCryptPasswordEncoder().encode("123456")).roles("VIP1","VIP2")
                .and().withUser("lisi").password(new BCryptPasswordEncoder().encode("123456")).roles("VIP2","VIP3")
                .and().withUser("wangwu").password(new BCryptPasswordEncoder().encode("123456")).roles("VIP3","VIP1");

    }
```

整合页面部分，因版本问题，暂时没有效果，先不放图片了

# 六、分布式

## 1、Dubbo

下载镜像

```linux
[root@localhost ~]# docker pull zookeeper

[root@localhost ~]# docker run --name zk01 -p 2181:2181 --restart always -d bbebb888169c

```

### 1、提供者

1. 将服务提供者注册到注册中心

导入相关依赖Dubbo和Zkclient

```xml
<!--引入Dubbo服务启动器-->
<dependency>
    <groupId>com.alibaba.boot</groupId>
    <artifactId>dubbo-spring-boot-starter</artifactId>
    <version>0.1.0</version>
</dependency>

<!--引入zookeeper的客户端工具-->
<!-- https://mvnrepository.com/artifact/com.github.sgroschupf/zkclient -->
<dependency>
    <groupId>com.github.sgroschupf</groupId>
    <artifactId>zkclient</artifactId>
    <version>0.1</version>
</dependency>
```

	2. 在主类添加启用Dubbo的注解

```java
@EnableDubbo
@SpringBootApplication
public class ProviderTicketApplication {

    public static void main(String[] args) {
        SpringApplication.run(ProviderTicketApplication.class, args);
    }

}
```

**如果不加此注解，消费者会报空指针异常**

 	2. 配置application配置文件

```properties
dubbo.application.name=provider
dubbo.registry.address=zookeeper://192.168.91.128:2181
dubbo.scan.base-packages=com.jcq.ticket.service
```

3. 使用@service发布服务

```java
@Component
@Service
public class TickerServiceImpl implements TickerService {
    @Override
    public String getTicker() {
        return "《厉害了，我的国》";

    }
}
```



### 2、消费者

1. 导入相关依赖

```xml
<!--引入Dubbo服务启动器-->
<dependency>
    <groupId>com.alibaba.boot</groupId>
    <artifactId>dubbo-spring-boot-starter</artifactId>
    <version>0.1.0</version>
</dependency>

<!--引入zookeeper的客户端工具-->
<!-- https://mvnrepository.com/artifact/com.github.sgroschupf/zkclient -->
<dependency>
    <groupId>com.github.sgroschupf</groupId>
    <artifactId>zkclient</artifactId>
    <version>0.1</version>
</dependency>
```

2. 配置文件

```properties
dubbo.application.name=consumer
dubbo.registry.address=zookeeper://192.168.91.128:2181
server.port=8081
```

3. 引用服务

- 需要把提供者的包个接口复制到项目中，且保持路径同一

![image-20200312214223898](E:\笔记\SpringBoot\springboot高级\SpringBoot高级.assets\image-20200312214223898.png)

![image-20200312214356130](E:\笔记\SpringBoot\springboot高级\SpringBoot高级.assets\image-20200312214356130.png)

加下来就是编写业务代码了

```java
import com.alibaba.dubbo.config.annotation.Reference;
import org.springframework.stereotype.Service;
@Service
public class UserServiceImpl {
    @Reference
    private TickerService tickerService;

    public String getTicker(){
         return tickerService.getTicker();
    }
}
```

**注意不要到错包了，会导致空指针异常**。

## 2、SpringCloud

### 1、注册中心

1、首先在主类添加**@EnableEurekaServer**注解

```java
@EnableEurekaServer
@SpringBootApplication
public class EurekaServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(EurekaServerApplication.class, args);
	}

}
```

2、添加配置文件

```yaml
server:
  port: 8761   #修改服务端口


eureka:
  client:
    fetch-registry: false  #不从注册中心获取服务
    register-with-eureka: false		#不在服务中心注册服务
    service-url:
      defaultZone: http://localhost:8761/eureka/	#访问路径
  instance:
    hostname: eureka-server		#主机名称
```

3、登录页面查看是否启动成功

网址：

http://localhost:8761/

### 2、提供者

1、编写配置文件

```yaml
server:
  port: 8001

spring:
  application:
    name: provider-ticket

eureka:
  instance:
    prefer-ip-address: true  #注册服务时，使用服务的ip地址
  service-url:
    defaultZone: http://localhost:8761/eureka/
```

2、正常编写业务代码及控制器代码



一个项目注册多个服务

直接将不同端口好的项目进行打包，使用cmd命令分别执行即可

项目打包后的图片

![image-20200313104523170](E:\笔记\SpringBoot\springboot高级\SpringBoot高级.assets\image-20200313104523170.png)



管理页面显示内容入下

![image-20200313104533629](E:\笔记\SpringBoot\springboot高级\SpringBoot高级.assets\image-20200313104533629.png)

### 3、消费者

1、编写配置文件

```yaml
server:
  port: 8200

eureka:
  instance:
    prefer-ip-address: true
  service-url:
    defaultZone: http://localhost:8761/eureka/

spring:
  application:
    name: consumer-user
```

2、主类代码：

```java
@EnableDiscoveryClient  //开启到注册中心查找服务
@SpringBootApplication
public class ConsumerUserApplication {

	public static void main(String[] args) {
		SpringApplication.run(ConsumerUserApplication.class, args);
	}

	/**
	 * 给容器添加组件
	 * 可以发送远程http请求
	 * @return
	 */
	@LoadBalanced  //开启负载均衡功能
	@Bean
	public RestTemplate restTemplate(){
		return new RestTemplate();
	}
}
```

3、业务代码

```java
@RestController
public class UserController {

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/buy")
    public String buyTicket(String name){

        //发送远程请求，请求注册中心的业务方法。地址直接写注册中心中的服务名，不需写端口号
        String ticket = restTemplate.getForObject("http://PROVIDER-TICKET/get", String.class);
        return name+"购买了"+ticket;
    }
}
```

# 七、热部署

只需要到如SpringBoot提供的依赖即可

```xml
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<scope>runtime</scope>
			<optional>true</optional>
		</dependency>
```

更改代码后，手动CTRL+F9  重新编译，即可实现热部署

# 