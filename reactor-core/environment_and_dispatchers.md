# Environment和Dispatcher
函数式构建模块已经准备完毕，让我们使用他们来玩转异步编程。第一步带领我们来到Dispatcher分区。

我们在启动任何Dispatcher之前，我们希望能高效的创建它们。通常创建Dispatcher的开销很大，需要预分配一个内存分区用于保持分配信号，这就是前言中介绍的著名的运行时分配和启动时预分配的不同对比。因此提出了一个名为`Environment`共享上下文的概念，使用它来管理这些不同类型的Dispatcher，从而避免不必要的创建开销。

