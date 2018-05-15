内容付费，数字消费的普及和扩张趋势
消费升级的概念
合规ICO项目募集数字货币的作用，这里的数字货币仅仅是金钱的代替？
#baas，blockchain as a service
目前的开放平台有哪些？除腾讯，ibm外，(华为白皮书也发布了)
#区块链联盟
国外，R3,Hyperledger
国内，中国区块链研究联盟CBRA，China Hyperledger,金链盟
#tips
区块链技术还处于发展的初期阶段，其中有三项必不可缺的核心技术，分别是：共识机制、密码学原理和分布式数据存储。

Q1：去中心化的特性是否对中心化机构不利？
A1：区块链虽有去中心化的特性，但很多线上业务的纠纷无法离开中心来解决。因此区
块链的真正价值在于促进各行各业的中心化机构之间达成共识，构建联盟，形成多个中心组成
的商业生态圈，这样的生态系统突出了中心的职能，大大简化了中心化机构运营成本。
Q2：区块链是低效服务吗？
A2：区块链服务是低效是一个认识上的误区。区块链的效率瓶颈主要在于共识算法，而
共识算法在不同场景下有不同的实现方式。例如腾讯区块链采用自主研发的高效自适应共识算
法，保证了共识完成即交易确认，并且对交易确认过程中的其他环节，如签名算法、账本存储
方式等进行了优化，实现了秒级确认交易。

#concept
Merkel tree 和 hash list

#bitcoin.org dev reading record
哈希头的计算方式，调增阈值控制难度。
bitcoin core的 off-by-one error。
每个完整节点只保存自己验证过的所有区块。
交易打包到区块的过程。
难以被篡改的数学证明。
markel tree 验证交易的过程 spv过程
节点升级(新的共识规则)的过程，怎么升级
Pubkey scripts and signature scripts combine secp256k1 pubkeys and signatures with conditional logic, creating a programmable authorization mechanism.
p2pkh pay to pupkey hash
p2sh pay to script hash
null data的意义何在？


#精通比特币阅读笔记
纸币的保真技术？验钞机原理？
B-money & hashcash
拜占庭问题的延伸和应用

比特币客户端：
完整客户端(邮件服务器)，轻量级客户端(邮件客户端)，在线客户端(邮件客户端)，后两者依赖第三方服务器。
轻量级客户端需要依赖第三方？

比特币交易查询网站
https://blockchain.info/
https://blockexplorer.com/


理论上可以处理1毫比特币(千分之一)，1聪比特币(亿分之一)，但是比特币参考实现中有dustTransaction，会对最小金额有限制。
白皮书中说是输出至多两个，实际参考实现中可以有更多的输出。

比特币钱包客户端如何快速的构建输入凑集金额？贪心？

比特币的两个时间点
10分钟和2weeks
每1~2016blocks计算一次，时间间隔理想值为1209600s即2周，平均每10分钟一个block

比特币单位和数量
千分之1-毫比特币  亿分之一-1聪比特币 
1聪到21，000，000比特币

轻量级钱包客户端，只保存自己的未消费输出，信息获取依赖服务器

挖矿奖励，随时间减少，隔一段时间减半

一般区块建立六次以上，基本不可逆转

什么叫做交易的确认

编译比特币核心，通过命令行使用比特币核心提供的json-rpc api接口
比特币代码 https://github.com/bitcoin/bitcoin
比特币客户端：中本聪客户端(satoshi-client) 一个参考实现bitcoind
多种语言实现：bitcoinj-java;btcd-go;
查询实际交易来看交易结构和区块链组织形式
btcd-go不提供钱包功能(交易功能)，但可与其他应用结合，且支持httppost（bitcoind）和websocket两种通信方式

一个比喻
比特币的挖矿-难度可以动态调整的巨大维度的数独游戏(难解，但是易于验证)

