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

```java
// 直接放回包装对象的实际值，为null抛出异常
String opt = Optional.of("optional").get();  // optional
```

- isPresent方法

```java
// 判断包装对象是否为空
boolean b = Optional.ofNullable(null).isPresent();  // false
```

- ifPresent方法

```java
// 不为空时进行操作，为空时什么也不做
Optional.ofNullable("abc").ifPresent(e -> {
    System.out.println(e);
});
```

- filter方法

```java
// 用于对Optional对象进行过滤
Optional.ofNullable(student).filter( u -> u.getAge() > 18).ifPresent(u ->  System.out.println("年龄大于18的学生"));
```

- map方法

map()方法的参数为Function（函数式接口）对象，map()方法将Optional中的包装对象用Function函数进行运算，并包装成新的Optional对象

```java
// 在 Java 8 之前，任何访问对象方法或属性的调用都可能导致 NullPointerException，为了确保不触发异常，就得在访问每一个值之前对其进行明确地检查
if (user != null) {
    Address address = user.getAddress();
    if (address != null) {
        Country country = address.getCountry();
        if (country != null) {
            String isocode = country.getIsocode();
            return isocode;
        }
    }
}
// 使用Optional.map方法简化非空判断过程
Optional<String> isocode = Optional
        .ofNullable(user)    // Optional<User>
        .map(user -> user.getAddress())    // Optional<Address>
        .map(address -> address.getCountry())   // Optional<Country>
        .map(isocode -> country.getIsocode());  // Optional<String>
        
System.out.println("isocode is: " + isocode.get());
```

- flatMap方法

map()方法不同的是，入参Function函数的返回值类型为Optional<U>类型，而不是U类型，这样flatMap()能将一个二维的Optional对象映射成一个一维的对象

```java
Optional.ofNullable(user).flatMap(u -> Optional.ofNullable(u.getAddress()));
```

- orElse方法

```java
// 如果包装对象值非空，返回包装对象值，否则返回入参other的值（默认值）
Optional.ofNullable(user).map(u -> u.getAddress()).orElse("Unkown");
```

- orElseGet方法

```java
// orElseGet()方法与orElse()方法类似，区别在于orElseGet()方法的入参为一个Supplier对象，用Supplier对象的get()方法的返回值作为默认值。
Optional.ofNullable(user).map(u -> u.getAddress()).orElseGet(() -> "Unkown");  
```

- orElseThrow方法

```java
// 用于包装对象值为空时需要抛出特定异常的场景。
Optional.ofNullable(user).map(u -> u.getAddress()).orElseThrow(() -> new RuntimeException("Unkown"));
```
