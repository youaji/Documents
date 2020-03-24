## 前言

上一节简单了解了 Kotlin 的语法使用，对一些语法有一些了解后，在面对 Java 转 Kotlin 时或想了解 Kotlin 到底转成了什么样的 Java？那我们需要知道这两个技能，不仅能偷一些懒也能了解一些简单原理；

1. AndroidStudio  中`Code → Convert Java File to Kotlin File` 或 `双击 Shift 输入 Convert Java File to Kotlin File`

   一键 Java 转 Kotlin，无法从 Kotlin 再转回 Java 哦。虽然能偷懒，但不能过分依赖，前提你要了解 Kotlin，转的时候有一些实现与性能会有出入。

2. `双击 Shift 输入 Show Kotlin Bytecode`

   Kotlin 有挺多 Java 中没有的特性，如果你想看下 Kotlin 这样实现在 Java 中到底是怎样体现的，那你可以去瞧一瞧。

## 构造器

上一节简单上手接触了 Kotlin 的一些语法，我们用上一节的知识知道如何实现一个类的构造器。

```kotlin
class KotlinView : View {
    private var paint = Paint() // 初始化画笔
  	// 次级构造器 → 调用 this（主构造器）
    constructor(context: Context) : this(context, null)
  	// 主构造器 → 调用 super
    constructor(context: Context, attrs: AttributeSet?) : super(context, attrs) {
      // 画笔样式设置  
      paint.style = Paint.Style.STROKE
    }
}
```

Kotlin 在构造器还有更简洁的写法，主要有**主构造器**、**次级构造 **以及 **init代码块** 与 **构造属性**。

### 主构造器

主构造器是什么呢？

我认为在一个类里面最终消费的构造器就是主构造器；比如上面的代码里面 `constructor(context: Context)`调用的是`this`，最终流向的还是`constructor(context: Context, attrs: AttributeSet?)`，所以它就是主构造器。

那主构造器如何更简洁呢？

可以将主构造器的函数直接放置类声明的后面就行。看下简约后的代码

```kotlin 
class KotlinView constructor(context: Context, attrs: AttributeSet?) : View(context, attrs) {
  	// 可以访问到主构造函数 context 参数
    val color = context.getColor(R.color.white)
    private var paint = Paint()
    constructor(context: Context) : this(context, null)
		// paint.style = Paint.Style.STROKE
  	fun testParams(){
    	context.resources// 不报红，但实际 context 并不是主构造函数里面的参数
      attrs// 会报红，意味着访问不到
    }
}
```

倒是简洁了蛮多，可是我们**主构造器里面的逻辑咋办**？别慌，往下看！

另外：

1. 如果主构造器没有被**可见性修饰符**或**注解**标记，那么`constructor`是可以省略的；
2. 还有**成员变量初始化**时是可以直接访问主构造参数，例如`color`变量。注意点看代码！

### 次级构造

这个就不多讲了，除了主构造器，剩下的不就是次级构造了吗

### init 代码块

在上面我们把主构造器简化了，可是里面的逻辑没地方放了，现在就改`init`上场了

```kotlin
class KotlinView (context: Context, attrs: AttributeSet?) : View(context, attrs) {
    private var paint = Paint()
    constructor(context: Context) : this(context, null)
    init {
        paint.style = Paint.Style.STROKE
    }
}
```

这样就把主构造器里面的逻辑安置好了，值得注意的一点：在初始化的时候初始化块会按照它们在**文件中出现的顺序执行**。如果`paint`变量在`init`的下面声明，那`paint`在`init`里面就会报红，在编译的时候，`init`代码会按照文件出现的顺序插入到实际的构造函数中，声明放在`init`下面的话，那就会被插入在`paint.style`的下面。

PS：注意到主构造器的`constructor没？没见了是吧，因为他既没有被可见性修饰也没有被注解，所以是可以省略的。

### 构造属性

构造属性是什么呢，我给他翻译过来就是：构造函数的参数属性化。

常规保守写法是这样的

```kotlin
class KotlinInfo {
    var name: String? = null
    var age: Int? = null

    constructor(name: String?, age: Int?) {
        this.name = name
        this.age = age
    }
}
```

可是代码太多，不是 Kotlin 的风格，那我们这样来

```kotlin
// 简化方式一：直接使用主构造函数参数初始化。
class KotlinInfo constructor(name: String?, age: Int?) {
    var name: String? = name
    var age: Int? = age
}
// 简化方式二：emmm~还是不够简洁，再来
class KotlinInfo constructor(var name: String?, var age: Int?) {
}
```

是吧，这样才对，主构造函数的入参即是参数又变量，所以这就是构造属性。

## data class

在 Java 中重写`hashCode()`、`toString()`等类时，需要我们一个个去重写出来，在 Kotlin 中我们只需要在`class`前声明为`data`「数据类」就会自动为我们生成`hashCode()`、`toString()`、`equals()`、`copy()`、`componentN()`方法。

```kotlin
// 声明类为 data 类型
data class KotlinInfo constructor(var name: String?, var age: Int?) {}

