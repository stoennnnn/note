### 1.mybatis是如何进行分页的，分页插件是什么

一般使用PageHelper分页

1. Mybatis使用RowBounds对象进行分页，它是针对ResultSet结果集执行的内存分页，而非物理分页。可以在sql内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。
2. 分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数。

举例：select * from student，拦截sql后重写为：select t.* from （select * from student）t limit 0，10

### 2.简述mybatis的插件运行原理，以及如何编写一个插件

1. 基于mybatis拦截器实现，plugin

2. 创建动态代理的时候 是按照插件配置顺序创建层层代理对象

3. 执行目标方法，是按逆向顺序执行的

   https://blog.csdn.net/qq_38409944/article/details/82494409

### 3.mybatis的动态sql是做什么的，都有哪些动态sql，能简述一下动态sql执行原理吗

### 4.#{}和${}的区别

1. ${}是Properties文件中的变量占位符，它可以用于标签属性值和sql内部，属于静态文本替换，比如${driver}会被静态替换为	com.mysql.jdbc.Driver
2. \#{}是sql的参数占位符，Mybatis会将sql中的#{}替换为?号，在sql执行前会使用PreparedStatement的参数设置方法，按序给sql的?号占位符设置参数值
3. 优先使用 #{}。因为 ${} 会导致 sql 注入的问题。

### 5.讲一下mybatis的一级缓存和二级缓存

https://www.cnblogs.com/happyflyingpig/p/7739749.html

答案整理：https://blog.csdn.net/llziseweiqiu/article/details/79413130

### 6.mybatis是否支持延迟加载，如果支持，他的实现原理是什么

Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。在Mybatis配置文件中，可以配置是否启用延迟加载lazyLoadingEnabled=true|false。

它的原理是，使用CGLIB创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用a.getB().getName()，拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来，然后调用a.setB(b)，于是a的对象b属性就有值了，接着完成a.getB().getName()方法的调用。这就是延迟加载的基本原理。

当然了，不光是Mybatis，几乎所有的包括Hibernate，支持延迟加载的原理都是一样的。

### 7.为什么说mybatis是半自动ORM映射工具，与全自动区别在哪里

Hibernate属于全自动ORM映射工具，使用Hibernate查询关联对象或者关联集合对象时，可以根据对象关系模型直接获取，所以它是全自动的。而Mybatis在查询关联对象或关联集合对象时，需要手动编写sql来完成，所以，称之为半自动ORM映射工具。

### 8.什么是mybatis的接口绑定，有什么好处？接口绑定有哪几种实现方式，分别怎么实现的？

接口绑定，就是在MyBatis中任意定义接口,然后把接口里面的方法和SQL语句绑定, 我们直接调用接口方法就可以,这样比起原来了SqlSession提供的方法我们可以有更加灵活的选择和设置。

接口绑定有两种实现方式,一种是通过注解绑定，就是在接口的方法上面加上 @Select、@Update等注解，里面包含Sql语句来绑定；另外一种就是通过xml里面写SQL来绑定, 在这种情况下,要指定xml映射文件里面的namespace必须为接口的全路径名。当Sql语句比较简单时候,用注解绑定, 当SQL语句比较复杂时候,用xml绑定,一般用xml绑定的比较多。

### 9.mybatis执行一个sql的过程

1. 通过MapperProxy代理去执行sql方法

2. 首先会去cacheMethod方法缓存中读取，如果有则直接返回，由MapperProxy代理执行

3. 如果没有，则通过MapperMethod生成，MapperMethod有两个重要的静态内部类：

   -  sqlCommand：主要是读取sql和sql命令类型(insert还是select)

   - MethodSignature：主要是读取返回类型和参数解析

4. 通过MapperMethod生成之后，放入方法缓存cacheMethod中，由MapperProxy代理执行

<img src="image/t" alt="img" style="zoom:80%;" />

### 11.mybatis使用jdk动态代理的地方

1. 拦截器中的plugin类使用jdk动态代理
2. 执行sql过程就是通过jdk动态代理的代理对象去执行，MapperProxy类

### 12.Mybatis中的Dao接口和XML文件里的SQL是如何建立关系的？

https://juejin.im/post/5c9f4af6f265da30bf15c45a

1. 创建sqlSource对象，根据sql标签封装sqlSource对象
2. 创建mappedStatement，一个sql标签就对应一个mppeStatement对象，然后缓存到Configuration对象。d：全限定类名+方法名；sqlSource对象
3. @Mapper注解或者配置将包下面所有的类注册到Spring bean中。并且将它们的beanClass设置为MapperFactoryBean。有意思的是，MapperFactoryBean实现了FactoryBean接口，俗称工厂Bean。那么，当我们通过@Autowired注入这个Dao接口的时候，返回的对象就是MapperFactoryBean这个工厂Bean中的getObject()方法对象。
4. Dao接口，就是人们常说的Mapper接口，接口的全限名，就是映射文件中的namespace的值，接口的方法名，就是映射文件中MappedStatement的id值，接口方法内的参数，就是传递给sql的参数。Mapper接口是没有实现类的，当调用接口方法时，接口全限名+方法名拼接字符串作为key值，可唯一定位一个MappedStatement，举例：com.mybatis3.mappers.StudentDao.findStudentById，可以唯一找到namespace为com.mybatis3.mappers.StudentDao下面id = findStudentById的MappedStatement。在Mybatis中，每一个<select>、<insert>、<update>、<delete>标签，都会被解析为一个MappedStatement对象。
5. Dao接口里的方法，是不能重载的，因为是全限名+方法名的保存和寻找策略。
6. Dao接口的工作原理是JDK动态代理，Mybatis运行时会使用JDK动态代理为Dao接口生成代理proxy对象，代理对象proxy会拦截接口方法，转而执行MappedStatement所代表的sql，然后将sql执行结果返回。