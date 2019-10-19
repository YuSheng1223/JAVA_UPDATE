### java8

#### 前言

Oracle在2014年更新了 java8，java8 支持函数式编程。包括新的日期API，新的Stream API等等。

在java8中，最重要的两个特性就是Lambda表达式和Stream API。

#### java8更新了什么？

（1）底层数据结构

java8对底层的HashMap结构进行了优化，在java7中，HashMap的底层结构是数组+链表的形式。而在java8中，HashMap的底层优化为了数组+链表+红黑树的形式。这种形式除了在新增元素的时候较java7性能低一些，但是在查找、删除等方面有了很大的性能提高。

（2）底层的内存结构

在java7中，方法区实际上是堆的一部分，处于方法区的永久区(PremGen)中，包含了所有的class和static变量。同时，这部分几乎不会被垃圾回收机制回收，简单来说就是回收条件比较苛刻。而在java8中，将方法区从堆中剥离了出来。重新建立了MetaSpace元空间，当元空间快要满的时候，垃圾回收机制就开始工作了。

上述两部分更新也使得java8相较于以前版本的jdk速度更快。

#### 为什么要使用Lambda？

在java8以前 我们如果要使用一个接口中的方法，必须要新建一个类来实现这个接口 或者利用匿名内部类来调用，我们期望的是，有一种方式，让我们只需要写接口实现的部分就可以完成全部工作，这样代码就会显得很简洁。在java8中，就对此做了更新，我们只需要将匿名内部类中真正有用的几行代码提取出来就可以了。

总结来说 对于要使用接口中方法，又不想去实现这个接口 我们可以使用Lambda表达式。

在使用Lambda的过程中 语法是需要注意的一点。在java8中引入了新的操作符  `->` 。`->`  左侧：为Lambda表达式的参数列表，如果没有参数 ，就只用一个()  代替即可。`->`右侧 ：右侧为Lambda表达式所需要执行的功能，即Lambda体。有个右侧有多行的代码，需要用{}括起来 。如果接口中方法需要返回值 则之间在{}内书写return即可 。

同时，在`->` 的左侧参数列表部分，我们对于参数类型的指定可有可无。JVM可以通过上下文来推断出参数的数据类型。

Lambda表达式需要**函数式接口**的支持。
**函数式接口**指的是只有一个抽象方法的接口，我们可以在接口上加上@FunctionalInterface注解，来验证是不是函数式接口。



#### 四大内置函数式接口

1. **消费性接口   Consumer<T>**

   void accept(T t); 有参数，但是无返回值
   对类型为T的对象进行操作

2. **供给型接口 Supplier<T>**
   T get();     无参数 ，返回一个T对象

   返回类型为T的对象

3. **函数型接口 Function<T,R>**

   R apply(T t);  有参数 有返回值

   对类型为T的对象进行操作，并且返回结果。结果为类型是R的对象

4. **断定型接口   Predicate<T>**
   boolean test(T t);  有参数 有返回值

   判断类型为T的对象是否满足某种约束，返回boolean类型的值

在java8中不仅仅只有这四个函数式接口，还有诸如 BiFunciton<T,U,R>,BiCunstorm<T,U>等函数式接口。合理应用每个接口才是重中之重。



#### 方法引用和构造器引用

##### 方法引用

 **方法的引用**: 如果Lambda表达式方法体中的已经实现，我们就可以使用方法引用。
 三种语法格式:
​    **1.对象::实例方法**

```java
	Consumer<String> cs = (x) -> System.out.println(x + "12313");
	cs.accept("今天是周六");
	// 改为方法引用
	// 首先要注意 -> 后面的方法体的内容与 Consumer接口中的accept方法
	// 参数列表相同 并且 返回值也相同
	// public void println(String x)
	// void accept(T t);
	PrintStream ps = System.out;
	cs = ps::println;
	cs.accept("今天是周六");
```

   **2.类::静态方法名称**      		

```java
	Comparator<Integer> ct = (x, y) -> Integer.compare(x, y);
	int compare = ct.compare(5, 10);
	System.out.println(compare);
	// 方法引用 通过类名::静态方法名调用 参数列表和返回值也需要相同
	// int compare(T o1, T o2);
	// public static int compare(int x, int y)
	ct = Integer::compare;
	int compare2 = ct.compare(10, 5);
```

   **3.类::实例方法名**

```java
	BiPredicate<String, String> bp = (x, y) -> x.equals(y);
	// 如果Lambda表达式体中 第一个参数为方法的调用者 第二个参数为方法的参数
	// 就可以使用 类名:: 实例方法名称
	BiPredicate<String, String> bp0 = String::equals;
	boolean test = bp0.test("", "53");
	System.out.println(test);
```

