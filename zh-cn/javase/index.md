# JavaSE

# JavaSE

Environment variable: set JAVA_HOME to the addr of install java
Then add `%JAVA_HOME%/bin` to path.
We do so to let Tomcat to recognize the java dir by looking at the JAVA_HOME

.java -> javac.exe -> .class -> java.exe -> result

bytecode file .class has the name of the class name in .java

`javac HelloWorld.java` then `java HelloWorldClassName`

```java
class HelloWorldClassName {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
      }
  }
```

>Important
>
>1. You can declare multiple classes in a source code file, but you can only have 1 public class at most in it.
>
>2. if declare a public class, then the source code file name has to be the same name of the class!
>
>3. Depending on the number of class you have in a source file, the compile process may generate 1 or more bytecode files.

## Syntax

Low Coupling, High Cohesion

### Comment

1. //
2. /**/

3.

  ```java

/**
  @author nameOfAuthor
  @version verOfSourceCode
*/

```

Notice this can be extracted by javadoc
and produce a document.
`javadoc -d docName -author -version HelloWorld.java`
Find the index.html in the docName.
Maybe need -encoding UTF-8
Also notice that if you want to illustrate the func(),
you should write the /** xxx */ out of the func() not inside it.

### Java API Document

### Coding Style

### String

Sting is not a primitive data type
而是引用数据类型

When print function has multiple data types doing concatination using +, one String in it will make everything a string and concatinate to one big String.

```java
System.out.println('*' + '\t');
System.out.println('*' + "\t");
System.out.println("*" + '\t');
System.out.println("*" + "\t");
```

### Var

```java
byte b = 5;
b = b - 2; //wrong! here 2 is int
```

### Base

```java
0b or 0B 
0-7
0xA or 0Xa

int num1 = 0b1001;
int num2 = 0123;
int num3 = 0x110A;


System.out.println(num1);
System.out.println(num2);
System.out.println(num3);
// all in base of 10 after printing out
```

### Operator

int res = m % n
res has the same sign of m!

-12 % 5 == -12 % -5

### Scanner

```java
import java.util.Scanner;

class ScannerTest{
    public static void main(String[] args){
        Scanner scan = new Scanner(System.in);
        String str = scan.nextLine();
        int i = scan.nextInt();
      }
  }
```

### Control Flow with Label

```java
label: for (int i = 1; i < 4; i++){
    for (int j = 1; j <= 10; j++){
        if(j % 4 === 0)
          continue label;
      }
      System.out.println(j);
  }
    System.out.println();
```

### Variable Number of args

public static void test(String ... strs);
The number of String passed in can be any amount including no string at all.

### Constructor

Orders of the assignment of attributes:

implict assignment > explict assignment/assign in code block > constructor > methods or fields

#### Notice

If no constructors are declared. There will be a default one being created implictly. The access modifiers would be as same as that of its class.

### JavaBean

have to meet:

1. the class is public
2. have a non-parameter constructor
3. hava attributes and getters and setters

JavaBean is the component written in Java and can be reused.

### package and import

`package` is to achieve better management of classes
every `.` indicates a layer in the DOM
the package the current file belongs to need to be declared on the first line

#### some packages in JDK

1. java.lang: core packages of Java, containing String, Math, Integer, System, Thread...
2. java.net: internet related classes and interfaces
3. java.io: IO
4. java.util: define some system features, framework of interfaces, datatime...
5. java.text: format related
6. java.sql: JDBC related classes and interfaces
7. java.awt: GUI

### MVC

1. View: deal with display

   - view.utils
   - view.ui

2. Controller: deal with logic

   - controller.activity
   - controller.fragment
   - controller.adapter
   - controller.service
   - controller.base

3. Model: deal with data

   - model.bean/domain
   - model.dao
   - model.db

### Overload and Override

Overload is determined at compile time
Override is determined at runtime

### instanceof

```java
Person person = new Male();
if(person instanceof Male){
    Male male = (Male) person;
    male.whoami();
}

```

### == and equals()

### JUnit

1. add JUnit lib
2. create a class to do the unit test
    - it should be public
    - have a non-parameter constructor
3. declare test method
    - should be public
    - should not have a return type
    - no parameters
4. need to add @Test above the test methods
    - `import org.junit.Test;`
5. run as junit test

### wrapper/auto boxing & unboxing

byte --> Byte
long --> Long
char --> Character
...

```java
Object obj = true ? new Integer(1) : new Double(2.0);
System.out.println(obj);
// The answer is 1.0
// Ternary operator will unify the class of that 2 operants
// So Integer will be upgraded to Double
```

```java
Integer i = new Integer(1);
Integer j = new Integer(1);
System.out.println(i == j); // false

Integer m = 1;
Integer n = 1;
System.out.println(m == n); // true

Integer x = 128;// so here it's equivalent to new a Integer
Integer y = 128;
System.out.println(x == y); // false
// int has IntegerCache which stores from -128 to 127
// when doing the boxing, it will first check the related cache
```

### static

