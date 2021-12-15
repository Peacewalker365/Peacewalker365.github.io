# Java Note

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
#### Important

1. You can declare multiple classes in a source code file, but you can only have 1 public class at most in it.

2. if declare a public class, then the source code file name has to be the same name of the class!

3. Depending on the number of class you have in a source file, the compile process may generate 1 or more bytecode files.

### Comment
1. //
2. /* */
3. ```java
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

看到226


