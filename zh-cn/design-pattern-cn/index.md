# 设计模式


# 设计模式

[Watch the Video](https://www.bilibili.com/video/BV19g411N7yx?p=2&spm_id_from=pageDriver)

[Watch the Video2](https://www.bilibili.com/video/BV1mc411h719?spm_id_from=333.999.0.0)

### 设计模式：

- 模式名称
- 问题，或应用范围
- 解决方案
- 效果及其权衡（优缺点）



#### 创建型模式：

- 单例模式， 工厂模式，抽象工厂模式，建造者模式，原型模式

#### 结构型模式：

- 适配器模式，桥接模式，装饰模式，组合模式，外观模式， 享元模式，代理模式

#### 行为型模式：

- 模板方法模式，命令模式，迭代器模式，观察者模式，中介者模式，备忘录模式，解释器模式，状态模式，策略模式，职责链模式，访问者模式



### OOP七大原则：

1. **开闭原则**： 对扩展开放，对修改关闭
2. 里氏替换原则： 继承必须确保超类所拥有的性质在子类中仍然成立。（”正方形不是长方形”）
3. 依赖倒置原则： 要面向接口编程，不要面向实现编程。（高层的模块不应该依赖低层的模块，依赖抽象，不依赖细节）
4. 单一职责原则： 控制类的粒度大小，将对象解耦，提高其内聚性。（一个对象不应该承担过多的职责，一个方法最好做好一件事，原子性。）
5. 接口隔离原则： 要为各个类建立它们需要的专用接口。
6. 迪米特法则： 只与你的朋友交谈，不跟陌生人交谈。（A-->B-->C，A与C不要通信，以此降低类之间的耦合性，提高模块的独立性，但是弊端是会产生中间件和提高系统复杂性，要权衡。）
7. 合成复用原则： 尽量先使用组合或者聚合等关联关系来实现，其次才考虑使用继承关系来实现。（区分has a和is a的关系，首先用has a的关系来解决，如果要使用继承的关系，就要确保原则2。原则2和7实际是原则1的具体规范。）



## 单例模式-Singleton

### 核心作用
保证一个类只有一个实例，并且提供一个访问该实例的全局访问点。

### 常见场景
- Windows的任务管理器
- Windows回收站
- 项目中，读取配置文件的类，一般也只有一个对象，没必要每次都去new对象读取
- 网站的计数器一般也会采用单例模式，可以保证同步
- 数据库连接池的设计一般也是单例模式
- 在Servlet编程中，每个Servlet也是单例的
- 在Spring中，每个Bean默认的就是单例

### 八种写法

先把构造方法设为私有。
`private ClassName();`
1. 饿汉式
类加载到内存后，就实例化一个单例，JVM保证线程安全。
简单实用，推荐。
唯一缺点： 不管你用到与否，类装载时就完成了实例化。
`Class.forName("name")` 可以懒加载，但是一旦加载，实例化就完成了。

```java
public static final ClassName INSTANCE = new ClassName();
public static ClassName getInstance() {return INSTANCE;}
```

2. 懒汉式
达到了按需初始化的目的， 但是线程不安全。
```java
private static ClassName INSTANCE;

public static ClassName getInstance() {
    if (INSTANCE == null) {
        INSTANCE = new ClassName();
    }
}
```
当一个线程判断INSTANCE为空，但还没有创建实例的时候，另一个线程进入，也判断INSTANCE为空，并且创建了实例...

可以通过加锁来解决，但是效率会下降。

带有双重检查的懒汉式曾经被认为是最完美的方案。然而实际应用中，并不一定需要。

```java
public static ClassName getInstance() {
    if (INSTANCE == null) {
        synchronized (ClassName.class) {
            try {
                ...;
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                INSTANCE = new ClassName;
        }
    }
    return INSTANCE;
}
```

3. 静态内部类

JVM保证单例, JVM加载类的时候只加载一次。
加载外部类时不会加载内部类，这样可以实现懒加载。
这是另一种完美解法。
```java
private static ClassNameHolder {
    private final static ClassName INSTANCE = new ClassName();
}

public static ClassName getInstance() {
    return ClassNameHolder.INSTANCE;
}
```

4. Java 创始人在*Effective Java*中写的一种单例模式

不仅可以解决线程同步，还可以防止反序列化。


```java
public enum ClassName {
    INSTANCE;
    
    public void methods() {}

    public static void main(String[] args) {
        for (int i = 0; i < 100; i++) {
            new Thread(()->{
                System.out.println(ClassName.INSTANCE.hashCode());
            }).start();
        }
    }
}
```

#### 为什么单例模式要防止反序列化？

Java的反射可以通过class文件，把class load到内存，并且new一个实例。
所以通过反序列化的方式，Java可以利用反射的特性，来制造实例。
在其他的写法中，阻止反序列化非常的繁琐。

枚举单例不会被反序列化的原因是：
- 枚举类没有构造方法！
- 所以你就算拿到它的class也没法构造它的对象。

注：
枚举：通过反射破解枚举发现不成功：
1、普通的反编译会欺骗开发者，说enum枚举是无参构造
2、实际enum为有参构造（见后面）；
3、通过反射破解枚举会发现抛出异常
Exception in thread "main" java.lang.IllegalArgumentException: Cannot reflectively create enum objects at java.lang.reflect.Constructor.newInstance(Constructor.java:417) at com.ph.single.Test.main(EnumSingle.java:19)

[讲解反射破坏](https://www.bilibili.com/video/BV1K54y197iS/?spm_id_from=333.788.recommend_more_video.1) around 11:00

```java
public class LazyMan {
    private LazyMan() {}
    private volatile static LazyMan lazyMan;
}

public static void main(String[] args) throws Exception {
    LazyMan instance = LazyMan.getInstance();
    Constructor<LazyMan> declaredConstructor = LazyMan.class.getDeclaredConstructor(null);
    declaredConstructor.setAccessible(true);
    LazyMan instance2 = declaredConstructor.newInstance(); // 无视私有构造器
    
    System.out.println(instance);
    System.out.println(instance2);
}
```



```java
public class LazyMan {
    private LazyMan() {
        synchronized (LazyMan.class) {
            if (lazyMan != null) {
                throw new RuntimeException("不要试图使用反射破坏异常");
            }
        }
    }
    
    private volatile static LazyMan lazyMan;
}

public static void main(String[] args) throws Exception {
    LazyMan instance = LazyMan.getInstance(); // 由于此处使lazyMan不为空，上面防御手段生效
    Constructor<LazyMan> declaredConstructor = LazyMan.class.getDeclaredConstructor(null);
    declaredConstructor.setAccessible(true);
    LazyMan instance2 = declaredConstructor.newInstance(); // 无视私有构造器
    
    System.out.println(instance);
    System.out.println(instance2);
}
```



```java
public class LazyMan {
    private LazyMan() {
        synchronized (LazyMan.class) {
            if (lazyMan != null) {
                throw new RuntimeException("不要试图使用反射破坏异常");
            }
        }
    }
    
    private volatile static LazyMan lazyMan;
}

public static void main(String[] args) throws Exception {
    Constructor<LazyMan> declaredConstructor = LazyMan.class.getDeclaredConstructor(null);
    declaredConstructor.setAccessible(true);
    LazyMan instance = declaredConstructor.newInstance(); // 无视私有构造器
    LazyMan instance2 = declaredConstructor.newInstance(); // 现在我们没有通过getInstance()去出发懒汉式，new出来的对象的指针不会交给LazyMan
    
    System.out.println(instance);
    System.out.println(instance2);
}
```



```java
public class LazyMan {
    
    private static boolean someKey = false; // 设置标志位
    
    private LazyMan() {
        synchronized (LazyMan.class) {
            if (someKey == false) { someKey = true;} //当第一次构造之后，被标记
            else {
                throw new RuntimeException("不要试图使用反射破坏异常");
            }
        }
    }
    
    private volatile static LazyMan lazyMan;
}

public static void main(String[] args) throws Exception {
    Constructor<LazyMan> declaredConstructor = LazyMan.class.getDeclaredConstructor(null);
    declaredConstructor.setAccessible(true);
    LazyMan instance = declaredConstructor.newInstance(); // 无视私有构造器
    LazyMan instance2 = declaredConstructor.newInstance();
    
    System.out.println(instance);
    System.out.println(instance2);
    // 现在我们是安全的，因为除非通过反编译手段，反射是无法获得标志位的关键字的。
}
```

如果将标志位进一步加密，则安全性会大大上升。

但是，再复杂的加密也会有人解密。

```java
public class LazyMan {
    
    private static boolean someKey = false; // 设置标志位
    
    private LazyMan() {
        synchronized (LazyMan.class) {
            if (someKey == false) { someKey = true;} //当第一次构造之后，被标记
            else {
                throw new RuntimeException("不要试图使用反射破坏异常");
            }
        }
    }
    
    private volatile static LazyMan lazyMan;
}

public static void main(String[] args) throws Exception {
    
    LazyMan.class.getDeclaredField(name: "someKey"); // 破解后我们可以通过反射获得字段
    someKey.setAccessible(true);
    
    Constructor<LazyMan> declaredConstructor = LazyMan.class.getDeclaredConstructor(null);
    declaredConstructor.setAccessible(true);
    LazyMan instance = declaredConstructor.newInstance(); // 无视私有构造器
    
    someKey.set(instance, false); // 重置字段。导致下面实例成功创建
    
    LazyMan instance2 = declaredConstructor.newInstance();
    
    System.out.println(instance);
    System.out.println(instance2);
}
```

这种无止境的攻防会将注重安全性的单例模式代码的复杂性不断拉升。

那么如何找到solution？



通过分析源码：

```java
// source code
if ((clazz.getModifiers() & Modifier.ENUM) != 0))
    throw new IllegalArgumentException("Cannot reflectively create enum objects");
```

enum 本质也是一个类

```java
public enum EnumSingle {
    
    INSTANCE;
    
    public EnumSingle getInstance() {
        return INSTANCE;
    }
}

class Test {
    public static void main(String[] args) {
        EnumSingle instance1 = EnumSingle.INSTANCE;
        
        EnumSingle.class.getDeclaredConstructor(null);
        declaredConstructor.setAccessible(true);
        EnumSingle instance2 = declaredConstructor.newInstance();
        
        System.out.println(instance);
    	System.out.println(instance2);
    }
}
// 没有成功，抛出异常显示不存在空参构造器。Hmm？
```

利用反编译分析源码：

`javap =p EnumSingle.class`

发现仍然显示有空参构造器，至此发现官方的工具都没有透露实情。



利用`jad.exe`工具：

`jad -sjava EnumSingle.class`

发现实际上Enum的构造器有参数： String and int



```java
public enum EnumSingle {
    
    INSTANCE;
    
    public EnumSingle getInstance() {
        return INSTANCE;
    }
}

class Test {
    public static void main(String[] args) {
        EnumSingle instance1 = EnumSingle.INSTANCE;
        
        EnumSingle.class.getDeclaredConstructor(String.class, int.class); //添加参数
        declaredConstructor.setAccessible(true);
        EnumSingle instance2 = declaredConstructor.newInstance();
        
        System.out.println(instance);
    	System.out.println(instance2);
    }
}
// 没有成功，至此证明Enum确实能保证单例模式的安全性
```

枚举是线程安全的，反编译后看源码可知，枚举类的对象在静态代码块被创建。

也就是在类加载的时候执行。

JVM的类加载是线程安全的，再加上不允许反射创建对象，所以是完美方法。但是有可能比较占用内存。


## 工厂模式

### 作用

- 实现了创建者和调用者的分离
- 详细分类：
    - 简单工厂模式
    - 工厂方法模式
    - 抽象工厂模式


- OOP七大原则
    - 开闭原则
    - 依赖倒转原则
    - 迪米特法则


### 核心本质

- 实例化对象不能使用new，用工厂方法代替
- 将选择实现类，创建对象统一管理和控制。从而将调用者跟我们的实现类解耦

### 三种模式



```java
package com.me.factory.simple;

public class Camaro implements Car {
    public void name() {
        System.out.println("Camaro");
    }
}

public class Tesla implements Car {
    public void name() {
        System.out.println("Tesla");
    }
}

public class Consumer {
    public static void main(String[] args) {
        Car car = new Camaro();
        Car car2 = new Tesla();
    }
}
```





- 简单工厂模式
    - 用来生产同一等级结构中的任意产品（对于增加新的产品，需要覆盖已有代码）

```java
// 方法1
package com.me.factory.simple;

public class CarFactory {
    public static Car getCar(String car) {
        if (car.equals("Camaro")) {
            return new Camaro();
        } else if (car.equals("Tesla")) {
            return new Tesla();
        } else {
            return null;
        }
    }
}

public class Consumer {
    public static void main(String[] args) {
        Car car = CarFactory.getCar("Camaro");
        Car car2 = CarFactory.getCar("Tesla");
    }
}

// 这里的问题是： 如果我想加入新车，那么就要修改getCar()代码，违反了开闭原则！
```



```java
// 方法2
public static getCamaro() {...}
public static getTesla() {...}

// 这里有相似的问题，仍然需要修改原有的class内容去给工厂添加新的methods
```



```java
//（简单）静态工厂模式 的弊端就是不修改代码的情况下是做不到增加新产品的
```



- 工厂方法模式

    - 用来生产同一等级结构中的固定产品（支持增加任意产品）

    - 在简单工厂的基础上，添加了中间层。



```java
//工厂方法模式
public interface CarFactory {
    Car getCar();
}

public class TeslaFactory implements CarFactory {
    @override
    public Car getCar() {
        return new Tesla(); 
    }
}

public class CamaroFactory implements CarFactory {
    @override
    public Car getCar() {
        return new Camaro(); 
    }
}

public class Consumer {
    public static void main(String[] args) {
        Car car = new CamaroFactory().getCar("Camaro");
        Car car2 = new TeslaFactory().getCar("Tesla");
    }
}
```



结构复杂度： 简单工厂模式好

代码复杂度： 简单工厂模式好

编程复杂度： 简单工厂模式好

管理复杂度： 简单工厂模式好



根据设计原则： 工厂方法模式

根据实际业务： 更多的是简单工厂模式



- 抽象工厂模式
    - 围绕一个超级工厂创建其他工厂，即其他工厂的工厂。



##  抽象工厂模式

### 定义
抽象工厂模式提供了一个创建一系列相关或者相互依赖对象的接口，无需指定它们具体的类。

### 适用场景
- 客户端（应用层）不依赖于产品类实例如何被创建、实现等细节。
- 强调一系列相关的产品对象（属于同一产品族）一起使用创建对象需要大量的重复代码
- 提供一个产品类的库，所有产品以相同的接口出现，从而使得客户端不依赖于具体的实现

### 优点
- 具体产品在应用层的代码隔离，无需关心创建的细节
- 将一个系列的产品统一到一起创建

### 缺点
- 规定了所有可能被创建的产品合集，产品簇中扩展新的产品困难
- 增加了系统的抽象性和理解难度

抽象工厂在二维上创建： 产品族和产品等级
iPhone和google Pixel是同一个产品等级，iPhone和iPad是同一个产品族。

```java
//手机产品接口
public interface PhoneProduct {
    void start();
    void shutdown();
    void call();
    void sendSMS();
}
```



```java
//路由器产品接口
public interface RouterProduct {
    void start();
    void shutdown();
    void openWifi();
    void setting();
}
```



```java
//iPhone
public class iPhone implements PhoneProduct {
    @override
    public void start() {
        System.out.println("iPhone is booting up...");
    }
    ...
}
```



```java
//VerizonRouter
public class VerizonRouter implements RouterProduct {
    @override
    public void start() {
        System.out.println("VerizonRouter is booting up...");
    }
    ...
}
```



```java
// 抽象产品工厂
public class ProductFactory {
    // 生产手机
    PhoneProduct PhoneProduct();
    
    // 生产路由器
    RouterProduct RouterProduct();
    
}
```



```java
// 苹果工厂
public class AppleFactory implements ProductFactory {
    @override
    public PhoneProduct PhoneProduct() {
        return new iPhone();
    }
    
    @override
    public RouterProduct RouterProduct() {
        return new iRouter();
    }
}
```



```java
// 生产实例
public class Client{
    public static void main(String[] args) {
    	AppleFactory appleFactory = new AppleFactory();
    	PhoneProduct phone = appleFactory.PhoneProduct();
    	phone.start();
	}
}

```

如果我们要家一个笔记本产品族，那么我们只需要在抽象产品工厂里添加这个品类。具体实现由各个品牌工厂负责。



### 工厂模式小结（包括抽象工厂模式）

- 简单工厂模式（静态工厂模式）虽然不符合设计原则，但是实际应用最多。
- 工厂方法模式 可以在不修改已有的class的前提下，通过增加新的工厂类实现扩展。
- 抽象工厂模式 不可以增加产品，但是可以增加产品族。



#### 应用场景

- JDK中Calendar的getInstance方法
- JDBC中的Connection对象的获取
- Spring中IOC容器创建管理bean对象
- 反射中Class对象的newInstance方法



## 建造者模式



建造者模式属于创建型模式，他提供了一种创建对象的最佳方式。



### 定义

将一个复杂对象的构建与他的表示分离，使得同样的构建过程可以创建不同的表示。



### 主要作用

- 在用户不知道对象的建造过程和细节的情况下就可以直接创建复杂的对象。

- 用户只需要给定复杂对象的类型和内容，建造者模式负责按顺序创建复杂对象（隐藏建造过程和细节）



### 例子

- 工厂（建造者模式）： 负责制造汽车（组装过程和细节在工厂内）
- 汽车购买者（用户）： 只要说出需要的型号和配置，不需要了解制造组装过程



```java
// 抽象的建造者
public abstract class Builder {
    abstract void buildA();
    abstract void buildB();
    abstract void buildC();
    abstract void buildD();
    
    abstract Product getProduct();
}
```



```java
// 产品： 房子
public class Product {
    private String A;
    private String B;
    private String C;
    private String D;
    
    public String getA() {return A}
    public String setA(String a) {A = a;}
}
```



```java
// 具体的建造者： 工人
public class Worker extends Builder {
    
    private Product product;
    
    public Worker() {
        product = new Product(); //这一步很重要，是工人创建产品。
    }
    
    @Override
    void A() {
        product.setA("地基种类1");
        System.out.println("地基建造完毕");
    }
    ...
}
```



```java
// 指挥： 核心。 负责指挥构建一个工程，工程如何构建，由此决定。
public class Director {
    // 指挥工人建造房子
    public Product build(Builder builder) {
        builder.buildA();
        builder.buildB();
        builder.buildC();
        builder.buildD();
        
        return builder.getProduct(); 
    }
}
```



```java
public class Test {
    public static void main(String[] args) {
        // 构建指挥
        Director director = new Director();
        // 指挥工人构建产品
        Product product = director.build(new Worker());
        // 测试产品
        System.out.println(product.getA());
    }
}
```



- 上面的例子是Builder模式的常规用法，Director在Builder模式中具有很重要的作用，它用于指导具体构建者如何构建产品，控制调用先后次序，并向调用者返回完整的产品类，但是有些情况下需要简化系统结构，可以把Director和抽象建造者进行结合。
- 通过静态内部类方式实现零件无序装配构造，这种方式使用更加灵活，更符合定义。内部有复杂对象的默认实现，使用是可以根据用户需求自由定义更改内容，并无需改变具体的构造方式。就可以产出不同的复杂产品。
- 比如：麦当劳的套餐，服务员（具体建造者）可以随意搭配产品组成套餐。我们不需要构造指挥者，因为我们把指挥者的功能交给了用户来操作，使得产品的创建更加简单灵活。



```java
// Builder
public abstract class Builder{
    abstract Builder buildBurger(String option);
    abstract Builder buildBeverage(String option);
    abstract Builder buildSide(String option);
    abstract Builder buildDessert(String option;
    
    abstract Product getProduct();
}
```



```java
// Product
public class Product {
    private String burger = "Regular Burger";
    private String beverage = "Regular coke";
    private String side = "Regular fries";
    private String dessert = "Regular smoothie";
}
```



```java
// Worker
public class Worker extends Builder {
    
    private Product product;
    
    public Worker() {
        product = new Product();
    }
    
    @Override
    Builder buildBurger(String option) {
        product.setBuildBurger(option);
        return this;
    }
    ...
        
    @Override
        Product getProduct() {
        return product;
    }
}
```



```java

public class Test {
    public static void main(String[] args) {
        
        Worker worker = new Worker();
        //链式编程： 在原来的基础上，可以自由组合，如果不组合，也有默认的套餐
        Product product = worker.buildBurger("Spicy Deluxe").buildBeverage("Diet Coke").getProduct();
        
        System.out.println(product.toString());
    }
}
```



### 建造者模式小结

#### 优点

- 产品的建造和表示分离，实现了解耦。客户端不必知道产品内部组成的细节。
- 将复杂的产品的创建步骤分解在不同的方法之中，让创建过程更清晰。
- 具体的建造者类之间是相互独立的，利于系统扩展。增加新的具体的建造者无需修改原有类库的代码，符合开闭原则。

#### 缺点

- 建造者模式所创建的产品一般具有较多的共同点，其组成部分相似。如果产品之间的差异性很大，则不适合！
- 如果产品的内部变化复杂，可能会导致需要定义很多具体的建造者来实现变化，导致系统庞大。



#### 应用场景

- 需要生成的产品对象有复杂的内部结构，这些产品对象具备共性。
- 隔离复杂对象的创建和使用，并使得相同的创建过程可以创建不同的产品。
- 适合一个有较多零件（属性）和产品（对象）的创建过程。



#### 与抽象工程相比较：

- 建造者模式返回一个组装好的完整产品，而抽象工厂模式返回一系列相关的产品。这些产品位于不同的产品等级结构，构成一个产品族。
- 抽象工厂模式中，客户端实例化工厂类，然后调用工厂方法获取产品对象。而建造者模式中，客户端可不直接调用建造者的相关方法，而是通过指挥者类来指导如何生成对象，包括对象的组装过程和建造步骤，它侧重于一步一步构造一个复杂对象，返回一个完整的对象。
- 如果将抽象工厂模式看成汽车配件生产工厂，生产一个产品族的产品，那么建造者模式就是一个汽车组装工厂，通过对部件的组装可以返回一辆完整的汽车。



## 原型模式

- 克隆
- Prototype
- Cloneable接口
- clone()方法



基于一个原型对象， 通过cloneable接口，重写clone()方法，避免重复复杂的构建程序，直接复制粘贴出另一个相同的克隆(不是相同的对象，是深拷贝！)，java中的cloneable是native接口，也就是调用的C++



```java
public class Video implements Cloneable {
    private Date createTime;
    private String name;
    
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
    
    public Video(){};
    
    public Video(String name, Date createTime) {
        this.name = name;
        this.createTime = createTime;
    }
    
    ...(get,set,toString)...
}
```



```java
public class Youtube {
    public static void main(String[] args) throws CloneNotSupportedException{
        Date date = new Date();
        Video v = new Video(name: "tutorial", date);
        Video v2 = (Video) v1.clone(); 
        
        System.out.println(v.hashCode());
        System.out.println(v2.hashCode());
    }
} // 但是这样的date是浅copy！
```



解决方法：

- 序列化，反序列化 (效率底)
- 改造Clone() (更方便)



```java
@Override
    protected Object clone() throws CloneNotSupportedException {
        Object obj = super.clone();
        Video v = (Video) obj;
        v.createTime = (Video) this.createTime.clone();
        return obj;
    }
```



Spring Bean: 单例模式，原型模式

原型模式 + 工厂模式 --> 把new替换为原型模式








## 适配器模式

#### 结构型模式: 从程序的结构上实现松耦合,从而扩大整体的类结构,用来解决更大的问题.



























