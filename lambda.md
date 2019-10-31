### 一、为什么需要lambda

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
需求如下：
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
