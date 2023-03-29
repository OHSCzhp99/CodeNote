## Java
---
### 1. Math
```java
1. Math：（Math.调用）
① abs() 绝对值
② ceil() 向上取整   floor() 向下取整
④ round() 四舍五入
⑤ max(a,b) 最大值  min(a,b) 最小值
⑥ pow(double a，double b) a的b次幂
⑦ sqrt() 开根号
⑧ random() 取得一个[0, 1)范围内的随机数
```

### 2. System（系统相关）
```java
1. System：（时间原点：1970.1.1，北京+8小时）
① System.exit(0)  停止虚拟机，结束程序
② System.currentTimeMillis()  返回当前系统时间的毫秒值
③ System.arraycopy(原数组，起始索引，目的地数组，起始索引，拷贝个数)  数组拷贝
```

### 3. Runtime（虚拟机相关）
```java
1. Runtime：
要先获取方法：Runtime r = Runtime.getRuntime()
① r.exit(0)   停止虚拟机，结束程序
② r.availableProcessors()   获取CPU线程数
③ r.maxMemory()  总内存大小，单位byte字节
④ r.totalMemory()  已经获取内存大小
⑤ r.freeMemory()  剩余内存大小

⑥ r.exec()  运行cmd命令（可用来关机操作）
r.exec("shutdown -s -t 3600")   
（shutdown 关机，-s默认1分钟，-s -t 指定时间，-a 取消关机操作，-r 关机并重启）
```

### 4. Object（对象顶级父类）
```java
1. Object：（对象顶级父类）
要先new对象：Object obj = new Object()；
① toString()  返回对象的字符串表示形式，配合对象里面的toString重写方法
② s1.equals(s2)  判断两个对象是否相等，如果对象s1里没有重写equals方法，则比较的是地址值是否相等，没有意义，
我们一般重写equals和哈希，才能比较对象的属性值

③ clone()  默认是浅克隆，浅克隆的话引用类型的地址会直接拷贝过来，如果地址值发生改变，克隆后的数值也会跟着改变，
而深克隆是引用类型的重新创建一个新地址来存放，就不会跟着改变了，但是深克隆自己写麻烦，要放别人的写好代码包

---- Objects 工具类（java写好的比较对象前先判断是否为空）
直接Objects.调用就行了
① Objects.equals(s1,s2)  先做非空判断，在比较两个对象
② Objects.isNull(s1)  判断数组是否为空，是返回true，否返回false
③ Objects.nonNull(s1)  跟上面反过来
```

### 5. BigInteger 和 BigDecimal（大整数和大浮点数）
```java
1. BigInteger：
① BigInteger b1 = new BigInteger("1000")  直接创建一个指定字符串的数
② BigInteger b2 = new BigInteger("100", 2)  后面带数字表示转成该进制，为4

③ BigInteger.valueOf(1000)  用静态方法创建，并且会做优化，-16到16会节约内存，
一般数据不是很大就用这个静态方法，跟上面①的方法差不多，只是只能取到long的范围，并且括号里的是数字不是字符串

④ 几个内部定义的常量：
BigInteger.ZERO
BigInteger.ONE
BigInteger.TEN

⑤ 大整形转换基本类型：
int num4 = bigNum.intValue(); （转int，还有long、folat...）
String num2 = bigNum.toString();  （转字符串）
String num3 = bigNum.toString(radix);（转指定进制的字符串）

⑤ BigInteger一旦进行方法计算，不会改变值，而是产生一个新的BigInteger对象
b1.add(b2)  加法
subtract()  减法
multiply()  乘法
divide()  除法，获取商
divideAndRemainder()  除法，获取商和余数，可用数组[0]和[1]拿到结果
equals()  比较两个值
pow(2)  2次方
gcd(2) 将两个数取最大公约数 
abs() 取绝对值
max()   获取较大值，返回的是大的那个值
compareTo() 比较较大值，返回的是 1大于 0等于 -1小于
intvalue()  转换成整数，还有doublevalue等等
---------------------------------
2. BigDecimal：
① BigDecimal b1 = new BigDecimal("10")  直接创建一个指定字符串的数
② BigDecimal.valueOf(10)   用静态方法创建，0-10会做优化

③ 加减乘除是跟上面一样的，还有个特殊的
divide(5，2，RoundingMode.HALF_UP)  除5，保留2位，四舍五入
```