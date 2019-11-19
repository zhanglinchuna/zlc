### String类所有方法使用详情

#### 获取字符串中某个字符

- char charAt(int index); //返回指定索引处的单个字符
- int	codePointAt(int index); //返回指定索引处字符的ascii码
- int	codePointBefore(int index) //返回指定索引前一个字符的ascii码
- int	codePointCount(int beginIndex, int endIndex); //返回指定索引范围内的字符ascii码个数
- int	offsetByCodePoints(int index, int codePointOffset); //  返回此String中从给定的index处偏移codePointOffset个代码点的索引

```java
String str = "abcdef";

str.charAt(1);  //结果：b
str.codePointAt(0); //结果：97
str.codePointBefore(1); //结果：97
str.codePointCount(0, 2); //结果：2
str.offsetByCodePoints(2,3); //结果：5
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

#### 字符串之间比较是否相等

- boolean	equals(Object anObject); //将此字符串与指定的对象比较。
- boolean	equalsIgnoreCase(String anotherString); //将此 String 与另一个 String 比较，不考虑大小写。
- boolean	contentEquals(CharSequence cs); //将此字符串与指定的 CharSequence 比较
- boolean	contentEquals(StringBuffer sb); //将此字符串与指定的 StringBuffer 比较
- boolean	regionMatches(int toffset, String other, int ooffset, int len); //两个字符串区域是否相等
- boolean	regionMatches(boolean ignoreCase, int toffset, String other, int ooffset, int len); //两个字符串区域是否相等

```java
String s1="123";
String s2=new String("123");
StringBuilder sb=new StringBuilder("123");

s1.equals(s2); //结果：true
s1.contentEquals(s2); //结果：true
s1.equals(sb); //结果：false
s1.contentEquals(sb); //结果：true

 /**
  * 参数：
  * ignoreCase — 如果为 true，则比较字符时忽略大小写。
  * toffset — 此字符串中子区域的起始偏移量。
  * other — 字符串参数。
  * ooffset — 字符串参数中子区域的起始偏移量。
  * len — 要比较的字符数。
  */
String str1 = "abcdef";
String str2 = "cd";
// str1.substring(2, 2+str2.length()).equals(str2.substring(0, str2.length()))简写如下
str1.regionMatches(2, str2, 0, str2.lenght()); //结果：true
str1.regionMatches(true, 2, "cD", 0, 2); //结果：true
```

> equals和contentEquals区别：
>
> equals：不仅比较这个字符串的内容还检查另一个被比较的对象是否是String类型，如果不是String类型则返回false
>
> contentEquals：只比较两者的内容是否相同，不检查被比较对象的类型

#### 字符数组转成字符串

- static String	copyValueOf(char[] data); //将字符数组转成字符串
- static String	copyValueOf(char[] data, int offset, int count); //将指定索引处的字符数组转成字符串

```java
String.copyValueOf(new char[]{'a','b','c'}); //结果：abc
String.copyValueOf(new char[]{'a','b','c'},0,2); //结果：ab
```

#### 字符串转成字符数组

- byte[] getBytes(); //使用平台的默认字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中
- byte[] getBytes(Charset charset); //使用给定的 charset 将此 String 编码到 byte 序列，并将结果存储到新的 byte 数组
- byte[] getBytes(String charsetName); //使用指定的字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中
- char[]	toCharArray(); //将此字符串转换为一个新的字符数组
- void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin); //将字符从此字符串复制到目标字符数组
- CharSequence	subSequence(int beginIndex, int endIndex); //返回一个新的字符序列，它是此序列的一个子序列

```java
"abc".getBytes(); //['a','b','c']
byte[] by = "abc".getBytes(Charset.defaultCharset());//['97','98','99']
/**
 * 参数：
 * srcBegin - 字符串中要复制的第一个字符的索引。
 * srcEnd - 字符串中要复制的最后一个字符之后的索引。
 * dst - 目标数组。
 * dstBegin - 目标数组中的起始偏移量。
 */
 char[] c = new char[5];
