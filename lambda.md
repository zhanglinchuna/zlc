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

