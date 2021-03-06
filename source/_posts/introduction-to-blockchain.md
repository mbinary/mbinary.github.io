---
title: 『区块链』简介
date: 2018-07-23  22:00
categories: 区块链
tags: [区块链,数字货币]
keywords:  
mathjax: false
description: 
---

以下内容摘录自 <<区块链原理,设计与应用>>
<!-- more -->


<!-- TOC -->

- [1. 分布式记账](#1-分布式记账)
    - [1.1. 原理](#11-原理)
    - [1.2. 重要性](#12-重要性)
- [2. 区块链的特点](#2-区块链的特点)
- [3. 区块链的定义](#3-区块链的定义)
- [4. 区块链的原理](#4-区块链的原理)
    - [4.1. 例如比特币](#41-例如比特币)
- [5. 区块链的演化](#5-区块链的演化)
- [6. 关键问题与挑战](#6-关键问题与挑战)
    - [6.1. 抗抵赖与隐私保护](#61-抗抵赖与隐私保护)
    - [6.2. 分布式共识](#62-分布式共识)
    - [6.3. 扩展性](#63-扩展性)
    - [6.4. 数据库和存储系统](#64-数据库和存储系统)
    - [6.5. 认识上的误区](#65-认识上的误区)
- [7. 应用场景](#7-应用场景)
    - [7.1. 金融服务](#71-金融服务)
    - [7.2. 征信管理](#72-征信管理)
    - [7.3. 权属管理](#73-权属管理)
    - [7.4. 资源共享](#74-资源共享)
    - [7.5. 贸易管理](#75-贸易管理)
    - [7.6. 物联网](#76-物联网)
- [8. 分布式系统](#8-分布式系统)
    - [8.1. 一致性问题(Consistency)](#81-一致性问题consistency)
        - [8.1.1. 理想一致性](#811-理想一致性)
        - [8.1.2. 带约束的一致性](#812-带约束的一致性)
    - [8.2. 共识算法](#82-共识算法)
        - [8.2.1. Paxos & Raft](#821-paxos--raft)
        - [8.2.2. FLP不可能原理](#822-flp不可能原理)
    - [8.3. CAP原理](#83-cap原理)
    - [8.4. ACID 原则](#84-acid-原则)
- [9. 密码学技术](#9-密码学技术)
    - [9.1. hash算法](#91-hash算法)
    - [9.2. 加解密算法](#92-加解密算法)
        - [9.2.1. 对称](#921-对称)
        - [9.2.2. 非对称加密](#922-非对称加密)
        - [9.2.3. 混合加密机制](#923-混合加密机制)
    - [9.3. 数字签名](#93-数字签名)
    - [9.4. 数字证书](#94-数字证书)
    - [9.5. Merkle 树](#95-merkle-树)
        - [9.5.1. 定义](#951-定义)
        - [9.5.2. 特点](#952-特点)
        - [9.5.3. 应用场景](#953-应用场景)
    - [9.6. 同态加密](#96-同态加密)
- [10. 比特币](#10-比特币)
    - [10.1. 从实体货币到数字货币](#101-从实体货币到数字货币)
    - [10.2. 去中心化实现数字货币的难题](#102-去中心化实现数字货币的难题)
    - [10.3. 原理与设计](#103-原理与设计)
        - [10.3.1. 基本交易过程](#1031-基本交易过程)
        - [10.3.2. 概念](#1032-概念)
    - [10.4. 闪电网络](#104-闪电网络)
    - [10.5. 侧链](#105-侧链)
- [11. 以太坊](#11-以太坊)
    - [11.1. 特点](#111-特点)
    - [11.2. 概念](#112-概念)
    - [11.3. 主要设计](#113-主要设计)
- [12. 超级账本-Hyperldger](#12-超级账本-hyperldger)

<!-- /TOC -->


<a id="markdown-1-分布式记账" name="1-分布式记账"></a>
# 1. 分布式记账
<a id="markdown-11-原理" name="11-原理"></a>
## 1.1. 原理
商业活动参与者首先要寻找一个多方均信任的第三方来记账, 确保交易的准确.

可以很容易设计出一个简单粗暴的分布式记账结构，如下图。多方均允许对账本进行任意读写，一旦发生新的交易即追加到账本上。这种情况下，如果参与多方均诚实可靠，则该方案可以正常工作；但是一旦有参与方恶意篡改已发生过的记录，则无法确保账本记录的正确性。
![introduction-to-blockchain-1](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/introduction-to-blockchain-1.png)

为防止恶意篡改, 可以引入验证机制. 使用`数字摘要技术(digital digest)`. 每当有新交易记录被追加到账本上, 记录前面交易历史的 hash 值, 此后每个时刻, 参与者都可以重新计算 hash, 看是否与记录的 hash 匹配. 不匹配说明修改过, 也可以容易地定位修改的交易记录了


不必要每次都计算前面所有历史的 hash, 可以计算 上次的 hash 加上当前交易 的 内容的 hash 
![introduction-to-blockchain-2](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/introduction-to-blockchain-2.png)

这正是一个区块链结构.

<a id="markdown-12-重要性" name="12-重要性"></a>
## 1.2. 重要性
分布式记账问题为何重要？可以类比互联网出现后对社会带来的重大影响。
互联网是人类历史上最大的分布式互联系统。作为信息社会的基础设施，它很好地解决了传递信息的问题。然而，由于早期设计上的缺陷，互联网无法确保所传递信息的可靠性，这大大制约了人们利用互联网进行大规模协作的能力。而以区块链为基础的分布式账本科技则可能解决传递可信信息的问题。这意味着基于分布式账本科技的未来商业网络，将成为新一代的文明基础设施——大规模的协作网络。

分布式账本科技的核心价值在于为未来多方协同网络提供可信基础。区块链引发的记账科技的演进，将促使商业协作和组织形态发生变革。

<a id="markdown-2-区块链的特点" name="2-区块链的特点"></a>
# 2. 区块链的特点
* 分布式容错性：分布式网络极其 robust , 能够容忍部分节点的异常状态；
* 不可篡改性：一致提交后的数据会一直存在，不可被销毁或修改；
* 隐私保护性：密码学保证了数据隐私，即便数据泄露，也无法解析。


可能带来的业务特性
* 可信任性：区块链技术可以提供天然可信的分布式账本平台，不需要额外第三方中介机构参与；
* 降低成本：跟传统技术相比，区块链技术可能带来更短的时间、更少的人力，降低维护成本；
* 增强安全：区块链技术将有利于安全、可靠的审计管理和账目清算，减少犯罪风险。


<a id="markdown-3-区块链的定义" name="3-区块链的定义"></a>
# 3. 区块链的定义
狭义上，区块链是一种以区块为基本单位的链式数据结构，区块中利用数字摘要对之前的交易历史进行校验，适合分布式记账场景下防篡改和可扩展性的需求。
广义上，区块链还指代基于区块链结构实现的分布式记账技术，还包括分布式共识、隐私与安全保护、点对点通信技术、网络协议、智能合约等。

<a id="markdown-4-区块链的原理" name="4-区块链的原理"></a>
# 4. 区块链的原理
* 交易(transaction): 一次对账本的操作,导致账本状态的一次改变.
* 区块(block): 记录一段时间内发生的所有交易和状态结果.,是对当前账本状态的一次共识
* 链(chain): 由区块按照发生顺序串联而成,是整个版本状态变化的日志记录


在实现上, 首先假设存在一个分布式的数据记录账本,只允许添加,不允许删除.

<a id="markdown-41-例如比特币" name="41-例如比特币"></a>
## 4.1. 例如比特币
首先，比特币客户端发起一项交易，广播到比特币网络中并等待确认。网络中的节点会将一些收到的等待确认的交易记录打包在一起（此外还要包括前一个区块头部的哈希值等信息），组成一个候选区块。然后，试图找到一个	nonce	串（随机串）放到区块里，使得候选区块的哈希结果满足一定条件（比如小于某个值）。这个nonce	串的查找需要一定的时间进行计算尝试。
一旦节点算出来满足条件的	nonce	串，这个区块在格式上就被认为是“合法”了，就可以尝试在网络中将它广播出去。其它节点收到候选区块，进行验证，发现确实符合约定条件了，就承认这个区块是一个合法的新区块，并添加到自己维护的区块链上。当大部分节点都将区块添加到自己维护的区块链结构上时，该区块被网络接受，区块中所包括的交易也就得到确认。

这种基于算力寻找 nonce 串的共识机制成为 PoW(Proof of Work). (还有很多其他共识机制 PoX, 如 PoS (stake)...) 

<a id="markdown-5-区块链的演化" name="5-区块链的演化"></a>
# 5. 区块链的演化
比特币区块链支持简单的脚本计算, 仅限于数字画笔相关的处理. 还可以将区块链上执行的处理过程进一步泛化,即提供 智能合约 Smart Contract. 由此提供除货币交易功能外更灵活的合约共功能,执行更为复杂的操作.

![introduction-to-blockchain-3](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/introduction-to-blockchain-3.png)

<a id="markdown-6-关键问题与挑战" name="6-关键问题与挑战"></a>
# 6. 关键问题与挑战
<a id="markdown-61-抗抵赖与隐私保护" name="61-抗抵赖与隐私保护"></a>
## 6.1. 抗抵赖与隐私保护
* 怎么防止交易记录被篡改
* 怎么证明交易双方的身份
* 怎么保护交易双方的隐私

<a id="markdown-62-分布式共识" name="62-分布式共识"></a>
## 6.2. 分布式共识
指标: 容错的结点比例, 决策收敛速度, 出错后的恢复,动态特性等.
<a id="markdown-63-扩展性" name="63-扩展性"></a>
## 6.3. 扩展性
不能简单得增加结点来扩展整个系统的处理能力.

对于比特币和以太坊区块链而言，网络中每个参与维护的核心节点都要保持一份完整的存储，并且进行智能合约的处理。此时，整个网络的总存储和计算能力，取决于单个节点的能力。甚至当网络中节点数过多时，可能会因为一致性的达成过程延迟降低整个网络的性能。尤其在公有网络中，由于大量低性能处理节点的存在，问题将更加明显。

要解决这个问题，根本上是放松对每个节点都必须参与完整处理的限制（当然，网络中节点要能合作完成完整的处理），这个思路已经在超级账本中得到应用；同时尽量减少核心层的处理工作。

在联盟链模式下，还可以专门采用高性能的节点作为核心节点，用相对较弱的节点仅作为代理访问节点。

<a id="markdown-64-数据库和存储系统" name="64-数据库和存储系统"></a>
## 6.4. 数据库和存储系统
区块链网络中的大量信息需要写到文件和数据库中进行存储。

预测将可能出现更具针对性的“块数据库（BlockDB）”，专门服务类似区块链这样的新型数据业务，其中每条记录将包括一个完整的区块信息，并天然地跟历史信息进行关联，一旦写入确认则无法修改。所有操作的最小单位将是一个块。为了实现这种结构，需要原生支持高效的签名和加解密处理。

<a id="markdown-65-认识上的误区" name="65-认识上的误区"></a>
## 6.5. 认识上的误区
区块链不等于数据库。虽然区块链也可以用来存储数据，但它要解决的核心问题是多方的互信问题。单纯从存储数据角度，它的效率可能不高，不建议把大量的原始数据放到区块链系统上。

<a id="markdown-7-应用场景" name="7-应用场景"></a>
# 7. 应用场景
<a id="markdown-71-金融服务" name="71-金融服务"></a>
## 7.1. 金融服务
区块链带来的潜在优势包括降低交易成本、减少跨组织交易风险等。

<a id="markdown-72-征信管理" name="72-征信管理"></a>
## 7.2. 征信管理
区块链平台将可能提供前所未有规模的相关性极高的数据，这些数据可以在时空中准确定位，并严格关联到用户。因此，基于区块链提供数据进行征信管理，将大大提高信用评估的准确率，同时降低评估成本

另外，跟传统依靠人工的审核过程不同，区块链中交易处理完全遵循约定自动化执行。基于区块链的信用机制将天然具备稳定性和中立性。

<a id="markdown-73-权属管理" name="73-权属管理"></a>
## 7.3. 权属管理
区块链技术可以用于产权、版权等所有权的管理和追踪。其中包括汽车、房屋、艺术品等各种贵重物品的交易等，也包括数字出版物，以及可以标记的数字资源。
目前权属管理领域存在的几个难题是：
* 物品所有权的确认和管理；
* 交易的安全性和可靠性保障；
* 必要的隐私保护机制。


利用区块链技术，物品的所有权是写在数字链上的，谁都无法修改。并且一旦出现合同中约定情况，区块链技术将确保合同能得到准确执行。这能有效减少传统情况下纠纷仲裁环节的人工干预和执行成本

<a id="markdown-74-资源共享" name="74-资源共享"></a>
## 7.4. 资源共享
相比于依赖于中间方的资源共享模式，基于区块链的模式有潜力更直接的连接资源的供给方和需求方，其透明、不可篡改的特性有助于减小摩擦。
<a id="markdown-75-贸易管理" name="75-贸易管理"></a>
## 7.5. 贸易管理
区块链技术可以帮助自动化国际贸易和物流供应链领域中繁琐的手续和流程。基于区块链设计的贸易管理方案会为参与的多方企业带来极大的便利。另外，贸易中销售和法律合同的数字化、货物监控与检测、实时支付等方向都可能成为创业公司的

<a id="markdown-76-物联网" name="76-物联网"></a>
## 7.6. 物联网
物联网络中每一个设备分配地址，给该地址所关联一个账户，用户通过向账户中支付费用可以租借设备，以执行相关动作，从而达到租借物联网的应用。

典型的应用包括	PM2.5	监测点的数据获取、温度检测服务、服务器租赁、网络摄像头数据调用等等。
 
另外，随着物联网设备的增多、边沿计算需求的增强，大量设备之间形成分布式自组织的管理模式，并且对容错性要求很高。区块链自身分布式和抗攻击的特点可以很好地融合到这一场景中。

<a id="markdown-8-分布式系统" name="8-分布式系统"></a>
# 8. 分布式系统
<a id="markdown-81-一致性问题consistency" name="81-一致性问题consistency"></a>
## 8.1. 一致性问题(Consistency)
对于系统中的多个服务结点,给定一系列操作, 在协议(某种共识算法)保障下, 使得它们对处理结果达成某种程度的一致存在的问题
* 节点之间的网络通讯是不可靠的，包括任意延迟和内容故障；
* 节点的处理可能是错误的，甚至节点自身随时可能宕机；
* 同步调用会让系统变得不具备可扩展性。


>>解决的基本思想: 将可能引发不一致的并行操作串行化

<a id="markdown-811-理想一致性" name="811-理想一致性"></a>
### 8.1.1. 理想一致性
分布式系统一致性应满足
* 可终止性（Termination）：一致的结果在有限时间内能完成；
* 共识性（Consensus）：不同节点最终完成决策的结果应该相同；
* 合法性（Validity）：决策的结果必须是其它进程提出的提案。

<a id="markdown-812-带约束的一致性" name="812-带约束的一致性"></a>
### 8.1.2. 带约束的一致性
理想情况的强一致性是很难达到的. 其实实际需求并没有那么强,可以适当放宽一致性要求.

<a id="markdown-82-共识算法" name="82-共识算法"></a>
## 8.2. 共识算法
由于响应请求往往存在时延、网络会发生中断、节点会发生故障、甚至存在恶意节点故意要破坏系统, 不能简单地通过多播过程投票.

一般地，把故障（不响应）的情况称为“非拜占庭错误”，恶意响应的情况称为“拜占庭错误”（对应节点为拜占庭节点）。


<a id="markdown-821-paxos--raft" name="821-paxos--raft"></a>
### 8.2.1. Paxos & Raft
这种算法解决的是对于 分布式系统中存在故障(fault), 但不存在恶意(corrupt)结点场景(即可能消息丢失或重复, 但无错误信息)下的共识达成(consensus)问题.

<a id="markdown-822-flp不可能原理" name="822-flp不可能原理"></a>
### 8.2.2. FLP不可能原理
>在网络可靠，存在节点失效（即便只有一个）的最小化异步模型系统中，不存在一个可以解决一致性问题的确定性算法。

即一个可扩展的分布式系统的共识问题的下限是无解

它告诉人们，不要浪费时间去为异步分布式系统设计在任意场景下都能实现共识的算法。

但是可以在付出一定代价下, 达到一定的目标.

下面的CAP原理告诉我们能做到多少
<a id="markdown-83-cap原理" name="83-cap原理"></a>
## 8.3. CAP原理
分布式系统不可能同时确保一致性（Consistency）、可用性（Availablity）和分区容忍性（Partition），**设计中往往需要弱化对某个特性的保证。**

* 一致性（Consistency）：任何操作应该都是原子的，发生在后面的事件能看到前面事件发生导致的结果，注意这里指的是强一致性；
* 可用性（Availablity）：在有限时间内，任何非失败节点都能应答请求；
* 分区容忍性（Partition）：网络可能发生分区，即节点之间的通信不可保障。


CAP 不能同时满足,设计系统时针对应用场景弱化对某个特性的支持
* 弱化一致性: 如网站静态页面内容, 实时性较弱的查询类数据库
* 弱化可用性: 对结果一致性很敏感的应用. 如银行取款机
* 弱化分区容忍性: 如 某些关系型数据库, ZooKeeper


<a id="markdown-84-acid-原则" name="84-acid-原则"></a>
## 8.4. ACID 原则
即	Atomicity（原子性）、Consistency（一致性）、Isolation（隔离性）、Durability（持久性）。
ACID	原则描述了对分布式数据库的一致性需求，同时付出了可用性的代价。
Atomicity：每次操作是原子的，要么成功，要么不执行；
Consistency：数据库的状态是一致的，无中间状态；
Isolation：各种操作彼此互相不影响；
Durability：状态的改变是持久的，不会失效。

一个与之相对的原则是	BASE（Basic	Availiability，Soft	state，Eventually	Consistency），牺牲掉对一致性的约束（最终一致性），来换取一定的可用性。

<a id="markdown-9-密码学技术" name="9-密码学技术"></a>
# 9. 密码学技术
<a id="markdown-91-hash算法" name="91-hash算法"></a>
## 9.1. hash算法
是一种信息摘要, 可以用于检验内容的完全性, 一致性等. 流行的有  md5, sha-1, sha-2(Secure Hash Algorithm), sha-1已被证明不具备"强抗碰撞性"
<a id="markdown-92-加解密算法" name="92-加解密算法"></a>
## 9.2. 加解密算法
![introduction-to-blockchain-4](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/introduction-to-blockchain-4.png)

组件包括: 加解密算法,加密密钥,解密密钥.
根据加解密的密钥是否相同,可以分成对称加密与非对称加密(asymmetrix cryptography).
<a id="markdown-921-对称" name="921-对称"></a>
### 9.2.1. 对称
对称密码从实现原理上可以分为两种：分组密码和序列密码。前者将明文切分为定长数据块
作为加密单位，应用最为广泛。后者则只对一个字节进行加密，且密码不断变化，只用在一
些特定领域，如数字媒介的加密等。
代表算法
* DES（Data	Encryption	Standard）：经典的分组加密算法,将	64	位明文加密为	64	位的密文，其密钥长度为	56位	+	8	位校验。现在已经很容易被暴力破解。
* 3DES：三重	DES	操作：加密	-->	解密	-->	加密，处理过程和加密强度优于	DES，但现在也被认为不够安全。
* AES（Advanced	Encryption	Standard）：分组算法，分组长度为	128、192、256	位三种。AES	的优势在于处理速度快，整个过程可以数学化描述，目前尚未有有效的破解手段。

适用于大量数据的加解密；不能用于签名场景；需要提前分发密钥。
<a id="markdown-922-非对称加密" name="922-非对称加密"></a>
### 9.2.2. 非对称加密
非对称加密是现代密码学历史上最为伟大的发明，可以很好的解决对称加密需要的提前分发密钥问题。

一般比对称加解密算法慢两到三个数量级；同时加密强度相比对称加密要差。
非对称加密算法的安全性往往需要基于数学问题来保障，目前主要有基于大数质因子分解、离散对数、椭圆曲线等几种思路。
代表算法:
* RSA：经典的公钥算法。算法利用了对大数进行质因子分解困难的特性，但目前还没有数学证明两者难度等价，或许存在未知算法在不进行大数分解的前提下解密。
* Diffie-Hellman	密钥交换：基于离散对数无法快速求解，可以在不安全的通道上，双方协商一个公共密钥。
* ElGamal：利用了模运算下求离散对数困难的特性。被应用在PGP	等安全工具中。
* 椭圆曲线算法（Elliptic	curve	cryptography，ECC）：基于对加解密算法椭圆曲线上特定点进行特殊乘法逆运算难以计算的特性。一般适用于签名场景或密钥协商，不适于大量数据的加解密。

RSA	算法等已被认为不够安全，一般推荐采用椭圆曲线系列算法。

<a id="markdown-923-混合加密机制" name="923-混合加密机制"></a>
### 9.2.3. 混合加密机制
即先用计算复杂度高的非对称加密协商一个临时的对称加密密钥（会话密钥，一般相对内容
来说要短的多），然后双方再通过对称加密对传递的大量数据进行加解密处理。
典型的场景是现在大家常用的	HTTPS	机制。HTTPS	实际上是利用了	Transport	Layer
Security/Secure	Socket	Layer（TLS/SSL）来实现可靠的传输。TLS	为	SSL	的升级版本

<a id="markdown-93-数字签名" name="93-数字签名"></a>
## 9.3. 数字签名
类似在纸质合同上签名确认合同内容，数字签名用于证实某数字内容的完整性（integrity）和来源（或不可抵赖，non-repudiation）。

一个典型的场景是，A	要发给	B	一份信息. 
A	先对文件进行摘要，然后用自己的私钥进行加密，将文件和加密串都发给B。B	收到文件和加密串后，用	A	的公钥来解密加密串，得到原始的数字摘要，跟对文件进行摘要后的结果进行比对。

<a id="markdown-94-数字证书" name="94-数字证书"></a>
## 9.4. 数字证书
数字证书用来证明某个公钥是谁的，并且内容是正确的。

数字证书内容可能包括版本、序列号、签名算法类型、签发者信息、有效期、被签发人、签发的公开密钥、CA	数字签名、其它信息等等，一般使用最广泛的标准为	ITU	和	ISO	联合制定的	X.509	规范。
其中，最重要的包括		签发的公开密钥	、	CA	数字签名		两个信息。因此，只要通过这个证书就能证明某个公钥是合法的，因为带有	CA	的数字签名。

<a id="markdown-95-merkle-树" name="95-merkle-树"></a>
## 9.5. Merkle 树
![merkle](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/merkle.png)
<a id="markdown-951-定义" name="951-定义"></a>
### 9.5.1. 定义
默克尔树（又叫哈希树）是一种二叉树，由一个根节点、一组中间节点和一组叶节点组成。最下面的叶节点包含存储数据或其哈希值，每个中间节点是它的两个孩子节点内容的哈希值，根节点也是由它的两个子节点内容的哈希值组成。

<a id="markdown-952-特点" name="952-特点"></a>
### 9.5.2. 特点
 底层数据的任何变动，都会传递到其父亲节点，一直到树根。
<a id="markdown-953-应用场景" name="953-应用场景"></a>
### 9.5.3. 应用场景
* 快速比较大量数据：当两个默克尔树根相同时，则意味着所代表的数据必然相同。
* 快速定位修改：例如上例中，如果	D1	中数据被修改，会影响到	N1，N4	和	Root。因此，沿着	Root	-->	N4	-->	N1，可以快速定位到发生改变的	D1；
* 零知识证明：例如如何证明某个数据（D0……D3）中包括给定内容	D0，很简单，构造一个默克尔树，公布	N0，N1，N4，Root，D0	拥有者可以很容易检测	D0	存在，但不知道其它内容。


<a id="markdown-96-同态加密" name="96-同态加密"></a>
## 9.6. 同态加密
同态加密（Homomorphic	Encryption）是一种特殊的加密方法，允许对密文进行处理得到仍然是加密的结果，即对密文直接进行处理，跟对明文进行处理再加密，得到的结果相同。从代数的角度讲，即同态性。(保运算)

<a id="markdown-10-比特币" name="10-比特币"></a>
# 10. 比特币

<a id="markdown-101-从实体货币到数字货币" name="101-从实体货币到数字货币"></a>
## 10.1. 从实体货币到数字货币
![introduction-to-blockchain-5](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/introduction-to-blockchain-5.png)

<a id="markdown-102-去中心化实现数字货币的难题" name="102-去中心化实现数字货币的难题"></a>
## 10.2. 去中心化实现数字货币的难题
* 货币的防伪：谁来负责对货币的真伪进行鉴定；
* 货币的交易：如何确保货币从一方安全转移到另外一方；
* 避免双重支付：如何避免同一份货币支付给多个接收者。


<a id="markdown-103-原理与设计" name="103-原理与设计"></a>
## 10.3. 原理与设计
比特币网络是一个分布式的点对点网络，网络中的矿工通过“挖矿”来完成对交易记录的记账过程，维护网络的正常运行。
<a id="markdown-1031-基本交易过程" name="1031-基本交易过程"></a>
### 10.3.1. 基本交易过程
比特币中没有账户的概念。因此，每次发生交易，用户需要将交易记录写到比特币网络账本中，等网络确认后即可认为交易完成。

除了挖矿获得奖励的	coinbase	交易只有输出，正常情况下每个交易需要包括若干输入和输出，未经使用（引用）的交易的输出（Unspent	Transaction	Outputs，UTXO）可以被新的交易引用作为其合法的输入。被使用过的交易的输出（Spent	Transaction	Outputs，STXO），则无法被引用作为合法输入。

<a id="markdown-1032-概念" name="1032-概念"></a>
### 10.3.2. 概念
* 账户

比特币采用了非对称的加密算法，用户自己保留私钥，对自己发出的交易进行签名确认，并公开公钥。
比特币的账户地址其实就是用户公钥经过一系列	Hash（HASH160，或先进行	SHA256，然后进行	RIPEMD160）及编码运算后生成的	160	位（20	字节）的字符串。
* 交易

易可能包括如下信息：
付款人地址：合法的地址，公钥经过	SHA256	和	RIPEMD160	两次	Hash，得到	160	位Hash	串；
付款人对交易的签字确认：确保交易内容不被篡改；
付款人资金的来源交易	ID：从哪个交易的输出作为本次交易的输入；
交易的金额：多少钱，跟输入的差额为交易的服务费；
收款人地址：合法的地址；
时间戳：交易何时能生效
节点收到交易信息后，将进行如下检查：
交易是否已经处理过；
交易是否合法。包括地址是否合法、发起交易者是否是输入地址的合法拥有者、是否是UTXO；
交易的输入之和是否大于输出之和。

* 交易脚本: 保障交易的完成的核心机制
* 区块

比特币区块链的一个区块不能超过	1	MB，将主要包括如下内容：
区块大小：4	字节；
区块头：80	字节：
交易个数计数器：1~9	字节；
所有交易的具体内容，可变长，匹配	Merkle	树叶子节点顺序。
* 避免作恶: 在一个开放的网络中，无法通过技术手段保证每个人都是合作的。但可以通过经济博弈来让合作者得到利益，让非合作者遭受损失和风险 .  如共识机制PoW

<a id="markdown-104-闪电网络" name="104-闪电网络"></a>
## 10.4. 闪电网络
比特币交易性能：全网每秒	7	笔左右的交易速度，远低于传统的金融交易系统

提出闪电网络的解决方法
主要通过引入智能合约的思想来完善链下的交易渠道。核心的概念主要有两个：RSMC（Recoverable	Sequence	Maturity	Contract）和	HTLC（Hashed	TimelockContract）。前者解决了链下交易的确认问题，后者解决了支付通道的问题。

<a id="markdown-105-侧链" name="105-侧链"></a>
## 10.5. 侧链
以比特币区块链作为主链（Parent	chain），其他区块链作为侧链，二者通过双向挂钩（Two-way	peg），可实现比特币从主链转移到侧链进行流通。
![introduction-to-blockchain-6](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/introduction-to-blockchain-6.png)

侧链可以是一个独立的区块链，有自己按需定制的账本、共识机制、交易类型、脚本和合约的支持等。侧链不能发行比特币，但可以通过支持与比特币区块链挂钩来引入和流通一定数量的比特币。当比特币在侧链流通时，主链上对应的比特币会被锁定，直到比特币从侧链回到主链。可以看到，侧链机制可将一些定制化或高频的交易放到比特币主链之外进行，实现了比特币区块链的扩展。侧链的核心原理在于能够冻结一条链上的资产，然后在另一条链上产生，可以通过多种方式来实现, 如 SPV
<a id="markdown-11-以太坊" name="11-以太坊"></a>
# 11. 以太坊
以太坊区块链底层也是一个类似比特币网络的	P2P	网络平台，智能合约运行在网络中的以太坊虚拟机里。网络自身是公开可接入的，任何人都可以接入并参与网络中数据的维护，提供运行以太坊虚拟机的资源。
<a id="markdown-111-特点" name="111-特点"></a>
## 11.1. 特点
跟比特币项目相比
* 支持图灵完备的智能合约，设计了编程语言	Solidity	和虚拟机	EVM；
* 选用了内存需求较高的哈希函数，避免出现强算力矿机、矿池攻击；
* 叔块（Uncle	Block）激励机制，降低矿池的优势，并减少区块产生间隔（10	分钟降低到15	秒左右）；

*采用账户系统和世界状态，而不是	UTXO，容易支持更复杂的逻辑；
* 通过	Gas	限制代码执行指令数，避免循环执行攻击；
* 支持	PoW	共识算法，并计划支持效率更高的	PoS	算法。

<a id="markdown-112-概念" name="112-概念"></a>
## 11.2. 概念
* 智能合约:即以计算机程序的方式来缔结和运行各种合约。
* 账户: 比特币在设计中并没有账户（Account）的概念，而是采用了UTXO	模型记录整个系统的状态。任何人都可以通过交易历史来推算出用户的余额信息。而以太坊则直接用账户来记录系统状态。每个账户存储余额信息、智能合约代码和内部数据存储等。 以太坊账户分为两类

    * 合约账户：存储执行的智能合约代码，只能被外部账户来调用激活；
    * 外部账户：以太币拥有者账户，对应到某公钥。账户包括	nonce、balance、storageRoot、codeHash	等字段，由个人来控制。

* 交易: 是指从一个账户到另一个账户的消息数据

包括如下字段：
    * to：目标账户地址。
    * value：可以指定转移的以太币数量。
    * nonce：交易相关的字串，用于防止交易被重放。
    * gasPrice：执行交易需要消耗的	Gas	价 格。
    * startgas：交易消耗的最大	Gas	值。
    * signature：签名信息。
在发送交易时，用户需要缴纳一定的交易费用，通过以太币方式进行支付
和消耗。
* 燃料（Gas），控制某次交易执行指令的上限。每执行一条合约指令会消耗固定的燃料。当某个交易还未执行结束，而燃料消耗完时，合约执行终止并回滚状态。

Gas	可以跟以太币进行兑换。需要注意的是，以太币的价格是波动的，但运行某段智能合约的燃料费用可以是固定的，通过设定	Gas	价格等进行调节
<a id="markdown-113-主要设计" name="113-主要设计"></a>
## 11.3. 主要设计
* 运行环境: EVM

以太坊虚拟机是一个隔离的轻量级虚拟机环境，运行在其中的智能合约代码无法访问本地网络、文件系统或其它进程。
对同一个智能合约来说，往往需要在多个以太坊虚拟机中同时运行多份，以确保整个区块链数据的一致性和高度的容错性。另一方面，这也限制了整个网络的容量。
* 开发语言: Solidity, vyper...  智能合约编写完毕后，用编译器编译为以太坊虚拟机专用的二进制格式（EVM	bytecode），由客户端上传到区块链当中，之后在矿工的以太坊虚拟机中执行。
* 交易模型: 以太坊的账户模型与比特币的 UXTO 模型对比![introduction-to-blockchain-7](https://raw.githubusercontent.com/mbinary/mbinary.github.io/hexo/source/images/introduction-to-blockchain-7.png)
* 共识: 基于成熟的	PoW	共识的变种算法	Ethash	协议
* 客户端和开发库

以太坊客户端可用于接入以太坊网络，进行账户管理、交易、挖矿、智能合约等各方面操
作。

<a id="markdown-12-超级账本-hyperldger" name="12-超级账本-hyperldger"></a>
# 12. 超级账本-Hyperldger
Hyperledger	项目是首个面向企业的开放区块链技术的重要探索.该项目试图打造一个透明、公开、去中心化的分布式账本项目，作为区块链技术的开源规范和标准，让更多的应用能更容易的建立在区块链技术之上。

