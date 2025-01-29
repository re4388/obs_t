## 1. 使用 Spring 框架的好处是什么？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#1-%E4%BD%BF%E7%94%A8spring%E6%A1%86%E6%9E%B6%E7%9A%84%E5%A5%BD%E5%A4%84%E6%98%AF%E4%BB%80%E4%B9%88)

- ** 轻量：**Spring 是轻量的，基本的版本大约 2MB
- ** 控制反转：**Spring 通过控制反转实现了松散耦合，对象们给出它们的依赖，而不是创建或查找依赖的对象们
- ** 面向切面的编程 (AOP)：**Spring 支持面向切面的编程，并且把应用业务逻辑和系统服务分开
- ** 容器：**Spring 包含并管理应用中对象的生命周期和配置
- **MVC 框架：**Spring 的 WEB 框架是个精心设计的框架，是 Web 框架的一个很好的替代品
- ** 事务管理：**Spring 提供一个持续的事务管理接口，可以扩展到上至本地事务下至全局事务（JTA）
- ** 异常处理：**Spring 提供方便的 API 把具体技术相关的异常（比如由 JDBC，Hibernate or JDO 抛出的）转化为一致的 unchecked 异常。

## 2. 什么是 Spring IOC 容器？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#2-%E4%BB%80%E4%B9%88%E6%98%AF-spring-ioc-%E5%AE%B9%E5%99%A8)

Spring 框架的核心是 Spring 容器。容器创建对象，将它们装配在一起，配置它们并管理它们的完整生命周期。Spring 容器使用依赖注入来管理组成应用程序的组件。容器通过读取提供的配置元数据来接收对象进行实例化，配置和组装的指令。该元数据可以通过 XML，Java 注解或 Java 代码提供。


## 3. 什么是依赖注入？可以通过多少种方式完成依赖注入？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#3-%E4%BB%80%E4%B9%88%E6%98%AF%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5%E5%8F%AF%E4%BB%A5%E9%80%9A%E8%BF%87%E5%A4%9A%E5%B0%91%E7%A7%8D%E6%96%B9%E5%BC%8F%E5%AE%8C%E6%88%90%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5)

在依赖注入中，您不必创建对象，但必须描述如何创建它们。您不是直接在代码中将组件和服务连接在一起，而是描述配置文件中哪些组件需要哪些服务。由 IoC 容器将它们装配在一起。

通常，依赖注入可以通过三种方式完成，即：

- 构造函数注入
- setter 注入
- 接口注入

在 Spring Framework 中，仅使用构造函数和 setter 注入。

## 4. 区分 BeanFactory 和 ApplicationContext？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#4-%E5%8C%BA%E5%88%86-beanfactory-%E5%92%8C-applicationcontext)

|BeanFactory|ApplicationContext|
|---|---|
|它使用懒加载|它使用即时加载|
|它使用语法显式提供资源对象|它自己创建和管理资源对象|
|不支持国际化|支持国际化|
|不支持基于依赖的注解|支持基于依赖的注解|

BeanFactory 和 ApplicationContext 的优缺点分析：

BeanFactory 的优缺点：
- 优点：应用启动的时候占用资源很少，对资源要求较高的应用，比较有优势；
- 缺点：运行速度会相对来说慢一些。而且有可能会出现空指针异常的错误，而且通过 Bean 工厂创建的 Bean 生命周期会简单一些。

ApplicationContext 的优缺点：
- 优点：所有的 Bean 在启动的时候都进行了加载，系统运行的速度快；在系统启动的时候，可以发现系统中的配置问题。
- 缺点：把费时的操作放到系统启动中完成，所有的对象都可以预加载，缺点就是内存占用较大。

## 5. 区分构造函数注入和 setter 注入

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#5-%E5%8C%BA%E5%88%86%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0%E6%B3%A8%E5%85%A5%E5%92%8C-setter-%E6%B3%A8%E5%85%A5)

|构造函数注入|setter 注入|
|---|---|
|没有部分注入|有部分注入|
|不会覆盖 setter 属性|会覆盖 setter 属性|
|任意修改都会创建一个新实例|任意修改不会创建一个新实例|
|适用于设置很多属性|适用于设置少量属性|

## 6. spring 提供了哪些配置方式？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#6-spring-%E6%8F%90%E4%BE%9B%E4%BA%86%E5%93%AA%E4%BA%9B%E9%85%8D%E7%BD%AE%E6%96%B9%E5%BC%8F)

- 基于 xml 配置

bean 所需的依赖项和服务在 XML 格式的配置文件中指定。这些配置文件通常包含许多 bean 定义和特定于应用程序的配置选项。它们通常以 bean 标签开头。例如：

```xml
<bean id="studentbean" class="org.edureka.firstSpring.StudentBean">
 <property name="name" value="Edureka"></property>
</bean>
```

- 基于注解配置

您可以通过在相关的类，方法或字段声明上使用注解，将 bean 配置为组件类本身，而不是使用 XML 来描述 bean 装配。默认情况下，Spring 容器中未打开注解装配。因此，您需要在使用它之前在 Spring 配置文件中启用它。例如：

```xml
<beans>
<context:annotation-config/>
<!-- bean definitions go here -->
</beans>
```

- 基于 Java API 配置

Spring 的 Java 配置是通过使用 @Bean 和 @Configuration 来实现。

1. @Bean 注解扮演与 `<bean />` 元素相同的角色。
2. @Configuration 类允许通过简单地调用同一个类中的其他 @Bean 方法来定义 bean 间依赖关系。

例如：

```java
@Configuration
public class StudentConfig {
    @Bean
    public StudentBean myStudent() {
        return new StudentBean();
    }
}
```

## 7. Spring 中的 bean 的作用域有哪些？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#7-spring-%E4%B8%AD%E7%9A%84-bean-%E7%9A%84%E4%BD%9C%E7%94%A8%E5%9F%9F%E6%9C%89%E5%93%AA%E4%BA%9B)

