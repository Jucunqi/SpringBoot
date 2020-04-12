# 一、springboot入门

## 1、springboot简介

简化Spring应用开发的一个框架；

整个Spring技术栈的一个大整合；

J2EE开发的一站式解决方案；

## 2、微服务

2014，martin fowler

微服务：架构风格（服务微化）

一个应用应该是一组小型服务；可以通过HTTP的方式进行互通；

单体应用：ALL IN ONE

微服务：每一个功能元素最终都是一个可独立替换和独立升级的软件单元；

## 3、环境准备

### 1、maven设置

推荐使用3.X

我使用的是3.3.3

### 2、IDEA配置

配置maven、jdk等相关设置

## 4、springboot编写helloworld

通过idea自动提供的脚手架功能快速创建springboot项目

### 1、创建springboot时可能出现的错误

### 1、连接springboot网站超时问题

​		如图给与解决方式

​				1、1 	点击设置

![3](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200304190412905.png)





​			1、2	一次点击图示位置进行网站设置![image-20200304190557345](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200304190557345.png)

​		

​			1、3	在弹出的界面中数据一下网址，并测试连接情况

![image-20200304190814324](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200304190814324.png)



​			1、4	出现图示连接成功后，重新创建springboot项目即可

​								

## 5、HelloWorld探究

### 		1、pom文件

#### 					1、父项目							

```java
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.5.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

​			springboot会自动依赖一个父项目

#### 		2、启动器

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

​			不同的项目会导入不同的启动器，springboot会根据启动器而去选择导入相应的包，从而省去了我们之前配置的xml文件



#### 		3、主程序类

​				主程序的main方法要写在最外层的包中，即包含所有的子包，这样就可以加载到所有的配置文件，***这是必须的***

​				主程序类需要配置相应的注解：@**SpringBootApplication**

​				@**EnableAutoConfiguration**：此注解的作用为，开启自动配置

​				我们需要自己配置的东西，自动配置类都帮我们解决了。

​				以上配置都在**springboot-autoconfigure**这个jar包中

## 6、使用idea快速创建项目

### 	1、java目录

​			1、使用快速创建的项目，java目录中会自动生成主方法，我们只需编写业务逻辑代码即可。

​			2、@**SpringBootApplication**：该注解等同于@RequestMapping（）和ResponseBody相结合

### 	2、resource目录

​			1、static文件夹：用于存放静态资源，如js、html、image、等

​			2、templates文件夹：用户放页面显示文件，springboot默认不支持jsp，但是也可以引用模板来使用jsp或他模板

### 	3、application.properties

​			1、该文件中可以修改一些springboot的默认配置，如Tomcat默认端口号等。



项目目录结构见下图

![image-20200304202058807](E:\笔记\springboot入门.assets\image-20200304202058807.png)



# 二、配置文件



## 1、spring认同的配置文件

### 		1、application.properties

​					与我们之前用的配置文件使用方法相同。

### 		2、application.yml



## 2、yml文件

​			yaml  是一种文件类型  也可以也做yml



yaml		**yaml  ain't  markup language**    这句话可以理解为：

- yaml a markup language		yaml是一个标记语言
- yaml ism't a markup language  	yaml不是一个标记语言



其实yaml还是标记语言

yaml与xml的区别

yaml以数据为中心，比xml、json等更适合作为配置文件

```yaml
server:
  port: 8081
	
```

xml

```xml
<servetr>
	<port>8081</port>
</servetr>
```



## 3、yaml语法

### 1、基本语法

​		yaml文件中默认的格式为  k: v    k与v中间的空格是不可缺少的，所有左对齐的数据为同一级别。

​		yaml文件只可以使用空格，不可以使用tab键



### 2、值得表示

#### 字面量：普通的值

​		包括：数字，字符串，布尔值等

​		语法： 	k: v      直接在后面写值即可

​		如： name: zhangsan 

yaml中“”  与‘’ 的区别：

​		yaml 中**双引号**不会对内容进行转义

​				name: "zhangsan \n"   会输出张三+换行		

​		yaml 中**单引号**会对内容进行转义

​				name: 'zhangsan \n'    会输出张三 \n



#### 对象、map

语法如下：

```yaml
people:
	name: zhangsan
	age: 19
```



行内式写法

```yaml
people: {name: zhangsan,age: 19}
```



#### 数组（list、集合）

以- 的方式进行书写

语法如下：

```yaml
pets:
  - dog
  - cat
  - pig
```



行内式的写法如下：

```yaml
pets: [dog,cat,pig]
```



**如此写法，你懂了吗？**



### 3、yaml文件及代码示例

yaml配置文件

```yaml
person:
  name: zhangsan
  age: 18
  date: 2020/3/4
  map: {k1: v1,k2: v2}
  list:
    - l1
    - l2
  pet:
    name: zhuzhu
    age: 3
```



代码部分如下：

```java
public class Person {


    private String name;
    private int age;
    private Date date;
    private Map<String,Object> map;
    private List<Object> list;
    private Pet pet;
    //getter and setter 省略...
```

### 4、使用properties进行相同的文件配置

```properties
person.name=张三
person.age=18
person.date=2020/3/5
person.map.k1=v1
person.map.k2=v2
person.list=l1,l2
person.pet.name=zhuzhu
person.pet.age=3
```



## 4、配置文件值得注入

### 	1、properties默认采用utf-8编码，记得修改idea编码格式

​			修改方法见下图

​	![image-20200305095921900](E:\笔记\springboot入门.assets\image-20200305095921900.png)

​			

​			将图示位置进行修改，点击ok返回。



### 2、@ConfigurationProperties与@Value的比较



|                      | @ConfigurationProperties | @value               |
| -------------------- | ------------------------ | -------------------- |
| 功能                 | 批量注入配置文件中的属性 | 单值进行配置文件注入 |
| 松散绑定（松散语法） | 支持                     | 不支持               |
| SpL                  | 不支持                   | 支持                 |
| JSR303数据校验       | 支持                     | 不支持               |
| 复杂类型封装         | 支持                     | 不支持               |

注：

​	1、松散绑定：如 person-name     等同于personName

​	2、SpEL：spring计算语言    如   #{30*2}     可以在文件注入时进行数据计算

​	3、JSR303数据校验：如@Email    校验数据格式是否为邮件格式 ，使用数据校验需要在类上添加**@Validate**注解

​	4、复杂类型封装：如map等



总结：

​		1、都可以进行配置文件注入。

​		2、如果对整个javabean进行值的注入，使用@ConfigurationProperties，方便快捷。

​		3、如果只是在逻辑中，单独对某个属性进行注入，使用@Value。



### 3、数据校验

代码如下

```java
@Component
@ConfigurationProperties(prefix = "person")
@Validated
public class Person {