？？？计算区块难题的时候需要MerkelRoot，这个又是和每笔交易内容相关的，交易又是不断发生的，如何取舍？？？
？？？？？transaction id 和 block id的生成？
私钥->公钥->地址
比特币私钥空间2^256 ≈10^77 可见宇宙约含有原子数目10^88 ?
比特币公钥的计算，椭圆曲线相关。secp256k1。椭圆曲线的加法和乘法运算。
OpenSSL加密库。
比特币地址：(p2pkh) A = RIPEMD160(SHA256(public key)),一般会再进行base58check编码。
base58 是base64字符中除去0,大写字母O,小写字母l,大写字母I,以及+，/这六个字符。 64-6 = 58.  64是64个可见字符。
看看base64和base58的设计思路。
base58check 是base58再加校验构成。
图示流程：私钥-公钥-公钥哈希(一种比特币地址)-添加类型前缀和双哈希校验位-base58check格式的比特币地址。(章节4.2)
base58check在比特币的应用中对不同类型数据设置不同前缀：比特币地址0x00（1），p2sh地址0x05(3),测试网络地址0x6f(m or n),private key wif 0x80(5,K,L),BIP38和BIP32有对加密私钥和扩展公钥类型的定义。
私钥的格式：
    . 普通256bit，64位16进制字符串,256/4=64；1E99423A4ED27608A15A2616A2B0E9E52CED330AC530EDCC32C8FFC6A526AEDD
    . WIF,base58check格式，8bit带有128的标识(0x6f)和32bit校验位(标志+数据进行double sha256截取前32bit)，所以一共8+256+32=296bit base58->296/5.85798=50.5293（这里的5.8为log58） = 51位字符，5J3mBbAH58CpQ3Y5RNJpUKPE62SQ5tfcvU2JpbnkeyhfsYB1Jcn。
    .WIF­compressed,KxFC1jmwwCoACiCAWZ3eXa96mBM6tb3TYzGmf6YwgdGWZgawvrtJ，52位字符
    私钥不能压缩也不被压缩存储，wif-compressed是指私钥对应生成的公钥是压缩格式的，该私钥以base58check格式存储。
    其原型hex-compressed则为私钥原始字节流后添加0x01标志位进行base58check得到。
公钥的格式：
    .每个公钥对应椭圆曲线上一个点的坐标，为标志+x+y构成，8+256+256=520bit -> 130位十六机制字符，标志说明0x04表示非压缩，0x02、0x03为压缩格式。另一方面可以只保存标志+x,然后通过公式计算y,这里的y会有正负之分对应y为奇数偶数,分别为0x03和0x02。标志+x共8+256=264位，66位十六进制字符。
比特币中使用的是ecc椭圆曲线加密算法，使用secp256k1这条曲线。
bitcoin地址:1424C2F4bC9JidNjjTUZCbUxv6Sa1Mt62x
decoderToHex:00211B74CA4686F81EFDA5641767FC84EF16DAFE0B388C8D1D
-> 共8+160+32 = 200bit 50位十六进制字符串
    00;标准形式比特币地址8bit
    211B74CA4686F81EFDA5641767FC84EF16DAFE0B;160bit哈希值
    388C8D1D;check值32bit
    base58check->200/5.8579=34.14,对应字符尾数在34、35左右。实际是34。
   
   1J7mdg5WxGENmwyJP9xuGhG5KRzu99BBCX = 34位base58
   1J7mdgYqyNd4ya3UEcq31Q7sqRMXw2XZ6n = 34位base58
一堆地址示例：
私钥5J3mBbAH58CpQ3Y5RNJpUKPE62SQ5tfcvU2JpbnkeyhfsYB1Jcn
地址1424C2F4bC9JidNjjTUZCbUxv6Sa1Mt62x

256bitECDSA私钥18E14A7B6A307F426A94F8114701E7C8E774E7F9A47E2C2035DB29A206321725

公钥加密和数字指纹概念
比较RSA、ecc(椭圆曲线)、素数幂

信用卡交易包含敏感信息，而且要在加密网络中传输，比特币交易可以在任意网络环境下传播。
有效交易几秒内指数级扩散

区块链记录着所有交易，每个人的"账户"实际就是区块链中所有的utxo组合（未经使用的交易输出）。区块链中并没有账户的物理概念。钱包客户端可能会对用户的utxo建立一个索引。每此交易其实就是utxo被消耗和新生成的过程。交易通过使用所有者的签名来解锁utxo，通过新的使用者的比特币地址创建utxo。

