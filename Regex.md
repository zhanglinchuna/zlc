### 一、正则基础语法

#### 1.1 字符串开始或结尾或单词边界匹配：`^` `$`

- `^`表示匹配字符串的开始位置  (例外：用在中括号中[ ] 时,可以理解为取反,表示不匹配括号中字符串)
- `$`表示匹配字符串的结束位置
- `\b`表示匹配字符串的开始位置 (`^`匹配字符串的开始，`\b`匹配单词的开始)
- `\B`表示以该字符串为子串但不是边界

<table>
    <tr>
        <th>正则表达式</th>
        <th>匹配的字符串</th>
    </tr>
    <tr>
        <td>^in</td>
        <td>任何以in开头的字符串</td>
    </tr>
    <tr>
        <td>[^0-9]</td>
        <td>任何非数字的字符串</td>
    </tr>
    <tr>
        <td>com$</td>
        <td>任何以com结尾的字符串</td>
    </tr>
    <tr>
        <td>the</td>
        <td>任何包含the的字符串</td>
    </tr>
    <tr>
        <td>\bthe</td>
        <td>任何以the开始的字符串</td>
    </tr>
    <tr>
        <td>\bthe\b</td>
        <td> 单词the</td>
    </tr>
    <tr>
        <td>\Bthe</td>
        <td>任何包含但不以the开始的字符串</td>
    </tr>
    <tr>
        <td>the\B</td>
        <td>任何包含但不以the结尾的字符串</td>
    </tr>
</table>

#### 1.2 两项中取一项：`|`

或者逻辑，从多个正则表达式选择其中一个

<table>
    <tr>
        <th>正则表达式</th>
        <th>匹配的字符串</th>
    </tr>
    <tr>
        <td>apple|orange</td>
        <td>apple,orange</td>
    </tr>
    <tr>
        <td>cpp|java|python</td>
        <td>cpp,java,python</td>
    </tr>
</table>


#### 1.3 一次或多次匹配`.` `*` `+` `?`  频数匹配 `{M}` ` {M,N}` ` {M,}`

- `.` 将匹配匹配除了换行符\n以外的<font style=color:red>任意字符</font>

- `*`将匹配其左边的正则表达式出现<font style=color:red>零次或多次</font>的情况。

- `+`将匹配其左边的正则表达式出现<font style=color:red>一次或多次</font>的情况。

- `？`将匹配其左边的正则表达式出现<font style=color:red>零次或一次</font>的情况。

- `{M}`将匹配其左边的正则表达式出现<font style=color:red>M次</font>的情况。

- `{M,N}`将匹配其左边的正则表达式出现<font style=color:red>M到N次</font>的情况。

- `{M,}`将匹配其左边的正则表达式出现<font style=color:red>M次或更多次</font>的情况。

<table>
    <tr>
        <th>正则表达式</th>
        <th>匹配的字符串</th>
    </tr>
    <tr>
        <td>dog.</td>
        <td>doga,dog5,dog(任意一个字符)</td>
    </tr>
    <tr>
        <td>dog?</td>
        <td>dog,do</td>
    </tr>
    <tr>
        <td>dog*</td>
        <td>do,dog,dogg,doggg,....do任意个g</td>
    </tr>
    <tr>
        <td>dog+</td>
        <td>dog,dogg,doggg,....dog任意个g</td>
    </tr>
    <tr>
        <td>dog{5}</td>
        <td>doggggg</td>
    </tr>
    <tr>
        <td>dog{2,3}</td>
        <td>dogg,doggg</td>
    </tr>
</table>


#### 1.4 匹配小括号中全部字符`()`

表示匹配括号中<font style=color:red>全部字符</font>

<table>
    <tr>
        <th>正则表达式</th>
        <th>匹配的字符串</th>
    </tr>
    <tr>
        <td>(dog)</td>
        <td>dog</td>
    </tr>
    <tr>
        <td>(dog)</td>
        <td>adogs</td>
    </tr>
</table>


#### 1.5 匹配括号中一个字符`[]`

`[]`用于匹配某些特定字符，区别于（.）匹配任意字符，匹配方括号中包含的<font style=color:red>任意字符</font>。