##### 构造器引用

语法格式： **类名::new**

```java
        Supplier<emp>  sp =  ()-> new emp();
		//返回一个对象
		sp.get();
		//利用构造器引用
		sp = emp::new;
		emp emp = sp.get();
		System.out.println(emp);
```

**数组引用：    数组类型::new**

```java
Function<Integer,String[]> fc = (x) -> new String[x];
	String[] apply = fc.apply(10);
	System.out.println(apply.length);
	
	fc = String[]::new;
	String[] apply2 = fc.apply(15);
	System.out.println(apply2.length);
```

​	 			

#### **Stream API **

 在数据源与数组、集合)与结果集之间，做一系列的流水线似的操作，这样会产生一个新的Stream流，但是**不会对原来的数据源**进行更改。

##### 注意点： 

1. Stream不会自己存储元素
2. Stream不会更改数据源，相反，它会返回一个新的流。
3. Stream的操作是延迟执行的，这意味着他们会等到需要结果的时候才睡执行

##### Stream的操作步骤

1. 创建Stream

   (1)通过Collection系列集合提供的stream()(串行)或者parallelSream(并行)创建

    	  

   ```
     List<String>   list = new ArrayList<>();

     Stream<String> stream = list.stream();
   ```

   ​
     (2)通过Arrays中的静态方法stream方法获得数组流

   ```java
       Student stu[]   = new Student[10];

       Stream<Student> stream2 = Arrays.stream(stu);
   ```

     		

     (3)通过Stream类中的静态方法 of

   ```java
   Stream<Integer> of = Stream.of(245,4354,345,14645);
   //foreach() 方法需要一个Consumer 接口类型的对象
   //然后 Lambda体部分使用方法的引用   forEach(System.out::println);
   Consumer<Integer> con = (x)-> System.out.println(x);
   of.sorted((x,y)-> x-y).forEach(con);
   of.sorted((x,y)-> x-y).forEach(System.out::println);
   ```

   实际上 Stream的of()方法的参数列表的可变参数，调用的仍然是Arrays的stream()方法。

   ```java
    public static<T> Stream<T> of(T... values) {
   	return Arrays.stream(values);
   }
   ```

​	

​	(4)创建无限流 迭代

​	参数： seed  UnaryOperator函数式接口  
​	从seed开始 进行迭代

​	因为UnaryOperator接口 继承了Function接口，这里传入的第二个参数，其实用的是 Function的 R apply(T t); 方法。

  	  

```java
	Stream<Integer> iterate = Stream.iterate(0, (x) -> x+2);

	iterate.forEach(System.out::println);
```



​	(5)使用  Stream的generate同样可以达到迭代的效果 

  	

```java
//supplier接口中的T get(); 方法  `	
Stream.generate(() ->Math.random()).forEach(System.out::println);`
```

​    

#####    **Stream的筛选与切片**

​	//比较常用的API

1. filter:将流中数据进行某种条件的筛选

2. limit：截断流，给出指定数量的元素

3. skip：去掉指定数量的元素

4. distinct：去重

   ```java
    	@Test
   	public void method01() {

    		Stream<emp> stream = list.stream();
    		//筛选
    		stream.filter((x) ->Integer.parseInt(x.getId()) > 45)

    			   .forEach(System.out::println);
    	}

    	@Test
    	public void method02() {
    		IntStream stream = Arrays.stream(new int[] {1,2,3,4,5,6,7,8});
    		//跳过
    		stream.skip(2)
    			  .forEach(System.out::print);
    		//result 3 4 5 6 7 8 
    	}

   	

    	@Test
    	public void method03() {

    		IntStream stream = Arrays.stream(new int[] {1,2,3,4,5,6,7,8});
    		//截取流
    		stream.limit(3)
    			  .forEach(System.out::print);	
    		//result 1 2 3
    	}

    	
    	@Test
    	public void method04() {
    		IntStream stream = Arrays.stream(new int[] {1,2,3,4,5,6,7,8,1,1,1});
    		//去重
    		stream.filter((x) -> x<2)
    			  .distinct()
    			  .forEach(System.out::println);	
    		// 1 
    	}

   ```

   ​

##### 映射

map---接收Lambda。将元素转换成其他形式或提取信息。接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素。
flatMap---接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流。

```java

	List list = Arrays.asList("12","23","34","45");
	
	list.stream()
		.map((x) -> x.equals("12"))
		.forEach(System.out::println);
	
	输出： true false false false
```

​			
​			
​       