fun main() {
    val kotlinInfo = KotlinInfo("Kotlin", 25)
    kotlinInfo.hashCode()
    kotlinInfo.toString()
    kotlinInfo.equals(null)
    kotlinInfo.copy()// 浅拷贝
    kotlinInfo.component1()// 对应第一个变量 name
    kotlinInfo.component2()// 对应第二个变量 age
}
```

但是生成数据类还是有前提的

- 主构造函数需要至少有一个参数
- 主构造函数的所有参数需要标记为 `val` 或 `var`；相当于必须是构造属性
- 数据类不能是抽象、开放、密封或者内部的

`component`属性的顺序对应主构造参数的声明顺序

## 相等性

- `==`	结构相等，调用了`equals()`
- `===`  引用相等

注意：与 Java 不同

## 解构

Kotlin 的一大特点**解构声明**，表示一个对象一次赋值给多个变量。

```kotlin
data class KotlinInfo constructor(var name: String?, var age: Int?) {}

fun main() {
    val (name, age) = KotlinInfo("Kotlin", 25)
}
```

这个就有一个前提，必须是数据类才能做解构声明，当然不是数据类我们也可以自定义解构声明，比如：

```kotlin
class KotlinInfo constructor(var name: String?, var age: Int?) {
    operator fun component1(): String? {
        return name
    }

    operator fun component2(): Int? {
        return age
    }
}

fun main() {
    val (name, age) = KotlinInfo("Kotlin", 25)
}
```

## **Elvis** 操作符

通过`?:`操作简化`if null`的操作

```kotlin
// kotlinInfo.name 为空返回"没有姓名"
val name = kotlinInfo.name ?: "没有姓名"
// kotlinInfo.age 为空提前返回
val age = kotlinInfo.age ?: return
// kotlinInfo.age 为空抛出异常
val age = kotlinInfo.age ?: thorw IllegalArgumentException("age is null") 
// 对应 if 代码是这样的
var name = kotlinInfo.name
if (kotlinInfo.name == null){
    name = "没有姓名"
}
```

## when 操作符

`when`不仅条件分支上支持条件表达式，分支上也可以多个值，通过`,`分割；还可以有返回值；默认分支条件都是布尔表达式。

```kotlin
// num 大于 0 哦~
val str = when (num) {
    in 0..99 -> "< 100"// 大于等于 0 小于100；返回字符串
    100 -> "100"// 等于 100；返回字符串
    200 -> "200"// 等于 200；返回字符串
    300, 301 -> "300,301"// 等于 300 或 301；返回字符串
    else -> ">300"// 其他情况全部返回字符串
}
```

## operator

一般用的时候找下就行，与 Java 的大致差不多，列举下加减乘除

| 操作符 | 函数       |
| :----- | :--------- |
| a+b    | a.plus(b)  |
| a-b    | a.minus(b) |
| a*b    | a.times(b) |
| a/b    | a.div(b)   |

## Lambda

Lambda 在 Java 中也接触过，这里主要说下哪些可以省略

```Kotlin
val kotlinInfos: List<KotlinInfo> = ArrayList()
// 1、常规写法
for (kotlinInfo in kotlinInfos) {
    if (kotlinInfo.name.equals("Kotlin")) {
        return
    }
}
// 2、使用 Lambda 的常规写法
kotlinInfos.forEach({ kotlinInfo: KotlinInfo ->
    if (kotlinInfo.name.equals("Kotlin")) {
        return
    }
})
// 3、函数最后一个参数是 Lambda ，那么可以将 Lambda 表达式移至外面，如下：
kotlinInfos.forEach() { kotlinInfo: KotlinInfo ->
    if (kotlinInfo.name.equals("Kotlin")) {
        return
    }
}
// 4.1、如果传入参数只有一个 Lambda，小括号可以省略；forEach()
// 4.2、Lamdda 表达式只有一个参数，类型也可以省略；kotlinInfo: KotlinInfo
kotlinInfos.forEach{ kotlinInfo ->
    if (kotlinInfo.name.equals("Kotlin")) {
        return
    }
}
// 5、Lambda 表达式只有一个参数还可以通过隐式的 it 来访问参数，如下：
kotlinInfos.forEach {
    if (it.name.equals("Kotlin")) {
        return
    }
}
```

## 循环

```kotlin
repeat(100){
    // do something
}
for (i in 0..99){
    // do something
}
for (i in 0 until 100) {
    // do something
}
```

## infix

前提：

- 必须是**成员函数**或**扩展函数**；
- 必须**只能接收一个参数且不能有默认值**；

下面是`until`的源码，函数是`Int`的扩展函数且只有一个参数无默认值

```kotlin
public infix fun Int.until(to: Int): IntRange {
    if (to <= Int.MIN_VALUE) return IntRange.EMPTY
    return this .. (to - 1).toInt()
}
```

## 嵌套函数

Kotlin 的函数中是可以在嵌套函数的，而且在内部函数里面可以访问外部函数的参数；但是有一点需要注意，每次调用外部函数时不仅会产生一个外部函数的对象而且内部函数也会产生一个函数对象，所以用的时候需要谨慎。

```kotlin
fun external(string: String) {
    fun internal() {
        print(string)
    }
    print(string)
}
```

## 函数简化

```kotlin
var string: String? = null
fun printText() {
    print(string)
}
fun getText(): String? {
    return string
}
```

```kotlin
var string: String? = null
fun printText() = print(string)
fun getText(): String? = string
```

## 函数参数默认值

```kotlin
fun toast(context: Context, text: CharSequence) {
    toast(context, text, Toast.LENGTH_SHORT)
}
fun toast(context: Context, text: CharSequence, duration: Int) {
    Toast.makeText(context, text, duration).show()
}
```

```kotlin
fun toast(context: Context, text: CharSequence, duration: Int = Toast.LENGTH_SHORT) {
		Toast.makeText(context, text, duration).show()
}
```

在 Java 中调用就只能使用两个参数的方法，这时候就需要用到`@JvmOverloads`

## @JvmOverloads

在`toast`函数上标记`@JvmOverloads`注解后，Java 就能用了

## 扩展

```kotlin
// 正常写法
fun dp2px(dx: Float): Float { ...}
// 扩展写法
fun Float.dp2px(): Float { ...}
// 两者使用区别
dp2px(10f)
10f.dp2px()
```

## 函数类型

函数类型**传入参数类型**和**返回值类型**组成，传入参数需要用`()`，用`->`连接返回值，返回值为`Unit`不可省略；

函数类型实际是一个接口，传递函数的时候可通过`::函数名`、`匿名函数`或者使用`lambda`

```kotlin
class View {
    interface OnClickListener {
        fun OnClick(view: View)
    }