<table>
    <tr>
        <th>正则表达式</th>
        <th>匹配的字符串</th>
    </tr>
    <tr>
        <td>c[abcd]d</td>
        <td>cad,cbd,ccd,cdd</td>
    </tr>
    <tr>
        <td>[cp][jv][py]</td>
        <td>cjp,pjp,cvp,cvy,pjp,pjy</td>
    </tr>
</table>

`[-]`和`[^]`用于限定范围或否定

- 方括号中两个字符以-符号连接表示指定一个范围，在连接的字符之中
- 方括号中紧紧连接^符号表示不匹配给定字符集任一字符

<table>
    <tr>
        <th>正则表达式</th>
        <th>匹配的字符串</th>
    </tr>
    <tr>
        <td>[a-c][d-f]</td>
        <td>ad,ae,af,bd,be,bf,cd,ce,cf</td>
    </tr>
    <tr>
        <td>[^aeiou]</td>
        <td>一个非元音字符</td>
    </tr>
    <tr>
        <td>[^\t\n]</td>
        <td>不匹配制表符或换行符</td>
    </tr>
</table>

#### 1.6 转义字符`\`

基本符号匹配都需要转义字符   如 `\* ` 表示匹配`*`号

- \w  匹配任何字母数字下划线字符
- \W  非字母和数字

- \d   匹配任何十进制数字
- \D  非数字

- \s  匹配任意空格字符

<table>
    <tr>
        <th>正则表达式</th>
        <th>匹配的字符串</th>
    </tr>
    <tr>
        <td>\w+</td>
        <td>一串由字母数字组成的字符串</td>
    </tr>
    <tr>
        <td>\d{3}-\d{3}-d{4}</td>
        <td>美国电话号码形式，如800-555-1212</td>
    </tr>
    <tr>
        <td>\w+@\w+\.com</td>
        <td>xxx@yyy.com形式的电子邮件地址</td>
    </tr>
    <tr>
        <td>\s</td>
        <td>与[\n\r\t\v\f]相同，与\S相反</td>
    </tr>
</table>

### 二、java.util.regex API

#### 2.1 Pattern类

- private Pattern(String p, int f) 私有的构造方法，不能通过new创建对象
- public static Pattern compile(String regex) 参数传入正则表达式，调用静态方法compile创建Pattern对象
- public static Pattern compile(String regex, int flags) 通过静态方法compile创建Pattern对象，该方法允许设置多个Match Flag

```java
Pattern.compile("[a-z]",Pattern.CASE_INSENSITIVE); //启用不区分大小写的匹配
Pattern.compile("[a-z]", Pattern.COMMENTS); //允许空白和注释
```

- public String pattern()  返回字符串类型的正则表达式，也就是compile函数的regex参数值
- public Matcher matcher(CharSequence input) 为目标字符串input创建一个Matcher对象
- public int flags() 返回当前Pattern对象的Match Flag值，如：Pattern.COMMENTS，Pattern.CASE_INSENSITIVE的值
- public static boolean matches(String regex, CharSequence input) 通过指定的正则表达式regex对input进行正则匹配
- public String[] split(CharSequence input) 通过正则表达式对input进行分割
- public String[] split(CharSequence input, int limit) 通过正则表达式对input进行分割，limit参数指明分割的段数
- public static String quote(String s) 将字符串s转换为正则字面量，在使用quote()方法之后，原有的字符串s变成了\Qs\E的样式，\Q 代表字面内容的开始，\E 代表字面内容的结束


#### 2.2 Matcher类

