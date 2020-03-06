## 概要

- <a href = "#类">类</a>
  - 在声明`接口&抽象类&枚举&注解`时，与 Java 相比基本一样，除了`枚举&注解`在声明的时候后面哟啊加上`class`;
  - 构造函数的简单实现基本一致，还有高级用法哦！
  - 在实现`继承&实现`时都是通过`冒号`，而且接口和类不区分先后顺序；
  - 获取`class`是通过双冒号的形式；
  - Kotlin 的`Any`类似于 Java 的`Object`;
  - 三种内部类：`object`的匿名；默认的静态；`inner`的嵌套；
  - Kotlin 的`this`的形式为`this.@ClassName.this`;
  - 仅当前模块可见的`internal`;
- 函数
  - 无返回值用`Unit`；
  - 默认生成的`setter&getter`，`@JvmField`标记的作用；
- 变量
  - 可空类型和不可空类型
  - 数组
  - 空安全设计
  - `lateinit`
  - 类型转换
  - 非空断言
  - 编译器常量
- 其他
  - 顶层函数&`object`&伴生对象三种方式实现静态函数与变量，使用`@JvmStatic`标记用`static`修饰；
  - 字符串的`${}`和`"""`；
  - 区间`前者..后者`，前者包含后者不包含；
  - 条件分支支持表达式的`switch`高级版when`；
  - Kotlin 不强制捕获异常；
  - 简单的循环；
  - 注释`[]`；
  - `open | final`；

## 类

### 接口 & 抽象类 & 枚举 & 注解

```kotlin
// 接口
interface KotlinInterface { ...}
// 抽象类
abstract class KotlinAbstractClass { ...}
// 枚举「java 仅用 enum 一个关键字就行了」
enum class KotlinEnum { ...}
// 注解「java 是 @interface 」
annotation class KotlinAnotation { ...}
```

### 构造函数

```kotlin
// 例一：无参构造 & 有参构造
class KotlinInfo {
    var str1: String? = null
    var str2: String? = null
  	// 函数体无逻辑，大括号是可以省略的
    constructor() {}
    constructor(str1: String?, str2: String?) {
        this.str1 = str1
        this.str2 = str2
    }
}
// 例二：重写父类构造
// 发现没？继承父类 View 时后面没有写括号，因为重写了父类的构造函数，其实还有简写的方式
class KotlinView : View {
    constructor(context: Context) : this(context, null)
    constructor(context: Context, attrs: AttributeSet?) : super(context, attrs)
}
```

### 继承 & 实现

都是通过`:`来修饰，通过`,`来分隔类与接口，接口与类的前后顺序不受限制。

```kotlin
class KotlinActivity : AppCompatActivity(), View.OnClickListener{}
```

### 获取 class 

```kotlin
// 获取 kotlin 的 class
KotlinActivity::class
// 获取 java 的 class
JavaActivity::class.java 
```

### Object & Any

在 Java 中所有类都是`Object`的子类，Kotlin 则是`Any`，`Any`其实相当于就是 Java 中的`Object`；

两者相比，`Any`比`Object`更精简，只有三个方法`equals()` `hashCode()` `toString()`。

### 内部类

##### 匿名内部类

使用`object`关键字实现匿名内部类

```kotlin
// 接口是没有构造函数的
// 因此在实现 OnClickListener 时后面是不需要小括号的
val button = Button(context)
button.setOnClickListener(object : View.OnClickListener {
    override fun onClick(v: View?) {
    }
})
```

##### 静态内部类

Kotlin 中默认内部类就是**静态内部类**，因此可以将函数修饰为静态而不修改类；

##### 嵌套内部类

既然默认就是静态内部类，那如何修改成**嵌套内部类**呢？

```kotlin
class StaticInnerClass { ...} // 默认为静态内部类
inner class InnerClass { ...} // 修改为嵌套内部类
```

### this

```kotlin
// Java 通过「类名.this」获取目标类引用
MainActivity.this.finish();
// Kotlin 通过「this@类名」获取目标类引用
this@MainActivity.finish()
```

### 可见性

Kotlin 默认可见修饰符为`public`，Kotlin 没有包访问权限；
新增`internal`可见性修饰符代表当前模块可见；
如：组件开发时当前 Moudle 可见；做 SDK 或开源库时对当前模块可见。

## 函数

### 函数声明

无返回值在 Java 是`void`；Kotlin 是`Unit`；无返回值时`Unit`是可以省略的；

```kotlin
// 无入参、无返回值
fun main(): Unit { ...}
// 有入参、有返回值
fun addNum(num1: Int, num2: Int): Int {
  return num1 + num2
}
```

### setter & getter

在声明变量时 Kotlin 默认就生成了`setter`&`getter`方法，注意`val`只会生成`getter`。

但是如果我们需要在`setter`&`getter`里面做一些处理咋办呢？

```kotlin
class KotlinInfo{
	var str: String? = null
        set(value) {
          	// field 为本身
            field = value
        }
        get() {
            return field
        }
  // 我在 java 中调用时不想要 set&get 方法，就想直接调用变量
  // kotlin 默认生成的 set&get 
  // 在 java 中使用这个变量时只能通过 set&get
  // 在 kotlin 中使用不能调用 set&get，默认就是 set&get
}
```

**扩展：**在 Kotlin 我们都是直接点出变量名来取值与赋值，Kotlin 默认生成的`setter`&`getter`在 Java 中是能用的，但是即使变量名是公共的，我们仍然也只能`setter`&`getter`来调用该变量。

Java 如何像 Kotlin 一样使用变量呢？或者说如何点出变量而不是通过`setter`&`getter`？

使用`@JvmField`注解可以解决这一痛点，但是这样 Java 调用时就不会有`setter`&`getter`了。

```kotlin
@JvmField // 通过 @JvmField 注解后，在 java 中调用就能直接使用变量，这样就不会有 set&get 了
var str: String? = null
```

## 变量

### var & val

Kotlin 通过`var`&`val`两个关键字来定义变量；

- `var`：可读可写变量；
- `val`：只读变量，相当于 Java 里`final`修饰过的变量；

```kotlin
var age: Int = 25			
val name: String = "Kotlin"
val kotlin: Kotlin = Kotlin()
```

在平时定义的时候，一般后面的类型都是可以省略的；

Kotlin 与 Java 一样都是静态类型的语言，一旦等号右边类型确定则无法再更改变量类型；

因此变量类型只是省略掉了，不代表没有类型，只是 Kotlin 做了类型推断。

```kotlin
var age = 25
val name = "Kotlin"
val kotlin = Kotlin()
```

### 数组

定义数组用`arrayOf`

```kotlin
var arrayStr = arrayOf("kotlin", "java") 
```

`arrayOf`会自动对内容类型做转换，但是这样会存在问题；

`arrayOf`在进行转换时，会进行拆箱装箱操作，所以其实他的类型是包装类型，这样就会存在性能消耗；

所以一般我们要指定数组内容类型。

```kotlin
var arrayInt = intArrayOf(1,2,3)
var arrayFloat = floatArrayOf(1f,2f)
```

### 空安全

##### 类型

空安全设计是 Kotlin 设计的一大亮点，空安全上比 Java 更安全原因，便是因为它的空安全设计；

- 可空类型：在变量类型后面通过`?`修饰；`var str: String? = null` `str`是可空类型，允许为`null`；
- 不可空类型：默认就是；`var str: String = "text"` `str`是不可空类型，不允许为`null`，编译时就会报错；

在 Java 中虽然也有 `@Nullable`&`@NotNull`可以标记，但是在 Java 中只会有警告提示，并不会在编译期间受影响；

##### 调用符

在使用可空类型时还会涉及到两个调用符

- `!!`强行调用符：强制执行，不管变量是否为空，如为空会抛出空指针异常；
- `?.`安全调用符：安全调用，相当于加了层是否为空校验，不为空才调用；

```kotlin
maybeNull!!.setText("text")
maybeNull?.setText("text")// if （maybeNull != null）{ maybeNull?.setText("text"); }
```

##### 平台类型

那么不可空类型就一定不会为空吗？

```kotlin
var button: Button
button = findViewById(R.id.btn)
// button 是虽然是不可空类型，但是仍然会有空指针的风险
// 我们一行写出来是这样的
var button: Button! = findViewById<Button>(R.id.btn)
// Button! 实际在编码时是隐藏提示的，可是他又是什么呢？
// 在类型后面加一个感叹号这就是一个平台类型
// 我们来看下 findViewById 的代码
@Nullable
public abstract <T extends View> T findViewById(@IdRes int id);
// 所以实际 findViewById 返回的对象是可能为空的
```

### lateinit

在使用延迟初始化「lateinit」时有两个前提

- 被修饰的变量必须是不可控类型
- 被修饰的变量不能有初始值

在使用时可以减少大量`?`的使用，减少没必要的可空类型使用；

或者也可以这么理解：我这个不可空类型变量需要晚点赋值，一开始你要允许我为空，待会我肯定给你赋值就行

```kotlin
// 不加 lateinit 是这样的
class KotlinActivity : AppCompateActivity(){
	private var button: Button? = null
  override fun onCreate(savedInstanceState: Bundle?){
  	super.onCreate(savedInstanceState)
    button = findViewById(R.id.button)
    button?.setText("btn")
  }
}
// 加了呢
class KotlinActivity : AppCompateActivity(){
	private lateinit var button: Button
  override fun onCreate(savedInstanceState: Bundle?){
  	super.onCreate(savedInstanceState)
    button = findViewById(R.id.button)
    button.setText("text")
  }
}
```

### 类型转换

##### 数据类型转换

```kotlin
var floatNum: Float = 200f // 直接赋值为 float 类型
var floatNum: Float = 200.toFloat() // 调用函数转换
```

在 Kotlin 中一切皆对象，包括数据类型也是，这里还有些装箱之类的概念，如`var floatNum: Float? = 200f`这里就有一个装箱操作。

##### 类型强转

使用`as`关键字进行类型强转

```kotlin
var view: View? = null 
// is 相当于 Java 的 instanceof
if (view is Button) { 
  // as 相当于 Java 的强转，(Button)view
  Button button = view as Button
  button.setText("text")
}
// Kotlin 在这里又体现了智能的地方
// 当你的 view 对象校验已经是 Button 了，它就认为不需要你在强转了
if （view is Button) {
	view.setText("text") 
}
```

### 非空断言

**可空类型**强转类型为**不可空类型**，可以在变量后面加上`!!`来达到类型装换。

### 编译器常量

概念：在编译期就确定了值且以后不再发生变化。

在**静态变量**前加上`const`关键字变成编译器常量；注意一定是在**静态变量**前！

## 其他

### 静态

##### 顶层函数

又叫**包级函数**，表示在 Kotlin 文件中直接申明的变量或函数，而这些变量或函数又都是静态的。

```kotlin
package com.youaji.util
// 静态变量
private val displayMetrics = Resources.getSystem().displayMetrics
// 静态函数
fun dp2px(dp: Float): Float {
    return TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, dp, displayMetrics)
}
```

使用的时候直接使用函数名或变量名，导包连同包名与函数名或变量名一起导入；

这样会存在一些问题，如重载的时候或多个文件一样的函数名，虽然导包的时候能区别，但识别度很不友好；这个问题先留着，留着等下解决，先来解决紧急点的问题，是不是 Java 调用也是一样呢？

```kotlin
// Kotlin 调用
import com.youaji.util.dp2px
val pxValue = dp2px(10f)
// Java 调用，虽然是通过类调用的，但是类名变成了 UtilKt
UtilKt.dp2px(10f);
// 好好的一个 Util 一定要写成 UtilKt 才能使用，想个办法改改
@file:JvmName("Utils")
package com.youaji.util
// Java 调用就变成这样了
Utils.dp2px(10f);
// 如果文件名是 Util.kt，那就不能设置成 Util，设置了也没用
// 原因还不知道，猜测如果你设置成与文件名一样也就没意义了，或者说这样也会与文件名冲突
// 虽然说能自定义名字，但是实际认为大多数场景是用不到的，so，一般情况也没必要去改
```

虽然可以通过`@file:JvmName("name")`修改名字，但是还是有些不爽，我文件名是`Util.kt`是不能设置名字为`Util`的，就算设置了也不生效，具体原因还没深究，猜测其实这样设置成与文件名一直也没意义，也有可能存在文件名冲突问题，所以，虽然能自定义，但是实际开发是大多数场景是用不到的，一般情况也没必要去修改。

##### object

前面通过`object`实现了匿名内部类，那这里`object`其实还能修饰静态类，还能实现单例呢！也解决了上面不能通过类名调用函数的疑问。

```kotlin
object Util {
    private val displayMetrics = Resources.getSystem().displayMetrics
    fun dp2px(dp: Float): Float {
        return TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP,dp,displayMetrics)
    }
}
// Kotlin 使用；这样就跟 java 一样，通过类名调用函数
Util.dp2px(10f)
// Java 使用；有差异，咋个多了个 INSTANCE 呢！其实是因为单例的原因
Util.INSTANCE.dp2px(10f);
```

##### 伴生对象

```kotlin
// 仅做示范呦
class Util {
    constructor() { displayMetrics = Resources.getSystem().displayMetrics}
    companion object {
        private lateinit var displayMetrics: DisplayMetrics
        fun getDisplayMetrics(): DisplayMetrics { return displayMetrics}
    }
}
Util.getDisplayMetrics() // kotlin 使用
Util.Companion.getDisplayMetrics(); // java 使用
```

##### 扩展

- 疑问一：为什么 Kotlin 把`static`关键字去掉呢？

  这个其实挺矛盾的，面向对象中，万物接对象，但是被 static 修饰的又不属于哪个对象，Kotlin 想避免这种情况，但是顶层函数的存在又有些冲突；

- 疑问二：Kotlin 中没有`static`，那这些静态方法是不是就是 Java 中被`static`修饰的方法呢？

  不是的，Kotlin 只是用这些方式更优雅的替代了 Java 中使用`static`的场景，但是在与 Java 混编的时候还是可以通过`@JvmStatic`标记为被`static`修饰。

```kotlin
@JvmStatic
fun dp2px(dp: Float): Float { ...}
@JvmStatic
fun getDisplayMetrics(): DisplayMetrics { ...}
// Kotlin 中使用仍是一样，Java 中跟使用 static 方法一样，无需使用 INSTANCE 或 Companion
Util.dp2dp(10f)
Util.getDisplayMetrics();
```

### 字符串

##### 字符串模板

通过`${}`形式使用字符串模板

```kotlin
var data = "2019-07-21"
var number = 666
var str = "${data}收到转账${number}元"
// 如果是单一的变量，大括号是可以省略的
var str = "收到转账$number元"
```

##### 多行字符串

通过`"""`代替`\n`拼接的多行字符串

```kotlin
var str = """
	第一行
	第二行
	第三行
