# Spring5

# Spring5

---
IOC
AOP

```none
-------------------------     -----------------------
|Data Access/Integration|     |         Web         |
|    JDBC    ORM        |     |WebSocket   WebMVC   |
|    OXM     JMS        |     |Web         WebFlux  |
|    Transactions       |     |                     |
-------------------------     -----------------------

    AOP     Aspects     Instrument      Messaging

-----------------------------------------------------
|                   Core Container                  |
|    Beans   Core    Context     Expression         |
-----------------------------------------------------
```

---

## Installation

---
[Spring](https://repo.spring.io/ui/repos/tree/General/release%2Forg%2Fspringframework%2Fspring)

- [Download Spring Framework](https://repo.spring.io/artifactory/release/org/springframework/spring)
- Unzip it
  - libs --> jars
    - jar
    - sources --> source code
    - javadoc --> doc
  - schema --> configs
- For basic functions, we need:
  - Beans
  - Core
  - Context
  - Expression
  - commons-logging (Not in Spring, google it)

---

## Have a Taste: Create Objects

---

### xml

```java
public class User{
    public void add(){
        System.out.println("added...");
    }
}
```

- Create a xml file, for axample, bean.xml
  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--create User object-->
    <bean id="user" class="com.pcewlkr.spring.basics.User"></bean>
</beans>
```

- Write a test to verify if it works

```java
@Test
public void testCreatingObject(){
    // load spring config
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    // get the obj
    User user = context.getBean("user", User.class);

    System.out.println(user);
}
```

- Output:

```none
com.pcewlkr.spring.basics.User@4816278d
```

---

## IOC Container

---

### IOC --> Low Level

- What is IOC?

  - Inverse of Control.
    - Usually, the creation of objects and the calling between them are managed by our codes, but now we hand those over Spring framework.
  - Goal of IOC: low coupling

- IOC Explain

xml parsing, factory mode, and reflection

For example, if we have service class and dao class, then we need to create a factory class.

```pseudocode
First step is the xml config.
<bean id="dao" class="com.me.UserDao"/>

class UserFactory {
    public UserDao getDao() {
        Sting classValue = classAttributes // --> xml parsing
        Class clazz = Class.forName(classValue); // --> create objects using reflection
        return (UserDao) clazz.newInstance();
    }
}

```

### IOC Interface --> BeanFactory

1. IOC is based on IOC container, which is based on the object factory.
2. Spring provides 2 implementations of IOC containers (2 interfaces):
   1. BeanFactory: Fundamental of IOC container, which usually is not directly used by devs.
   2. AplicationContext: Sub interface of BeanFactory, which provides more powerful functions and often used by devs in real life.
3. ApplicationContext: implementation classes
   1. FileSystemXmlApplicationContext --> use absolute path
   2. ClassPathXmlApplicationContext --> use relative path

### IOC Ops Bean Managment --> XML Based

- What is Bean Managment?
  - Spring Object Creation
  - Spring Attribute Injection

- 2 ways of implementations of Bean Managment
  1. Based on xml config
  2. Based on annotations

#### xml config based

&nbsp;

- Object Creation

```xml
<bean id="user" class="com.me.spring.User"/>
```

Add attributes in bean tags. e.g.: id, class.

- Attributes Injection --> DI: Dependency Injection

  - Setter
  - Constructor with params
  - p namespace Injection --> Simplifies xml config

```xml
<!-- Setter -->
<bean id="user" class="com.me.spring.User">
    <property name="username" value="peacewalker"></property>
    <property name="age" value="25"></property>
</bean>

<!-- Constructor with params -->
<bean id="order" class="com.me.spring.basics.Order">
    <constructor-arg name="name" value="Mr. Li"></constructor-arg>
    <constructor-arg name="address" value="NYC"></constructor-arg>
</bean>
```

At he beginning of Spring config xml, there are info about xml namespace.

1. Add p namespace config.
2. Config in bean tag

```xml
<!-- p namespace -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p" // here is the namespace p added
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">


    <bean id="order" class="com.me.spring.basics.Order" p:name="Mr. Li" p:address="NYC"/>
</beans>
```

How to assign null and other special chars?

1. Use escape chars
2. use <![CDATA[content here...]]>

```xml
<property name="address">
    <null/>
</property>
<property name="address" value="&lt;NYC&gt;"/>
<property name="name">
    <value><![CDATA[<NYC>]]></value>
</property>
```

Attribute Injection --> **Outer bean**

1. Create 2 classes: service and dao
2. Invoke dao's methods from service
3. config in Spring config xml

```java
package com.me.spring.basics.dao;

public interface UserDao {
    void update();
}
```

```java
package com.me.spring.basics.dao;

public class UserDaoImpl implements UserDao{
    @Override
    public void update(){
        System.out.println("dao updated...");
    }
}
```

```java
package com.me.spring.basics.service;
import com.me.spring.basics.dao.UserDao;
import com.me.spring.basics.dao.UserDaoImpl;

public class UserService {
    private UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void add(){
        System.out.println("Service added...");

//        UserDao userDao = new UserDaoImpl();
//        userDao.update();
    }
}

```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userService" class="com.me.spring.basics.service.UserService">
<!--        Inject the object using the attribute name in UserService class and the obj bean id -->
        <property name="userDao" ref="userDaoToInject"></property>
    </bean>
    <bean id="userDaoToInject" class="com.me.spring.basics.dao.UserDaoImpl"></bean>

</beans>
```

```java
@Test
    public void test03(){
        ApplicationContext context = new ClassPathXmlApplicationContext("outerBeanInjection.xml");
        UserService userService = context.getBean("userService", UserService.class);
        userService.add();
    }
```

Attribute Injection --> **Inner bean** and **inline assignment**

- 1 to n
  - 1 staff can belong to 1 department
  - 1 department can have n staffs

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="staff" class="com.me.spring.basics.bean.Staff">
        <property name="name" value="Lucy"></property>
        <property name="gender" value="Female"></property>
        <property name="dept">
            <bean id="dept" class="com.me.spring.basics.bean.Dept">
                <property name="name" value="Security Dept"></property>
            </bean>
        </property>
    </bean>
</beans>
```

**Notice**: Inner bean cannot be referenced outside.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="staff" class="com.me.spring.basics.bean.Staff">
        <property name="name" value="Jack"></property>
        <property name="gender" value="male"></property>
        <property name="dept" ref="dept"></property>
        <property name="dept.name" value="Tech Dept"></property> <!-- Only if Staff's attribute dept has a getter! -->
    </bean>
    <bean id="dept" class="com.me.spring.basics.bean.Dept">
        <property name="name" value="Marketing Dept"></property>
    </bean>
</beans>
```

Attribute Injection --> **Inner bean** and **inline assignment**

- Array Injection
- List Injection
- Map Injection
- Set Injection

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="student" class="com.me.spring.basics.collections.Student">
        <property name="courses">
            <array>
                <value>Java</value>
                <value>C++</value>
                <value>Algo</value>
            </array>
        </property>
        <property name="list">
            <list>
                <value>Jessy</value>
                <value>Marry</value>
            </list>
        </property>
        <property name="maps">
            <map>
                <entry key="JAVA" value="java"></entry>
            </map>
        </property>
        <property name="set">
            <set>
                <value>MySQL</value>
                <value>Redis</value>
            </set>
        </property>
    </bean>
</beans>
```

How about a List of objects? What if several objects share the same list?

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="student" class="com.me.spring.basics.collections.Student">
        <property name="list">
            <list>
                <ref bean="course1"></ref>
                <ref bean="course2"></ref>
            </list>
    </bean>
    <bean id="course1" class="com.me.spring.basics.collections.Course">
        <property name="name" value="Spring5"></property>
    </bean>
    <bean id="course2" class="com.me.spring.basics.collections.Course">
        <property name="name" value="SpringBoot2"></property>
    </bean>
</beans>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- added util namespace and schemaLocation-->
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/util  http://www.springframework.org/schema/spring-util.xsd">

    <util:list id="courseList">
        <ref bean="course1"></ref>
        <ref bean="course2"></ref>
    </util:list>
    <bean id="student" class="com.me.spring.basics.collections.Student">
        <property name="list" ref="courseList">
        </property>
    </bean>
    
    <bean id="course1" class="com.me.spring.basics.collections.Course">
        <property name="name" value="Spring5"></property>
    </bean>
    <bean id="course2" class="com.me.spring.basics.collections.Course">
        <property name="name" value="SpringBoot2"></property>
    </bean>
</beans>               
```

### Bean and FactoryBean

There are 2 kinds of bean: bean and FactoryBean.  

FactoryBean --> The return type can be diff from the defined class in xml.

```java
package com.me.spring.basics.factoryBean;

import com.me.spring.basics.collections.Course;
import org.springframework.beans.factory.FactoryBean;

public class MyBean implements FactoryBean<Course> {

    private Course course;

    public Course getCourse() {
        return course;
    }

    public void setCourse(Course course) {
        this.course = course;
    }

    @Override
    public Course getObject() throws Exception {
        return course;
    }

    @Override
    public Class<?> getObjectType() {
        return Course.class;
    }

    @Override
    public boolean isSingleton() {
        return FactoryBean.super.isSingleton();
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="course1" class="com.me.spring.basics.collections.Course">
        <property name="name" value="Java"></property>
    </bean>

    <bean id="myBean" class="com.me.spring.basics.factoryBean.MyBean">
        <property name="course" ref="course1"></property>
    </bean>
</beans>
```

```java
@Test
public void test06(){
    ApplicationContext context = new ClassPathXmlApplicationContext("factoryBean.xml");
    Course myBean = context.getBean("myBean", Course.class);
    System.out.println(myBean);
}
```

### Bean's Scope

1. We can set whether a bean instance is a Singleton(单实例)or Prototype(多实例).
2. By defalt, it's Singleton.
3. `<bean id="someID" class="com.me.spring.SomeClass" scope="Prototype"/>`

Singleton是单例类型，就是在创建起容器时就同时自动创建了一个bean的对象，不管你是否使用，他都存在了，每次获取到的对象都是同一个对象。注意，Singleton作用域是Spring中的缺省作用域。

Prototype是原型类型，它在我们创建容器的时候并没有实例化，而是当我们获取bean的时候才会去创建一个对象，而且我们每次获取到的对象都不是同一个对象。

五种作用域中，request、session和global session三种作用域仅在基于web的应用中使用（不必关心你所采用的是什么web应用框架），只能用在基于web的Spring ApplicationContext环境。

### Bean's Lifetime

1. Create bean instance through non-param constructor
2. Use setters to inject bean attributes and other beans
3. BeanPostProcessor Interface --> postProcessBeforeInitialization()
4. Invoke bean's init method --> need to be configured
5. BeanPostProcessor Interface --> postProcessAfterInitialization()
6. Running
7. Once the container is closed, invoke the Destroy() method --> need to be configured

Bean实例生命周期的执行过程如下：

Spring对bean进行实例化，默认bean是单例；

Spring对bean进行依赖注入；

如果bean实现了BeanNameAware接口，Spring将bean的名称传给setBeanName()方法；

如果bean实现了BeanFactoryAware接口，Spring将调用setBeanFactory()方法，将BeanFactory实例传进来；

如果bean实现了ApplicationContextAware接口，它的setApplicationContext()方法将被调用，将应用上下文的引用传入到bean中；

如果bean实现了BeanPostProcessor接口，它的postProcessBeforeInitialization()方法将被调用；

如果bean中有方法添加了@PostConstruct注解，那么该方法将被调用；

如果bean实现了InitializingBean接口，spring将调用它的afterPropertiesSet()接口方法，类似的如果bean使用了init-method属性声明了初始化方法，该方法也会被调用；

如果在xml文件中通过`<bean>`标签的init-method元素指定了初始化方法，那么该方法将被调用；

如果bean实现了BeanPostProcessor接口，它的postProcessAfterInitialization()接口方法将被调用；

此时bean已经准备就绪，可以被应用程序使用了，他们将一直驻留在应用上下文中，直到该应用上下文被销毁；

如果bean中有方法添加了@PreDestroy注解，那么该方法将被调用；

若bean实现了DisposableBean接口，spring将调用它的distroy()接口方法。同样的，如果bean使用了destroy-method属性声明了销毁方法，则该方法被调用；

>这里特别说明一下Aware接口，Spring的依赖注入最大亮点就是所有的Bean对Spring容器的存在是没有意识的。但是在实际项目中，我们有时不可避免的要用到Spring容器本身提供的资源，这时候要让 Bean主动意识到Spring容器的存在，才能调用Spring所提供的资源，这就是Spring的Aware接口，Aware接口是个标记接口，标记这一类接口是用来“感知”属性的，Aware的众多子接口则是表征了具体要“感知”什么属性。例如BeanNameAware接口用于“感知”自己的名称，ApplicationContextAware接口用于“感知”自己所处的上下文。其实Spring的Aware接口是Spring设计为框架内部使用的，在大多数情况下，我们不需要使用任何Aware接口，除非我们真的需要它们，实现了这些接口会使应用层代码耦合到Spring框架代码中。

```java
package com.me.spring.basics.lifetime;

public class Lifetime {
    private int time;
    private Lifetime lifetime;

    public void setTime(int time) {
        this.time = time;
    }

    public Lifetime(){
        System.out.println("Create bean instance through Lifetime()");
    }

    public Lifetime(int time) {
        this.time = time;
        System.out.println("Setter DI");
    }

    public void initMethod(){
        System.out.println("Execute init method");
    }

    public void destroyMethod(){
        System.out.println("Destroy bean instance");
    }
}
```

```java
package com.me.spring.basics.lifetime;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;

public class LifetimePostProcessor implements BeanPostProcessor {

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("Before init");
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("After init");
        return bean;
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="lifetime" class="com.me.spring.basics.lifetime.Lifetime" scope="singleton" init-method="initMethod" destroy-method="destroyMethod"></bean>

    <!-- All beans will be modified by this post processor! -->
    <bean id="postProcessor" class="com.me.spring.basics.lifetime.LifetimePostProcessor"></bean>
</beans>
```

```java
@Test
public void test07(){
    ApplicationContext context = new ClassPathXmlApplicationContext("lifetime.xml");
    Lifetime lifetime = context.getBean("lifetime", Lifetime.class);
    System.out.println("Using bean...");
    System.out.println(lifetime);
    ((ClassPathXmlApplicationContext)context).close();
}
```

### Autowire

- According to the attribute name or type, Spring will do the matching and injection

```java
package com.me.spring.basics.autowire;

public class Emp {
    private Dept dept;

    @Override
    public String toString() {
        return "Emp{" +
                "dept=" + dept +
                '}';
    }

    public void setDept(Dept dept) {
        this.dept = dept;
    }
}

package com.me.spring.basics.autowire;

public class Dept {

    @Override
    public String toString() {
        return "Dept{}";
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!-- The injected bean's id must be same as the string after 'set' in the setter's method name in the current bean class -->
    <bean id="emp1" class="com.me.spring.basics.autowire.Emp" autowire="byName"></bean>
<!--  Find the type of the attributes in the current bean class, notice there will be error occur if there are more than one bean with same type we needed  -->
    <bean id="emp2" class="com.me.spring.basics.autowire.Emp" autowire="byType"></bean>
    <bean id="dept" class="com.me.spring.basics.autowire.Dept"></bean>
</beans>
```

### IOC Bean --> Outside Config File

1. Directly Configure DB Info
   1. Configure Druid Conection Pool
2. Refer outside config file to configure DB connection pool

- Create `jdbc.properties`

```none
prop.driverClass=com.mysql.jdbc.Driver
prop.url=jdbc:mysql://localhost:3306/userDb
prop.username=root
prop.password=root
```

- Reference through context namespace in Spring config file

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

<!--  Directly configure data connection pool  -->
<!--    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">-->
<!--        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>-->
<!--        <property name="url" value="jdbc:mysql://localhost:3306/userDb"></property>-->
<!--        <property name="username" value="root"></property>-->
<!--        <property name="password" value="root"></property>-->
<!--    </bean>-->


    <context:property-placeholder location="classpath:jdbc.properties"/>

    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${prop.driverClass}"></property>
        <property name="url" value="${prop.url}"></property>
        <property name="username" value="${prop.username}"></property>
        <property name="password" value="${prop.password}"></property>
    </bean>
</beans>
```

### IOC Ops Bean Managment --> Annotation Based

- @Component
- @Service
- @Controller
- @Repository

- How?

1. Import aop jar
2. Turn on component scan
3. Create classes and add annotations
4. Detailed config on component scan

```java
package com.me.spring.basics.annotations.service;

import org.springframework.stereotype.Component;

// you can omit the value, the default value is as same as the class name with a lowercase of the first letter
@Component(value = "userService") // <bean id = "userService" class="..."/>
public class UserService {
    public void add(){
        System.out.println("Service added...");
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

<!--    turn on component scan, to scan multiple packages, use `,` or use higher level path-->
    <context:component-scan base-package="com.me.spring.basics.annotations, com.me.spring.basics.test"></context:component-scan>

<!--    example 1-->
    <context:component-scan base-package="com.me.spring.basics" use-default-filters="false">
<!--        only scan Controller annotation-->
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

<!--    example 2-->
    <context:component-scan base-package="com.me.spring.basics">
<!--        Do not scan Controller annotation-->
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
</beans>
```

### Annotations for Atrributes Injection

- @Autowired --> by type
- @Qualifier --> by name
- @Resource --> type or name
- @Value --> primitives

#### @Autowired

1. add annotations on classes
2. Injection

```java
package com.me.spring.basics.annotations.service;

import com.me.spring.basics.annotations.dao.UserDao;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

// you can omit the value, the default value is as same as the class name with a lowercase of the first letter
@Service // <bean id = "userService" class="..."/>
public class UserService {
    @Autowired // do not need setter since it's encapsulated
    private UserDao userDao;
    public void add(){
        System.out.println("Service added...");
        userDao.add();
    }
}

package com.me.spring.basics.annotations.dao;

import org.springframework.stereotype.Repository;

@Repository
public class UserDaoImpl implements UserDao{
    @Override
    public void add() {
        System.out.println("userDao.add() is called...");
    }
}
```

#### @Qualifier

- Must be used along with @Autowired

```java
@Autowired // do not need setter since it's encapsulated
@Qualifier(value = "userDaoImpl")
...
```

#### @Resource --> not in Spring but javax.annotation.Resource

`@Resource`  --> by type
`@Resource(name ="userDaoImpl")` --> by name

#### @Value --> for the primitives

```java
@Value(value = "TheName")
private String name;
```

### Pure Annotation Dev

1. Create a config class to substitute xml config
2. Test case

```java
@Configuration
@ComponentScan(basePackages = {"com.me.spring.basics"})
```

```xml
ApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
```

## AOP

- Add funcitons without modify the original codes, whether make it in effect is determined by whether AOP scans it

### At Low Level -> Dynamic Proxy

- With an interface --> JDK Dynamic Proxy --> use interface impl as proxy

- Without an interface --> CGLIB Dynamic Proxy --> use subclass implementing Invocationhandler as proxy --> less constrains but less efficiency

### JDK dp

- Proxy.newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h);
  - class loader
  - the interfaces implemented
  - implement InvocationHandler, create proxy object and added functions

```java
package com.me.spring.basics.proxy;

public interface MyDao {
    public int add(int a, int b);

    public String update(String id);
}

package com.me.spring.basics.proxy;

public class MyDaoImpl implements MyDao{
    @Override
    public int add(int a, int b) {
        System.out.println("add() is executed...");
        return a+b;
    }

    @Override
    public String update(String id) {
        System.out.println("update() is executed...");
        return id;
    }
}

package com.me.spring.basics.proxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.Arrays;

public class JDKProxy {
    public static void main(String[] args) {
        MyDaoImpl myDaoImpl = new MyDaoImpl();
        MyDao myDao = (MyDao)Proxy.newProxyInstance(MyDaoImpl.class.getClassLoader(), MyDaoImpl.class.getInterfaces(), new MyDaoProxy(myDaoImpl));
        System.out.println(myDao.add(1,5));
    }
}

class MyDaoProxy implements InvocationHandler{

    private Object obj;

    public MyDaoProxy(){}
    public MyDaoProxy(Object obj){
        this.obj = obj;
    }

    public void bind(Object obj){
        this.obj = obj;
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("Before the function..." + method.getName() + Arrays.toString(args));
        Object res = method.invoke(obj, args);
        System.out.println("After the function..." + res);
        return res;
    }
}
```

### CHLIB dp

### AOP Terms

- **Joinpoint**: Insertion points (methods) of AOP. A joinpoint is a point in the execution of the application where an aspect can be plugged in.
- **Pointcut**: The specific methods being adviced. Pointcut is an AOP terminology for all the points in the execution of your code where you want this advice method to cut in.
- **Advice**: The action taken in joinpoint, the job of an aspect is called Advice.It defines both when and what of the aspect. Spring aspects can work with five kinds of advice
  - @Before
  - @After
  - @Around
  - @AfterThrowing
  - @AfterReturning
- **Advisor**: pointcut + advice
- **Aspect**: pointcut + advice, but we don't need to implement the advice interfaces here. The modularization of concern is known as Aspect.In spring,Aspect is a class with special priviliges and are annotaed with @Aspect to represent Aspects.
- **Weaving**: Weaving is the process of applying aspects to a target object to create a new proxied object.
- **Proxy**: obj created after the target being adviced

### AOP ops

1. AspectJ: not a part of Spring but an independent AOP framework
   1. xml based
   2. annotation based
2. Import dependencies: aop, aspectJ.weaver, aopalliance, cglib

#### pointcut expression

`execution([access modifier] [return type] [path of class] [method name] ([args]))`

```java
// example1 --> com.me.dao.BookDao add()
execution(* com.me.dao.BookDao.add(..))
// access modifier can be omit
// it must include the return type, * indicates any types

// example2 --> all methods
execution(* com.me.dao.BookDao.*(..))

// example3 --> all methods in all classes
execution(* com.me.dao.*(..))
```

#### AspectJ (annotation)

1. create a class and methods
2. create the advice class with advice logic in it
3. advice config
   1. turn on annotation scan
   2. create instances of proxy and proxyee using annotation
   3. add @Aspect annotation on proxy
   4. config Spring config to turn on proxy obj generation
4. In the proxy class, add advice annotation and pointcut expressions

@Around > @Before > @After > @AfterReturning > @AfterThrowing

If there is exceptions, @AfterReturning will not run.
@After will execute regardless whether exception occurs

```java
package com.me.spring.basics.aopanno;

import org.springframework.stereotype.Component;

@Component
public class User {
    public void add(){
        System.out.println("add...");
    }
}

package com.me.spring.basics.aopanno;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

@Component
@Aspect
public class UserProxy {

    @Around(value = "execution(* com.me.spring.basics.aopanno.User.add(..))")
    public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("around-before...");
        proceedingJoinPoint.proceed();
        System.out.println("around-after...");
    }

    @Before(value = "execution(* com.me.spring.basics.aopanno.User.add(..))")
    public void before(){
        System.out.println("before...");
    }

    @After(value = "execution(* com.me.spring.basics.aopanno.User.add(..))")
    public void after(){
        System.out.println("after...");
    }

    @AfterThrowing(value = "execution(* com.me.spring.basics.aopanno.User.add(..))")
    public void afterThrowing(){
        System.out.println("afterThrowing...");
    }

    @AfterReturning(value = "execution(* com.me.spring.basics.aopanno.User.add(..))")
    public void afterReturning(){
        System.out.println("afterReturning...");
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    <context:component-scan base-package="com.me.spring.basics.aopanno"></context:component-scan>
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>

```

**Notice**:

- pointcut can be extracted
- multiple advice on one method can be set to diff priorities

```java
package com.me.spring.basics.aopanno;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

@Component
@Aspect
@Order(1)
public class UserProxy {

    @Pointcut(value = "execution(* com.me.spring.basics.aopanno.User.add(..))")
    public void pointcut(){}

    @Around(value = "pointcut()")
    public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("around-before...");
        proceedingJoinPoint.proceed();
        System.out.println("around-after...");
    }

    @Before(value = "pointcut()")
    public void before(){
        System.out.println("before...");
    }

    @After(value = "pointcut()")
    public void after(){
        System.out.println("after...");
    }

    @AfterThrowing(value = "pointcut()")
    public void afterThrowing(){
        System.out.println("afterThrowing...");
    }

    @AfterReturning(value = "pointcut()")
    public void afterReturning(){
        System.out.println("afterReturning...");
    }
}

```

- If you want annotations do everything!

```java
package com.me.spring.basics.aopanno;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@Configuration
@ComponentScan(basePackages = {"com.me.spring.basics.aopanno"})
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class Config {}
```

#### AspectJ (xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    
    <bean id="user" class="com.me.spring.basics.aopanno.User"></bean>
    <bean id="userProxy" class="com.me.spring.basics.aopanno.UserProxy"></bean>

    <aop:config>
        <aop:pointcut id="p" expression="execution(* com.me.spring.basics.aopanno.User.add())"/>
        <aop:aspect ref="userProxy">
            <aop:before method="before" pointcut-ref="p"></aop:before>
        </aop:aspect>
    </aop:config>
</beans>
```

## JdbcTemplate

- mysql-connector-java

## Transaction

### ACID

- Atomicity: 事务作为一个整体被执行，包含在其中的对数据库的操作要么全部被执行，要么都不执行.
- Consistency: 事务应确保数据库的状态从一个一致状态转变为另一个一致状态。一致状态的含义是数据库中的数据应满足完整性约束.
- Isolation: 多个事务并发执行时，一个事务的执行不应影响其他事务的执行. [Probs](http://blog.sina.com.cn/s/blog_499740cb0100ugs7.html)
  - READ UNCOMMITTED
  - READ COMMITTED
  - REPEATABLE READ
  - SERIALIZABLE
- Durability: 已被提交的事务对数据库的修改应该永久保存在数据库中.

### Transaction Manager

- Transactions are usually added into Service level of JavaEE
- There are 2 ways of transaction managment in Spring
  - programming(use try-catch-finally to code it)
  - declaration --> AOP at low level
    - xml
    - annotation
- Spring Transaction Manager API
  - PlatformTransactionManager --> Interface
    - for mybatis and jdbc --> DataSourceTransactionManager

#### Declaration: Annotation

1. Create Transaction Manager in config
2. Turn on transaction annotation
   1. add tx namespace
3. In the service class, add transaction annotation on the corresponding methods (or the whole class) `@transactional`
   1. Notice if put it on the class, it's on every method in it

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
                           http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">
<!--turn on component scan-->
    <context:component-scan base-package="com.me.spring.transaction"></context:component-scan>

<!--  db connection pool  -->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
<!--  @deprecated  ->      <property name="driverClassName" value="${prop.driverClass}"></property>-->
        <property name="url" value="${prop.url}"></property>
        <property name="username" value="${prop.username}"></property>
        <property name="password" value="${prop.password}"></property>
    </bean>

<!--  JdbcTemplate object  -->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
<!--        Inject dataSource-->
        <property name="dataSource" ref="dataSource"></property>
    </bean>

<!--    Create transaction manager-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
<!--        Inject data source-->
        <property name="dataSource" ref="dataSource"></property>
    </bean>
</beans>
```

```java
@Service
public class UserService {

    @Autowired
    private UserDao userDao;

    @Transactional
    public void accountMoney(double amount, String sender, String receiver){
        userDao.reduceMoney(amount, sender);
        userDao.addMoney(amount, receiver);
    }
}

public interface UserDao {

    public void addMoney(double money, String name);
    public void reduceMoney(double money, String name);
}

@Repository
public class UserDaoImpl implements UserDao{

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Override
    public void addMoney(double amount, String name) {
        String sql = "update t_account set money=money-? where username=?";
        jdbcTemplate.update(sql, amount, name);
    }

    @Override
    public void reduceMoney(double amount, String name) {
        String sql = "update t_account set money=money+? where username=?";
        jdbcTemplate.update(sql, amount, name);
    }
}

@Test
public void test01(){
    ApplicationContext context = new ClassPathXmlApplicationContext("druid.xml");
    UserService userService = context.getBean("userService", UserService.class);
    userService.accountMoney(100, "Lucy", "Marry");
}
```

#### [@Transaction(...)](https://juejin.cn/post/6844903600943022088)

- propagation --> 事务传播行为 (REQUIRED by default)
  - 多<事务方法>之间进行调用，这个过程中事务是如何进行管理的
  - REQUIRED --> 表示当前方法必须在一个具有事务的上下文中运行，如有客户端有事务在进行，那么被调用端将在该事务中运行，否则的话重新开启一个事务。(如果被调用端发生异常，那么调用端和被调用端事务都将回滚)
  - REQUIRED_NEW --> 表示当前方法必须运行在它自己的事务中。一个新的事务将启动，而且如果有一个现有的事务在运行的话，则这个方法将在运行期被挂起，直到新的事务提交或者回滚才恢复执行
  - SUPPORTS --> 表示当前方法不必需要具有一个事务上下文，但是如果有一个事务的话，它也可以在这个事务中运行
  - NOT_SUPPORTED --> 表示该方法不应该在一个事务中运行。如果有一个事务正在运行，他将在运行期被挂起，直到这个事务提交或者回滚才恢复执行
  - MANDATORY --> 表示当前方法必须在一个事务中运行，如果没有事务，将抛出异常
  - NEVER --> 表示当方法务不应该在一个事务中运行，如果存在一个事务，则抛出异常
  - NESTED --> 表示如果当前方法正有一个事务在运行中，则该方法应该运行在一个嵌套事务中，被嵌套的事务可以独立于被封装的事务中进行提交或者回滚。如果封装事务存在，并且外层事务抛出异常回滚，那么内层事务必须回滚，反之，内层事务并不影响外层事务。如果封装事务不存在，则同PROPAGATION_REQUIRED的一样
- isolation --> 事务隔离级别 (REPEATABLE READ by default)
  - Probs:
    - Dirty Reads --> A dirty read occurs when a transaction reads data that has not yet been committed. For example, suppose transaction 1 updates a row. Transaction 2 reads the updated row before transaction 1 commits the update. If transaction 1 rolls back the change, transaction 2 will have read data that is considered never to have existed.
    - Nonrepeatable Reads --> A nonrepeatable read occurs when a transaction reads the same row twice but gets different data each time. For example, suppose transaction 1 reads a row. Transaction 2 updates or deletes that row and commits the update or delete. If transaction 1 rereads the row, it retrieves different row values or discovers that the row has been deleted.
    - Phantoms --> A phantom is a row that matches the search criteria but is not initially seen. For example, suppose transaction 1 reads a set of rows that satisfy some search criteria. Transaction 2 generates a new row (through either an update or an insert) that matches the search criteria for transaction 1. If transaction 1 reexecutes the statement that reads the rows, it gets a different set of rows.
  - Solution:
    - READ UNCOMMITTED --> solve none
    - READ COMMITTED --> solve dirty reads
    - REPEATABLE READ --> solve dirty reads and Nonrepeatable reads
    - SERIALIZABLE --> solve all of them
- timeout --> 超时时间
  - 事务必须在一定的时间内提交，否则进入回滚判断
  - 默认为-1 --> 无超时限制
- readOnly --> 是否只读
  - false by default
  - if set to true, only allow query
- rollbackFor --> 回滚
  - set the exceptions that will trigger rollback
- noRollbackFor --> 不回滚
  - set the exceptions that will not trigger rollback

```java
@Transactional(propagation = Propagation.REQUIRED, isolation = Isolation.REPEATABLE_READ, readOnly = false, timeout = -1)

```

#### Declaration: XML

- Configure transaction manager
- Configure advice
- Configure pointcut and aspect

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
                           http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">
<!--turn on component scan-->
    <context:component-scan base-package="com.me.spring.transaction"></context:component-scan>

<!--  db connection pool  -->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
<!--  @deprecated  ->      <property name="driverClassName" value="${prop.driverClass}"></property>-->
        <property name="url" value="${prop.url}"></property>
        <property name="username" value="${prop.username}"></property>
        <property name="password" value="${prop.password}"></property>
    </bean>

<!--  JdbcTemplate object  -->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
<!--        Inject dataSource-->
        <property name="dataSource" ref="dataSource"></property>
    </bean>

<!--    Create transaction manager-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
<!--        Inject data source-->
        <property name="dataSource" ref="dataSource"></property>
    </bean>

<!--    configure advice-->
    <tx:advice id="txAdvice">
        <tx:attributes>
<!--            set the rule to locate the methods-->
            <tx:method name="accountMoney" propagation="REQUIRED"/>
<!--            <tx:method name="account*"></tx:method>-->
        </tx:attributes>
    </tx:advice>

<!--    set advisor and pointcut-->
    <aop:config>
<!--        pointcut-->
        <aop:pointcut id="pt" expression="execution(* com.me.spring.transaction.service.UserService.accountMoney())"/>
<!--        advisor-->
        <aop:advisor advice-ref="txAdvice" pointcut-ref="pt"></aop:advisor>
    </aop:config>
</beans>
```

#### Declaration: Pure Annotation

```java
package com.me.spring.transaction.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;

import javax.sql.DataSource;

@Configuration
@ComponentScan(basePackages = {"com.me.spring.transaction"})
@EnableTransactionManagement // turn on Transaction Manager
public class Config {

    // Create db connection pool
    @Bean
    public DruidDataSource getDruidDataSource(){
        DruidDataSource dataSource = new DruidDataSource();
//        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/userDb");
        dataSource.setUsername("root");
        dataSource.setPassword("xxxxxxxxx");
        return dataSource;
    }

    // Create JdbcTemplate
    @Bean
    public JdbcTemplate getJdbcTemplate(DataSource dataSource){ // Find the object in the IOC container by type
        JdbcTemplate jdbcTemplate = new JdbcTemplate();

        // inject dataSource
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }

    // Create transaction Manager
    @Bean
    public DataSourceTransactionManager getDataSourceTransactionManager(DataSource dataSource){
        DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
        transactionManager.setDataSource(dataSource);
        return transactionManager;
    }
}
```

```java
@Test
public void test02(){
    ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
    UserService userService = context.getBean("userService", UserService.class);
    userService.accountMoney(100, "Lucy", "Marry");
}
```

## Logging framework

### Log4j2

- log4j-api
- log4j-core
- log4j-slf4j-impl
- log4j-api

#### Create `log4j2.xml`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!-- Logging level: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->
<!-- The status in Configuration tag is to set the internal info output, it can be omitted. If set to trace, you can see all kinds of detailed output -->
<configuration status="INFO">
<!--    firstly define all appender -->
    <appenders>
<!--        output logging info to console-->
        <console name="Console" target="SYSTEM_OUT">
<!--            the format of log-->
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </console>
    </appenders>
<!--    define logger, only if the logger is defined and import the appender, the appender takes effect -->
    <loggers>
        <root level="info">
            <appender-ref ref="Console"/>
        </root>
    </loggers>
</configuration>
```

#### Manually log

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class UserLog{
    private static final Logger log = LoggerFactory.getLogger(UserLog.class);

    public static void main(String[] args) {
        log.info("hello, log4j2");
        log.warn("hello, log4j2");
    }
}
```

## @Nullable

1. Can be used on method, attributes, params
2. method --> meaning return val can be null
3. param --> can be null
   1. `method(@Nullable String name)`
4. attributes --> can be null

## GenericApplicationContext/AnnotationConfigApplicationContext

Creating objects using Functional Programming is supported

```java
@Test
    public void test03(){
        // create GenericApplicationContext object
        GenericApplicationContext context = new GenericApplicationContext();
        // call refresh() to clean it up and register bean
        context.refresh(); // just call this once
        context.registerBean(User.class, ()->new User());
        // get the registered bean in spring
        User user = (User)context.getBean("com.me.spring.basics.User");
        System.out.println(user);

        // you can also name it
        context.registerBean("namedUser", User.class, ()->new User());
        User namedUser = (User)context.getBean("namedUser");
        System.out.println(namedUser);
    }
```

## JUnit

### JUnit4

- spring-test
- JUnit4

```java
package com.me.spring.tests;

import com.me.spring.transaction.service.UserService;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class) // JUnit version
@ContextConfiguration({"classpath:druid.xml"}) // load config file
public class JTest4{
    @Autowired
    private UserService userService;

    @Test
    public void test01(){
        userService.accountMoney(20, "Lucy", "Marry");
    }
}
```

### JUnit5

- JUnit5

```java
package com.me.spring.tests;

import com.me.spring.transaction.service.UserService;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.junit.jupiter.SpringJUnitConfig;

//@ExtendWith(SpringExtension.class)
//@ContextConfiguration("classpath:druid.xml")
@SpringJUnitConfig(locations = "classpath:druid.xml")
public class JUnit5 {

    @Autowired
    private UserService userService;

    @Test
    public void test01(){
        userService.accountMoney(20, "Lucy", "Marry");
    }
}
```

## [前置没学完 稍后继续](https://www.bilibili.com/video/BV1Vf4y127N5?p=53)


## Webflux



### Reactive Programming



### Process and Core APIs



### SpringWebflux: Annotation Programming



### SpringWebflux: Functional Programming



