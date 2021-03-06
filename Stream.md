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
        List<String> list = Arrays.asList("3e3e3e");
        list.stream()
                .map(e -> e.split(""))// 将每个字符串元素分解为字符数组
                .flatMap(e -> Arrays.stream(e))//将每个字符数组并转化为流
                .peek(System.out::println)
                .collect(Collectors.toList());
    }
}
```
```
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
        
        Object[] objs = list.stream().filter(e -> e.length()>6).toArray();//无参的方法
        String[] ss = list.stream().filter(e -> e.length()>6).toArray(String[]::new);//有参的方法
    }
}
```
#### 5.3 reduce

reduce方法有三个重载的方法

```java
public interface Stream<T> extends BaseStream<T, Stream<T>> {

    Optional<T> reduce(BinaryOperator<T> accumulator);// 方法1
    
    T reduce(T identity, BinaryOperator<T> accumulator);// 方法2
    
    <U> U reduce(U identity,BiFunction<U, ? super T, U> accumulator,BinaryOperator<U> combiner);// 方法3
}
```
方法1：

这个方法只有一个参数，该方法的作用就是将流中的两个元素传给BinaryOperator的实现方法，BinaryOperator的实现方法处理完后返回处理后的结果，然后将处理结果和流中元素又循环回调BinaryOperator的实现方法，将结果和元素传递给方法，以此类推，直到最后一个元素执行完毕，返回的就是最终结果。

```java
public class StreamTest {
    public static void main(String[] args) {
        List<Integer> ints = Arrays.asList(1,2,3,4,5,6,7,8,9);//将所有元素累加
        Optional<Integer> optional = ints.stream().reduce(Integer::sum);// Integer::sum 相当于 (a,b)->Integer.sum(a,b)
        System.out.println(optional.get());
    }
}
```
```
45
```
方法2：

方法2比方法1多了一个参数identity，identity实际上就是一个初始值，如果流中没有元素的话，还有初始值作为结果返回，不会存在null的情况。

```java
public class StreamTest {
    public static void main(String[] args) {
        List<Integer> ints = Arrays.asList();//空元素的集合
        Optional<Integer> optional = ints.stream().reduce((a,b)->Integer.sum(a,b));
        //System.out.println(optional.get());//异常！流中的元素为空，所以结果optional为null
        Integer result = ints.stream().reduce(3, (a, b) -> Integer.sum(a, b));//设置默认值 3
        System.out.println(result);
    }
}
```
```
3
```
方法3

在方法2方法的基础上又加了一个参数combiner，其实这个方法是用于处理并行流的归纳操作，最后的参数combiner用于归纳各个并行的结果，用于得出最终结果。

```java
public class StreamTest {
    public static void main(String[] args) {
        List<Integer> ints = Arrays.asList(1,2,3,4,5,6,7,8,9);
        Integer min = ints.parallelStream().reduce(Integer.MAX_VALUE, Integer::min, Integer::min);
        System.out.println(min);
    }
}
```
```
1
```
#### 5.4 collect

collect是收集的意思，这里的作用就是收集归纳，将流中的数据映射为各种结果。

collect方法有两个重载方法：

```java
public interface Stream<T> extends BaseStream<T, Stream<T>> {

    <R, A> R collect(Collector<? super T, A, R> collector);// 方法1
    
    <R> R collect(Supplier<R> supplier,BiConsumer<R, ? super T> accumulator,BiConsumer<R, R> combiner);// 方法2
}
```

方法1：

它只需要一个Collector类型的参数，这个Collector可以称呼为收集器，我们可以随意组装一个收集器来进行元素归纳。

JDK提供了一个Collectors工具类，在这个工具类里面预实现了多个的Collector供我们直接使用

- toMap：将数据流转换为map，里面包含的元素是用key/value的形式的
- toSet：将数据流转换为set，里面包含的元素不可重复
- toList：将数据流转出为list，里面包含的元素是有序的
- joining：拼接字符串
- groupingBy：分组，可以将list转换map
- couting：统计元素数量
- maxBy：获取最大元素
- minBy：获取最小元素
- summarizingInt: 汇总int类型的元素，返回IntSummaryStatistics，再调用具体的方法对元素进行统计：getCount（统计数量），getSum（求和），getMin（获取最小值），getMax（获取最大值），getAverage（获取平均值）
- summarizingLong：汇总long类型的元素，用法同summarizingInt
- summarizingDouble：汇总double类型的元素，用法同summarizingInt
- averagingInt：获取int类型的元素的平均值，返回一个double类型的数据
- averagingLong：获取long类型的元素的平均值，用法同averagingInt
- averagingDouble：获取double类型的元素的平均值，用法同averagingInt
- mapping：获取映射，可以将原始元素的一部分内容作为一个新元素返回


```java
public class CollectorsTest {
   
