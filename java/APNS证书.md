#ios应用中的证书生成
在此之前建议温习下网络课上的数字签名，ssl相关的知识，可加深理解，避免盲目尝试走弯路。

##openssl 命令：

<http://blog.csdn.net/as3luyuan123/article/details/16105475>

<http://blog.csdn.net/fym0121/article/details/7987869>

<http://blogs.msdn.com/b/kaushal/archive/2010/11/05/ssl-certificates.aspx>

##证书生成步骤

1. 生成CSR
	
	CSR：Certificate Signing Request,证书签名请求。
	
	每申请一个数字证书前，需要提供一个证书签名请求。
	如何生成CSR：  
	（Mac->Application/Utilies->Request a Certificate from a Certificate Authority...)
	
	具体参考
	<http://www.raywenderlich.com/32960/apple-push-notification-services-in-ios-6-tutorial-part-1>
	<http://blog.csdn.net/chowpan/article/details/17927747>

2. 在apple developer 页面新创证书的步骤中，选择csr文件路径，然后你的csr将会发送给一个证书颁发机构（ios developer portal), 它会根据csr的信息生产ssl证书。（根据ssl相关的理论知识，产生的这个证书将会关联一组public key和private key）

3. 证书产生之后，在 Mac Keychain Access.app里面选定新生产的证书，导出到桌面，保存成p12格式。

在这一步中：可能存在三种组合的导入  

* 证书
* 证书+private key （错误！！！，会到这private key导入两次）
* private key

这三种导入导致p12的大小不同，然后再将其转换成后端需要的格式时，也会不同，这是通常容易出问题的地方。我认为，不管后端需要什么，只有导入1）就行了。既然证书一旦生产就必然绑定了private key和public key，说明这些东西都可以通过1）提取到。

###如何产生backend所需的证书格式

不同语言的backend可能需要不同的格式，这些格式基本上都可以通过上一步的p12进行格式转换得到。关键是首先搞清楚你的库需要的是什么格式的。网上很多将apns不work由于证书导致，但是很多时候，并不是上一步产生的证书有问题，而是不用的应用和后端所需的证书格式不同，导致很多人只是简单的copy他人的步骤，但是却不能满足自己的应用的lib的要求。

好吧，既然如此，不妨先用openssl的命令验证下证书吧。

以apns为例，假如上一步生产的p12证书为apns.p12

假如你的程序需要的是一个单独的cert.pem 和key.pem，那么可以通过以下步骤转换

1. cert.pem 有两种方式可得：

* 从apple developer页面download 证书，存为cert.cer,然后执行  
 
  ```openssl x509 -in cert.cer -inform der -out cert.pem```

* 从p12转换 

　``` openssl pkcs12 -clcerts -nokeys -in apns.p12 -out cert.pem```

2. key.pem  

   * 不对私钥加密  
   
   ```
   openssl pkcs12 -nocerts -nodes -in apns.p12 -out key0.pem
   ```

   * des3加密私钥  (-des3 为默认设置，会对private key进行加密)  
   
  ```
     openssl pkcs12 -nocerts -in apns.p12 -out key1.pem
  ```   
     此时需要你输入密码。注意此处的密码并不是生成证书时需要你提供用来保护文件的import password，而是设置private key内容本身进行提取时的密码，而不是pem文件的密码。

PS：此处如果你感兴趣的话，可以通过cat key0.pem 和cat key1.pem比较内容，来发现两者的区别。private key部分内容是不一样的，而且开始标志也不一样，前者为-----BEGIN PRIVATE KEY-----,后者为-----BEGIN ENCRYPTED PRIVATE KEY-----。 

   * 转化成 RSA 私钥，并将结果用des3加密  
   
   ```
   openssl rsa -in key0.pem -out key2.pem -des3
   ```

3.或者你可能需要一个key+cert的pem  

   ```
   cat cert.pem key.pem>apns.pem
   ```
 

假如你的程序需要的是一个整合的带private key的pem，那么

 
##不同应用需要的证书

###APNS

1. java+com.notnoop.apns:apns.p12

2. php  apns.pem


###PASSBOOK

1. java+org.bouncycastle:cert.pem key2.pem