- public Pattern pattern() 返回创建Matcher对象的Pattern对象
- public MatchResult toMatchResult() 将匹配结果以MatchResult的形式返回
- public Matcher usePattern(Pattern newPattern) 修改Matcher对象的Pattern，用以进行新的模式匹配
- public Matcher reset() 重置匹配器的状态
- public Matcher reset(CharSequence input) 重置匹配器的状态，重置目标字符串的值为input
- public int start() 返回当前匹配到的字符串在原目标字符串中的起始索引位置
- public int start(int group) 返回当前匹配到的字符串中group组在目标字符串的起始索引位置
- public int end() 返回当前匹配的字符串的最后一个字符在原目标字符串中的offset（偏移量）
- public int end(int group) 返回当前匹配的字符串中group组的最后一个字符在原目标字符串中的offset（偏移量）
- public String group() 返回匹配到的字符串，结合find函数使用
- public String group(int group) 返回匹配到的字符串中的group组的字符串
- public String group(String name) 返回被named-capturing组捕获的字符串
- public int groupCount() 返回当前Matcher对象捕获的组的个数
- public boolean matches() 将整个目标字符串与正则表达式进行匹配，只有完全匹配才能返回true，否则false
- public boolean find() 对目标字符串进行正则匹配，通过while可以多次执行find方法，获取多次的匹配结果，代码编写方式类似于iterator.next()
- public boolean find(int start) 在制定的索引位置对目标字符串进行正则匹配
- public boolean lookingAt() 目标字符串的起始字符串与正则表达式匹配返回true，否则返回false
- public static String quoteReplacement(String s) 返回字符串s字面意义的替代字符串
- public Matcher appendReplacement(StringBuffer sb, String replacement) 向sb中追加replacement字符串，replacement字符串中可以包含匹配器中的分组参数，如1，2
- public StringBuffer appendTail(StringBuffer sb) 将Matcher匹配后的尾部字符串追加至sb中

```java
Pattern p = Pattern.compile("cat");
Matcher m = p.matcher("one cat two cats in the yard");
StringBuffer sb = new StringBuffer();
while (m.find()) {
    m.appendReplacement(sb, "dog");
}
m.appendTail(sb);
//执行结果：one dog two dogs in the yard
```
- public String replaceAll(String replacement) 将目标字符串中所有满足正则匹配的字符串替换为replacement
- public String replaceFirst(String replacement) 将目标字符串中第一个满足正则匹配的字符串替换为replacement
- public Matcher region(int start, int end) 设置目标字符串的匹配范围
- public int regionStart() 返回匹配器区域的起始点索引位置
- public int regionEnd() 返回匹配器区域的结束点索引位置
- public boolean hasTransparentBounds() TransparentBounds标志位：查询TransparentBounds标志位true|false，此标志位默认为false。如果匹配范围不是整个目标字符串，而是一部分，那么如果此标志位设为true的话，则允许顺序环视、逆序环视以及单词分界符超越匹配范围边界的设置，匹配目标字符串的其他部分，也就是可以稍微有越界行为。可以通过useTransparentBounds()进行修改设置
- public Matcher useTransparentBounds(boolean b) 设置TransparentBounds标志位的值true|false

```java
// \b 匹配一个字边界，即字与空格间的位置。例如，“er/b”匹配“never”中的“er”，但不匹配“verb”中的“er”
String regex = "\\bcar\\b";
String text = "Madagascar is best seen by car or bike.";
Matcher m = Pattern.compile(regex).matcher(text);
// TransparentBounds = false，region区域从index=7开始，Madagascar也就是从car开始，匹配器无法感知region区域外的字符，因此第一个car被匹配。
m.region(7, text.length());
m.useTransparentBounds(false);
m.find();
System.out.println("Matches starting at character " + m.start());
m.reset();
// TransparentBounds = true，region区域从index=7开始，Madagascar也就是从car开始，匹配器可以感知region区域外的字符，因此第一个car不被匹配。
m.useTransparentBounds(true);
m.find();
System.out.println("Matches starting at character " + m.start());

/**
 * 执行结果：
 *   Matches starting at character 7
 *   Matches starting at character 27
 */
```
- public boolean hasAnchoringBounds() AnchoringBounds标志位：查询AnchoringBounds标志位的值，此标志位默认为true。在应用正则表达式的时候，我们可以指定目标字符串的检索范围，也就是说在目标字符串的子字符串中应用正则表达式。但此时会有一个问题，那就是 ^ 和 $ 应该匹配整个字符串的开头和结尾呢？ 还是检索范围的起始和结束位置呢？Java 为我们提供了足够的灵活性，我们可以通过下面的方法来查看和设置，默认值是匹配检索范围的起始和结束位置
    - public Matcher useAnchoringBounds(boolean b) 设置AnchoringBounds标志位的值true|false
    - public boolean hitEnd()
    - public boolean requireEnd()
    - boolean search(int from)
    - boolean match(int from, int anchor)
    - int getTextLength() 返回目标字符串的长度
    - CharSequence getSubSequence(int beginIndex, int endIndex) 获取目标字符串的子字符串
    - char charAt(int i) 返回目标字符串中索引为i的字符
