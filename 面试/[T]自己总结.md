## Spring

#### Spring生命周期：

````txt
Spring框架的生命周期指的是Spring容器中的Bean对象的创建、初始化、使用和销毁的过程。

1. 实例化：当Spring容器启动时，根据配置文件或注解的定义，创建Bean的实例。

2. 属性注入：Spring容器将配置文件中或注解中定义的属性值注入到Bean的实例中。

3. BeanPostProcessor的前置处理：在Bean实例化和属性注入之后，Spring容器会调用BeanPostProcessor的前置处理方法对Bean进行处理。

4. 初始化：Spring容器会调用Bean的初始化方法，可以通过配置文件或注解的方式指定初始化方法。

5. BeanPostProcessor的后置处理：在Bean的初始化方法调用之后，Spring容器会调用BeanPostProcessor的后置处理方法对Bean进行处理。

6. 使用：Bean被注入到其他对象中，并被其他对象使用。

7. 销毁：当Spring容器关闭时，会调用Bean的销毁方法，可以通过配置文件或注解的方式指定销毁方法。

需要注意的是，Bean的生命周期由Spring容器管理，开发人员只需要关注Bean的定义和使用，不需要手动管理Bean的生命周期。
````
#### IOC

##### 一、什么是ioc? 

IOC（控制反转）是 Spring 框架的核心概念，通过依赖注入（DI）实现对象及其依赖关系的创建、配置和管理，旨在降低组件间的耦合度、提高模块化、可测试性和可配置性，从而实现更易于维护和扩展的代码。

##### 二、怎么放入容器中？

将 Bean 放入 IoC 容器（也称为应用上下文）的方式有多种，以下是其中五种常见的方式：

1. 基于 XML 配置：在 XML 配置文件中使用 `<bean>` 元素定义和配置 Bean。通过加载该 XML 文件创建 ApplicationContext 来将 Bean 放入容器中。

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 声明一个名为 "myBean" 的 Bean -->
    <bean id="myBean" class="com.example.MyBean">
        <!-- 可以在这里配置 Bean 的属性 -->
    </bean>

</beans>
```

2. 基于 Java 配置：使用 `@Configuration` 注解标记一个类作为配置类，在配置类中使用 `@Bean` 注解定义和配置 Bean 的方法。通过注册配置类创建 AnnotationConfigApplicationContext 来将 Bean 放入容器中。

```java
@Configuration
public class MyConfig {

    @Bean
    public MyBean myBean() {
        return new MyBean();
    }

}
```

```java
AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(MyConfig.class);
```

3. 使用组件扫描：使用 `@Component` 或其他相关注解（如 `@Service`、`@Repository` 等）标记要扫描的类，在配置类中启用组件扫描。容器会自动扫描并将被标记的类实例化为 Bean。

```java
@Configuration
@ComponentScan(basePackages = "com.example")
public class MyConfig {

    // 无需显式定义Bean

}
```

```java
AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(MyConfig.class);
```

4. 使用 `@Import` 注解：在配置类中使用 `@Import` 注解引入其他配置类。通过注册主配置类创建 AnnotationConfigApplicationContext ，容器会自动将被引入的配置类中定义的 Bean 放入容器中。

```java
@Configuration
@Import({OtherConfig.class, AnotherConfig.class})
public class MyConfig {
    // ...
}
```

```java
AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(MyConfig.class);
```

5. 通过实现 `BeanFactoryPostProcessor` 接口：编写一个实现了 `BeanFactoryPostProcessor` 接口的类，在其中调用 BeanFactory 的方法来手动注册 Bean，可以在应用上下文启动前对 Bean 进行定制化操作。

```java
public class MyBeanFactoryPostProcessor implements BeanFactoryPostProcessor {

    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
        // 手动注册 Bean
        beanFactory.registerSingleton("myBean", new MyBean());
    }

}
```

```java
AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext();
context.addBeanFactoryPostProcessor(new MyBeanFactoryPostProcessor());
context.refresh();
```

三、单例还是多例（单例，非懒加载）作用域？

默认单例、非懒加载

单例：就是所有的请求都用一个对象来处理，比如我们常用的service和dao层的对象通常都是单例的。

多例：则指每个请求用一个新的对象来处理。

```javascript
<!--scope="singleton"  默认单例，只创建一个对象   懒/饿汉模式-->
<!--scope="prototype"  多例，创建多个对象    饿汉模式-->
<!--lazy-init="false"
    懒加载：对象什么时候创建。
       1.1 默认创建容器的时候创建(非懒加载)
       1.2 获取对象的时候创建
     只对单例（scope为懒汉模式）时起作用,多例时就是在获取对象时创建-->
         
```

四、如何注入？

依赖注入有三种主要的方式：

1. 构造函数注入（Constructor Injection）：通过构造函数来注入依赖。

2. 属性注入（Setter Injection）：通过属性的setter方法来注入依赖。

3. ###### 注解注入

   ###### `@Autowired` 先按照类型再按照名称。 

   `@Autowired` 注解可以被添加到字段、构造函数和 setter 方法上，用于实现依赖注入

   @Pramary ：在某个类上打上这个注解，表示重要主要

   ###### @Resource(name="")

   只能加到字段上

   先按照名称再按照类型。名称和bean的id一直

五、实现模式（DI）？

依赖注入是IOC的一种具体实现方式。它通过将对象之间的依赖关系由外部容器负责建立和维护，而不是由对象自身来创建和管理。依赖注入可以通过构造函数注入、属性注入或接口注入等方式来实现。常见的IOC容器如Spring Framework、JavaEE的CDI等可以通过配置文件或注解的方式描述对象之间的依赖关系，并在运行时动态地将依赖注入到需要的对象中。

六、什么原理？

IOC的实现原理基于反射和配置解析，通过动态创建对象、解决依赖关系和管理生命周期等方式，实现了对象的控制权转移和解耦。

###### 



##### BeanFactory

`BeanFactory` 是 Spring 框架的核心接口之一，用于管理和访问应用程序中的对象（也称为 Bean）。它提供了一种创建、配置和管理 Bean 的机制。

作为 IoC（控制反转）容器的基础，`BeanFactory` 负责实例化和装配 Bean，并通过容器来管理它们的生命周期。以下是 `BeanFactory` 的几个主要功能：

1. 实例化 Bean：`BeanFactory` 负责根据配置信息或注解定义来实例化 Bean 对象。这样可以将应用程序从直接依赖具体类的方式解耦，实现了控制反转。

2. 配置 Bean：`BeanFactory` 可以根据配置文件或注解对 Bean 进行属性注入、构造函数注入等配置操作，使得 Bean 具备所需的状态和行为。

3. 管理 Bean 生命周期：`BeanFactory` 跟踪和管理 Bean 的生命周期，负责在适当的时候初始化、销毁和清理 Bean。

4. 提供依赖注入：`BeanFactory` 可以通过依赖注入（Dependency Injection）的方式将 Bean 之间的依赖关系自动注入，消除了手动处理对象之间的依赖关系的繁琐工作。

5. 支持 Bean 的延迟加载：`BeanFactory` 支持延迟加载（Lazy Loading）策略，只有在需要使用 Bean 时才会进行实例化，提高了应用程序的性能和资源利用率。

`BeanFactory` 的主要实现类是 `DefaultListableBeanFactory`，它是 Spring 框架中最常用的 IoC 容器的默认实现。此外，Spring 还提供了其他类型的 `BeanFactory`，如 `XmlBeanFactory`（基于 XML 配置的 BeanFactory 实现）和 `AnnotationConfigApplicationContext`（基于注解配置的 ApplicationContext 实现），以满足不同场景的需求。

通过使用 `BeanFactory`，您可以更好地组织和管理应用程序中的对象，实现松耦合、可测试和易于维护的代码。



##### BeanPostProcessor

`BeanPostProcessor`（后置处理器）是Spring框架中的一个接口，允许对Bean的创建和初始化过程进行定制化。它提供了在Bean初始化前后执行额外处理的钩子。

当一个Bean被创建时，Spring容器会自动将注册的`BeanPostProcessor`实例应用于该Bean。这些后置处理器可以修改Bean的配置或行为。

`BeanPostProcessor`通常包含`postProcessBeforeInitialization()`和`postProcessAfterInitialization()`等方法。`postProcessBeforeInitialization()`方法允许在Bean初始化之前对其进行自定义处理，而`postProcessAfterInitialization()`则允许在Bean初始化之后进行更多的定制化。

`BeanPostProcessor`的常见用途包括：

```text
1. 对Bean应用自定义注解或行为。 
2. 实现属性验证或初始化逻辑。 
3. 通过动态解析依赖项来辅助依赖注入。
4. 为Bean添加日志记录或监控能力。
```
通过实现`BeanPostProcessor`接口并将其注册到Spring容器中，您可以在Bean的创建和初始化阶段对其进行定制化。



##### AnnotationConfigApplicationContext

`AnnotationConfigApplicationContext` 是Spring框架中的一个类，它提供了一种基于Java注解来引导和配置Spring应用上下文的方式。它允许您使用诸如 `@Component`、`@Configuration`、`@Bean` 等注解来定义和配置您的bean，而不是使用XML配置文件。

通过使用 `AnnotationConfigApplicationContext`，您可以以编程的方式创建应用上下文，并注册所需的配置类或包含注解组件的包。该类会扫描指定的包中的注解类，处理这些注解，并在应用上下文中创建相应的bean定义。

以下是使用 `AnnotationConfigApplicationContext` 的示例：

```java
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MyApp {
    public static void main(String[] args) {
        // 创建 AnnotationConfigApplicationContext 实例
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext();

        // 注册配置类
        context.register(MyConfig.class);

        // 刷新应用上下文，触发bean的创建和装配过程
        context.refresh();

        // 从上下文中获取bean并使用它们
        MyBean myBean = context.getBean(MyBean.class);
        myBean.doSomething();

        // 关闭应用上下文
        context.close();
    }
}
```

在这个示例中，`MyConfig` 是一个包含使用注解的bean定义的配置类。`refresh()` 方法初始化应用上下文并创建bean，`getBean()` 方法从上下文中检索特定的bean。最后，`close()` 方法关闭应用上下文并释放与之关联的任何资源。

使用 `AnnotationConfigApplicationContext` 提供了一种便捷和现代的方式来使用注解配置Spring应用程序，使代码更简洁、更易于管理。

#### AOP

##### 概念

Spring AOP（Aspect-Oriented Programming）是Spring框架提供的一种面向切面编程的支持。它通过在运行时动态地将代码织入到应用程序的特定位置，实现对横切关注点的处理，例如日志记录、性能监控、事务管理等。

1. 切面（Aspect）：切面是一个模块化单元，它包含了与横切关注点相关的通知和切点。通常，一个应用程序可以包含多个切面。
2. 切点（Pointcut）：切点定义了在哪些连接点（Join Point）上应该应用通知。连接点是在应用程序执行过程中能够插入切面的特定位置，例如方法调用或异常抛出等。
3. 连接点（Join Point）：连接点表示在应用程序执行期间可以插入切面的点。实际上，连接点就是程序执行的某个特定位置，如调用方法时、抛出异常时等。
4. 通知（Advice）：通知是切面在连接点上执行的具体操作。它定义了在何时、何地和如何应用切面逻辑。常见的通知类型包括`before`（前置）、`after`（后置）、`around`（环绕）、`after returning`（返回后）和`after throwing`（抛出异常后）等。

##### 如何使用

在项目中使用Spring AOP需要进行以下步骤：

1. 定义切面：创建一个切面类，使用`@Aspect`注解标识，该类包含一组切点和通知。切点定义了需要拦截的方法，通知定义了在切点处执行的逻辑。
2. 配置切面：在Spring的配置文件（如XML配置文件或Java配置类）中，配置切面的实例和相关的切点、通知等信息。
3. 启用AOP：在Spring的配置文件或启动类中，启用AOP的支持。可以使用`<aop:aspectj-autoproxy>`标签或`@EnableAspectJAutoProxy`注解来启用AOP。
4. 应用切面：在需要应用AOP的地方，通过注解或XML配置等方式将切面应用到目标方法或类上。

以上是Spring AOP在项目中的应用方式。通过使用AOP，可以将一些通用的横切关注点与业务逻辑进行解耦，提高代码的复用性、可维护性和可扩展性。

类的功能进行增强；写个切面类，写个切点，$proxy

简单的说就是可配置的动态代理，用来实现数据库事务、插入日志，等各种增强的功能。

##### 如何实现

###### xml方式实现aop

```xml
<!--首先配置一个通知类，里面包含五种通知-->
<bean id="Adivce" class="com.demo02.Advice"/>
    <aop:config>
        <aop:aspect ref="Adivce">
            <!--配置切点-->
            <aop:pointcut id="a1" expression="execution(void com.demo02.EmpServiceImpl.getAllEmp())"/>
            <!--配置五种通知-->
            <aop:before method="before" pointcut-ref="a1"/>
            <aop:after method="after" pointcut-ref="a1"/>
            <aop:around method="around" pointcut-ref="a1"/>
            <aop:after-returning method="returning" pointcut-ref="a1"/>
            <aop:after-throwing method="throwing" pointcut-ref="a1"/>
        </aop:aspect>
    </aop:config>