比特币应该是现有输出，再有的输入。创世区块的coinbase交易，没有输入，产生第一次输出。

交易的格式：版本，输入数目，输入[]，输出数目，输出[],时间戳。
输出：总量，锁定脚本长度，锁定脚本。
输入：包含改utxo输出的交易哈希值(23字节)，输出索引，解锁脚本尺寸，解锁脚本，序列号(可以先不研究)。

8.3交易的独立校验
比特币多个零碎的utxo组成输入，贪心算法的一个应用
coinbase交易
coinbase和创世区genesis block？第一个聪的诞生？

2009年1月3日，比特币的创始人中本聪在创世区块里留下一句永不可修改的话：
“The Times 03/Jan/2009 Chancellor on brink of second bailout for banks（2009年1月3日，财政大臣正处于实施第二轮银行紧急援助的边缘）。”当时正是英国的财政大臣达林被迫考虑第二次出手纾解银行危机的时刻，这句话是泰晤士报当天的头版文章标题。区块链的时间戳服务和存在证明，第一个区块链产生的时间和当时正发生的事件被永久性的保留了下来。

鼓励交易数量上限，防止恶意攻击。MAX_ORPHAN_TRANSACTIONS。

比特币交易数据存储采用什么组织形式？db层面


解锁脚本和锁定脚本，最常见的，锁定脚本即一个公钥脚本，用于说明什么样的条件才能够使用此输出；解锁脚本为交易输入中的一个字段，可以和锁定脚本结合来验证确实可以使用这个utxo，比如sign+pubkey。最简单的形式为p2pk,锁定脚本pubkey op_checksign，一个可用的解锁脚本为signature(from private key)。 

OP_RETURN的引入，避免非支付UTXO集的膨胀。
op_return的输出其值一般为0，因为它不能被消费，所以一般不会给>0的数值，提出此概念就是为了构造一种带有额外数据的输出，但不录入utxo数据集，不可被消费。
对于signature的生成类型有多种，也就是有多个hashtype，具体在bitcoin reference中有说明。
p2pk;p2pkh;multisign;p2sh(redeem script, 将负责脚本存储负担转移到输入方，而非输出方,输出方会存储于utxo集，影响内存)

P2P技术，bitcoin，bitTorrent,Napster.
扩展的比特币网络(extended bitcoin network):比特币P2P协议，矿池挖矿协议，Stratum协议以及其他连接比特币系统组件相关协议的整体网络结构。

SPV节点，简易支付验证节点。只下载区块头而不包含区块里的交易信息，大小是完整区块链存储的千分之一。
Bloom过滤器的引入来解决spv验证方式可能带来的隐私泄露问题。

交易池，孤立交易池，这两个在内存，每次启动初始为空。
utxo池，这个在存储器保存。？？

区块链存储，比特币核心客户端中使用google的levelDB数据库存储元数据。
区块结构，4字节区块大小(单位字节)，80字节区块头，1~9可变整数个字节表示交易数量,若干个交易信息。
平均(注意是平均)每个区块至少包含超过500个交易，每个交易又至少250字节，所以一个包含所有交易的完整区块比区块头的1000倍还要大。

区块头哈希值(区块哈希值实际是指区块头哈希值)，对区块头进行两次sha256得到256bit即32字节长度地址
高度，区块高度，创世区块高度为0。高度可能重复，但是区块哈希值不会重复，两者都不在区块信息中保存，而是结点计算后保存在各自索引数据库中。

创世区块被写入比特币客户端软件中
https://github.com/bitcoin/bitcoin/blob/3955c3940eff83518c186facfec6f50545b5aab5/src/chainparams.cpp#L123

查看区块信息
https://blockchain.info/block/000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f
block#0 只有一笔交易
创世区块的留言查看，二进制内容转换成字符，隐藏留言。

Merkle树快速归纳和校验数据完整性
merkle树和简单支付验证7.8 流程没有明白。从全节点获取的数据是什么？这里验证什么，验证交易？验证区块？从别的节点获取merkle区块消息真实度？不是直接获取结果而是获取root和路径，然后自己来验证。

