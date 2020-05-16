# CoMove
CoMove is a short name for Cosmos-Move, A Smart Contract Framework on Cosmos Blockchain. 

## Background

[Cosmos](https://cosmos.network) is a well-known cross-chain blockchain project. It not only provides a blockchain cross-chain solution, but also provides a very excellent blockchain development framework - [Cosmos-SDK](https://github.com/cosmos/cosmos-sdk). In Cosmos's design philosophy, you can develop a completely controllable blockchain for each application. However, in many use cases of DApps, many developers do not want to develop an independent blockchain and operate it because this requires a lot of resources to be successful, the easier way for them is developing DApps on mature blockchains, such as CosmosHub, IrisHub, so smart contracts are still necessary in many scenarios.

[Move](https://github.com/libra/libra/tree/master/language) is a dedicated smart contract programming language created by Facebook Libra. It is small, secure and powerful. But it is still immature and still under heavy development. However, I am convinced that it will be the best smart contract language.

[CosmWasm](https://github.com/cosmwasm/) It is the first smart contract solution on Cosmos. Thanks to Ethan Frey and other contributors for their exploration and contribution in this area.

## RoadMap

[CoMove Project](https://github.com/orgs/co-move/projects/1) 

## About GO and Rust

Cosmos is developed in Go, while Move VM is developed in Rust. So, to implement Co-Move, the first problem we need to solve is cross-language interoperation. There are currently two mature options to choose from:

* CGO + Rust FFI, you can learn more [here](https://github.com/medimatrix/rust-plus-golang).
* Web Assembly

I personally prefer the CGO + FFI solution. As far as I know, the virtual machine for Web Assembly is also implemented using CGO, so I hope this solution is more direct. Intuitively, ‘Move VM in WebAssembly VM’ looks a little more complicated, although it may not be convincing.

If there is time later, we will consider the solution of inter-cross compilation: [RUSTGO: CALLING RUST FROM GO WITH NEAR-ZERO OVERHEAD](https://blog.filippo.io/rustgo/)

## Design ideas

CoMove plans to provide a set of smart contract tools. it consists of many parts. Considering the limited resources, we plan to focus on the migration of virtual machine and the transformation of the Move programming language. When this infrastructure is complete, let's develop the rest of the work.

Honestly, developing CoMove is a challenging task, and one of the most challenging tasks is how to integrate the Cosmos SDK and Move VM. Usually when dealing with this kind of problem, we will introduce the "bridge" design pattern to ensure that both sides of the integration can be as unchanged as possible. Throughout the implementation we will implement two bridges: Ship and DataLoader.

CoMove is mainly composed of the following parts:

### Move Module

It is the implementation of CoMove in the Cosmos SDK, and it is a standard Cosmos Module. It is responsible for implementing the publishing of smart contracts, calling the Move VM to execute smart contracts, and processing the execution results of smart contracts.

### Ship

As we mentioned earlier, Ship is a bridge that connects the module module and the Move VM. Move VM is still in a very active development state, so we need to make a wrapper for it to ensure that we can adapt well to the update of the Move VM. Another important function is that it allows Cosmos Module written in GO language to call Move VM written in Rust language. When the Move VM virtual machine executes a smart contract, many Global States will change according to the execution results, but these changes will not update to blockchain immediately. Instead, a "Write Set" is generated and handed over to an execution module. This is exactly what we need.

### DataLoader

DataLoader is another bridge we designed to help Move implement some native functions. One of the most important functions is DataLoaderGlobal. It is used to load some read-only Global States during smart contract execution. So we simply call it DataLoader.

### Standard Library

Move was born for Libra blockchain, so he naturally left a deep imprint on Libra. After all, Libra and Cosmos are different. We need to provide a standard library for Cosmos.

### Toolset

Although we introduced the bridge design pattern in the design to reduce the coupling between the various components of the system, it does not mean that there is no change. Instead, all key components may actually change. mainly include:
* Move Virtual Machine
* Move Bytecode Verifier
* Move Compiler

## Sponsors

* [ping.pub](https://ping.pub) 

Ping.pub is a validator operator and a participant, builder and promoter in the Cosmos ecosystem. Since the mainnet launch, we have / are contributing 4 tools / modules: [LOOK Explorer](https://cosmos.ping.pub)，[PING Wallet](https://wallet.ping.pub)，[Faucet Module](https://github.com/ping-pub/modules/blob/master/incubator/faucet/README.md)，[Co-Move](https://github.com/co-move/). We also hosted / co-sponsored 5 Meetups in 5 different cities in China. Although our delegation is inversely proportional to our contribution, we still insist on becoming one of the most active contributors.


## 背景

[Cosmos](https://cosmos.network) 是知名的跨链区块链项目。它不仅提供了区块链跨链的解决方案，而且还提供了一个非常优秀的区块链开发框架 - [Cosmos-SDK](https://github.com/cosmos/cosmos-sdk)。在Cosmos的设计理念里，你可以为每一个应用开发一个完全自主可控的区块链。但是在很多DApp的使用场景，许多开发者并不想开发一个条独立的区块链，运营它，因为这需要很多资源才能做好，他们只想在某一个成熟区块链（例如：CosmosHub，IrisHub）上去做一些功能扩展，所以，智能合约在很多场景下还是有必要的。

[Move](https://github.com/libra/libra/tree/master/language) 是由Facebook Libra创造的专用智能合约编程语言，它小巧，安全，强大。但是他现在还不成熟，还在紧张的开发中，不过，我深信它会是最好的智能合约语言。

[CosmWasm](https://github.com/cosmwasm/) 它是首个Cosmos上的智能合约解决方案，感谢Ethan Frey以及其他贡献者在这一领域的探索和贡献。

## 关于 GO 和 Rust

Cosmos 使用Go语言开发，而Move VM使用的Rust语言开发。所以，要实现Co-Move, 我们需要解决的第一个问题就是跨语言调用的问题。现在主要有两个成熟的方案可供选择：

* CGO + Rust FFI 如果你感兴趣，你可以在[这里](https://github.com/medimatrix/rust-plus-golang)了解更多信息。
* Web Assembly

我个人倾向于采用CGO+FFI的方案，据我了解其实Web Assembly的虚拟机也是使用CGO来做的，所以，我希望这个方案更直接一点。不管怎么说，Move VM in WebAssembly VM看起来要复杂一点，虽然这么说可能不太科学。

如果后面有时间了，我们会考虑尝试交互编译的方案：[RUSTGO: CALLING RUST FROM GO WITH NEAR-ZERO OVERHEAD](https://blog.filippo.io/rustgo/)

## 设计思路

CoMove计划提供一整套的智能合约工具集。他由很多部分组成。考虑到资源有限，我们计划先将主要精力用在虚拟机的移植和Move编程语言的改造上。等到这些基础设施完成后，我们再来开发剩下来的其他工作。

老实说，开发CoMove是一个具有挑战性的工作，其中最具挑战的工作之一就是如何集成Cosmos SDK和Move VM。通常在处理这类问题时候的，我们都会引入"bridge"设计模式，来保证被集成的双方都可以尽可能地保持不变。在整个实现过程中我们将实现两座桥，它们分别是：Ship 和 DataLoader。

CoMove主要由以下几个部分组成：

### Move Module

它是CoMove在Cosmos SDK的实现，它就是一个标准的Cosmos Module. 负责实现智能合约的发布，调用Move VM来执行智能合约和处理智能合约的执行结果等功能

### Ship

正如我们之前提到的，Ship是一个连接module module和Move VM的桥。Move VM目前仍然处于非常活跃的开发状态，因此，我们需要对他做一个封装，用来确保我们可以很好地适应Move VM的更新。它的另一个重要的作用就是可以让GO语言写的Cosmos Module可以调用Rust语言写的Move VM。Move VM虚拟机在执行智能合约的时候，很多Global State都会根据执行结果而发生变化，但是这些变化不会立即生效，而是生成一个"Write Set"结果集，交给一个叫execution module的处理。这也正是我们需要的。

### DataLoader

DataLoader是我们设计的另一个桥，主要用来帮助Move实现一些Native functions. 这其中最重要的一个function就是DataLoaderGlobal. 它用在智能合约执行过程中获取一些只读的Global States. 所以我们干脆就叫他DataLoader.

### Standard Library 

Move是为Libra而生的，所以他自然留下了Libra深刻的烙印。然而，Libra和Cosmos毕竟不同。我们需要提供适用于Cosmos的standard library.

### Toolset

虽然我们在设计的时候引入了桥设计模式来降低系统各个组件之间的耦合性，但是并不意味着就没有改变。相反，其实所有关键部件可能都有改变。主要包括：
* Move Virtual Machine
* Move Bytecode Verifier
* Move Compiler

## 赞助商 / Sponsors

* [ping.pub](https://ping.pub) 

Ping.pub是节点运营商，也是Cosmos生态的参与者，建设者和推广者，至从主网上线以来，我们已经/正在贡献了4个工具/模块，分别是：[LOOK Explorer](https://cosmos.ping.pub)，[PING Wallet](https://wallet.ping.pub)，[Faucet Module](https://github.com/ping-pub/modules/blob/master/incubator/faucet/README.md)，[Co-Move](https://github.com/co-move/)。我们还在中国5个不同城市主办/协办了5场Meetup. 虽然，我们的delegation和我们贡献成反比，但是我们仍然坚持要成为最活跃的贡献者之一。  

## Contributors

* liangping

