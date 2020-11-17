## █ Service 是否在主线程中运行？Service 中是否能执行耗时的操作？

默认情况下，Service 运行在当前应用所在进程的主线程中，也可以给 Service 指定进程

```xml
<service
	android:name="com.xx.XXService"
    android:enabled="true"
	android:process=":remote"/>
```

Service 运行在主线程，而主线程不能执行耗时的操作（网络/IO），因为 Service 中不能执行耗时的操作。



#### Service 的启动方式？

Service 启动方式有两种，分别是 `startService`和`bindService`。

`startService`：启动后 Activity 就与 Service 无关了

`bindService`：启动后 Activity 中能拿到 IBinder 对象，可以继续与 Service 通信



#### Service 的生命周期？

Service 的启动方式有两种，不同的启动方式，Service 的生命周期会有所不同。

- 只 `startService`

    `onCreate`-> `onStartCommand` -> `onDestroy`  （多次`startService`，只会多次执行 `onStartCommand`，不会执行多次 `onCreate`）

- 只 `bindService`

    `onCreate `-> `onBind `-> `onUnbind `-> `onDestroy`

- 先 `startService` 后 `bindService`

    `onCreate` -> `onStartCommand` -> `onBind` -> `onUndind` -> `onDestroy` （多次 `startService` 只重复执行 `onStartCommand`，多次 `bindService`不重复走生命周期方法）

- 先 `bindService` 后 `startService`

    `onCreate` -> `onBind` -> `onStartCommand` -> `onUnbind` -> `onDestroy`

如果一个 Service 被两种启动方式同时启动了，那么只有同时执行了 `stopService` 和 `unbindService`方法后，该 Service 才销毁。



#### 什么是 IntentService ？

IntentService 是 Service 的子类，它与 Service 不同的是，Service 是运行在主线程中的，而 IntentService 是运行在 子线程中的。IntentService 的 `onCreate` 方法中启动了一个线程 `HandlerThread`，并获取了 HandlerThread 这个子线程的 Looper，因此最终处理消息是在 Looper 所在的子线程中。在 onStart 中将消息发送出去，处理消息是在 IntentHandler 的内部类 ServiceHandler 中的 handleMessage 中，handleMessage 中调用了处理事件的抽象方法 onHandleIntent 方法（具体业务由子类实现），处理完成之后调用 stopSelf 方法终止自己，因此不需要开发者手动停止服务。





