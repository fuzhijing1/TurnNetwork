# 1、白皮书里面强调了Turn重点解决的问题：
- 性能问题：通过横向扩容解决
- 手续费问题：如何解决？
- 游戏开发问题：其实把游戏业务跟分片链深度结合，既能解决性能问题，又可以解决游戏开发问题

![业务视图](selectbubble.png "业务视图")

- 比如游戏开发的时候，提供了一个solidity的API
  ```
  uint bubbleId = Bubble.selectBubble(size);
  ```
selectBubble这个方法非常灵活且强大
- 当游戏逻辑合约创建room1的时候，调用selectBubble方法，TurnNetwork会创建一个Bubble1网络，并把这个room1跟这个Bubble1网络绑定后，把Bubble1网络的信息返回给游戏客户端
- 如果有新用户进入游戏并创建room2的时候，调用selectBubble方法，TurnNetwork会判断Bubble1网络是否拥塞，如果拥塞，则会创建一个Bubble2网络，并把room2跟这个Bubble2网络绑定后，把Bubble2网络的信息返回给游戏客户端，如果Bubble1网络不拥塞，则会把room2跟Bubble1网络绑定并快速把Bubble1的信息返回给游戏客户端
- 以此类推，TurnNetwork可以根据参与游戏玩家数量以及业务拥塞的程度，自动横向扩容的方式解决性能问题，实现业务的弹性

  这是TurnNetwork的核心竞争力
