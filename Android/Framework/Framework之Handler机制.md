## █ Handler 原理

Handler 原理设计到四个角色，分别是 Handler、Message、MessageQueue 和 Looper。它们的关系是：

Handler 将 Message 发送到 MesageQueue，Looper 内部的死循环不断的从 MessageqQueue 中取出要执行的 Message，最终回调到了 Handler 的 handleMessage() 方法。

MessageQueue 是一个单链表结构，内部的消息根据开机时间的先后顺序排列，除非有异步消息会优先执行，否则消息都会根据时间的先后顺序依次被取出来执行。



## █ Handler 的 post(Runnble) 是怎么实现的？

## █ Callback 、Runnble 、msg 的执行优先级

## █ Handler 机制 Looper 从 MessageQueue 取完消息后会怎么样？

## █ loop.prepare() 做了什么？

## █ handler.sendEmptyMessageDelay() 和 handler.postDelayed() 的区别？

## █ MessageQueue 的数据结构

