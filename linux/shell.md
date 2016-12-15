#shell 基础教程
#基础教程
参考：<http://c.biancheng.net/cpp/view/2739.html>
##变量
注意点：

* 注意，变量名和等号之间不能有空格，这可能和你熟悉的所有编程语言都不一样  
* \${variableName} 尽量加{}  

##特殊变量
<http://c.biancheng.net/cpp/view/2739.html>  

##环境变量 set、env、export
<http://www.linuxidc.com/Linux/2012-06/61855.htm> 
<http://www.linuxidc.com/Linux/2011-08/40478.htm>  
 linux 分 shell变量(set)，用户变量(env)， shell变量包含用户变量，export是一种命令工具，是显示那些通过export命令把shell变量中包含的用户变量导入给用户变量的那些变量. 
  

* set:显示(设置)shell变量 包括的私有变量以及用户变量，不同类的shell有不同的私有变量 bash,ksh,csh每中shell私有变量都不一样
* env:显示(设置)用户变量变量
* export:显示(设置)当前导出成用户变量的shell变量。
* 使用unset命令来清除**环境变量**，注意set env  export设置的变量，都可以用unset来清除



##变量替换  
* ""里面的变量会被替换。是否替换转义字符，可以看具体命令的选项。如 echo -e 表示对转义部分进行替换。
*  \`\` 表示执行一个命令，通常用来将命令结果保持到变量中
   如：today=\`date\`
* 变量替换可以根据变量的状态（是否为空、是否定义等）来改变它的值
	* ${var:-defaultValue}:如果变量 var 为空或已被删除(unset)，那么返回 defaultValue，但**不改变 var 的值**。
	* ${var:=defaultValue}:如果变量 var 为空或已被删除(unset)，那么返回 defaultValue，但** 同时将 var 设成新值defaultValue**。
	* ${var:?message}:如果变量 var 为空或已被删除(unset)，那么将消息 message 送到标准错误输出，可以用来检测变量 var 是否可以被正常赋值。  
**若此替换出现在Shell脚本中，那么脚本将停止运行。**
	* ${var:+defaultValue}: 如果变量 var 被定义，那么返回 defaultValue，但不改变 var 的值。    

## 运算 
###算术运算 
   bash 本身不支持简单的数学运算，需要用其他命令来实现，比如 expr,awk 等。注意：  
   
*  表达式和运算符之间要有**空格**，例如 val= 'expr 2+2' 是不对的，必须写成 2 + 2
*  乘号(*)前边必须加反斜杠(\)才能实现乘法运算；
*  $[ ]表示对方括号中的表达式求值 val=$[ 2+8 ].(注意[]内左右都必须留空格,此处+前后可以无空格了，why？)
### 关系原始（数字）
关系运算符只支持数字，不支持字符串，除非字符串的值是数字。
-eq,-ne,-gt,-lt,-ge,-le.
###字符串运算
=,!=,-z(字符串长度为0返回 true）,-n（字符串长度不为0返回 true, 字符串本身（不为空，返回 true，如 if [ $a ] 返回 true）  

### 文件测试  

-d,-f

#重定向  

一般情况下，每个 Unix/Linux 命令/进程运行时都会打开三个文件，并使用文件描述符来引用这些文件。由于文件描述符不容易记忆，shell同时也给出了相应的文件名： 

* 标准输入文件(stdin)：stdin的文件描述符为0，Unix程序默认从stdin读取数据。（缺省是键盘，也可以是文件或者其他命令的输出）
* 标准输出文件(stdout)：stdout 的文件描述符为1，Unix程序默认向stdout输出数据。（缺省是屏幕，也可以是文件）  
* 标准错误文件(stderr)：stderr的文件描述符为2，Unix程序会向stderr流中写入错误信息。（缺省是屏幕，也可以是文件）  
* /dev/null 是一个特殊的文件，写入到它的内容都会被丢弃；如果尝试从该文件读取内容，那么什么也读不到。但是 /dev/null 文件非常有用，将命令的输出重定向到它，会起到”禁止输出“的效果。  

系统中实际有12个文件描述符，可以任意使用文件描述符3-9


例子：  

* command > file: 将 stdout 重定向到 file，注意 file 会被覆盖（如果存在的话）
* command > file: 将 stdout 重定向，追加到文件file末尾
* command < file: 将stdin 重定向到 file。
* command > file 2>&1: 将stdout重定向到 file，将stderr重定向到 file。 （stdout和stderr合并后到file)
* command > /dev/null：如果希望执行某个命令，但又不希望在屏幕上显示输出结果
* command > /dev/null 2>&1： 如果希望屏蔽 stdout 和 stderr

exec 3<&0 0<name.txt   //设置3为标准输入 name.txt的内容输入到标准输入  此时的exec不会清除环境变量，也不会重新登录，覆盖当前的sehll

# 文件
## 文件包含
. filename 或者 source filename  
注意：注意点号(.)和文件名中间有一 **空格**。  
被包含脚本不需要有执行权限。 

#常用命令
##read   
从键盘或者文件的某一行文本中读如信息，并将其赋给一个变量read varible1 varible2 如果只指定了一个变量，那么read将会把所有的输入赋给该变量，直到遇到第一个文件结束符或回车；如果给出了多个变量，它们按吮吸分别赋给不同的变量，。shell将用空格作为变量之间的分隔符  举例说明：在shell脚本中有read A B C命令行，而在提示的命令行中输入1 2 3，则A=1，B=2，C=3   
 
##tee   
 把输出的一个副本输送到标准输出，另一个副本拷贝到相应的文件中. tee -a file 这个命令一般用于管道之后。  举例说明：  cat file|tee file.out 将file中的信息输出到屏幕并拷贝到file.out中  
##管道  
 可以通过管道把一个命令的输出作为另一个命令的输入举例说明：df -k |awk '{print $1}'|grep -v "Filesystem"df：显示所有文件系统对i节点和磁盘块的使用情况awk:浏览和抽取信息grep：文本过滤显示所有文件系统对i接点和磁盘块的使用情况信息中的第一列并不含有"Filesystem"的信息  
## find  （1）查找具有某些特征文件的命令，可遍历当前目录甚至于整个文件系统来查看某些文件或目录，其遍历大的文件系统时一般放在后台执行。    （2）find命令的一般形式      find pathname -options [-print -exec -ok]  
     -pathname :find命令所查找的目录路径。如用"."来表示当前的目录，用/来表示系统根目录      -print :find命令将匹配的文件输出到标准输出      -exec: find命令对匹配的文件执行该参数所给出的shell命令，相应的命令形式为    'command'{} \; (注意{}和\之间的空格)      -ok 和 -exec的作用相同，只不过以一种更为安全的模式来执行该参数所给出的shell命令，在执行每一个命令之前，都会给出提示，让用户来确定是否执行。  
        **options有如下几种：**    -name ：按照文件名查找文件      -perm ：按照文件权限来查找文件      -user ：按照文件属主来查找文件      -group ：按照文件所属的组来查找文件      -mtime -n +n 按照文件的更改时间来查找文件，-n表示文件更改时间距现在n天以内，+n表示文件更改时间距现在n天以前。find命令还有-atime 和-ctime选项，但它们都和-mtime选项相似。    -size n[c]查找文件长度为n块的文件，带有c时表示文件长度以字节计。    -nogroup 查找无有效所属组的文件，即该文件所属的组在/etc/groups中不存在    -newer file1 !file2查找更改时间比文件file1新但比文件file2旧的文件    -depth 先查找指定目录有无匹配文件，若无则再在子目录中查找    -type 查找某一类型的文件，如      b :块设备文件      d：目录      e：字符设备文件      p；管道文件      l：符号链接文件      f：普通文件  
    （3）find命令举例      find -name "*.txt" -print 查找txt结尾的文件并输出到屏幕上      find /cmd ".sh" -print 查找/cmd目录下所有sh文件，并输出      find . -perm 755 -print 查找当前目录下权限为755的文件，并输出        find `pwd` -user root -print 查找当前目录下属主为root的文件，并输出      find ./ -group sunwill -print 查找当前目录下所属主是sunwill的文件      find /var -mtime -5 -print 查找/var目录下更改时间为5天内的所有文件      find /var -mtime +5 -print 查找/var目录下更改时间为5天以前的所有文件      find /var -newer "myfile1" ! -newer "myfile2" -print 查找/var目录下比myfile1新，但是比myfile2旧的所有文件。      find /var -type d -print 查找/var目录下所有目录      find /var -type l -print 查找/var目录下所有的符号链接文件。      find . -size +1000000c -print 查找当前目录下大于1000000字节的文件      find / -name "con.file" -depth -print 查找根目录下有无"con.file",若无则在其子目录中查找      find . -type f -exec ls -l {} \; 查找当前目录下是否有普通文件，若有则执行ls -l  
    
##  xargs    在使用find命令的-exec选项处理匹配到的文件时，find命令将所有匹配到的文件一起传递给exec。不幸的是，有些系统对能够传递给exec的命令长度有限制，这样find命令运行几分钟之后就算出现溢出错误。错误信息通常是“参数列太长”或“参数列溢出”。这就是xargs的用处所在，特别是与find命令一起使用,exec会发起多个进程，而xargs会多个，只有一个      find ./ -perm -7 -print | xargs chmod o-w 查找权限为7的文件并传递给chmod处理 
## grep1. grep 的一般格式为 grep [options] 基本正则表达式 [文件]      字符串参数最好采用是双引号括，一是以防被误解为shell命令，二是可以用来查找多个单词组成的字符串      -c：只输出匹配行的记数      -i：不区分大小写（只适用于单个字符）      -h：查询多个文件时不显示文件名      -H：只显示文件名      -l：查询多文件时只输出包含匹配字符的文件名      -n：只显示匹配行及其行号      -s：不显示不存在或无匹配文本的错误信息。      -v：显示不包含匹配文本的所有行。  2. 举例说明：      grep ^[^210] myfile 匹配myfile中以非2、1、0开头的行      grep "[5-8][6-9][0-3]" myfile 匹配myfile中第一位为5|6|7|8，第二位6|7|8|9，第三位为0|1|2|3的三个字符的行      grep "4\{2,4\}" myfile 匹配myfile中含有44,444或4444的行      grep "\?" myfile匹配myfile中含有任意字符的行  
3. grep命令类名      [[:upper:]]   表示[A-Z]      [[:alnum:]]   表示[0-9a-zA-Z]      [[:lower:]]   表示[a-z]      [[:space:]]   表示空格或者tab键      [[:digit:]]   表示[0-9]      [[:alpha:]]   表示[a-zA-Z]    如：grep "5[[:digit:]][[:digit:]]" myfile 匹配myfile中含有5开头接下去两位都是数字的行。  
  
##awk可以从文件或字符串中基于指定规则浏览和抽取信息，是一种自解释的编程语言。    1. awk命令行方式 awk [-F filed-spearator] 'command' input-files      awk脚本：所有awk命令插入一个文件，并使awk程序可执行，然后用awk命令解释器作为脚本的首行，以便通过键入脚本名称来调用它。awk脚本是由各种操作和模式组成。     模式部分决定动作语句何时触发及触发事件。（BEGIN,END）    动作对数据进行处理，放在{}内指明（print）  2. 分隔符、域和记录    awk执行时，其浏览域标记为$1,$2,...$n.这种方法成为域标识。$0为所有域。  3. 举例说明:      awk '{print $0}' test.txt |tee test.out 输出test.txt中所有行$0表示所有域      awk -F : '{print $1} test.txt |tee test.out'   同上。。只是分隔符为":"      awk 'BEGIN {print "IPDate\n"}{print $1 "\t" $4} END{print "end-of-report"}' test.txt      开始时打印“IPDate”结束时打印“end-of-report”中间打印主体信息，比如总共匹配三条信息，则输出如下：     IPDate  1 first   

##sed
sed 命令的options如下      -n：不打印      -c：下一命令是编辑命令      -f：如果正在调用sed脚本文件  
    
sed在文件中查询文本的方式        --使用行号，可以是一个简单的数字，或是一个行号的范围        --使用正则表达式  读取文本的方式         x     x为一行号        x,y     表示行号范围从x到y        /pattern/   查询包含模式的行        /pattern/pattern/ 查询包含两个模式的行        pattern/,x   在给定的行号上查询包含模式的行        x,/pattern/   通过行号和模式查询匹配行        x,y!     查询不包含指定行号x和y的行      
 基本sed编辑命令          p   打印匹配行          d   删除匹配行          =   显示文件行号          a\   在定位行号后附加新文本信息          i\   在定位行号后插入新文本信息          c\   用新文本替换定位文本          s   使用替换模式替换相应模式          r   从另一个文件中读文件          w   写文本到一个文件          q   第一个模式匹配完成后推出或立即退出          l   显示与八禁止ASCII代码等价的控制字符          {}   在定位行执行的命令组          n   从另一个文件中读文本下一行，并附加在下一行          g   将模式2粘贴到/pattern n/          y   传送字符  
           举例说明：          sed -n '2p' test.txt 打印第二行的信息(注意：-n是不打印不匹配的信息，若没加-n，则打印文件的所有信息而不是匹配信息)        sed -n '1,4p' test.txt 打印第一行到第四行的信息        sed -n '/los/p' test.txt模式匹配los，并打印出来        sed -n '2,/los/p' test.txt 从第二行开始。。知道匹配第一个los        sed -n '/^$/p' test.txt 匹配空行        sed -n -e '/^$/p' -e '/^$/=' test.txt 打印空行及行号        sed -n '/good/a\morning' test.txt 在匹配到的good后面附加morning        sed -n '/good/i\morning' test.txt 在匹配到的good前面插入morning        sed -n '/good/c\morning' test.txt 将匹配到的good替换成morning        sed '1,2d' test.txt 删除第1和2行        sed 's/good/good morning/g' test.txt 匹配good并替换成goodmorning        send 's/good/& hello /p' test.txt 匹配到good就在其后面加上hello        send 's/good/ hello &/p' test.txt 匹配到good就在其前面加上hello    
        
#日志文件  
1. 创建日志文件的重要性：    记录重要的信息，如登录、运行情况、监控等。。为我们分析和排除错误非常有帮助  2. 以时间为标识的日志文件    适用于长期存储的日志。举个创建以时间为标识日志的例子入下：  

```  #!/bin/bash  #datelog.sh  current_date=`date ""+%Y%m%d` #当前的日期（ 年月日 ）  todaylog="log/${current_date}.log" #今天的日志文件名  #如果日志文件不存在，创建一个  if [ ! -f $todaylog ]    then    touch $todaylog  fi  #输出日志到日志文件  log_time_format=`date "+%Y-%m-%d %T"`  echo "${log_time_format} commands start.....">>${todaylog}  # commands blocks  sleep 4  log_time_format=`date "+%Y-%m-%d %T"`  echo "${log_time_format} commands end!">>${todaylog} #结束时记录日志
```
3. 以进程号为标识的临时文件  一般记录脚本运行过程中需要暂时记录的信息，一旦脚本运行结束，这些信息就可以删除。举个以进程号为标识的日志的例子：    #!/bin/bash    #kill_process.sh    #取得当前进程号    current_pid=$$    #获得特定进程号并重定向到一个临时文件中    ps -aux|grep "/usr/sbin/httpd" | grep -v "grep"| awk '{print $2}' >/tmp/${current_pid}.txt    #commands start    for pid in `cat /tmp/${current_pid}.txt    do    {      echo "kill -9 $pid"      kill -9 $pid    }    done     rm -f /tmp/${current_pid}.txt   //脚本运行结束前删除该日志文件  

  
#信号  
* 信号就是系统向脚本或命令发出的消息，告知它们某个事情的发生。* kill -l 列出所有的信号* kill发送信号给进程  一些信号的含义：  信号   信号名   含义  
  0             退出shell信号，可以从命令行输入exit或在一个进程或命令行中使用<CTRL-d>    1   SIGHUP     挂起或父进程被杀死    2   SIGINT     来自键盘的中断信号，通常是<CTRL-C>    3   SIGOUIT   从键盘退出    9   SIGKILL   无条件终止   11   SIGSEGV   段（内存）冲突   15   SIGTERM   软件终止(缺省杀进程)  （另外还有50多种信号，可以用kill -l 命令查看）  
