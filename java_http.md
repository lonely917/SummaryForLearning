<!-- TOC -->

- [http](#http)
    - [请求报文](#请求报文)
    - [响应报文](#响应报文)
    - [请求方法](#请求方法)
    - [知识点](#知识点)
    - [http1.1 & http2](#http11--http2)
- [https](#https)
    - [加密知识点](#加密知识点)
    - [https概览](#https概览)
    - [https通信全流程](#https通信全流程)
    - [证书的认证和用途](#证书的认证和用途)
    - [https和中间人问题](#https和中间人问题)
    - [一些资料](#一些资料)
- [tcp](#tcp)
    - [tcp连接建立](#tcp连接建立)
    - [tcp连接断开](#tcp连接断开)
- [java httpclient(java11)](#java-httpclientjava11)
- [若干问题思考](#若干问题思考)

<!-- /TOC -->
## http

### 请求报文
1. 请求行
2. 请求头(空行结尾)
3. 请求主体
```
GET https://www.baidu.com/ HTTP/1.1
Host: www.baidu.com
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.120 Safari/537.36
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3
Sec-Fetch-Site: none
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8
Cookie: BIDUPSID=0AA6D27BFAC6E8468FCB3A37FC9796D0;

```

### 响应报文
1. 状态行
2. 响应头(空行结尾)
3. 响应主体
```
HTTP/1.1 200 OK
Bdpagetype: 2
Bdqid: 0xcc8e66db003d6e8a
Cache-Control: private
Connection: Keep-Alive
Content-Type: text/html;charset=utf-8
Date: Sun, 01 Dec 2019 14:28:52 GMT
Expires: Sun, 01 Dec 2019 14:28:52 GMT
Server: BWS/1.1
Set-Cookie: BDSVRTM=212; path=/
Set-Cookie: BD_HOME=1; path=/
Set-Cookie: H_PS_PSSID=1454_21102_30211_20697_29700_22157; path=/; domain=.baidu.com
Strict-Transport-Security: max-age=172800
Traceid: 1575210532275427405814739831721218109066
X-Ua-Compatible: IE=Edge,chrome=1
Content-Length: 256200

<!Doctype html><html xmlns=http://www.w3.org/1999/xhtml><head>   <meta http-equiv=Content-Type content="text/html;charset=utf-8"><meta http-equiv=X-UA-Compatible content="IE=edge,chrome=1"><meta content=always name=referrer> <link rel="shortcut icon" href=/favicon.ico type=image/x-icon> <link rel=icon sizes=any mask href=//www.baidu.com/img/baidu_85beaf5496f291521eb75ba38eacbd87.svg><title>百度一下，你就知道 </title><style id="style_super_inline">body,h1,h2,h3,h4,h5,h6,hr,p,blockquote,
....

```
### 请求方法
1. GET 传输数据，多用于请求数据
2. POST 传输数据，多用于上传数据
3. PUT 上传资源
4. DELETE 删除资源
5. HEAD 和GET类似，但是不返回报文主体，主要用于确认URL有效性
6. PATCH 类似PUT，资源部分修改
6. OPTIONS 查询指定URL支持的方法
7. CONENCT 建立隧道使用
8. TRACE 追踪路径

### 知识点
1. 短链接和长连接
2. 流水线技术 pipeline
3. cookie和session概念
4. 现代浏览器已经由很丰富的存储技术(web storage api & indexedDB )
5. tcp复用概念


### http1.1 & http2



## https
### 加密知识点
1. 对称加密(DES，3DES，AES...)
2. 非对称加密(RSA，椭圆曲线...)
3. 对称加密根据密钥进行加密和解密，通信双方都是用一个相同的密钥加解密；
4. 对称加密的痛点在于密钥的安全传输；
5. 非对称加密使用公钥加密，私钥解密，公钥公开，因此不存在密钥安全传输的问题；
6. 非对称加密的痛点计算复杂、耗时更长；
7. 非对称加密，发送发使用接收方的公钥进行加密，只有接收方有对应私钥才能解密。
8. 非对称加密，发送发可以使用自己的私钥进行签名，接收方可以根据发送方的公钥来验证签名，明确发送方的身份，细化这一过程：发送方先对信息求取摘要，然后使用私钥对摘要加密得到签名，附加在信息后，接收方就可以根据信息计算摘要A，然后根据签名和发送方的公钥还原摘要B，如果A和B一致，说明信息是发送方发来的且信息没有被篡改。

### https概览
https可以理解为http secure，或者http over ssl(Secure Socket Layer)，由于http在协议层是没有加解密机制的(但应用程序可以对报文主体进行加解密，相当于在http协议之上的通信报文主体进行加解密，这也是前后台接口设计过程中经常使用的策略，这里不表)，使用http+ssl可以构建一个安全传输的http协议。https可以理解为将http的通信接口使用ssl/tls(secure socket layer/transport layer)协议，ssl也可以与其他应用层协议结合比如ftp或者smtp等构建安全传输的xx协议。就https而言，ssl位于应用层http和传输层tcp之间。

结合对称加密和非对称加密的长处，在https中使用`非对称加密来传输对称加密的密钥`，使用`对称加密来进行基本的数据传输`。这样既解决了简单对称加密的密钥传输问题，又对非对称算法双向传输需要两对公私钥以及复杂耗时的痛点进行了弱化。

一个简化的https模型：客户端向服务器发起https请求，服务器发送公钥给客户端，客户端生成一个对称加密算法密钥m用于后续和服务器的http加密通信，客户端使用服务器的公钥对m进行加密，服务器使用私钥对报文解密得到对称密钥算法的密钥m，后续双方使用m对http报文进行对称加密解密。

一些延伸：
1. 对称加密算法的选取
2. 公钥的传输
3. 中间人攻击的应对
4. 证书的概念以及验证

### https通信全流程
1. 客户端发起握手请求，client hello;
2. 服务器回应握手请求，server hello;
3. 服务器发送证书(含有自己的公钥);
4. 服务器发送server hello done;
5. 客户端生成随机密码串(pre master secret)，然后发送给服务器，这个过程使用公钥加密；
6. 客户端发送ChangeCiperSpec报文，后续切换加密算法，使用pre master secret可以生成master secret；
7. 客户端发送握手结束报文；
8. 服务器发送发送ChangeCiperSpec报文，后续切换加密算法，使用pre master secret可以生成master secret；
9. 服务器发送握手结束报文；
10. 此时ssl连接已经建立，客户端向服务器发送加密后的http报文，解密使用；
11. 服务器向客户端回应报文一样使用对称加密算法进行加密；
12. 客户端发起断开连接的时候走tcp四次挥手过程。
13. 注意，并非每次https请求都要进行握手协商、证书认证、pms传输的过程，https支持快速恢复，每次连接会有一个session，可以从上一个连接快速恢复。

### 证书的认证和用途
https通信过程中如何明确服务器身份是我想要通信的服务器呢，即如何避免中间人攻击。公钥通过证书发送，证书由权威CA机构颁发，证书包含证书信息和签名(签名是CA机构利用CA的私钥对证书信息进行摘要计算后加密生成)，客户端能够根据证书签名对证书认证，从而确定证书的有效性，进而确定证书中公钥的有效性，也就明确了服务器端身份。对证书签名的认证需要使用CA机构的公钥，这个公钥在操作系统内置的一些证书中。所以上述过程可以这样描述：客户端收到服务器的证书，从证书中获取服务器端的公钥，为了明确这个公钥可信的，需要对证书进行认证，对证书认证就需要证书发布方的公钥，这个公钥在另一个证书中，这个用于验证证书的证书提前已经内置在计算机中。

证书携带有CA机构说明、持有者信息(比如服务器域名)、公钥等信息，还会有一个签名，签名认证通过即可信任证书中的标识信息以及公钥。

### https和中间人问题
通过证书可以解决中间人问题，在没有证书机制的情况下，中间人可以在ssl建立连接的过程中伪造公钥，在客户端和服务器之间转接信息而不被察觉。由于证书的存在，这种中间人机制就不可行了，首先伪造公钥需要伪造证书，中间人需要使用CA发布的证书(这一点也能做到)，但是客户端拿到证书的时候即使验证该证书时合法的，但是其中的身份标识信息是不正确的(比如域名信息)，因此客户端可以发现这个证书的身份和我要请求的地址或者域名是不一致的。

### 一些资料
1. tls概念 https://tools.ietf.org/html/rfc2246
2.  HTTP Over TLS https://tools.ietf.org/html/rfc2818


## tcp
### tcp连接建立

### tcp连接断开


## java httpclient(java11)

1. http://openjdk.java.net/groups/net/httpclient/
2. JEP 110 Delivered the HTTP Client in incubating form in JDK 9
3. JEP 321 Standarized the HTTP Client in Java 11

## 若干问题思考

1. TCP的建立和断开
2. TCP报文格式
3. UDP报文格式
3. HTTP报文格式
4. 浏览器一次完整的请求过程
5. HTTPS解决的痛点
6. HTTPS可替代方案
7. java http 请求相关类
8. java 加解密相关类