[static](https://www.cnblogs.com/uodut/p/7067195.html)

static method cannot be override! since it is loaded when class is loaded
You can only call static methods through the class name!
like `superClass.staticMethod()` and `subClass.staticMethod()`
They are not considered as override.

### code block

```java
class Person {
    String name;
    int age;
    static String description = "a person";
    
    // code block will be excuted before constructor!
    {}

    public Person(){
        
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void eat(){
        System.out.println("eating...");
    }

    // getter and setter here



```

code block is used to init the class and objects
can be modified with static.

> **static code block:**
> will only be excuted once when the class is loaded
> e.g. we can use this to load the config
> In this, you can call static or non-static attributes and methods
>
> **non-static code block:**
> will be excuted every time an object of the class is created

### final

final class cannot be extended: String, System, StringBuffer
final method cannot be override
final attribute have to be init(explictly or in code block or constructor)
final var is constant
final parameter means it's a constant

static final: global const

### abstract

> abstract class:
> a class having abstract methods in it must be an abstract class
> cannot be instantiated
> have a constructor!! you can call it in subclass' constructor
>
> ```java
> class Person{
>     public Person(int age, String name){
>         this.age = age;
>         this.name = name;
>     }
> }
>
> class Student extends Person {
>     public Student(int age, String name){
>         Super(age, name);
>     }
> }
>
> ```
>
>
> abstract method
> `public abstract void eat();`

abstract cannot modify `private`, `static`, `final class`, `final method`, attributes, and constructor.

#### implict inner class in abstract class

```java
method(new Student());
//-----------------------------
Person person = new Person(){
    @Override
    public void eat(){}

    @Override
    public void breath(){}
}

method(person);

//-----------------------------
method(new Person(){
    @Override
    public void eat(){}
});



```

### TempateMethod

make uncertain part or the part that tends to change accordingly abstract

```java
abstract class Template{
public void spendTime(){
        long start = System.currentTImeMillis();
        code(); // uncertain part!
        long end = System.currentTImeMillis();
        System.out.println("Time spent: " + (end - start));
    }

    public abstract void code();
}

```

#### Note

> 多态的概念比较复杂，有多种意义的多态，一个有趣但不严谨的说法是：继承是子类使用父类的方法，而多态则是父类使用子类的方法。
> 一般，我们使用多态是为了避免在父类里大量重载引起代码臃肿且难于维护。

### interface

```java
package interface_tutorial;
/*
* class and interface are on the same level
* - before JDK8: you can only define global const and abstract method
* > public static final
* > public abstract
*
* - after JDK8: you can also define static method, default method
*
* in real life, you want to implement interfaces through classes
*/
public class Interface_tutorial {
    public static void main(String[] args) {
        System.out.println(Flyable.MAX_SPEED);

        Plane plane = new Plane();
        plane.fly();
        plane.stop();
    }
}

interface Flyable{
    public static final int MAX_SPEED = 7900;
    int MIN_SPEED = 1; // you can omit public static final

    public abstract void fly();
    void stop(); // you can also omit public abstract

    // you cannot not have constructor in an interface
}

class Plane implements Flyable {

    @Override
    public void fly() {
        System.out.println("Jet fly!");
    }

    @Override
    public void stop() {
        System.out.println("Landed!");
    }
}
```

```java

package interface_tutorial;

public class Computer {
    public void dataTransfer(USB usb){ // interface is a kind of regulation or a set of rules
        usb.start();
        System.out.println("working details...");
        usb.stop();
    }

    public static void main(String[] args) {
        Computer computer = new Computer();
        PS5 ps5 = new PS5();
        computer.dataTransfer(ps5); // polymorphism, since you cannot pass in an instance of an interface!
    }
}

interface USB {
    void start();
    void stop();
}

class Printer implements USB {

    @Override
    public void start() {
        System.out.println("Printer data transfer started...");
    }

    @Override
    public void stop() {
        System.out.println("Printer data transfer stopped...");
    }
}

class PS5 implements USB {

    @Override
    public void start() {
        System.out.println("PS5 data transfer started...");
    }

    @Override
    public void stop() {
        System.out.println("PS5 data transfer stopped...");
    }
}

```

JDBC are just aggregations of interfaces to setup the program specification

> 暴露规则,降低耦合,功能拓展.

```java
// also we can do:

computer.dataTransfer(new USB() {
        @Override
        public void start() {
            System.out.println("implicit device data transfer started...");
    }

        @Override
        public void stop() {
            System.out.println("implicit device data transfer stopped...");
    }
});

```

### Proxy

```java
package proxy;

public class ProxyTest {

    public static void main(String[] args) {
        Server server = new Server();
        ProxyServer proxyServer = new ProxyServer(server);
        proxyServer.browse();
    }
}

interface Network {
    void browse();
}

// The Server being proxied
class Server implements Network {
    @Override
    public void browse(){
        System.out.println("Real server accesses Internet...");
    }
}

// Proxy
class ProxyServer implements Network {

    private Network network;
    public ProxyServer(Network network){
        this.network = network;
    }

    public void verify(){
        System.out.println("Verifying info before connecting to the Internet...");
    }

    @Override
    public void browse() {
        verify();
        network.browse();
    }

    public Network getNetwork() {
        return network;
    }

    public void setNetwork(Network network) {
        this.network = network;
    }
}

```

#### 应用场景

1. 安全代理： 屏蔽对真实角色的直接访问
2. 远程代理：通过代理类处理远程方法调用（RMI）
3. 延迟加载：先加载轻量级的代理对象，真正需要时再加载真实对象。比如一个文档中有图片，打开文件时不可能显示所有图片，可以使用代理模式在需要查看图片时用proxy进行大图的打开

#### 分类

1. 静态代理：写在代码中
2. 动态代理：运行时运用反射

### Interface （supplementary）

After Java8, you can define static and default method in interface

1. the static methods in interface only can be called by interfaces, you cannot call them from the classes implementing the interfaces.

2. default methods can be called by the instances of the classes implementing its interface, and it can be overrode but you shouldn't do that the most of time.

3. if one's super class and the interface it implemented have a method with same name, if this method is not overrode, the one of its super class will be called by default!!!

4. if one implements multiple interfaces who have a method with same name, and the method is not overrode --> error

5. how to call a overrode method from the current class' super class or interfaces implemented?

```java
// methodA and methodB have been overrode

super.methodB(); // call it from super class
interfaceA.super.methodA(); // call it from the interfaces implemented
```

### Inner Class

Declare a Class B inside Class A, B is inner class and A is outer class.

#### Member Inner Class v.s. Local Inner Class(declared in methods, code blocks, constructors)

```java
public class InnerTest {
    public static void main(String[] args) {
        // create static member inner class
        Person.Body body = new Person.Body();
        body.intro();

        // create non-static member inner class
        Person person = new Person();
        Person.Foot foot = person.new Foot();
        foot.intro();

        // it seems we cannot directly 'new' a local inner class object in the main...
        // we have to make it work in the relative methods...

    }
}

class Person {
    String name = "Jacky";
    public void method(){

        int localNum = 10; // if this var is accessed in the inner class declared in this method, then this var must be final(you don't need to put final here since it's automatically done implicitly). The reason is due to the lifetime. num here belong to the outer class, but you want to use that local var in inner class method, so you have to make it final so that outer class can send a copy of num, and you cannot change it since it's a copy and your change won't affect the original num in outer class.

        // local inner class
        class Hand {
            String name = "Jacky's Hand";
            public void intro(){
                //num = 20;   HERE IS WRONG, Variable 'num' is accessed from within inner class, needs to be final or effectively final!
                System.out.println("This is hand...");
            }
        }
    }

    {
        // local inner class
        class Head {
            String name = "Jacky's Head";
            public void intro(){
                System.out.println("This is head...");
            }

            public void speak(String name){
                System.out.println(name); // parameter
                System.out.println(this.name); // inner class attribute
                System.out.println(Person.this.name); // outer class attribute
            }

            // return a class that implemented the interface
//            public Comparable getComparable(){
//                class MyComparable implements Comparable {
//                    @Override
//                    public int compareTo(Object obj){
//                        return 0;
//                    }
//                }
//
//                return new MyComparable();
//            }

            // OR! Here we actually implicitly creat an anonymous inner class having the same name of the interface!
            public Comparable getComparable(){
                return new Comparable() {
                    @Override
                    public int compareTo(Object o) {
                        return 0;
                    }
                };
            }
        }
    }

    // non-static member inner class
    class Foot {
        String name = "Jacky's Foot";
        public void intro() {
            System.out.println("This is foot...");
        }
    }
    // static member inner class
    static class Body {
        String name = "Jacky's Body";
        public void intro(){
            System.out.println("This is body...");
        }
    }
}

```

**Notice**:
> inner class can be modified by static and private and all other access modifiers!

1. How to instantiate member inner class' object

2. How to distinguish the attributes of inner and outer classes in the inner class method

3. How to use the local inner class in real life

### Exception

```java
import org.testng.annotations.Test;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;

public class Exceptions {
    public static void main(String[] args) {

    }

    @Test
    public void test1(){
        String str = "abc";
        int num = 0;
        try{
            num = Integer.parseInt(str);
            System.out.println("Parsing...");
        }catch (NumberFormatException e){
            System.out.println("Catching the format exception...");
            System.out.println(e.getMessage());
            e.printStackTrace();
        }catch (NullPointerException e){
            System.out.println("Catching null pointers...");
            System.out.println(e.getMessage());
            e.printStackTrace();
        }catch (Exception e){
            System.out.println("Catching unexpected exceptions...");
            System.out.println(e.getMessage());
            e.printStackTrace();
        }

        System.out.println(num);
    }

    @Test
    public void test2_1() {
        FileInputStream fis = null;
        try{
            File file = new File("hello.txt");
            fis = new FileInputStream(file);

            int data = fis.read();
            while(data != -1){
                System.out.print((char)data);
                data = fis.read();
            }

            fis.close();
        }catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
                if(fis != null)
                    fis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    @Test
    public void test2_2() throws IOException { // throws them to upper level, until they are thrown by the main...
        FileInputStream fis = null;
        try{
            File file = new File("hello.txt");
            fis = new FileInputStream(file);

            int data = fis.read();
            while(data != -1){
                System.out.print((char)data);
                data = fis.read();
            }

            fis.close();
        }catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if(fis != null)
                fis.close();
        }
    }

    // finally-block will be executed even when catch block has exceptions or there is return statement in try&catch block
    // We have to terminate db-connection, IO stream, or things like network socket manually, since JVM cannot gc them
    @Test
    public int Test3(){
        int a = 100;
        int b = 0;
        try{
            System.out.println(a/b);
            return 1;
        }catch(ArithmeticException e){
            e.printStackTrace();
            return 2;
        }catch (Exception e){
            e.printStackTrace();
            return 3;
        }finally {
            System.out.println("Nailed it!");
        }
    }
}
```

1. try-catch-finally will deal with the exceptions and the code after it will be executed.

2. throws will throw the exceptions to the caller, and the code after it will not be executed.

```java
/*
* Rules for exception overridden
* The overrode current class' method cannot throw broader level of exception than its super class!
* Say if you have a method A, and in A, you have 3 consecutive method called where the next need the result of the last.
* Those methods should throw the exceptions to A, and A will use try-catch-finally to deal with them.
* Since we do not to need to run the code after, if there are dependencies.
*/

import java.io.FileNotFoundException;
import java.io.IOException;

public class ExceptionOverride {
    public static void main(String[] args) {
        ExceptionOverride eo = new ExceptionOverride();
        eo.display(new SubClass()); // polymorphism --> thus the super class need to at least handle all exceptions to its subclasses
    }

    public void display(SuperClass s){
        try{
            s.method();
        }catch (IOException e){
            e.printStackTrace();
        }
    }
}

class SuperClass {
    public void method() throws IOException {

    }
}

class SubClass extends SuperClass {
    public void method() throws FileNotFoundException {

    }
}

```

#### throw

in try-catch-finally and throws, the exception object is created by the system.

`throw` gives you the option to manually create the exception object.

```java
public class Throw {
    public static void main(String[] args) {
        Student s = new Student();
        s.register(-100);
    }
}


class Student{
    private int id;

    public void register(int id){
        if (id > 0){
            this.id = id;
        }else{
            throw new RuntimeException("illegal data");
        }
    }
}
```

#### Customized Exception

```java
/*
* How to?
* 1. inherit from existed exceptions: RuntimeException, Exception
* 2. provide a global val: serialVersionUID
* 3. provide a overloaded constructor
* */


public class CustomException extends RuntimeException{
    static final long serialVersionUID = -7483948573493424108L;

    public CustomException(){}
    public CustomException(String msg){
        super(msg);
    }
}

```

## Advanced Java Features

### Muti-Thread

#### Method 1

```java
/*
* How to create a thread?
* Method 1: Inheritance
*   1. create a class A extends Thread
*   2. override the run()
*   3. create an instance `a` of A
*   4. call a.start()
* */

public class ThreadDeclare {
    public static void main(String[] args) {
        MyThread myThread = new MyThread();
        myThread.start(); // new thread
        // Since we don't use it again once the thread is started, we can do it like: new MyThread().start();

        // main thread
        for (int i = 0; i < 100; i++) {
            if (i % 2 != 0){
                System.out.println(Thread.currentThread().getName() + ":" + Thread.currentThread().getPriority() + ":" + i);
            }
        }
    }
}

class MyThread extends Thread {
    @Override
    public void run(){
        for (int i = 0; i < 100; i++) {
            if (i % 2 == 0){
                System.out.println(Thread.currentThread().getName() + ":" + Thread.currentThread().getPriority() + ":" + i);
            }
        }
    }
}
```

1. We have to call start to start a new thread, if you call run, then it's running in the same thread.

2. Can we use the same thread instance to start a new thread? NO!!! Because the status of the thread object is not 0 any longer.

#### Frequently used methods of Thread Class

- void start(): start a new thread and call run()
- run(): what is executed while the thread is running
- String getName(): return the name of the thread
- void setName(String name): set the name of the thread
- static Thread currentThread(): return the current thread, often used in main thread and Runnable realization.
- staic void yield(): pause the current thread, give the chance to another thread with an same or higher priority. If there isn't any thread having the same priority, then ignore this method
- join(): caller thread will be blocked until the callee thread finishes
- static void sleep(long millies): put itself into sleep for a time, throw a InterruptedException
- stop(): force to end the lifetime of a thread, not recommanded
- boolean isAlive(): return boolean

#### Method 2

```java
/*
* Method 2: implement Runnable interface
* 1. Create a class A implemented Runnable interface
* 2. implement run() of the Runnable interface in A
* 3. create an instance `a` of A
* 4. pass `a` to the Thread constructor to create a Thread object o
* 5. o.start()
*
* notice that we always can choose to do this implicitly: new Thread(a).start();
* */
public class Runnable {
    public static void main(String[] args) {
        RunThread rt = new RunThread();
        new Thread(rt).start(); // why here the run() of the interface get called instead of that of the Thread object?
        // Because in the run() of Thread class calls the run() of the Runnable interface!
        new Thread(rt).start();
    }
}

class RunThread implements java.lang.Runnable {
    @Override
    public void run(){
        for (int i = 0; i < 100; i++) {
            if (i % 2 == 0){
                System.out.println(Thread.currentThread().getName() + ":" + i);
            }
        }
    }
}

```

```java
// NOTICE: not thread safe!

public class TicketSell {
    public static void main(String[] args) {
        Window window = new Window();

        Thread t1 = new Thread(window);
        Thread t2 = new Thread(window);
        Thread t3 = new Thread(window);

        t1.setName("Window01");
        t2.setName("Window02");
        t3.setName("Window03");

        t1.start();
        t2.start();
        t3.start();
    }
}

class Window implements java.lang.Runnable{
    private static int ticketNum = 100;

    @Override
    public void run(){
        while (true){
            if (ticketNum > 0){
                System.out.println(Thread.currentThread().getName() + "sold --> ticketID: " + ticketNum);
                ticketNum--;
            }else break;
        }
    }
}

```

It's obvious that the 2nd method is better in terms of data sharing and convenience of inheritance.

#### Lifetime of Thread

**State**:

1. NEW: thread state for a thread which has not yet started
2. RUNNABLE: ready
3. BLOCKED: thread blocked waiting for a monitor lock
4. WAITING: in the waiting state
5. TIMED_WAITING: waiting thread with a specified waiting time
6. TERMINATED

NEW --> start() --> RUNNABLE(ready but not nescessary be running)

#### Sync

**Sync Method 1**:

1. Synchronized Monitor can be any object
2. Those threads must use the same lock!!!

```java
public class TicketSell {
    public static void main(String[] args) {
        Window window = new Window();

        Thread t1 = new Thread(window);
        Thread t2 = new Thread(window);
        Thread t3 = new Thread(window); // here we can see that t1, t2, t3 are using the same lock

        t1.setName("Window01");
        t2.setName("Window02");
        t3.setName("Window03");

        t1.start();
        t2.start();
        t3.start();
    }
}

class Window implements java.lang.Runnable{
    private static int ticketNum = 100;
    private static Object lock = new Object(); // all the thread must use the same lock!

    @Override
    public void run(){
        while (true){
            synchronized (lock){ // or we can use `this.class` as the parameter
                if (ticketNum > 0){
                    System.out.println(Thread.currentThread().getName() + "sold --> ticketID: " + ticketNum);
                    ticketNum--;
                }else break;
            }
        }
    }
}

```

**Sync Method 2**: synchronized method

We can put the exact shared part in a method and modify it with `synchronized`

```java
public class TicketSell {
    public static void main(String[] args) {
        Window window = new Window();

        Thread t1 = new Thread(window);
        Thread t2 = new Thread(window);
        Thread t3 = new Thread(window);

        t1.setName("Window01");
        t2.setName("Window02");
        t3.setName("Window03");

        t1.start();
        t2.start();
        t3.start();
    }
}

class Window implements java.lang.Runnable{
    private static int ticketNum = 100;

    @Override
    public void run(){
        while (ticketNum != 0){
            sell();
        }
    }

    private synchronized void sell(){ // the monitor here is actually `this`
        if (ticketNum > 0){
            System.out.println(Thread.currentThread().getName() + "sold --> ticketID: " + ticketNum);
            ticketNum--;
        }
    }
}
```

```java
public class TicketSell {
    public static void main(String[] args) {

        Window t1 = new Window();
        Window t2 = new Window();
        Window t3 = new Window();

        t1.setName("Window01");
        t2.setName("Window02");
        t3.setName("Window03");

        t1.start();
        t2.start();
        t3.start();
    }
}

class Window extends Thread{
    private static int ticketNum = 100;

    @Override
    public void run(){
        while (ticketNum != 0){
            sell();
        }
    }

    // private synchronized void sell(){ // the monitor here is actually `this`, so the monitor would be t1,t2,t3!
    private static synchronized void sell(){ // unless we make it static! And `this.class` is the monitor here!!!
        if (ticketNum > 0){
            System.out.println(Thread.currentThread().getName() + "sold --> ticketID: " + ticketNum);
            ticketNum--;
        }
    }
}
```

#### Singleton

```java
class Bank{
    private Bank(){}

    private static Bank instance = null;

    private static synchronized Bank getInstance(){ // not efficient
        if(instance == null)
            instance = new Bank();
        return instance;
    }
}
```

```java
// Better efficiency
class Bank{
    private Bank(){}

    private static Bank instance = null;

    private static synchronized Bank getInstance(){
        if(instance == null)
            synchronized (this.class) {
                if(instance == null)
                    instance = new Bank();
            }
        return instance;
    }
}
```

#### Lock (after JDK5) [Sync Method 3]

- java.util.concurrent.locks.Lock
  - ReentrantLock

```java
import java.util.concurrent.locks.ReentrantLock;

public class LockTest {
    public static void main(String[] args) {
        SellWindow sellWindow = new SellWindow();

        Thread t1 = new Thread(sellWindow);
        Thread t2 = new Thread(sellWindow);
        Thread t3 = new Thread(sellWindow);

        t1.start();
        t2.start();
        t3.start();
    }
}

class SellWindow implements java.lang.Runnable{
    private static int ticket = 100;

    private static ReentrantLock lock = new ReentrantLock(true);

    @Override
    public void run(){
        while (true){
            lock.lock();
            if (ticket > 0){
                System.out.println(Thread.currentThread().getName() + ":" + ticket);
                ticket--;
            }else break;
            lock.unlock();
        }
    }
}
```

#### Thread Communication

```java
// Let 2 thread print 1-100 interleavingly 
public class Communication {
    public static void main(String[] args) {
        PrintNum printNum = new PrintNum();
        Thread t1 = new Thread(printNum);
        Thread t2 = new Thread(printNum);

        t1.start();
        t2.start();
    }
}

class PrintNum implements java.lang.Runnable{
    private int num = 1;

    @Override
    public void run(){

        while (true){
            synchronized (this) { // the monitor cannot be PrintNum.class, reason is listed below...
                notify();

                if (num < 100){
                    System.out.println(Thread.currentThread().getName() + ":" + num);
                    num++;

                    try {
                        wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                }else break;
            }
        }
    }
}
```

- wait(), notify(), notifyAll() must be used in the synchronized code block or method

- wait(), notify(), notifyAll()'s **caller** must be the **sync monitor** in the synchronized code block or method, or it will throw an exception

- wait(), notify(), notifyAll() are defined in java.long.Object class

#### [Great Practice Prob](https://www.bilibili.com/video/BV1Kb411W75N?p=443)

#### Method 3: Callable Interface

Compare to Runnable:

- may have a return val

- may throw exceptions (you can only use try-catch in run() of Runnable instead of throwing out the exceptions)

- support generic type

- need to borrow **FutureTask** class' help to get the return val

  - Future Interface:

    - can do oprations on Runnable and Callable tasks, like cancel the executed results, query if finishes, get the result...

    - FutureTask is the only implemented class of Future interface

    - FutureTask realized Runnable and Future interfaces at the same time, it can be excuted by a thread as Runnable implementation and also get the return val of Callable implementation as a Future implementation

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;
import java.util.concurrent.FutureTask;

public class CallableTest {
    public static void main(String[] args) {
        NumThread numThread = new NumThread();

        FutureTask futureTask = new FutureTask(numThread);

        new Thread(futureTask).start();

        try {
            Object sum = futureTask.get();
            System.out.println("Sum is: " + sum);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}


class NumThread implements Callable {

    @Override
    public Object call() throws Exception {
        int sum = 0;
        for (int i = 1; i < 100; i++) {
            if (i % 2 == 0){
                System.out.println(i);
                sum += i;
            }
        }
        return sum;
    }
}
```

1. create a class implements the Callable interface

2. implement call()

3. create the Callable implementation instance

4. pass it into a FutureTask constructor to create an object

5. pass this FutureTask instance into a thread as Runnable implementation, and call start()

6. if you need to reture val of the Callable's call(), use the get() of the FutureTask instance

#### Tread Pool [Method 4]

- Better response time
- Lower resource consumption
- Easier thread management
  - corePoolSize
  - maximumPoolSize
  - keepAliveTime...

// Notice that we do not do thread pool in this way in real life, this is only the concepts
// We may just use framework or CompletableFuture

1. create a thread pool
2. assign it with implemented Runnable or Callable
3. shut it down

```java
import java.util.concurrent.Callable;
import java.util.concurrent.Executor;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ThreadPoolTest {
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newFixedThreadPool(10);

        executorService.execute(new SomeOtherThread());
        executorService.submit(new SomeThread());

        // if you want to set the config of the thread pool
        System.out.println(executorService.getClass());
        ThreadPoolExecutor service = (ThreadPoolExecutor) executorService;
        service.setCorePoolSize(20);

        executorService.shutdown();
    }
}

class SomeThread implements Callable{

    @Override
    public Object call(){
        int sum = 0;
        for (int i = 0; i < 100; i++) {
            System.out.println(i);
            sum += i;
        }
        return sum;
    }
}

class SomeOtherThread implements java.lang.Runnable{

    @Override
    public void run(){
        for (int i = 200; i < 250; i++) {
            System.out.println(i);
        }
    }
}
```

### Frequently Used Classes

#### String class

```java
import org.testng.annotations.Test;

public class StringTest {
    @Test
    public void test() {
        String s1 = "Java";
        String s2 = "Test";

        String s3 = "JavaTest";
        String s4 = "Java" + "Test";

        String s5 = s1 + "Test"; // s1 point to a pointer in the heap, and the pointer points to "Java" in the constant pool
        String s6 = s1 + s2;

        String s7 = s5.intern(); // find the literal in constant pool and return it
        final String s8 = s1 + s2;

        System.out.println(s3 == s4); //T
        System.out.println(s3 == s5); //F
        System.out.println(s3 == s6); //F
        System.out.println(s5 == s6); //F
        System.out.println(s3 == s7); //T

        System.out.println(s3 == s8); // F Look at this!
        System.out.println(s3.getClass()); // same
        System.out.println(s8.getClass());
        System.out.println(s3.hashCode()); // same
        System.out.println(s8.hashCode());
        System.out.println(System.identityHashCode(s3)); // diff
        System.out.println(System.identityHashCode(s8));
    }
}

```

#### StringBuffer and StringBuilder

String, StringBuffer, StringBuilder diff?

String: immutable
StringBuffer: mutable, thread-safe, low efficiency
StringBuilder: mutable, not thread-safe, high efficiency

Efficiency: StringBuilder > StringBuffer >> String

```java
@Test
    public void testStringBuffer(){
        String str = null;
        StringBuffer sb = new StringBuffer();
        sb.append(str);

        System.out.println(sb.length()); // 4
        System.out.println(sb); // "null"

        StringBuffer sb1 = new StringBuffer(str); // NullPointerException
        System.out.println(sb1);
    }
```

#### Date

```java
import org.testng.annotations.Test;

import java.util.Date;

import static java.lang.Thread.sleep;

public class DateTest {
    @Test
    public void test01(){
        long time = System.currentTimeMillis(); // return the time diff from 1970.1.1 00:00:00 in millisecond
        System.out.println(time);
    }

    // java.util.Date
    //    |---java.sql.Date // this is used in sql

    @Test
    public void Test02() throws InterruptedException {
        // Constructor 1
        Date date1 = new Date();
        System.out.println(date1.toString());
        System.out.println(date1.getTime());

        // Constructor 2
        Date date2 = new Date(294475929283L);
        System.out.println(date2.toString());

        // sql.Date
        java.sql.Date date3 = new java.sql.Date(4365435414324L);
        System.out.println(date3);

        // util.Date --> sql.Date // except forced cast it
        Date date4 = new Date();
        java.sql.Date date5 = new java.sql.Date(date4.getTime());
        System.out.println(date5);
    }


}

```

#### java.text.SimpleDateFormat

```java
import org.testng.annotations.Test;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

// 1. date --> string
// 2. string --> date

public class SimpleDateFormatTest {
    @Test
    public void testSimpleDateFormat() throws ParseException {

        SimpleDateFormat sdf = new SimpleDateFormat();
        Date date = new Date();

        // date --> String
        String dateFormatted = sdf.format(date);
        System.out.println(dateFormatted);

        // String --> date
        String sb = "1/1/22, 8:00 PM";
        Date dateParsed = sdf.parse(sb);
        System.out.println(dateParsed);

        SimpleDateFormat sdf1 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"); // if you want a specific pattern
        String dateFormatted1 = sdf1.format(date);
        System.out.println(dateFormatted1);

        // date --> String
        Date date1 = sdf1.parse("2022-01-02 18:12:58");
        System.out.println(date1);
    }
}
```

#### Calendar

```java
import org.testng.annotations.Test;

import java.util.Calendar;
import java.util.Date;
import java.util.Scanner;

public class CalendarTest {

    // Calendar is an abstract class
    @Test
    public void testCalendar(){

        // Instantiation
        // 1. create instance of its subclass GregorianCalendar
        // 2. call the static method getInstance()

        Calendar calendar = Calendar.getInstance();
        System.out.println(calendar.getClass()); //GregorianCalendar

        // Usage: get() set() add() getTime() setTime()
        int dayOfMonth = calendar.get(Calendar.DAY_OF_MONTH);
        System.out.println(dayOfMonth);
        System.out.println(calendar.get(Calendar.DAY_OF_YEAR));

        calendar.set(Calendar.DAY_OF_YEAR, 365);
        System.out.println(calendar.get(Calendar.DAY_OF_YEAR));

        calendar.add(Calendar.DAY_OF_YEAR, -100);
        System.out.println(calendar.get(Calendar.DAY_OF_YEAR));

        // calendar --> date
        Date date = calendar.getTime();
        System.out.println(date);

        // date --> calendar
        Date date1 = new Date();
        calendar.setTime(date1);
        dayOfMonth = calendar.get(Calendar.DAY_OF_MONTH);
        System.out.println(dayOfMonth);

        // Notice that when you get the month, it starts from 0, and when get the date Sunday is 1 and Sat. is 7
    }
}
```

#### JDK8 new APIs about date and time

Probs:

1. things like date and time should be immutable
2. year starts from 1900 in Date and month starts from 0 --> new Date(2022,1,1) --> Fri Feb 01 00:00:00 GMT+08:00 3922
3. format only works for Date but not Calendar
4. they are not thread-safe and cannot deal with leap second

So now we have **java.time**

```java
import org.testng.annotations.Test;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;

public class TimeTest {

    @Test
    public void timeTest(){

        // now(): Instantiation
        LocalDate localDate = LocalDate.now();
        LocalTime localTime = LocalTime.now();
        LocalDateTime localDateTime = LocalDateTime.now();

        System.out.println(localDate);
        System.out.println(localTime);
        System.out.println(localDateTime);

        // of()
        LocalDateTime time1 = LocalDateTime.of(2022, 1, 1, 18, 0, 0);
        System.out.println(time1);

        //getXXX()
        System.out.println(localDateTime.getDayOfYear());
        System.out.println(localDateTime.getMonthValue());

        // immutable! withXXX()
        LocalDateTime time2 = localDateTime.withDayOfMonth(30);
        System.out.println(localDateTime); // the original data is immutable
        System.out.println(time2); // only return val changed

        // immutable! arithmetic operations
        LocalDateTime time3 = localDateTime.plusMonths(5);
        System.out.println(localDateTime);
        System.out.println(time3);
    }
}
```

#### Instant

```java
import org.testng.annotations.Test;

import java.time.Instant;
import java.time.OffsetDateTime;
import java.time.ZoneOffset;

public class InstantTest {
    @Test
    public void instantTest(){
        // instantiate
        Instant instant = Instant.now();
        System.out.println(instant);

        // add offset since timezone
        OffsetDateTime offsetDateTime = instant.atOffset(ZoneOffset.ofHours(8));
        System.out.println(offsetDateTime);

        // get the milli-time since 1970.1.1 00:00:00
        long l = instant.toEpochMilli();
        System.out.println(l);

        // get Instant instance from a millis
        Instant instant1 = Instant.ofEpochMilli(4823749237420L);
        System.out.println(instant1);
    }
}
```

#### DateTimeFormatter

```java
import org.testng.annotations.Test;

import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.time.format.FormatStyle;
import java.time.temporal.TemporalAccessor;

public class DateTimeFormatterTest {
    @Test
    public void test(){
        // 1. pre-defined std format
        DateTimeFormatter formatter = DateTimeFormatter.ISO_LOCAL_DATE_TIME;

        LocalDateTime localDateTime = LocalDateTime.now();
        System.out.println(localDateTime);
        formatter.format(localDateTime);
        System.out.println(localDateTime);

        TemporalAccessor parsedTime = formatter.parse("2022-01-02T19:04:38.722511");
        System.out.println(parsedTime);

        // 2. localized format
        DateTimeFormatter formatter1 = DateTimeFormatter.ofLocalizedDateTime(FormatStyle.SHORT);
        String formattedTime = formatter1.format(localDateTime);
        System.out.println(formattedTime);

        // 3. customized format: mostly used!!!
        DateTimeFormatter formatter2 = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        String formatted = formatter2.format(LocalDateTime.now());
        System.out.println(formatted);

        TemporalAccessor accessor = formatter2.parse("2022-01-02 19:11:04");
        System.out.println(accessor);
    }
}
```

#### Other API about date and time

- ZoneId
- ZonedDateTIme
- Clock
- Duration
- Period
- TemporalAdjuster
- TemporalAdjusters

Conversions among Date, Calendar, Instant, LocalDateTime...

#### Comparator

1. Natural sorting: java.lang.Comparable
2. Customized sorting: java.lang.Comparator

```java
import org.testng.annotations.Test;

import java.util.Arrays;

public class Compare {

    /*
    * Comparable interface:
    * 1. override the compareTo(obj)
    * 2. this > obj --> return positive
    *    this < obj --> return negative
    *    this = obj --> return 0
    *
    * */

    public static void main(String[] args) {
        Goods[] arr = new Goods[4];
        arr[0] = new Goods("iPhone", 1200);
        arr[1] = new Goods("iPad", 1200);
        arr[2] = new Goods("MacBook Pro", 3599);
        arr[3] = new Goods("iMac Pro", 4999);

        Arrays.sort(arr);

        System.out.println(Arrays.toString(arr));
    }

    @Test
    public void compareTest(){
        String[] arr = new String[]{"T", "A", "C", "E", "A", "Z", "X", "D"};
        Arrays.sort(arr);
        System.out.println(Arrays.toString(arr));
    }
}

class Goods implements Comparable{
    private String name;
    private double price;

    public Goods(){}

    public Goods(String name, double price){
        this.name = name;
        this.price = price;
    }

    @Override
    public int compareTo(Object o) {
        // Method 1:
        // return Double.compare(this.price, goods.price);

        // Method 2:
        if (o instanceof Goods){
            Goods goods = (Goods) o;
            if (this.price > goods.price)
                return 1;
            else if (this.price < goods.price)
                return -1;
            else return this.name.compareTo(goods.name);
        }else throw new RuntimeException("Passed in a wrong parameter type");
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public double getPrice() {
        return price;
    }

    public void setPrice(double price) {
        this.price = price;
    }

    @Override
    public String toString() {
        return "Goods{" +
                "name='" + name + '\'' +
                ", price=" + price +
                '}';
    }
}
```

```java

import org.testng.annotations.Test;
import java.util.Arrays;

public class ComparableTest {
    @Test
    public void compareTest(){
        String[] arr = new String[]{"T", "A", "C", "E", "A", "Z", "X", "D"};
        Arrays.sort(arr, (o1, o2) -> -o1.compareTo(o2));
        System.out.println(Arrays.toString(arr));
    }
}
```

```java
import java.util.Arrays;
import java.util.Comparator;

public class Compare {
    public static void main(String[] args) {
        Goods[] arr = new Goods[4];
        arr[0] = new Goods("iPhone", 1200);
        arr[1] = new Goods("iPad", 1200);
        arr[2] = new Goods("MacBook Pro", 3599);
        arr[3] = new Goods("iMac Pro", 4999);

        Arrays.sort(arr, new Comparator<Goods>() {
            @Override
            public int compare(Goods o1, Goods o2) {
                if (o1.getPrice() > o2.getPrice())
                    return -1;
                else if (o1.getPrice() < o2.getPrice())
                    return 1;
                else return o1.getName().compareTo(o2.getName());
            }
        });

        System.out.println(Arrays.toString(arr));
    }
}

// omitted class Goods
```

#### System, Math, BigInteger, BigDecimal

- System
  - in
  - out
  - err
  - exit(int status)
  - native long currentTimeMillis()
  - void gc()
  - String getProperty(String key)

- Math
  - abs
  - acos, asin, atan, cos, sin, tan
  - sqrt
  - pow(double a, double b)
  - log
  - exp
  - max(double a, double b)
  - min(double a, double b)
  - random()
  - long round(double a)
  - toDegrees(double angrad)
  - toRadians(double angdeg)

- BigInteger, BigDecimal
  - public BigInteger abs()
  - BigInteger add(BigDecimal)...

```java
import org.testng.annotations.Test;

import java.math.BigDecimal;
import java.math.BigInteger;
import java.math.RoundingMode;

public class BigNum {
    @Test
    public void test(){
        BigInteger bi = new BigInteger("423874298347279652938748017419723698127394723984692374817289473219347912873");
        System.out.println(bi);

        BigDecimal bd1 = new BigDecimal("54354.43243535342342354345453534");
        BigDecimal bd2 = new BigDecimal("15");

        System.out.println(bd1.divide(bd2, RoundingMode.HALF_UP));
        System.out.println(bd1.divide(bd2, 55, RoundingMode.HALF_UP));
    }
}
```

### Enum

- values()
- valueOf(String str)
- toString()

```java
public class EnumClass {
    public static void main(String[] args) {
        System.out.println(Season.SPRING);
        System.out.println(Season.SPRING.getName());
        System.out.println(Season.SPRING.getDes());

        System.out.println(Food.SOUP);

        Season[] values = Season.values();
        for (Season value : values) {
            System.out.println(values[i]);
        }

        Season season = Season.valueOf("SPRING");
        System.out.println(season);
    }
}

enum Food{SOUP, NOODLE, MEAT, VEGETABLE}

enum Season{
    SPRING("Spring", "warm"),
    SUMMER("Summer", "hot"),
    AUTUMN("Fall", "chill"),
    WINTER("Winter", "cold");

    private final String name;
    private final String des;

    private Season(String name, String des){
        this.name = name;
        this.des = des;
    }

    public String getName() {
        return name;
    }

    public String getDes() {
        return des;
    }

//    @Override
//    public String toString() {
//        return "Season{" +
//                "name='" + name + '\'' +
//                ", des='" + des + '\'' +
//                '}';
//    }
}
```

#### Using enum to implement interfaces

```java
interface Info{
    void show();
}

enum Food implements Info{
    SOUP{
        @Override
        public void show(){
            System.out.println("Better than water");
        }
    },
    NOODLE {
        @Override
        public void show() {
            System.out.println("Best Carbohydrate");
        }
    },
    MEAT{
        @Override
        public void show() {
            System.out.println("The soul of life");
        }
    },
    VEGETABLE{
        @Override
        public void show() {
            System.out.println("damn...");
        }
    }

    //@Override // if we override show() here, it override everyone's
    //...
}
```

### Annotation

```java
// For javaDoc
@author
@version
@see
@since
@param // for method, and can have more than one
@return // for method
@exception //for method, and can have more than one

// Compile time check
@Override
@Deprecated
@SupperessWarnings("unused")
@SupperessWarnings({"unused", "rawtypes"})
...

// Junit
@Test(timeout = 1000)
@Test(expected = Exception.class)
// the 5 below should be used along @Test rather than use them alone
@BeforeClass // for static method, executed at the init of class
@AfterClass // for static method, executed after all other methods
@Before // for non-static methods, executed before every @Test
@After // for non-static methods
@Ignore // mark the unfinished test method to ignore them

// 
```

- keyword: @interface
- java.lang.Annotation Interface
- Annotation's member var is defined as non-param methods!
- You can use all 8 primitive type and String, Class, enum, Annotation as the type
- Its method name and return val are the annotation's name and type
- you can use default to assign default val
- if there is only 1 member var, naming it as value is suggested
- if the annotation has no member, then it's called mark, otherwise metadata Annotation
- if there are params for the annotation then you have to appoint their vals, unless they have default vals. `paramName = val`
- if there is only one param and the name is value, we can omit `value=`

```java
@Mark
@MyAnnotation(value = "val")
@MyAnnotation("val")
class Person{

}

@interface MyAnnotation{
    String value() default "d";
}

@interface Mark{}
```

Annotation can help tracing to code's dependency to realize the function of config files.
e.g.: Servlet3.0 support annotation, so we don't need to deploy Servlet functions in `web.xml`

```java
// meta annotation
@Retention
@Target
@Documented
@Inherited
```

@Retention includes a member var RetentionPolicy

- RetentionPolicy.SOURCE: compiler will discard it
- RetentionPolicy.CLASS: when the java program is running, JVM will not retain this for default
- RetentionPolicy.RUNTIME: retained when the java program is running, and can be obtained through reflection

@Target regulates what elements the annotaion can act on

- CONTRUCTOR
- FIELD: attributes
- PACKAGE
- PARAMETER
- LOCAL_VARIABLE
- TYPE: class, interface including annotation, and enum
- METHOD

@Documented means all the annotations modified by this can be exacted into the javadoc

- notice that @Documented must be set to @Retention(RetentionPolicy.RUNTIME)

@Inherited means that all annotation modified by this can be inherited

- annoataions on a class will be inherited by its subclass automatically

#### Features in JDK8

- support repeatable annotation
  - Declare @Repeatable on MyAnnotation, and the arg is MyAnnotations.class
  - the @Target and @Retention and @Iherited must be same on both MyAnnotation and MyAnnotations

```java
import java.lang.annotation.*;
import static java.lang.annotation.ElementType.*;

public class AnnoTest {}

@Repeatable(MyAnnotations.class)
@Inherited
@Retention(RetentionPolicy.RUNTIME)
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE})
@interface MyAnnotation{
    String value();
}

@Inherited
@Retention(RetentionPolicy.RUNTIME)
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE})
@interface MyAnnotations{
    MyAnnotation[] value();
}

