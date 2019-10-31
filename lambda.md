### 一、实际业务中如何使用Lambda

假设我们有个Employee员工实体类.

```java
@Data
public class Employee{
    private int id; //id
    private String name;  //姓名
    private int age;  // 年龄
    private double salary;  // 薪水
}
```
List数据如下.

```java
List<Employee> emps = Arrays.asList(
    new Employee(101, "张三", 18, 9999.99),
    new Employee(102, "李四", 59, 6666.66),
    new Employee(103, "王五", 28, 3333.33),
    new Employee(104, "赵六", 8, 7777.77),
    new Employee(105, "田七", 38, 5555.55)
);
```
现有需求如下：
- 获取公司中年龄小于 35 的员工信息
- 获取公司中工资大于 5000 的员工信息

一般我们会定义两个方法分别来实现：

```java
public List<Employee> filterEmployeeAge(List<Employee> emps){
    List<Employee> list = new ArrayList<>();
    
    for (Employee emp : emps) {
        if(emp.getAge() <= 35){
            list.add(emp);
        }
    }
    return list;
}

public List<Employee> filterEmployeeSalary(List<Employee> emps){
    List<Employee> list = new ArrayList<>();
    
    for (Employee emp : emps) {
        if(emp.getSalary() >= 5000){
            list.add(emp);
        }
    }
    return list;
}
```
我们注意到上面两个方法的非常相似，主要是if判断的逻辑不同，如果需求增加，就不得不增加方法。

#### 1.1 优化方式一：策略设计模式

1，定义一个接口，用于判断是否满足某个条件。

```java
public interface MyPredicate<T>{
    boolean isSelected(T t);
}
```
2，定义两个实现类，分别实现MyPredicate接口

```java
public class FilterEmployeeForAge implements MyPredicate<Employee>{
    @Override
    public boolean isSelected(Employee employee){
        return employee.getAge() < 35;
    }
}
```
```java
public class FilterEmployeeForSalary implements MyPredicate<Employee>{
    @Override
    public boolean isSelected(Employee employee){
        return employee.getSalary() > 5000;
    }
}
```
3，定义一个方法filterEmployee，主要是从List<Employee>对象中根据MyPredicate接口的实现类来挑选哪些Employee符合条件。

```java
public List<Employee> filterEmployee(List<Employee> emps, MyPredicate<Employee> mp){
    List<Employee> list = new ArrayList<>();

    for (Employee employee : emps){
        if (mp.isSelected(employee)){
            list.add(employee);
        }
    }
    return list;
}
```
4，测试

```java
@Test
public void test(){
    List<Employee> list1 = filterEmployee(emps, new FilterEmployeeForAge());
    for (Employee employee : list1){
        System.out.println(employee);
    }

    List<Employee> list2 = filterEmployee(emps, new FilterEmployeeForSalary());
    for (Employee employee : list2){
        System.out.println(employee);
    }
}
```
使用策略设计模式filterEmployee方法只用写一个即可，如果需要添加新的策略那么我们必须再写实现类，然后使用的时候filterEmployee方法第二个参数传入具体的实现类即可完成对应的需求。

#### 1.2 优化方式二：匿名内部类

这种方式可以避免接口的实现类编写，将实现放入匿名内部类中。相比策略设计模式，我们不需要实现类，并且在使用的时候也有不同。即：

```java
@Test
public void test(){
    List<Employee> list1 = filterEmployee(emps, new MyPredicate<Employee>(){
        @Override
        public boolean isSelected(Employee employee){
            return employee.getAge() < 35;
        }
    });

    List<Employee> list2 = filterEmployee(emps, new MyPredicate<Employee>(){
        @Override
        public boolean isSelected(Employee employee){
            return employee.getSalary() > 5000;
        }
    });
}
```
可以看出匿名内部类方式避免了接口实现类的编写，它是把实现类的逻辑放入到filterEmployee方法使用的时候来实现。如果新增需求只需要再写匿名内部类即可。

#### 1.3 优化方式三：Lambda 表达式

匿名内部类有大量重复的代码，可以使用Lambda表达式即可完成消除重复代码的功能。

```java
@Test
public void test(){
    List<Employee> list1 = filterEmployee(emps, (e) -> e.getAge() < 35);
    list1.forEach(System.out::println);

    List<Employee> list2 = filterEmployee(emps, (e) -> e.getSalary() > 5000);
    list2.forEach(System.out::println);
}
```
#### 1.4 优化方式四：Stream API