/*
这段代码是一个使用Spring AOP（面向切面编程）配置的示例。

首先，通过 <bean> 标签将一个名为 "Adivce" 的 bean 定义为 com.demo02.Advice 类的实例。

然后，在 <aop:config> 标签中配置了一个切面（aspect），使用 ref 属性引用了上述的 "Adivce" bean。

接下来，通过 <aop:pointcut> 标签配置了一个切点（pointcut），id 设置为 "a1"，expression 属性指定了切点的表达式，即指定了要切入的目标方法。这里的表达式是 execution(void com.demo02.EmpServiceImpl.getAllEmp())，表示切入 com.demo02.EmpServiceImpl 类的 getAllEmp() 方法。

然后，使用 <aop:before>、<aop:after>、<aop:around>、<aop:after-returning> 和 <aop:after-throwing> 标签配置了五种通知（advice）：

<aop:before> 标签配置了一个前置通知（before advice），method 属性指定了 "before" 方法，pointcut-ref 属性引用了上述的切点 "a1"。
<aop:after> 标签配置了一个后置通知（after advice），method 属性指定了 "after" 方法，pointcut-ref 属性引用了上述的切点 "a1"。
<aop:around> 标签配置了一个环绕通知（around advice），method 属性指定了 "around" 方法，pointcut-ref 属性引用了上述的切点 "a1"。
<aop:after-returning> 标签配置了一个返回通知（after-returning advice），method 属性指定了 "returning" 方法，pointcut-ref 属性引用了上述的切点 "a1"。
<aop:after-throwing> 标签配置了一个异常通知（after-throwing advice），method 属性指定了 "throwing" 方法，pointcut-ref 属性引用了上述的切点 "a1"。
这样，通过配置切面和通知，就可以在目标方法执行前后或发生异常时插入相应的逻辑处理。
*/
```

###### 注解方式实现aop

```javascript
    <!--开启注解驱动-->
    <context:annotation-config/>
    <!--指定要扫描的包-->
    <context:component-scan base-package="com.demo03"/>
    <!--开启aop注解支持-->
    <aop:aspectj-autoproxy/>
    //在通知类上打注解 @Aspect
    //定义切点execution（返回值类型  包名.类名.方法名（..）） *号代表任意
    @Pointcut("execution(* com.demo03.*.*(..))")
    public void aa(){
    }
```

原理动态代理(默认)   cdlib(没实现接口只能用)   使用场景 实现事务 插入日志 权限判断

只要一个方法被增强这个类就被增强

**连接点**---通知执行的时机（之前之后）

通知和切点写在切面里 

##### 通知

在AOP中，通知（Advice）是切面的一部分，用于定义在特定的连接点（Join Point）上执行的操作。通知可以在方法调用前、方法调用后、方法抛出异常时或方法返回结果之后等不同的时机执行。

以下是常见的几种AOP通知类型：

1. 前置通知（Before advice）：在目标方法执行之前执行的通知。
2. 后置通知（After advice）：在目标方法执行之后（无论是否发生异常）执行的通知。
3. 返回通知（After returning advice）：在目标方法成功返回结果之后执行的通知。
4. 异常通知（After throwing advice）：在目标方法抛出异常之后执行的通知。
5. 环绕通知（Around advice）：在目标方法执行前后都可以执行的通知，可以控制目标方法的执行过程。

通过使用这些不同类型的通知，开发人员可以将横切关注点与核心业务逻辑分离，并且能够在需要的时候注入额外的行为。

请注意，AOP框架如Spring AOP提供了对这些通知类型的支持，并允许开发人员声明式地定义和配置它们。

在AOP中，不同类型的通知按照以下执行顺序进行：

```
环绕通知（前半部分） -> 前置通知 -> 目标方法执行 -> 返回通知/异常通知 -> 后置通知 -> 环绕通知（后半部分） 
```
需要注意的是，环绕通知可以在目标方法执行前后插入自定义的逻辑，并且可以选择是否调用目标方法，因此它具有更大的灵活性和控制权。其他通知类型的执行顺序相对固定，会根据目标方法的执行结果或异常情况进行调用。	

#### 设计模式

##### 单例模式

1、单例模式基本定义：程序运行时，在java虚拟机中只存在该类的一个实例对象。

2、饿汉式单例：在该类初始化的时候就创建实例对象，线程是安全的。

3、饱/懒汉式单例：首次使用单例实例的时候创建，之后使用时再判断单例实例是否已创建，如果没有则创建实例。



##### 怎么写一个单例？

写一个公共的类，有一个私有的静态变量，一个私有的构造方法 ，一个静态的构造方法

饿汉式：一开始就 new 出来 每次调用这个方法就返回这个 instance

懒汉式：一开始的instance是null，调用方法时判断如果没有就new一个并返回，如果有了就直接返回



设计模式是一套被广泛接受和使用的解决特定问题的最佳实践方法。设计模式提供了一种在软件开发过程中常见问题的解决方案，它们是从实践中总结出来的，并经过验证和证明可以提高代码的可重用性、可维护性和可扩展性。

设计模式通常被分为三个主要类别：

1. 创建型模式（Creational Patterns）：这些模式关注对象的创建过程，以便在不暴露对象创建逻辑的情况下实例化对象。常见的创建型模式包括工厂模式、抽象工厂模式、单例模式、建造者模式和原型模式。

2. 结构型模式（Structural Patterns）：这些模式关注对象之间的组合，以形成更大的结构，并提供了一种简化对象之间交互的方法。常见的结构型模式包括适配器模式、装饰器模式、代理模式、组合模式、桥接模式、外观模式和享元模式。

3. 行为型模式（Behavioral Patterns）：这些模式关注对象之间的通信和职责分配，以便更好地组织和管理代码。常见的行为型模式包括策略模式、观察者模式、模板方法模式、迭代器模式、命令模式、状态模式、备忘录模式、中介者模式和访问者模式。

每种设计模式都有其特定的用途和适用场景，可以根据具体的需求选择合适的设计模式。使用设计模式可以提高代码的可读性、可维护性和可扩展性，并帮助开发人员更好地组织和管理代码。

**Spring框架使用了多种设计模式，以下是一些常见的设计模式在Spring中的应用：**

1. 单例模式（Singleton Pattern）：Spring的Bean默认是单例的，通过容器管理对象的创建和依赖注入，确保每个Bean只存在一个实例。

2. 工厂模式（Factory Pattern）：Spring使用工厂模式创建和管理Bean，通过配置文件或注解来定义Bean的创建方式，从而实现对象的解耦和灵活性。

3. 代理模式（Proxy Pattern）：Spring AOP（面向切面编程）使用了代理模式，通过动态代理技术实现对目标对象的增强，如事务管理、日志记录等。

4. 观察者模式（Observer Pattern）：Spring的事件机制使用了观察者模式，通过定义事件和监听器，实现对象之间的解耦和事件的处理。

5. 模板模式（Template Pattern）：Spring的JdbcTemplate和HibernateTemplate等模板类使用了模板模式，将一些通用的操作封装在模板类中，简化了数据库操作和持久化操作的编码。

6. 适配器模式（Adapter Pattern）：Spring的适配器模式用于将不同的接口适配到统一的接口，使得不同的组件可以协同工作。

7. 策略模式（Strategy Pattern）：Spring的策略模式用于实现不同的算法或策略的切换，通过配置文件或注解来选择不同的实现。

8. 依赖注入（Dependency Injection）：Spring的核心思想就是依赖注入，通过注入依赖关系来实现对象之间的解耦和灵活性。

这些设计模式的应用使得Spring框架具有高度的灵活性、可扩展性和可维护性，提供了一种优雅的方式来管理对象的创建和依赖关系。



#### Spring事务

**（就是用AOP实现的）**

概念：

Spring事务是Spring框架提供的一种管理数据库事务的机制。它通过封装底层的事务管理API，提供了更简单、更灵活的事务管理方式。

在Spring中，可以使用声明式事务管理或编程式事务管理来管理事务。

1. 声明式事务管理：通过在方法或类上添加@Transactional注解，可以将方法或类标记为需要事务管理的。在方法执行时，Spring会自动为其创建一个事务，并在方法执行前开启事务，在方法执行后根据方法的执行情况决定是提交事务还是回滚事务。声明式事务管理可以将事务的管理与业务逻辑解耦，使得代码更加简洁，同时也提供了更高的可重用性和可维护性。

2. 编程式事务管理：通过编写代码来显式地控制事务的开启、提交和回滚。在编程式事务管理中，需要手动获取事务管理器，并在代码中显式地调用事务管理器的方法来控制事务的行为。编程式事务管理相对于声明式事务管理更加灵活，可以根据具体的业务需求进行精细化的事务控制，但也会增加代码的复杂度和维护成本。

Spring事务管理支持多种事务管理器，包括JDBC事务管理器、Hibernate事务管理器、JTA事务管理器等，可以根据具体的数据访问技术选择合适的事务管理器。

通过Spring事务管理，可以实现数据库事务的ACID特性，确保数据的一致性和完整性。同时，Spring事务管理还提供了异常处理和事务回滚机制，可以有效地处理事务中的异常情况。
```java
@Transactional(transactionManager = "dataSourceTransactionManager",rollbackFor = Exception.class,isolation = Isolation.*REPEATABLE_READ*,propagation = Propagation.*SUPPORTS*)
```
##### 事务传播行为

Spring框架定义了七种事务传播行为（Transaction Propagation Behavior），开发人员可以根据实际需求选择合适的传播行为来管理事务。下面是这七种事务传播行为：

1. REQUIRED：如果当前存在事务，则加入该事务，如果不存在事务，则创建一个新事务。这是默认的传播行为。

2. SUPPORTS：如果当前存在事务，则加入该事务，如果不存在事务，则以非事务的形式执行。

3. MANDATORY：强制要求当前存在事务，如果当前没有事务，则抛出异常。

4. REQUIRES_NEW：无论当前是否存在事务，都创建一个新事务，并在新事务内执行。如果当前存在事务，则挂起该事务，并在新事务结束后恢复。

5. NOT_SUPPORTED：以非事务的形式执行操作，如果当前存在事务，则挂起该事务，并在操作完成后恢复。

6. NEVER：禁止当前方法在事务中执行，如果当前存在事务，则抛出异常。

7. NESTED：如果当前存在事务，则在嵌套事务内执行。如果父事务存在，子事务将成为父事务的一部分。如果父事务回滚，子事务也会回滚，但是子事务可以独立进行提交。

这些事务传播行为可以通过在@Transactional注解中指定propagation属性来配置。例如，使用@Transactional(propagation = Propagation.REQUIRED)来表示使用REQUIRED传播行为。

正确选择合适的事务传播行为对于确保数据一致性和避免并发问题非常重要，开发人员应根据具体业务需求仔细考虑和使用适当的传播行为。

*常用REQUIRED其他不常用        默认情况运行期异常(runtime exception)才会回滚(rollback)  可以自己编辑*



**隔离级别**是指在数据库中，事务间相互隔离程度的一种度量标准。常见的隔离级别有以下四种：

1. 读未提交（Read Uncommitted）：事务中的修改可以被其他事务读取，即一个事务可以读取到另一个事务未提交的数据。这种隔离级别会导致脏读（Dirty Read）问题。

2. 读已提交（Read Committed）：一个事务只能读取到已经提交的数据，即一个事务只能读取到其他已经提交的事务的修改结果。这种隔离级别会导致不可重复读（Non-repeatable Read）问题。

3. 可重复读（Repeatable Read）：在一个事务中，多次读取同一数据的结果是一致的，即一个事务在执行期间，不会读取到其他事务对同一数据的修改结果。这种隔离级别会导致幻读（Phantom Read）问题。

4. 串行化（Serializable）：最高级别的隔离级别，确保事务串行执行，即一个事务在执行期间完全隔离于其他事务。这种隔离级别可以避免脏读、不可重复读和幻读问题，但会影响并发性能。

不同的隔离级别在保证数据一致性和并发性之间存在着权衡。根据具体的业务需求和性能要求，选择合适的隔离级别是很重要的。

MySQL默认是可重复读     Oracle默认是读已提交





## SpringMVC

SpringMVC是一种基于Java的Web开发框架，它采用了MVC（Model-View-Controller）的设计模式来组织和管理应用程序的各个部分。

##### 工作流程：

Spring MVC是一款在Java平台上构建Web应用程序的开源框架。它遵循MVC（模型-视图-控制器）设计模式，通过将应用程序分为不同的组件来实现数据、展示和控制的分离。

下面是Spring MVC的工作流程：

1. 用户发送请求到前端控制器（DispatcherServlet）。
2. 前端控制器请求处理器映射器（HandlerMapping）去查找处理器（Handler）。

3. 找到以后处理器映射器（HandlerMappering）向前端控制器返回执行链（HandlerExecutionChain）。

4. 前端控制器（DispatcherServlet）调用处理器适配器（HandlerAdapter）去执行处理器（Handler）。

5. 处理器适配器去执行Handler。

6. 处理器执行完给处理器适配器返回ModelAndView。

7. 处理器适配器向前端控制器返回ModelAndView。

8. 前端控制器请求视图解析器（ViewResolver）去进行视图解析。

9. 视图解析器向前端控制器返回View。

10. 前端控制器对视图进行渲染。

11. 前端控制器向用户响应结果。

这个过程中，Spring MVC提供了一些拦截器Interceptor和处理器适配器HandlerAdapter等组件，可以用于实现各种功能，如身份验证、日志记录和异常处理等。

总体而言，Spring MVC的工作流程是通过前端控制器DispatcherServlet来协调各个组件的工作，实现请求的分发和处理。这种模式可以使开发者更加专注于业务逻辑的实现，提高了代码的可维护性和可扩展性。

##### 常用注解：

```text
在Spring MVC中，有一些常用的注解用于标识和配置控制器（Controller）类和处理器方法。下面是一些常用的注解：