挖矿奖励包含两部分：创建新区块的奖励和区块内所有交易的交易费。其中创建新区块的奖励大约每四年减少一半。具体计算是每210，000块后减半。
210000/(2016/14)/365 ≈ 3.9954 年
实际运行情况：2009年1月每个区块50，2012年11月开始每个区块25，直到2016年某个时刻变为12.5，最终估计在2140年所有比特币20，999，999，980全部发行完毕。

主链100，000高度区块有四个交易，很多区块，尤其是靠前的区块内部是没有交易的，只有一个区块奖励，当然有交易不意味这有交易费，交易费为0也是可以被确认的。

创世区块	
https://blockchain.info/block-height/0                                    2009-01-03 18:15:05
https://blockchain.info/block-height/209999 209，999高度block reward为50   2012-11-28 15:01
https://blockchain.info/block-height/210000 210，000高度block reward为25   2012-11-28 15:24
https://blockchain.info/block-height/419999                          25   2016-07-09 16:41:53
https://blockchain.info/block-height/420000                          12.5 2016-07-09 16:46:13
目前12.5奖励，估计在2020年上半年减为6.25

计算比特币总量的脚本
关于比特币发行递减这种模型导致的货币通货紧缩可能带来的影响进行讨论。

8.5整合交易至区块 的例子，区块挖取的竞赛和交易池内交易以及utxo的处理。
各个节点独自验证交易，并构建自己的区块，先根据一定原则对交易进行打包，然后开始解题，解题后将消息告知其他节点，此消息得到迅速传播，如果解题过程收到通知，新区块产生，则进行验证，验证合理，放弃解题，生成新区块，并将池中的交易和新区块进行比对，已经被放入区块的交易舍弃掉，其余的可以继续等待被打包，更新这些uxto的块龄，开始进行下一个区块的竞赛过程，这期间的新交易有可能被纳入到再一次的区块竞赛中，根据区块大小，交易长度，金额手续费等等进行组装和选取交易。

交易结构中的交易输出索引，uxto的索引？

候选区块的创建，目标问题的计算，计算过程中有新的交易到来怎么处理？[重读的时候自己有所发现，前面总结了]

8.7.3标题上方的一个计算，没看明白，

单区块分叉每周都会出现，但是双区块分叉已经很少发生。
10分钟的设定也是在交易确认快捷和分叉发生率之间做出的一个折中。
？？难度动态调整，谁来调整，节点自动计算？？(比特币客户端自动计算，简单地理解每2016个块则去比较这段时间和20160分钟，这是一个粗略的比较，20160分钟实际是两周的含义，调整幅度因子小于4，代码定义如此，避免剧烈波动)
难度字段的表示以及计算，难度调节因子的计算。
解题实际就是对区块头进行的哈希，解出来的满足难度条件的数值就是最终的区块哈希。

收到新区块-验证法则
每一笔交易-验证法则

比特币算力的增长和摩尔定律的限制。

随机值拉升方案没有看明白？(应该就是nonce和coinbase中的data中字段一起用于工作量证明)
100PH/s 100peta hashed per second   peta = 10^15
矿池的运作原理。聚集众多旷工的力量，矿池服务器直接连接全节点或者访问块链数据库副本，代替池内旷工进行区块和交易验证，矿池内的旷工减轻负担(15~20G存储 2G内存)，采矿协议STM，GBT，GWK(2012年底过时，不支持高于4GH/s时的采矿)。
传统矿池服务器进行协调，但是管理人有作弊的可能。
去中心化的矿池：P2Pool,将采矿记录在份额链上，利用区块链的思想进行去中心化采矿。

8.12共识攻击两个例子，很好。
51%攻击只需要控制30%的算力即可发动，并非要求必须超过51%，之所以命名如此，是因为超过51%发动攻击基本都能成功。
大宗交易要等待6个交易确认，降低共识攻击的概率。
共识攻击两个影响：双重支付或者拒绝服务。双重支付只能是对攻击者自身钱包试试。

OP_RETURN操作符