    public static void main(String[] args) {
        List<String> list = Arrays.asList("123","456","789","1101","212121121","asdaa","3e3e3e","2321eew");
        // toCollection 将结果转成LinkedList
        List<String> list01 = list.stream().collect(Collectors.toCollection(LinkedList::new));
        // toCollection 将结果转成TreeSet
        List<String> list02 = list.stream().collect(Collectors.toCollection(TreeSet::new));
        // toList 将结果转成ArrayList
        List<String> list03 = list.stream().collect(Collectors.toList());
        // toSet 将结果转成HashSet
        Set<String> set01 = list.stream().collect(Collectors.toSet());
        // toMap 将结果转成HashMap
        Map<String, String> collect = list.stream().collect(Collectors.toMap(k -> k,k -> "value:" + k));
        // joining 拼接字符串
        String collect1 = list.stream().collect(Collectors.joining(","));
        // couting 统计元素数量
        Long collect2 = list.stream().collect(Collectors.counting());
        // maxBy 获取最大元素
        String collect3 = list.stream().collect(Collectors.maxBy((a, b) -> a.compareTo(b))).orElse(null);
        
        //  summarizingInt 汇总int类型的元素，返回IntSummaryStatistics，再调用具体的方法对元素进行统计：getCount（统计数量），getSum（求和），getMin（获取最小值），getMax（获取最大值），getAverage（获取平均值）
        List<String> list04 = Arrays.asList("2", "3", "5");
        IntSummaryStatistics summaryStatistics = list04.stream().collect(Collectors.summarizingInt(x -> Integer.parseInt(x)));
        // getSum（求和）结果：sum = 10
        long sum = summaryStatistics.getSum();

        // averagingInt 获取int类型的元素的平均值，返回一个double类型的数据
        Double double = list04.stream().collect(Collectors.averagingInt(x -> Integer.parseInt(x))); // 结果：double = 3.3333333333333335
        
        // mapping 获取映射，可以将原始元素的一部分内容作为一个新元素返回
        List<User> userList = new ArrayList<User>() {{add(new User("jack",23)); add(new User("james",30)); add(new User("curry",28));}};
        //[jack, james, curry]
        List<String> collect4 = userList.stream().collect(Collectors.mapping(User::getName, Collectors.toList()));
    }
    
    @Data
    @AllArgsConstructor
    static class User {
        private String name;
        private Integer age;
    }
}
```

方法2：

该方法有3个参数：

第一个参数：supplier用于生成一个R类型的结果容器来盛放结果

第二个参数：accumulator累加器用于定义盛放的方式，其中T为一个元素，R为结果容器

第三个参数：combiner的作用是将并行操作的各个结果整合起来

```java
public class StreamTest {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("123", "456", "789", "1101", "212121121", "asdaa", "3e3e3e", "2321eew");
        list.stream().parallel().collect(
                ArrayList::new, //第一个参数：创建一个新的ArrayList集合
                (a, b) -> a.add(b), // 第二个参数：a是第一个参数创建的ArrayList集合，b是list流中的元素
                (a, b) ->a.addAll(b));// 第三个参数：将多个线程的ArrayList集合一个一个的整体添加到第一个集合中，最终整合出一个最终结果并返回
    }
}
```

#### 5.5 max和min

通过给定的比较器，得出流中最大\最小的元素，为避免null返回，这里使用Optional来封装返回值。

```java
public class StreamTest {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("123","456","789","1101","212121121","asdaa","3e3e3e","2321eew");
        System.out.println("长度最大：" + list.stream().max((a,b)-> a.length()-b.length()));
        System.out.println("长度最小：" + list.stream().min((a,b)-> a.length()-b.length()));
    }
}
```
```
长度最大：Optional[212121121]
长度最小：Optional[123]
```

#### 5.6 count

count是无参方法，用于计数，返回流中元素个数。

```java
public class StreamTest {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("123","456","789","1101","212121121","asdaa","3e3e3e","2321eew");
        System.out.println("元素个数为：" + list.stream().count());
    }
}
```
```
元素个数为：8
```

#### 5.7 match

匹配操作，包含：allMatch、anyMatch、noneMatch

- allMatch：所有元素都满足条件，返回boolean类型
- anyMatch：任意一个元素满足条件，返回boolean类型
- noneMatch：所有元素都不满足条件，返回boolean类型

###### anyMatch

该方法需要一个Predicate参数，用于校验流中的元素，只要有一个满足规则，则返回true，全不满足，返回false。

```java
public class StreamTest {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("123","456","789","1101","212121121","asdaa","3e3e3e","2321eew");
        System.out.println(list.stream().anyMatch(e -> e.length()>10));
        System.out.println(list.stream().anyMatch(e -> e.length()>8));
    }
}
```
```
false
true
```

###### allMatch

该方法同样需要一个Predicate参数，用于校验流中的所有元素，只有全部满足规则才能返回true，只要有一个不满足则返回false。

```java
public class StreamTest {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("123","456","789","1101","212121121","asdaa","3e3e3e","2321eew");
        System.out.println(list.stream().allMatch(e -> e.length()>1));
        System.out.println(list.stream().allMatch(e -> e.length()>3));
    }
}
```
```
true
false
```

###### noneMatch

该方法同样需要一个Predicate参数，用于校验流中的所有元素,只有所有元素都不满足规则的情况下返回true，否则返回false。

```java
public class StreamTest {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("123","456","789","1101","212121121","asdaa","3e3e3e","2321eew");
        System.out.println(list.stream().noneMatch(e -> e.length()>10));
        System.out.println(list.stream().noneMatch(e -> e.length()>8));
    }
}
```
```
true
false
```
#### 5.8 find

查找操作，包含：findFirst、findAny

- findFirst：找到第一个，返回的类型为Optional
- findAny：使用 stream() 时找到的是第一个元素，使用 parallelStream() 并行时找到的是其中一个元素，返回的类型为Optional

###### findFirst

该方法无参数，主要用于获取流中的第一个元素，如果流无序，那么可能返回任意一个。

```java
public class StreamTest {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("123","456","789","1101","212121121","asdaa","3e3e3e","2321eew");
        System.out.println(list.stream().parallel().findFirst().get());
    }
}
```
```
123
```

###### findAny

该方法无参数，主要用于获取流中的任一元素。

```java
public class StreamTest {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("123","456","789","1101","212121121","asdaa","3e3e3e","2321eew");
        System.out.println(list.stream().parallel().findAny().get());
    }
}
```
```
asdaa
```

### 六、实际案例

1. 从两个集合中找相同的元素。一般用于批量数据导入的场景，先查询出数据，再批量新增或修改。

```java
public class WorkTest {