    @Email
    private String name;
    private int age;
    private Date date;
    private Map<String,Object> map;
    private List<Object> list;
    private Pet pet;
```

@Validated 与 @Email 	搭配使用

### 4、@PropertiesSource和@ImportResource的使用

#### 		1、@PropertiesSource

​			它的作用是加载制定位置的配置文件。

```java
@Component
@ConfigurationProperties(prefix = "person")
@PropertySource(value = {"classpath:person.properties"})
public class Person {


    private String name;
    private int age;
    private Date date;
    private Map<String,Object> map;
    private List<Object> list;
    private Pet pet;

```



**注意：**不可以把@ConfigurationProperties注释掉



#### 		2、@ImportResource给spring添加组件

以前使用xml的形式给spring容器添加组件

如：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="helloService" class="com.jcq.springboot.service.HelloService"></bean>
</beans>
```

然后在主类上添加注解@ImportResource

```java
@ImportResource(locations = {"classpath:bean.xml"})
```



不过springboot更推荐使用**配置类**的方式添加组件

使用全注解的方式

配置类的代码如下：

```java
/**
 * @Configuration：指明当前类是一个配置类，用来替代之前的配置文件
 */
@Configuration
public class MyAppConfig {

    @Bean
    public HelloService helloService(){

        System.out.println("给容器中添加组件");
        //方法的返回值，将会添加到容器中，组件的默认id为方法名
        return new HelloService();
    }
}

```



### 5、配置文件占位符

#### 	1、随机数

#### 	2、占位符

代码如下

```properties
person.name=张三${random.uuid}
person.age=${random.int}
person.date=2020/3/5
person.map.k1=v1
person.map.k2=${persion.service:hello}_v2
person.list=l1,l2
person.pet.name=${person.name}zhuzhu
person.pet.age=3
```



## 5、profile

### 		1、properties文件

​			1、通过     application-{fileName}.properties  的方式进行区分

​			2、在application.properties 文件中激活

```properties
server.port=8081
spring.profiles.active=dev
#---------
server.port=8082
spring.profiles=dev
```

### 		2、yaml文件

​			通过   ---     进行区分

​			

```yaml
server:
  port: 8081

spring:
  profiles:
    active: dev    #表示激活
---
server:
  port: 8082

spring:
  profiles: prod
---
server:
  port: 8083

spring:
  profiles: dev
```



### 	3、在命令行进行激活

​			指令： --spring.profiles.active=dev

-  可以将文件打成far包后用  java -jar命令后     继续使用该命令
-  也可以通过给虚拟机使用命令   -Dspring.profiles.active=dev

​			达到激活的效果



## 6、配置文件加载位置

### 	1、优先级

springboot会扫描一下位置下的properties或yaml文件作为默认的配置文件

- file:/config
- file:/
- classpath:/config
- classpath:/

**优先级从上到下依次递减**

### 	2、互补配置

==优先级高的配置文件会覆盖掉优先级低的配置文件，进行配置互补==

也可以在项目发布后使用命令添加配置文件加载路径，改变默认配置

--spring.config.location=${filepath}

## 7、外部配置加载位置

- ==最高级为命令行==
- ...
- ==jar包外的优先于jar包内的==
- ==带{profile}的优先于不带{profile}的==

详情参考官方文档

分类十分详细，没有做具体笔记

==优先级从高到低；高优先级的配置覆盖低优先级的配置，所有的配置会 形成互补配置== 

1.命令行参数 所有的配置都可以在命令行上进行指定 java -jar spring-boot-02-config-02-0.0.1-SNAPSHOT.jar --server.port=8087 --server.context-path=/abc 多个配置用空格分开； --配置项=值 

2.来自java:comp/env的JNDI属性 

3.Java系统属性（System.getProperties()） 

4.操作系统环境变量 

5.RandomValuePropertySource配置的random.*属性值 

==由jar包外向jar包内进行寻找；== 

==优先加载带profile== 

6.jar包外部的application-{profile}.properties或application.yml(带spring.profile)配置文件 

7.jar包内部的application-{profile}.properties或application.yml(带spring.profile)配置文件 

==再来加载不带profile== 

8.jar包外部的application.properties或application.yml(不带spring.profile)配置文件 

9.jar包内部的application.properties或application.yml(不带spring.profile)配置文件 

10.@Configuration注解类上的@PropertySource 

11.通过SpringApplication.setDefaultProperties指定的默认属性 所有支持的配置加载来源； 参考官方文档



## 8、自动配置原理

1. springboot会加载相关的自动配置类
2. 自动配置类中会个容器中添加相应的组件
3. 每个组件会加载相应的properties类，在属性类中，会到我们的配置文件中加载相应属性

### 1、自动配置类  

如：

```java
@Configuration(
    proxyBeanMethods = false
)
@EnableConfigurationProperties({HttpProperties.class})//相应的properties类
@ConditionalOnWebApplication(
    type = Type.SERVLET
)
@ConditionalOnClass({CharacterEncodingFilter.class})
@ConditionalOnProperty(
    prefix = "spring.http.encoding",   //可以从这里查看我们在配置文件中应该配置的内容前缀
    value = {"enabled"},
    matchIfMissing = true
)
public class HttpEncodingAutoConfiguration {
    
    private final Encoding properties;//引用properties类
    
     @Bean   //给容器中添加组件
    @ConditionalOnMissingBean
    public CharacterEncodingFilter characterEncodingFilter() {
        CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
       //有些内容需要用到配置文件中的相关配置
        filter.setEncoding(this.properties.getCharset().name());
            filter.setForceRequestEncoding(this.properties.shouldForce(org.springframework.boot.autoconfigure.http.HttpProperties.Encoding.Type.REQUEST));
            filter.setForceResponseEncoding(this.properties.shouldForce(org.springframework.boot.autoconfigure.http.HttpProperties.Encoding.Type.RESPONSE));
        return filter;
    }
```



### 2、properties类

```java
public class HttpProperties {
    private boolean logRequestDetails;
    private final HttpProperties.Encoding encoding = new HttpProperties.Encoding();
    
    //通过静态内部类的方式加载属性文件
    //这些属性的值，就是我们在配置问价中可以进行的相关配置
    public static class Encoding {
            public static final Charset DEFAULT_CHARSET;
            private Charset charset;
            private Boolean force;
            private Boolean forceRequest;
            private Boolean forceResponse;
            private Map<Locale, Charset> mapping;
```



### 3、精髓：

1. SpringBoot启动时会加载大量配置类
2. 我们看我们需要的功能是否具备相应的配置类
3. 我们再来看这个配置类中配置了哪些组件。（如果没有我们需要的组件，就需要我们自行进行配置，如果存在相应配置类，我们就不用再进行配置了）
4. 给容器中自动配置了添加属性时，会从Properties类中获取某些属性，我们就可以在配置文件中，对这些属性进行赋值。



> xxxAutoConfiguration：自动配置类
>
> > 给容器中添加组件；
>
> xxxProperties：相应属性类
>
> > 封装配置文件中相关属性；



### 4、细节

1. @Condition注解的的扩展

```txt
@Conditional的扩展注解
1.class条件注解

@ConditionalOnClass：某个class位于类路径上，才会实例化一个Bean。

@ConditionalOnMissingClass：某个class类路径上不存在的时候，才会实例化一个Bean。

2.Bean条件注解

@ConditionalOnBean：当容器中有指定Bean的条件下进行实例化。

@ConditionalOnMissingBean：当容器里没有指定Bean的条件下进行实例化。

3.属性条件注解

@ConditionalOnProperty：当指定的属性有指定的值时进行实例化。

4.Resource条件注解

@ConditionalOnResource：当类路径下有指定的资源时触发实例化。

5.web条件注解

@ConditionalOnNotWebApplication：不是web应用，才会实例化一个Bean。

@ConditionalOnWebApplication：当项目是一个Web项目时进行实例化。
6.表达式条件注解

@ConditionalOnExpression：基于SpEL表达式的条件判断，当表达式为true的时候，才会实例化一个Bean。



@ConditionalOnJava：当JVM版本为指定的版本范围时触发实例化。
@ConditionalOnJndi：在JNDI存在的条件下触发实例化。
@ConditionalOnSingleCandidate：当指定的Bean在容器中只有一个，或者有多个但是指定了首选的Bean时触发实例化。

```



2. @Condition自动配置报告

在配置文件中添加**debug=true**属性即可打印自动配置报告

配置内容

```properties
#server.port=8081
#spring.profiles.active=dev

debug=true
```

自动配置报告

```java
============================
CONDITIONS EVALUATION REPORT
============================


Positive matches:
-----------------

   AopAutoConfiguration matched:
      - @ConditionalOnProperty (spring.aop.auto=true) matched (OnPropertyCondition)

   AopAutoConfiguration.ClassProxyingConfiguration matched:
      - @ConditionalOnMissingClass did not find unwanted class 'org.aspectj.weaver.Advice' (OnClassCondition)
      - @ConditionalOnProperty (spring.aop.proxy-target-class=true) matched (OnPropertyCondition)
          
          
Negative matches:
-----------------

   ActiveMQAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required class 'javax.jms.ConnectionFactory' (OnClassCondition)          
```



自动配置报告中，包括符合条件启动的配置类，以及未符合条件的配置类



# 三、日志

## 1、日志框架

日志框架的来源，不在此进行记录了

spring框架默认是采用JCL门面，commons-logging

==SpringBoot选用SLF4J和logback==

## 2、SLF4F框架使用

### 1、helloworld  入门

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
```



### **2、sff4j结合其他框架使用需要的jar**

![](E:\笔记\springboot入门.assets\concrete-bindings.png)



### 3、遗留问题

应用层框架不能直接转换成slf4j，还需要中间包进行转换

![](E:\笔记\springboot入门.assets\legacy.png)



## 3、SpringBoot日志关系

![image-20200305183218834](E:\笔记\springboot入门.assets\image-20200305183218834.png)



==SpringBoot能自动适配所有日志，而且底层使用SLF4J和logback方式记录日志，引入其他框架的时候，只需要把这个框架依赖的日志包排除掉==



## 4、日志使用

### 1、默认配置

SpringBoot默认对打印日志的格式和级别进行了设置，我们可以在配置文件中，进行修改

SpringBoot的默认打印日志级别为Info，并且不会将日志写到文件。



代码如下：

```java
   //记录器
        Logger logger = LoggerFactory.getLogger(getClass());

