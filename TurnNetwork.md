# 全链游戏目前主流的解决方案分为两大类型：

## 一、从L2扩容入手
### 目的：是侧重于扩展链的性能和降低链上的交易手续费

- 主流的方案几乎都是采用以太坊L2层扩容方案
 
  - 直接部署为以太坊的分片链（L2）方式，利用以太坊主网作为主链（L1），安全由以太坊主网保证；
  
  比较典型的如Optimism、Arbitrum、Polygon：
  Optimism （OP Stack）
  Arbitrum （ Optimistic Rollup）

  两条链的主要技术区别在于 Optimism 使用单轮欺诈证明，而 Arbitrum 使用多轮欺诈证明。
  这意味着 Optimism 的方法速度更快，但由于 Gas 费更高，因此可能更昂贵，因为它是在 L1 上执行的。Arbitrum 的发放需要更多的时间，但更具成本效益。

  此外，Optimism 使用 EVM，而 Arbitrum 拥有自己的 Arbitrum 虚拟机（AVM）。这意味着 Optimism 的编程语言仅限于 Solidity。Arbitrum 支持所有 EVM 编程语言。

  Polygon zkEVM（其 EVM ZK-rollup 解决方案）
  Polygon 2.0 的概念是由 ZK 技术支持的各种 L2 链组成的生态系统，利用独特的跨链协调协议来实现 Polygon zkEVM、Polygon PoS 和超级网之间的无缝互操作性。
  
  - 另外一些则是完全自己建立主链和分片链（L1）和（L2）
  比如：TurnNetwork

## 二、从游戏引擎入手
### 目的则是侧重于降低游戏开发者和运营商门槛
- 其中最为典型的如MUD、dojo
  - MUD就是用Solidity语言开发的全链游戏引擎，由于其运行在EVM中，几乎可以用于所有兼容EVM的区块链网络中。
   - MUD甚至用Solidity封装了一个类似关系型数据库的框架（store库），开发人员只需要定义表和字段的形式，就能利用代码生成工具，生成对应的智能合约。
   - 客户端和链之间的自动数据同步，每个客户端都有一个内置的、与链上数据库实时同步的本地副本。这也是 MUD 提供的一个核心功能，帮开发者省去了自己管理复杂状态同步的繁重工作。
   - MUD还提供了一个World模块，解决了开发人员在Store库之上的逻辑和访问控制的问题，并且解决了智能合约的升级问题，这些都是一般应用开发框架都会提供的功能。
   - 如果抛开MUD基于ECS模型专门针对游戏应用方面的封装，则MUD完全可以胜任任何复杂的分布式应用（DAPP）的开发，是目前最为完善的Solidity/EVM的应用开发框架。
   - 
- dojo
  - 类似MUD
  
- World Engine
 - World Engine从传统游戏应用服务横向扩容的思路出发，把全链游戏业务服务的横向扩容跟链的分片结合起来，从而解决性能问题。
  - EVM Base Shard（EVM 分片）就是兼容EVM的区块链链。
 
  - Game Shard（游戏分片）则承载了游戏业务服务，Cardinal 是 World Engine 的第一个游戏分片实现。
    - 它使用 Entity-Component-System (ECS) 游戏架构，一个具有面向数据的架构。
    - 允许并行化游戏，并提高游戏计算的吞吐量。
    - 它有一个可配置的「Tick rate」，最高可达每秒 20 次 Tick。对于这里的区块链来说，那就是每秒 20 个区块。
    - 另外它还是自我索引的，无需外部索引器。
  - World Engine 可以根据需求定制这个分片。
    - 构建的分片，注入到基础分片中。
    - 只需要实现一套标准的接口，就像我们熟悉的 Cosmos，Cosmos 有一个 IBC 接口。
    - 我们基本上可以将这个整合成一个类似的规范，将自己的分片带到 World Engine 堆栈中。
    -  并且分片还可以通过地理定位，以减少延迟。
      -  比如，游戏可能有在美国的排序器，然后亚洲玩家必须等待 300 毫秒的延迟，才能让 transaction 到达排序器。这在游戏中是一个巨大的问题，因为 300 毫秒是很长的时间。

##三、TurnNetwork

TurnNetwork从TON的分片链的自动伸缩机制获得灵感，设计了两层紧耦合架构
- L1层主链能自动根据游戏运营商的需求自动创建L2层bubble网络，而bubble网络是一个完整的链，有点类似World Engine的EVM 分片，但bubble网络的生命周期的管理是由L1层的主链直接进行，游戏运营商通过在L1层主链上部署业务合约，并质押一定的Token，L1层主链就会自动创建对应的bubble网络，最为神奇的是，不仅可以创建bubble网络，还可以实现自动回收，并且还能根据业务使用情况，创建多个bubble网络，这是不是有点像k8s管理docker容器的功能，游戏运营商在L1层主链上部署的业务合约则有点像在编排bubble。
- L1层主链是一个具备完备共识（pos+bft）和经济模型的以太坊风格的侧链，兼容EVM，后续不排除兼容更多风格的智能合约和VM，比如wasm
- L1层主链和bubble网络通过跨链协议和多签方式实现双向通信，不仅打通了资产跨链能力，还具备数据跨链的能力

TurnNetwork自己本身可以作为以太坊网络L2层，采用ZK-rollup方案接入以太坊生态，目前是具备这种运营弹性的，这样可以让以太坊生态应用快速移植到TurnNetwork之上，而不需要任何成本
- 这样的架构就成为三层架构，L1：以太坊网络-L2：TurnNetwork-L3：bubble网络

TurnNetwork侧重于扩展链的性能和降低交易手续费，但跟Optimism、Arbitrum、Polygon的解决方案不同的地方是同时具备L2层的能力以外还管理了L3层的bubble网络
并且全连游戏引擎MUD可以直接部署在TurnNetwork，因为TurnNetwork兼容EVM，甚至World Engine的Game Shard（游戏分片）经过简单修改也能用于TurnNetwork，不排除后续TurnNetwork直接无缝支持World Engine的Game Shard（游戏分片）能力

bubble网络
- bubble网络才是TurnNetwork的精髓
- bubble网络是整个TurnNetwork自动伸缩，实现无限扩容的关键，而且bubble网络生命周期是短暂的，跟业务紧密相关
- bubble网络对比World Engine的EVM 分片来说，更具灵活性，并且是完全自动伸缩
- bubble网络还非常超前的提出移动节点的设想，这对于动不动就用最高性能服务器的发展氛围（如EOS）来说，确实是个另类
  - bubble网络的节点对于服务器要求很低，甚至可以是一部手机，也可以是一部笔记本电脑，当然也可以是一个具备公网ip的服务器
    - bubble的共识机制非常的轻量级，非常适合移动设备，网络层通过p2p打洞实现nat穿越后，可以在没有固定公网ip的设备上进行通信
    - 由于bubble网络的生命周期短暂，所以写入到硬盘的区块是有限的，当bubble网络回收的时候，这个bubble网络的区块数据会被备份到结构化的区块链存储中（如ipfs）
    - 

