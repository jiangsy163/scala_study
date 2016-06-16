**A Scala Tutorial for Java Programmers**

**A First Example**

	object HelloWorld {
	  def main(args: Array[String]) {
	    println("Hello, world!")
	  }
	}

 main为 static 方法.在Scala不存在静态字段或者方法.定义静态变量使用全局单例对象.

**Compiling the example**

使用 scalac编译，生产标准 Java class 文件，执行时使用scala命令：

		scalac HelloWorld.scala

**Running the example**
	
	> scala -classpath . HelloWorld
	> 
	Hello, world!

**Interaction with Java**

默认导入 java.lang包.

	import java.util.{Date, Locale}
	import java.text.DateFormat
	import java.text.DateFormat._
	
	object FrenchDate {
	  def main(args: Array[String]) {
	    val now = new Date //Java类
	    val df = getDateInstance(LONG, Locale.FRANCE)
	    println(df format now)
	  }
	}

导包字符 (_) 替换 (*).  (*)为有效标识符.

导入 DateFormat 类中的所有成员. 这样可以直接使用静态方法 getDateInstance和静态字段 LONG 可见.

单一参数的函数可以使用插入语法：

	df format now
	df.format(now)

用Scala同样可以实现Java类和接口.

**Everything is an Object**

Scala是纯对象语言, 所有事物都是对象，包括数字和方法.

**Numbers are objects**

数学表达式:

		1 + 2 * 3 / x
consists exclusively of method calls, because it is equivalent to the following expression, as we saw in the previous section:

	(1).+(((2).*(3))./(x))
This also means that +, *, etc. are valid identifiers in Scala.
The parentheses around the numbers in the second version are necessary because Scala’s lexer uses a longest match rule for tokens. Therefore, it would break the following expression:
1.+(2)
into the tokens 1., +, and 2. The reason that this tokenization is chosen is because 1. is a longer valid match than 1. The token 1. is interpreted as the literal 1.0, making it a Doublerather than an Int. Writing the expression as:
(1).+(2)
prevents 1 from being interpreted as a Double.

**Functions are objects**

函数也是对象，函数式编程.把方法作为值. 使用用户接口代码注册回调函数.
定时方法oncePerSecond把回调方法作为一个参数.回调方法的写法 () => Unit ，并且不是所有方法都有返回值(类型 Unit类似于 void )..

	object Timer {
	  def oncePerSecond(callback: () => Unit) {
	    while (true) { callback(); Thread sleep 1000 }
	  }
	  def timeFlies() {
	    println("time flies like an arrow...")
	  }
	  def main(args: Array[String]) {
	    oncePerSecond(timeFlies)
	  }
	}

**Anonymous functions：匿名方法**

首先,方法timeFlies定义只是为了提交给 oncePerSecond 方法:

	object TimerAnonymous {
	  def oncePerSecond(callback: () => Unit) {
	    while (true) { callback(); Thread sleep 1000 }
	  }
	  def main(args: Array[String]) {
	    oncePerSecond(() =>
	      println("time flies like an arrow..."))
	  }
	}
右箭头 => 区分方法体和参数. 上例参数为空

**Classes**

Scala是一种原生对象语言, Scala与java类似，区别在于Scala类可以定义有参数.两个参数，初始化必须提交，方法中有两个方法用于访问两个部分。

	class Complex(real: Double, imaginary: Double) {
	  def re() = real
	  def im() = imaginary
	}
两个方法返回类型没有指定. 右手方将自动推测返回类型 Double.

**Methods without arguments/ 无参方法**

调用方法re 和im 时需要添加空括号:

	object ComplexNumbers {
	  def main(args: Array[String]) {
	    val c = new Complex(1.2, 3.4)
	    println("imaginary part: " + c.im())
	  }
	}
定义无参无括号方法来访问字段，赋值取值均不用括号:

	class Complex(real: Double, imaginary: Double) {
	  def re = real
	  def im = imaginary
	}

**Inheritance and overriding/ 继承和覆盖**

默认所有类继承于超类，默认继承scala.AnyRef.

	class Complex(real: Double, imaginary: Double) {
	  def re = real
	  def im = imaginary
	  override def toString() =
	    "" + re + (if (im < 0) "" else "+") + im + "i"
	}

**Case Classes and Pattern Matching/Case类和模式匹配**

Java中使用抽象超类及其子类来定义父子关系, Scala 中使用case classes 区分两个类:


**Traits/特征：类似Java 实现**

In Java, 中排序实现Comparable 接口. In Scala, 定义等效Comparable 作为特征  Ord.  特征定义如下:

	trait Ord {
	  def < (that: Any): Boolean // Any为所有类型的超类
	  def <=(that: Any): Boolean =  (this < that) || (this == that)
	  def > (that: Any): Boolean = !(this <= that)
	  def >=(that: Any): Boolean = !(this < that)  
	}
	the Date 类特征定义如下:
	class Date(y: Int, m: Int, d: Int) extends Ord {
	  def year = y
	  def month = m
	  def day = d
	  override def toString(): String = year + "-" + month + "-" + day
	override def equals(that: Any): Boolean =
	  that.isInstanceOf[Date] && {// 是否为实例
	    val o = that.asInstanceOf[Date] // 作为实例
	    o.day == day && o.month == month && o.year == year
	  }
	def <(that: Any): Boolean = {
	  if (!that.isInstanceOf[Date])
	    error("cannot compare " + that + " and a Date")
	
	  val o = that.asInstanceOf[Date]
	  (year < o.year) ||
	  (year == o.year && (month < o.month ||
	                     (month == o.month && day < o.day)))
	}
**Genericity/泛型**

类容器作为引用,可以为空或对象的类型

	class Reference[T] {
	  private var contents: T = _
	  def set(value: T) { contents = value }
	  def get: T = contents
	}
The class Reference 是一个类型参数 T . 作为类内部变量的类型 contents variable.

	object IntegerReference {
	  def main(args: Array[String]) {
	    val cell = new Reference[Int]
	    cell.set(13)
	    println("Reference contains the half of " + (cell.get * 2))
	  }
	}
