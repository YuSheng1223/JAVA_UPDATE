# java8
学习java8的新特性
### Oracle在2014年更新了 java8，java8 支持函数式编程。包括新的日期API，新的Stream API等等。
在java8中，最重要的两个特性就是Lambda表达式和Stream API。
	<br>java8相较于java7以前的版本：
			<br>1.速度更快
			<br>（1）,底层数据结构
			<br>java8对底层的HashMap结构进行了优化，在java7中，HashMap的底层结构是数组+链表的形式。而在java8中，HashMap的底层优化为了数组+链表+红黑树的形式。这种形式除了在新增元素的时候较java7性能低一些，但是在查找、删除等方面有了很大的性能提高。
			<br>（2）,底层的内存结构
			<br>在java7中，方法区实际上是堆的一部分，处于方法区的永久区(PremGen)中，包含了所有的class和static变量。同时，这部分几乎不会被垃圾回收机制回收，简单来说就是回收条件比较苛刻。而在java8中，将方法区从堆中剥离了出来。重新建立了MetaSpace元空间，当元空间快要满的时候，垃圾回收机制就开始工作了。
			
			
### 为什么要使用Lambda？
<br> * 在java8以前 我们如果要使用一个接口中的方法，必须要新建一个类来实现这个接口 或者利用匿名内部类来调用
<br> * 
<br> * 但是如果有个Lambda表达式  就会很方便。我们只需要将匿名内部类中真正有用的几行代码提取出来就可以了。
<br> * 
<br> * 总的来说 对于要使用接口中方法，又不想去实现这个接口 我们可以使用Lambda
<br> * 
<br> * 在使用Lambda的过程中 语法是需要注意的一点
<br> * 在java8中引入了新的操作符  ->
<br> *     ->  左侧 ： 为Lambda表达式的参数列表
<br> *              如果没有参数  就只用一个()  代替即可
<br> *         右侧  ： 右侧为Lambda表达式所需要执行的功能，即Lambda体
<br> *         	    有个右侧有多行的代码，需要用{}括起来    
<br> *              如果接口中方法需要返回值 则之间在{}内书写return即可 
<br> *		   
<br> * 同时，在-> 的左侧参数列表部分，我们对于参数类型的指定可有可无。JVM可以通过上下文来推断出参数的数据类型。

<br> Lambda表达式 需要函数式接口的支持。
<br>函数式接口指的是只有一个抽象方法的接口，我们可以在接口上加上@FunctionalInterface注解，来验证是不是函数式接口。



四大内置函数式接口
---------

 1. 消费性接口   Consumer<T>

     void accept(T t); 有参数 但是无返回值
     对类型为T的对象进行操作

 2. 供给型接口 Supplier<T>
      T get();     无参数  返回一个T对象
     返回类型为T的对象

 3. 函数型接口 Function<T,R>
        R apply(T t);  有参数 有返回值
    对类型为T的对象进行操作，并且返回结果。结果为类型是R的对象

 4. 断定型接口   Predicate<T>
      boolean test(T t);  有参数 有返回值
    判断类型为T的对象是否满足某种约束  返回boolean类型的值

在java8中不仅仅只有这四个函数式接口，还有诸如 BiFunciton<T,U,R>,BiCunstorm<T,U>等函数式接口。合理应用每个接口才是重中之重。



**### 方法的引用和构造器引用**

<br> 方法的引用:     如果Lambda表达式方法体中的已经实现，我们就可以使用方法引用。
<br>    三种语法格式:
<br>    1.对象::实例方法
<br> 	
	
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
		
<br>   2.类::静态方法名称
      		
		Comparator<Integer> ct = (x, y) -> Integer.compare(x, y);
		int compare = ct.compare(5, 10);
		System.out.println(compare);
		// 方法引用 通过类名::静态方法名调用 参数列表和返回值也需要相同
		// int compare(T o1, T o2);
		// public static int compare(int x, int y)
		ct = Integer::compare;
		int compare2 = ct.compare(10, 5);
<br>   3.类::实例方法名
    		
		BiPredicate<String, String> bp = (x, y) -> x.equals(y);
		// 如果Lambda表达式体中 第一个参数为方法的调用者 第二个参数为方法的参数
		// 就可以使用 类名:: 实例方法名称
		BiPredicate<String, String> bp0 = String::equals;
		boolean test = bp0.test("", "53");
		System.out.println(test);
构造器引用
---

语法格式： 类名::new

	

            Supplier<emp>  sp =  ()-> new emp();
    		//返回一个对象
    		sp.get();
    		//利用构造器引用
    		sp = emp::new;
    		emp emp = sp.get();
    		System.out.println(emp);
    		
    		
数组引用：    数组类型::new


	

    Function<Integer,String[]> fc = (x) -> new String[x];
    	String[] apply = fc.apply(10);
    	System.out.println(apply.length);
    	
    	fc = String[]::new;
    	String[] apply2 = fc.apply(15);
    	System.out.println(apply2.length);
	
	 			

			
			
       
