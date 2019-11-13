### 一、概述

Stream操作简称流操作，这里的流与IO流毫无关系，这里的流指的是流式操作，就是流水线操作。

流不是集合，它不对数据做保存，只是最数据进行算法处理，比如最大值，最小值，排序等操作。Stream会在数据源内部隐式的遍历进行处理。Stream会并行遍历数据，将数据分成若干段，同时进行处理，最终汇总结果一起输出。

Stream 就如同一个迭代器（Iterator），单向，不可往复，数据只能遍历一次，遍历过一次后即用尽了，就好比流水从面前流过，一去不复返。

### 二、特点

首先对stream的操作可以分为两类，中间操作(intermediate operations)和结束操作(terminal operations):

- 中间操作总是会惰式执行，调用中间操作只会生成一个标记了该操作的新stream。
  - 惰式执行：stream上的操作并不会立即执行，只有等到用户真正需要结果的时候才会执行。
- 可消费性：stream只能被“消费”一次，一旦遍历过就会失效，就像容器的迭代器那样，想要再次遍历必须重新生成。
- 结束操作会触发实际计算，计算发生时会把所有中间操作积攒的操作以pipeline的方式执行，这样可以减少迭代次数。计算完成之后stream就会失效。
- 无存储。stream不是一种数据结构，它只是某种数据源的一个视图，数据源可以是一个数组，Java容器或I/O channel等。
- 为函数式编程而生。对stream的任何修改都不会修改背后的数据源，比如对stream执行过滤操作并不会删除被过滤的元素，而是会产生一个不包含被过滤元素的新stream。

### 三、创建流

```java
public class StreamTest {
    public static void createStream() {
        // 通过数组生成流
        int[] ints = {1,2,3,4,5,6};
        IntStream s1 = Arrays.stream(ints);
        Stream s2 = Stream.of("111","222","333");
        String[] ss = {"123","321","456","654"};
        Stream<String> s3 = Arrays.stream(ss);
        // 通过构建器生成流
        Stream<Object> s4 = Stream.builder().add("123").add("321").add("444").add("@21").build();
        // 通过集合生成流
        List<String> lists = Arrays.asList("123","321","1212","32321");
        Stream<String> s5 = lists.stream();
        Stream<String> s6 = lists.parallelStream();// 并行流
        // 创建空流
        Stream<String> s7  = Stream.empty();
        // 创建无限流
        Stream.generate(()->"number"+new Random().nextInt()).limit(100).forEach(System.out::println);
        Stream.iterate(0,n -> n+2).limit(10).forEach(System.out::println);
    }
}
```

### 四、流中间操作

流中间操作指的是该操作的返回值仍然是流。

- filter：过滤流，过滤流中的元素，返回一个符合条件的Stream
- map：转换流，将一种类型的流转换为另外一种流。（mapToInt、mapToLong、mapToDouble 返回int、long、double基本类型对应的Stream）
- flatMap：简单的说，就是一个或多个流合并成一个新流。（flatMapToInt、flatMapToLong、flatMapToDouble 返回对应的IntStream、LongStream、DoubleStream流。）
- distinct：返回去重的Stream。
- sorted：返回一个排序的Stream。
- peek：主要用来查看流中元素的数据状态。
- limit：返回前n个元素数据组成的Stream。属于短路操作
- skip：返回第n个元素后面数据组成的Stream。


#### 4.1 filter

过滤流，过滤流中的元素，返回一个符合条件的Stream

```java
public class StreamTest {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("123","456","789","1101","asdaa","3e3e3e","2321eew","212121121");
        filterTest(list);
    }
    public static void filterTest(List<String> list){
        list.stream()
                .filter(e -> e.length() > 4 && e.length()<7)// 过滤掉长度小于等于4,大于等于7的元素
                .peek(System.out::println)// 查阅中间流结果
                .collect(Collectors.toList());// 将流转回集合类型
    }
}
```
```
asdaa
3e3e3e
```
#### 4.2 map

转换流，将一种类型的流转换为另外一种流，需要针对流中的每个元素执行，然后将执行的结果组成新的流返回。

```java
public class StreamTest {
    public static void main(String[] args) {
        List<String> list01 = Arrays.asList("afd", "fdf", "Xes");
        List list = mapToStrTest(list01);
        System.out.println("----------");
        List<Person> list02 = Arrays.asList(new Person(20, "zs"), new Person(22, "ls"), new Person(25, "ww"));
        List<Map> maps = mapToObjTest(list02);
    }

    public static List mapToStrTest(List<String> list){
        return list.stream().map(e -> e.toUpperCase())//将字符串转成大写
                .peek(System.out::println)// 查阅中间流结果
                .collect(Collectors.toList());
    }

    public static List<Map> mapToObjTest(List<Person> list) {
        return list.stream().map(e -> {
            Map<String, Object> map = new HashMap<>();
            map.put(e.getName(), e.getAge());
            return map; //返回一个对象
        }).peek(System.out::println)// 查阅中间流结果
          .collect(Collectors.toList());
    }
}
```
```
AFD
FDF
XES
----------
{zs=20}
{ls=22}
{ww=25}
```
#### 4.3 flatMap

