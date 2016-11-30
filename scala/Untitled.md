Scala 基础
#基础教程
<http://www.runoob.com/scala/currying-functions.html>  

#一些不同的用法
* Option,Some,None:Some和None 都是 Option 的子类，有值用 Some，无值用 None。
<http://www.jianshu.com/p/95896d06a94d>  

#匿名函数，高阶函数，闭包，柯里化  
* 闭包：更侧重用于内部函数对外部变量的访问。实际应用：当外部函数或你需要定义的函数已经确定好输入输出，但是你又必须 access 其内部，并且需要根据外部环境来影响内部实现的时候。
* 柯里化：更侧重用于于函数的构造器，用于构造变化的函数，将两个参数变成一个参数，每次返回为一个函数。实际应用:处理编程和运行期变化的部分与不变的部分。 偏导？
  <http://blog.csdn.net/a2011480169/article/details/53151279>  

#apply和unapply
* 通过 apply 方法我们无需使用 new 操作就可以创建对象。 将（）映射到 apply 方法上
* unapply方法算是apply方法的反向操作：unapply接受一个对象，然后从对象中提取值，提取的值通常是用来构造该对象的值。
<http://blog.csdn.net/pzw_0612/article/details/48576569>
  