        //日志级别
        logger.trace("这是trance日志。。。");
        logger.debug("这是debug日志。。。");
        logger.info("这是info日志。。。");
        logger.warn("这是warn日志。。。");
        logger.error("这是error日志。。。");
```

配置文件如下:

```properties
# 修改指定包下的日志级别
logging.level.com.jcq.springboot=trace
#修改日志输出格式
logging.pattern.console=%C %d{YYYY-MM-dd hh:mm:ss}  %m %n
```



### 2、指定配置

在类路径下放上不同日志框架指定格式的配置文件，就可以不使用SpringBoot的默认配置了。

| Logging System          | Customization                                                |
| ----------------------- | ------------------------------------------------------------ |
| Logback                 | `logback-spring.xml`, `logback-spring.groovy`, `logback.xml`, or `logback.groovy` |
| Log4j2                  | `log4j2-spring.xml` or `log4j2.xml`                          |
| JDK (Java Util Logging) | `logging.properties`                                         |



logback.xml:直接被识别

**logback-spring.xml**：日志框架不会直接加载配置项，有SpringBoot解析配置项，可以使用SpringBoot的高级功能，推荐使用**logback-spring.xml**

### 3、日志框架转换

按照之前的转换图进行转换，注意移除部分依赖



# 四、web开发

## 1、简介

创建SpringBoot的web项目

1. 创建项目是勾选需要使用到的模块。
2. SpringBoot已经提供了相应功能的支持。
3. 编写自己的业务代码

- 熟悉每个模块需要自动配置类，以及需要的配置文件

## 2、静态资源路径规则

1）、引入的静态资源默认路径为/webjar/**

```java
 public void addResourceHandlers(ResourceHandlerRegistry registry) {
            if (!this.resourceProperties.isAddMappings()) {
                logger.debug("Default resource handling disabled");
            } else {
                Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
                CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
                if (!registry.hasMappingForPattern("/webjars/**")) {
                    this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{"/webjars/**"}).addResourceLocations(new String[]{"classpath:/META-INF/resources/webjars/"}).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
   String staticPathPattern = this.mvcProperties.getStaticPathPattern();
                if (!registry.hasMappingForPattern(staticPathPattern)) {
                    this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{staticPathPattern}).addResourceLocations(WebMvcAutoConfiguration.getResourceLocations(this.resourceProperties.getStaticLocations())).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
                }

            }
        }
```

[可以到webjar官方获得相应依赖](https://www.webjars.org/)

网站访问项目的资源路径为：  http://localhost:8080/webjar/${filename}

webjar:以jar包的方式引用静态资源

2）、/**  表示类路径下任意资源，要是没有处理则到下列路径下查找文件

```java
"classpath:/META-INF/resources/", 
"classpath:/resources/", 
"classpath:/static/", 
"classpath:/public/"
 "/"   //当前项目根路径
```

3）、欢迎页的配置

```java
WelcomePageHandlerMapping(TemplateAvailabilityProviders templateAvailabilityProviders, ApplicationContext applicationContext, Optional<Resource> welcomePage, String staticPathPattern) {
    if (welcomePage.isPresent() && "/**".equals(staticPathPattern)) {
        logger.info("Adding welcome page: " + welcomePage.get());
        this.setRootViewName("forward:index.html");
```

在静态资源路径下的index.html

网页访问路径为/**

如：http://localhost:8080/

基本目录结构如下：

![image-20200305213957900](E:\笔记\springboot入门.assets\image-20200305213957900.png)



## 3、模板引擎

SpringBoot官方推荐Thymeleaf模板引擎

### 1、引入Thymeleaf

```xml
<!--引入模板引擎-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

使用SpringBoot2.2.5，引入的模板引擎版本为3.0.11RELEASE

### 2、thymeleaf的使用

```
public class ThymeleafProperties {
    private static final Charset DEFAULT_ENCODING;
    public static final String DEFAULT_PREFIX = "classpath:/templates/";
    public static final String DEFAULT_SUFFIX = ".html";
    private boolean checkTemplate = true;
    private boolean checkTemplateLocation = true;
    private String prefix = "classpath:/templates/";
    private String suffix = ".html";
    private String mode = "HTML";
```

文件放在根路径下的templates文件夹下的html文件





## 4、语法规则

1. 头部引入

若使用Thymeleaf的语法规则，必须在html文件中引入头文件，否则没有提示功能

```html
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org">
```

2. 基本语法

- th:text="${}"    可以将作用域中的值取出，并且会对字符进行转义
- th:utext="${}	也可以取出作用域中的值，但是不会进行转义
- th:each="user:${users}"   循环遍历，遍历的总对象为大括号中的内容，每一个对象为冒号前不带大括号的内容

代码如下：

```java
@RequestMapping("/success")
public String success(Map<String,Object> map){
    map.put("hello","<h1>你好</h1");
    map.put("users", Arrays.asList("zhangsan","lisi","wangwu"));
    return "success";
}
```

```html
<body>
    <div th:utext="${hello}"></div>
    <hr />
    <!--会对文本中的内容进行转义-->
    <div th:text="${hello}"></div>
    <hr>
    <!--每次遍历都会生成一个所在的标签 -->
    <div th:text="${user}" th:each="user:${users}" ></div>
    <hr>
    <h4 th:each="user:${users}" th:text="${user}"></h4>
</body>
```



页面显示：

```java
# 你好

------

<h1>你好</h1>

------

zhangsan

lisi

wangwu

------

#### zhangsan

#### lisi

#### wangwu
```



使用Thymeleaf给js函数传参

注意：单引号的使用以及转义符号的使用和理解

```java
	<!--thymeleaf给js函数传参-->
    <a th:href="'javascript:a(\''+${msg}+'\')'">点我</a>

        <script language="JavaScript">

        function a(msg) {
        alert("传过来的消息为："+msg);
    }
	</script>
```







## 5、SpringMVC自动配置

### 1、MVCAutoConfiguration

SpringBoot以为我们提供了大量的自动配置功能，不过我们都可以都过自己的方式向容器中添加组件，来满足自己想要的功能。

### 2、SpringMVC扩展

想要扩展配置功能，需要我们自己定义一个配置类，并放入容器中，但是不要添加@EnableWebMvc注解

```java
@Configuration
public class MyMvcConfig  implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        //给指定url访问添加视图解析器，访问同名的静态资源
        //registry.addViewController("/success");
        //给指定url访问添加视图解析器，访问指定的静态资源
        registry.addViewController("/jcq").setViewName("success");
    }
}
```

### 3、全面接管SpringMVC

SpringBoot给SpringMVC提供的所有配置都不起作用，只有用户自己配置的起作用，相当于，原始的SpringMVC都需要我们手动进行配置。

达到这种效果，只需要我们在配置类上添加@EnableWebMvc注解即可。一般不推荐使用。

```java
@EnableWebMvc
@Configuration
public class MyMvcConfig  implements WebMvcConfigurer {
```







## 6、如何修改SpringBoot的默认配置

SpringBoot提供自动配置的通常模式：

1. SpringBoot在自动配置很多组件的时候，都会先检查容器中是否已经存在了这个组件，如果没有则按照SpringBoot提供的默认配置进行，如果有则按照用户自己设定的功能执行。如何该组件有多个共同使用（ViewResolver），SpringBoot会通过自己的功能，达到用户与默认的功能共同生效，配合使用。
2. 在SpringBoot中会有很多的xxxConfigurer帮助我们进行扩展配置



## 7、RestfulCRUD

### 1、访问默认首页

将页面及样式脚本等导入项目中

注意：

1. 前段页面要导入到templates文件夹中，这样可以使用模板脚本的高级功能
2. 普通脚本等静态资源导入到static文件夹中



![image-20200306141527950](E:\笔记\SpringBoot\springboot入门\springboot入门.assets\image-20200306141527950.png)

将欢迎页设置为login.html

```java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("login");
    }
}
```

在模板中将资源引用使用Thymeleaf语言进行修改，这样可以让路径跟随项目根路径动态改变。

```html
th:href="@{/asserts/css/bootstrap.min.css}"'
th:src="@{/asserts/img/bootstrap-solid.svg}"
```

### 2、国际化

1. 创建一个基本的属性文件以及分别分别创建不同语言的属性文件
2. 可以使用idea使用的快捷界面工具

![image-20200306161254238](E:\笔记\SpringBoot\springboot入门\springboot入门.assets\image-20200306161254238.png)

3. SpringBoot默认的国际化属性文件是根目录下的message.properties文件

   所以，需要在配置文件中修改默认配置

```properties
spring.messages.basename=i18n.login
```

​		注意：

​				==只需要写基本配置文件的名字即可，不需要写文件后缀==

4. 修改html文件中的文本域内容

```html
<img class="mb-4" src="../asserts/img/bootstrap-solid.svg" th:src="@{/asserts/img/bootstrap-solid.svg}" alt="" width="72" height="72">
			<h1 class="h3 mb-3 font-weight-normal" th:text="#{login.please}">Please sign in</h1>
			<label class="sr-only" th:text="#{login.userName}">Username</label>
			<input type="text" class="form-control" placeholder="Username" th:placeholder="#{login.userName}" required="" autofocus="">
			<label class="sr-only" th:text="#{login.passWord}">Password</label>
			<input type="password" class="form-control" placeholder="Password" th:placeholder="#{login.passWord}" required="">
			<div class="checkbox mb-3">
				<label>
          <input type="checkbox" value="remember-me"> [[#{login.remember}]]
```

Thymeleaf的语法为   #{}

5. 创建自定义的国际化配置类

```java
public class MyLocaleResolver implements LocaleResolver {

    @Override
    public Locale resolveLocale(HttpServletRequest request) {
        //得到url中的语言参数
        String len = request.getParameter("l");
        System.out.println(len);
        Locale locale = Locale.getDefault();
        if(!StringUtils.isEmpty(len)){
            //将得到的参数进行分割
            String[] s = len.split("_");
            System.out.println(s);
            locale = new Locale(s[0],s[1]);
        }
        return locale;
    }
    //逻辑为按照请求url中的参数进行判断
```

6. 将配置类加入SpringBoot容器中

```java
 	@Bean
    public LocaleResolver localeResolver(){
        System.out.println("执行国际化");
        return new MyLocaleResolver();
    }
```

注意：

​		==像SpringBoot容器中注入组件时，方法名严格为返回类名的首字母小写格式，否则需要咋在bean标签中添加说明==

```java
  	//注意这里
	@Bean("localeResolver")
    public LocaleResolver mylocaleResolver(){
        System.out.println("执行国际化");
        return new MyLocaleResolver();
    }
```

### 3、登录功能实现



​		首先设置配置文件，取消模板缓存，使用ztrl+F9快捷键，重新编译

1. 前台页面表单域中添加name属性，否则无法进行数据提交

```properties
<input name="username" type="text" class="form-control" placeholder="Username" th:placeholder="#{login.userName}" required="" autofocus="">
			<label class="sr-only" th:text="#{login.passWord}">Password</label>
			<input name="password" type="password" class="form-control" placeholder="Password" th:placeholder="#{login.passWord}" required="">
```

2. 编写控制层代码，进行用户认证

```java
@Controller
public class LoginController {

    @PostMapping(value = "/user/login")
    public String login(@RequestParam("username") String username,
                        @RequestParam("password") String password,
                            Map<String,Object> map,HttpSession session){

        //用户登录验证
        if(!StringUtils.isEmpty(username) && "123456".equals(password)){
            //登录成功
            //将数据放入session中
            session.setAttribute("loginUser","username");
            return "redirect:/main.html";
        }else{
            //登录失败
            map.put("msg","用户名或密码错误");
            return "login";
        }
    }
}
```

3. 编写拦截器，将未登录用户拦截

```java
/**
 * 拦截器配置
 */
public class LoginHandlerInterceptor implements HandlerInterceptor {

    //进入之前检查，是否已经登录
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        Object user = request.getSession().getAttribute("loginUser");
        if(user!=null&&!user.equals("")){
            //已登录
            return true;
        }else{
            //未登录
            request.setAttribute("msg","请先进行登录");
            request.getRequestDispatcher("/index.html").forward(request,response);
            return false;
        }

    }
```

4. 将拦截器添加到容器 中

```java
 //添加拦截器
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        //定义拦截url
        registry.addInterceptor(new LoginHandlerInterceptor()).addPathPatterns("/**")
            //定义放行url  
            .excludePathPatterns("/","/index.html","/user/login");
    }
```

#### 注意：

​		在controller中可以使用视图解析器，在其他类中，不可以使用视图解析器。

​		注意视图解析器的使用，**作为重点**



### 4、CRUD-员工实验

1. 普通crud和restful的crud

|      | 普通CRUD | restfulCRUD      |
| ---- | -------- | ---------------- |
| 添加 | addEmp   | emp{id}---POST   |
| 修改 | updEmp   | emp{id}---PUT    |
| 查询 | selEmp   | emp---GET        |
| 删除 | delEmp   | emp{id}---DELETE |

2. 实验的请求架构

| 实验功能                 | 请求URI  | 请求方式 |
| ------------------------ | -------- | -------- |
| 查询所有员工             | emps     | GET      |
| 查询某个员工             | emp/{id} | GET      |
| 来到添加页面             | emp      | GET      |
| 添加员工                 | emp/{id} | POST     |
| 来到修改页面（显示员工） | emp      | GET      |
| 修改员工                 | emp{id}  | PUT      |
| 删除员工                 | emp/{id} | DELETE   |

### 5、CRUD-员工列表

#### Thymeleaf提取公共元素

```java
定义一个公共片段
<div th:fragment="copy">
&copy; 2011 The Good Thymes Virtual Grocery
</div>
    
在文件中引入该片段
<div th:insert="~{footer :: copy}"></div>   
    
效果
<div id="copy-section">
&copy; 2011 The Good Thymes Virtual Grocery
</div>
    
```



有三种引入片段的方法：

**th:insert**：直接将元素插入本标签

**th:replace**： 依旧使用原来标签，本标签不做显示

**th:include**：替代掉原有的标签

效果

```java
<div th:insert="footer :: copy"></div>
<div th:replace="footer :: copy"></div>
<div th:include="footer :: copy"></div>
    
    
<div>
    <footer>
    &copy; 2011 The Good Thymes Virtual Grocery
    </footer>
</div>
        
<footer>
    &copy; 2011 The Good Thymes Virtual Grocery
</footer>
        
<div>
&copy; 2011 The Good Thymes Virtual Grocery
</div>    
```

#### 错误：

在向模板传值的时候使用的Model也没有导 错包，为什么前台页面接收不到？

最后我将Model换成了Map结果前台就接收到了？

我竟然把list引起来了？ 

这个小错我竟然找了一个小时？

唉 ，笑笑吧

可能还是对idea的不熟练

代码放上来打脸

```
@Controller
public class EmpController {

    @Autowired
    public EmployeeDao employeeDao;
    @GetMapping("/emps")
    public String list(Map<String,Object> map,Model model){

        Collection<Employee> list = employeeDao.getAll();
//        model.addAttribute("emps","list");
        map.put("emps",list);
        return "emp/list";
    }
}
```

### 6、CRUD-员工添加

引入添加页面

```java
<form>
    <div class="form-group">
        <label>LastName</label>
        <input type="text" class="form-control" placeholder="zhangsan">
    </div>
    <div class="form-group">
        <label>Email</label>
        <input type="email" class="form-control" placeholder="zhangsan@atguigu.com">
    </div>
    <div class="form-group">
        <label>Gender</label><br/>
        <div class="form-check form-check-inline">
            <input class="form-check-input" type="radio" name="gender"  value="1">
            <label class="form-check-label">男</label>
        </div>
        <div class="form-check form-check-inline">
            <input class="form-check-input" type="radio" name="gender"  value="0">
            <label class="form-check-label">女</label>
        </div>
    </div>
    <div class="form-group">
        <label>department</label>
        <select class="form-control">
            <option>1</option>
            <option>2</option>
            <option>3</option>
            <option>4</option>
            <option>5</option>
        </select>
    </div>
    <div class="form-group">
        <label>Birth</label>
        <input type="text" class="form-control" placeholder="zhangsan">
    </div>
    <button type="submit" class="btn btn-primary">添加</button>
</form>
```

**错误：**

日期格式化错误，可在全局配置文件中做修改

```properties
#设置日期格式化
spring.mvc.date-format=yyyy-MM-dd
```



### 7、员工修改

put方式请求的发送：

1. 在SpringBoot默认没有开启put请求接收，需要修改全局配置文件

```properties
#表单提交方式
spring.mvc.hiddenmethod.filter.enabled=true
```

2. 在表单域中使用hidden属性，name为“_method” value="put " put不区分大小写

```java
<!--如果emp不为空，发送put请求-->
						<input type="hidden" name="_method" value="put" th:if="${emp!=null}"/>
						<input type="hidden" name="id" th:value="${emp.id}" th:if="${emp!=null}" />
```

3. 控制器使用@**PutMapping**注解接收put请求

```java
@PutMapping("/emp")
    public String updEmp(Employee employee){

        System.out.println("接收到的修改请求："+employee);
        employeeDao.save(employee);
        return "redirect:/emps";
    }
```

### 8、员工删除

注意delete请求的发送以及，js代码的编写语法



```java
//表单
<form id="delete_form"  method="post">
    <input type="hidden" name="_method" value="delete" />
</form>
//标签按钮
<button th:attr="delUri=@{/emp/}+${emp.id}"  
    class="btn btn-sm btn-danger deleteBtn">删除</button>
```

js代码

```javascript
<!--删除事件-->
    <script>
    $(".deleteBtn").click(function () {
        $("#delete_form").attr("action",$(this).attr("delUri")).submit();
        return false;
    })
	</script>
```



## 8、错误处理机制

### 1、SpringBoot默认的错误处理

#### 1、如何定制错误页面

1. 有模板引擎的情况：

   有模板引擎的话，会找模板引擎下的error目录下的4XX或5XX文件。

   也可以获取到错误消息：

   timestemp：时间戳。

   status：状态码

   error：错误提示

   exception：错误对象

   message：异常消息

   errors：JSR303数据校验错误信息都在这里

2. 没有模板的情况：

   没有模板引擎的话，会到静态资源也就是static文件夹下寻找，但是无法获取到页面相应信息。

3. 即没有模板引擎，也没有配静态资源。

   使用SpringBoot默认的错误信息页面。

#### 2、如何定制json数据



## 9、使用外部servlet容器

使用版本为，SpringBoot2.X     Tomcat9

之前使用Tomcat7会有错误问题

# 五、Docker

## 1、安装虚拟机

## 2、安装Docker

```xml
1、内核必须为3.10以上
uname -r 

2、安装docker
yum install docker
3、输入y正确安装
4、启动systemctl docker start
出现版本号表示正常安装完成
[root@localhost ~]# systemctl start docker
[root@localhost ~]# docker -v
Docker version 1.13.1, build cccb291/1.13.1

5、开机自启
[root@localhost ~]# systemctl enable docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.

6、停止docker
[root@localhost ~]# systemctl stop docker

7、查询镜像
[root@localhost ~]# docker search mysql

```

## 3、Docker常用操作

### 1、使用镜像加速器

```linux
# 创建并编辑文件
vi /etc/docker/daemon.json

# 编辑文件内容
{
 "registry-mirrors":["https://6kx4zyno.mirror.aliyuncs.com"]
}

# 重启服务
systemctl daemon-reload
systemctl restart docker.service
重启docker
service docker restart


验证
# docker info
Registry Mirrors:
 https://xxxxxx.mirror.aliyuncs.com

```



出现错误：

```xml
Error response from daemon: Get https://index.docker.io/v1/search?q=restart&n=25: net/http: TLS handshake timeout

```

解决方案：

```
vim /etc/resolv.conf
```

把里面的内容注释，并改为：

```
nameserver 8.8.8.8
nameserver 8.8.8.4
```

重启网络服务

```
systemctl restart network
```

### 2、容器操作

步骤

```xml
1、搜索镜像
docker search tomcat

2、拉取镜像
docker pull tomcat

3、启动一个做了端口映射的tomcat
docker run -d -p 8888:8080 tomcat
-d  后台运行
-p  将主机端口映射到容器内部端口

4为了使用简单，关闭了linux的防火墙
查看防火墙状态
service firewalld status

5关闭防火墙
service firewalld stop

6查看日志内容
docker logs container-name/container-id   (容器id)

```



启动容器中的任务：

```java
docker start id
```



### 3、安装MySql

```xml
1、搜索MySql
docker pull mysql

2、正确启动
docker run --name mysql01 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:tag
```



做了端口映射的启动

```xml
docker run -p 3306:3306  --name mysql02 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7

```



其他高级的操作

```xml
docker run -p 3306:3306  --name mysql02 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
指定mysql的一些参数
```

# 六、SpringBoot与数据访问

## 1、JDBC

创建项目

```xml
 <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
```



配置文件

```yaml
spring:
  datasource:
  	//注意：用户名密码前面没有前缀
    username: root
    password: 123456
    url: jdbc:mysql://192.168.91.128:3307/jdbc
    driver-class-name: com.mysql.cj.jdbc.Driver
```



还可以在配置文件中配置要执行的sql脚本文件

```yaml
schema:
      - classpath:department.sql
	//必须要配置这段话，否则会报错
    initialization-mode: always
```



Spring封装sql的类，以前没有过用过

JdbcTemplate

```java
@Controller
public class HelloController {
    @Autowired
    private JdbcTemplate jdbcTemplate;
    @RequestMapping("/query")
    @ResponseBody
    public Map<String,Object> Query(){
		//查询出一个集合
        List<Map<String, Object>> list = jdbcTemplate.queryForList("select * from department");
        return list.get(0);

    }
}
```



## 2、整合Druid数据源

### 1、配置文件

```yaml
spring:
  datasource:
    #   数据源基本配置
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://192.168.91.128:3307/mybatis
    type: com.alibaba.druid.pool.DruidDataSource
    #   数据源其他配置
    initialSize: 5
    minIdle: 5
    maxActive: 20
    maxWait: 60000
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true
    #   配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
    filters: stat,wall,slf4j
    maxPoolPreparedStatementPerConnectionSize: 20
    useGlobalDataSourceStat: true
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500

```



### 2、引入数据源

```java
@Configuration
public class DruidConfig {

    @ConfigurationProperties(prefix = "spring.datasource")
    @Bean
    public DataSource druid(){
        return new DruidDataSource();
    }

    //配置druid监控
    //配置一个管理后台的Servlet
    @Bean
    public ServletRegistrationBean statViewServlet(){
        ServletRegistrationBean bean = new ServletRegistrationBean(new StatViewServlet(),"/druid/*");
        Map<String,String> initParams = new HashMap<>();

        initParams.put("loginUsername","admin");
        initParams.put("loginPassword","123456");
        initParams.put("allow","");//允许所有访问
        //拒绝谁访问
//        initParams.put("deny","");

        bean.setInitParameters(initParams);
        return bean;
    }

    //配置一个web监控的filter
    @Bean
    public FilterRegistrationBean webStatFilter(){
        FilterRegistrationBean bean = new FilterRegistrationBean();
        bean.setFilter(new WebStatFilter());

        Map<String,String> initParams = new HashMap<>();
        initParams.put("exclusions","*.js,*.css,/druid/*");
        bean.setInitParameters(initParams);
        bean.setUrlPatterns(Arrays.asList("/*"));
        return bean;
    }

}
```





## 3、整合MyBatis

### 1、pom.xml

```xml
  <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.1</version>
        </dependency>
```

![image-20200308203246332](E:\笔记\SpringBoot\springboot入门\springboot入门.assets\image-20200308203246332.png)

步骤：

1. 引入Druid数据源
2. 给数据库建表
3. 创建实体类

### 2、注解版

Mapper文件

```java
@Mapper
public interface DepartmentMapper {
    //使用主键自增，keyProperty属性为，实体类的属性
    @Options(useGeneratedKeys = true,keyProperty = "id")
    @Insert("insert into department (departmentName) values (#{departmentName})")
    int insDept(Department department);

    @Delete("delete from department where id = #{id}")
    int delDept(Integer id);

    @Update("update department set departmentName=#{departmentName} where id = #{id}")
    int updDept(Department department);

    @Select("select * from department where id = #{id}")
    Department selDept(Integer id);
}

```

Controller层代码

```java
@RestController
public class DeptController {
    @Resource
    DepartmentMapper departmentMapper;


    @RequestMapping("/insert")
    public Department insert(Department department){

        departmentMapper.insDept(department);
        return department;
}

    @RequestMapping("/delete/{id}")
    public Department delete(@PathVariable Integer id){
        departmentMapper.delDept(id);
        return null;
    }

    @RequestMapping("/update")
    public Department update(Department department){

        departmentMapper.updDept(department);
        return null;
    }

    @RequestMapping("/select/{id}")
    public Department select(@PathVariable Integer id){

       return  departmentMapper.selDept(id);

    }
}
```

开启驼峰匹配原则

```java
@org.springframework.context.annotation.Configuration
public class MyBatisConfig {

    //配置驼峰解析原则
    @Bean
    public ConfigurationCustomizer configurationCustomizer(){
        return new ConfigurationCustomizer() {
            @Override
            public void customize(Configuration configuration) {
                System.out.println("来了吗");
                configuration.setMapUnderscoreToCamelCase(true);
            }
        };
    }
}

```

### 3、配置文件版

MyBatis配置文件：

```properties
mybatis.configuration.map-underscore-to-camel-case=true
mybatis.mapper-locations=classpath:mapper/*Mapper.xml
```



```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <!--设置驼峰匹配规则-->
    <settings>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
</configuration>
```

Mapper映射文件xml：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.jcq.springboot.mapper.EmployeeMapper">   <!-- 命名空间 -->
    <!-- 对象映射，可以不写 -->
    <!-- 查询功能，resultType 设置返回值类型 -->
    <select id="selById" resultType="com.jcq.springboot.bean.Employee">  <!-- 书写 SQL 语句 -->
        SELECT * FROM employee where id=#{id}
    </select>
</mapper>
```

其他的用法跟正常使用MyBatis相同

## 4、整合JPA

### 1、配置文件

```yml
#配置数据源
spring:
  datasource:
    url: jdbc:mysql://192.168.91.128:3307/jpa
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver

  jpa:
    hibernate:
      #自动建表
      ddl-auto: update
      #显示Sql语句
    show-sql: true
```

### 2、实体类

```java
@Entity
@Table(name = "user")
//如果通过id直接获取对象需要开放这个注解，否则会报懒加载错误
//@JsonIgnoreProperties(value = {"hibernateLazyInitializer","handler"})
public class User implements Serializable {
    @Id //这是一个主键
    @GeneratedValue(strategy = GenerationType.IDENTITY)  //主键自增策略
    private Integer id;
    @Column
    private String username;
    @Column
    private String email;
    
    //getter and setter .....
```

### 3、Repository类

 只需定义一个借口并继承JpaRepository即可

```java
public interface UserRepository extends JpaRepository<User,Integer> {
}
```

### 4、Controller层

```jaVA
@RestController
public class UserController {
    @Autowired
    private UserRepository userRepository;
    @GetMapping("/user/{id}")
    public User getUser(@PathVariable Integer id){
        //User user = userRepository.findById(id).get();
        User user = userRepository.getOne(id);
        return user
    }
}
```

# 七、自定义启动器

步骤：

1. 创建空项目

   1. 1 启动器模块

      1.2  自动配置模块

2. 启动器模块只需依赖自动配置模块即可

```xml
 <dependencies>
        <!--引入自动配置模块-->
        <dependency>
            <groupId>com.jcq.starter</groupId>
            <artifactId>jcq-spring-boot-starter-autoconfigurer</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
    </dependencies>
```

自动配置模块项目目录如下

![image-20200309163136563](E:\笔记\SpringBoot\springboot入门\springboot入门.assets\image-20200309163136563.png)

3. 在自动配置模块中添加业务方法

```java
public class HelloService {

    HelloProperties helloProperties;

    public HelloProperties getHelloProperties() {
        return helloProperties;
    }

    public void setHelloProperties(HelloProperties helloProperties) {
        this.helloProperties = helloProperties;
    }

    public String sayHello(String name){
        return helloProperties.getPrefix()+name+helloProperties.getSuffix();
    }
}

```

4. 编写xxxProperties类

```java
//注解指明，当有项目使用启动器时，配置文件的前缀
@ConfigurationProperties(prefix = "jcq.hello")
public class HelloProperties {
    private String prefix;
    private String suffix;

    public String getPrefix() {
        return prefix;
    }

    public void setPrefix(String prefix) {
        this.prefix = prefix;
    }

    public String getSuffix() {
        return suffix;
    }

    public void setSuffix(String suffix) {
        this.suffix = suffix;
    }
}
```

5. 编写自动配置类

```java
//表明是一个自动配置类
@Configuration
//引用xxxProperties类中的数据
@EnableConfigurationProperties(HelloProperties.class)
//只有引用的项目是Web项目是生效
@ConditionalOnWebApplication
public class HelloAutoConfiguration {

    @Autowired
    HelloProperties helloProperties;
   //将业务类注入到容器中
    @Bean
    public HelloService helloService(){
        HelloService helloService = new HelloService();
        helloService.setHelloProperties(helloProperties);
        return helloService;
    }
}
```

7. 在类路径下创建WEB/INF文件夹，在该路径下创建spring.factories文件

```factories
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.jcq.starter.HelloAutoConfiguration
```

8. 将项目安装到仓库中

9. 其他项目使用此启动器，只需在pom.xml中添加依赖即可

```xml
 <!--引入自定义启动器-->
        <dependency>
            <groupId>com.jcq</groupId>
            <artifactId>jcq-spring-boot-starter</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
```

这样便可使用启动器中的自动配置类的功能

# 
