#shell examples
## 获取当前某个执行文件的根目录字符串
```
if [ -z "${SPARK_HOME}" ]; then
  export SPARK_HOME="$(cd "`dirname "$0"`"/..; pwd)"
fi
```
解析：  

* cd: 改变目录
* dirname "$0"/..:获取当前 shell 脚步的目录路径，然后拼接到期根目录上（.. 同 ../)
* pwd:输出当前目录
* $(命令)：输出某天命令的执行结果

##执行脚本：  



## Find the java binary
```
if [ -n "${JAVA_HOME}" ]; then
  RUNNER="${JAVA_HOME}/bin/java"
else
  if [ `command -v java` ]; then
    RUNNER="java"
  else
    echo "JAVA_HOME is not set" >&2
    exit 1
  fi
fi
```