@MyAnnotation("hi")
@MyAnnotation("hello")
class TestClass{}
```

- Added 2 ElementType member:
  - ElementType.TYPE_PARAMETER: can be used on type var's declaration --> e.g.: generic type declaration
  - ElementType.TYPE_USE: can be used on any type args

```java
class Generic<@MyAnnotation("hey") T>{
    public void show(){
        ArrayList<@MyAnnotation("yo") String> list = new ArrayList<>();
    }
}
```

### Collection

- Collection
  - List: ArrayList, LinkedList, Vector
  - Set: HashSet, LinkedHashSet, TreeSet
- Map: HashMap, LinkedHashMap, TreeMap, Hashtable, Properties

```java
import org.testng.annotations.Test;

import java.util.ArrayList;
import java.util.Collection;
import java.util.Date;
import java.util.Iterator;

public class CollectionTest {

    @Test
    public void test1(){
        Collection c = new ArrayList();

        c.add("A");
        c.add("BB");
        c.add(123); // auto-boxing
        c.add(new Date());

        System.out.println(c.size()); // num of elements in c

        Collection c1 = new ArrayList();
        c1.add("A");
        c1.addAll(c); // add all elements in c into c1
        System.out.println(c1.size());
        System.out.println(c1);

        // isEmpty()
        System.out.println(c.isEmpty());

        // clear()
        c.clear();
        System.out.println(c.size());
        System.out.println(c.isEmpty());

        // check jdk doc to learn other APIs

        Iterator iter = c1.iterator();

        while(iter.hasNext()){
            Object obj = iter.next();
            if("A".equals(obj)){
                iter.remove(); // this just remove the element in this iterator not the collection!!
            }else System.out.println(obj);
        }
    }
}
```

#### List interface

- Ordered and Repeatable

1. ArrayList: Not thread-safe, effcient, use `Object[] elementData` at low level
2. LinkedList: good for frequent insertion and deletion, use doubly linked list for storage at low level
3. Vector: Thread-safe, less efficiency, use `Object[] elementData` at low level

ArrayList Source Code:
    In JDK7, when the object is created, an Object[] array of length 10 is created.
    If you call add() and the exceeds the capacity, enlarge it by 1.5 time.
    But if you pass in size when you init the ArrayList, then you will get the Object[] with the exact size, which is recommended.
    In JDK8, lazy mode, the array is created after you first call add()

Vector:
    started with 10, enlarged by 2.

#### Set Interface

- Unordered and non-repeatable
  
1. HashSet: Not thread-safe, can store `null`
2. |----LinkedHashSet: can traversal itself like a linked list
3. TreeSet: can be sorted

HashSet:
    Calculate the hashCode() and go to the corresponding index, if there is an element, call equals() of the element added.
    If same, discard; if not, make them a linked lists
    If the length exceeds 8, make it a red-black tree.
    In JDK8, the added element appends to the tail.

**Notice**:
    The class that is being added to Set must override hashCode() and equals() and maintain consistency, which means the same object should have the same hash code.
    Tip: Use same elements for the algos in hashCode() and equals().

HashSet's lower level is HashMap.

LinkedHashList:
    Based on the HashSet, added Node containing the element.

TreeSet:
    Cannot add different objects of different classes! Since we need to compare them for natural sorting or customized sorting.
    Implement Comparable and rewrite compareTo(). Or, we can use Comparator<> as what we discussed before.
    TreeSet uses red-black tree.

#### Map Interface

1. HashMap: mainly used. Non-thread-safe, high efficiency. Can store null as key and val.
2. |----LinkedHashMap: Better than HashMap for frequently traversal.
3. TreeMap
4. Hashtable: old implementation. Thread-safe, lower efficiency. Cannot store null as key and val.
5. |----Properties: Used for configs, the key and val are String.

In Map --> Key: Set, Value: Collection
Since keys cannot repeat, but vals can be same.
In the class of the key, equals() and hashCode() must be overrode.
In the class of the val, equals() needs to be overrode.

JDK7:
    HashMap starts with Entry[] table of length 16.
    map.put(key1, value1);
    Firstly, key1.hashCode() will get the index in the Entry[].
    If the place is empty, success; else, key1.equals(existedKey) is called for every existed key in there.
    If same, replace the old val with the new val; else, success.
    Enlarge by 2.

JDK8:
    1. new HashMap(): doesn't create a 16 long array
    2. Use Node[] instead of Entity[]
    3. Create the 16 long array when firstly call put()
    4. array + linked list --> array + linked list + red-black tree(len of linked list >= 8 and length of arr > 64)

- DEFAULT_INITIAL_CAPACITY: 16
- MAXIMUM_CAPACITY: 2^30
- DEFAULT_LOAD_FACTOR
- TREEIFY_THRESHOLD: If the len of linked list in a Bucket is larger than this val, convert to red-black tree
- MIN_TREEIFY_CAPACITY: When a Node is treeified, the min cap of the hashtable, which should be at least 4 times of the TREEIFY_THRESHOLD
- table: array to store elements, 2^n
- entrySet: the specific set to store the elements
- size: key-value pairs' num
- modCount: The count of resizing and restructuring
- threshold: cap * load factor = 12 by default
- loadFactor: 0.75 by default

LinkedHashMap: Use Entry extends Node to add `Entry<K,V>` before, after;

HashSet just put it elements in the keys of HashMap, and the vals are PRESENT, which is a static final Object indicating a null.

### Collections

Utils to operate on Collection and Map

- Sorting (static)
  - reverse(List)
  - shuffle(List)
  - sort(List)
  - sort(List, Comparator)
  - swap(List, int, int)

- Searching and Substitution
  - Object max(Collection)
  - Object max(Collection, Comparator)
  - ...min...
  - int frequency(Collection, Object)
  - void copy(List dest, List src)
  - boolean replaceAll(List list, Object oldVal, Object newVal)

### Generic

- Collection and Collections support Generic type
- It cannot be a primitive type(need boxing)
- If not giving a specific type at init, it's java.lang.Object by default

```java
public class GenericTest {
    public static void main(String[] args) {
        Order<String> order = new Order<>();
        String s = "Wow!";
        order.setOrderT(s);

        // We've specified the type at inheritance so that we don't need to point it out again at init
        SubOrder subOrder = new SubOrder();
    }
}

