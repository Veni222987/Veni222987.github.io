---
title: 【Exp】深入理解Go语言函数与方法
tags:
  - Go
categories: Exp
cover: https://th.bing.com/th/id/R.21fa5dfc7b4388af6eea1ebc49980db8?rik=JJQgQ9mjDCwCVg&pid=ImgRaw&r=0
highlight_shrink: false
date: 2024-04-11 13:24:42
---

# 函数

Go语言的函数有四个组成部分：函数名、形参列表、返回值列表、函数体。一个最标准的函数形式如下：

```go
func twoSum(x, y int) int {
    return x + y
}
```

函数的前三个组成部分构成了函数的签名，可以使用如下方式查看签名，其中%T是Go语言的占位符，用于打印变量的类型。

```go
f := twoSum
fmt.Printf("%T\n", f)
// 输出结果：
// func(int, int) int
```

函数在Go语言中也是一种数据类型，具有地址。可以通过函数名或者`&`运算符来获取函数的地址，并将其赋值给函数类型的变量。这使得在Go语言中可以像操作其他类型一样操作函数，例如将函数作为参数传递给其他函数或存储函数的地址。所以，可以使用函数作为参数传递实现回调函数，也可以将返回值设置为函数实现闭包，后面会详细讲。在我看来，函数可以**理解成为一个特殊的引用数据类型**，其零值为`nil`，可以取址、可以作为赋值符号的右操作数。

接下来文章将按照函数的四个组成部分逐步深入理解函数。

## 形参

参数列表可以简写，例如：

```go
func twoSum(x int, y int) int {}
```

可以简写成为：

```go
func twoSum(x, y int) int {}
```

但是省略的条件是同一类型的形参放在一块。注意形参都是**有序的**，Go语言**没有参数默认值**，在传递实参的时候，必须严格按照形参列表顺序传递。所以，在开发中需要注意入参顺序，写好函数注释。有人会说，没有默认参数值多不方便啊，如果一个函数有十几个参数，没有默认参数怎么办呢？这就是工程架构设计的问题了，需要拆分接口，分步实现，或者采用结构体等形式实现默认操作。

### 可变参数

可变参数是通过在参数类型前加上省略符`...`来定义的。这表示该参数可以接受零个或多个值，这些值将被作为**切片（slice）**传递给函数。

```go
func sumOfAll(nums ...int) (res int) {
	for _, value := range nums {
		res += value
	}
	return
}
```

可变参数在一个函数里面只能使用一次，并且必须放在参数列表的最后一个位置。如果使用两次或者不将其放在最后一个位置（其实使用两个可变参数就必然会导致其中一个可变参数不位于最后一个位置），将会无法通过编译：`Can only use '...' as the final argument in the list`。

### 函数的参数传递

对于Go而言，函数的参数传递方式只有值传递。这对于熟悉Java的人来说比较难以理解，因为对他们而言引用传递可能更深入人心。其实除了基本数据类型以外，函数传递的都是该变量在内存中的地址值，当程序访问变量的时候，都是通过地址访问的，也就是说，引用类型在修改数据的时候都是间接修改了内存中的值，从而使得修改对于所有其他的引用都是可见的。”Go语言圣经“原话是这么说的：

> Arguments are passed by value, so the function receives a copy of each argument; modifications to the copy do not affect the caller. However, if the argument contains some kind of reference, like a pointer, slice, map, function, or channel, then the caller may be affec ted by any modifications the function makes to variables indirectly referred to by the argument.

### 回调函数

回调函数是一种编程模式，其中一个**函数作为参数传递给另一个函数**，并且在**特定的事件发生或条件满足时被调用**。下面的函数展示了Go语言的回调函数的使用方法，其中`passingFunc`接受一个`func(int) int`类型的回调函数，然后传入一个numDouble函数将data数据翻倍，最后在主函数调用。

```go
func passingFunc(data []int, callback func(int) int) {
    for _, d := range data {
       fmt.Println(callback(d))
    }
}

func numDouble(num int) int {
    return num * 2
}

func main() {
	data := make([]int, 5)
	for i, _ := range data {
		data[i] = i
	}
	passingFunc(data, numDouble)
}
```

上述代码输出结果为：`0 2 4 6 8` 。（空格为换行，为省略空间表示为这种形式）。

## 函数名

Go不允许函数重载，也就是说，函数名是一个函数的唯一标识，不存在同一个函数名不同参数的情况（C++、Java都有）。

