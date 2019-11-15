### String类所有方法使用详情

#### 获取字符串中某个字符

- char charAt(int index); //返回指定索引处的单个字符
- int	codePointAt(int index); //返回指定索引处字符的ascii码
- int	codePointBefore(int index) //返回指定索引前一个字符的ascii码
- int	codePointCount(int beginIndex, int endIndex); //返回指定索引范围内的字符ascii码个数

```java
String str = "abcdef";

str.charAt(1);  // 结果：b
str.codePointAt(0); // 结果：97
str.codePointBefore(1); // 结果：97
str.codePointCount(0, 2); // 结果：2
```

#### 字符串之间比较大小

- int	compareTo(String anotherString);  //按字典顺序比较两个字符串
- int	compareToIgnoreCase(String str);  //按字典顺序比较两个字符串，不考虑大小写

```java
"a".compareTo("A"); // 结果：32， "a"的ascii码：97，"A"的ascii码：65
"abc".compareToIgnoreCase("ABC"); //结果：0
```

#### 字符串拼接

- String concat(String str); //将指定字符串连接到此字符串的结尾

```java
"hello".concat(" world"); //结果：hello world
```

#### 是否包含指定字符串

- boolean	contains(CharSequence s); //字符串包含指定的char值序列时，返回true

```java
"abcdef".contains("cde"); //结果：true
```