@Controller：用于标识一个类为控制器（Controller）类。
@RequestMapping：用于映射请求路径和处理器方法。可以用于类级别和方法级别，用于指定请求的URL路径。
@RequestBody 是 Spring MVC 提供的注解之一，用于将 HTTP 请求的内容（请求体）绑定到方法参数上
@ResponseBody：用于将方法的返回值直接作为响应体返回给客户端。
@RestController注解相当于@Controller注解和@ResponseBody注解的组合。
@GetMapping、@PostMapping、@PutMapping、@DeleteMapping：这些注解是@RequestMapping的缩写形式，用于映射GET、POST、PUT、DELETE请求。

@PathVariable：用于将URL路径中的变量绑定到方法参数上。
@RequestParam：用于将请求参数绑定到方法参数上。默认必须写，如果想要将@RequestParam注解的参数设置为非必需的，可以通过设置required属性为false来实现。
@CookieValue：用于将Cookie值绑定到方法参数上。
@RequestHeader：用于将请求头信息绑定到方法参数上。
@ResponseStatus：用于指定响应状态码和原因短语。
@SessionAttributes：用于指定哪些模型属性需要存储到会话中。
@ModelAttribute：用于将请求参数绑定到JavaBean对象上，并将该对象添加到模型中。
@RequestAttribute：用于将请求属性绑定到方法的参数上。

自定义异常
@ControllerAdvice：自定义异常
@ExceptionHandler：用于处理异常，可以在控制器中定义一个方法来处理指定类型的异常。
跨域
@CrossOrigin
以上是一些常用的注解，它们可以帮助我们更方便地配置和使用Spring MVC框架。在实际开发中，根据具体的需求和场景，还可以使用其他的注解来进行更精细的配置和处理。
```

下面是一个简化的示例代码，展示了Spring MVC的执行流程：

```java
@Controller
@RequestMapping("/hello")
public class HelloController {

    @RequestMapping("/world")
    public ModelAndView helloWorld() {
        ModelAndView modelAndView = new ModelAndView("hello");
        modelAndView.addObject("message", "Hello, World!");
        return modelAndView;
    }
}

// DispatcherServlet配置
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[] { AppConfig.class };
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[] { WebConfig.class };
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/" };
    }
}

// 应用配置
@Configuration
@ComponentScan(basePackages = "com.example")
public class AppConfig {
    // 配置其他Bean
}

// Web配置
@Configuration
@EnableWebMvc
public class WebConfig extends WebMvcConfigurerAdapter {
    // 配置视图解析器、处理器映射器等
}
```

在上面的示例中，我们定义了一个`HelloController`，其中的`helloWorld`方法处理了请求`/hello/world`，并返回一个包含`message`属性的ModelAndView对象。在`MyWebAppInitializer`中，我们配置了DispatcherServlet，并指定了应用配置和Web配置的类。在应用配置类`AppConfig`中，我们可以配置其他的Bean。在Web配置类`WebConfig`中，我们可以配置视图解析器、处理器映射器等。

需要注意的是，上述示例是一个简化的流程，实际的Spring MVC执行流程还包括其他一些细节和扩展点，例如拦截器、数据绑定、数据验证等。但总体来说，以上是Spring MVC的基本执行流程。





## 数据库

##### InnoDB

InnoDB是一种关系型数据库管理系统（RDBMS）MySQL中的存储引擎，它在MySQL中负责处理数据的存储和管理。以下是关于InnoDB的一些重要特点：

1. 事务支持：InnoDB是一个ACID（原子性、一致性、隔离性和持久性）兼容的存储引擎，它提供了强大的事务支持，可以确保数据的完整性和一致性。

2. 行级锁定：InnoDB使用行级锁定来实现并发控制，这意味着多个用户可以同时访问不同的行数据而不会互相阻塞。这提高了并发性能和并发处理能力。

3. 外键约束：InnoDB支持外键约束，可以在表之间建立关联关系，并且可以通过外键约束来维护数据的完整性。

4. MVCC（多版本并发控制）：InnoDB使用MVCC来处理并发事务。它可以在读取和写入操作时对数据进行版本控制，提供了非阻塞的读取操作，并避免了读取冲突。

5. 数据缓存：InnoDB通过使用缓冲池来缓存数据页，以提高对数据的读取和写入性能。缓冲池可以减少磁盘I/O操作，加快数据的访问速度。

6. 崩溃恢复：InnoDB具有崩溃恢复机制，可以在数据库异常关闭后自动进行恢复操作，确保数据的持久性。

7. 支持全文搜索：InnoDB引擎从MySQL 5.6版本开始支持全文搜索功能，使得在存储和查询大量文本数据时更加方便和高效。

总体而言，InnoDB是MySQL中最常用的存储引擎之一，它提供了强大的事务支持、行级锁定和MVCC等特性，适用于需要并发控制和数据完整性的应用场景。

##### 数据库优化

SQL优化是指通过优化SQL语句的编写和数据库的结构，提高数据库查询的性能和响应速度。以下是一些常见的SQL优化技巧：

1. 使用索引：索引可以加速查询操作。可以通过在查询字段上创建合适的索引来提高查询效率。但是，过多的索引会影响数据库的性能和响应速度，所以需要根据实际情况选择合适的索引。索引加在什么地方？使用不当会失效  在前边加%和_模糊查询  隐式类型转换  函数运算 复合索引没使用第一列？如何测试用了索引  用explain测试一下？表有激烈

2. 避免全表扫描：全表扫描是指对整个表进行扫描，通常会影响数据库的性能和响应速度。可以通过使用索引、分区等方式来避免全表扫描。

3. 避免使用SELECT *：SELECT *会查询所有的列，通常会影响数据库的性能和响应速度。可以只查询需要的列，以提高查询效率。

4. 使用合适的连接方式：在进行多表查询时，可以选择合适的连接方式，如INNER JOIN、LEFT JOIN、RIGHT JOIN等。不同的连接方式会影响查询效率，需要根据实际情况选择合适的连接方式。

5. 使用合适的聚合函数：在进行聚合查询时，可以选择合适的聚合函数，如COUNT、SUM、AVG等。不同的聚合函数会影响查询效率，需要根据实际情况选择合适的聚合函数。

6. 避免使用子查询：子查询通常会影响数据库的性能和响应速度。可以通过使用联合查询、临时表等方式来避免使用子查询。

7. 使用合适的数据类型：选择合适的数据类型可以减少存储空间和提高查询效率。例如，使用INT代替VARCHAR存储数字类型的数据。

8. 使用批量操作：对于批量插入、更新、删除等操作，可以使用批量操作来提高数据库的性能和响应速度。

9. 优化数据库结构：良好的数据库结构可以提高数据库的性能和可维护性。例如，使用适当的范式、避免冗余数据、使用外键等。

10. 使用数据库性能分析工具：可以使用数据库性能分析工具来分析SQL语句的性能和响应时间，以找出性能瓶颈并进行优化。常见的数据库性能分析工具有Explain、SQL Profiler等。

慢查询测一测

## SSM整合

在SSM框架中，通常会使用三个配置文件来配置Spring、Spring MVC和Servlet容器，分别是spring.xml、springmvc.xml和web.xml。下面是它们的具体配置方式：

在web.xml配置文件中，首先配置了一个DispatcherServlet，用于处理所有的HTTP请求。在DispatcherServlet的配置中，通过<context-param>标签指定了spring.xml和springmvc.xml的路径。然后，配置了Servlet的映射，将所有的请求都交给DispatcherServlet处理。最后，配置了Spring的ContextLoaderListener，用于加载Spring容器。三个域对象；

在spring.xml配置文件中，通常会配置数据源、事务管理器等与Spring相关的内容。同时，需要配置MyBatis的SqlSessionFactory和Mapper扫描，以便实现与数据库的交互。最后，使用Spring的组件扫描配置来自动扫描并装配Spring的组件。

在springmvc.xml配置文件中，通常会配置Spring MVC相关的内容。首先，配置<mvc:annotation-driven/>来启用Spring MVC的注解驱动。然后，配置视图解析器和静态资源处理，以便实现视图的解析和静态资源的访问。拦截器配置，跨域配置；最后，使用Spring MVC的组件扫描配置来自动扫描并装配Spring MVC的组件。

1. spring.xml配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 配置数据源、事务管理器等 -->
    ...

    <!-- 配置MyBatis的SqlSessionFactory -->
    ...

    <!-- 配置Mapper扫描 -->
    ...

    <!-- 配置Spring的组件扫描 -->
    ...

</beans>
```



2. springmvc.xml配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- 配置Spring MVC的注解驱动 -->
    <mvc:annotation-driven/>

    <!-- 配置视图解析器 -->
    ...

    <!-- 配置静态资源处理 -->
    ...

    <!-- 配置Spring MVC的组件扫描 -->
    ...

</beans>
```



3. web.xml配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
         http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!-- 配置DispatcherServlet -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring.xml,classpath:springmvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!-- 配置Spring监听器 -->·
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring.xml</param-value>
    </context-param>

</web-app>
```

容器的关系spring 和springmvc

spring 扫描service springMvc扫描controller+

springMVC中的service是原始的service，不支持事务

通过以上的配置，可以实现Spring、Spring MVC和Servlet容器的整合和功能的正常运行。其中，spring.xml配置文件用于配置Spring相关的内容，springmvc.xml配置文件用于配置Spring MVC相关的内容，web.xml配置文件用于配置Servlet容器相关的内容。



## Redis	（默认端口6379）

#### 基本格式：

5种数据类型：String，Hash，List，Set（无序集合），Sorted set（有序集合）

是对于value类型来说   key都是字符串的

#### 常用命令：

Redis是一个流行的开源内存数据库，它提供了丰富的命令用于操作和管理数据。以下是一些Redis的常用命令：

0. **keys  *** :   查看所有key	

1. **SET key value**：设置指定key的值为value。
2. **GET key**：获取指定key的值。
3. **DEL key**：删除指定的key。
4. E**XISTS key**：检查指定的key是否存在。
5. KEYS pattern：根据指定的模式查找所有匹配的key。
6. **EXPIRE key seconds**：为指定的key设置过期时间，单位为秒。
7. TTL key：获取指定key的剩余过期时间。
8. **INCR key**：将指定key的值增加1。
9. **DECR key**：将指定key的值减少1。
10. **HSET key field value**：将指定哈希表key中的字段field设置为value。
11. **HGET key field**：获取指定哈希表key中字段field的值。
12. **HMSET key field1 value1 field2 value2** ...：同时设置多个哈希表字段的值。
13. **HMGET key field1 field2** ...：获取指定哈希表key中多个字段的值。
14. **LPUSH key value1 value2** ...：将一个或多个值插入列表key的左侧。
15. **RPUSH key value1 value2** ...：将一个或多个值插入列表key的右侧。
16. **LPOP key**：移除并返回列表key的左侧第一个元素。
17. **RPOP key**：移除并返回列表key的右侧第一个元素。
18. **SADD key member1 member2** ...：将一个或多个成员加入到集合key中。
19. **SMEMBERS key**：获取集合key中的所有成员。
20. **ZADD key score1 member1 score2 member2** ...：将一个或多个成员及其分数添加到有序集合key中。

持久化机制：

Redis 为了保证效率，数据缓存在了内存中，但是会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件中，以保证数据的持久化。

Redis 的持久化策略有两种：

* RDB：快照形式是直接把内存中的数据保存到一个 dump.rdb 的文件中，定时保存，保存策略。

* AOF：把所有的对 Redis 的服务器进行修改的命令都存到一个文件里，命令的集合。

  Redis 默认是快照 RDB 的持久化方式。

当 Redis 重启的时候，它会优先使用 AOF 文件来还原数据集，因为 AOF 文件保存的数据集通常比 RDB 文件所保存的数据集更完整。你甚至可以关闭持久化功能，让数据只在服务器运行时存。

什么样的数据放在redis里面：

1. **会话缓存**：如果你的应用程序使用会话来跟踪用户，你可以将会话数据存储在Redis中，以减少数据库的负载。
2. **全页缓存**：许多网站会在Redis中缓存整个HTML页面，以便在下一次请求时直接从Redis中提取，而不是从数据库中重新生成。
3. **用户信息/身份验证**：你可以在Redis中存储用户信息，如用户名，邮箱，电话号码等。当然，你也需要确保这些信息的安全性。
4. **队列和消息系统**：Redis具有原生的队列和发布订阅功能，可以用于实现异步任务或消息队列。
5. **排行榜或计数器**：例如一个网站的页面访问次数，或者一个游戏的玩家得分排行榜。
6. **实时分析**：如果你正在对数据进行实时分析，你可以使用Redis作为一个缓冲区，将数据先存储在Redis中，然后周期性地将其写入数据库。
7. **缓存数据库查询**：如果你的应用程序经常对数据库进行一些重复的查询，那么将查询结果存储在Redis中会大大提高效率。
8. **访问频率高**：如果某些数据被频繁访问，将它们存储在内存中可以大大提高应用的性能。例如，用户会话、热门内容的详情等。
9. **读取操作远多于写入操作**：Redis适合读密集型的场景。例如，用户的基本信息，产品的详细信息，价格等，这些信息的读取次数通常远多于修改次数。

