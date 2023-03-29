## Java
---
### 1. Java工具（javac 和 java）
```java
javac helloword.java（编译，生成class文件）
java helloword（运行，没c没后缀）
```

### 2. 环境变量（目的是全局）
```java
1.系统变量新建：
变量名：JAVA_HOME
变量值：jdk的目录（不带bin）

2.path中新建：%JAVA_HOME%\bin（用两个百%引用，后面再加bin）
```
### 3. Java版本
```java
Java SE：标准版，用于桌面应用开发（是基础）
Java ME：小型版，嵌入式和小型移动（没用了）
Java EE：企业版，用于Web方向的网站开发
```
### 4. JDK 和 JRE（JDK 包含 JRE，而 JRE 又包含了 JVM）
```java
JDK 工具：
JVM：java虚拟机，真正运行java的地方
核心类库：java已经装好的方法
开发工具：javac、java、jdb、jhat

JRE 工具：
java的运行环境
```