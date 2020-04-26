https://mp.weixin.qq.com/s?__biz=MzIwMTY0NDU3Nw==&mid=2651938444&idx=2&sn=0cfc68758d3e021b7c7b4727eec5fc4c&chksm=8d0f31c2ba78b8d45a4fec0f65349239cbc8706aa18cd66258cefe299d9ee6638b97a20114b2&scene=21#wechat_redirect

### 1.什么是springboot，springboot有哪些优点?

（1）创建独立的spring应用程序

（2）内嵌tomcat，jetty/undertow容器，无须部署war包

（3）自动化配置，很多都开箱即用

（4）有指标度量，健康检查等

（5）无需代码生成以及xml配置

（6）简化maven配置，比如starter就直接可以获取需要的开发包，通过starter就能以spring application形式运行springboot项目。如spring-boot-starter-web捆绑了SpringMVC依赖，spring-boot-starter-tomcat内嵌了tomcat

### 2.如何重新加载springboot上的更改，而无需重新启动服务器?

https://blog.csdn.net/dismylife/article/details/82380475

使用devtools工具

springboot使用了两个类加载器，对于不会变化的类，如第三方jar包，加载到一个base classloader中，开发者编写的则被加载到restart classloader中，当应用重启时，整个restart classloader被销毁重建，而前者保留下来不必再次加载，提升效率。

devtools默认一些目录下的不会修改，如/static ,/public /templates等，当然也可以自定义这些目录，或者排除这些默认目录，通过spring.devtools.restart.exclude=static/**,public/**方式排除

如果要加入其他的代码路径，则spring.devtools.restart.additional-paths属性修改

### 3.tomcat是怎么内嵌到springboot中的（还有jetty，undertow）

https://blog.csdn.net/heroqiang/article/details/84939176

### 4.springboot配置文件的加载

#### 4.1.启动自动读取配置文件

启动完成发出一个SpringApplicationEvent事件，listener会监听到这个事件，去找到application.properties文件，然后先去找spring.profiles.active属性

https://blog.csdn.net/chengkui1990/article/details/79866499

#### 4.2.手动使用@value注解

@Value("${server.port:8080}")

#### 4.3.手动使用@ConfigurationProperties注解指定配置文件全限名

@ConfigurationProperties("prefix = "com.example.demo"")

#### 4.4.多环境配置下使用指定配置文件

spring.profiles.active=dev

或者启动时加上此参数指定

或者在打包时加上此参数指定

#### 4.5.多环境配置下使用多个配置文件

spring.profiles.active = dev,database

### 5.说一下springboot的自动配置原理

https://blog.csdn.net/weixin_38364973/article/details/82348015

一般启动类上都有@SpringBootApplication注解，它包含了@EnableAutoConfiguration，@SpringBootConfiguration，@ComponentScan三个注解，其中@EnableAutoConfiguration就是开启自动配置的注解

@EnableAutoConfiguration里面又import了一个AutoConfiguationImportSelector类，它的selectImports方法中，会去META-INF/spring.factories加载所有的文件内容，包装成properties对象，再从其中获取到类名对应的值，加载到容器中。

如果要在自动配置时排除一些类，可以在ComponentScan中排除

```java
@ComponentScan(

@Filter(

	type = FilterType.CUSTOM,

	classes = {AutoConfigurationExcludeFilter.class}

))
```