举例说明：kill -s SIGKILL 1234 表示无条件终止进程1234(进程号)     通过kill -9 1234 其含义与上面一样。  　　kill -1 1234 和kill -s SIGHUP 1234都是挂起 进程号为1234的进程        　　**trap捕捉信号**  
  
* 信号可以被应用程序或脚本捕获，并依据该信号（１、２、３和１５）采取相应的行动。一些信号不能被捕获。如，如果一个命令收到了信号９，就无法再捕获其他信号。  
* 捕捉到一个信号后，可能会采取三中    　１).不采取任何行动，由系统来进行处理（如信号９由系统处理）  　２).捕获该信号，但忽略它  　３).捕获该信号，并采取相应的行动  
　  * trap可以使你在脚本中捕捉信号，命令形式为trap name signal(s)      其中,name是捕捉到信号以后所采取的一系列操作。实际中，name一般是一个专门用来处理所捕捉信号的函数。name需要用双引号（“”）引起来。signal是待捕捉的信号。  最常见的行动包括：  １) 清除临时文件２) 忽略该信号(如trap "" 23)  ３)询问用户是否终止该脚本进程  举例说明：  

```　　#!/bin/bash    #trap.sh    trap 'exitprocess' 2　　　　#捕捉到信号２之后执行exitprocess function    LOOP=0    function exitprocess()    {    echo "You Just hit <CTRL-C>, at number $LOOP"    echo "I will now exit"    exit 1    }    while :   # 循环直到捕捉到信号（注意中间的空格）     do        LOOP=$[ $LOOP+1 ]        echo $LOOP　　　　　　　　　sleep 1    done  e
```
                     
     
    
    
      

 
   