"""
```

### 区间

```kotlin
// 一般我们做区间判断是这么写
if (num >= 200 && num < 300) { }
// 但是我们有了区间就可以这样写
if (num in 200..299) { }
// 什么意思呢？表示 num 是否在包含 200 且小于 300 的区间当中
```

### when

when 相当与 Java 中的高级版`switch`，因为 when 在条件分支上支持表达式。

```kotlin
// 一般使用，相当于 java 的 switch
when (num) {
    200 -> println("200")
    300 -> println("300")
    400 -> println("400")
    else -> println("其他")
}
// 配合区间的表达式
when (num) {
    in 200..299 -> println(">= 200 < 300")
    in 300..399 -> println(">= 300 < 400")
    in 400..499 -> println(">= 400 < 500")
    500 -> println("500")
    else -> println("其他")
}
```

### 异常捕获

在 Kotlin 中是不需要强制捕获异常的 `try - catch`

### 循环

简单使用下循环

```kotlin
val kotlinInfos = ArrayList<KotlinInfo>()
// java 使用 
for (KotlinInfo kotlinInfo : kotlinInfos) { ...}
// kotlin 使用
for (kotlinInfo in kotlinInfos) { ...}
```

### 注释

注释中可以在任意地方使用`[]`来引用目标，代替`@param` `@link`等。

### open | final

Kotlin 中的类和函数，默认是被`final`修饰的，但是`abstract`和`override`是例外.

### 问题

基本可以上手了，在学习总结的同时仍然存在一些疑问！

- setter & getter
  - 什么情况默认生成？
  - 为什么可以省略`setter&getter`直接访问变量？
  - 为什么有些不会提示你可以直接使用变量？
- 为什么 Kotin 要舍弃`static`？而 Kotlin 的静态函数又不像 Java 的`static`呢？
- 为什么 Kotlin 不需要强制捕获异常？