# java8
## 接口的默认方法
```
public interface Formula {

    double calculate(int a);

    default double sqrt(int a) {
        return Math.sqrt(a);
    }
}
```
## lamada表达式
```
Collections.sort(list, new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return o2.compareTo(o1);
            }
});
-------->
Collections.sort(list,(String o1,String o2)->{
             return o2.compareTo(o1);
        });
-------->
Collections.sort(list,(o1,o2)->o2.compareTo(o1));list.sort((o1,o2)->o2.compareTo(o1));
```
## 函数式接口
函数式接口表示只有一个抽象方法，但是可以有多个默认方法。
不过函数式接口的注解也不是必须的，当接口只有一个抽象方法的时候，虚拟机会默认给他添加@FunctionInterface
```
@FunctionalInterface
public interface Converter<F,T> {
    T convert(F from);
}
----->
Converter<String,Integer> converter=(str)->Integer.parseInt(str);
```
## 方法和构造方法的引用
静态方法的引用
```
Converter<String,Integer> converter2=Integer::parseInt;
```
实例方法的引用
```
class Something
{
    String startWith(String str)
    {
        return String.valueOf(str.charAt(0));
    }
}
Something something=new Something();
Converter<String,String> converter3=something::startWith;
System.out.println(converter3.convert("123"));
```
构造方法的引用
```
class Person
{
    String firstName;
    String lastName;

    Person() {}

    Person(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
}
------>
public interface PersonFactory<P extends Person> {
    Person create(String firstname,String lastname);
}
------>
PersonFactory<Person> personFactory=Person::new;
Person person = personFactory.create("dahuang", "hao");
```
## lamada表达式作用域
### 局部变量
我们可以直接在 lambda 表达式中访问外部的局部变量：
```
int num=3;
Converter<Integer,String> converter4=(num2)->String.valueOf(num2+num);
```
### 静态变量和实例变量
## 内置函数式接口
Pridecate
只有一个参数的，返回boolean类型的断言式接口。
使用实例
```
Predicate<String> predicate=(s)->s.length()>0;
Predicate<Boolean> predicate2=Objects::isNull;
Predicate<Boolean> predicate3=Objects::nonNull;
Predicate<String> isEmpty = String::isEmpty;
Predicate<String> isNotEmpty = isEmpty.negate();
```
Functions
接收一个参数并产生结果
Suppliers
Consumers
Comparators
## Streams(流)
### 过滤和排序
```
array.stream().filter(str->str.startsWith("a")).forEach(System.out::println);
```
细节，传入一个条件，foreach System.out::println
```
array.stream().sorted((o1,o2)->o2.compareTo(o1)).forEach(System.out::println);
```
原来的数据并没有排序
### map映射
```
array.stream().map(String::toUpperCase).forEach(System.out::println);
```
### 匹配
```
 boolean res = array.stream().anyMatch(str -> str.startsWith("a"));
 boolean res2 = array.stream().allMatch(str -> str.startsWith("a"));
 boolean res3 = array.stream().noneMatch(str -> str.startsWith("z"));
```