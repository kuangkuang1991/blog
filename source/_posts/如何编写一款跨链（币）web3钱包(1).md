---
title: 如何编写一款跨链（币）web3钱包(1)
date: 2023-02-20 22:57
categories: 技术
tags: 
- 技术
- JS
- web3
- wallet
toc: true
---

### 基本原理

咱们以ETH和TRON跨币钱包为例。首先你得知道币与币之间的关联和区别。关联在于它们同属以太坊这个大家庭，区别则是每个币种有属于自己的coin id（注意区分coin id、chainId和networkId，后两者基本可以等同）。

比如ETH的coin id 是60，TRX的coin id则是195。此值可用于以助记词生成钱包对象时作为path的参数。具体来说，比如etherjs的方法：ethers.Wallet.fromMnemonic(mnemonic, path)。第一个参数是助记词，第二个参数path就是EIP-44规范的密钥路径，拆分如下：

m / purpose' / coin' / account' / change / address_index

举一个实例path：m/44'/60'/0'/0/0。m=m，purpose=44都是固定的。60指向ETH，account是账户索引，一般是0，change用于区分外部公开地址和内部地址，一般也是0，address_index是地址索引，一个钱包对象可以生成N个地址，官方推荐在20个以内，所以这里的值可以是0到19（对应metamask里面的功能就是add account）。

推荐一个原理文章：https://learnblockchain.cn/2018/09/28/hdwallet/#%E7%A7%98%E9%92%A5%E8%B7%AF%E5%BE%84%E5%8F%8ABIP44

基于上述原理，所以可以分别生成ETH和TRON的钱包对象：

ETH钱包对象：ethers.Wallet.fromMnemonic(mnemonic, "m/44'/60'/0'/0/0")

TRON钱包对象：ethers.Wallet.fromMnemonic(mnemonic, "m/44'/195'/0'/0/0")

当然，基于etherjs，上述生成的ETH钱包对象可以直接使用，调用其各种方法。Tron则稍有不同，是使用官方库提供的TronWeb，实例化后使用。一般仅需用到TRON钱包对象的privateKey，用于生成address和发起交易传参。

### 地址、账户和状态

币种的最明显区别，应该就是address格式了。根据Tron官网所述

> TRON 地址为大写字母 T 开头的 34 字符地址，例如 T9zTTVfegCiJ5ovip4y2dCPiEdXT9EmtEw 。以太坊地址前附加字节 0x41 后执行 Base58check 操作所得

简单来说，已知ETH address，可以推导出Tron address，反之则无法推导。

参考前文提到的path中的address_index，可知要实现主账户和子账户间的切换，只需要在生成钱包对象时传入不同的address_index值即可。比如初始账户是m/44'/195'/0'/0/0，账户2则是m/44'/195'/0'/0/1，账户3则是m/44'/195'/0'/0/2……

相比ETH只有一个address和balance的状态，Tron还有带宽、能量，经由每日官方分配可得。交易没有gas，但会消耗带宽和能量，当其不足时，会直接烧掉TRX。

另外需要注意的是，Tron初始账户需要转入1TRX才能激活使用。

### 在币内切换网络

ETH中，传入rpc_url可以初始化一个provider（ethers.providers.JsonRpcProvider）。钱包对象是跨网络的，provider则是当前网络下的实例对象。provider下即有很多使用方法如getNetwork，getGasPrice，getBalance等等。

Tron中没有provider的概念，只是在实例化tronWeb对象时，传入rpc_url网络参数参数即可生成和网络关联的实例对象。