10. **数据量不是太大**：由于Redis是内存数据库，因此并不适合存储大量的数据。一般来说，数据量应该能够适应你的服务器内存大小。

11. **对数据一致性要求不高**：因为Redis通常作为缓存使用，数据可能在某些情况下（例如，缓存失效或者服务器重启）会丢失，因此，对于需要高度一致性的数据，如交易系统中的订单信息，就不适合存储在Redis中。

12. **数据可以被序列化**：Redis存储的是键值对，其中的值需要能够被序列化为字符串，所以你可以存储的数据类型受此限制。

###### 如何将数据库中的数据存到redis中：

查询数据的时候先去redis中查询，没有的话，去数据库中查询，并存到redis中，如果要修改数据直接删掉redis中的数据

###### 确保数据库和Redis中的数据保持同步是一项重要任务，以下是一些常用的解决方案：

1. 在更新数据库时同时更新Redis：在应用程序中，当对数据库进行插入、更新或删除操作时，确保同时更新Redis中对应的数据。这可以通过在数据库操作后，使用相应的Redis命令进行数据同步来实现。

2. 使用数据库触发器（Trigger）：数据库触发器是一种在数据库中定义的特殊程序，它会在指定的数据库事件（如插入、更新、删除）发生时自动执行。您可以编写触发器来捕获数据库变更，并使用Redis命令将相应的数据同步到Redis中。

3. 定时批量同步：定期扫描数据库，将新增、更新或删除的数据批量同步到Redis中。您可以设置一个定时任务或使用调度工具来执行此操作。请注意，该方法可能导致Redis中的数据与数据库存在一定的延迟。

4. 使用消息队列：当数据库发生变化时，将变更的数据放入消息队列中，然后由另一个进程或服务消费消息并将数据同步到Redis中。这种方式可以异步地处理数据同步，减少对数据库性能的影响，并提供更好的可伸缩性。

无论您选择哪种解决方案，请确保在数据更新时保持一致性，并处理可能出现的异常情况，如网络故障或数据同步失败。根据具体情况选择最适合您应用程序的方法。

##### redis缓存

**缓存击穿** ：是指在缓存中没有数据（数据过期了），但是在数据库中有数据。这时由于并发访问的用户数较多，在缓存中查询不到数据后同时去数据库中查询，造成数据库压力较大。

解决：

1. 设置热点数据永不过期

2. 加互斥锁。击穿的原因之一是并发查询，此时加个互斥锁，让用户排队查询。

**缓存穿透** ：是指缓存和数据库中都没有的数据。用户此时发起请求，但是因为缓存和数据库中都没有数据，导致用户一直不断请求这个数据，使数据库压力过大。

解决：用户查询没有结果的数据后返回一个代表空值的值。

**缓存雪崩** ：是指缓存数据大批量同时到期，而查询的数据量过大，导致数据都从数据库中查询，使数据库压力过大。和缓存击穿不同的是，雪崩是大批量不同的数据同时过期了，击穿是并发查询同一条数据。

解决：

1. 将缓存过期时间设置随机，避免同时过期的情况。
2. 如果缓存数据库是分布式部署，将热点数据均匀分布在不同的缓存数据库中。
3. 设置热点数据永远不过期。

## Redis分片

Redis分片是一种将数据分散存储在多个Redis节点上的技术，通过将数据分散存储，可以提高系统的并发处理能力和存储容量。

在Redis分片中，数据被分成多个片（shard），每个片都存储在不同的Redis节点上。分片的方式可以根据业务需求选择不同的策略，常见的分片策略有以下几种：

1. 哈希分片：根据数据的键（key）进行哈希计算，将计算结果映射到不同的节点上。相同的键会被映射到相同的节点上，保证相同的数据总是存储在同一个节点上。

2. 范围分片：根据数据的键的范围进行分片，例如按照键的字母顺序或者数值大小进行分片。范围分片可以保证相邻的数据被存储在相邻的节点上，提高数据的局部性。

3. 一致性哈希分片：使用一致性哈希算法将数据映射到不同的节点上。一致性哈希算法可以保证在节点的增加或减少时，数据的迁移量最小，减少数据迁移的开销。

在Redis分片中，客户端根据分片策略将数据请求发送给相应的节点进行处理。对于读操作，客户端可以直接发送给对应的节点；对于写操作，客户端可能需要将数据发送给多个节点进行存储。

需要注意的是，Redis分片并不提供数据的复制和高可用性，因此在使用Redis分片时，需要结合Redis的主从复制或者集群模式来提供数据的冗余和故障恢复能力。

总的来说，Redis分片是一种将数据分散存储在多个Redis节点上的技术，通过将数据分片存储，可以提高系统的并发处理能力和存储容量。不同的分片策略可以根据业务需求选择，同时需要结合数据的复制和高可用性机制来提供数据的冗余和故障恢复能力。

## 分布式锁

**分布式锁**是一种常见的处理分布式系统并发访问问题的技术，它可以保证在分布式系统中的多个节点上对某个共享资源进行互斥访问。

实现分布式锁的方式有很多种，下面介绍几种常用的实现方式：

1. 基于数据库的实现

可以利用数据库的事务特性来实现分布式锁。比较简单的方法是，在数据库中创建一个表，每行代表一个被锁定的资源，使用该行的主键作为锁名称（或将锁名称保存在另一张表中）。在获取锁时，使用 SELECT ... FOR UPDATE 语句将该行记录加锁，并在同一事务中进行后续操作；释放锁时，提交事务即可。该方法的**优点是易于理解和实现，缺点是需要访问数据库，性能较低，如果数据库出现问题可能影响系统正常运行**。

2. 基于缓存的实现

常见的缓存如 Redis 或 Memcached 等可以通过设置缓存过期时间来实现分布式锁。具体方法是，在缓存中定义一个以锁名称为 key 的缓存项，并指定过期时间；尝试获取锁时，使用 SETNX 命令进行原子操作，如果返回值为 1 则表示加锁成功，并在后续操作中指定适当的超时时间；释放锁时，使用 DEL 命令将缓存项删除即可。该方法的**优点是性能较高、易于实现和扩展，缺点是存在死锁和锁竞争等问题**。

3. 基于 ZooKeeper 的实现

ZooKeeper 是一个分布式协调服务，可以通过创建临时节点来实现分布式锁。具体方法是，在 ZooKeeper 中创建一个唯一名称的临时节点，并以该节点为锁。尝试获取锁时，创建该节点并检查是否为最小序号节点，如果是则表示加锁成功，否则对其前一个节点注册监听器；释放锁时，删除该节点。该方法的**优点是可靠性高，通用性好，缺点是性能较低、实现复杂、依赖于 ZooKeeper 服务的稳定性。**

以上是三种常见的分布式锁实现方式，还有其他的分布式锁实现方式，如基于文件、消息队列等实现。根据具体应用场景和要求，选择适合的实现方式来保证系统的正确性和效率。

***SETNX命令***是在Redis中用于设置一个键的值，当且仅当该键不存在时才会设置成功。SETNX表示"SET if Not eXists"。

语法：
```
SETNX key value
```

参数说明：
- `key`: 键名
- `value`: 键对应的值

返回值：
- 如果键成功设置了值，则返回1；
- 如果键已经存在，不进行任何操作，返回0。

使用示例：
```
SETNX mykey "Hello"
```
上述示例将会检查键`mykey`是否存在，如果不存在，则将其值设置为"Hello"，并返回1。如果键`mykey`已经存在，则不进行任何操作，返回0。

SETNX命令常用于实现分布式锁的获取，通过将键作为互斥锁，利用SETNX的特性来保证只有一个客户端能够成功获取到锁。

## 分布式事务

**分布式事务**是指事务的参与者、支持事务的服务器、资源服务器以及事务管理器 **「分别位于不同的分布式系统的不同节点之上」** 。

**分布式事务**是指跨越多个计算机系统的事务。在分布式系统中，由于数据和计算资源的分散和并行处理，可能会出现一些数据不一致的问题。分布式事务的目的是确保在跨越多个计算机系统的事务中，所有相关操作的一致性和原子性。

##### 分布式理论

**CAP定理：** 

- 一致性（C）：在分布式系统中的所有数据备份，**「在同一时刻是否拥有同样的值」** 。（等同于所有节点访问同一份最新的数据副本）

- 可用性（A）：在集群中一部分节点 **「故障」** 后，集群整体 **「是否还能响应」** 客户端的读写请求。（对数据更新具备高可用性）

- 分区容错性（P）：即使出现 **「单个组件无法可用,操作依然可以完成」** 。

**BASE理论：**

基本可用性（Basically Available）：系统保持可用状态，尽管在出现故障或异常情况时，系统的可用性可能会降低。
		软状态（Soft state）：系统中的状态可以在一段时间内是不确定的，即系统可能在特定时间点看到不同的数据副本或状态。
		最终一致性（Eventual Consistency）：系统最终会达到一致的状态，但在分布式环境下，由于异步复制和延迟，数据的一致性可能需要一段时间来实现。

##### 分布式事务解决方法：

###### 两阶段提交协议（2pc）：

该协议由一个协调者和多个参与者组成，协调者负责协调参与者的事务提交。
该协议的**第一阶段**是协调者向所有参与者发送准备提交消息，等待所有参与者响应。
**第二阶段**是协调者向所有参与者发送提交或回滚消息，根据参与者的响应进行决策。
但是如果协调者此时宕机了，参与者一直收不到协调者的请求，就会一直占用着锁，占用资源。导致其他线程阻塞。这样并发性很低。**（编程式的分布式事务解决方案）**

###### **三阶**段提交（3pc）：

在2pc的基础上引入了超时机制，当协调者宕机后，参与者如果一直收不到协调者的消息，会自动提交。但是这也会造成数据不一致的问题，如有在其中有的参与者操作失败需要进行回滚操作，但是另一部分完成了操作，进行了提交，这样就导致了数据不一致问题。

###### 补偿事务（Compensating Transaction）：

「 **针对每个操作，都要注册一个与其对应的确认和补偿（撤销）操作」** 。补偿事务通常用于在分布式事务失败时回滚已经提交的操作。它通过执行相反的操作来恢复系统状态。例如，如果一个订单已经被支付，但是发生了错误导致订单无法完成，则补偿事务可以撤销付款并将订单状态设置为未支付。**「Try,Confirm,Cancel」**

* Try阶段主要是对 **「业务系统做检测及资源预留」**
* Confirm 阶段主要是对 **「业务系统做确认提交」** ，Try阶段执行成功并开始执行 Confirm阶段时，默认 Confirm阶段是不会出错的。即：只要Try成功，Confirm一定成功。
* Cancel 阶段主要是在业务执行错误，需要回滚的状态下执行的业务取消，**「预留资源释放」** 。

无论哪种方法，分布式事务都是一个复杂的问题，需要仔细考虑并在系统设计和实现中加以处理。

###### 4.本地消息表 

分布式事务本地消息表是一种用于实现分布式事务的技术。它的基本思想是将分布式事务中的消息存储在本地的消息表中，然后通过消息表来保证事务的一致性和可靠性。

在分布式系统中，由于网络延迟、节点故障等原因，事务的执行可能会失败或者出现延迟。为了解决这个问题，可以将事务中的操作转化为消息，然后将消息存储在本地的消息表中。这样，即使事务执行失败或者延迟，消息仍然会被保存在消息表中，待恢复时再进行处理。

分布式事务本地消息表通常具有以下特点：

1. 可靠性

2. 幂等性

3. 事务性

4. 顺序性

5. 可恢复性

分布式事务本地消息表是实现分布式事务的一种有效方式，可以提高系统的可靠性和性能。但是，它也需要考虑消息表的设计和实现，以及消息的处理和恢复机制等方面的问题。

###### 5.MQ（异步化）

分布式事务MQ（Distributed Transactional Message Queue）是一种结合了分布式事务和消息队列的技术，用于解决分布式系统中的事务一致性问题。

在分布式系统中，由于涉及多个节点和服务之间的交互，事务的执行可能会面临一致性问题。传统的关系型数据库事务在分布式环境下的性能和可靠性往往无法满足需求，因此引入了分布式事务MQ来解决这个问题。

分布式事务MQ的基本原理是将事务中的操作转化为消息，并通过消息队列进行传递和处理。具体流程如下：

1. 事务发起方（Producer）：将事务中的操作封装成消息，并发送到分布式事务MQ中。

2. 分布式事务MQ：接收到消息后，将消息持久化存储，并为该消息生成一个全局唯一的事务ID。

3. 事务参与方（Consumer）：从分布式事务MQ中获取消息，并根据事务ID进行处理。在处理过程中，可以将消息进一步发送到其他服务或者存储到其他数据库中。

4. 分布式事务协调器（Coordinator）：负责协调事务的提交或回滚操作。当所有事务参与方都完成消息的处理时，协调器根据事务ID进行全局的提交或回滚操作。

分布式事务MQ的特点如下：

1. 分布式事务一致性：通过将事务操作转化为消息，并使用分布式事务协调器进行协调，保证了分布式系统中的事务一致性。

2. 高可靠性：分布式事务MQ通常具备消息持久化、消息重试、消息确认等机制，可以提高消息的可靠性和可恢复性。

