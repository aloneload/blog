---
title: JDK各个版本特性介绍
categories: 
- 编程语言
tags: 
- java 
- lambda 
---
jdk8
----

 

 - Lambda 表达式 − Lambda允许把函数作为一个方法的参数（函数作为参数传递进方法中。

```
package jdk8;

public class LambdaPractice {

	public static void main(String[] args) {

		MathOperation addition = (int a, int b) -> a + b;
		MathOperation subtraction = (a, b) -> a - b;
		MathOperation multiplication = (int a, int b) -> {
			return a * b;
		};
		MathOperation division = (int a, int b) -> a / b;

		System.out.println("60 + 12 = " + operate(60, 12, addition));
		System.out.println("60 - 12 = " + operate(60, 12, subtraction));
		System.out.println("60 x 12 = " + operate(60, 12, multiplication));
		System.out.println("60 / 12 = " + operate(60, 12, division));

	}

	private static int operate(int a, int b, MathOperation mathOperation) {
		return mathOperation.operation(a, b);
	}
}

@FunctionalInterface
interface MathOperation {
	int operation(int a, int b);
}
```

 - 方法引用−方法引用提供了非常有用的语法，可以直接引用已有Java类或对象（实例）的方法或构造器。与lambda联合使用，方法引用可以使语言的构造更紧凑简洁，减少冗余代码。

```
构造器引用：它的语法是Class::new
静态方法引用：它的语法是Class::static_method
特定类的任意对象的方法引用：它的语法是Class::method
特定对象的方法引用：它的语法是instance::method
```
 

 - 默认方法 − 默认方法就是一个在接口里面有了一个实现的方法。
**默认方法就是接口可以有实现方法，而且不需要实现类去实现其方法。只需在方法名前面加个default关键字即可实现默认方法**

```
package jdk8;

public class DefaultAndStaticMethod implements A {

	public static void main(String[] args) {
		DefaultAndStaticMethod defaultAndStaticMethod = new DefaultAndStaticMethod();
		// 调用默认方法
		defaultAndStaticMethod.print();
		//调用静态方法
		A.born();
		//调用继承的默认方法
		defaultAndStaticMethod.inherit();
		
	}
	@Override
	public void inherit() {
		System.out.println("继承自A的方法");
	}

}

interface A {
	default void print() {
		System.out.println("我是A默认方法");
	}
	default  void inherit() {
		System.out.println("我是A需要被继承的方法");
	}

	static void born() {
		System.out.println("我是A静态方法");
	}
}
```


 - Stream API −新添加的Stream API（java.util.stream） 把真正的函数式编程风格引入到Java中。
 
 增加了一些新的 IO/NIO 方法，使用这些方法可以从文件或者输入流中获取流（java.util.stream.Stream），通过对流的操作，可以简化文本行处理、目录遍历和文件查找。
新增的 API 如下：
BufferedReader.line(): 返回文本行的流 Stream<String>
File.lines(Path, Charset):返回文本行的流 Stream<String>
File.list(Path): 遍历当前目录下的文件和目录
File.walk(Path, int, FileVisitOption): 遍历某一个目录下的所有文件和指定深度的子目录
File.find(Path, int, BiPredicate, FileVisitOption... ): 查找相应的文件
*见http://blog.csdn.net/aloneload/article/details/78091860*
 - Date Time API − 加强对日期与时间的处理。

**java.time 中包含了所有关于时钟（Clock），本地日期（LocalDate）、本地时间（LocalTime）、本地日期时间（LocalDateTime）、时区（ZonedDateTime）和持续时间（Duration）的类**

```
public class DateTimeApi {

	public static void main(String[] args) throws InterruptedException {
		test();
	}

	private static void test() throws InterruptedException {
		// LocalDate
		LocalDate localDate = LocalDate.of(2017, Month.OCTOBER, 1);// 2017-10-01
		int year = localDate.getYear(); // 2017
		Month month = localDate.getMonth(); // OCTOBER
		int dom = localDate.getDayOfMonth(); // 1
		DayOfWeek dow = localDate.getDayOfWeek(); // SUNDAY
		boolean leap = localDate.isLeapYear(); // false （不是闰年）
		// LocalTime
		LocalTime time = LocalTime.of(14, 32); // 14:32
		int hour = time.getHour(); // 14
		int minute = time.getMinute(); // 32
		time = time.withSecond(6); // 14:32:06
		time = time.plusMinutes(3); // 20:35:06
		// LocalDateTime
		LocalDateTime localDateTime = LocalDateTime.now();
		// of： 静态工厂方法，从组成部分中创建实例
		// now： 静态工厂方法，用当前时间创建实例
		// parse： 静态工厂方法，总字符串解析得到对象实例
		// with： 返回一个部分状态改变了的时间日期对象拷贝
		// plus： 返回一个时间增加了的、时间日期对象拷贝
		// minus： 返回一个时间减少了的、时间日期对象拷贝
		// at： 用当前时间日期对象组合另外一个，创建一个更大或更复杂的时间日期对象
		// format： 提供格式化时间日期对象的能力
		// Instance时刻
		Instant start = Instant.now();
		Thread.sleep(1001);
		Instant end = Instant.now();
		System.out.println("end-start=" + (end.compareTo(start)));
		// Duration period 时间段 可作为参数进行日期时间的运算
		Duration duration = Duration.of(1, ChronoUnit.HOURS);
		Period period = Period.of(1, 2, 3);
		System.out.println(duration + period.toString());
		// 格式化
		DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy/MM/dd");
		LocalDate localDate2 = LocalDate.parse("2017/10/01", dateTimeFormatter);
		String date = localDate2.format(dateTimeFormatter);
		System.out.println();
		// ZoneId
		ZoneId zoneId = ZoneId.of("America/Caracas");
		LocalDate date2 = localDate.now(zoneId);
		System.out.println(date2);

	}

}
```

 - Optional 类 − Optional 类已经成为 Java 8 类库的一部分，用来解决空指针异常。

