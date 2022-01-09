# Maven

浏览器
  ｜
表示层-> 视图层-> HTML5/CSS/JS/JSP
  ｜  -> 控制层-> Servlet/Action/Handler
  ｜
业务逻辑层(Spring IOC AOP)
  ｜
持久化层(JDBC/DBUtils/Spring JDBCTemplate/Hibernate/MyBatis)
  ｜
  DB(MySQL/Oracle)


目前的问题：
1. 一个项目就是一个工程
  - 如果一个项目非常庞大，就不适合继续使用package来划分模块。最好是每一个模块对应一个工程，利于协作分工。借助于Maven就可以将一个项目拆分成多个工程，工程之间可以互相访问沟通。
2. 项目中需要的jar包必须手动复制粘贴到WEB-INF/lib目录下
 - 同样的jar包重复出现在不同的工程中。臃肿不轻巧。
 - Maven可以让jar包仅仅保存在仓库中，让有需要的工程引用这个文件接口，不需要把jar包复制过来。
3. jar包需要别人替我们准备好或到官网下载
  - 不同技术的官网提供jar包下载的形式是五花八门的。
  - 有些技术的官网就是通过Maven或SVN等专门的工具来提供下载。
  - 如果是以非正规方式下载的jar包，内容也可能不规范。
  - 借助Maven可以用一种规范的方式下载jar包。因为所有知名框架或第三方工具的jar包以及按照统一的规范存放在了Maven的中央仓库中。
4. 一个jar包的依赖需要自己手动加入到项目中

# Maven

clean --> compile --> compile-test --> package --> install --> deploy

- clean
- compile
- test-compile
- package: 打包
- install: 将打包得到的文件安装到仓库
- deploy: 将动态Web工程生成的war包复制到Servlet容器的指定目录下，使其可以运行

## POM (Project Object Model)

DOM(Document Object Model)

## Coordinates

GAV:

1. groupid: com.myteam.projectName
2. artifactid: moduleName
3. version: 1.0.0

```xml
<groupId> org.springframework</groupId>
<artifactId>spring-core</artifactId>
<version>4.0.0.RELEASE</version>
```

The path of the module will be: **org/springframework/spring-core/4.0.0.RELEASE/spring-core-4.0.0.RELEASE.jar**

## Repo

1. local repository: `~/.m2` by default

2. remote repository
   1. private(LAN) --> usually refer to **Nexus**
   2. central(on Internet, for worldwide Maven services)
   3. central mirror(to release the press of central)

First check of the module is in Nexus, if not, go to Maven Central to download to Nexus.

- What's in the repo?
  - plug-ins of Maven
  - jar of 3rd party tool or framework
  - the maven project built by ourselves

## Dependency

1. While compiling, Maven will try to find the dependencies in local repo, it will fail if they are not there. Use install command.
2. Scope
   1. compile --> main and test --> will be in the package
   2. test --> test --> will not be in the package
   3. provided --> main and test --> will not be in the package (only participate development, will not participate deploy and run, for the run stage, the server will provide the module)

## Lifecycle

1. Clean Lifecycle
   1. pre-clean
   2. clean
   3. post-clean
2. Default Lifecycle: core part, which responsiable for compile, test, package, install, and deploy...
   1. pre-default
   2. default
   3. post-default
3. Site Lifecycle: generate project report, site, and release site
   1. pre-site
   2. site
   3. post-site

We can do `mvn clean install site` for everything in one time.

## Plug-in and Goal

|Lifecycle|Goal|Plug-in|
|----|----|----|
|compile|compile|maven-compile-plugin|
|test-compile|testCompile|maven-compile-plugin|

1. The phases of lifecycle define the tasks to execute.
2. The phase and the goal of plug-in is corresponding.
3. Similar goal will use a specific plug-in to achieve.
4. We can see goals as commands to invoke the plug-in functions.

## Advanced Dependency

- Transitivity
  - test and provided scope's dependencies cannot transmit
  - A depends on X, B depends on A, then B will include X
- Exclude a dependency (only work in the current proj and all sub projs)
  
  - ```xml
    <exclusions>
        <exclusion>
            <groupId>commons-logging</gourpId>
            <artifactId>commons-logging</artifactId>
        </exclusion>
    </exclusions>
    ```

|A -->|B -->|C|
|----|----|----|
|log4j.1.2.1?|log4j.1.2.14|log4j.1.2.17|

If A depends on B and B depends on C, C use 17 and B use 14, what does A use by default?
14, since the path is nearer!

A -> B -> log4j.1.2.14 == 2
A -> B -> C -> log4j.1.2.17 == 3

But what if B and C have the same length of path?

Then it's decided by the order of the declaration in A, for example, if B is declared first, then A goes with B's log4j's version.

Version Managment

How to upgrade to several module from version 1.0.0 to 2.0.0?

- use properties tag
- then reference it at where you need it

```xml
<properties>
    <com.ace.spring.version>5.0.0.RELEASE</com.ace.spring.version>
</properties>

...
        <version>${com.ace.spring.version}</version>
...
```

## Inheritence

Case:
    A --> junit4.0
    B --> junit4.0
    C --> junit4.9

How to manage the version of all junit? (Notice that module in test scope cannot transit to others)

- Set the dependency in the higher level proj, and do not set it again in current level projs.
  - Create a Maven proj, here we call it S. (Notice: **Packaging tag** must be **POM**)
  - Let all sub projs depends on S
  - Delete all duplicated coordinates between proj and its super-proj S
  - Use properties tag to manage junit
  - Delete the junit version tag in all S' sub classes

```xml
<!-- This is the pom.xml in sub classes of S -->
<parent>
    <!-- Here is the super proj's coordinate -->
    <gourpId>com.ace.myProj.maven</groupId>
    <artifactId>Parent</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <!-- relative path starting from this proj -->
    <relativePath>../Parent/pom.xml</relativePath>
</parent>

```xml
<!-- This is the pom.xml of S -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.9</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

## Aggregation

You have to install the super proj before install the current one!

Can we have a one-stop solution?

```xml
<!-- Aggregation config -->
<modules>
    <module>../A</module>
    <module>../B</module>
    <module>../C</module>
</modules>
```

Now we can just `mvn install` under the super proj path

**Notice**: If the order doesn't matter since Maven will figure it out.

## Deploy

Usually, we manually put it in Tomcat's dir and use command `catalina run` to deploy.

But NOW!

```xml
<!--  Special config during building  -->
<build>
    <finalName>TheBigWeb</finalName>

    <!--  plug-ins needed during building  -->
    <plugins>
        <plugin>
            <!-- cargo is a company focus on booting Servlet containers -->
            <!-- https://mvnrepository.com/artifact/org.codehaus.cargo/cargo-core-uberjar -->
            <groupId>org.codehaus.cargo</groupId>
            <artifactId>cargo-core-uberjar</artifactId>
            <version>1.9.9</version>
            <!-- config for plugin -->
            <configuration>
                <!-- path for containers in curr sys -->
                <container>
                    <containerId>tomcat6x</containerId>
                    <home>~/Dev/testMavenCargoContainer</home>
                </container>
                <configuration>
                    <type>existing</type>
                    <home>~/Dev/testMavenCargoContainer</home>
                    <!-- if the Tomcat port is 8080 then we do not need this -->
                    <properties>
                        <cargo.servlet.port>8989</cargo.servlet.port>
                    </properties>
                </configuration>
            </configuration>

            <!-- config for when will this plugin run -->
            <executions>
                <execution>
                    <id>cargo-run</id>
                    <phase>install</phase>
                    <goals>
                        <goal>run</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

## [The Website!](https://mvnrepository.com/)

