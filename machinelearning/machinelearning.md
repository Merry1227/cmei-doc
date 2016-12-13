#Machine Learning 学习笔记
Andrew Ng的课程真是通俗易懂，读了几年的数据挖掘，很多东西还是一直半解。五年之后上了coursera温习下，发现很多基本东西都通了。
<https://www.coursera.org/learn/machine-learning/lecture/8SpIM/gradient-descent>

#基础
1.假设函数：
hypothesis h(x): 是特征变量 x 与标签 y 之间的函数，x 是自变量， 参数是固定的。我们的目标就是求这个参数。  
2. cost function: J 自变量是假设函数的参数，即表示的参数的变化如何影响最终的值得。这个值越小，则意味着该参数所形成的最终h(x）在训练集上的总的差距最小。  
3. 梯度下降 Gradient Descent：是求 cost function 最小值的一种方式。在同一个圈上的点，所得的 cost function值相同。    

```So we have our hypothesis function and we have a way of measuring how well it fits into the data. Now we need to estimate the parameters in the hypothesis function. That's where gradient descent comes in.``` 


![h-cf](hypothesis-costfunction.png) 
笔记：梯度 可以对应数学中求导（derivative）来理解（求偏导）。导数的含义可以对应物理里面的加速度，反应的是速度的变化。如果再二次求导，则意味着反应参数的变化速度了。这也就是为啥很多公式迭代逼近最终参数的方式都是先设一个初始值，然后将偏导作为增量加入下次迭代中的基本原理。  
在每次迭代中，需要同时加上所有参数的变化，而不是一次一个参数依次变化。
![gradient-decent-algorithm](gradient-descent-algorithm.png)
4. Learning Rate 学习曲线：
*  learning rate 反应的参数的变化，也就是参数的斜率slope，它的大小觉得了 J 的收敛速度。
*  为正时，表示参数递增至收敛，为负数，表示参数递减至收敛
![learning-rate2](learning-rate1.png)
* learning rate 设置过小，影响收敛速度，设置过大则容易偏离最终值。
![learning-rate1](learning-rate2.png)
* learning rate 为固定值既可以保证会朝着收敛的方向**慢慢**逼近，无需每次再修改调整。'
![learning-rate3](learning-rate3.png)



