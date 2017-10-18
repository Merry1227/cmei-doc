#HTTP

##HTTP headers

##HTTP client
###各种Timeout
分三步：
    1）get a connection from pool
    2) connection.connect
    3) connection. data
ConnectionRequestTimeout:client 从conneciton pool里面获得一个connection的时间。
   happens when you have a pool of connections and they are all busy, not allowing the connection manager to give you one connection to make the request.
   
   
ConnectTimeout:建立一个连接时间
SocketTimeout：等待数据时间，在两次数据包之间的maximum period inactivity 

http://stackoverflow.com/questions/31611861/why-setconnectionrequesttimeout-doesnt-stop-my-1-min-get-request/31612285#31612285