    fun setOnClickListener(listener: (View) -> Unit) { }
}

fun onClick(view: View) {
    print("click")
}

fun main() {
    val view = View()
    // 方式一：函数传入
    view.setOnClickListener(::onClick)
    // 方式二：匿名函数
    view.setOnClickListener(fun(view: View) { print("click") })
    // 方式三：Lambda
    view.setOnClickListener { print("click") }
}
```

## 内联函数

### 内联函数

`inline`关键字声明**内联函数**，编译时内联函数的函数体会被插入至调用处，因此实现内联函数时注意，函数体内的代码行数尽量减少。

**内联函数**有什么作用呢？看看代码

```kotlin
inline fun log(text: String) {
  Log.d("TAG", text)
}
fun main() {
  log("log text")
}
// 用 inline 修饰声明内联函数 log
// 实际编译后的代码就变成这样了
fun main() {
  log.d("TAG", "log text")
}
// 将内联函数 log 里面的代码直接放到了调用的地方
// 到底有什么用呢？
// 从调用栈的角度来看看
// 不使用内联函数大致是这样的
// main 入栈 → log 入栈 → log.d() → log 出栈 → main 出栈
// 使用内联函数是这样的
// main 入栈 → log.d() → main 出栈
```

所以内联函数能减少一层调用栈，但是如果函数体里面内容较多时，编译器会变得比较辛苦，因此我们在实现内联函数时一定要注意。

思考：示例代码里面的内联函数`log`的`inline`有警告！

### 部分禁用内联

`noinline`可以禁止部分参数参与编译

## 具体化的类型参数

通过配合`inline + reified`达到**真泛型**的效果

```kotlin
interface Api{ ... }
val retrofit = Retrofit.Builder()
        .baseUrl("https://api.com")
        .build()

inline fun <reified T> create(): T {
    return retrofit.create(T::class.java)
}

val api = create<Api>()
```

## 委托

### 属性委托

常见的属性操作，通过委托的方式，让其只实现一次

- `lazy`：延迟属性，值仅在首次访问时计算
- `observable`：可观察属性：属性发生改变时通知

### 类委托

通过类委托的模式减少继承

## 标准函数

- `apply`
  - 返回自身：作用域中使用`this`做为参数
  - 适合对一个对象做附加操作
- `also`
  - 返回自身：作用域中使用`it`做为参数
- `run`
  - 无需返回自身：作用域中使用`this`做为参数
- `let`
  - 无需返回自身：作用域中使用`it`做为参数
  - 适合配合空判断时
- `with`
  - 使用对同一个对象进行多次操作时