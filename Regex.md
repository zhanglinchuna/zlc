#### 字符串开始或结尾或单词边界匹配：`^` `$`

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

#### 两项中取一项：`|`

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


#### 一次或多次匹配`.` `*` `+` `?`  频数匹配 `{M}` ` {M,N}` ` {M,}`

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



#### 匹配小括号中全部字符`()`

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



#### 匹配括号中一个字符`[]`

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

#### 转义字符`\`

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