### 省略函数名——匿名函数

```go
func main() {
    // 定义一个匿名函数并立即调用
    result := func(a, b int) int {
        return a + b
    }(3, 4)

    fmt.Println("结果:", result)

    // 将匿名函数赋值给变量
    subtract := func(a, b int) int {
        return a - b
    }

    result = subtract(10, 5)
    fmt.Println("结果:", result)
}
```

使用匿名函数作为参数传递给回调函数会使得代码更加简洁，例如上面回调函数的示例代码中，可以不用定义numDouble函数，而是直接将其作为匿名函数传入。

```go
passingFunc(data, func(num int) int {
  return num * 2
})
```

### 匿名函数与作用域

匿名函数可以获取作用域外的变量，使用的方法是直接在函数体内声明并使用该匿名函数，例如：

```go
x := 10
func() {
    fmt.Println("x的值为:", x)
    x += 5
    fmt.Println("修改后:", x)
}()
fmt.Println("匿名函数执行后:", x)
//执行结果（省略版）： 10 15 15
```

也可以将匿名函数赋值给一个变量让其多次调用，这里就不举例了。从上述代码看可以看到，匿名函数可以在函数体内部调用，并且在函数作用域内的变量都可以被该匿名函数访问和修改。当返回值为函数时，这种作用域将会被保留称为闭包的引用环境。

### 匿名函数与闭包

有这么一个对闭包的总结： **闭包=函数+引用环境**。因此闭包的核心就是：**函数**和**引用环境**。 
[Go语言基础知识 —— Closure(闭包)]: https://zhuanlan.zhihu.com/p/645853924

下面先看一段代码：

```go
func newCounter() func() int {
    count := 0
    return func() int {
       count++
       return count
    }
}

func main() {
    f := newCounter()
    fmt.Println(f()) //1
    fmt.Println(f()) //2
    f1 := newCounter()
    fmt.Println(f1()) //1
    fmt.Println(f1()) //2
}
```

一开始看到可能会觉得很抽象，但是没关系，接下来会慢慢讲解。首先观察newCounter这个函数的返回值是一个函数类型，意味着什么呢？意味着这段代码使用newCounter创建了一个函数。观察函数体内，可以发现，返回的是一个匿名函数并没有包含count变量。那为什么多次调用count会实现自增功能呢？那是因为在创建函数的时候用到了count，所以每一次执行newCount都会创建一个count的副本和一个匿名函数，实现了将这个匿名函数绑定到当时环境的功能，而被创建出来的函数，就是一个闭包。

所以，再回顾一下上面那句话：**闭包=函数+引用环境**。闭包其实就是一个特殊函数，他可以捕获函数内部变量和参数，并将它们与函数创建的环境绑定在一起。

## 返回值

函数的单返回值就不多说了，下面重点讲讲多返回值。多返回值常常结合错误处理使用，一般而言，函数的第一个返回值是函数本身具有实际意义的返回结果，最后一个返回值通常是错误，错误的类型往往是error。一般设计上，Go语言的多返回值都是两个，但是Go本身支持两个以上的返回值，例如整数除法就可以同时返回商、余数、近似值等。

> error是Go语言内置的类型，以字符串的形式简要报告了错误，提供了灵活的处理方式。

### 命名返回值的return带操作数会被覆盖吗

当我们在return语句中显式指定了返回值时，它将覆盖函数体中的命名返回值，举个例子：

```go
func plus2(x int) (res int) {
    res = x
    defer func() {
       res++
    }()
    return res * 2
    /*
       等价于
       res = res * 2
       return
    */
}
```

上面的函数中，我们在参数列表预定义了返回结果，按理来说只需要写`return`就好，后面不需要跟任何操作数。但是如果写了操作数的话就相当于对操作数进行了一次赋值，也就是用后来的结果覆盖了原结果。

实际上，return语句并不是原子操作，它执行的逻辑是：**保存返回变量->执行defer语句->执行ret返回调用函数**。

## 函数体

### 延迟函数调用与defer语句

defer是一个功能强大的关键字，表示延迟函数调用。从使用形式上看 ，defer就是一个普通的函数或方法调用，注意defer必须跟一个函数或方法调用，如果不是，那么请将其改为匿名函数形式。它将defer后面的代码执行完毕之后再执行defer语句，无论是后面的代码会return还是宕机。当然，如果陷入了死循环是无法执行defer后面的代码的。defer一般用于一对操作，例如打开和关闭文件、加锁和释放锁等等。当同一个函数里面有多个defer的时候，按照栈的顺序执行，即先进后出。使用方法如下：