Lambda 表达式的确简化了代码，但是我们还是需要编写filterEmployee方法，如果能去除这处代码则更加简化代码，只要使用Stream API完成这个功能。

```java
@Test
public void test(){
    emps.stream()
        .filter((e) -> e.getAge() < 35)
        .forEach(System.out::println);

    emps.stream()
        .filter((e) -> e.getSalary() > 5000)
        .forEach(System.out::println);
}
```

### 二、Lambda表达式基础

当我们定义一个接口，如果接口中只定义了一个方法，那么这种接口被称为函数式接口，Lambda表达式就必须依托这个前提。

接口

```java
public interface MyPredicate<T>{
    boolean isSelected(T t);
}
```
过滤的方法

```java
public List<Employee> filterEmployee(List<Employee> emps, MyPredicate<Employee> mp){
    List<Employee> list = new ArrayList<>();
    
    for (Employee employee : emps) {
        if(mp.isSelected(employee)){
            list.add(employee);
        }
    }
    return list;
}
```
调用过滤方法传入匿名实现类

```java
List<Employee> list = filterEmployee(emps, new MyPredicate<Employee>(){
    @Override
    public boolean isSelected(Employee t) {
        return t.getAge() < 35;
    }
});
```
Lambda表达式调用过滤方法

```java
List<Employee> list = filterEmployee(emps, (e) -> e.getAge() < 35);
```

### 三、Lambda表达式结构

Java8中引入了一个新的操作符 "->" 该操作符称为箭头操作符或 Lambda 操作符，它将 Lambda 表达式拆分成两部分：
- 左侧：Lambda 表达式的参数列表，就是isSelected方法的参数列表(Employee t)
- 右侧：Lambda 表达式中所需执行的功能， 即 Lambda 体：return t.getAge() < 35;

```java
new MyPredicate<Employee>() {
    @Override
    public boolean isSelected(Employee t) {
        return t.getAge() < 35;
    }
}
```
被下面的lambda表达式替换

```java
(e) -> e.getAge() < 35
```
e只是一个名称，代表Employee对象，e.getAge() < 35 可以省略return。

### 四、Lambda语法格式

（1）无参数，无返回值（接口的方法是没有参数和返回值的）

```java
() -> System.out.println("Hello Lambda!")
```
（2）有一个参数，并且无返回值

```java
(x) -> System.out.println(x)
```
（3）若只有一个参数，小括号可以省略不写

```java
x -> System.out.println(x)
```
（4） 有两个以上的参数，有返回值，并且 Lambda 体中有多条语句，需要大括号，需要return。

```java
Comparator<Integer> com = (x, y) -> {
     System.out.println("函数式接口");
     return Integer.compare(x, y);
};
```
（5）若 Lambda 体中只有一条语句， return 和 大括号都可以省略不写。

```java
Comparator<Integer> com = (x, y) -> Integer.compare(x, y)
```
（6）Lambda 表达式的参数列表的数据类型可以省略不写，因为JVM编译器通过上下文推断出，数据类型，即“类型推断”

```java
(Integer x, Integer y) -> Integer.compare(x, y)
```
注意：Lambda 表达式需要“函数式接口”的支持，函数式接口：接口中只有一个抽象方法的接口，称为函数式接口。 可以使用注解`@FunctionalInterface` 修饰，可以检查是否是函数式接口。

### 五、Lambda接口编程流程

1. 定义一个函数式接口
2. 编写一个方法，输入需要操做的数据和接口
3. 在调用方法时传入数据 和 lambda 表达式，用来操作数据

举例，定义一个可以对两个整数进行加减乘除的操作。以前我们可能定义四个方法，但是如果增加操作类型则需要再定义对应的方法。可以使用Lambda表达式来实现。

1，定义一个函数式接口，使用@FunctionalInterface注解标注

```java
@FunctionalInterface
public interface MyFunction<R,T>{
    R operator(T t1, T t2);
}
```
2，编写一个方法，输入需要操做的数据和接口，基本数据类型需要包装类型

```java
public Integer getValue(Integer x, Integer y, MyFunction<Integer, Integer> mf){
    return mf.operator(x, y);
}
```
3，在调用方法时传入数据 和 lambda 表达式，用来操作数据

