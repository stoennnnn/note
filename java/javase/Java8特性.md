#### 1.lambda表达式

##### list.forEach()

```java
 List<String> list = Arrays.asList("a", "b", "c", "d", "e");
// list.forEach(e-> System.out.println(e));
 list.forEach(System.out::println);//::方法引用符号，前面是类型后面是引用的方法String::isEmpty
```

##### Streams

Stream是对集合包装，支持许多操作b比如：map，filter，sorted，count，min，max，collect，sum等

##### filter

```java
list.stream().filter(e->e.contains("a")).forEach(System.out::println);
```

##### min/max

```java
List<Integer> list = Arrays.asList(1, 7, 8, 3,2);
Integer i = list.stream().max((e, e2) -> (e.compareTo(e2))).get();//传入2哥参数
System.out.println(i);
```

##### mapToInt()，mapToDouble(),mapToLong等

```java
IntSummaryStatistics r = list.stream().mapToInt(x -> x).summaryStatistics();
System.out.println(r.getAverage()+","+r.getCount()+","+r.getMax()+","+r.getMin()+","+r.getSum());
```

##### sorted

```java
List<Integer> list = Arrays.asList(1,6,2,3);
List<Integer> collect = list.stream().sorted((a, b) -> a.compareTo(b)).collect(Collectors.toList());
collect.forEach(System.out::println);
```

#### 2.Optional类

##### Optional.ofNullable(a)和Optional.of()

```java
List<String> list = Arrays.asList("aa","bb","cc","dd");
List<Integer> collect = list.stream().map(String::hashCode).collect(Collectors.toList());
collect.forEach(System.out::println);
```

##### **Optional.isPresent()**

存在返回true，不存在返回false

##### **Optional.get()**

获取值。如果是空值，Optional.*ofNullable*(a)抛出*NoSuchElementException: No value present**异常。*后者抛出*NullPointerException*异常

##### **Optional.**orElse()

如果有值返回值，没有就返回指定值。

```java
String s = b.orElse("woaini");
System.out.println( s);
```

**Optional.map()**

将Optional里面的值映射成其他值，并返回Optional；为空则返回空Optional

```java
Optional<Stri；ng> s = b.map(e -> e.toUpperCase());
System.out.println( s.get());
```

##### **Optional.flatMap()**

和上面的区别是，mapping函数里面的返回值必须是Optional；

```java
Optional<String> s = b.flatMap(e -> Optional.ofNullable(e.toUpperCase()));
System.out.println( s.get());
```

##### **Optional.filter()**

条件过滤，满足条件则返回Optional，不满足返回空的Optional

```java
Optional<String> s = b.filter(e->e.contains("a"));
System.out.println( s.get());
```