"abc".getChars(0,2,c,2); //['','','a','b','']
```


#### 是否以指定字符串开头或结尾

- boolean	startsWith(String prefix); //测试此字符串是否以指定的前缀开始
- boolean	startsWith(String prefix, int toffset); //测试此字符串从指定索引开始的子字符串是否以指定前缀开始
- boolean	endsWith(String suffix); //字符串是否以指定的后缀结束

```java
String str = "abc.txt";
str.endsWith(".txt"); //结果：true
```

#### 字符串格式化

- static String	format(Locale l, String format, Object... args); //使用指定的语言环境、格式字符串和参数返回一个格式化字符串。
- static String	format(String format, Object... args); //使用指定的格式字符串和参数返回一个格式化字符串。

#### 字符第一次出现位置

- int	indexOf(int ch); //返回指定字符在此字符串中第一次出现处的索引
- int	indexOf(int ch, int fromIndex); //返回在此字符串中第一次出现指定字符处的索引，从指定的索引开始搜索
- int	indexOf(String str); //返回指定子字符串在此字符串中第一次出现处的索引
- int	indexOf(String str, int fromIndex); //返回指定子字符串在此字符串中第一次出现处的索引，从指定的索引开始
- int	lastIndexOf(int ch); //返回指定字符在此字符串中最后一次出现处的索引
- int	lastIndexOf(int ch, int fromIndex); //返回指定字符在此字符串中最后一次出现处的索引，从指定的索引处开始进行反向搜索
- int	lastIndexOf(String str) //返回指定子字符串在此字符串中最右边出现处的索引
- int	lastIndexOf(String str, int fromIndex); //返回指定子字符串在此字符串中最后一次出现处的索引，从指定的索引开始反向搜索

```java
String str = "abcba";
str.indexOf("b"); //结果：1
str.lastIndexOf("b"); //结果：3

str.indexOf("b",2); //结果：3
str.lastIndexOf("b",2); //结果：1
```

#### 字符串长度

- int	length(); //返回此字符串的长度
- boolean	isEmpty(); //当且仅当 length() 为 0 时返回 true

#### 正则匹配字符串

- boolean	matches(String regex); //字符串是否匹配给定的正则表达式
- String[]	split(String regex); //根据给定正则表达式的匹配拆分此字符串
- String[]	split(String regex, int limit); //根据匹配给定的正则表达式来拆分此字符串

#### 字符串替换

- String	replace(char oldChar, char newChar); //返回一个新的字符串，它是通过用newChar替换此字符串中出现的所有oldChar得到的
- String	replace(CharSequence target, CharSequence replacement); //使用指定的字面值替换序列替换此字符串所有匹配字面值目标序列的子字符串
- String	replaceAll(String regex, String replacement); //使用给定的replacement替换此字符串所有匹配给定的正则表达式的子字符串
- String	replaceFirst(String regex, String replacement); //使用给定的replacement替换此字符串匹配给定的正则表达式的第一个子字符串

```java
String str = "66ccffbb";
str.replace('b', 'a'); //结果：66ccffaa
str.replaceAll("bb","cc"); //结果：66ccffcc
str.replaceFirst("c","e"); //结果：66ecffbb
```

#### 字符串截取

- String	substring(int beginIndex); //返回一个新的字符串，它是此字符串的一个子字符串
- String	substring(int beginIndex, int endIndex); //返回一个新字符串，它是此字符串的一个子字符串
- String	trim(); //忽略字符串前面和后面空格

```java
String str = "abcdef";
str.substring(4); //结果：ef
str.substring(3, 6); //结果：def
String trim = "    abcdef   ";
trim.trim(); //结果：abcdef
```

#### 字符串大小写转换

- String	toLowerCase(); //将此字符串转换为小写
- String	toLowerCase(Locale locale); //使用给定 Locale 的规则将此 String 中的所有字符都转换为小写
- String	toUpperCase(); //将此字符串转换为大写
- String	toUpperCase(Locale locale); ////使用给定 Locale 的规则将此 String 中的所有字符都转换为大写

#### 其它类型转String

- static String	valueOf(boolean b); //返回 boolean 参数的字符串表示形式
- static String	valueOf(char c); //返回 char 参数的字符串表示形式
- static String	valueOf(char[] data); //返回 char 数组参数的字符串表示形式
- static String	valueOf(char[] data, int offset, int count); //返回 char 数组参数的特定子数组的字符串表示形式
- static String	valueOf(double d); //返回 double 参数的字符串表示形式
- static String	valueOf(float f); //返回 float 参数的字符串表示形式
- static String	valueOf(int i); //返回 int 参数的字符串表示形式
- static String	valueOf(long l); //返回 long 参数的字符串表示形式
- static String	valueOf(Object obj); //返回 Object 参数的字符串表示形式
