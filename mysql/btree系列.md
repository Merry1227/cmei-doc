#B-tree 系列
* B-tree:用于外存数据查找，由于其整体平衡的特性，可以减少查盘时间。一般数据库文件多用此结构。

<http://blog.csdn.net/hbhhww/article/details/8206846>

* 红黑树Red Black Tree：B-tree为保证整体平衡，查找时间减少，但是插入时调整树的平衡所花的时间复杂度高。而红黑树的原则则是尽量保证局部平衡，子树不相差太远，调整时这要o(logn)此颜色变量，且调整次数不超过三次。适用于内存查找，微软STL的Map就是用它实现的。

<http://blog.chinaunix.net/uid-26575352-id-3061918.html>
<http://blog.csdn.net/eric491179912/article/details/6179908>
<http://www.cnblogs.com/fornever/archive/2011/12/02/2270692.html>
<http://blog.sina.com.cn/s/blog_625cce080100fh43.html>

* R-tree：是B-tree向多维空间发展的另一种形式，适用于空间查询。  
<http://en.wikipedia.org/wiki/R-tree>
<http://blog.csdn.net/jiqiren007/article/details/5377750>