- singleton : 唯一 bean 实例，Spring 中的 bean 默认都是单例的。
- prototype : 每次请求都会创建一个新的 bean 实例。
- request : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP request 内有效。
- session : ：在一个 HTTP Session 中，一个 Bean 定义对应一个实例。该作用域仅在基于 web 的 Spring ApplicationContext 情形下有效。
- global-session： 全局 session 作用域，仅仅在基于 portlet 的 web 应用中才有意义，Spring5 已经没有了。Portlet 是能够生成语义代码 (例如：HTML) 片段的小型 Java Web 插件。它们基于 portlet 容器，可以像 servlet 一样处理 HTTP 请求。但是，与 servlet 不同，每个 portlet 都有不同的会话

## 8. 如何理解 IoC 和 DI？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#8-%E5%A6%82%E4%BD%95%E7%90%86%E8%A7%A3ioc%E5%92%8Cdi)

IOC 就是控制反转，通俗的说就是我们不用自己创建实例对象，这些都交给 Spring 的 bean 工厂帮我们创建管理。这也是 Spring 的核心思想，通过面向接口编程的方式来是实现对业务组件的动态依赖。这就意味着 IOC 是 Spring 针对解决程序耦合而存在的。在实际应用中，Spring 通过配置文件（xml 或者 properties）指定需要实例化的 java 类（类名的完整字符串），包括这些 java 类的一组初始化值，通过加载读取配置文件，用 Spring 提供的方法（getBean ()）就可以获取到我们想要的根据指定配置进行初始化的实例对象。

- 优点：IOC 或依赖注入减少了应用程序的代码量。它使得应用程序的测试很简单，因为在单元测试中不再需要单例或 JNDI 查找机制。简单的实现以及较少的干扰机制使得松耦合得以实现。IOC 容器支持勤性单例及延迟加载服务。

**DI：DI—Dependency** Injection，即 “依赖注入”：组件之间依赖关系由容器在运行期决定，形象的说，即由容器动态的将某个依赖关系注入到组件之中。依赖注入的目的并非为软件系统带来更多功能，而是为了提升组件重用的频率，并为系统搭建一个灵活、可扩展的平台。通过依赖注入机制，我们只需要通过简单的配置，而无需任何代码就可指定目标需要的资源，完成自身的业务逻辑，而不需要关心具体的资源来自何处，由谁实现。

## 9. 将一个类声明为 Spring 的 bean 的注解有哪些？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#9-%E5%B0%86%E4%B8%80%E4%B8%AA%E7%B1%BB%E5%A3%B0%E6%98%8E%E4%B8%BAspring%E7%9A%84-bean-%E7%9A%84%E6%B3%A8%E8%A7%A3%E6%9C%89%E5%93%AA%E4%BA%9B)

我们一般使用 @Autowired 注解自动装配 bean，要想把类标识成可用于 @Autowired 注解自动装配的 bean 的类，采用以下注解可实现：

- @Component ：通用的注解，可标注任意类为 Spring 组件。如果一个 Bean 不知道属于哪个层，可以使用 @Component 注解标注。 8 @Repository : 对应持久层即 Dao 层，主要用于数据库相关操作。
- @Service : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- @Controller : 对应 Spring MVC 控制层，主要用户接受用户请求并调用 Service 层返回数据给前端页面。

## 10. spring 支持几种 bean scope？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#10-spring-%E6%94%AF%E6%8C%81%E5%87%A0%E7%A7%8D-bean-scope)

Spring bean 支持 5 种 scope：

- **Singleton** - 每个 Spring IoC 容器仅有一个单实例。
- **Prototype** - 每次请求都会产生一个新的实例。
- **Request** - 每一次 HTTP 请求都会产生一个新的实例，并且该 bean 仅在当前 HTTP 请求内有效。
- **Session** - 每一次 HTTP 请求都会产生一个新的 bean，同时该 bean 仅在当前 HTTP session 内有效。
- **Global-session** - 类似于标准的 HTTP Session 作用域，不过它仅仅在基于 portlet 的 web 应用中才有意义。Portlet 规范定义了全局 Session 的概念，它被所有构成某个 portlet web 应用的各种不同的 portlet 所共享。在 global session 作用域中定义的 bean 被限定于全局 portlet Session 的生命周期范围内。如果你在 web 中使用 global session 作用域来标识 bean，那么 web 会自动当成 session 类型来使用。

仅当用户使用支持 Web 的 ApplicationContext 时，最后三个才可用。

## 11. Spring 中的 bean 生命周期？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#11-spring-%E4%B8%AD%E7%9A%84-bean-%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F)

Bean 的生命周期是由容器来管理的。主要在创建和销毁两个时期。

