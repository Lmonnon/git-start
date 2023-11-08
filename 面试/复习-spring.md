#### 谈谈你对spring IOC的理解

ioc（inversion of Control，控制反转），对于spring框架来说，就是由spring来控制对象的生命周期和对象之间的依赖关系。

是 Spring 框架的核心概念，通过依赖注入（Dependency Injection）实现对象及其依赖关系的创建、配置和管理，旨在降低组件间的耦合度、提高模块化、可测试性和可配置性，从而实现更易于维护和扩展的代码。

控制：IOC容器控制对象

反转：依赖对象的获取方式反转了

DI（Dependency Injection，依赖注入）是一种软件设计模式，用于实现控制反转。DI 的核心概念是将对象的依赖关系从代码内部转移到外部，使得组件间的耦合度降低，代码更易于测试和维护。在依赖注入中，对象不需要自己创建或管理它所依赖的其他对象，而是将这些依赖通过构造函数、方法或属性等途径注入到对象中。依赖注入通常通过 IoC 容器（如 Spring IoC 容器）来实现，该容器负责创建、配置和管理对象及其依赖关系。

依赖注入方式：

通过构造函数、set方法、成员变量

@Autowired和@Resource区别



单例：多例@Scope(value="prototype") **作用域**

非懒加载：XML 配置中设置 `lazy-init` 属性为 `true`。默认是false

设计模式：

实现原理：反射

饿汉饱含

#### spring AOP

AOP为面向切面编程，底层是通过动态代理实现，实质上就是将相同逻辑的重复代码横向抽取出来, 拦截对象方法，**对方法进行改造、增强**！比如在 方法执行前、方法返回后、方法前后、方法抛出异常后 等地方进行一定的增强处理，应用场景： 事务、日志、权限、监控打点。

**概念**

AOP为面向切面编程，就是将相同逻辑的重复代码横向抽取出来, 拦截对象方法，**对方法进行改造、增强**！

怎么配、怎么使用aop

**连接点**：在哪里干

**切入点**：在哪里干的集合

通知

环绕通知、前置通知、后置通知、异常通知、最终通知

**执行顺序**

正常：环绕通知、前置通知、后置通知、最终通知

异常：环绕通知、前置通知、异常通知、最终通知

**实现原理**

基于动态代理来实现，在容器启动的时候生成代理实例。默认地，如果使用接口的，用 JDK 提供的动态代理实现，如果没有接口，使用 CGLIB 实现

**使用场景**

事务、日志、权限



#### spring事务

Spring事务
概念
事务是逻辑上的一组操作，要么都执行，要么都不执行。事务在spring的实现

怎么声明spring事务：声明式和编程式

spring事务的7种传播：required



spring事务隔离级别：该隔离级别

四种

spring是怎么管理事务的

事务类型



实现别人接口

try catch 出了错，想执行自己的一段代码

#### 将bean放入spring容器的几种方式

##### 1、@Configuration+@Bean

@Configuration用来声明一个配置类，@Bean用于声明一个bean，将其加入spring容器中。

##### 2、@Componet+@ComponetScan

这种方式也是我们用的比较多的方式，@Componet中文译为组件，放在类名上面，然后@ComponentScan放置在我们的配置类上，然后可以指定一个路径，进行扫描带有@Componet注解的bean，然后加至容器中。

##### 3、@Import

###### 3.1、@Import直接导入类，@Import(Person.class)

###### 3.2、@Import+ImoprtSelecter

```
@Import(MyImportSelector.class)
public class Demo1 {

    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(Demo1.class);
        Person bean = applicationContext.getBean(Person.class);
        System.out.println(bean);
    }

}

class MyImportSelector implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        return new String[]{"com.springboot.pojo.Person"};
    }
}
```

###### 3.3、@Import+ImportBeanDefinitionRegistrar