竞争币和竞争链 alt coin and alt chain
基于工作量证明的一致性协议，基于资源证明或发布证明的协议
工作量证明：(sha256，scrypt算法)
权益证明：
有的基于工作量证明中的一种或多种算法，有的给予权益证明，有的两者皆涉及。
多目的挖矿创新，避免工作量证明“浪费资源”的观点。
致力于匿名性的竞争币。
非货币型竞争区块链：域名币(比特币代码的首个克隆产物);bitmessage(无服务器的加密邮件系统)；以太坊(不同于比特币的独立实现，图灵完备)
区块链和共识系统的发明，降低大型系统在组织和协调上的花销，同时消除权力攫取、腐败及管制俘获的可乘之机。

比特币安全：纸钱包，冷存储机制，硬件钱包，分散钱包，多重签名，遗产处理。


mastering bitcoin
>>>>>>>>>>>

>>>>>>>>>>>>
#programming blockchain by using c# 
You can acquire testnet coins quickly by using faucets, just google "get testnet bitcoins".

what better way is there to understand something, but coding it?
Proof of Stake and Proof of Work are the best expression of affection, everything else is Fiat.
Complementary Reading
Here is some literature that you can use to complete this book:
Mastering Bitcoin of Andreas M. Antonopoulos
The Bitcoin Developer’s Reference Guide
Nicolas Dorier’s articles on CodeProject
nopara73's articles on CodeProject

private key and pubic key and bitcoin address
private key -> public key
public key -> publick key hash
public key + network -> address
address -> ScriptPubKey（in fact scriptpubkey has no network info which means pubkey hash-> ScriptPubKey）

Bitcoin Addresses are composed of a version byte which identifies the network where to use the address and the hash of a public key
A public key hash is generated by using a SHA256 hash on the public key, then a RIPEMD160 hash on the result, using Big Endian notation. The function could look like this: RIPEMD160(SHA256(pubkey))

Base58Check : base64进一步，添加校验位以及区分歧义相似字符。
The Base58Check encoding has some neat features, such as checksums to prevent typos and a lack of ambiguous characters such as '0' and 'O'.

scriptpubkey
It is a short script that explains what conditions must be met to claim ownership of bitcoins. 
address->ScriptPubKey
ScriptPubKey appears to have nothing to do with the Bitcoin Address, but it does show the hash of the public key.
A ScriptPubKey does not necessarily contain the hashed public key(s) permitted to spend the bitcoin
It is also possible to retrieve the hash from the ScriptPubKey and generate a Bitcoin Address from it.

The Transaction ID is defined by SHA256(SHA256(txbytes))

这句话不明白？
The consensus rules enforce that the sum of output's value in the coinbase transaction does not exceed the sum of transaction fees in the block plus the mining reward.

give me a child until he is seven and i will give you a man

priate key + network ->Bitcoin Secret（wallet import format）
bitcoin secret 可以得到private， 但是bitcoin adress 不能得到 public key 因为它里面只有public key hash

PGP(Pretty Good Privacy)，是一个基于RSA公钥加密体系的邮件加密软件。

Proof of ownership as an authentication method章节中message和signature的验证，message什么用，用在哪里，signature如何生成（利用private key对message处理生成？），signature和scriptsign？

spend your coin 介绍了scriptsign使用库进行计算的方法，但没有介绍原理。

关于密钥的生成和伪随机数
KDF的使用，密钥的加密存储，尽量提高暴力破解成本，多密钥或多地址的必要性。
关于密钥安全性，一些加密方案
--BIP38 part1&part2:
对private key利用password再次进行加密，进一步升级，将PassphraseCode交给第三方，实现非可信第三方依然能够使用PassphraseCode来生成加密后的密钥，a third party key generator + passphrasecode -> encrypted key

--BIP32（HD Wallet）
problems we want to solve:1 Prevent outdated backups & 2 Delegating key / address generation to an untrusted peer
A “Deterministic” wallet would fix our backup problem. With such a wallet, you would have to save only the seed. From this seed, you can generate the same series of private keys over and over.

注意生成的子key有hardened-keys 和 non-hardened-keys两种类型，区别是前者能够复原主key。

masterkey->childkey,也可以是层次树状的，用于部分组织架构。
不再需要备份所有钱包地址，一个主key可以根据标识去生成地址，但是这里需要记录那些地址标识。
另外利用masterPubKey来实现第三方地址管理，根据masterpubkey来生成具体的地址。