3. 异步处理：将事务操作转化为消息后，可以实现异步处理，提高系统的响应速度和吞吐量。

4. 可扩展性：分布式事务MQ可以通过增加消息队列和消费者来实现系统的水平扩展，提高系统的并发能力和处理能力。

分布式事务MQ是一种强大的工具，可以在分布式系统中实现事务的一致性和可靠性。但是，它也需要考虑消息的顺序性、消息的幂等性、分布式事务协调器的设计和实现等方面的问题。

## nginx

是什么？

Nginx 是一个高性能的HTTP和反向代理Web服务器，同时也提供IMAP/POP3/SMTP服务。

特点：占用内存少，并发能力强。

Nginx专门为性能优化而开发。

#### 代理

###### 正向代理

意思是一个位于客户端和原始服务器之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标，然后代理向原始服务器转交请求并将获得的内容返回给客户端。

![img](https://img-blog.csdnimg.cn/img_convert/0bf59fad467b4116891783eca15c7802.png)

客户端配置代理服务器，通过代理服务器进行互联网访问，这个过程就是正向代理。

###### 反向代理

反向代理服务器位于用户与目标服务器之间，但是对于用户而言，反向代理服务器就相当于目标服务器，即用户直接访问反向代理服务器就可以获得目标服务器的资源。

过程：反向代理时，只需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据后，将数据返回给客户端，此时反向代理服务器和目标服务器在外界看来就是一个服务器，暴露的是代理服务器，隐藏了真实服务器的IP地址。

![img](https://img-blog.csdnimg.cn/img_convert/71980169bfe94636b5c37119dd14ea40.png)

#### 负载均衡

Nginx是一款高性能的开源反向代理服务器，它可以通过负载均衡策略将客户端请求分发给多个后端服务器，以提高系统的性能和可靠性。Nginx支持多种负载均衡策略：

###### I.轮询策略（默认分配策略）

- 优点：简单、均匀地将请求分发给后端服务器，适用于后端服务器性能相近的情况。
- 缺点：无法根据后端服务器的负载情况进行动态调整，可能导致负载不均衡。

```conf
upstream myserver {
    server 192.168.220.120:8080;
    server 192.168.220.120:8081;
}

```
###### II.weight策略（权重策略）

- 优点：根据后端服务器的权重设置，将请求按照权重分发给不同的服务器，可以根据服务器性能调整权重，实现负载均衡。
- 缺点：需要手动设置服务器的权重，不适用于动态环境。

```conf
upstream myserver {
    server 192.168.220.120:8080 weight=1;
    server 192.168.220.120:8081 weight=5;
}
```

###### III.ip_hash策略

每个请求会根据ip地址的hash结果进行分配，这样每个用户就会固定访问一个后端服务器，也就是只要用户的IP地址不发生改变，对应的后续访问的后端服务器就不会变化，可解决session问题。

- 优点：根据后端服务器的权重设置，将请求按照权重分发给不同的服务器，可以根据服务器性能调整权重，实现负载均衡。
- 缺点：需要手动设置服务器的权重，不适用于动态环境。

```conf
upstream myserver {
    server 192.168.220.120:8080 weight=1;
    server 192.168.220.120:8081 weight=5;
}
```

###### IV.fair策略*

按后端服务器的响应时间来分配，响应时间短的优先分配。
```conf
upstream myserver {
    server 192.168.220.120:8080;
    server 192.168.220.120:8081;
    fair;
}
```

#### 动静分离

Nginx的动静分离是一种常见的架构设计模式，通过将动态请求和静态资源请求分开处理，可以提高系统的性能和可扩展性。具体来说，动静分离的实现方式如下：

1. 配置Nginx：
   在Nginx的配置文件中，通过location指令将动态请求和静态资源请求分别指向不同的后端服务器或处理方式。例如，可以将以"/api"开头的请求指向动态请求的后端服务器，将以"/static"开头的请求指向静态资源的目录。

2. 动态请求处理：
   动态请求通常是指需要经过后端服务器处理的请求，例如接口请求、数据库查询等。这些请求会被Nginx转发给后端服务器进行处理，并将处理结果返回给客户端。

3. 静态资源处理：
   静态资源通常是指不需要经过后端服务器处理的请求，例如HTML、CSS、JavaScript、图片等。这些请求可以由Nginx直接返回，而无需转发给后端服务器。Nginx可以通过配置缓存机制，将静态资源缓存到内存或磁盘中，以提高响应速度。

通过动静分离，可以将动态请求和静态资源请求分开处理，从而实现以下优势：

- 提高性能：静态资源由Nginx直接返回，无需经过后端服务器的处理，可以大大减轻后端服务器的负载，提高系统的响应速度和吞吐量。

- 提高可扩展性：由于静态资源请求不涉及后端服务器的处理，可以通过增加Nginx服务器来水平扩展系统的处理能力，而无需修改后端服务器的代码。

- 简化架构：通过将动态请求和静态资源请求分离，可以将系统的架构变得清晰简单，提高代码的可维护性和可扩展性。

总的来说，Nginx的动静分离是一种常见的架构设计模式，通过将动态请求和静态资源请求分开处理，可以提高系统的性能和可扩展性，同时简化系统的架构。

#### 正则表达式

~^以什么开头   ~XXX$  以什么结尾

```text
（1）location = / {}
=为精确匹配 / ，主机名后面不能带任何字符串，比如访问 / 和 /data，则 / 匹配，/data 不匹配
再比如 location = /abc，则只匹配/abc ，/abc/或 /abcd不匹配。若 location  /abc，则即匹配/abc 、/abcd/ 同时也匹配 /abc/。

（2）location / {}
因为所有的地址都以 / 开头，所以这条规则将匹配到所有请求 比如访问 / 和 /data, 则 / 匹配， /data 也匹配，
但若后面是正则表达式会和最长字符串优先匹配（最长匹配）

（3）location /documents/ {}
匹配任何以 /documents/ 开头的地址，匹配符合以后，还要继续往下搜索其它 location
只有其它 location后面的正则表达式没有匹配到时，才会采用这一条

（4）location /documents/abc {}
匹配任何以 /documents/abc 开头的地址，匹配符合以后，还要继续往下搜索其它 location
只有其它 location后面的正则表达式没有匹配到时，才会采用这一条

（5）location ^~ /images/ {}
匹配任何以 /images/ 开头的地址，匹配符合以后，停止往下搜索正则，采用这一条

（6）location ~* \.(gif|jpg|jpeg)$ {}
匹配所有以 gif、jpg或jpeg 结尾的请求
然而，所有请求 /images/ 下的图片会被 location ^~ /images/ 处理，因为 ^~ 的优先级更高，所以到达不了这一条正则

（7）location /images/abc {}
最长字符匹配到 /images/abc，优先级最低，继续往下搜索其它 location，会发现 ^~ 和 ~ 存在

（8）location ~ /images/abc {}
匹配以/images/abc 开头的，优先级次之，只有去掉 location ^~ /images/ 才会采用这一条

（9）location /images/abc/1.html {}
匹配/images/abc/1.html 文件，如果和正则 ~ /images/abc/1.html 相比，正则优先级更高
```



## Dubbo

Dubbo是一种高性能、轻量级的分布式服务框架，它提供了服务治理、负载均衡、容错机制等功能，使得分布式服务的开发和管理变得更加简单和高效。下面是Dubbo的工作原理：

1. 服务注册与发现：
   - 服务提供者在启动时，将自己提供的服务信息注册到注册中心（例如Zookeeper）中。
   - 服务消费者在启动时，从注册中心获取需要调用的服务信息。

2. 服务调用：
   - 服务消费者通过代理对象调用服务提供者的方法。
   - 代理对象将方法调用转发给本地的Invoker（调用者）对象。
   - Invoker对象负责管理服务提供者的地址列表，并根据负载均衡策略选择一个服务提供者进行调用。
   - Invoker对象将方法调用请求封装成消息，并通过网络发送给服务提供者。
   - 服务提供者接收到消息后，将消息解码，并调用本地的服务实现方法。
   - 服务提供者将方法执行结果封装成消息，并通过网络发送给服务消费者。
   - 服务消费者接收到消息后，将消息解码，并返回方法执行结果给调用方。

3. 服务治理：
   - Dubbo提供了丰富的服务治理功能，例如负载均衡、容错机制、服务降级、路由规则等。
   - 负载均衡：Dubbo支持多种负载均衡策略，例如随机、轮询、一致性哈希等，可以根据实际需求选择合适的负载均衡策略。
   - 容错机制：Dubbo提供了多种容错机制，例如失败自动切换、失败重试、失败快速失败等，可以提高系统的稳定性和容错能力。
   - 服务降级：Dubbo支持服务降级，当服务提供者出现故障或不可用时，可以通过降级策略提供默认值或者返回错误信息，以保证系统的可用性。
   - 路由规则：Dubbo支持路由规则，可以根据业务需求将请求路由到不同的服务提供者，实现灵活的服务治理。

总的来说，Dubbo的工作原理是通过服务注册与发现、服务调用和服务治理等机制，实现了分布式服务的管理和调用。它提供了高性能、轻量级的分布式服务框架，可以简化分布式系统的开发和管理。

## HTTP协议

是什么？

http协议超文本传输协议（Hyper Text Transfer Protocol）:是一种位于应用层超文本传输协议。可以通过http协议在客户端和服务端之间通信。

它是一种短连接，无状态的协议。http1.0是无连接无状态的，http1.1是短连接，无状态的。

**短连接**是指一次请求和响应后，连接一会就关闭，这种方式可以节省资源。(支持的并发多)

**无状态**是指服务端在收到客户端发来的消息后，只知道收到了一条消息，并不知道是谁发送的。解决这个问题可以在客户端加上cookie-------服务端发给客户端的；



长连接和短连接是在网络通信中常用的两种连接方式，它们各有优缺点。

**长连接**：

优点：

- 减少了连接的建立和断开的开销：长连接在建立后可以保持一段时间的连接状态，避免了频繁的连接建立和断开操作，从而减少了网络传输的开销。
- 实时性较高：长连接可以始终保持连接状态，可以实时地进行数据传输和交互，适用于实时性要求较高的场景。
- 网络资源占用相对较少：长连接可以共享网络资源，多个请求可以共用同一个连接，减少了网络资源的占用。

缺点：

- 占用服务器资源：长连接需要服务器始终保持连接状态，会占用一定的服务器资源，当连接数量过多时可能会影响服务器的性能。
- 需要维护连接状态：长连接需要额外的机制来维护连接状态，包括心跳检测、连接超时等，增加了开发和维护的复杂性。
- 可能存在网络延迟：长连接一旦建立，会一直保持连接状态，如果出现网络问题或者中间节点的故障，可能导致数据传输的延迟或中断。

**短连接**：

优点：

- 资源释放及时：短连接在每次请求后会主动释放连接，不会占用过多的服务器资源。
- 简单高效：短连接不需要维护连接状态，每次请求都是独立的，实现简单且效率较高。
- 可靠性高：每次请求都是独立的，不会因为之前的请求影响到后续的请求，相互之间不会产生干扰。

缺点：

- 消耗网络资源较多：短连接在每次请求时需要进行连接建立和断开操作，消耗了一定的网络资源。
- 实时性较差：由于每次请求都需要重新建立连接，短连接无法像长连接那样实时地传输数据，对于实时性要求较高的场景可能不太适用。
- 频繁的连接建立和断开操作：短连接需要频繁进行连接建立和断开操作，增加了网络传输的开销和延迟。

##### 通信过程

浏览器访问网址后去解析dns服务器去获取ip，tcp三次握手连接到服务端，后去发送请求协议去请求服务器，返回响应协议，客户端进行解析渲染



#### http协议分为请求协议和响应协议:

##### 请求协议：

* 请求首行 （GET /servletLearn/s1 HTTP/1.1） 请求方式 资源路径 协议版本号
* 请求头  key：value形式   常用的请求头有啥？host  refer(从别的域名来的时候带着他的域名)  Connection  Cookie  User-Agent(表示浏览器或者操作系统的属性)
* 请求空行                            
* 请求体：传输数据 （get请求没有请求体）from json
* ![image-20230605135634016](https://s2.loli.net/2023/06/05/GZ5yJSxYCdpBqP3.png)

        有8中请求方式：Get，Post，Put，Head，Delete，Options（默认半小时可以设置），Trace， Conntection

##### 响应协议：

* 响应首行 （HTTP/1.1200）协议版本号 响应状态码

* 响应头  key：value形式  setCookie 

* 响应空行

* 响应体：json  html  xml

![image-20230605135728757](https://s2.loli.net/2023/06/05/cniIQm1WvCgBSOX.png)

###### **响应状态码：1xx—5xx**

   1xx  100：未返回数据，2xx  200：成功返回数据，3xx  302：重定向

   4xx：前台错误 400 Bad Request：请求参数有误，404请求路径错误，405 请求方法错误 ,415 请求格式错误(json,from) 401需要身份验证

   5xx：后台错误 500：服务器内部错误，无法完成请求

###### Content-Type的常见值包括：

- text/plain：纯文本格式
- text/html：HTML格式
- application/json：JSON格式
- application/xml：XML格式
- multipart/form-data：多部分表单数据格式
- image/jpeg：JPEG图像格式
- audio/mp3：MP3音频格式
- video/mp4：MP4视频格式

#### HTTP 和 HTTPS（HTTP Secure）是两种不同的传输协议，它们之间的主要区别如下：

1. 安全性

HTTP 传输协议具有明文的特点，数据在传输过程中不加密，存在被窃取、篡改等风险；而 HTTPS 则采用 SSL/TLS 协议进行数据加密和身份验证，保证数据传输的安全可靠。

2. 端口号

HTTP 默认端口为 80，HTTPS 默认端口为 443。当然，这些端口可以配置修改。

3. 证书

HTTPS 协议需要使用证书来建立安全连接，通过数字证书机构（CA）对网站的身份进行认证，防止恶意攻击和中间人攻击。

4. 性能

由于 HTTPS 协议涉及到加密和解密等过程，相较于 HTTP 协议，性能会稍微有所下降，同时也需要占用更多的资源。

综上所述，HTTP 协议适合在内网环境或者不涉及安全性要求的场景下使用；而 HTTPS 协议则适合关注用户隐私和数据安全的 Web 应用场景，如银行、电商、社交网络等网站。



## 动态代理

#### 概念

动态代理是一种在运行时动态生成代理对象的技术，它可以在不修改原始类的情况下，通过代理对象来完成对原始类的方法调用。动态代理通常用于在不改变原有代码的情况下，对原始类的功能进行扩展或增强。

Java中的动态代理主要通过两个核心接口来实现：`java.lang.reflect.InvocationHandler`和`java.lang.reflect.Proxy`。

`InvocationHandler`接口定义了一个invoke方法，该方法在代理对象的方法被调用时会被触发，可以在该方法中对原始方法进行增强处理。

`Proxy`类是实现动态代理的主要类，它提供了一组静态方法用于创建动态代理对象。通过调用Proxy的`newProxyInstance`方法，传入一个`InvocationHandler`对象和原始类的接口，即可生成一个代理对象。

#### 流程

动态代理是在运行时动态生成代理类的机制，它可以在不修改源代码的情况下为目标对象创建一个代理对象。下面是动态代理的基本流程：

1. 定义接口：首先需要定义一个接口，该接口是目标对象和代理对象都要实现的。

2. 实现InvocationHandler接口：创建一个实现了`InvocationHandler`接口的类，该类负责处理代理对象的方法调用。这个类通常被称为“代理处理器”或“调用处理器”。

3. 创建代理对象：使用`Proxy.newProxyInstance()`方法创建代理对象。这个方法接收三个参数：ClassLoader，接口数组和InvocationHandler对象。该方法会动态生成一个代理类，并通过反射机制创建代理对象。

4. 调用代理对象方法：通过调用代理对象的方法来触发代理逻辑。当代理对象的方法被调用时，实际上会通过`InvocationHandler`的`invoke()`方法来进行处理。

5. 代理处理器的invoke()方法：`InvocationHandler`的`invoke()`方法会在代理对象的方法被调用时执行。在该方法中，可以根据需求进行一些前置或后置处理，也可以决定是否调用目标对象的方法。

总的来说，动态代理的流程是：定义接口 -> 实现InvocationHandler接口 -> 创建代理对象 -> 调用代理对象方法 -> 代理处理器的invoke()方法。

动态代理的优势在于它能够在运行时创建代理对象，无需事先编写具体类。这样可以实现更加灵活和可复用的代理逻辑，例如日志记录、性能监控、事务管理等。

动态代理的优点包括：
- 可以在运行时动态生成代理对象，无需提前编写代理类。
- 可以对多个原始类进行统一的代理处理，减少重复代码。
- 可以在不修改原始类的情况下，对原始类的方法进行增强或拦截。

动态代理在Java中的应用非常广泛，例如AOP（面向切面编程）中的切面就是通过动态代理来实现的。同时，Java中还提供了多种动态代理的实现方式，包括基于接口的代理（JDK动态代理）和基于类的代理（CGLIB动态代理），可以根据具体需求选择合适的实现方式。

总的来说，动态代理是一种在运行时动态生成代理对象的技术，可以在不修改原始类的情况下对其方法进行增强或拦截。Java中的动态代理主要通过InvocationHandler接口和Proxy类来实现，具有灵活性和扩展性，广泛应用于AOP等领域。



**后置处理器中使用动态代理可以实现事务增强**

事务增强是指在方法执行前后进行事务管理的一种技术。在后置处理器中使用动态代理可以实现事务增强的功能。

具体步骤如下：

1. 创建一个后置处理器类，实现BeanPostProcessor接口。
2. 在后置处理器的postProcessBeforeInitialization方法中，判断当前Bean是否需要进行事务增强。
3. 如果需要进行事务增强，使用动态代理创建一个代理对象。
4. 将代理对象返回给容器，替代原来的Bean对象。
5. 在代理对象中，实现事务管理的逻辑，例如在方法执行前开启事务，在方法执行后提交或回滚事务。

示例代码如下：

```java
public class TransactionEnhancer implements BeanPostProcessor {

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        // 判断当前Bean是否需要进行事务增强
        if (bean.getClass().isAnnotationPresent(Transactional.class)) {
            // 使用动态代理创建一个代理对象
            Object proxy = Proxy.newProxyInstance(bean.getClass().getClassLoader(),
                    bean.getClass().getInterfaces(), new TransactionProxy(bean));
            return proxy;
        }
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        return bean;
    }
}

public class TransactionProxy implements InvocationHandler {

    private Object target;

    public TransactionProxy(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // 在方法执行前开启事务
        beginTransaction();
        // 调用原始对象的方法
        Object result = method.invoke(target, args);
        // 在方法执行后提交或回滚事务
        commitTransaction();
        return result;
    }

    private void beginTransaction() {
        // 开启事务的逻辑
    }

    private void commitTransaction() {
        // 提交或回滚事务的逻辑
    }
}

// 使用@Transactional注解标记需要进行事务增强的方法
@Transactional
public class UserServiceImpl implements UserService {
    // ...
}
```

在上述示例中，TransactionEnhancer类是一个后置处理器，使用动态代理创建代理对象，并将代理对象返回给容器。TransactionProxy类是一个动态代理类，实现了InvocationHandler接口，用于实现事务管理的逻辑。UserServiceImpl类是一个示例服务类，使用@Transactional注解标记需要进行事务增强的方法。

通过在后置处理器中使用动态代理，可以实现对带有@Transactional注解的方法进行事务增强的功能。



## Git相关

命令：

git init  初始化当前目录为一个git库

git clone 克隆远程仓库内容到本地，一般是远程仓库不是空的我们需要传文件上去;
		git add . 将文件从工作区->暂存区
		git commit -m "" 暂存区->本地仓库
		git status  查看修改状态
		git log 查看提交记录
		git pull +远程仓库名称+线程名  拉取
		git push +远程仓库名称+线程名  推送
		git diff ：git diff <分支名1> <分支名2> ：比较两个分支上最后 commit 的内容的差别

clone和pull的区别：
			clone第一次需要将远程的仓库数据下载到本地
			pull 在每次push之前需要将远程仓库进行pull防止覆盖更新

git branch 查看本地分支
		git checkout 切换分支
		git checkout -b 创建并切换
		git merge xxx 合并分支

**分支管理**

在版本回退的中，每次提交都会有记录，git 把它们串成时间线，形成类似于时间轴的东西，这个时间轴就是一个分支，我们称为master 分支

在开发的时候往往是团队协作，多人进行开发，因此光有一个分支是无法满足多人同时开发的需求的，并且在分支上工作不影响其他分支使用，会更加安全，git 鼓励开发者使用分支去完成一些开发任务

分支指令：

查看分支 git branch

创建分支；git branch 分支名

切换分支：git checkout 分支名

创建并且切换分支：git checkout -b 分支名 （-b 表示创建并切换）

删除分支：git branch -d （在删除分支时一定要退出当前分支，然后删除 ）

合并分支： git merge 被/把合并的分支名

注意：这些分支只是对本地的操作，要想同步到线上分支 使用 git push 命令即可

 

当前分支：develop

Git merge master 			 // merge分支中的修改合并到当前分支

 

***\*冲突的产生与解决\*******\*：\****

查看是否有冲突：git diff

当本地仓库的值与远程仓库的内容不一致时，会出现报错，（线下内容与线上内容不同步就会报错）

![img](file:///C:\Users\14064\AppData\Local\Temp\ksohtml17700\wps1.jpg) 

解决冲突：

1.先git pull

![img](file:///C:\Users\14064\AppData\Local\Temp\ksohtml17700\wps2.jpg) 

此时git 已经将线上版本与本地仓库的冲突合并到了对应的文件中

2.打开冲突

![img](file:///C:\Users\14064\AppData\Local\Temp\ksohtml17700\wps3.jpg) 

上面的是新写的，下面的是之前写的

3.删除或保留对应的内容，假如都想保留那么就将注释标识删掉即可 

4.重新提交即可(git add .  ->  git commit -m “” ...)



Git 上线diff 代码

先切换到master 分支

再git pull 拉去master 分支

再切换到git 到开发分支

再git pull 拉去开发分支

再进行代码比对



Merge代码步骤

保持当前分支最新 git pull

查看有没有改动 git status

切换到需要merge 到的分支 比如：git checkout master 

Git pull 拉取

Git 切换到merge 的分支 feature-0615

Git pull

Git status

Git merge 被merge 的分支 比如：git merge master

Git diff

Git push

## jvm

可以分为两个部分，堆和方法区是所有线程共有的，而虚拟机栈，本地方法栈和程序计数器则是线程私有的。

![image-20230605140602838](https://s2.loli.net/2023/06/05/23AGNugjaoZBlhk.png)

堆：存放对象实例和数组的，堆被划分成两个不同的区域：**新生代 ( Young )、老年代 ( Old )**，新生代 ( Young ) 又被划分为三个区域：伊甸园，幸存区s0s1，默认比例811

-Xms(最小值)和-Xmx(最大值)参数设置（最大最小值都要小于1G）

方法区：存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码缓存等数据。

虚拟机栈：主管Java程序的运行，它保存**方法的局部变量、部分结果，并参与方法的调用和返回**。

本地方法栈：用于支持Java程序中调用本地方法（Native Method）的执行过程

程序计数器：是一个**线程私有**的数据区域，是一块较小的内存空间，可以看作是当前线程所执行的字节码的行号指示器。



**垃圾回收：** 垃圾回收机制是分代回收策略。堆内存分为年轻代和年老代。年轻代分为eden区和幸存区，幸存区有幸存1区和幸存2区。他们之间大小比例是1:1：8。 当一个对象被new出来后，存入到eden区，当伊甸园区满了后，进行yong gc，使用**复制算法**，把幸存下来的对象放进幸存1区中。当对象被回收15次后仍然存活，会将对象放进年老区中。如果年老区内存满了，会执行full gc，对整个堆进行一次垃圾回收，使用的是**标记清除算法**。如果经过多次垃圾回收后，年轻区和年老区都满了，jvm会报出堆内存溢出异常。

##### 垃圾回收算法

垃圾回收算法是一种用于自动管理动态分配的内存的算法。它通过检测和回收不再被程序使用的内存，以释放资源并防止内存泄漏。

以下是几种常见的垃圾回收算法：

1. 标记-清除（Mark and Sweep）算法：这是最基本的垃圾回收算法之一。它通过追踪对象的引用关系，在堆中标记所有活动对象，然后清除未标记的对象。缺点是会产生内存碎片，并且清除阶段可能导致停顿时间较长。

2. 复制（Copying）算法：这种算法将堆分为两个区域，每次只使用其中一个。活动对象被复制到另一个区域，同时进行压缩操作。该算法避免了内存碎片问题，但需要额外的空间来进行复制。

3. 标记-整理（Mark and Compact）算法：类似于标记-清除算法，但它在清除阶段后会对活动对象进行紧凑整理，使所有对象紧凑地排列在一起，从而消除内存碎片。

4. 分代（Generational）算法：这种算法根据对象的生命周期将堆分为不同的代。大部分对象在创建后很快变得不可达，因此被认为是短期存活对象。分代算法根据对象的存活时间将它们分配到不同的代中，并为每个代选择适当的回收策略。

5. 并发（Concurrent）算法：这种算法允许在垃圾回收过程中同时执行程序代码，以减少停顿时间。并发垃圾回收算法需要处理并发访问和一致性问题，因此实现较为复杂。

这些垃圾回收算法通常由Java虚拟机（JVM）或其他编程语言的运行时环境负责执行。具体选择哪种算法取决于应用程序的需求、内存使用模式和性能要求。

##### 垃圾回收器

1. **Serial GC**

   * 单线程执行
   * 使用复制算法清理年轻代、标记-清除-整理算法清理老年代
   * `-XX:+UseSerialGC`
2. **Parallel GC**

   * 多线程执行
   * `-XX:+UseParallelGC`
3. **Concurrent Mark Sweep (CMS) GC**

   * 主要针对老年代
   * 避免长时间的停顿
   * `-XX:+UseConcMarkSweepGC`
4. **G1 GC**

   * 分区式垃圾回收
   * 预测停顿时间模型
   * `-XX:+UseG1GC`
5. **ZGC & Shenandoah GC**

   * 低延迟高吞吐量
   * 利用了多阶段并发算法
   * `-XX:+UseZGC` & `-XX:+UseShenandoahGC`

##### 内存泄漏和内存溢出

**内存泄漏**（Memory leak）是指程序中已经不再使用的内存没有被正确释放，导致系统原本可以使用的内存空间被占据而无法使用。

是指应用程序在申请内存后，无法释放已经申请的内存空间。一次内存泄露危害可以忽略，但如果任其发展最终会导致内存溢出。如读取文件后流要进行及时的关闭以及对数据库连接的释放。

**内存溢出**（Memory overflow）则是指当程序运行时，申请的内存超过了系统实际可用的内存大小，导致程序崩溃或者出现其他异常情况。一般是由于程序设计不合理或算法错误等问题导致的。

是指应用程序在申请内存时，没有足够的内存空间供其使用。如我们在项目中对于大批量数据的导入，采用分段批量提交的方式。

## 类加载器

#### 类加载机制

类加载器是jre的一部分，负责动态将类添加到Java虚拟机。负责将字节码文件加载到内存中并转换为可执行的Java类。

类加载机制指的是Java虚拟机(JVM)将Java类加载到内存中的过程。Java类在运行时需要被加载到内存中才能被JVM执行，而类加载机制就是负责将Java类加载到内存中的过程。

##### 类加载分类

    1、启动类加载器 bootstrap classloader ：加载`<JAVA_HOME\>\jre\lib`路径下的核心类库，由于安全考虑只加载 包名 java、javax、sun开头的类

    2、扩展类加载器 extension classloader ：加载`<JAVA_HOME>\jre\lib\ext`目录下的类库

    3、应用程序类加载器 application classloader：加载 `classpath环境变量`所指定的类库，是用户自定义类的默认类加载器。

##### 类加载过程

类加载机制分为三个阶段：

1. 加载：类加载器根据类的全限定名查找并加载类的字节码文件。加载过程中，会进行字节码文件的校验、解析和初始化等操作。

2. 链接：链接分为三个阶段：验证、准备、解析。验证是确保类文件的字节码符合JVM规范，准备是为类的静态变量分配内存并设置默认值，解析是将符号引用解析为直接引用。

3. 初始化：在类加载完成后，JVM会执行类的初始化操作，包括执行类的静态代码块和初始化静态变量等操作。

类加载机制是Java语言的核心机制之一，它保证了Java程序的正确性和安全性。



双亲委派机制

当一个类加载器需要加载某个类时，它首先会委派给它的父类加载器去尝试加载。如果父类加载器能够成功加载该类，则直接返回加载结果；如果父类加载器无法加载，则将加载请求传递给自己的子类加载器，由子类加载器继续尝试加载。这个过程就是双亲委派机制。可以防止Java核心类被篡改；



## 代理

静态代理和动态代理都是面向对象编程中的设计模式，用于在不改变原始类代码的情况下，为原始类提供额外的功能或增强原始类的行为。

1. **静态代理：**
静态代理是通过创建一个代理类，该代理类与原始类实现相同的接口，并持有一个对原始类对象的引用。在代理类中，可以在调用原始类方法的前后添加额外的逻辑，从而实现对原始类的功能增强。静态代理的特点是代理类需要在编译时已经确定，并且每个代理类只能代理一个具体的原始类。

2. **动态代理：**
动态代理是在运行时通过Java的反射机制动态生成代理类的方式。与静态代理不同，动态代理不需要事先创建代理类，而是在程序运行时动态地创建代理实例。动态代理通常使用系统类库中的Proxy类和InvocationHandler接口来实现。在动态代理中，InvocationHandler接口负责实现对原始类方法的增强逻辑，而Proxy类则负责创建代理类的实例。动态代理的优势在于可以代理多个接口的方法，并且在代理类发生变化时无需重新编译。

**区别：**

- 静态代理需要手动创建代理类，而动态代理在程序运行时动态生成代理类。
- 静态代理每个代理类只能代理一个具体的原始类，而动态代理可以代理多个接口的方法。
- 静态代理在编译时就确定了代理类和原始类的关系，而动态代理在运行时根据需要生成代理类。

选择使用静态代理还是动态代理取决于具体的需求。如果代理类数量较少且固定，可以使用静态代理。如果代理类数量较多且需要动态变化，或者需要代理多个接口的方法，可以考虑使用动态代理。

#### 举例

静态代理举例：
假设有一个接口 `Subject`，它定义了一个方法 `request()`。现在我们希望在调用 `request()` 方法前后添加一些额外的逻辑，比如在执行前输出日志和在执行后进行资源释放。这时可以使用静态代理来实现。

1. 创建接口 `Subject`：

```java
public interface Subject {
    void request();
}
```

2. 创建原始类 `RealSubject` 实现 `Subject` 接口：

```java
public class RealSubject implements Subject {
    @Override
    public void request() {
        System.out.println("RealSubject: Processing request");
    }
}
```

3. 创建代理类 `ProxySubject`，它也实现 `Subject` 接口，并持有一个对 `RealSubject` 对象的引用，在调用 `request()` 方法前后添加额外的逻辑：

```java
public class ProxySubject implements Subject {
    private RealSubject realSubject;

    public ProxySubject(RealSubject realSubject) {
        this.realSubject = realSubject;
    }

    @Override
    public void request() {
        System.out.println("ProxySubject: Before request");
        realSubject.request();
        System.out.println("ProxySubject: After request");
    }
}
```

4. 使用静态代理：

```java
public class Main {
    public static void main(String[] args) {
        RealSubject realSubject = new RealSubject();
        ProxySubject proxySubject = new ProxySubject(realSubject);

        // 调用代理对象的 request() 方法
        proxySubject.request();
    }
}
```

运行结果：

```
ProxySubject: Before request
RealSubject: Processing request
ProxySubject: After request
```

动态代理举例：
假设同样有一个接口 `Subject`，我们希望在调用 `request()` 方法前后添加一些额外的逻辑。这时可以使用动态代理来实现。

1. 创建接口 `Subject`：

```java
public interface Subject {
    void request();
}
```

2. 创建原始类 `RealSubject` 实现 `Subject` 接口：

```java
public class RealSubject implements Subject {
    @Override
    public void request() {
        System.out.println("RealSubject: Processing request");
    }
}
```

3. 创建 InvocationHandler 接口的实现类 `ProxyHandler`，它负责实现对 `request()` 方法的增强逻辑：

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class ProxyHandler implements InvocationHandler {
    private Object realSubject;

    public ProxyHandler(Object realSubject) {
        this.realSubject = realSubject;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("Before request");
        Object result = method.invoke(realSubject, args);
        System.out.println("After request");
        return result;
    }
}
```

4. 使用动态代理：

```java
import java.lang.reflect.Proxy;

public class Main {
    public static void main(String[] args) {
        RealSubject realSubject = new RealSubject();
        ProxyHandler proxyHandler = new ProxyHandler(realSubject);

        // 创建动态代理对象
        Subject proxySubject = (Subject) Proxy.newProxyInstance(
                realSubject.getClass().getClassLoader(),
                realSubject.getClass().getInterfaces(),
                proxyHandler);

        // 调用动态代理对象的 request() 方法
        proxySubject.request();
    }
}
```

运行结果：

```
Before request
RealSubject: Processing request
After request
```

通过动态代理，我们无需手动创建代理类，而是通过 `Proxy.newProxyInstance()` 方法根据原始类和增强逻辑生成代理类，并将对应方法调用委托给 `InvocationHandler` 实现类来处理。这样可以灵活地在运行时生成代理对象，并对多个接口进行代理。

## 异常



##### 统一异常处理

统一异常处理是一种在软件开发中常用的策略，用于处理应用程序中抛出的各种异常。它的目的是通过集中处理异常，提高代码的可维护性和可读性，同时向用户提供更友好的错误提示。

下面是实现统一异常处理的一般步骤：

1. 创建一个全局异常处理器类（Global Exception Handler），(该类需要实现ExceptionHandler接口或继承DefaultHandlerExceptionResolver类。不需要)
2. 在全局异常处理器中定义处理各种异常的方法，可以根据需要捕获特定类型的异常。
3. 在处理方法中编写异常处理逻辑，例如记录日志、返回合适的错误信息或页面等。
4. 注册全局异常处理器到应用程序中，使其能够捕获到所有未被其他异常处理器处理的异常。
   - 在Spring框架中，可以通过@ControllerAdvice注解标记全局异常处理器类，并使用@ExceptionHandler注解定义处理方法。
   - 在Servlet容器中，可以配置web.xml文件，将全局异常处理器作为默认的异常处理器。

使用统一异常处理的好处包括：

- 避免在每个可能抛出异常的地方都编写繁琐的try-catch块，简化了代码逻辑。
- 提高了代码的可维护性，异常处理逻辑集中在一个地方，方便排查和修改。
- 给用户提供更友好的错误提示，可以根据具体的异常类型返回对应的错误信息或页面。
- 记录异常日志，便于问题定位和排查。

需要注意的是，在使用统一异常处理时，也要谨慎处理异常，避免掩盖真正的问题或导致不可预料的后果。同时，仍然需要在合适的地方处理特定的异常，以确保程序的正确性和稳定性。



service里面能写  try-catch  抛异常  

实现接口异常方法不能抛只能 try-catch     

有可能报错想获取控制权try-catch  eg:记日志、关闭资源

###### **catch可以不写 只写try-finally** 

当使用`try-catch`语句时，`try`块是必须的，用于包含可能引发异常的代码。而`catch`块是可选的，用于捕获并处理异常。`finally`块也是可选的，无论是否发生异常，都会在`try`或`catch`块执行完毕后被执行。

如果你只想编写`try`和`finally`块，可以省略`catch`块。这样做的话，如果在`try`块中发生异常，则该异常将不会被捕获和处理，而是继续向上层调用栈传播。然后，不管是否发生异常，`finally`块中的代码都会执行。

以下是一个只包含`try`和`finally`的示例：

```java
try {
    // 可能会发生异常的代码
} finally {
    // 在try块执行完毕后，无论是否发生异常，都会执行的代码
}
```

需要注意的是，省略了`catch`块后，你将无法对发生的异常进行处理和响应。因此，在实际开发中，通常建议同时使用`try`、`catch`和`finally`块来确保异常能够得到正确地处理和清理。

## 多线程

##### 实现线程的三种方式

1. 继承Thread类：创建一个新的类并继承Thread类，重写run()方法来定义需要执行的任务，然后通过创建该类的实例对象来启动线程。

2. 实现Runnable接口：创建一个实现Runnable接口的类，并实现其run()方法，通过创建该类的实例对象，并将其作为Thread类的构造方法参数来启动线程。

3. 实现Callable接口：创建一个实现Callable接口的类，并实现其call()方法，可以返回执行结果。然后通过创建该类的实例对象，并将其作为FutureTask类的构造方法参数，再创建Thread类来启动线程。

##### 线程池的3种创建方式

```javascript
/*
 	<dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-lang3</artifactId>
        <version>3.12.0</version>
    </dependency>
*/

//创建固定大小的线程池
     ExecutorService threadPool = Executors.newFixedThreadPool(3);
//创建缓存线程池(根据任务数量去创建线程)
     ExecutorService threadPool = Executors.newCachedThreadPool();
//创建单一线程池（如果线程结束，可以创建一个新的）
     ExecutorService threadPool = Executors.newSingleThreadExecutor();

threadPool.execute(new Runnable());
threadPool.shutdown();//没有任务时，关闭线程池
```

线程池是一种用于管理和复用线程的机制，它可以帮助我们提高线程的利用率和管理线程的生命周期。线程池通常具有以下属性：

1. 核心线程数（corePoolSize）：线程池中最小的线程数量。即使线程池中没有任务需要执行，核心线程也会一直存在，不会被回收。
2. 最大线程数（maximumPoolSize）：线程池中允许的最大线程数量。当任务量增加时，线程池会动态地创建新的线程，直到达到最大线程数。
3. 空闲线程存活时间（keepAliveTime）：当线程池中的线程数量超过核心线程数时，空闲线程的存活时间。如果空闲线程在指定的时间内没有被使用，它们将被终止并从线程池中移除。
4. 任务队列（workQueue）：用于存放待执行任务的队列。当线程池中的线程数量达到核心线程数时，新的任务会被放入任务队列中等待执行。
5. 线程工厂（threadFactory）：用于创建线程的工厂类。线程池通过线程工厂创建新的线程。
6. 拒绝策略（rejectedExecutionHandler）：当线程池已经达到最大线程数，并且任务队列也已满时，新的任务无法被执行时，拒绝策略定义了如何处理这些被拒绝的任务。
7. （blockingQueue）:线程池任务队列
8. timeUnit:时间单位

这些属性可以根据具体的需求进行配置，以满足不同的并发处理需求。合理地配置线程池的属性可以提高系统的性能和稳定性，避免资源的浪费和任务的堆积。

##### 线程安全问题

当多个线程访问某个方法时，不管你通过怎样的调用方式、或者说这些线程如何交替地执行，我们在主程序中不需要去做任何的同步，这个类的结果行为都是我们设想的正确行为，那么我们就可以说这个类是线程安全的。

##### 线程同步 

线程同步是多线程编程中的一个概念，用于确保两个或多个并发线程不同时访问某一共享资源（如共享变量、数组或者文件等）。线程同步可以解决由于线程的并发执行而引发的问题，例如数据不一致、数据丢失等。

线程同步的主要目的是防止同时访问共享资源，导致数据混乱的情况。为了实现这个目标，需要在访问共享资源的代码前后加上同步控制，如锁等机制，以确保任一时刻只有一个线程在访问资源。

在Java中，线程同步可以通过以下几种方式实现：

**Synchronized 关键字**：可以修饰方法或者代码块。当它修饰一个方法或一个代码块时，能够保证在同一时刻最多只有一个线程执行该段代码。

**ReentrantLock 类**：Java提供的另一种锁机制，相比于synchronized，ReentrantLock类提供了一些高级功能，比如具备可重入性、中断响应的加锁、公平和非公平锁模式、读写分离锁等。

**Volatile 关键字：** 它能确保将修改的值立即同步到主内存，以及每次使用前立即从主内存刷新。

**Atomic 类**：在Java中，原子操作是不能被线程调度机制打断的操作；也就是说，当一个线程开始执行原子操作后，如果线程在进行原子操作期间被调度程序打断，那么该操作依然能够继续完成。

AtomicStampedReference：

[详解解决CAS机制中ABA问题的AtomicStampedReference (baidu.com)](https://baijiahao.baidu.com/s?id=1648077822185803003&wfr=spider&for=pc)

CountDownLatch、CyclicBarrier、Semaphore：这些都是高级的线程同步工具类，可以帮助开发者更好的控制线程的同步。

这些都是实现线程同步的方法，不同的方法有其适用的场景，需要根据具体的需求来选取合适的同步方法。



##### 同步工具

###### Semaphore信号灯

允许线程最大访问量，如果超出最大访问量，其它线程要等待

```java
Semaphore semap = new Semaphore(3);//最大访问量为3
semap.acquire();//获取信号灯
semap.release();//释放信号灯
```

###### Countdownlatch

简单的说就是线程等待，用来协调线程的同步，当所有线程执行到就count-1，全部集合之后再继续执行。一个典型应用场景就是启动一个服务时，主线程需要等待多个组件加载完毕，之后再继续执行。

```javascript
//所有运动员准备好，等待裁判发出起跑命令
CountDownLatch latch1 = new CountDownLatch(1);
CountDownLatch latch2 = new CountDownLatch(3);
latch1.await();
latch2.countDown();//计数器减1 
```

###### Cyclicbarrie

是所有线程都进行等待，直到所有线程都准备好进入await()方法之后，执行条件变量的signalAll方法唤醒等待的线程。所有线程同时开始执行！

```javascript
CyclicBarrier barrier = new CyclicBarrier(5);
barrier.await();
```

###### 读写锁 ReentrantReadWriteLock

读锁和读锁不互斥，写锁和写锁互斥，写锁和读锁也互斥

```javascript
ReadWriteLock rwl = new ReentrantReadWriteLock();
rwl.readLock().lock();//上读锁
rwl.readLock().unlock();//释放读锁，在finally块里释放锁

rwl.writeLock().lock();//上写锁
rwl.writeLock().unlock();//释放写锁
```

###### 线程之间的通信Condition

就是线程1执行10次，换另一个线程执行5次，实现线程之间的通信

```javascript
ReentrantLock lock = new ReentrantLock();
Condition condition1 = lock.newCondition();//在锁的基础上创建条件
lock.lock();//先上锁
condition1.await();//线程1等待执行
condition2.signal();//通知线程2执行完毕
```

signal signalAll 的区别是：condition的signal是将等待队列中的first节点放入同步队列，并通过调用unpark方法来唤醒线程。signalAll就是signal的while循环，将所有等待队列中的node放入同步队列，并逐一唤醒。线程被唤醒之后，还是需要去尝试获得锁，如果获得锁失败，则继续阻塞。

##### 线程的生命周期

共5个（新建、就绪、运行、阻塞、死亡）

![image-20230605140036047](https://s2.loli.net/2023/06/05/6qL3uNzaEYRwOTB.png)

sleep 站着锁 wait 释放锁

##### 线程和进程的区别

1. 进程（Process）和线程（Thread）是操作系统中的两个重要概念，它们具有以下区别：

   1. 定义：进程是执行中的程序实例，具有独立的内存空间和系统资源；线程是进程的执行单位，是进程内的一个独立执行流程。
   2. 资源占用：每个进程都有独立的内存空间和系统资源（如文件句柄、打开的网络连接等），而线程共享所属进程的资源。
   3. 调度和切换：进程是系统进行调度和分配资源的基本单位，进程间的切换代价较高；线程是在进程内部切换执行的，线程间的切换代价较低。
   4. 并发性：在多核处理器上，多个进程可以并发执行，每个进程可以利用多个线程实现并发执行。
   5. 通信与同步：进程间通信需要使用特定的机制，如管道、消息队列、共享内存等；线程之间可以通过共享内存直接进行通信和数据共享，但需要考虑同步和互斥问题。
   6. 稳定性：一个进程的异常或错误通常不会影响其他进程的正常执行；但一个线程的异常或错误可能会导致整个进程崩溃。
   7. 创建和销毁：创建和销毁进程的开销较大；创建和销毁线程的开销相对较小。

   总的来说，进程是资源分配和调度的基本单位，线程是进程内部并发执行的基本单位。进程之间相互独立，而线程共享进程的资源。在设计和开发中，需要根据具体的需求和场景合理选择使用进程或线程，以达到最佳的性能和资源利用。

## MyBatis

MyBatis是一种Java持久化框架，它使用XML配置文件或者注解来映射数据库操作。以下是MyBatis中常用的标签：

1. `<mapper>`：定义一个Mapper接口对应的XML配置文件。
2. `<select>`：用于执行查询语句。
3. `<insert>`：用于执行插入语句。
4. `<update>`：用于执行更新语句。
5. `<delete>`：用于执行删除语句。
6. `<resultMap>`：将查询结果映射到Java对象的规则定义。
7. `<id>`：定义主键属性的映射规则。
8. `<result>`：定义属性与列的映射规则。
9. `<association>`：用于处理多对一关系，将一个父对象映射到引用属性中。
10. `<collection>`：用于处理一对多关系，将多个子对象映射到集合属性中。
11. `<if>`：用于条件判断，在动态SQL中进行条件拼接。
12. `<choose>`、`<when>`、`<otherwise>`：用于多条件判断，在动态SQL中进行条件选择。
13. `<foreach>`：用于遍历集合进行循环操作，在动态SQL中进行批量操作。

以上是MyBatis中常用的标签，通过合理使用这些标签可以灵活地配置和映射SQL语句，实现数据库操作的需求。

在MyBatis中，#和$是两种不同的参数占位符处理方式：

1. #：使用#作为参数占位符，会对传入的参数进行预编译处理，可以防止SQL注入攻击。例如：`SELECT * FROM users WHERE id = #{userId}`。
2. $：使用$作为参数占位符，直接将参数的字符串值替换到SQL语句中，不进行预编译处理。这种方式可以用于动态拼接SQL，但存在SQL注入的风险。例如：`SELECT * FROM users WHERE id = ${userId}`。

关于一对多和多对一的映射关系，MyBatis提供了以下常用的标签：

1. collection：用于处理一对多关系，可以将多个子对象映射到一个集合属性中。
2. association：用于处理多对一关系，可以将一个父对象映射到一个引用属性中。

这些标签可以在resultMap中使用，通过配置相应的映射关系，实现一对多和多对一的数据查询与映射。

## JDK常用命令

`jdk`命令是用于执行Java开发工具包（JDK）中的各种命令的命令行工具。以下是一些常见的`jdk`命令：

1. `java`命令：用于运行Java应用程序。
2. `javac`命令：用于将Java源代码编译为字节码文件。
3. `jar`命令：用于创建和管理JAR（Java Archive）文件，它可以将多个相关的类和资源文件打包成一个单独的文件。
4. `javadoc`命令：用于生成Java代码的API文档，可以从代码注释中提取文档内容。
5. `jarsigner`命令：用于对JAR文件进行数字签名，以确保文件的完整性和安全性。
6. `keytool`命令：用于管理Java密钥和证书，包括生成、导出和查看密钥库和证书等操作。

这只是一些常见的`jdk`命令示例，JDK还提供了其他许多命令和工具，用于支持Java应用程序的开发、调试和部署。您可以通过在命令行中输入`jdk`命令来查看可用的命令和选项的完整列表，并通过使用`-help`参数获取每个命令的详细帮助信息。



## 以下是Java中常用的10个类名：

1. String：字符串操作类，用于处理和操作字符串。
2. List：列表接口，定义了对列表数据结构的基本操作。
3. Map：映射接口，定义了键值对的映射关系，常用的实现类有HashMap、TreeMap等。
4. Set：集合接口，用于存储不重复的元素，常用的实现类有HashSet、TreeSet等。
5. ArrayList：动态数组，实现了List接口，提供了可变长度的数组。
6. HashMap：散列表，实现了Map接口，通过键值对来存储和访问元素。
7. StringBuilder：可变字符串，用于高效地拼接字符串。
8. File：文件类，用于操作文件和目录。
9. inputstream、outputstream
10. Date：日期类，用于表示日期和时间。
11. Math：数学类，提供了各种数学运算和函数。

## 常用的Java包包括但不限于：

1. java.lang：提供了Java语言的核心类，如String、Math等。
2. java.util：提供了实用工具类，如集合框架、日期类、随机数生成器等。
3. java.io：提供了输入输出相关的类，用于读写文件和流操作。
4. java.net：提供了网络编程相关的类，用于建立和操作网络连接。
5. java.awt和javax.swing：提供了图形用户界面（GUI）开发所需的类和接口。
6. java.sql：提供了访问和操作关系型数据库的类。

这只是一小部分常用的类名和包，Java拥有非常丰富的类库和功能，您可以根据自己的需求和项目要求进一步探索和学习。

## HashMap相关

HashTable和HashMap是Java中用于存储键值对的两个常见的集合类。它们之间有以下区别：

1. 线程安全性：HashTable是线程安全的，而HashMap不是。在多线程环境下，多个线程可以同时对HashTable进行操作，而HashMap在没有额外的同步措施的情况下并不是线程安全的。

2. null键和null值：HashTable不允许null键或null值的存在，当尝试插入null键或null值时，会抛出NullPointerException。而HashMap允许一个null键和多个null值的存在。

3. 继承关系：HashTable是Hashtable类的子类，而HashMap是AbstractMap类的子类，并且实现了Map接口。

4. 性能：由于线程安全性的考虑，HashTable在并发环境下会引入额外的开销，因此相对于HashMap而言，它的性能较差。在单线程环境下，HashMap通常具有更好的性能。

5. 迭代顺序：HashMap不保证迭代顺序与元素插入顺序一致，而HashTable和Enumeration（HashTable的迭代器）迭代顺序与元素插入顺序一致。

综上所述，如果您需要在多线程环境下使用或需要禁止null键或null值的存在，可以选择使用HashTable。如果您在单线程环境下运行，并且希望获得更好的性能，则可以使用HashMap。



ConcurrentHashMap和HashMap是Java集合框架中的两个不同的实现，它们在多线程环境下的行为和性能方面存在一些区别。

ConcurrentHashMap适用于多线程环境，可以提供较好的并发性能和线程安全性。而HashMap在单线程环境下的性能可能更好，但在多线程环境下需要外部同步手段来确保线程安全性。

## ==和equel的区别

对于基本数据类型，`==` 比较的是它们的值是否相等。

对于引用类型（对象），`==` 比较的是它们的引用是否相同，即它们是否指向同一个对象。

`equals()` 方法是用于比较两个对象的内容是否相等。通常情况下，需要重写。默认情况下，`equals()` 方法与 `==` 操作符的行为相同，即比较对象的引用。

## StringBuilder和StringBuffer和string的区别

`Builder`、`StringBuffer` 和 `String` 是在 Java 中用于处理字符串的类，它们之间有一些区别和适用场景。

1. `String` 类:
   - `String` 类是不可变的，一旦创建，它的值就不能被修改。
   - 每次对 `String` 类的操作都会创建一个新的字符串对象，因此频繁的字符串操作会导致大量的对象创建和内存消耗。
   - `String` 类适合于字符串不需要频繁修改的情况，例如保存常量、进行字符串比较和匹配等操作。
   - string不能被继承因为它被final修饰
2. `StringBuilder` 类:
   - `StringBuilder` 类是可变的，它提供了修改字符串内容的方法，例如追加、插入、替换等操作。
   - `StringBuilder` 类的操作不会创建新的对象，而是在原有对象的基础上进行修改，因此效率更高。
   - `StringBuilder` 类适合于需要频繁进行字符串修改的情况，例如动态拼接字符串、构建大段文本等操作。
3. `StringBuffer` 类:
   - `StringBuffer` 类与 `StringBuilder` 类类似，也是可变的，并提供了修改字符串内容的方法。
   - 与 `StringBuilder` 不同的是，`StringBuffer` 类的方法是线程安全的，因此在多线程环境中使用更加可靠，但性能稍低。StringBuffer的核心方法，如append、insert和delete等，在方法内部使用了synchronized关键字进行同步。这意味着一次只能有一个线程访问和修改StringBuffer对象，确保了线程安全性。
   - `StringBuffer` 类适合于需要线程安全的字符串修改操作的情况。

综上所述，`String` 类适合于不需要修改字符串的场景，`StringBuilder` 类适合于需要频繁修改字符串的场景，而 `StringBuffer` 类适合于需要线程安全的字符串修改操作的场景。根据具体的需求和使用场景，选择适合的类可以提高代码效率和可靠性。



执行顺序  StringBuilder>StringBuffer>string