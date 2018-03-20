内容付费，数字消费的普及和扩张趋势
消费升级的概念
合规ICO项目募集数字货币的作用，这里的数字货币仅仅是金钱的代替？
#baas，blockchain as a service
目前的开放平台有哪些？除腾讯，ibm外
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
完整客户端(邮件服务器)，轻量级客户端(邮件客户端)，在线客户端(邮件客户端)，后两者以来第三方服务器。
轻量级客户端需要依赖第三方？

比特币交易查询网站
https://blockchain.info/
https://blockexplorer.com/

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