```java
@Test
public void test(){
    System.out.println(getValue(20,5, (x, y) -> x + y ));
    System.out.println(getValue(20,5, (x, y) -> x - y ));
    System.out.println(getValue(20,5, (x, y) -> x * y ));
    System.out.println(getValue(20,5, (x, y) -> x / y ));
}
```
### 六、Java8内置接口

上面我们看到要使用Lambda表达式必须先定义接口，创建相关方法之后才可使用，这样做十分不便，其实java8已经内置了许多接口，方便我们使用Lambda表达式。

*Java8 内置的四大核心函数式接口*

Consumer<T> : 消费型接口：有入参，无返回值

```java
void accept(T t);
```
Supplier<T> : 供给型接口：无入参，有返回值

```java
T get(); 
```
Function<T, R> : 函数型接口：有入参，有返回值

```java
R apply(T t);
```
Predicate<T> : 断言型接口：有入参，有返回值，返回值类型确定是boolea

```java
boolean test(T t); 
```

举例：对字符串进行操作，有输入有输出，使用函数型接口 Function<T, R>

编写一个方法，输入需要操做的数据和接口

```java
public String strHandler(String str, Function<String, String> fun){
    return fun.apply(str);
}
```
在调用方法时传入数据 和 lambda 表达式，用来操作数据

```java
@Test
public void test(){
    System.out.println(strHandler("ABC",(x) -> x.toLowerCase()));
    System.out.println(strHandler("  aaf  ",(x) -> x.trim()));
}
```
注意：当strHandler方法只是把传入的数据str放入到apply(str)进行执行的时候，也就是strHandler方法只是简单的调用apply方法而没有其他逻辑，那么strHandler方法也可以省略掉.

```java
@Test
public void test(){
    Function<String, String> fun = (x) -> x.toLowerCase();
    System.out.println(fun.apply("ABC"));
    fun = (x) -> x.trim();
    System.out.println(fun.apply("  aaf  "));
}
```

### 七、引用

引用理解为 Lambda 表达式的另外一种表现形式，提供了一种简短的语法而已。主要有三种：

- 方法引用
- 构造器引用
- 数组引用

#### 7.1 方法引用

注意：方法引用所引用的方法的参数列表与返回值类型，需要与函数式接口中抽象方法的参数列表和返回值类型保持一致！

1. 对象的引用 :: 实例方法名

```java
@Test
 public void test2(){
    Employee emp = new Employee(101, "张三", 18, 9999.99);
    
    Supplier<String> sup1 = () -> emp.getName();
    System.out.println(sup1.get());
    
    Supplier<String> sup2 = emp::getName;
    System.out.println(sup2.get());
}
```
其中

```java
() -> emp.getName()
```
被替换为

```java
emp::getName
```
注意 getName的参数和返回值与Supplier接口的get方法的参数和返回值一致，否则会出现错误。

```java
@Test
public void test1(){
    PrintStream ps = System.out;
    Consumer<String> con = (str) -> ps.println(str);
    con.accept("Hello Java8！");
    
    Consumer<String> con2 = ps::println;
    con2.accept("Hello Java8！");
    
    Consumer<String> con3 = System.out::println;
    con3.accept("Hello Java8！");
}
```
这个例子中有入参，但是经过方法引用变形之后，似乎没有入参了，其实这是由于println方法和accept方法的入参和返回值一致，因此无需传入。

2. 类名 :: 静态方法名

```java
@Test
public void test(){
    Comparator<Integer> com = (x, y) -> Integer.compare(x, y);
    Comparator<Integer> com2 = Integer::compare;
}
```
下面的也是：

```java
@Test
public void test(){
    BiFunction<Double, Double, Double> fun = (x, y) -> Math.max(x, y);
    System.out.println(fun.apply(2.5, 222.2));
    
    BiFunction<Double, Double, Double> fun2 = Math::max;
    System.out.println(fun2.apply(3.2, 21.5));
}
```
3. 类名 :: 实例方法名

```java
@Test
public void test(){
    BiPredicate<String, String> bp = (x, y) -> x.equals(y);
    System.out.println(bp.test("abcde", "abcde"));

    BiPredicate<String, String> bp2 = String::equals;
    System.out.println(bp2.test("abc", "abc"));
}
```
使用场景： 若Lambda 的参数列表的第一个参数，是实例方法的调用者，第二个参数(或无参)是实例方法的参数时，格式： ClassName::MethodNam

#### 7.2 构造器引用
