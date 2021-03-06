# 请求/应答

使用`EventBus`来发布和响应请求/应答（Request/Reply）类事件。

常见的例子：你想要从在`EventBus`配置的分发器上执行的一个任务中收到应答。除了简单的发布/订阅模型外，`Reactor`的`EventBus`还提供多种事件处理模型。除了注册消费者之外，你也可以注册函数（`Function`），来让`EventBus`自动通知一个应答给函数返回值指定的键。在使用`.on()`和`.notify()`方法外，你也可以使用`.receive()`和`.send()`方法。

**请求/应答**

```
EventBus bus;

bus.on($("reply.sink"), ev -> {
  System.out.printf("Got %s on thread %s%n", ev, Thread.currentThread())
});     (1)

bus.receive($("job.sink"), ev -> {
  return doWork(ev);
});     (2)

bus.send("job.sink", Event.wrap("Hello World!", "reply.sink"));     (3)
```

1. 指定一个消费者来处理所有应答。
2. 指定一个方法在分发线程中执行任务并返回结果。
3. 使用给定的应答键在总线中发布一个事件。

如果你没有需要发布回复的一般主题时，你可以把请求和应答的操作使用`.sendAndReceive(Object, Event<?>, Consumer<Event<?>>)`方法合并到一次调用中。这将会执行`.send()`调用并在方法被调用时在分发线程中执行给定的应答回调。

**sendAndReceive()**

```
EventBus bus;

bus.receive($("job.sink"), (Event<String> ev) -> {
  return ev.getData().toUpperCase();
});     (1) 

bus.sendAndReceive(
    "job.sink",
   Event.wrap("Hello World!"),
   s -> System.out.printf("Got %s on thread %s%n", s, Thread.currentThread())
);      (2)
```

1. 分配一个方法用于在分发器线程中执行任务并返回结果。
2. 向总线中发布一个事件，并将方法执行结果作为入参来调度分发器中给定的消费者。