上述两种方法，将生成过程交给第三方，但是客户端是否还要验证？既然要验证且能验证，那为何不在本地生成？
！！！！对具体使用的算法原理进行分析

--Mnemonic Code for HD Keys (BIP39)
便于记忆，目前支持若干种语言。

这里key的位数是多少，key，masterkey，钱包地址等？？？

xprv9tvBA4Kt8UTuEW9Fiuy1PXPWWGch1cyzd1HSAz6oQ1gcirnBrDxLt8qsis6vpNwmSVtLZXWgHbqff9rVeAErb2swwzky82462r6bWZAW6Ty

L1tZPQt7HHj5V49YtYAMSbAmwN9zRjajgXQt9gGtXhNZbcwbZk2r
41e0d7ab8af1ba5452b824116a31357dc931cf28
04678afdb0fe5548271967f1a67130b7105cd6a828e03909a67962e0ea1f61deb649f6bc3f4cef38c4f35504e51ec112de5c384df7ba0b8d578a4c702b6bf11d5f

Current bitcoin rules allows only one OP_RETURN of 80 bytes per transaction

p2pk & p2pkh
public key hash -> 160bit ->40位16进制字符
scriptpubkey -> p2pk or p2pkh,P2WSH
p2pkh较前者好处，1 避免直接暴露公钥，以后会有风险，ECC算法被量子计算机破解，通过public key得到private key，2 缩短长度，便于生成二维码等。
但是量子计算机对hash的逆运算能做到么？
软分叉和硬分叉？
Segwit = segregated witness
the semantics of P2WPKH is the same as the semantics of P2PKH, except that the signature is not placed at the same location as before.
P2WSH:pay to witness script hash
MultiSig:
Nowadays, native Pay To Multi Sig (as you have seen above) and native P2PK are never used directly. Instead they are wrapped into something called a Pay To Script Hash payment.

P2SH，P2WSH(P2WSH (Pay to Witness Script Hash))
This P2SH scriptPubKey represents the hash of the multi-sig script: redeemScript.Hash.ScriptPubKey.
As with P2PKH/P2WPKH, the only difference between P2SH and P2WSH is about the location of what was previously in the scriptSig, and the scriptPubKey being modified.

P2W* over P2SH没有看明白，钱包如何兼容？
The Bitcoin script language is a stack based language so everyone having done some assembler should be able to read it.


script language http://davidederosa.com/basic-blockchain-programming/bitcoin-script-language-part-one/

P2SH，P2WSH

NBitcoin 提供TransactionBuilder封装交易，对不同的ownership类型进行统一处理。
ownership and proof of ownership
colored coin    600santosi

##segwit-benefits
https://bitcoincore.org/en/2016/01/26/segwit-benefits/


NBitcoin tests:unit test 
资产的发布和转移
Open Assets and Ricardian contracts（没看明白）
RicardianContract and IssuerScript
 

 一个例子，colored coin的一个选举应用-Liquid Democracy，可以使用网络通知投票形式，也可以利用RucardianContract合约形式。

 ##visual studio code教程

##
公有链，联盟链，私有链
供应链金融，寻人，法务凭证(慧狮)，游戏，baas，trustSQL

##
天使与魔鬼，达芬奇密码
人工智能，区块链，量子计算
Atom2Vec
零知识证明

proof of burn 没看明白，burn一些bitcoin，作为reputation，就不能欺诈了？这些bitcoin永远不能被消费。

1. 比特币中有智能合约的概念吗？
2. 比特币不是图灵完备的
3. CD-ROM 基于硬件技术的一次性写入，不可篡改，比特币通过密码学的散列算法来实现难以篡改
4. 分布式和去中心化，两个概念。传统分布式存储系统和比特币中的分布式账本概念不同。
5. 拜占庭问题的数学证明？在区块链中的应用。
6. 共识算法(一致性)。可信节点，不可信节点。Paxos，Raft；PoW, PoS，PBFT。
7. 智能合约高级语言,Solidity、Serpent、LLL。系统应用，以太坊，hyperledger。
8. 涉及密码学技术：哈希算法、对称加密、非对称加密、数字签名、数字证书、同态加密、零知识证明等
9. 完整，机密，身份认证。tls+证书，避免中间人攻击。
10. 国密SM2/3/4
11. 比特币每秒7笔，以太坊每秒15笔，中心化visa系统峰值56000，支付宝2017双十一256000。
12. DAPP, 闪电网络， 雷电网络。
13. docker，Kubernetes，kafa/zookeper,Gossip协议，ecs(elastic computer service)

