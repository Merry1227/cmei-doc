# 资料汇总
* 台湾李宏毅教授: 《1天搞懂深度学习》
https://www.slideshare.net/tw_dsconf/ss-62245351?qid=108adce3-2c3d-4758-a830-95d0a57e46bc&v=&b=&from_search=3

* https://www.zhihu.com/question/26006703

* https://blog.csdn.net/vincent2610/article/details/52708863?locationNum=14

* 中文tensorflow: http://www.tensorfly.cn/tfdoc/tutorials/overview.html

TensorFlow里面的tensor指张量，其实可以理解为一种数据的描述和表示方法。
   * tensorflow: 后端是C++, python是客户端， session是客户端与后端的连接，tensorflow原理类似跟其他分布式计算原来类似，
   先客户端定制计算图（即定义你要做什么), 然后session连接，最后run触发计算过程。
   除此之外，还可以根据InteractiveSession, 来进行交互，在允许图的时候，插入一些计算图。
   * tensor： 张量 TODO 了解张量的各种表示法，
```
   在同构的意义下，第零阶张量（r = 0） 为标量（Scalar），
   第一阶张量 （r = 1） 为向量 （Vector）， 第二阶张量 （r = 2） 则成为矩阵 （Matrix）。
   例如，对于3维空间，r=1时的张量为此向量：（x,y,z）。由于变换方式的不同，张量分成协变张量 （Covariant Tensor，指标在下者）、逆变张量 （Contravariant Tensor，指标在上者）、 
   混合张量 （指标在上和指标在下两者都有）三类.
```   

# logistic回归和softmax回归
## logistic解决二分类问题，softmax解决多分类问题
https://blog.csdn.net/vincent2610/article/details/52708863?locationNum=14  

要点记录：
* softmax(f(x)) 可以将一个线性函数f(x)输出转换成概率值形式。
* 这里的softmax可以看成是一个激励（activation）函数或者链接（link）函数，把我们定义的线性函数的输出转换成我们想要的格式。

```
   一直以来，对这个activation函数的作用不是很理解，现在感觉有点豁然开朗，至少来说，激励函数的其中一个作用就是将线性函数转成概率形式。
   因为线性函数的输出其实值域范围非常大，通过softmax这种指数类型激活函数，可以将任意值域转换到[0,1]
```

* TensorFlow安装：
分布式：https://blog.csdn.net/CodeMaster_/article/details/76223835


# 深度学习问题
* 维度灾难(Curse of dimensionality)
* 梯度下降加上代价函数的时候， 样本空间太大(几亿)时候，计算代价太大 （抽样成几百）