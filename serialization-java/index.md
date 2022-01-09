# Serialization


# Serialization and Deserialization in Java

### What
把Java对象转换为字节序列的过程-----------> 序列化 把字节序列恢复为Java对象的过程-----------> 反序列化

### Why

Java objects exist in the heap of JVM.
They will be gone once JVM stops running.

Thus, we have to store them in byte form for the purpose of persistency or tranferring to remote machines.

### Functions

1. Release the pressure of the memory and enhance data persistency.

2. Make the transportation of java objects on Internet possible.

### APIs

- java.io.Serializable
- java.io.Externalizable
    - writeExternal()
    - readExternal()
- java.io.ObjectOutputStream
- java.io.ObjectInputSream


### Code

[ref](https://cloud.tencent.com/developer/article/1877229?from=article.detail.1686925)