#以太坊
https://ethfans.org/wikis/%E4%BB%A5%E5%A4%AA%E5%9D%8A%E7%99%BD%E7%9A%AE%E4%B9%A6


#bitcoin reference guide pdf
double sha256的应用？工作量证明是用的这个？
工作量证明的难度系数和难度值的转换关系证明？
block彼此的链接方式？物理实现是什么样的？
nonce字段只有4字节，对于计算目标值很快就能遍历完，解决方案？加入coinbase相关字段？
block和transaction的hash定位的实现？
一个参考实现默认值546satoshi，out的value低于此值被认为dust transaction
input和output的locktime，以及input的sequenceNumber的作用？
coinbase交易中的的coinbase字段-block-height-size + block-height + arbitrary data（arbitrary data can be used for prove of work）
final transaction rule, 和locktime以及sequence num有关，一个废弃的transaction replacement机制，已经不被视为standard
普通交易和coinbase交易的完整性保障，前者公钥私钥，后者利用工作量证明相关？不应该都跟工作量证明相关一致吗，所有交易都会影响到merkelroot进而影响pow？
script 可以创造很复杂的锁定条件，但是比特币的参考实现中只使用了一些脚本模板，P2PK,P2PKH,P2SH,MultiSign,Nulldata.（See https://en.bitcoin.it/wiki/Script for complete reference.）
off-by-one error?
null data transaction 不遵循dust transaction rule，他的output value可以为0，为了避免blockchain flooding，每个交易里只能有一个输出是这种类型。
sha256&ripemd160&ecdsa
两种地址的计算P2PH&P2SH,以及base58编码
signature的计算：scriptsign的计算，涉及几种hashtype，分别对应交易字段参与的不同程度。
算出谜题后，广播的时候，广播哪些内容，其他节点如何知道这个block里有哪些交易，或者这些交易是否已经在自身节点中收到过？
dustTransaction 公式中的146是什么意思？

#重读白皮书
1. 另种电子货币体系(信息交换体系):基于信任和基于加密
2. 收到交易的几点证明：确认交易存在，证明接收者身份(可以使用这个交易的输出)，以及交易没有双重支付。
3. 工作量证明的难度动态调整，保证区块行程时间在某一个固定值附近，使得货币稳定的产出，使得打包时间固定在10分钟左右，不至于太快产生太多分歧（fork）。
4. 传统的隐私来自第三方信用中介。加密货币中交易会广播，一些可选策略，布隆过滤，公钥哈希或者多交易地址，可以进行一定程度地改进。
5. 即使控制算力，攻击者所做的只能是诋毁自己的交易，双重支付，而不能凭空造币或者抢币。
6. 论文中没有提拜占庭问题，只是说诚实算力高于攻击者算力。关于拜占庭问题BFT个改进BFT算法的0.5和0.667概率问题进行研究。

summary:论文是对这种基于工作量证明的分布式加密货币方案的原型设计，没有涉及到一些实现细节，或者说具体实现方式选用算法是可变的，这也符合论文定位，可以从参考实现中去学习这些细节。文章主要提出了交易加密策略来明确交易者身份(pubkey privatekey signature)，基于工作量的证明来避免double spending问题，并对攻击者篡改交易的可能性进行了数学分析。为了避免双花问题，需要交易公开，因此涉及到数据和身份隐私性的改进。为了鼓励节点参与，提出了激励机制(incentive)。避免数据膨胀，使用梅克尔树(Merkel Tree)来进行交易的存储和校验。MerkelTree可以使得简易支付验证(SPV)成为可能。

论文中涉及到的数学问题和概念：分布式一致性算法，共识机制，pow，哈希现金，梅克尔树(Merkel Tree)，赌徒破产问题Gambler's Ruin Problem，泊松分布。

#比特币参考实现学习
