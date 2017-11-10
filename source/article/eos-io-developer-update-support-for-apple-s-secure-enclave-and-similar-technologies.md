# EOS.IO Developer Update - Support for Apple's secure enclave and similar technologies | EOS 开发更新 - 支持苹果的安全孤块和类似技术

> 本文翻译自：https://eosio.github.io/eos/group__eosc.html
>
> 译者：[区块链中文字幕组 胡亮](https://github.com/gumoon)
>
> 翻译时间：2017-11-5

The block.one team has been working around the clock to make eos.io the most advanced blockchain software possible. While advancing the core software, we have also been working with a number of major players in the industry to migrate their platforms to make use of the eos.io software.

block.one 团队正在以尽可能快的速度开发这个最高级的区块链。除了改进核心软件，我们也在和一些行业内的主要玩家合作，整合他们的平台来支持运行 eos.io 软件。

## Constantly Evolving Design | 持续优化设计

EOS.IO is not a fixed specification, but a living design that is constantly being enhanced above and beyond our original white paper. We strive to make EOS.IO the best possible platform and to deliver it as rapidly as we can. Today we would like to discuss some of the changes that we are in the process of implementing.

EOS.IO 不是一个固定的规范，而是一个动态的设计，它持续的被增强并超越了我们原来的白皮书。我们力争让 EOS.IO 成为最好的平台，并且尽快交付。今天，我们想讨论一下我们在实现过程中的一些变动。

### Parallel Execution Ahead of Schedule | 并发执行引擎开发工作提前开始
Our original roadmap called for a single threaded implementation to be complete by June 2018 and for multi-threaded development to take place thereafter. We are excited to share that work on the parallel execution engine has begun 8 months ahead of schedule and we believe that it will be ready by June 2018. The work required to make this happen includes a complete rewrite of chainbase, the underlying database technology behind Steem.

我们原来的路线图写着2018年6月完成单线程引擎实现，随后开始多线程引擎开发。我们非常高兴的告诉您，开发并行执行引擎的工作已经提前8个月开始了，我们相信到2018年6月将完成。为了完成这项工作，先需要完成 chainbase 的重写。chainbase 是 Steem 的底层数据库技术。

### New Shard-Aware EOS.IO Database | 新的智能分片 EOS.IO 数据库
Over the past several weeks we have implemented a new shard-aware database that is designed to enable multiple threads to access independent memory regions (called shards) at the same time. Shard data ranges are not fixed, different transactions can group different shards depending upon the necessary data access patterns.

过去的几周，我们已经实现了一个新的智能分片数据库，它支持多线程同时访问独立的内容区块（叫做分片）。分片数据范围不是固定的，不同的交易可以依靠必要数据访问模式组合不同的分片。

### New Multi-Threaded Key Recovery | 新的多线程私钥恢复
We have developed a multi-threaded key recovery service that will accelerate the rate at which transactions can be validated compared to our existing single threaded approach.

我们开发了一个多线程私钥恢复服务，相比我们已经存在的单线程服务，它将加快生效交易。

## Interblockchain Communication | 区块链间通信
We have been putting a lot of design work into how two blockchains will communicate with each other. This involves carefully crafting the structure of our merkle trees to make proofs meaningful and efficient. It also means restructuring our block headers and transaction headers.

关于两个区块链之间如何通信，我们已经做了很多设计工作。这需要仔细而精巧的设计我们的默克尔树来确保证明有意义并且高效。这也意味着需要重构我们的区块头和交易头。

### Introducing Regions | 引入 region
Transactions now have an extra header field, a region. Think of it like a postal code indicating which blockchain the transaction is intended to be included in. By default region 0 implies the current chain and all other chains have different region codes. When a contract generates a deferred transaction for another region it is a signal for block producers to ignore that transaction. It is up to the other chains to use merkle proofs to verify the region code they have assigned themselves.

现在交易头增加了一个额外的字段：region。设想它就像一个邮政编码，表明交易想要包括在哪个区块链里。 region 默认为 0，表示当前区块链，其他的链有不同的 region 码。当合约为其他 region 生成一个推迟的交易，表示要求区块生产者忽略那个交易。这由其它链使用默克尔证明来验证他们自己分配的 region 码。

### Error Handling on Deferred (Asynchronous) Transactions | 延迟交易的错误处理
Originally the EOS.IO software had no error handling for asynchronous transactions except for timeout. The reason we had this limitation was that error handlers themselves could fail. The only option for error handling was to wait for the transaction to expire without executing.

原来的 EOS.IO 软件针对异步交易没有错误处理机制，除了超时。原因是错误处理函数自身可能失败。需要错误处理的唯一可能是等待交易过期而不执行。

We have updated the block structure to allow producers to schedule transactions that fail for objective reasons and to execute an error handler. There are 3 possible states that a deferred transaction could be included as: success, error with successful error handler, or error with failure of error handler. Only objective failures are included, subjective failures such as using too much wall clock time will still require waiting for a timeout.

我们更新了区块结构允许块生产者调度由于客观原因失败的交易，并且执行错误处理函数。延迟交易有三种可能的状态：成功、错误处理器返回成功的失败、错误处理器返回失败的失败。只包括客观的失败，像使用太多时钟时间的主观失败将仍然要求等待超时。

## Adding Support for Apple's Secure Enclave | 添加对苹果安全孤块的支持
We are extending our support for elliptic curve keys validation to include the secp256r1 curve. This is the standard created by NIST and used by Apple, Android, and many Smart Cards. Users will have the option to pick their curves (secp256k1, is used by Bitcoin and EOS by default) and can use both (r1 and k1) if they don't know which one to trust! The most important aspect of this is that it will give every cell phone user a hardware wallet with biometric 2nd factor validation.

我们拓展了椭圆曲线私钥验证支持，增加了 secp256r1 曲线。这是 NIST 创建的标准，苹果、安卓、和很多智能卡使用该标准。用户将可以选择它们支持的曲线（secp256k1, 被比特币和 EOS 默认支持），如果不知道选择信任哪个的话，可以都选择（r1 和 k1)。这个最重要的方面是，它给予个人手机用户一个硬件钱包，支持使用生理第二因素验证。

Implementation is ongoing, but the design decision has been made and should make EOS.IO usable in far more environments.

方案已经定了，目前在实现中。它应该会让 EOS.IO 支持更多的硬件。

## Conclusion | 总结
There is a lot of work going on here at block.one and the eos.io software is becoming better every day.

在 block.one， 更多工作正在继续，eos.io 软件也在一天天变得更好。

----------------------------------------------------

#### 区块链中文字幕组

致力于前沿区块链知识和信息的传播，为中国融入全球区块链世界贡献一份力量。

如果您懂一些技术、懂一些英文，欢迎加入我们，加微信号:w1791520555。

[点击查看项目GITHUB，及更多的译文...](https://github.com/BlockchainTranslator/EOS)

#### 本文译者简介

胡亮 区块链技术爱好者，欢迎加微信号:haobaba-huliang

本文由币乎社区（bihu.com）内容支持计划赞助。

版权所有，转载需完整注明以上内容。

----------------------------------------------------

