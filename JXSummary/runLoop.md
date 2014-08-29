# NSRunLoop

runLoop 是线程相关的基础部分, 一个runLoop就是一个事件处理的循环, 用来不停的调度工作以及处理事件.

* 使用 runLoop的目的是让你的线程在有工作的时候忙于工作,而没工作的时候处于休眠状态。  
* runLoop还可以在loop在循环中的同时响应其他输入源，比如界面控件的按钮，手势等。

### 事件源

| runLoop事件来源         | 事件来源介绍                |
|------------------------|---------------------------|
| 输入源 ( input source ) | 输入源传递异步事件,通常消息来自于其他线程或程序。输入源的种类:基于端口的输入源和自定义输入源。|
| 定时源 ( timer source ) | 定时源则传递同步事件,发生在特定时间或者重复的时间间隔。|

### 详细介绍

**runLoop模式是所有要监视的输入源和定时源以及要通知的runLoop注册观察者的集合。可以将runLoop观察者和以下事件关联:**

* Run loop 入口
* Run loop 何时处理一个定时器
* Run loop 何时处理一个输入源
* Run loop 何时进入睡眠状态
* Run loop 何时被唤醒,但在唤醒之前要处理的事件
* Run loop 终止

**每次运行runLoop, 你线程的runLoop对会自动处理之前未处理的消息,并通知相关的观察者。具体的顺序如下:**

1. 通知观察者 Run loop 已经启动。
2. 通知观察者任何即将要开始的定时器。
3. 通知观察者任何即将启动的非基于端口的源。
4. 启动任何准备好的非基于端口的源。
5. 如果基于端口的源准备好并处于等待状态,立即启动;并进入步骤 9。
6. 通知观察者线程进入休眠。
7. 将线程置于休眠直到任一下面的事件发生:

   * 某一事件到达基于端口的源
   * 定时器启动
   * Run loop 设置的时间已经超时
   * Run loop 被显式唤醒

8. 通知观察者线程将被唤醒。

9. 处理未处理的事件

	* 如果用户定义的定时器启动,处理定时器事件并重启 Run loop。进入步骤 2。
   * 如果输入源启动,传递相应的消息。
   * 如果 Run loop 被显式唤醒而且时间还没超时,重启 Run loop，进入步骤 2。

10. 通知观察者 Run loop 结束。



### 学习总结

1. 每一个线程都有一个runLoop, 它处于一个循环中, 可以阻塞当前线程等待其他线程执行完毕, 再打开循环.. 这样可以保证多个线程进行同步执行. 
2. 每一个定时器的启动都需要打开runLoop, 由于主线程自动打开了runLoop, 所以不需要再开启. 而所有的子线程的runLoop都需要手动开启, 因此如果直接在子线程中使用定时器是无效的.. 必须通过`[[NSRunLoop currentRunLoop] run]`

**`Simple example`**

```
- (void)startRunLoop
{
    NSLog(@"Main Thread start");
    
    [NSThread detachNewThreadSelector:@selector(oneThread) toTarget:self withObject:nil];

    while (!_finished) {
        NSLog(@"Main Thread run.");
    
        [[NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode beforeDate:[NSDate distantFuture]];
    }
    
    NSLog(@"Main Thread end.");
}

- (void)oneThread
{
    NSLog(@"oneThread start");

    for (int i = 0; i <5 ;i++) {
        NSLog(@"InthreadProce2 count = %d.", i);
        sleep(1);
    }
    
    _finished = YES;
    
    NSLog(@"oneThread end");
}
```
### Learn Link

[RunLoop](http://blog.csdn.net/jjunjoe/article/details/8313016)

