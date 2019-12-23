<!-- TOC -->

- [http](#http)
    - [请求报文](#请求报文)
    - [响应报文](#响应报文)
    - [请求方法](#请求方法)
    - [知识点](#知识点)
    - [http演进](#http演进)
- [https](#https)
    - [加密知识点](#加密知识点)
    - [https概览](#https概览)
    - [https通信全流程](#https通信全流程)
    - [证书的认证和用途](#证书的认证和用途)
    - [https和中间人问题](#https和中间人问题)
    - [一些资料](#一些资料)
- [tcp](#tcp)
    - [报文结构](#报文结构)
    - [tcp连接建立](#tcp连接建立)
    - [tcp连接断开](#tcp连接断开)
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
1. 短链接和长连接(1.0是默认短链接，1.1默认长连接)
2. 流水线技术(基于长连接之后提出pipeline增加并发，但可能出现线头阻塞)
3. tcp复用概念(multiplexing，http2底层支持tcp复用，二进制帧)
4. cookie和session概念
5. 现代浏览器已经由很丰富的存储技术(web storage api & indexedDB )



### http演进
>http1.0
1. 每个请求都是使用一个tcp连接，资源耗费。

>http1.1
1. 默认长连接，使得连接可复用，一次握手挥手之间可以发起多个http请求。
2. 进一步使用pipeline，一次tcp连接里的多个http请求可以并发地进行，而不必串行发生，但是注意接收服务器返回需要按照请求的顺序。(因此某些场景存在线头阻塞问题)
3. cache-control头、host头
4. 断点续传chunked-transfer

>http2
1. http over tcp，tcp复用(multiplexing)，使得1.1中线头阻塞问题得到解决，服务器收到基于一个tcp连接的并发的http请求，处理完毕即可返回，允许乱序。(注意不是彻底地解决线头阻塞，最终还要靠http3)
2. google早年推出spdy，后被IETF标准化有了HTTP2.0.

>http3
1. http over QUIC(quick udp internet connections)
2. 相较于http2+tcp+tls的优势：减少三次握手(1.5rtt)以及TLS握手时间(2rtt)；改进拥塞控制；避免队头阻塞的多路复用...

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

一个简化的https模型：客户端向服务器发起https请求，服务器发送公钥给客户端，客户端生成一个对称加密算法密钥m用于后续和服务器的http加密通信，客户端使用服务器的公钥对m进行加密，服务器使用私钥对报文解密得到对称密钥算法的密钥m，后续双方使用m对http报文进行对称加密解密。(这是一个简化的过程，证书传输以及随机密钥生成都进行了省略)

一些延伸：
1. 对称加密算法的选取
2. 公钥的传输
3. 中间人攻击的应对
4. 证书的概念以及验证

### https通信全流程
1. 客户端发起握手请求，client hello(支持的协议版本、加密算法、压缩算法、随机数A);
2. 服务器回应握手请求，server hello(确认协议版本、加密算法、随机数B、公钥证书即步骤2-3-4);
3. 服务器发送证书(含有自己的公钥);
4. 服务器发送server hello done;
5. 客户端生成随机密码串(pre master secret)，然后发送给服务器，这个过程使用公钥加密；
6. 客户端发送ChangeCiperSpec报文，后续切换加密算法，使用pre master secret以及A和B可以生成master secret；
7. 客户端发送握手结束报文；
8. 服务器发送发送ChangeCiperSpec报文，后续切换加密算法，使用pre master secret(来自5过程，用私钥解密获取)以及A和B可以生成master secret；
9. 服务器发送握手结束报文；
10. 此时ssl连接已经建立，客户端向服务器发送加密后的http报文，解密后使用；
11. 服务器向客户端回应报文一样使用对称加密算法进行加密；
12. 客户端发起断开连接的时候走tcp四次挥手过程。
13. 注意，并非每次https请求都要进行握手协商、证书认证、pms传输的过程，https支持快速恢复，每次连接会有一个session，可以从上一个连接快速恢复。

上述流程可以简化为四次交互(注意这是https中的tls握手部分，在此之前会进行tcp的三次握手)：
1. 客户端握手请求、发送一些信息
2. 服务器回应握手、确认一些信息并发送证书
3. 客户端验证证书并发送利用公钥加密后的第三个随机数，并生成主密钥用于后续对称加密。
4. 服务器给出回应，私钥解密获取随机数，生成主密钥，进行后续对称加密通讯。

### 证书的认证和用途
https通信过程中如何明确服务器身份是我想要通信的服务器呢，即如何避免中间人攻击。公钥通过证书发送，证书由权威CA机构颁发，证书包含证书信息和签名(签名是CA机构利用CA的私钥对证书信息进行摘要计算后加密生成)，客户端能够根据证书签名对证书认证，从而确定证书的有效性，进而确定证书中公钥的有效性，也就明确了服务器端身份。对证书签名的认证需要使用CA机构的公钥，这个公钥在操作系统内置的一些证书中。所以上述过程可以这样描述：客户端收到服务器的证书，从证书中获取服务器端的公钥，为了明确这个公钥可信的，需要对证书进行认证，对证书认证就需要证书发布方的公钥，这个公钥在另一个证书中，这个用于验证证书的证书提前已经内置在计算机中。

证书携带有CA机构说明、持有者信息(比如服务器域名)、公钥等信息，还会有一个签名，签名认证通过即可信任证书中的标识信息以及公钥。

### https和中间人问题
通过证书可以解决中间人问题，在没有证书机制的情况下，中间人可以在ssl建立连接的过程中伪造公钥，在客户端和服务器之间转接信息而不被察觉。由于证书的存在，这种中间人机制就不可行了，首先伪造公钥需要伪造证书，中间人需要使用CA发布的证书(这一点也能做到)，但是客户端拿到证书的时候即使验证该证书时合法的，但是其中的身份标识信息是不正确的(比如域名信息)，因此客户端可以发现这个证书的身份和我要请求的地址或者域名是不一致的。

### 一些资料
1. tls概念 https://tools.ietf.org/html/rfc2246
2.  HTTP Over TLS https://tools.ietf.org/html/rfc2818


## tcp
### 报文结构
1. http报文：请求行/响应行 + 请求头/响应头 + 主体
2. tcp报文：tcp首部+数据(可以是http报文)；tcp报文可以分段重组
3. ip报文：ip首部+数据(可以是tcp报文)

tcp首部固定20字节，16位源端口和16位目的端口，32位的序号和32位的确认号，另外还有数据偏移、保留位、一些标志位(SYN,ACK,FIN等)、窗口值、校验和、紧急指针、填充位等。SYN=1表示同步报文，SYN=1、ACK=0表示这是一个连接请求，对方同意建立连接则返回SYN=1、ACK=1，tcp约定连接建立后发送报文ACK要置1。FIN=1表示发送方数据发送完毕要求断开连接。

SYN和ACK不同于seq和ack，这个ack是首部中的确认号，表示接下来要求对方返回的序列号值，比如在一个已经建立好连接的通信过程中A向B发送数据的过程：

1. A向B发送了seq=x,ack=y(假定数据有效程度为100)
2. B会发送seq=y，ack=x+101(假定数据有效程度为100)
3. 随后A发送seq=x+101,ack=y+101。


### tcp连接建立
三次握手：

A connect B(server)

1. A发送SYN=1/ACK=0/seq=x(SYN_SENT)
2. B收到后返回SYN=1/ACK=1/seq=y/ack=x+1(SYN_RECEIVED)
3. A收到后发送SYN=0/ACK=1/seq=x+1/ack=y+1(ESTABLISHED)
4. B收到后建立连接(ESTABLISHED)

2处B确定A的发送正常以及B的接收正常，3处A确定自己的收发正常以及B的收发正常，4处B确定A的接收和B的发送正常，至此可靠的连接才建立。

注意1和2 SYN=1表示同步报文，不可携带数据但是需要消耗一个序列号；3是一个ACK报文，可以携带数据，不携带数据则不消耗序列号(不消耗序列号则seq依然为x)。

### tcp连接断开
四次挥手(客户端主动断开连接)：

A disconnect from B(Server)
1. A发送FIN=1/ACK=1/seq=x/ack=y (FIN-WAIT-1)
2. B收到后发送FIN=0/ACK=1/seq=y/ack=x+1(CLOSE-WAIT)
3. A收到后进入FIN-WAIT-2状态
4. B向A继续发送需要发送的数据至结束()
5. B向A发送FIN=1/ACK=1/seq=m/ack=x+1(LAST-ACK)
6. A收到后发送FIN=0/ACK=1/seq=x+1/ack=m+1(TIME-WAIT)
7. B收到后即可断开连接(CLOSED)
7. A等待两个MSL时间后断开连接(CLOSED)

A告知B要断开连接，B收到FIN报文后给与回执需要让A直到FIN报文发送成功，但是B端可能还有数据要发送，因此B处于一个等待关闭(CLOSE_WAIT)的状态。A收到回执报文后从FIN-WAIT-1变成FIN-WAIT-2状态，A会等待B这边单向数据传输完毕，B可以继续发送数据，数据传输完毕后向A发送一个FIN报文，请求断开连接并等待回执，处于LAST-ACK状态，A收到B的FIN报文后，明白B的数据传输已经结束并向自己发送断开请求，A给与回执，此时处于TIME-WAIT状态，B收到这最后一个ACK后即从LAST-ACK状态变成CLOSE状态，A回执发送完毕后会等待一段时间，2MSL，如果没有收到B发送来的报文大概率可以认为B收到了最后一个ACK，然后A断开连接即CLOSED状态。

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
9. http相关api使用的时候连接池的概念
10. tls版本协商过程-服务器支持协议版本的配置