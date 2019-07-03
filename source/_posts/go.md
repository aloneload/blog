---
title: go语言介绍
categories: 
- 编程语言
tags: 
- go 
 
---
## go语言特性介绍
- go是一门支持并发编程和内存垃圾回收的编译型静态类型语言
- go是以软件工程为目的的语言设计，同大多数通用型编程语言相比，清晰简洁高效
  1. 清晰的依赖关系
  1. 清晰的语法
  1. 清晰的语义
  1. 偏向组合而不是继承
  1. 编程模型（垃圾回收、并发）的简单性
  1. 易于为它编写工具（gotool/gofmt/godoc/gofix）
  
## 依赖处理
- 在编译阶段，将未被使用的依赖视为错误
- import 导入代码而不是整个库，清晰依赖关系要比代码重用重要

## 语法命名
- 语法就是编程语言的用户界面
- 首字母大写：名字对于包使用者可见
- 其余：名字对于包使用者不可见

## 并发
- 不要通过共享内存来通信，要通过通信来共享内存

## 要组合，不要继承
- 面向对象支持：允许添加方法到任意类型，没有类型体系
- 接口组合

## 错误
if和return处理错误
```
f, err := os.Open(fileName)
if err != nil {
    return err
}
```

## 工具 
- gofmt 格式化代码
- gofix 重构

## 包与导入导出
- 每个 Go 程序都是由包构成的
- 程序从 main 包开始运行
- 按照约定，包名与导入路径的最后一个元素一致
-使用分组导入语句是更好的形式
```
import (
	"fmt"
	"math"
)

```
- 在 Go 中，如果一个名字以大写字母开头，那么它就是已导出的
- 在导入一个包时，你只能引用其中已导出的名字

## 函数
- 类型在变量名之后
- 函数可以返回任意数量的返回值
- 函数也是值。它们可以像其它值一样传递
- 函数值可以用作函数的参数或返回值
```
func swap(x, y string) (string, string) {
	return y, x
}
```
-- 在使用闭包访问外部变量的时候，问问你自己这个变量时候会发生改变，这样的改变对闭包的运行有什么影响。

## 变量
- var 语句用于声明一个变量列表
- var 语句可以出现在包或函数级别
- 在函数中，简洁赋值语句 := 可在类型明确的地方代替 var 声明

## 基本类型
```
bool
string
int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr
byte // uint8 的别名
rune // int32 的别名
    // 表示一个 Unicode 码点
float32 float64
complex64 complex128
```
- 零值
 1. 数值类型为 0
 1. 布尔类型为 false
 1. 字符串为 ""（空字符串）
- 表达式 T(v) 将值 v 转换为类型 T
- 常量的声明与变量类似，只不过是使用 const 关键字
- 零值是非常有用的。如 bytes.Buffer的文档所述 “Buffer 的零值是一个准备好了的空缓冲。” 类似的，sync.Mutex 也没有明确的构造函数或 Init 方法。取而代之，sync.Mutex 的零值被定义为非锁定的互斥量。 

## for
- 基本的 for 循环由三部分组成，它们用分号隔开：
  - 初始化语句：在第一次迭代前执行(可选)
   - 条件表达式：在每次迭代前求值（false退出）
  - 后置语句：在每次迭代的结尾执行（可选）
 
 - 无限循环
 ```
 for {
	}
 ```
 - if同 for 一样， if 语句可以在条件表达式前执行一个简单的语句
  - switch 是编写一连串 if - else 语句的简便方法。它运行第一个值等于条件表达式的 case 语句
  - switch 的 case 语句从上到下顺次执行，直到匹配成功时停止
  - 没有条件的 switch 同 switch true 一样。这种形式能将一长串 if-then-else 写得更加清晰
  ```
  func main() {
	t := time.Now()
	switch {
	case t.Hour() < 12:
		fmt.Println("Good morning!")
	case t.Hour() < 17:
		fmt.Println("Good afternoon.")
	default:
		fmt.Println("Good evening.")
	}
}
  
  ```
 