````
@Import(MyImportBeanDefinitionRegistrar.class)
public class Demo1 {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(Demo1.class);
        Person bean = applicationContext.getBean(Person.class);
        System.out.println(bean);
    }
}
```
class MyImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {
    @Override
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
        // 构建一个beanDefinition, 关于beanDefinition我后续会介绍，可以简单理解为bean的定义.
        AbstractBeanDefinition beanDefinition = BeanDefinitionBuilder.rootBeanDefinition(Person.class).getBeanDefinition();
        // 将beanDefinition注册到Ioc容器中.
        registry.registerBeanDefinition("person", beanDefinition);
    }
}
```
````

###### 3.4、@Import+DeferredImportSelecter

这种方式也需要我们进行实现接口，其实它和@Import的第二种方式差不多，DeferredImportSelector 它是 ImportSelector 的子接口，所以实现的方法和第二种无异。只是Spring的处理方式不同，它和Spring Boot中的自动导入配置文件 延迟导入有关，非常重要。

```
@Import(MyDeferredImportSelector.class)
public class Demo1 {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(Demo1.class);
        Person bean = applicationContext.getBean(Person.class);
        System.out.println(bean);
    }
}
class MyDeferredImportSelector implements DeferredImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        // 也是直接将Person的全限定名放进去
        return new String[]{Person.class.getName()};
    }
}
```

##### 4、	使用FactoryBean接口

```
@Configuration
public class Demo1 {
    @Bean
    public PersonFactoryBean personFactoryBean() {
        return new PersonFactoryBean();
    }

    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(Demo1.class);
        Person bean = applicationContext.getBean(Person.class);
        System.out.println(bean);
    }

}

class PersonFactoryBean implements FactoryBean<Person> {

    /**
     *  直接new出来Person进行返回.
     */
    @Override
    public Person getObject() throws Exception {
        return new Person();
    }
    /**
     *  指定返回bean的类型.
     */
    @Override
    public Class<?> getObjectType() {
        return Person.class;
    }

}
```

##### 5、使用 BeanDefinitionRegistryPostProcessor

```
public class Demo1 {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
        MyBeanDefinitionRegistryPostProcessor beanDefinitionRegistryPostProcessor = new MyBeanDefinitionRegistryPostProcessor();
        applicationContext.addBeanFactoryPostProcessor(beanDefinitionRegistryPostProcessor);
        applicationContext.refresh();
        Person bean = applicationContext.getBean(Person.class);
        System.out.println(bean);
    }
}

class MyBeanDefinitionRegistryPostProcessor implements BeanDefinitionRegistryPostProcessor {

    @Override
    public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry) throws BeansException {
        AbstractBeanDefinition beanDefinition = BeanDefinitionBuilder.rootBeanDefinition(Person.class).getBeanDefinition();
        registry.registerBeanDefinition("person", beanDefinition);
    }
    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
     
    }

}
```





#### http协议

工作过程
例子 当我们访问一个网站时就是 通过三次捂手连上协议
短连接和长链接 优缺点 长链接性能好一点 但支持并发差 
cookie是服务端发给客户端的 用resbangs。cookie
请求头 key：value形式 用/r/n 分开
请求头中的字段 还有Referer 域名不同的请求会自动产生
拦截器中 的 跨域 预处理 默认是半小时 
httpclient 去链接服务端 



#### spring MVC



动态代理和aop分别实现事务



gateway

nginx

location、负载均衡

redis





#### 多线程

线程创建的三种方式

线程池创建的三种方式

创建线程池的7个参数

什么是线程安全的

什么是线程同步

线程同步关键字

sleep和wait区别

线程同步工具

线程的生命周期



#### 数据库事务

delete tran





#### sql优化

对开发人员来说sql优化主要在sql语句上的优化

sql语句优化主要优化查询语句

优化查询语句就是在合适的列上建立合适索引

那些列上适合建索引？



开发人员一般怎么对数据库优化----语句优化----查询语句优化----怎么做：建立合适索引-----从哪建立合适索引----怎么算不当建立索引----索引建立不当有什么坏处----怎么避免不用索引----怎么测试我用没用索引



##### jvm

##### 单例多例

##### 多线程

三种实现方式

线程安全

线程同步

同步工具

线程的生命周期

sleep和wait区别

##### mvc流程



按学生分组，最小值大于80  having