```go
var mu sync.Mutex
var m = make(map[string]int)
func lookup(key string) int {
  mu.Lock()
  defer mu.Unlock()
  return m[key]
}
```

上述代码使用defer实现了加锁和释放锁，从而保证了对map的互斥访问。

### 在defer后面修改返回值会怎样

还记得上面说过的return语句执行顺序吗？**保存返回变量->执行defer语句->执行ret返回调用函数**。这个就决定了defer语句是否影响返回值。所以结果就是：**defer语句会修改指针类型和命名返回值的返回结果，而不会影响匿名返回值的结果**。从实现原理来看，匿名返回值在保存返回变量的阶段就已经被保存下来了，后面的操作都会被忽略。

```go
func plus(x int) int {
    defer func() {
       x++
    }()
    return x * 2
}

func plus2(x int) (res int) {
    res = x
    defer func() {
       res++
    }()
    return res * 2
}

func plus3(x *int) *int {
    defer func() {
       *x++
    }()
    *x = *x * 2
    return x
}

func main() {
    x := 1
    fmt.Println(plus(x))
    fmt.Println(plus2(x))
    fmt.Println(*plus3(&x))
}

//输出结果：2 3 3
```

### 宕机与恢复如何使用

之所以把它放在函数体这一部分，是因为Go的程序就是由函数组成的，其最外层入口就是main函数，程序的宕机是由函数的宕机造成的。panic和其他语言的运行时异常有点相似，如果不加以处理 ，最终都会导致程序终止执行。在发生panic之后，程序会终止执行，然后运行所有的defer函数，最后在程序退出之前打印调用堆栈。

panic除了由函数在运行时造成以外，还可以自己生成，因为panic本身就是一个函数，函数原型为：`func panic(v any)`。其中any代表了panic可以传入任何类型的参数，但是要求能够描述panic的原因，所以一般情况下使用string类参数。自己抛出panic的demo如下：

```go
func doSomething() {
		// ......业务代码
    // 产生panic
  panic("发生了错误：输入条件无意义")
}
```

当然，除了把panic直接抛出以外，还可以从panic中恢复(recover)，并且recover必须放在defer中才有意义，例如：

```go
func recoverFromPanic() {
    if r := recover(); r != nil {
       fmt.Println("捕获到panic:", r)
    }
}

func doSomething() {
    defer recoverFromPanic()
    // 产生panic
    panic("发生了错误：")
}

func main() {
    doSomething()
    fmt.Println("程序继续执行")
}
```

recover就相当于异常处理机制的catch语句，recover可以捕获内层函数没有recover的panic。一个合理的做法是将部分可预见的panic封装称为error向上返回，使其不影响程序的执行。但是必须做好错误提醒，因为一旦将panic封装为error之后，将会默认隐藏调用堆栈，从而加大debug难度。

# 方法

和函数一样，我们可以把Go语言的方法拆分成五个组成部分：接收者、方法名、形参列表、返回值列表、方法体。方法比函数多了一个接收者，有点类似传统OOP语言的成员函数，声明的时候放在方法名前面，例如：

```go
type Point struct {
    X, Y float64
}

func (p Point) distanceTo(q Point) float64 {
    return math.Sqrt((p.X-q.X)*(p.X-q.X) + (p.Y-q.Y)*(p.Y-q.Y))
}

func main() {
    p := Point{1, 2}
    fmt.Printf("%.2f", p.distanceTo(Point{2, 3}))
}
```

Go要求自定义接收名，例如`p Point`，在Go中不使用类似this或者self这种隐式指针或者引用，但可以自己将其命名称为类似的名字，推荐命名尽量简短，因为方法可能需要频繁操作自身变量，推荐使用该类型的首字母小写。

## 方法名和成员变量名冲突吗

Go的方法和类型成员同在一个命名空间内，所以不能起和成员变量一样的方法名，例如，我们在上述代码段中尝试加入一个名称为X的方法，编译器将会报错：

![image-20240414172852308](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/image-20240414172852308.png)

但是包级别函数名和方法名不冲突，也就是说，可以同时声明`func (a TypeA) A(){}`和`func A(){}`。

