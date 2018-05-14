---
title: 函数式编程笔记（二）兵马再动
categories: 
- 编程语言
tags: 
- 函数式编程 
- lambda 
---

## 为什么学习函数式编程


 - 不可变性带来的好处：不会有竞态条件发生，很适合多核分布式下并发；重现使得测试调试非常方便；由于不依赖状态，可实现热部署。
   
 - 编程生态的变化：各种语言加入lambda表达式（匿名函数），写法上都是类似的箭头表达式（Python是lambda关键字）；声明式编程专注想要得到什么比命令式更细粒度的如何去实现来的简单；函数式编程语言对于数据计算更易理解，比如sql和大数据方面的map,reduce等

## 高阶函数

需求：计算f(x,y)=x*x=y*y
Java实现：

```java
public class SumSquare {
    public static void main(String[] args) {
        IntFunction<Integer> square = x -> x * x;
        Supplier<Integer> out = sum_square(square, 3, 4);
        System.out.println(out.get());//输出25

    }

    private static Supplier<Integer> sum_square(IntFunction square, int a, int b) {
      return () -> {
          return (int) square.apply(a) + (int) square.apply(b);
      };
    }
}
```
是不是一头雾水，如果把上述square.apply(a)写成square(a)是不是就有点常规认识的函数调用呢？事实上，在Scala里，函数式编程就是上述的square（a）,此处（）是进行重载得到的（java对基本的运算符不能重载，只能用Function实现的apply方法，但有什么关系，谁又没规定函数调用必须是square（a）,sun认为square.apply(a)是就是喽），但烦人的不仅如此，我猜java不支持模式匹配（类似于switch，但可以匹配更复杂的，比如可匹配函数），于是就定义了各种各种的函数式接口，函数式接口是只有一个方法的接口，实现它就可以以函数为一等公民进行编程（明显是披着函数的对象嘛），比如上述out.get()不接受参数返回表达式，这点从声明的泛型上明显的看出来。那Python里函数是纯纯的好喝的吗（类似c那种），其实只要对象实现__call__方法，那这个对象就是函数，反过来说，一切函数皆对象。至于js里的函数是不是也可以理解成含有apply call bind方法的对象呢,这三可以改变this指向，如果this指向确定（包括宿主环境对象如window对象），可以类似f()调用，如果this不确定，还得使用f.apply(target,...)(target是所属对象)调用。c++函数对象也是重载（）符号完成对象到函数对象的转变（函数指针对于高阶的这两条也能工作的很好）。

 - 进阶三板斧（filter，map,reduce）

需求：筛选十以内的偶数并计算平方和
java：