flatMap针对流中的每一个元素进行操作，将结果组成新流，当流中元素包含子元素的时候，通过该方法，获取到元素的子元素，并将子元素组成新流返回。

```java
public class StreamTest {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("123","456","789","1101","asdaa","3e3e3e","2321eew","212121121");
        flatMap(list);
    }
    public static void flatMap(List<String> list){
        list.stream()
                .filter(e -> e.length()>5 && e.length()<7)//过滤，获取长度大于5且小于7的字符
                .peek(System.out::println)
                .map(e -> e.split(""))// 将每个字符串元素分解为字符数组
                .flatMap(e -> Arrays.stream(e))//将每个字符数组并转化为流
                .peek(System.out::println)
                .collect(Collectors.toList());
    }
}
```
```
3e3e3e
3
e
3
e
3
e
```
#### 4.4 distinct

distinct方法用于去重复元素

```java
public class StreamTest {
    public static void main(String[] args) {
        distinctTest();
    }
    public static void distinctTest(){
        int[] int1 = {1,2,3,4};
        int[] int2 = {5,3,7,1};
        List<int[]> ints = Arrays.asList(int1,int2);// ints[1,2,3,4,5,3,7,1]
        ints.stream()
                .flatMapToInt(Arrays::stream)
                .distinct() // 去除重复元素
                .peek(System.out::println)
                .toArray();
    }
}
```
```
1
2
3
4
5
7
```
#### 4.5 sorted

sorted表示对流中的元素进行排序，需要使用Conparable和Comparator比较器。

```java
public class StreamTest {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("123","456","789","1101","asdaa","3e3e3e","2321eew","212121121");
        sortedTest(list);
    }
    public static void sortedTest(List<String> list){
        System.out.println("----自然顺序:");
        list.stream().sorted().peek(System.out::println).collect(Collectors.toList());
        System.out.println("----指定排序:");// 按长度排序
        list.stream().sorted((a,b) -> a.length()-b.length()).peek(System.out::println).collect(Collectors.toList());
    }
}
```
```
----自然顺序:
1101
123
212121121
2321eew
3e3e3e
456
789
asdaa
----指定排序:
123
456
789
1101
asdaa
3e3e3e
2321eew
212121121
```
#### 4.6 limit

limit可用于从首个元素开始截取N个元素，组成新流返回。

```java
public class StreamTest {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("123","456","789","1101","asdaa","3e3e3e","2321eew","212121121");
        limitTest(list);
    }
    public static void limitTest(List<String> list){
        list.stream().limit(2).peek(System.out::println).collect(Collectors.toList());// 获取集合前两位元素
    }
}
```
```
123
456
```
#### 4.7 skip

skip表示放弃N个元素，将剩余元素组成新流返回。

```java
public class StreamTest {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("123","456","789","1101","asdaa","3e3e3e","2321eew","212121121");
        skipTest(list);
    }
    public static void skipTest(List<String> list){
        list.stream().skip(2).peek(System.out::println).collect(Collectors.toList());// 跳过集合前两位元素
    }
}
```
```
789
1101
asdaa
3e3e3e
2321eew
212121121
```
### 五、流结束操作

- forEach: 循环操作Stream中数据。
- forEachOrdered: 按元素顺序执行循环操作。
- toArray: 返回流中元素对应的数组对象。
- reduce: 聚合操作，用来做统计。
- collect: 聚合操作，封装目标数据。
- min、max、count: 聚合操作，最小值，最大值，总数量。
- anyMatch: 短路操作，有一个符合条件返回true。
- allMatch: 所有数据都符合条件返回true。
- noneMatch: 所有数据都不符合条件返回true。
- findFirst: 短路操作，获取第一个元素。
- findAny: 短路操作，获取任一元素。

#### 5.1 forEach和forEachOrdered

遍历集合操作，如果是多线程并行遍历forEach不能保证元素的顺序而forEachOrdered可以保证元素按顺序遍历

```java
public class StreamTest {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("123","456","789","1101","212121121","asdaa","3e3e3e","2321eew");
        forEachTest(list);
        System.out.println("----------");
        forEachOrderedTest(list);
    }
    public static void forEachTest(List<String> list){
        list.stream().parallel().forEach(System.out::println);// 使用parallel方法多线程并行遍历
    }
    public static void forEachOrderedTest(List<String> list){
        list.stream().parallel().forEachOrdered(System.out::println);// 使用parallel方法多线程并行遍历
    }
}
```
```
asdaa
212121121
789
1101
2321eew
3e3e3e
456
123
----------
123
456
789
1101
212121121
asdaa
3e3e3e
2321eew
```

#### 5.2 toArray

toArray有两个方法，一个是无参方法，一个有参方法。

无参方法返回的只能是Object[]数组类型，而有参方法，可以指定结果数组类型。

```java
public class StreamTest {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("123","456","789","1101","212121121","asdaa","3e3e3e","2321eew");
        toArrayTest(list);
    }
    public static void toArrayTest(List<String> list){
        Object[] objs = list.stream().filter(e -> e.length()>6).toArray();//无参的方法
        String[] ss = list.stream().filter(e -> e.length()>6).toArray(String[]::new);//有参的方法
    }
}
```
#### 5.3 reduce
