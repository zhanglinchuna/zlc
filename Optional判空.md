### Optional类的创建

- 构造方法是私有，不能new来创建
- Optional的三个静态方法来创建对象，【empty()、of()、ofNullable()】

```java
// 1、创建一个包装对象值为空的Optional对象
Optional<String> optStr = Optional.empty();
// 2、该方法通过一个非 null 的 value 来构造一个 Optional，返回的 Optional 包含了 value 这个值。对于该方法，传入的参数不能为 null，否则便会抛出 NullPointerException。
Optional<String> optStr1 = Optional.of("optional");
// 3、该方法和 of 方法的区别在于，传入的参数可以为 null
Optional<String> optStr2 = Optional.ofNullable(null);
```

### Optional类的使用

- get方法
直接放回包装对象的实际值，为null抛出异常