## 哪些类型可以声明方法

我们可以为**同一个包**内**基本数据类型和引用类型声明方法**，指针类型和接口类型无法增加方法。例如：

```go
// 基本类型增加方法
type age int

func (a age) olderThan(b age) bool {
    return a > b
}

// 引用类型增加方法
type myPoint []float64

func (p myPoint) distanceTo(q myPoint) float64 {
	return math.Sqrt((p[0]-q[0])*(p[0]-q[0]) + (p[1]-q[1])*(p[1]-q[1]))
}

// 接口类型无法定义方法，（error是内置的接口类型）以下代码将会无法编译：
/*
type myError error
func (e myError) function(){
}
*/

// 同理，指针类型也无法定义方法：下面的代码会报错
/*
type pInt *int
func (p pInt) function(){	
}
*/
```

## 指针接收者方法

接收者分为**值接收者**和**指针接收者**两种。指针接收者方法就是把所属类型的接收对象改为指针的形式，例如，我们为Point类创建指针接收者方法可以写成这样：

```go
func (p *Point) distance2(q *Point) float64 {
    // ......
}
```

也就是把方法绑定的变量改成了该类型的指针形式。如果直接在原来的基础上增加一个指针接收者方法，那编译器将会出现提醒：`Struct Point has methods on both value and pointer receivers. Such usage is not recommended by the Go Documentation. `也就是说不推荐同时存在两种接收者。所以，**一旦该类型有一个指针接收者方法，那么其余所有方法都应该使用指针作为接收者**。

由于指针接收者的存在，所以Go语言不允许指针类型定义方法，防止混淆，因为编译器会对两种接收者进行隐式转换，见下面的代码。

```go
// 值接收者类型
type Point struct {
    X, Y float64
}

func (p Point) distanceTo(q Point) float64 {
    return math.Sqrt((p.X-q.X)*(p.X-q.X) + (p.Y-q.Y)*(p.Y-q.Y))
}

// 指针接收者类型
type Point2 struct {
    X, Y float64
}

func (p *Point2) scaleBy(size float64) {
	p.X, p.Y = size*p.X, size*p.Y
}

func (p *Point2) distanceTo(q *Point2) float64 {
    return math.Sqrt(((*p).X-(*q).X)*((*p).X-(*q).X) + (p.Y-q.Y)*(p.Y-q.Y))
}

// 自动隐式转换示例，看起来可能有点绕，但是记住这个例子是为了证明方法接收者会进行隐式转换，从而不允许为指针定义方法
func main() {
	p := Point{1, 2} // 变量值
	fmt.Printf("%.2f\n", p.distanceTo(Point{2, 3}))
	fmt.Printf("%.2f\n", (&p).distanceTo(Point{2, 3}))

	p2 := &Point2{1, 2} // 变量指针
	p2.scaleBy(2) // 直接调用
	fmt.Printf("%.2f\n", p2.distanceTo(&Point2{0, 0}))
	fmt.Printf("%.2f\n", (*p2).distanceTo(&Point2{0, 0}))

	(*p2).scaleBy(0.5) // 类型转换后调用
	fmt.Printf("%.2f\n", p2.distanceTo(&Point2{0, 0}))
	fmt.Printf("%.2f\n", (*p2).distanceTo(&Point2{0, 0}))
}
```

- **nil也是一个合法的接收者**

对于常见的OOP语言而言，空指针null是无法调用任何成员函数的。但是在Go中，nil是一个合法的接收者，当调用者的值为nil的时候，也可以有对应操作：

```go
func (p *Point2) length() float64 {
  if p==nil {
    return 0
  }
  return p.X*p.X+p.Y*p.Y
}
```

在这种情况下，即使使用一个*Point2类型的空指针作为接收者，也可以调用length，返回长度为0。

- **方法变量可以将一个结构体的方法转换成为普通函数使用**

和函数变量类似，方法变量也是将某一个方法赋值到变量中，后面使用变量调用这个方法，可以直接像函数一样使用，但是使用较少，此处就省略了。

# 总结

本文主要讲述了Go中的函数和方法的使用要点和注意事项，以实验的方法验证了部分特性。函数和方法是Go中最重要的两个概念。Go不是传统意义上的OOP语言，它通常通过包级别来实现封装功能，而包内使用函数式编程，并对外提供服务。总而言之，用好函数和方法是用好Go的关键。