    @Test
    public void testWork1() {
        List<String> list1 = Lists.newArrayList("a", "b", "ab");
        List<String> list2 = Lists.newArrayList("a", "c", "ab");
        List<String> collect = list1.stream()
                .filter(x -> list2.stream().anyMatch(e -> e.equals(x)))
                .collect(Collectors.toList());
        //结果：[a, ab]
        System.out.println(collect);

    }
}
```

2. 有两个集合a和b，过滤出集合a中有，但是集合b中没有的元素。这种情况可以使用在假如指定一个id集合，根据id集合从数据库中查询出数据集合，再根据id集合过滤出数据集合中不存在的id，这些id就是需要新增的。

```java
@Test
public void testWork2() {
    List<String> list1 = Lists.newArrayList("a", "b", "ab");
    List<String> list2 = Lists.newArrayList("a", "c", "ab");
    List<String> collect = list1.stream()
      .filter(x -> list2.stream().noneMatch(e -> e.equals(x)))
      .collect(Collectors.toList());
    //结果：[b]
    System.out.println(collect);
}
```

3. 根据条件过滤数据，并且去重做数据转换

```java
 @AllArgsConstructor
  @Data
  class User {
      private String name;
      private Integer age;
  }

  @Test
  public void testWork3() {
      List<User> userList = new ArrayList<User>() {{
          add(new User("jack",23));
          add(new User("james",30));
          add(new User("curry",28));
          add(new User("tom",27));
          add(new User("sue",29));
      }};

      List<String> collect = userList.stream()
              .filter(x -> x.getAge() > 27)
              .sorted((a, b) -> a.getAge().compareTo(b.getAge()))
              .limit(2)
              .map(User::getName)
              .collect(Collectors.toList());
      //结果：[curry, sue]
      System.out.println(collect);
  }
```
4. 统计指定集合中，姓名相同的人中年龄最小的年龄

```java
@Test
public void testWork4() {
    List<User> userList = new ArrayList<User>() {{
        add(new User("tom", 23));
        add(new User("james", 30));
        add(new User("james", 28));
        add(new User("tom", 27));
        add(new User("sue", 29));
    }};

    userList.stream().collect(Collectors.groupingBy(User::getName))
            .forEach((name, list) -> {
                User user = list.stream().sorted((a, b) -> a.getAge().compareTo(b.getAge())).findFirst().orElse(null);
                //结果：name:sue,age:29
                //     name:tom,age:23
                //     name:james,age:28
                System.out.println("name:" + name + ",age:" + user.getAge());
            });
}
```
