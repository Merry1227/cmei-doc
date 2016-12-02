Scala笔记
#基础教程
<http://www.runoob.com/scala/currying-functions.html>  

#一些不同的用法
* Option,Some,None:Some和None 都是 Option 的子类，有值用 Some，无值用 None。
<http://www.jianshu.com/p/95896d06a94d>  

#匿名函数，高阶函数，闭包，柯里化  
* 闭包：更侧重用于内部函数对外部变量的访问。实际应用：当外部函数或你需要定义的函数已经确定好输入输出，但是你又必须 access 其内部，并且需要根据外部环境来影响内部实现的时候。
	def makeIncreaser(more: Int) = (x: Int) => x + more	The function value (the object) that’s created at runtime from this function literal is called a closure. The name arises from the act of “closing” the function literal by “capturing” the bindings of its free variables. A function literal with no free variables, such as (x: Int) => x + 1, is called a closed term, where a term is a bit of source code. Thus a function value created at run- time from this function literal is not a closure in the strictest sense, because (x: Int) => x + 1 is already closed as written. But any function literal with free variables, such as (x: Int) => x + more, is an open term. Therefore, any function value created at runtime from (x: Int) => x + more will by definition require that a binding for its free variable, more, be captured. The resulting function value, which will contain a reference to the captured more variable, is called a closure, therefore, because the function value is the end product of the act of closing the open term, (x: Int) => x + more. 
* 柯里化：更侧重用于于函数的构造器，用于构造变化的函数，将两个参数变成一个参数，每次返回为一个函数。实际应用:处理编程和运行期变化的部分与不变的部分。 偏导？
  <http://blog.csdn.net/a2011480169/article/details/53151279>  
  

#apply和unapply
* 通过 apply 方法我们无需使用 new 操作就可以创建对象。 将（）映射到 apply 方法上
* unapply方法算是apply方法的反向操作：unapply接受一个对象，然后从对象中提取值，提取的值通常是用来构造该对象的值。
<http://blog.csdn.net/pzw_0612/article/details/48576569>


#To trait, or not to trait? 
Traits类似 java 的 interface，但是可以实现方法，而不只是声明方法其他类可以 用关键字 extends  或者 with 来 mix-in traits 而非class inheritance 关系 Thin Interface and Rich InterfaceWhenever you implement a reusable collection of behavior, you will have to decide whether you want to use a trait or an abstract class. There is no firm rule, but this section contains a few guidelines to consider. If the behavior will not be reused, then make it a concrete class. It is not reusable behavior after all. If it might be reused in multiple, unrelated classes, make it a trait. Only traits can be mixed into different parts of the class hierarchy. If you want to inherit from it in Java code, use an abstract class. Since traits with code do not have a close Java analog, it tends to be awkward to inherit from a trait in a Java class. Inheriting from a Scala class, meanwhile, is exactly like inheriting from a Java class. As one exception, a Scala trait with only abstract members translates directly to a Java interface, so you should feel free to define such traits even if you expect Java code to inherit from it. See Chapter 31 for more information on working with Java and Scala together. If you plan to distribute it in compiled form, and you expect outside groups to write classes inheriting from it, you might lean towards using an abstract class. The issue is that when a trait gains or loses a member, any classes that inherit from it must be recompiled, even if they have not changed. If outside clients will only call into the behavior, instead of inheriting from it, then using a trait is fine.If efficiency is very important, lean towards using a class. Most Java runtimes make a virtual method invocation of a class member a faster oper- ation than an interface method invocation. Traits get compiled to interfaces and therefore may pay a slight performance overhead. However, you should make this choice only if you know that the trait in question constitutes a per- formance bottleneck and have evidence that using a class instead actually solves the problem. If you still do not know, after considering the above, then start by making it as a trait. You can always change it later, and in general using a trait keeps more options open. 

#Implicit
简单说，是实现了一种类型到任何一种类型的转换，或者说适应。
<http://m.blog.csdn.net/article/detailsd=52138748>
object implictis  
<https://dzone.com/articles/scala-implicits-type-classes>

#类型泛化中的+,-,<:,>:
参考：<http://fineqtbull.iteye.com/blog/477994>

* trait Queue[T] {}   
这是非变情况。这种情况下，当类型S是类型A的子类型，则Queue[S]不可认为是Queue[A]的子类型或父类型，这种情况是和Java一样的。 
* trait Queue[+T] {}    
这是协变情况。这种情况下，当类型S是类型A的子类型，则Queue[S]也可以认为是Queue[A}的子类型，即Queue[S]可以泛化为Queue[A]。也就是被参数化类型的泛化方向与参数类型的方向是一致的，所以称为协变。 
* trait Queue[-T] {}   
这是逆变情况。这种情况下，当类型S是类型A的子类型，则Queue[A]反过来可以认为是Queue[S}的子类型。也就是被参数化类型的泛化方向与参数类型的方向是相反的，所以称为逆变。
* U >: T  
这是类型下界的定义，也就是U必须是类型T的父类(或本身，自己也可以认为是自己的父类)。
* S <: T  
这是类型上界的定义，也就是S必须是类型T的子类（或本身，自己也可以认为是自己的子类)。

  