- defer 使用规则（golang当中，defer代码块会在函数调用链表中增加一个函数调用。这个函数调用不是普通的函数调用，而是会在函数正常返回，也就是return之后添加一个函数调用。因此，defer通常用来释放函数内部变量。）
  1. defer被声明时，其参数就会被实时解析
  1. defer执行顺序为先进后出
  1. defer可以读取有名返回值
  
## 指针
 - 类型 *T 是指向 T 类型值的指针。其零值为 nil
 - & 操作符会生成一个指向其操作数的指针
 * 操作符表示指针指向的底层值
 
## 结构体
 - 一个结构体（struct）就是一个字段的集合
 - 结构体字段使用点号来访问
 - 结构体字段可以通过结构体指针来访问`p := &v    p.X = 1e9`
 ```
 v1 = Vertex{1, 2}  // has type Vertex
	v2 = Vertex{X: 1}  // Y:0 is implicit
	v3 = Vertex{}      // X:0 and Y:0
	p  = &Vertex{1, 2} // has type *Vertex
 
 ```
 
## 数组与切片
 - 类型 [n]T 表示拥有 n 个 T 类型的值的数组
 - 类型 []T 表示一个元素类型为 T 的切片
 - 切片通过两个下标来界定，即一个上界和一个下界，二者以冒号分隔
 - 切片就像数组的引用
 - 在进行切片时，你可以利用它的默认行为来忽略上下界
 - 切片 s 的长度和容量可通过表达式 len(s) 和 cap(s) 来获取
 - 切片可以用内建函数 make 来创建 `a := make([]int, 5)  // len(a)=5`
 - 为切片追加新的元素是种常用的操作，为此 Go 提供了内建的 append 函数`func append(s []T, vs ...T) []T`
 - for 循环的 range 形式可遍历切片或映射
 
## map
 - 映射的文法与结构体相似，不过必须有键名
 - 在映射 m 中插入或修改元素`m[key] = elem`
 - 获取元素 `elem = m[key] `
 - 删除元素`delete(m, key)` 
 - 通过双赋值检测某个键是否存在 `elem, ok = m[key]`
 
## 方法
 - Go 没有类。不过你可以为结构体类型定义方法 
 - 方法就是一类带特殊的 接收者 参数的函数
 - 方法只是个带接收者参数的函数
 - 就是接收者的类型定义和方法声明必须在同一包内；不能为内建类型声明方法
 - 你可以为指针接收者声明方法
 - 带指针参数的函数必须接受一个指针
 - 而以指针为接收者的方法被调用时，接收者既能为值又能为指针（传值无区别，调用有区别）
 - 使用指针接收者的原因有二：
   1. 首先，方法能够修改其接收者指向的值
   2. 其次，这样可以避免在每次调用方法时复制该值
 

## 接口
- 接口类型 是由一组方法签名定义的集合
- 接口类型的值可以保存任何实现了这些方法的值
- 隐式接口从接口的实现中解耦了定义，这样接口的实现可以出现在任何包中，无需提前准备
- 在内部，接口值可以看做包含值和具体类型的元组`(value, type)`接口值调用方法时会执行其底层类型的同名方法
- 即便接口内的具体值为 nil，方法仍然会被 nil 接收者调用,在一些语言中，这会触发一个空指针异常,Go 中通常会写一些方法来优雅地处理它
```
func (t *T) M() {
	if t == nil {
		fmt.Println("<nil>")
		return
	}
	fmt.Println(t.S)
}
```
- nil 接口值既不保存值也不保存具体类型
- 指定了零个方法的接口值被称为 *空接口：*`interface{}`
- 类型断言 提供了访问接口值底层具体值的方式。`t := i.(T)``t, ok := i.(T)//不会panic`
- 类型选择 是一种按顺序从几个类型断言中选择分支的结构
```
switch v := i.(type) {
case T:
    // v 的类型为 T
case S:
    // v 的类型为 S
default:
    // 没有匹配，v 与 i 的类型相同
}

```
- fmt 包中定义的 Stringer 是最普遍的接口之一
```
type Stringer interface {
    String() string
}
```
- error
```
type error interface {
    Error() string
}
```
- 通常函数会返回一个 error 值，调用的它的代码应当判断这个错误是否等于 nil 来进行错误处理
```
i, err := strconv.Atoi("42")
if err != nil {
    fmt.Printf("couldn't convert number: %v\n", err)
    return
}
fmt.Println("Converted integer:", i)
```
- io.Reader 接口有一个 Read 方法
```
func (T) Read(b []byte) (n int, err error)

```
- 在遇到数据流的结尾时，它会返回一个 io.EOF 错误