class SomeOrder<T> extends Order<T>{

}

class SubOrder extends Order<String> {
    String subName;
    int subId;
}

class Order<T> {
    String orderName;
    int orderId;

    T orderT;

    public Order(){}
    public Order(String orderName, int orderId, T orderT){
        this.orderId = orderId;
        this.orderName = orderName;
        this.orderT = orderT;
    }

    public String getOrderName() {
        return orderName;
    }

    public void setOrderName(String orderName) {
        this.orderName = orderName;
    }

    public int getOrderId() {
        return orderId;
    }

    public void setOrderId(int orderId) {
        this.orderId = orderId;
    }

    public T getOrderT() {
        return orderT;
    }

    public void setOrderT(T orderT) {
        this.orderT = orderT;
    }
}
```

#### Generic Class and Interface

1. If having multi-param --> `<E1, E2, E3>`
2. The constructor of generic class is `public GenericClass(){}`, NOT `public GenericClass<E>(){}`
3. Although `ArrayList<Integer>` and `ArrayList<String>` are diff, only 1 ArrayList is loaded to JVM at runtime
   - They are treated as diff at compile time, but same thing at runtime. So you can pass the compile if anything wrong.
4. CANNOT use Generic type in **static method**(since the param must be specified at init, you might not know the exact type when you call the static method) and **Exception**.
5. Cannot use `new E[]`, but you can use `E[] elements = (E[]) new Object[cap]` since the `Object[] elementData` in ArrayList source code has no generic type
6. Subclass inherit the generic type

   ```java
   class Father<T1, T2>{}

    class Son1 extends Father{} // equivalent to extents Father<Object, Object>

    class Son2 extends Father<Integer, Integer>{}

    class Son3<T1, T2> extends Father<T1, T2>{}

    class Son4<T2> extends Father<Integer, T2>{}
   ```

#### Generic Method

`<T> T[] toArray(T[] a);` --> Yes
`public static <E> List<E> copyFromArrayToList(E[] arr){}` --> Yes
`int[] toArray(T[] a);` --> No

Generic method has nothing to do with whether its class or interface is generic.
In the method, there must be generic structure, the generic type in param and class's param don't matter.

Why we need `<T>` --> if not, the compiler will see the `<E>` in `List<E>` as an actual class, not a generic type.

**Can be `static`** because the generic type in Generic method is specified when being called rather than at init, it's not related to the generic type of its class.

#### ?

```java
List<String> list1 = new ArrayList<String>();
List<Integer> list2 = new ArrayList<Integer>();