Optional将对象和null包装起来，使得null有迹可循

```
package jdk8;

import java.util.Optional;

public class OptionalTest {

	public static void main(String[] args) {
		test();
	}

	private static void test() {
		Optional<String> optional = Optional.of("java8");
		// Optional<String> optional2 = Optional.of(null); 报错
		Optional<String> optional2 = Optional.ofNullable(null);
		String name = optional2.orElse("default");
		System.out.println("name:" + name);// 输出default
		// 如果不包装，将得不到有用的信息
		// optional2.get();// ava.util.NoSuchElementException: No value present

		// orElseThrow与orElse方法类似，区别在于返回值。
		// orElseThrow抛出由传入的lambda表达式/方法生成异常。
		try {
			optional2.orElseThrow(Exception::new);
		} catch (Throwable ex) {
			System.out.println(ex.getMessage());
		}

		// lambda表达式返回值会包装为Optional实例。
		Optional<String> upperName = optional.map((value) -> value.toUpperCase());
		// 如果满足返回Optional实例值，否则返回空Optional。
		Optional<String> longName = optional2.filter((value) -> value.length() > 6);

	}

}
```

 - Nashorn, JavaScript 引擎 − Java 8提供了一个新的Nashorn
       javascript引擎，它允许我们在JVM上运行特定的javascript应用。
 - 新工具 − 新的编译工具，如：Nashorn引擎 jjs、 类依赖分析器jdeps。

```
jdeps LambdaPractice.class
```
![这里写图片描述](http://img.blog.csdn.net/20170926154056708?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
jdk7
----

 - 在Switch中可用String

```
String s = "java8";
		switch (s) {
		case "java8":
			System.out.println("java8");
			break;
		case "java7":
			System.out.println("java7");
			break;
		default:
			System.out.println("default");
			break;
		}
```
 - 数值可加下划线用作分隔符（编译时自动被忽略）
```
 int add=1_342_000;
```
  
 - 增强的try
资源管理器自动调用资源的close()函数。和Python里的with语句差不多,再也不用担心忘关资源了
```
// try-with-resource
		try (BufferedReader br = new BufferedReader(new FileReader("data/nio-data.txt"))) {
			
			String line;
			while((line=br.readLine())!=null) {
			System.out.println(line);
			}
		} catch (IOException|NumberFormatException e) {//此处演示多个异常
			e.printStackTrace();
		}
```
***另网传的对Java集合（Collections）的增强支持，可直接采用[]、{}的形式存入对象，采用[]的形式按照索引、键值来获取集合中的对象。此特性并不支持***


jdk5
----

 - 自动装箱与拆箱

```
int a = 4;
Integer b = 5;// 装箱 5-》integer	System.out.println(a + b);// 拆箱 //计算 int 的4+5
```

 - 枚举
单例模式最好的实现

```
enum Version {
	JDK8(8), JDK7(7);
	private int attibute;
	public int getAttibute() {
		return this.attibute;
	}
	private Version(int attibute) {
		this.attibute = attibute;
	}
//使用Version.JDK8.getAttibute();得到属性


```


 - 静态导入

```
import static java.lang.Math.*;
```

 - 可变参数
```
// 可变参数
	private static int sum(int... intlist) {
		int sum;
		sum = 0;
		for (int i = 0; i < intlist.length; i++) {
			sum += intlist[i];
		}
		return sum;

	}
	//System.out.println(sum(1, 2, 3));
	//System.out.println(sum(1, 2));
```
 - 内省，主要用于操作JavaBean中的属性，通过getXxx/setXxx。一般的做法是通过类Introspector来获取某个对象的BeanInfo信息，然后通过BeanInfo来获取属性的描述器（PropertyDescriptor），通过这个属性描述器就可以获取某个属性对应的getter/setter方法，然后我们就可以通过反射机制来调用这些方法。

```
People people = new People(1,"zhangsan");
		BeanInfo beanInfo=Introspector.getBeanInfo(people.getClass());
		PropertyDescriptor[] pds = beanInfo.getPropertyDescriptors();
		for (PropertyDescriptor propertyDescriptor : pds) {
			Object out2 = propertyDescriptor.getReadMethod().invoke(people);
			System.out.println(out2);
		}
```

 - 泛型（包括通配类型/边界类型等）
 - For-Each循环
 - 注解

> GitHub：https://github.com/aloneload/JdkFeatures