## goroutine
- Go 运行时管理的轻量级线程
- go go f(x, y, z) 会启动一个新的 Go 程并执行,x, y 和 z 的求值发生在当前的 Go 程中，而 f 的执行发生在新的 Go 程中。
- 信道是带有类型的管道，你可以通过它用信道操作符 <- 来发送或者接收值
```
ch <- v    // 将 v 发送至信道 ch。
v := <-ch  // 从 ch 接收值并赋予 v。

```
- 和映射与切片一样，信道在使用前必须创建`ch := make(chan int)`
- 默认情况下，发送和接收操作在另一端准备好之前都会阻塞。这使得 Go 程可以在没有显式的锁或竞态变量的情况下进行同步
- 信道可以是 带缓冲的。将缓冲长度作为第二个参数提供给 make 来初始化一个带缓冲的信道`ch := make(chan int, 100) `
- 仅当信道的缓冲区填满后，向其发送数据时才会阻塞。当缓冲区为空时，接受方会阻塞
- 接收者可以通过为接收表达式分配第二个参数来测试信道是否被关闭：若没有值可以接收且信道已被关闭，那么在执行完`v, ok := <-ch` ok 会被设置为 false。
- 循环 `for i := range c `会不断从信道接收值，直到它被关闭
- select 语句使一个 Go 程可以等待多个通信操作
- select 会阻塞到某个分支可以继续执行为止，这时就会执行该分支。当多个分支都准备好时会随机选择一个执行
- 为了在尝试发送或者接收时不发生阻塞，可使用 default 分支
```
select {
case i := <-c:
    // 使用 i
default:
    // 从 c 中接收会阻塞时执行
}
```

## sync.Mutex 
- Go 标准库中提供了 sync.Mutex 互斥锁类型及其两个方法`Lock Unlock`
- 可以通过在代码前调用 Lock 方法，在代码后调用 Unlock 方法来保证一段代码的互斥执行
- 可以用 defer 语句来保证互斥锁一定会被解锁 
```
// SafeCounter 的并发使用是安全的。
type SafeCounter struct {
	v   map[string]int
	mux sync.Mutex
}

// Inc 增加给定 key 的计数器的值。
func (c *SafeCounter) Inc(key string) {
	c.mux.Lock()
	// Lock 之后同一时刻只有一个 goroutine 能访问 c.v
	c.v[key]++
	c.mux.Unlock()
}

// Value 返回给定 key 的计数器的当前值。
func (c *SafeCounter) Value(key string) int {
	c.mux.Lock()
	// Lock 之后同一时刻只有一个 goroutine 能访问 c.v
	defer c.mux.Unlock()
	return c.v[key]
}

func main() {
	c := SafeCounter{v: make(map[string]int)}
	for i := 0; i < 1000; i++ {
		go c.Inc("somekey")
	}

	time.Sleep(time.Second)
	fmt.Println(c.Value("somekey"))
}
```
- sync.Mutex(互斥锁,一读一写)
- sync.RWMutex(读写锁,可以多读一写)
- sync.Pool(对象池, 合理利用可以减少内存分配, 降低GC压力)
- sync.Once(并发控制. 适用于开几个goroutines去执行一个只执行一次的任务, 比如单例模式)
- sync.Cond(并发控制, cond.Wait()阻塞至其他goroutie运行到cond.Signal())
- sync.WaitGroup(并发控制. 通常用法 wg.Add增加任务数量 goroutie完成任务后执行wg.Done,任务数量减1 wg.Wait等待wg任务数量为0)

## web服务
目前主流的web服务：REST/SOAP

网络进程三元组（ip地址，协议，端口）

## 其他

- %+v 打印包括字段在内的实例的完整信息
- %#v 打印包括字段和限定类型名称在内的实例的完整信息
- %T 打印某个类型的完整说明