list1.add("AA");
list1.add("BB");
list1.add("CC");

list2.add(111);
list2.add(222);
list2.add(333);

List<?> list = new ArrayList();
list = new ArrayList<String>();

list = list1;
System.out.println(list);
list = list2;
System.out.println(list);

list.add(null);
// list.add("DD"); // CANNOT add anything in a List<?> rather than null

// you can read anything to Object
Object o = list.get(0);
System.out.println(o);
```

> We can appoint the upper limit and lower limit of the ? using `extends` and `super`
> <? extends NUmber> only allow Number and its subclasses
> <? super Number> only allow Number and its superclass and upper levels
> <? extends Comparable> only allow classes implemented Comparator interface

#### Cannot assign superclass to its subclass

### IO Stream

#### File

```java
/*
* An object of a File class can be a file or a file directory
* java.io
* */
import org.testng.annotations.Test;
import java.io.File;

public class Files {
    @Test
    public void Test1(){
        File file = new File("." + File.pathSeparator +  "hello.txt");
        File dir = new File(".", "Hey_dir");
        File file1 = new File(dir, "yo.txt");
    }
}
```

- String getAbsolutePath()
- String getPath()
- String getName()
- String getParent()
- long length()
- long lastModified()
- String[] list()
- File[] listFiles()
- boolean renameTo(File dest): rename to a new path

- boolean isDirectory()
- boolean isFile()
- boolean exists()
- boolean canRead()
- boolean canWrite()
- boolean isHidden()

- boolean createNewFile()
- boolean mkdir()
- boolean mkdirs(): if parent path doesn't exist, create it as well

- boolean delete(): if it's a dir, there should not have any subdir or files in it!

#### IO Stream Classify

|abstract base class|bit stream|byte stream|
|-------------------|----------|-----------|
|input|InputStream|Reader|
|output|OutputStream|Writer|

bit stream is good for pics, video, and audio...
byte stream is good for text-based file

|抽象基类|节点流(文件流)|缓冲流(处理流的一种)|转换流(处理流的一种)|打印流(same)|数据流|对象流|
|----|----|----|----|----|----|----|
|InputStream|FileInputStream|BufferedInputStream|InputStreamReader|NA|DataInputStream|ObjectInputStream|
|OutputStream|FileOutputStream|BufferedReader|OutputStreamWriter|PrintStream|DataOutputStream|ObjectOutputStream|
|Reader|FileReader|BufferedReader|NA|NA|NA|NA|
|Writer|FileWriter|BufferedWriter|NA|PrintWriter|NA|NA|

RandomAccessFile can be both input and output

- long getFilePointer(): get the pointer
- void seek(long pos): set the position of the pointer

```java
import org.testng.annotations.Test;