[![](https://camo.githubusercontent.com/8c27c8c962746d6a58d1268fa3c533b99675a8d84536e20f074dd83a83c731ed/687474703a2f2f626c6f672d696d672e636f6f6c73656e2e636e2f696d672f313538333637353039303634315f35312e706e67)](https://camo.githubusercontent.com/8c27c8c962746d6a58d1268fa3c533b99675a8d84536e20f074dd83a83c731ed/687474703a2f2f626c6f672d696d672e636f6f6c73656e2e636e2f696d672f313538333637353039303634315f35312e706e67)

### 创建过程：

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#%E5%88%9B%E5%BB%BA%E8%BF%87%E7%A8%8B)

1，实例化 bean 对象，以及设置 bean 属性； 2，如果通过 Aware 接口声明了依赖关系，则会注入 Bean 对容器基础设施层面的依赖，Aware 接口是为了感知到自身的一些属性。容器管理的 Bean 一般不需要知道容器的状态和直接使用容器。但是在某些情况下是需要在 Bean 中对 IOC 容器进行操作的。这时候需要在 bean 中设置对容器的感知。SpringIOC 容器也提供了该功能，它是通过特定的 Aware 接口来完成的。 比如 BeanNameAware 接口，可以知道自己在容器中的名字。 如果这个 Bean 已经实现了 BeanFactoryAware 接口，可以用这个方式来获取其它 Bean。 （如果 Bean 实现了 BeanNameAware 接口，调用 setBeanName () 方法，传入 Bean 的名字。 如果 Bean 实现了 BeanClassLoaderAware 接口，调用 setBeanClassLoader () 方法，传入 ClassLoader 对象的实例。 如果 Bean 实现了 BeanFactoryAware 接口，调用 setBeanFactory () 方法，传入 BeanFactory 对象的实例。） 3，紧接着会调用 BeanPostProcess 的前置初始化方法 postProcessBeforeInitialization，主要作用是在 Spring 完成实例化之后，初始化之前，对 Spring 容器实例化的 Bean 添加自定义的处理逻辑。有点类似于 AOP。 4，如果实现了 BeanFactoryPostProcessor 接口的 afterPropertiesSet 方法，做一些属性被设定后的自定义的事情。 5，调用 Bean 自身定义的 init 方法，去做一些初始化相关的工作。 6，调用 BeanPostProcess 的后置初始化方法，postProcessAfterInitialization 去做一些 bean 初始化之后的自定义工作。 7，完成以上创建之后就可以在应用里使用这个 Bean 了。

### 销毁过程：

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#%E9%94%80%E6%AF%81%E8%BF%87%E7%A8%8B)

当 Bean 不再用到，便要销毁 1，若实现了 DisposableBean 接口，则会调用 destroy 方法； 2，若配置了 destry-method 属性，则会调用其配置的销毁方法；

### 总结

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#%E6%80%BB%E7%BB%93)

主要把握创建过程和销毁过程这两个大的方面； 创建过程：首先实例化 Bean，并设置 Bean 的属性，根据其实现的 Aware 接口（主要是 BeanFactoryAware 接口，BeanFactoryAware，ApplicationContextAware）设置依赖信息， 接下来调用 BeanPostProcess 的 postProcessBeforeInitialization 方法，完成 initial 前的自定义逻辑；afterPropertiesSet 方法做一些属性被设定后的自定义的事情；调用 Bean 自身定义的 init 方法，去做一些初始化相关的工作；然后再调用 postProcessAfterInitialization 去做一些 bean 初始化之后的自定义工作。这四个方法的调用有点类似 AOP。 此时，Bean 初始化完成，可以使用这个 Bean 了。 销毁过程：如果实现了 DisposableBean 的 destroy 方法，则调用它，如果实现了自定义的销毁方法，则调用之。

## 12. 什么是 spring 的内部 bean？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#12-%E4%BB%80%E4%B9%88%E6%98%AF-spring-%E7%9A%84%E5%86%85%E9%83%A8-bean)

只有将 bean 用作另一个 bean 的属性时，才能将 bean 声明为内部 bean。为了定义 bean，Spring 的基于 XML 的配置元数据在 `<property>` 或 `<constructor-arg>` 中提供了 `<bean>` 元素的使用。内部 bean 总是匿名的，它们总是作为原型。

例如，假设我们有一个 Student 类，其中引用了 Person 类。这里我们将只创建一个 Person 类实例并在 Student 中使用它。

Student.java

```java
public class Student {
    private Person person;
    //Setters and Getters
}
public class Person {
    private String name;
    private String address;
    //Setters and Getters
}
```

bean.xml

```xml
<bean id=“StudentBean" class="com.edureka.Student">
    <property name="person">
        <!--This is inner bean -->
        <bean class="com.edureka.Person">
            <property name="name" value=“Scott"></property>
            <property name="address" value=“Bangalore"></property>
        </bean>
    </property>
</bean>
```

## 13. 什么是 spring 装配？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#13-%E4%BB%80%E4%B9%88%E6%98%AF-spring-%E8%A3%85%E9%85%8D)

当 bean 在 Spring 容器中组合在一起时，它被称为装配或 bean 装配。 Spring 容器需要知道需要什么 bean 以及容器应该如何使用依赖注入来将 bean 绑定在一起，同时装配 bean。

Spring 容器能够自动装配 bean。也就是说，可以通过检查 BeanFactory 的内容让 Spring 自动解析 bean 的协作者。

自动装配的不同模式：

- **no** - 这是默认设置，表示没有自动装配。应使用显式 bean 引用进行装配。
- **byName** - 它根据 bean 的名称注入对象依赖项。它匹配并装配其属性与 XML 文件中由相同名称定义的 bean。
- **byType** - 它根据类型注入对象依赖项。如果属性的类型与 XML 文件中的一个 bean 名称匹配，则匹配并装配属性。
- **构造函数** - 它通过调用类的构造函数来注入依赖项。它有大量的参数。
- **autodetect** - 首先容器尝试通过构造函数使用 autowire 装配，如果不能，则尝试通过 byType 自动装配。

## 14. 自动装配有什么局限？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#14-%E8%87%AA%E5%8A%A8%E8%A3%85%E9%85%8D%E6%9C%89%E4%BB%80%E4%B9%88%E5%B1%80%E9%99%90)

- 覆盖的可能性 - 您始终可以使用 `<constructor-arg>` 和 `<property>` 设置指定依赖项，这将覆盖自动装配。
- 基本元数据类型 - 简单属性（如原数据类型，字符串和类）无法自动装配。
- 令人困惑的性质 - 总是喜欢使用明确的装配，因为自动装配不太精确。

## 15. Spring 中出现同名 bean 怎么办？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#15-spring%E4%B8%AD%E5%87%BA%E7%8E%B0%E5%90%8C%E5%90%8Dbean%E6%80%8E%E4%B9%88%E5%8A%9E)

- 同一个配置文件内同名的 Bean，以最上面定义的为准
- 不同配置文件中存在同名 Bean，后解析的配置文件会覆盖先解析的配置文件
- 同文件中 ComponentScan 和 @Bean 出现同名 Bean。同文件下 @Bean 的会生效，@ComponentScan 扫描进来不会生效。通过 @ComponentScan 扫描进来的优先级是最低的，原因就是它扫描进来的 Bean 定义是最先被注册的～

## 16. Spring 怎么解决循环依赖问题？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#16-spring-%E6%80%8E%E4%B9%88%E8%A7%A3%E5%86%B3%E5%BE%AA%E7%8E%AF%E4%BE%9D%E8%B5%96%E9%97%AE%E9%A2%98)

spring 对循环依赖的处理有三种情况： ①构造器的循环依赖：这种依赖 spring 是处理不了的，直 接抛出 BeanCurrentlylnCreationException 异常。 ②单例模式下的 setter 循环依赖：通过 “三级缓存” 处理循环依赖。 ③非单例循环依赖：无法处理。

下面分析单例模式下的 setter 循环依赖如何解决

Spring 的单例对象的初始化主要分为三步： [![](https://camo.githubusercontent.com/9a5d113cdfb661b0dad0ad52bdaac37f184c9e2142a7b81040021944c2bdb8c9/687474703a2f2f626c6f672d696d672e636f6f6c73656e2e636e2f696d672f313538343736313431333334315f31322e706e67)](https://camo.githubusercontent.com/9a5d113cdfb661b0dad0ad52bdaac37f184c9e2142a7b81040021944c2bdb8c9/687474703a2f2f626c6f672d696d672e636f6f6c73656e2e636e2f696d672f313538343736313431333334315f31322e706e67)

（1）createBeanInstance：实例化，其实也就是调用对象的构造方法实例化对象

（2）populateBean：填充属性，这一步主要是多 bean 的依赖属性进行填充

（3）initializeBean：调用 spring xml 中的 init 方法。

从上面讲述的单例 bean 初始化步骤我们可以知道，循环依赖主要发生在第一、第二部。也就是构造器循环依赖和 field 循环依赖。

[![](https://camo.githubusercontent.com/2a7876faf1e51c2751c5004d7371d58b53a69e90c3ad0f7c94af9fc0d3524089/687474703a2f2f626c6f672d696d672e636f6f6c73656e2e636e2f696d672f313538343735383330393631365f31302e706e67)](https://camo.githubusercontent.com/2a7876faf1e51c2751c5004d7371d58b53a69e90c3ad0f7c94af9fc0d3524089/687474703a2f2f626c6f672d696d672e636f6f6c73656e2e636e2f696d672f313538343735383330393631365f31302e706e67)

举例：A 的某个 field 或者 setter 依赖了 B 的实例对象，同时 B 的某个 field 或者 setter 依赖了 A 的实例对象” 这种循环依赖的情况。A 首先完成了

初始化的第一步（createBeanINstance 实例化），并且将自己提前曝光到 singletonFactories 中。

此时进行初始化的第二步，发现自己依赖对象 B，此时就尝试去 get (B)，发现 B 还没有被 create，所以走 create 流程，B 在初始化第一步的时候发现自己依赖了对象 A，于是尝试 get (A)，尝试一级缓存 singletonObjects (肯定没有，因为 A 还没初始化完全)，尝试二级缓存 earlySingletonObjects（也没有），尝试三级缓存 singletonFactories，由于 A 通过 ObjectFactory 将自己提前曝光了，所以 B 能够通过

ObjectFactory.getObject 拿到 A 对象 (虽然 A 还没有初始化完全，但是总比没有好呀)，B 拿到 A 对象后顺利完成了初始化阶段 1、2、3，完全初始化之后将自己放入到一级缓存 singletonObjects 中。

此时返回 A 中，A 此时能拿到 B 的对象顺利完成自己的初始化阶段 2、3，最终 A 也完成了初始化，进去了一级缓存 singletonObjects 中，而且更加幸运的是，由于 B 拿到了 A 的对象引用，所以 B 现在 hold 住的 A 对象完成了初始化。

## 17. Spring 中的单例 bean 的线程安全问题？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#17-spring-%E4%B8%AD%E7%9A%84%E5%8D%95%E4%BE%8B-bean-%E7%9A%84%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8%E9%97%AE%E9%A2%98)

当多个用户同时请求一个服务时，容器会给每一个请求分配一个线程，这时多个线程会并发执行该请求对应的业务逻辑（成员方法），此时就要注意了，如果该处理逻辑中有对单例状态的修改（体现为该单例的成员属性），则必须考虑线程同步问题。 **线程安全问题都是由全局变量及静态变量引起的。** 若每个线程中对全局变量、静态变量只有读操作，而无写操作，一般来说，这个全局变量是线程安全的；若有多个线程同时执行写操作，一般都需要考虑线程同步，否则就可能影响线程安全.

**无状态 bean 和有状态 bean**

- 有状态就是有数据存储功能。有状态对象 (Stateful Bean)，就是有实例变量的对象，可以保存数据，是非线程安全的。在不同方法调用间不保留任何状态。
- 无状态就是一次操作，不能保存数据。无状态对象 (Stateless Bean)，就是没有实例变量的对象。不能保存数据，是不变类，是线程安全的。

在 spring 中无状态的 Bean 适合用不变模式，就是单例模式，这样可以共享实例提高性能。有状态的 Bean 在多线程环境下不安全，适合用 Prototype 原型模式。 Spring 使用 ThreadLocal 解决线程安全问题。如果你的 Bean 有多种状态的话（比如 View Model 对象），就需要自行保证线程安全 。

## 18. 什么是 AOP？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#18-%E4%BB%80%E4%B9%88%E6%98%AF-aop)

AOP (Aspect-Oriented Programming), 即 **面向切面编程** , 它与 OOP (Object-Oriented Programming, 面向对象编程) 相辅相成，提供了与 OOP 不同的抽象软件结构的视角. 在 OOP 中，我们以类 (class) 作为我们的基本单元，而 AOP 中的基本单元是 **Aspect (切面)**

## 19. AOP 有哪些实现方式？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#19-aop-%E6%9C%89%E5%93%AA%E4%BA%9B%E5%AE%9E%E7%8E%B0%E6%96%B9%E5%BC%8F)

实现 AOP 的技术，主要分为两大类：

- 静态代理 - 指使用 AOP 框架提供的命令进行编译，从而在编译阶段就可生成 AOP 代理类，因此也称为编译时增强；
    - 编译时编织（特殊编译器实现）
    - 类加载时编织（特殊的类加载器实现）。
- 动态代理 - 在运行时在内存中 “临时” 生成 AOP 动态代理类，因此也被称为运行时增强。
    - `JDK` 动态代理：通过反射来接收被代理的类，并且要求被代理的类必须实现一个接口 。JDK 动态代理的核心是 InvocationHandler 接口和 Proxy 类 。
    - `CGLIB` 动态代理： 如果目标类没有实现接口，那么 `Spring AOP` 会选择使用 `CGLIB` 来动态代理目标类 。`CGLIB` （ Code Generation Library ），是一个代码生成的类库，可以在运行时动态的生成某个类的子类，注意， `CGLIB` 是通过继承的方式做的动态代理，因此如果某个类被标记为 `final` ，那么它是无法使用 `CGLIB` 做动态代理的。

## 20. Spring AOP and AspectJ AOP 有什么区别？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#20-spring-aop-and-aspectj-aop-%E6%9C%89%E4%BB%80%E4%B9%88%E5%8C%BA%E5%88%AB)

Spring AOP 基于动态代理方式实现；AspectJ 基于静态代理方式实现。 Spring AOP 仅支持方法级别的 PointCut；提供了完全的 AOP 支持，它还支持属性级别的 PointCut。

## 21. Spring 框架中用到了哪些设计模式？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#21-spring-%E6%A1%86%E6%9E%B6%E4%B8%AD%E7%94%A8%E5%88%B0%E4%BA%86%E5%93%AA%E4%BA%9B%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)

**工厂设计模式** : Spring 使用工厂模式通过 `BeanFactory`、`ApplicationContext` 创建 bean 对象。

**代理设计模式** : Spring AOP 功能的实现。

**单例设计模式** : Spring 中的 Bean 默认都是单例的。

**模板方法模式** : Spring 中 `jdbcTemplate`、`hibernateTemplate` 等以 Template 结尾的对数据库操作的类，它们就使用到了模板模式。

**包装器设计模式** : 我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。

**观察者模式:** Spring 事件驱动模型就是观察者模式很经典的一个应用。

**适配器模式** :Spring AOP 的增强或通知 (Advice) 使用到了适配器模式、spring MVC 中也是用到了适配器模式适配 `Controller`。

## 22. Spring 事务实现方式有哪些？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#22-spring-%E4%BA%8B%E5%8A%A1%E5%AE%9E%E7%8E%B0%E6%96%B9%E5%BC%8F%E6%9C%89%E5%93%AA%E4%BA%9B)

- 编程式事务管理：这意味着你可以通过编程的方式管理事务，这种方式带来了很大的灵活性，但很难维护。
- 声明式事务管理：这种方式意味着你可以将事务管理和业务代码分离。你只需要通过注解或者 XML 配置管理事务。

## 23. Spring 框架的事务管理有哪些优点？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#23-spring%E6%A1%86%E6%9E%B6%E7%9A%84%E4%BA%8B%E5%8A%A1%E7%AE%A1%E7%90%86%E6%9C%89%E5%93%AA%E4%BA%9B%E4%BC%98%E7%82%B9)

- 它提供了跨不同事务 api（如 JTA、JDBC、Hibernate、JPA 和 JDO）的一致编程模型。
    
- 它为编程事务管理提供了比 JTA 等许多复杂事务 API 更简单的 API。
    
- 它支持声明式事务管理。
    
- 它很好地集成了 Spring 的各种数据访问抽象。
    

## 24. spring 事务定义的传播规则

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#24-spring%E4%BA%8B%E5%8A%A1%E5%AE%9A%E4%B9%89%E7%9A%84%E4%BC%A0%E6%92%AD%E8%A7%84%E5%88%99)

- PROPAGATION_REQUIRED: 支持当前事务，如果当前没有事务，就新建一个事务。这是最常见的选择。
- PROPAGATION_SUPPORTS: 支持当前事务，如果当前没有事务，就以非事务方式执行。
- PROPAGATION_MANDATORY: 支持当前事务，如果当前没有事务，就抛出异常。
- PROPAGATION_REQUIRES_NEW: 新建事务，如果当前存在事务，把当前事务挂起。
- PROPAGATION_NOT_SUPPORTED: 以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
- PROPAGATION_NEVER: 以非事务方式执行，如果当前存在事务，则抛出异常。
- PROPAGATION_NESTED: 如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则进行与 PROPAGATION_REQUIRED 类似的操作。

## 25. SpringMVC 工作原理了解吗？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#25-springmvc-%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86%E4%BA%86%E8%A7%A3%E5%90%97)

**原理如下图所示：**

[![img](https://camo.githubusercontent.com/0cb24416366238d11d5258c7ab47e0fed2fd84efe73947b525a7d209d8b3cfe0/687474703a2f2f626c6f672d696d672e636f6f6c73656e2e636e2f696d672f5370696e674d56432d50726f636573732e6a7067)](https://camo.githubusercontent.com/0cb24416366238d11d5258c7ab47e0fed2fd84efe73947b525a7d209d8b3cfe0/687474703a2f2f626c6f672d696d672e636f6f6c73656e2e636e2f696d672f5370696e674d56432d50726f636573732e6a7067)

上图的一个笔误的小问题：Spring MVC 的入口函数也就是前端控制器 `DispatcherServlet` 的作用是接收请求，响应结果。

**流程说明（重要）：**

1. 客户端（浏览器）发送请求，直接请求到 `DispatcherServlet`。
2. `DispatcherServlet` 根据请求信息调用 `HandlerMapping`，解析请求对应的 `Handler`。
3. 解析到对应的 `Handler`（也就是我们平常说的 `Controller` 控制器）后，开始由 `HandlerAdapter` 适配器处理。
4. `HandlerAdapter` 会根据 `Handler` 来调用真正的处理器开处理请求，并处理相应的业务逻辑。
5. 处理器处理完业务后，会返回一个 `ModelAndView` 对象，`Model` 是返回的数据对象，`View` 是个逻辑上的 `View`。
6. `ViewResolver` 会根据逻辑 `View` 查找实际的 `View`。
7. `DispaterServlet` 把返回的 `Model` 传给 `View`（视图渲染）。
8. 把 `View` 返回给请求者（浏览器）

## 26. 简单介绍 Spring MVC 的核心组件

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#26-%E7%AE%80%E5%8D%95%E4%BB%8B%E7%BB%8D-spring-mvc-%E7%9A%84%E6%A0%B8%E5%BF%83%E7%BB%84%E4%BB%B6)

那么接下来就简单介绍一下 `DispatcherServlet` 和九大组件（按使用顺序排序的）：

|组件|说明|
|:--|:--|
|DispatcherServlet|Spring MVC 的核心组件，是请求的入口，负责协调各个组件工作|
|MultipartResolver|内容类型 ( `Content-Type` ) 为 `multipart/*` 的请求的解析器，例如解析处理文件上传的请求，便于获取参数信息以及上传的文件|
|HandlerMapping|请求的处理器匹配器，负责为请求找到合适的 `HandlerExecutionChain` 处理器执行链，包含处理器（`handler`）和拦截器们（`interceptors`）|
|HandlerAdapter|处理器的适配器。因为处理器 `handler` 的类型是 Object 类型，需要有一个调用者来实现 `handler` 是怎么被执行。Spring 中的处理器的实现多变，比如用户处理器可以实现 Controller 接口、HttpRequestHandler 接口，也可以用 `@RequestMapping` 注解将方法作为一个处理器等，这就导致 Spring MVC 无法直接执行这个处理器。所以这里需要一个处理器适配器，由它去执行处理器|
|HandlerExceptionResolver|处理器异常解析器，将处理器（ `handler` ）执行时发生的异常，解析 (转换) 成对应的 ModelAndView 结果|
|RequestToViewNameTranslator|视图名称转换器，用于解析出请求的默认视图名|
|LocaleResolver|本地化（国际化）解析器，提供国际化支持|
|ThemeResolver|主题解析器，提供可设置应用整体样式风格的支持|
|ViewResolver|视图解析器，根据视图名和国际化，获得最终的视图 View 对象|
|FlashMapManager|FlashMap 管理器，负责重定向时，保存参数至临时存储（默认 Session）|

Spring MVC 对各个组件的职责划分的比较清晰。`DispatcherServlet` 负责协调，其他组件则各自做分内之事，互不干扰。

## 27. @Controller 注解有什么用？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#27-controller-%E6%B3%A8%E8%A7%A3%E6%9C%89%E4%BB%80%E4%B9%88%E7%94%A8)

`@Controller` 注解标记一个类为 Spring Web MVC **控制器** Controller。Spring MVC 会将扫描到该注解的类，然后扫描这个类下面带有 `@RequestMapping` 注解的方法，根据注解信息，为这个方法生成一个对应的**处理器**对象，在上面的 HandlerMapping 和 HandlerAdapter 组件中讲到过。

当然，除了添加 `@Controller` 注解这种方式以外，你还可以实现 Spring MVC 提供的 `Controller` 或者 `HttpRequestHandler` 接口，对应的实现类也会被作为一个**处理器**对象

## 28. @RequestMapping 注解有什么用？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#28-requestmapping-%E6%B3%A8%E8%A7%A3%E6%9C%89%E4%BB%80%E4%B9%88%E7%94%A8)

`@RequestMapping` 注解，在上面已经讲过了，配置**处理器**的 HTTP 请求方法，URI 等信息，这样才能将请求和方法进行映射。这个注解可以作用于类上面，也可以作用于方法上面，在类上面一般是配置这个**控制器**的 URI 前缀

## 29. @RestController 和 @Controller 有什么区别？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#29-restcontroller-%E5%92%8C-controller-%E6%9C%89%E4%BB%80%E4%B9%88%E5%8C%BA%E5%88%AB)

`@RestController` 注解，在 `@Controller` 基础上，增加了 `@ResponseBody` 注解，更加适合目前前后端分离的架构下，提供 Restful API ，返回例如 JSON 数据格式。当然，返回什么样的数据格式，根据客户端的 `ACCEPT` 请求头来决定。

## 30. @RequestMapping 和 @GetMapping 注解的不同之处在哪里？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#30-requestmapping-%E5%92%8C-getmapping-%E6%B3%A8%E8%A7%A3%E7%9A%84%E4%B8%8D%E5%90%8C%E4%B9%8B%E5%A4%84%E5%9C%A8%E5%93%AA%E9%87%8C)

1. `@RequestMapping`：可注解在类和方法上；`@GetMapping` 仅可注册在方法上
2. `@RequestMapping`：可进行 GET、POST、PUT、DELETE 等请求方法；`@GetMapping` 是 `@RequestMapping` 的 GET 请求方法的特例，目的是为了提高清晰度。

## 31. @RequestParam 和 @PathVariable 两个注解的区别

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#31-requestparam-%E5%92%8C-pathvariable-%E4%B8%A4%E4%B8%AA%E6%B3%A8%E8%A7%A3%E7%9A%84%E5%8C%BA%E5%88%AB)

两个注解都用于方法参数，获取参数值的方式不同，`@RequestParam` 注解的参数从请求携带的参数中获取，而 `@PathVariable` 注解从请求的 URI 中获取

## 32. 返回 JSON 格式使用什么注解？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#32-%E8%BF%94%E5%9B%9E-json-%E6%A0%BC%E5%BC%8F%E4%BD%BF%E7%94%A8%E4%BB%80%E4%B9%88%E6%B3%A8%E8%A7%A3)

可以使用 **`@ResponseBody`** 注解，或者使用包含 `@ResponseBody` 注解的 **`@RestController`** 注解。

当然，还是需要配合相应的支持 JSON 格式化的 HttpMessageConverter 实现类。例如，Spring MVC 默认使用 MappingJackson2HttpMessageConverter。

## 33. 什么是 springmvc 拦截器以及如何使用它？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#33-%E4%BB%80%E4%B9%88%E6%98%AFspringmvc%E6%8B%A6%E6%88%AA%E5%99%A8%E4%BB%A5%E5%8F%8A%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8%E5%AE%83)

Spring 的处理程序映射机制包括处理程序拦截器，当你希望将特定功能应用于某些请求时，例如，检查用户主题时，这些拦截器非常有用。拦截器必须实现 org.springframework.web.servlet 包的 HandlerInterceptor。此接口定义了三种方法：

- preHandle：在执行实际处理程序之前调用。
- postHandle：在执行完实际程序之后调用。
- afterCompletion：在完成请求后调用。

## 34. Spring MVC 和 Struts2 的异同？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#34-spring-mvc-%E5%92%8C-struts2-%E7%9A%84%E5%BC%82%E5%90%8C)

**入口**不同

- Spring MVC 的入门是一个 Servlet **控制器**。
- Struts2 入门是一个 Filter **过滤器**。

**配置映射**不同，

- Spring MVC 是基于**方法**开发，传递参数是通过**方法形参**，一般设置为**单例**。
- Struts2 是基于**类**开发，传递参数是通过**类的属性**，只能设计为**多例**。

**视图**不同

- Spring MVC 通过参数解析器是将 Request 对象内容进行解析成方法形参，将响应数据和页面封装成 **ModelAndView** 对象，最后又将模型数据通过 **Request** 对象传输到页面。其中，如果视图使用 JSP 时，默认使用 **JSTL** 。
- Struts2 采用**值栈**存储请求和响应的数据，通过 **OGNL** 存取数据。

## 35. REST 代表着什么？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#35-rest-%E4%BB%A3%E8%A1%A8%E7%9D%80%E4%BB%80%E4%B9%88)

REST 代表着抽象状态转移，它是根据 HTTP 协议从客户端发送数据到服务端，例如：服务端的一本书可以以 XML 或 JSON 格式传递到客户端

可以看看 [REST API design and development](http://bit.ly/2zIGzWK) ，知乎上的 [《怎样用通俗的语言解释 REST，以及 RESTful？》](https://www.zhihu.com/question/28557115)了解。

## 36. 什么是安全的 REST 操作？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#36-%E4%BB%80%E4%B9%88%E6%98%AF%E5%AE%89%E5%85%A8%E7%9A%84-rest-%E6%93%8D%E4%BD%9C)

REST 接口是通过 HTTP 方法完成操作

- 一些 HTTP 操作是安全的，如 GET 和 HEAD ，它不能在服务端修改资源
- 换句话说，PUT、POST 和 DELETE 是不安全的，因为他们能修改服务端的资源

所以，是否安全的界限，在于**是否修改**服务端的资源

## 37. REST API 是无状态的吗？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#37-rest-api-%E6%98%AF%E6%97%A0%E7%8A%B6%E6%80%81%E7%9A%84%E5%90%97)

**是的**，REST API 应该是无状态的，因为它是基于 HTTP 的，它也是无状态的

REST API 中的请求应该包含处理它所需的所有细节。它**不应该**依赖于以前或下一个请求或服务器端维护的一些数据，例如会话

**REST 规范为使其无状态设置了一个约束，在设计 REST API 时，你应该记住这一点**

## 38. REST 安全吗？你能做什么来保护它？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#38-rest%E5%AE%89%E5%85%A8%E5%90%97-%E4%BD%A0%E8%83%BD%E5%81%9A%E4%BB%80%E4%B9%88%E6%9D%A5%E4%BF%9D%E6%8A%A4%E5%AE%83)

安全是一个宽泛的术语。它可能意味着消息的安全性，这是通过认证和授权提供的加密或访问限制提供的

REST 通常不是安全的，需要开发人员自己实现安全机制

## 39. 为什么要用 SpringBoot?

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#39-%E4%B8%BA%E4%BB%80%E4%B9%88%E8%A6%81%E7%94%A8springboot)

在使用 Spring 框架进行开发的过程中，需要配置很多 Spring 框架包的依赖，如 spring-core、spring-bean、spring-context 等，而这些配置通常都是重复添加的，而且需要做很多框架使用及环境参数的重复配置，如开启注解、配置日志等。Spring Boot 致力于弱化这些不必要的操作，提供默认配置，当然这些默认配置是可以按需修改的，快速搭建、开发和运行 Spring 应用。

以下是使用 SpringBoot 的一些好处：

- 自动配置，使用基于类路径和应用程序上下文的智能默认值，当然也可以根据需要重写它们以满足开发人员的需求。
- 创建 Spring Boot Starter 项目时，可以选择选择需要的功能，Spring Boot 将为你管理依赖关系。
- SpringBoot 项目可以打包成 jar 文件。可以使用 Java-jar 命令从命令行将应用程序作为独立的 Java 应用程序运行。
- 在开发 web 应用程序时，springboot 会配置一个嵌入式 Tomcat 服务器，以便它可以作为独立的应用程序运行。（Tomcat 是默认的，当然你也可以配置 Jetty 或 Undertow）
- SpringBoot 包括许多有用的非功能特性（例如安全和健康检查）。

## 40. Spring Boot 中如何实现对不同环境的属性配置文件的支持？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#40-spring-boot%E4%B8%AD%E5%A6%82%E4%BD%95%E5%AE%9E%E7%8E%B0%E5%AF%B9%E4%B8%8D%E5%90%8C%E7%8E%AF%E5%A2%83%E7%9A%84%E5%B1%9E%E6%80%A7%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E7%9A%84%E6%94%AF%E6%8C%81)

Spring Boot 支持不同环境的属性配置文件切换，通过创建 application-{profile}.properties 文件，其中 {profile} 是具体的环境标识名称，例如：application-dev.properties 用于开发环境，application-test.properties 用于测试环境，application-uat.properties 用于 uat 环境。如果要想使用 application-dev.properties 文件，则在 application.properties 文件中添加 spring.profiles.active=dev。

如果要想使用 application-test.properties 文件，则在 application.properties 文件中添加 spring.profiles.active=test。

## 41. Spring Boot 的核心注解是哪个？它主要由哪几个注解组成的？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#41-spring-boot-%E7%9A%84%E6%A0%B8%E5%BF%83%E6%B3%A8%E8%A7%A3%E6%98%AF%E5%93%AA%E4%B8%AA%E5%AE%83%E4%B8%BB%E8%A6%81%E7%94%B1%E5%93%AA%E5%87%A0%E4%B8%AA%E6%B3%A8%E8%A7%A3%E7%BB%84%E6%88%90%E7%9A%84)

启动类上面的注解是 @SpringBootApplication，它也是 Spring Boot 的核心注解，主要组合包含了以下 3 个注解：

@SpringBootConfiguration：组合了 @Configuration 注解，实现配置文件的功能。

@EnableAutoConfiguration：打开自动配置的功能，也可以关闭某个自动配置的选项，如关闭数据源自动配置功能： @SpringBootApplication (exclude = { DataSourceAutoConfiguration.class})。

@ComponentScan：Spring 组件扫描。

## 42. 你如何理解 Spring Boot 中的 Starters？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#42-%E4%BD%A0%E5%A6%82%E4%BD%95%E7%90%86%E8%A7%A3-spring-boot-%E4%B8%AD%E7%9A%84-starters)

Starters 可以理解为启动器，它包含了一系列可以集成到应用里面的依赖包，你可以一站式集成 Spring 及其他技术，而不需要到处找示例代码和依赖包。如你想使用 Spring JPA 访问数据库，只要加入 spring-boot-starter-data-jpa 启动器依赖就能使用了。

Starters 包含了许多项目中需要用到的依赖，它们能快速持续的运行，都是一系列得到支持的管理传递性依赖。

## 43. Spring Boot Starter 的工作原理是什么？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#43-spring-boot-starter-%E7%9A%84%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86%E6%98%AF%E4%BB%80%E4%B9%88)

Spring Boot 在启动的时候会干这几件事情：

- Spring Boot 在启动时会去依赖的 Starter 包中寻找 resources/META-INF/spring.factories 文件，然后根据文件中配置的 Jar 包去扫描项目所依赖的 Jar 包。
- 根据 spring.factories 配置加载 AutoConfigure 类
- 根据 @Conditional 注解的条件，进行自动配置并将 Bean 注入 Spring Context

总结一下，其实就是 Spring Boot 在启动的时候，按照约定去读取 Spring Boot Starter 的配置信息，再根据配置信息对资源进行初始化，并注入到 Spring 容器中。这样 Spring Boot 启动完毕后，就已经准备好了一切资源，使用过程中直接注入对应 Bean 资源即可

## 44. 保护 Spring Boot 应用有哪些方法？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#44-%E4%BF%9D%E6%8A%A4-spring-boot-%E5%BA%94%E7%94%A8%E6%9C%89%E5%93%AA%E4%BA%9B%E6%96%B9%E6%B3%95)

- 在生产中使用 HTTPS
- 使用 Snyk 检查你的依赖关系
- 升级到最新版本
- 启用 CSRF 保护
- 使用内容安全策略防止 XSS 攻击

## 45. Spring 、Spring Boot 和 Spring Cloud 的关系？

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#45-spring-spring-boot-%E5%92%8C-spring-cloud-%E7%9A%84%E5%85%B3%E7%B3%BB)

Spring 最初最核心的两大核心功能 Spring Ioc 和 Spring Aop 成就了 Spring，Spring 在这两大核心的功能上不断的发展，才有了 Spring 事务、Spring Mvc 等一系列伟大的产品，最终成就了 Spring 帝国，到了后期 Spring 几乎可以解决企业开发中的所有问题。

Spring Boot 是在强大的 Spring 帝国生态基础上面发展而来，发明 Spring Boot 不是为了取代 Spring , 是为了让人们更容易的使用 Spring 。

Spring Cloud 是一系列框架的有序集合。它利用 Spring Boot 的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等，都可以用 Spring Boot 的开发风格做到一键启动和部署。

Spring Cloud 是为了解决微服务架构中服务治理而提供的一系列功能的开发框架，并且 Spring Cloud 是完全基于 Spring Boot 而开发，Spring Cloud 利用 Spring Boot 特性整合了开源行业中优秀的组件，整体对外提供了一套在微服务架构中服务治理的解决方案。

用一组不太合理的包含关系来表达它们之间的关系。

Spring ioc/aop > Spring > Spring Boot > Spring Cloud

## 参考

[](https://github.com/cosen1024/Java-Interview/blob/main/Spring/Spring.md#%E5%8F%82%E8%80%83)

[https://juejin.cn/post/6844903860658503693](https://juejin.cn/post/6844903860658503693)

[https://www.cnblogs.com/jingmoxukong/p/9408037.html](https://www.cnblogs.com/jingmoxukong/p/9408037.html)

[http://www.ityouknow.com/springboot/2019/07/24/springboot-interview.html](http://www.ityouknow.com/springboot/2019/07/24/springboot-interview.html)