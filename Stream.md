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

#### 3.1 基于数组创建流

```java
public class StreamTest {
    public static void createStream() {
        // 通过数组生成流
        int[] ints = {1,2,3,4,5,6};
        IntStream s1 = Arrays.stream(ints);
        Stream s2 = Stream.of("111","222","333");
        String[] ss = {"123","321","456","654"};
        Stream<String> s3 = Arrays.stream(ss);
    }
}
```
#### 3.2 通过构建器生成流

```java
public class StreamTest {
    public static void createStream() {
        // 通过构建器生成流
        Stream<Object> s4 = Stream.builder().add("123").add("321").add("444").add("@21").build();
    }
}
```
#### 3.3 基于集合生成流

```java
public class StreamTest {
    public static void createStream() {
        // 通过集合生成流
        List<String> lists = Arrays.asList("123","321","1212","32321");
        Stream<String> s5 = lists.stream();
        Stream<String> s6 = lists.parallelStream();// 并行流
    }
}
```
#### 3.4 创建空流

```java
public class StreamTest {
    public static void createStream() {
        // 创建空流
        Stream<String> s7  = Stream.empty();
    }
}
```
#### 3.5 基于函数创建无限流

```java
public class StreamTest {
    public static void createStream() {
        // 创建无限流
        Stream.generate(()->"number"+new Random().nextInt()).limit(100).forEach(System.out::println);
        Stream.iterate(0,n -> n+2).limit(10).forEach(System.out::println);
    }
}
```

### 四、流中间操作

流中间操作指的是该操作的返回值仍然是流。

<table>
<thead>
<tr>
<th>序号</th>
<th>操作</th>
<th>方法</th>
<th>说明</th>
<th>备注</th>
</tr>
</thead>
<tbody>
<tr>
<td>1</td>
<td>filter</td>
<td>Stream&lt;T&gt; filter(Predicate&lt;? super T&gt; predicate)</td>
<td>返回当前流中满足参数predicate过滤条件的元素组成的新流</td>
<td>过滤器</td>
</tr>
<tr>
<td>2</td>
<td>map</td>
<td>&lt;R&gt; Stream&lt;R&gt; map(Function&lt;? super T, ? extends R&gt; mapper)</td>
<td>返回通过给定mapper作用于当前流的每个元素之后的结果组成的新流</td>
<td>函数</td>
</tr>
<tr>
<td>3</td>
<td>mapToInt</td>
<td>IntStream mapToInt(ToIntFunction&lt;? super T&gt; mapper)</td>
<td>返回通过给定mapper作用于当前流的每个元素之后的结果组成的新的Int流</td>
<td>函数</td>
</tr>
<tr>
<td>4</td>
<td>mapToLong</td>
<td>LongStream mapToLong(ToLongFunction&lt;? super T&gt; mapper)</td>
<td>返回通过给定mapper作用于当前流的每个元素之后的结果组成的新的Long流</td>
<td>函数</td>
</tr>
<tr>
<td>5</td>
<td>mapToDouble</td>
<td>DoubleStream mapToDouble(ToDoubleFunction&lt;? super T&gt; mapper)</td>
<td>返回通过给定mapper作用于当前流的每个元素之后的结果组成的新的Double流</td>
<td>函数</td>
</tr>
<tr>
<td>6</td>
<td>flatMap</td>
<td>&lt;R&gt; Stream&lt;R&gt; flatMap(Function&lt;? super T, ? extends Stream&lt;? extends R&gt;&gt; mapper)</td>
<td>根据给定的mapper作用于当前流的每个元素，将结果组成新的流来返回</td>
<td>扁平函数</td>
</tr>
<tr>
<td>7</td>
<td>flatMapToInt</td>
<td>IntStream flatMapToInt(Function&lt;? super T, ? extends IntStream&gt; mapper)</td>
<td>根据给定的mapper作用于当前流的每个元素，将结果组成新的Int流来返回</td>
<td>扁平函数</td>
</tr>
<tr>
<td>8</td>
<td>flatMapToLong</td>
<td>LongStream flatMapToLong(Function&lt;? super T, ? extends LongStream&gt; mapper)</td>
<td>根据给定的mapper作用于当前流的每个元素，将结果组成新的Long流来返回</td>
<td>扁平函数</td>
</tr>
<tr>
<td>9</td>
<td>flatMapToDouble</td>
<td>DoubleStream flatMapToDouble(Function&lt;? super T, ? extends DoubleStream&gt; mapper)</td>
<td>根据给定的mapper作用于当前流的每个元素，将结果组成新的Double流来返回</td>
<td>扁平函数</td>
</tr>
<tr>
<td>10</td>
<td>distinct</td>
<td>Stream&lt;T&gt; distinct()</td>
<td>返回去掉当前流中重复元素之后的新流</td>
<td>去重</td>
</tr>
<tr>
<td>11</td>
<td>sorted</td>
<td>Stream&lt;T&gt; sorted()</td>
<td>返回当前流中元素排序之后的新流，需要元素类型实现Comparable</td>
<td>排序</td>
</tr>
<tr>
<td>12</td>
<td>sorted</td>
<td>Stream&lt;T&gt; sorted(Comparator&lt;? super T&gt; comparator)</td>
<td>返回当前流中元素排序之后的新流，需要传递一个Comparator</td>
<td>排序</td>
</tr>
<tr>
<td>13</td>
<td>peek</td>
<td>Stream&lt;T&gt; peek(Consumer&lt;? super T&gt; action)</td>
<td>针对流中的每个元素执行操作action</td>
<td>查阅</td>
</tr>
<tr>
<td>14</td>
<td>limit</td>
<td>Stream&lt;T&gt; limit(long maxSize)</td>
<td>返回指定的数量的元素组成的新流</td>
<td>限制</td>
</tr>
<tr>
<td>15</td>
<td>skip</td>
<td>Stream&lt;T&gt; skip(long n)</td>
<td>返回第n个之后的元素组成的新流</td>
<td>跳过</td>
</tr>
</tbody>
</table>