import javax.xml.namespace.QName;
import java.io.*;
import java.nio.charset.StandardCharsets;
import java.util.Locale;

/*
* 根据操作数据单位：字节流，字符流
* 根据流向：输入流输出流
* 根据角色：节点流，处理流
* */
public class IOStream {
    @Test
    public void testFileReader(){
        FileReader fr = null;
        try {
            // 1. create file object
            File file = new File("./src/io/java/hello.txt"); // this file has to be existed, or it'll throw FileNotFoundException
            System.out.println(file.getAbsolutePath());

            // 2. create a stream
            fr = new FileReader(file);

            // 3. read data
            // read() returns the char read in, if EOF return -1
            int data;
            while ((data = fr.read()) != -1){
                System.out.print((char)data);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally { // We have to ensure the stream got closed!
            // 4. close stream
            try {
                if (fr != null)
                    fr.close(); // gc cannot close this, we have to do it manually
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    @Test
    public void testFileReaderUpgraded(){
        FileReader fr = null;
        try {
            File file = new File("./src/io/java/hello.txt");

            fr = new FileReader(file);

            char[] cbuf = new char[8];
            int len;
            while ((len = fr.read(cbuf)) != -1) { // Every time it reads chars if size of cbuf
                for (int i = 0; i < len; i++)
                // for (int i = 0; i < cbuf.length; i++) WILL NOT WORK!!! since the very last time, cbuf is not updated
                    // String str = new String(cbuf, 0, len); This will work
                    // System.out.print(str);
                    System.out.print(cbuf[i]);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fr != null)
                try {
                    fr.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }

    @Test
    public void testFileWriter() throws IOException {
        /*
        * If file is not existed, create one
        *
        * Else, overwrite it if didn't set append to true for the FileWriter stream
        *  */

        File file = new File("./src/io/java/hello_out.txt");

        FileWriter fw = new FileWriter(file, false);

        fw.write("Hey, how are you?");

        fw.close();
    }

    @Test
    public void testFileReaderAndWrite(){
        FileReader fr = null;
        FileWriter fw = null;
        try {
            File srcFile = new File("./src/io/java/hello.txt");
            File destFile = new File("./src/io/java/hello_out.txt");

            fr = new FileReader(srcFile);
            fw = new FileWriter(destFile);

            char[] cbuf = new char[5];
            int len;

            while ((len = fr.read(cbuf)) != -1){
                fw.write(cbuf, 0, len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fw != null)
                try {
                    fw.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            if (fr != null)
                try {
                    fr.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }

    @Test
    public void testFileInputStream(){
        FileInputStream fis = null;
        FileOutputStream fos = null;
        try {
            File srcFile = new File("./src/io/java/pic.webp");
            File destFile = new File("./src/io/java/pic_out.webp");

            fis = new FileInputStream(srcFile);
            fos = new FileOutputStream(destFile);

            byte[] buffer = new byte[8];
            int len;
            while ((len = fis.read(buffer)) != -1){
                fos.write(buffer, 0, len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fos != null)
                try {
                    fos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            if (fis != null)
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }

    public void copyFile(String scrPath, String destPath){
        FileInputStream fis = null;
        FileOutputStream fos = null;
        try {
            File srcFile = new File(scrPath);
            File destFile = new File(destPath);

            fis = new FileInputStream(srcFile);
            fos = new FileOutputStream(destFile);

            byte[] buffer = new byte[1024];
            int len;
            while ((len = fis.read(buffer)) != -1){
                fos.write(buffer, 0, len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fos != null)
                try {
                    fos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            if (fis != null)
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }

    @Test
    public void testCopyFile(){
        long start = System.currentTimeMillis();
        copyFile("./src/io/java/pic.webp", "./src/io/java/pic_out.webp");
        long end = System.currentTimeMillis();
        System.out.println("Time used: " + (end-start) + "ms");
    }

     // buffer stream can greatly enhance the performance!
    public void bufferedCopyFile(String scrPath, String destPath){
        FileInputStream fis = null;
        FileOutputStream fos = null;
        BufferedInputStream bis = null;
        BufferedOutputStream bos = null;
        try {
            File srcFile = new File(scrPath);
            File destFile = new File(destPath);

            fis = new FileInputStream(srcFile);
            fos = new FileOutputStream(destFile);

            bis = new BufferedInputStream(fis);
            bos = new BufferedOutputStream(fos);

            byte[] buffer = new byte[1024];
            int len;
            while ((len = bis.read(buffer)) != -1)
                bos.write(buffer, 0, len);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 1. First close outer stream, then inner stream.
            // 2. However, the inner will automatically close when you close the outer ones
            if (bos != null)
                try {
                    bos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            if (bis != null)
                try {
                    bis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }

    @Test
    public void testWithOrWithoutBuffer(){
        long start = System.currentTimeMillis();
        copyFile("./src/io/java/pic.webp", "./src/io/java/pic_out.webp");
        long end = System.currentTimeMillis();
        System.out.println("Regular copy time used: " + (end-start) + "ms");

        start = System.currentTimeMillis();
        bufferedCopyFile("./src/io/java/pic.webp", "./src/io/java/pic_out.webp");
        end = System.currentTimeMillis();
        System.out.println("Buffered copy time used: " + (end-start) + "ms");
    }

    @Test
    public void testBufferedReaderAndWriter(){
        BufferedReader br = null;
        BufferedWriter bw = null;
        try {
            br = new BufferedReader(new FileReader(new File("./src/io/java/hello.txt")));
            bw = new BufferedWriter(new FileWriter(new File("./src/io/java/hello_out.txt")));

//            char[] cbuf = new char[1024];
//            int len;
//            while ((len = br.read(cbuf)) != -1)
//                bw.write(cbuf, 0, len);
            String data;
            while ((data = br.readLine()) != null)
                bw.write(data + "\n");
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (bw != null)
                try {
                    bw.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            if (br != null)
                try {
                    br.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }

    /*
    * InputStreamReader: bit stream --> byte stream: decode
    * OutputStreamReader: byte stream --> bit stream: encode
    *
    * Charset: ASCII --> us
    * ISO8859-1 --> euro
    * Unicode --> International 2 bits
    * UTF-8 --> 1 to 4 bits
    * */
    @Test
    public void testInputStreamReader(){
        InputStreamReader isr = null;
        OutputStreamWriter osw = null;
        try {
            FileInputStream fis = new FileInputStream("./src/io/java/hello.txt");
            isr = new InputStreamReader(fis, StandardCharsets.UTF_8);
            FileOutputStream fos = new FileOutputStream("./src/io/java/hello_gbk.txt");
            osw = new OutputStreamWriter(fos, StandardCharsets.ISO_8859_1);

            char[] cbuf = new char[20];
            int len;
            while ((len = isr.read(cbuf)) != -1)
                osw.write(cbuf, 0, len);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (osw != null)
                try {
                    osw.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            if (isr != null) {
                try {
                    isr.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    // redirect System.in and System.out
    // System.setIn(InputStream is)
    // System.setOut(PrintStream ps)
    /*
    * convert the keyboard input to UPPERCASE till exit is entered
    * Method1: Scanner, use next() to return a char[]
    * Method2: System.in --> (bit stream --> byte stream) --> BufferedReader's readLine()
    * */
    @Test
    public void testInputStreamOutputStream(){
        BufferedReader br = null;
        try {
            InputStreamReader isr = new InputStreamReader(System.in);
            br = new BufferedReader(isr);
            while (true){
                String data = null;
                data = br.readLine();

                if("e".equalsIgnoreCase(data) || "exit".equalsIgnoreCase(data)) // use "e".equalsIgnoreCase(data) instead of data.equalsIgnoreCase("e") to avoid NullPointerException
                    break;
                String upperCase = data.toUpperCase();
                System.out.println(upperCase);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (br != null) {
                try {
                    br.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    @Test
    public void testDataStream(){
        DataOutputStream dos = null;
        DataInputStream dis = null;
        try {
            dos = new DataOutputStream(new FileOutputStream("./src/io/java/hello_config.txt"));
            dis = new DataInputStream(new FileInputStream("./src/io/java/hello_config.txt"));
            dos.writeUTF("Name");
            dos.flush();
            dos.writeInt(30);
            dos.flush();
            dos.writeBoolean(true);
            dos.flush();

            String name = dis.readUTF();
            int age = dis.readInt();
            boolean isMale = dis.readBoolean();

            System.out.println("name = " + name);
            System.out.println("age = " + age);
            System.out.println("isMale = " + isMale);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (dis != null)
                try {
                    dis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            if (dos != null)
                try {
                    dos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }

    @Test
    public void testObjectStream(){
        // ObjectInputStream and ObjectOutputStream cannot serialize var modified by static and transient
        // Serializable interface
        // serialization is RMI(Remote Method Invoke), RMI is the base of JavaEE
        // let a class can be serializable --> implements either one of Serializable and Externalizable

        ObjectOutputStream oos = null;
        try {
            oos = new ObjectOutputStream(new FileOutputStream("./src/io/java/object.dat"));
            oos.writeObject(new String("I love Ocean"));
            oos.flush();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (oos != null) {
                try {
                    oos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }

        ObjectInputStream ois = null;
        try {
            ois = new ObjectInputStream(new FileInputStream("./src/io/java/object.dat"));

            Object obj = ois.readObject();
            String str = (String) obj;
            System.out.println(str);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } finally {
            if (ois != null) {
                try {
                    ois.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    @Test
    public void testRandomAccessFile(){
        RandomAccessFile raf1 = null;
        RandomAccessFile raf2 = null;
        try {
            // extends java.lang.Object and implements DataInput and DataOutput
            raf1 = new RandomAccessFile(new File("./src/io/java/pic.webp"), "r");
            raf2 = new RandomAccessFile(new File("./src/io/java/pic_ram_out.webp"), "rwd");
            // mode: r, rw, rwd, rws
            // rwd will sync the update
            // rws will sync the update and metadata

            byte[] buffer = new byte[1024];
            int len;
            while ((len = raf1.read(buffer)) != -1)
                raf2.write(buffer, 0, len);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (raf1 != null) {
                try {
                    raf1.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

            if (raf2 != null) {
                try {
                    raf2.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    /*
    * If RandomAccessFile is output stream ad the file is existed already, it will substitute the file from the beginning by default!!!
    * Now use RAF to realize insertion
    * */
    @Test
    public void testRandomAccessFileForInsertion(){
        RandomAccessFile raf = null;
        ByteArrayOutputStream baos = null; // use ByteArrayOutputStream instead of StringBuilder will avoid 乱码
        try {
            raf = new RandomAccessFile("./src/io/java/hey.txt", "rw");
            baos = new ByteArrayOutputStream();
            raf.seek(3);
            byte[] buffer = new byte[20];
            int len;
            // save the string after pos 3 and the pointer will be at the end
            while ((len = raf.read(buffer)) != -1){
                baos.write(buffer, 0, len);
            }
            // go back to pos 3
            raf.seek(3);
            raf.write("INSERTION".getBytes());
            raf.write(baos.toString().getBytes());
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (raf != null) {
                try {
                    raf.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}


/*
* To be serializable
* 1. implements Serializable interface
* 2. public static final long serialVersionUID
* 3. all the attributes of Person have to be Serializable (by default, all primitive types are serializable)
* */
class Person implements Serializable {
    public static final long serialVersionUID = 5436453642346L;

    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

#### NIO

Java NIO

- java.nio.channels.Channel
- |-----FileChannel: for local files
- |-----SocketChannel: for TCP client
- |-----ServerSocketChannel: for TCP server
- |-----DatagramChannel: for UDP

Path, Paths, and Files

Path is the upgraded File class

Path path = Paths.get("index.html");

- String toAbsolutePath()
- File toFile()

Files

- Path copy(Path src, Path dest, CopyOption ... how)
- Path createDirectory(Path path, FileAttribute<?> ... attr)
- Path createFile(Path path, FileAttribute<?> ... attr)
- void delete(Path path)
- void deleteIfExists(Path path)
- Path move(Path src, Path dest, CopyOption ... how)
- long size(Path path)
- ...

### Networking IO

InetAddress class --> IP

loopback Internet protocol (IP) address: 127.0.0.1 --> localhost

Port num: 16-bit int

- 0-1023: for pre-defined services
  - HTTP:80
  - FTP:21
  - Telnet:23
- 1024-49151: for apps or processes
  - mysql:3306
  - Tomcat:8080
  - Oracle:1521
- 49152-65535: for dynamic or private ports

The combination of port and IP --> Socket

```java
import org.testng.annotations.Test;

import java.net.InetAddress;
import java.net.UnknownHostException;

public class Networking {
    @Test
    public void testIP(){
        try {
            InetAddress inet1 = InetAddress.getByName("192.168.1.189");
            System.out.println(inet1);
            InetAddress inet2 = InetAddress.getByName("www.google.com");
            System.out.println(inet2);
            // get local ip
            InetAddress inet3 = InetAddress.getLocalHost();
            System.out.println(inet3);
            System.out.println(inet3.getHostName());
            System.out.println(inet3.getHostAddress());
        } catch (UnknownHostException e) {
            e.printStackTrace();
        }

    }
}
```

### Reflection (see another md file)

```java
import org.testng.annotations.Test;

import java.io.InputStream;
import java.lang.reflect.InvocationTargetException;
import java.util.Properties;

public class ReflectionTest {
    @Test
    public void testProps() throws Exception{
        Properties props = new Properties();
        ClassLoader classLoader = ReflectionTest.class.getClassLoader();
        InputStream is = classLoader.getResourceAsStream("./jdbc.properties"); // under the same dir of this class
        props.load(is);

        String user = props.getProperty("user");
        String password = props.getProperty("password");
        System.out.println("user = " + user + "\npassword = " + password);
    }

    @Test
    public void testInstance() throws NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException {
        Class clazz = PersonType.class;
        // To use this way, the class has to have a public non-param constructor
        // the javabean needs a public non-param constructor so that the subclass can call super() when it extends this runtime class
        PersonType object = (PersonType) clazz.getDeclaredConstructor().newInstance();
        System.out.println(object);
    }
}
 class PersonType{
    private String name;
    private int age;
    public PersonType(){}
    public PersonType(String name, int age){
        this.name = name;
        this.age = age;
    }

     public String getName() {
         return name;
     }

     public void setName(String name) {
         this.name = name;
     }

     public int getAge() {
         return age;
     }

     public void setAge(int age) {
         this.age = age;
     }

     @Override
     public String toString() {
         return "Person{" +
                 "name='" + name + '\'' +
                 ", age=" + age +
                 '}';
     }
 }
 ```

#### Dynamic Proxy

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class DynamicProxy {
    public static void main(String[] args) {
        SuperMan superMan = new SuperMan();
        Human proxyInstance = (Human) ProxyFactory.getProxyInstance(superMan);
        System.out.println(proxyInstance.getBelief());
        proxyInstance.eat("Spicy soup!");
    }
}

interface Human{
    String getBelief();
    void eat(String food);
}

class SuperMan implements Human{

    @Override
    public String getBelief() {
        return "I believe I can fly!";
    }

    @Override
    public void eat(String food) {
        System.out.println("I love " + food);
    }
}

class ProxyFactory{
    public static Object getProxyInstance(Object obj){
        MyInvocationHandler myInvocationHandler = new MyInvocationHandler();
        myInvocationHandler.bind(obj);
        return Proxy.newProxyInstance(obj.getClass().getClassLoader(), obj.getClass().getInterfaces(), myInvocationHandler);
    }
}

class MyInvocationHandler implements InvocationHandler{

    private Object obj;
    public void bind(Object obj){
        this.obj = obj;
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        Object returnValue = method.invoke(obj, args);
        return returnValue;
    }
}
```

#### AOP

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class DynamicProxy {
    public static void main(String[] args) {
        SuperMan superMan = new SuperMan();
        Human proxyInstance = (Human) ProxyFactory.getProxyInstance(superMan);
        System.out.println(proxyInstance.getBelief());
        proxyInstance.eat("Spicy soup!");
    }
}

class HumanUtil{
    public void method1(){
        System.out.println("method1");
    }

    public void method2(){
        System.out.println("method2");
    }
}

interface Human{
    String getBelief();
    void eat(String food);
}

class SuperMan implements Human{

    @Override
    public String getBelief() {
        return "I believe I can fly!";
    }

    @Override
    public void eat(String food) {
        System.out.println("I love " + food);
    }
}

class ProxyFactory{
    public static Object getProxyInstance(Object obj){
        MyInvocationHandler myInvocationHandler = new MyInvocationHandler();
        myInvocationHandler.bind(obj);
        return Proxy.newProxyInstance(obj.getClass().getClassLoader(), obj.getClass().getInterfaces(), myInvocationHandler);
    }
}

class MyInvocationHandler implements InvocationHandler{

    private Object obj;
    public void bind(Object obj){
        this.obj = obj;
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

        HumanUtil util = new HumanUtil();
        util.method1();

        Object returnValue = method.invoke(obj, args);

        util.method2();

        return returnValue;
    }
}
```

## Java 8 Features

### Lambda

```java
import org.testng.annotations.Test;

import java.net.InetAddress;
import java.util.Comparator;

public class LambdaTest {

    @Test
    public void test1(){
        java.lang.Runnable r1 = new java.lang.Runnable() {
            @Override
            public void run() {
                System.out.println("Yes!");
            }
        };

        r1.run();

        System.out.println("-------------------------------------");

        java.lang.Runnable r2 = () -> System.out.println("Yes!!!");
        r2.run();
    }

    @Test
    public void test2(){
        Comparator<Integer> com1 = new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return 0;
            }
        };
        int res1 = com1.compare(12,21);
        System.out.println(res1);
        System.out.println("-------------------------------------");
        // lambda
        Comparator<Integer> com2 = (o1,o2) -> Integer.compare(o1,o2);
        System.out.println(com2.compare(32,21));
        System.out.println("-------------------------------------");
        // method ref
        Comparator<Integer> com3 = Integer :: compare;
        System.out.println(com3.compare(56,51));
    }
}
```

### Functional Interface

@FunctionalInterface
The interface that only have 1 method
Lambda --> the instance of Functional interface

- `Consumer<T> --> void accept<T t>`
- `Supplier<T> --> T get()`
- `Function<T,R> --> R apply(T t)`
- `Predicate<T> --> boolean test(T t)`
- ...

```java
@Test
public void test3(){
    happyTime(400, money -> System.out.println("Bought a bottle of water..."));
}

public void happyTime(double money, Consumer<Double> consumer){
    consumer.accept(money);
}

@Test
public void test4(){
    List<String> list = Arrays.asList("NY", "FL", "TX", "CA", "NC");
    List<String> c = filterString(list, s -> s.contains("C"));
    System.out.println(c);
}

public List<String> filterString(List<String> list, Predicate<String> predicate){
    ArrayList<String> filterList = new ArrayList<>();
    for (String s : list) {
        if (predicate.test(s)){
            filterList.add(s);
        }
    }
    return filterList;
}
```

### Method Reference (constructor, array)

When the function passed to lambda has had an implementation, we can use method ref

```java
// param type and return type must be same!
@Test
public void test5(){
    // object :: non-static method
    // Consumer --> void accept(T t)
    // PrintStream --> void println(T t)
    PrintStream ps = System.out;
    Consumer<String> con1 = ps::println;
    con1.accept("New York");

    // Supplier --> T get()
    // Employee --> String getName()
    Employee emp = new Employee(1001, "Tom", 23, 5600);
    Supplier<String> sup = emp::getName;
    System.out.println(sup.get());

    // class :: static method --> t1 must be the caller, and the method has a param t2
    // Comparator --> int compare(T t1, T t2)
    // Integer --> int compare(T t1, T t2)
    Comparator<Integer> com1 = Integer::compare;
    System.out.println(com1.compare(1,2));

    // class :: method instance
    // Comparator --> int compare(T t1, T t2)
    // String -> int t1.compareTo(t2)
    Comparator<String> com2 = String::compareTo;
    System.out.println(com2.compare("Dam","Dan"));
}

@Test
public void test6(){
    // constructor ref
    // Supplier --> T get()
    // Employee --> default constructor
    Supplier<Employee> sup1 = Employee::new;

    // array ref
    // Function --> R apply(T t)
    //
    Function<Integer, Double[]> func1 = Double[]::new;
    Double[] arr1 = func1.apply(10);
    System.out.println(Arrays.toString(arr1));
}

class Employee{
    int id;
    public String name;
    public int age;
    public int salary;

    public Employee(int id, String name, int age, int salary){
        this.age = age;
        this.name = name;
        this.salary = salary;
    }

    public String getName() {
        return name;
    }
}
```

### Stream API

Stream API is powerful in terms of operations on Collection.

- Stream doesn't store data
- Stream doesn't change to original data
- stream only execute when the result is needed --> lazy mode

Create Stream --> Intermediate Ops --> Termination

```java
import org.testng.annotations.Test;

import java.util.*;
import java.util.function.ToIntFunction;
import java.util.stream.Collector;
import java.util.stream.Collectors;
import java.util.stream.IntStream;
import java.util.stream.Stream;

public class StreamTest {

    // 1. Creation
    @Test
    public void test(){
        List<String> list = new ArrayList<>();

        // through Collection
        Stream<String> stream = list.stream();
        Stream<String> parallelStream = list.parallelStream(); // read at a time, does not ensure the order

        // through Arrays --> static <T> Stream<T> stream(T[] array)
        int[] arr = new int[]{1,2,3,4,5,6};
        IntStream stream1 = Arrays.stream(arr);

        // through Stream's of()
        Stream<Integer> integerStream = Stream.of(1, 2, 3, 4, 5, 6, 7, 8);

        // infinite stream
        // public static<T> Stream<T> iterator(final T seed, final UnaryOperator<T> f)
        Stream.iterate(0, t -> t+2).limit(10).forEach(System.out::println);

        //public static<T> Stream<T> generate(Supplier<T> s)
        Stream.generate(Math::random).limit(10).forEach(System.out::println);
    }

    // 2. intermediate
    // filter(Predicate p) --> receive Lambda, filter out some elements
    // limit(n) --> cut off stream, the elements will not exceed a critical val
    // skip(n) --> skip element, return a stream discarding the first n elements, if there are less than n elements, return a empty stream
    // distinct() --> filter, use hashCode() equals() to get distinct elements
    @Test
    public void test2(){
        List<String> list = new ArrayList<>();
        Stream<String> stream = list.stream();
        stream.filter(e -> e.length() > 10).forEach(System.out::println);
        stream = list.stream();
        stream.limit(10).forEach(System.out::println);
        stream = list.stream();
        stream.skip(5).forEach(System.out::println);
        stream = list.stream();
        stream.distinct().forEach(System.out::println);
    }

    // map(Function f)
    // flatMap(Function f)
    @Test
    public void test3(){
        List<String> list = Arrays.asList("aa", "bb", "cc", "dd");
        list.stream().map(str -> str.toUpperCase()).forEach(System.out::println);

        list.stream().flatMap(StreamTest::fromStringToStream).forEach(System.out::println);
    }

    static Stream<Character> fromStringToStream(String str){
        List<Character> list = new ArrayList<>();
        for (Character c : str.toCharArray()){
            list.add(c);
        }
        return list.stream();
    }

    // sorted()
    // sorted(Comparator com)
    @Test
    public void test4(){
        // the class need to implement the corresponding interface
    }

    // 3. Termination
    // allMatch(Predicate p)
    // anyMatch(Predicate p)
    // noneMatch(Predicate p)
    // findFirst(Predicate p)
    // findAny(Predicate p)
    // count
    // max(Comparator c)
    // min(Comparator c)
    // forEach(Consumer c)
    @Test
    public void test5(){

    }

    // reduce(T identity, BinaryOperator b) --> combine the elements in stream repeatedly, return T
    // reduce(BinaryOperator b) --> combine the elements in stream repeatedly, return Optional<T>
    @Test
    public void test6(){
        List<Integer> list = Arrays.asList(1,2,3,4,5,6,7,8,9,0,10);
        Integer sum = list.stream().reduce(0, Integer::sum);
        System.out.println(sum);

        Optional<Integer> sum1 = list.stream().reduce(Integer::sum);
        System.out.println(sum1);
    }

    // collect(Collector c) --> convert stream to another form
    @Test
    public void test7(){
        List<Integer> list = Arrays.asList(1,2,3,4,5,6,7,8,9,0,10,1,1,2);
        Set<Integer> set = list.stream().collect(Collectors.toSet());
        System.out.println(set);

        Double collect = list.stream().collect(Collectors.averagingInt(i -> i * i));
        System.out.println(collect);
    }
}
```

### Optional class

```java
@Test
public void test8(){
    List<Integer> list = Arrays.asList(1,2,3,4,5,6,7);
    Optional<List<Integer>> optionalIntegers = Optional.of(list);
    System.out.println(optionalIntegers);
    
    list = null;
    // Optional<List<Integer>> optional = Optional.of(list); of(T t) --> t cannot be null
}
```

```java
// Avoid NullPointerException
public String getStr(String str){
    Optional<String> optionalStr = Optional.ofNullable(str);
    String res = optionalStr.orElse("You got a NULL!");
    return res;
}
```

optionalData.get() --> must make sure it's not null

## JDK 9-11

JDK 9

- Modules
- jShell
- private method in interfaces
- diamond operator
- try
- String --> from store as char[] to store as byte[]
- of()
- enhanced Stream API
- new HTTP client API
- javadoc support HTML5
- Deprecated API
- Nashorm
- dynamic compiler

JDK 10 --> local var type inference

JDK 11 --> ZGC