```
import java.util.stream.Stream;

/*v筛选十以内的偶数并计算平方和*/
public class FilterMapreduceTest {
    
//java8 以前
    private static int sumEven() {
        int result = 0;
        for (int i = 1; i < 11; i++) {
            if (i % 2 == 0) {
                result += i * i;
            }
        }
        return result;
    }
    
    //java8
    private static  int newSumEven(){
        return Stream.iterate(1, i -> i + 1).limit(10)//无限流，惰性求值才有可能产生无限流
              //  .parallel()      //1
                .filter(i -> i % 2 == 0) //filter里面是一个谓词高阶函数，返回满足条件的序列
                .map(i -> i * i).peek(System.out::println) //map里面是一个高阶函数，每个元素做出映射
                .reduce(0, (a, b) -> a + b); //第二个参数是两个参数的高阶函数，归约求和
    }
    //上面两个方法所占代码行数相差无几，但是当业务逻辑比如条件类似sql里where复杂查询时，newSumEven就会体现出代码优势，由于基于流的操作，同时性能也会提高
    public static void main(String[] args) {
        System.out.println("结果：" + newSumEven());
        System.out.println("传统结果：" + sumEven());
    }
}
```
上述代码运行在单线程下，通过添加注释1转为并行流，其背后是fork/join框架，默认线程为核数。流不同于传统的序列操作强大的地方在于其类似流水线的生产方式![这里写图片描述](http://img.blog.csdn.net/20170904185703182?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

1在进行reduce的时候2有可能进行map,10有可能进行filter，也就是说逻辑上只要没有时序关系，他们就有可能并行执行顺序也不确定。可以peek()打印下看每一步的输出，这也是流调试常用的手段。
Python

```Python
source=range(1,11)
sourceFilter=filter(lambda i:i%2==0,source)
sourceMap=map(lambda i:i**2,sourceFilter)
sourceReduce=reduce(lambda a,b:a+b,sourceMap)
print(sourceReduce)//输出220
```
js中操作dom时数组或集合有很多的高阶函数find(),head(),sort()，foreach()等

```js
[1,2,3,4,5,6,7,8,9,10]
.filter(x=>x%2==0)
.map(x=>x*x)
.reduce((a,b)=>a+b,0)//输出220
```
有了这三板斧，像其他的max(),min(),sum(),first()等等很容易自己就写出来，对于像序列这种操作还有一个非常实用的zip（拉链或者配对）,可用于生成key-value对。

 - 面向对象里多态行为是通过重写实现的，于是GOF的设计模式大行其道，当行为参数化以后，java可以通过匿名类简化具名子类的数量，只是模板代码太多，当支持函数式编程后，好多设计模式都变得异常简单。

来看看经典的例子：

```java
public class FactoryPatternDemo {
    public static void main(String[] args) {
        ShapeFactory shapeFactory = new ShapeFactory();
        Shape shape1 = shapeFactory.getShape("CIRCLE");
        shape1.draw();//输出Inside Circle::draw() method.
        Shape shape2 = shapeFactory.getShape("RECTANGLE");
        shape2.draw();//输出Inside Rectangle::draw() method.
    }
}

interface Shape {
    void draw();
}

class Rectangle implements Shape {
    @Override
    public void draw() {
        System.out.println("Inside Rectangle::draw() method.");
    }
}

class Circle implements Shape {
    @Override
    public void draw() {
        System.out.println("Inside Circle::draw() method.");
    }
}

class ShapeFactory {
    //use getShape method to get object of type shape
    public Shape getShape(String shapeType) {
        if (shapeType == null) {
            return null;
        }
        if (shapeType.equalsIgnoreCase("CIRCLE")) {
            return new Circle();
        } else if (shapeType.equalsIgnoreCase("RECTANGLE")) {
            return new Rectangle();
        }
        return null;
    }
}
```

> 要想不这么死，就得换个活法 --《绣春刀2》

来看看重构后的代码：

```java
public class FactoryPatternDemo {
    public static void main(String[] args) {
        ShapeFactory shapeFactory = new ShapeFactory();
        shapeFactory.getShape("CIRCLE").draw();//输出Inside Circle::draw() method.
        shapeFactory.getShape("RECTANGLE").draw();//输出Inside Rectangle::draw() method.
    }
}

interface Shape {
    void draw();
}

class ShapeFactory {
    //use getShape method to get object of type shape
    public Shape getShape(String shapeType) {
        if (shapeType == null) {
            return null;
        }
        if (shapeType.equalsIgnoreCase("CIRCLE")) {
            return ()->System.out.println("Inside Circle::draw() method.");
        } else if (shapeType.equalsIgnoreCase("RECTANGLE")) {
            return ()-> System.out.println("Inside Rectangle::draw() method.");
        }
        return null;
    }
}
```
重构后采用()-> System.out.println("Inside Rectangle::draw() method.")直接实现Shape接口，减少代码量

## 科里化

需求：对于多参数的函数调用能不能分别赋予自变量的值呢
科里化1是一种将具备2个参数(比如，x和y)的函数f转化为使用一个参数的函数g，并 且这个函数的返回值也是一个函数，它会作为新函数的一个参数。后者的返回值和初始函数的 返回值相同，即f(x,y) = (g(x))(y)。
一个经典的页面刷新的例子：

```
function update(item){
        return function (text){
            $("div#"+item).html(text);
        }
    }
    function refresh(url, callback){
        var params = ;
        $.ajax({
                type:"post",
                url:url,
                data:{ "key" : "value" },
                success:  function (data, status){
                    callback(data);
                 }
       
        });
    }
    refresh("url", update("div_1"));
```
科里化是模块化函数、提高代码重用性的技术。

## 闭包

需求：js中怎么才能用函数实现对象，即封装属性提供访问接口
 - 闭包：一个拥有许多变量和绑定了这些变量的环境的表达式（通常是一个函数），因而这些变量也是该表达式的一部分。总的来说，支持闭包的语言在函数执行出栈后其中的数据被保留了下来，并没有被gc,java8支持受限的闭包（不能修改非本地变量），js,python,scala均支持闭包

```js
　　function a(x){
		function b(y){return x+y;}
		return b;
	}
	var c=a(3);
	c(4);
```
这里c(4)每次执行时Chrome的闭包显示是a,其中x=3,闭包其实是自由变量x,以及表达式x+y,（chrome显示的a，是其x以及x+y的边界域，感觉a才是真正的闭包域啊），闭包可以保存函数里的数据，所以科里化才变得有意义，要不然每次调用都还是多参数。闭包在js的地位比其他语言更受宠，估计是为了要面向对象，闭包可以很好的实现成员的封装，并提供对外的访问接口，类似setter/getter那样

## 结合器

需求：实现复合函数，如果有函数f(x),g(x),可以复合为f(g(x))或者g(f(x))

```java
public static void main(String[] args) {
        Function<Integer,Integer> square=x->x*x;
        Function<Integer,Integer> cube=x->3*x*x*x;
        Integer result1 = square.compose(cube).apply(2);//compose复合器
        Integer result2  = cube.compose(square).apply(2);
        Integer result3 = square.andThen(x -> 4* x).apply(5);//andThen

        System.out.println("先cube再square："+result1);//先cube再square：576
        System.out.println("先square再cube："+result2);//先square再cube：192
        System.out.println("先square再5x："+result3);//先square再5*x：100

    }
```
需要好多步骤处理某种资源，比如拦截器拦截url时，写成这样的.的形式更加的可读代码也很清爽。

## 递归与迭代

需求：阶乘实现

```java
//阶乘计算
public class Factorial {
    public static void main(String[] args) {
        System.out.println("迭代：" + factorialIterative(10));
        System.out.println("递归：" + factorialRecursive(10));
        System.out.println("尾递归" + factorialHelper(1, 10));
    }

    //迭代式
    static int factorialIterative(int n) {
        int r = 1;
        for (int i = 1; i <= n; i++) {
            r *= i;
        }
        return r;
    }

    //递归式
    static long factorialRecursive(long n) {
        return n == 1 ? 1 : n * factorialRecursive(n - 1);
    }

    //尾递归
    static long factorialHelper(long acc, long n) {
        return n == 1 ? acc : factorialHelper(acc * n, n - 1);
    }
    //java8 流
    static long factorialStreams(long n){
        return LongStream.rangeClosed(1, n)
                         .reduce(1, (long a, long b) -> a * b);
}
}
```
既然迭代会修改值（比如i++），采用递归又会开启对个嵌套栈，很容易StackOverflow，所以如果把递归放到函数返回，也就是最后一步，并且把记录状态的值传递给它，那么这个函数栈之后也不会使用，可以把它清掉或者新开的函数重用此调用栈，此时就成了伪递归，Scala对此进行了优化Scala，提供的尾递归注解@scala.annotation.tailrec可以帮助我们检查是否是尾递归，很遗憾java目前没有支持，不过可以以流reduce实现它.es6在严格模式下也会开启尾递归优化

## 持久化数据结构（不可变）

需求：如何保证自己写的程序不会修改意料之外的值呢，函数式编程重在不可变的思想，因此尽量使用不可变的数据结构吧
这里的持久化指的是数据结构的值始终保持一致，不受其他部分变化的影响（而不是保留值到内存或者是硬盘的持久化）。函数式方法不允许修改任何全局数据结构或者任何作为参数传入的参数。

```
package studyJava8;

import java.util.*;

public class Prototype {
    public static void main(String[] args) {
        List<Integer> sources = Arrays.asList(1, 7, 5, 4, 6);
        System.out.println("函数式更新"+operate2(sources)+"源"+sources);
       System.out.println("破坏式操作结果："+operate(sources)+"源"+sources);
       //函数式更新[1, 4, 5, 6, 7]源[1, 7, 5, 4, 6]
//破坏式操作结果：[1, 4, 5, 6, 7]源[1, 4, 5, 6, 7]
    }

    private static List<Integer> operate(List<Integer> sources) {
         Collections.sort(sources);
         return sources;
    }
    private static List<Integer> operate2(List<Integer> sources) {
        List<Integer> dest=new ArrayList<>();
        dest.addAll(sources);
        Collections.sort(dest);
        return dest;
    }
}

```
可以看到破坏时更新时意外的更新了sources的值，这个例子可能无关痛痒，但如果在多处使用这个值，破坏式更新使得其值已经不可用。所以建议编写程序时使用函数式更新，这个时候原型模式非常有用，也要留意意浅拷贝与深拷贝，如果对象比较大，深度拷贝可使用二进制拷贝加快对象的创建。

## 模式匹配

需求：还记得数学归纳法吗？

```
f(0) = 1
f(n) = n*f(n-1) 
```
能不能根据参数的不同选择不同的函数呢？if-else当然可以，如果情况更加复杂，代码将变得非常复杂，模式匹配可以很好的简化（这里模式匹配并不是正则表达式的匹配）java的switch只支持数值型和String，对于不同函数的选择可以用if-else或者不同的子类通过多态进行选择

```
var  sign=0
val ch:Char='+'
ch match {
	case '+' => sign = 1
	case '-' => sign = -1
	case _ => sign = 0 //其他值，相当于switch的default 
}
/*当然Scala里支持类型匹配，数组等序列匹配，更复杂的函数匹配（根据函数签名和返回值的不同选择不同的函数，好强大）*/
/*更复杂的模式匹配，BinOp是Expr的子类，检查expr是否为BinOp，抽取它的三个组成部分(opname、left、 right)，紧接着对这些组成部分分别进行模式匹配——第一个部分匹配String+，第二个部分匹配变量e(它总是匹配)，第三个部分匹配模式Number(0)。*/
def simplifyExpression(expr: Expr): Expr = expr match {
	case BinOp("+", e, Number(0)) => e
	case BinOp("*", e, Number(1)) => e
	case BinOp("/", e, Number(1)) => e
	case _ => expr
}
```
代码详见：https://github.com/aloneload/FunctionTest/
