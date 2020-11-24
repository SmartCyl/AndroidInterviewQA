## █ Handler 原理

Handler 原理设计到四个角色，分别是 Handler、Message、MessageQueue 和 Looper。它们的关系是：

Handler 将 Message 发送到 MesageQueue，Looper 内部的死循环不断的从 MessageqQueue 中取出要执行的 Message，最终回调到了 Handler 的 handleMessage() 方法。

MessageQueue 是一个单链表结构，内部的消息根据开机时间的先后顺序排列，除非有异步消息会优先执行，否则消息都会根据时间的先后顺序依次被取出来执行。



## █ Handler 的 post(Runnble) 是怎么实现的？

post() 调用 Handler 的 sendMessageDelayed() 方法，根据参数 Runnable 创建一个 Message 对象，发送一个延迟为0的延迟消息，最终调用到 Handler 的 enqueueMessage() 方法将消息按照时间先后顺序添加到 MessageQueue 中，然后通过 Looper 的 loop() 方法不断的从 MessageQueue 中取消息分发给 Handler 处理。



## █ Handler 是怎么做到线程切换的？

在主线程创建一个 Handler 对象，在子线程中用 handler 发送一个消息，因为 Looper.prepare() 是在主线程中执行的，所以 Looper 和 MessageQueue 都是在主线程中，在子线程中 handler 发送的消息都会存储到主线程的 MessageQueue 中，Looper.loop() 也是运行在主线程中不断的取消息，取出的消息分发给主线程的 handler 执行，这就完成了在子线程中发送消息却在主线程中执行的线程切换。



## █ Looper.loop() 里面是死循环，为什么不会卡死？

Android 系统是消息驱动的，Handler 发送的消息保存在 MessageQueue 中，只有 Looper.loop() 在不断的循环的取消息，才能让系统继续进行。主线程也是一个线程，不然一旦主线程中的代码执行完成，主线程就会结束，那么整个应用也就结束了，只有让主线程不结束那么应用就会一直运行。

Activity 的声明周期都是通过 handler 发送消息，然后 ActivityThread 的内部类 H 接受消息，并回调相关的生命周期函数。如果没有死循环，就无法不断的从 MessageQueue 中取消息，也就无法回调各种生命周期函数了。

从另一方面说，造成卡死的原因是因为在规定的时间内没有完成操作，再次有输入响应时，本次的响应就无法得到执行，这就造成了应用卡死状态。而在 Looper.loop() 的死循环中，当当前没有可执行的消息时，会调用 nativePollOnce() 函数让 CPU 休眠，有消息处理时就会将 CPU 唤醒，不会造成 CPU 的浪费。



## █ Loop.prepare() 做了什么？

判断当前线程是否已经创建了一个 Looper，如果已经有 Looper 了就抛出异常，否则就创建一个新的 Looper保存到 ThreadLocal 中。



## █ MessageQueue 是什么时候创建的？

在 loop.prepare() 中先判断了 Looper 是否创建，没有创建则先创建一个 Looper，在创建 Looper 时，在 Looper 的构造函数中创建了一个 MessageQueue。



## █ handler.sendEmptyMessageDelay() 和 handler.postDelayed() 的区别？

两者都会调用到 handler.sendMessageDelayed()，sendEmptyMessageDelay() 内部会根据 what 创建一个新的 Message，postDelayed() 根据参数 Runnable 创建一个带有 callback 的 Message。不一样的是最终在分发消息的时候，postDelayed() 因为给 Message 设置了 callback，在分发的时候优先给了 handleCallback()，事件处理是在传入参数的 Runnable 的 run() 方法中；而 sendEmptyMessageDelay() 最终事件处理是在 Hanlder 的 handleMessage() 方法中。

![](https://i.loli.net/2020/11/24/aPeKvqo1SCgbpfi.png)

