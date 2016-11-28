非常详细的实用教程。
<http://blog.csdn.net/allwefantasy/article/details/3136570/>
给出了非常有用的实例：包括常见的电话，ip，日期。
更赞的是给出了很多的应用场景。
比如日志提取。

#速查笔记 
<http://www.runoob.com/regexp/regexp-syntax.html>

正则格式：匹配字符的定义后接匹配次数

匹配符号定义：  

- **.**：通配符匹配所有，包括空格，tab，换行等
- []: **只**匹配单个字符，^表示否，与[]连用较多
- （）：与或|一起使用，或者用于分组group  

常用符号:  
 
- [0-9]:\d
- [^0-9]:\D
- [A-Z0-9]:\w
- [^A-Z0-9]:\W
- [\t\n\r\f]:\s 匹配任何空白字符
- [^\t\t\r\f]:\S

匹配次数定义:  

- *:>=0 次
- +:>=1 次
- ？:0或1次
- {n}: =n 次
- {n,m}: n到m 次。包括 n 次，m 次

匹配替换功能：Python string 提供了 substitute 功能。Java replace。
