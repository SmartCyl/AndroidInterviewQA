## █ Application 和 Activity 在 Context 的继承树上有何区别？二者使用上有什么不同？

![](https://i.loli.net/2020/11/17/edX6MgHzNFx7Pmj.png)



在继承树上的区别：

两者都是`ContextWrapper`的子类，`Application`是`ContextWrapper`的直接子类，而`Activity`是`ContextWrapper`的间接子类，`Activity`继承自`ContextThemeWrapper`，`ContextThemeWrapper`继承自`ContextWrapper`。



使用上的不同：

1. 两者的生命周期不同，`Activity`随着页面的销毁而销毁，`Application`随着整个应用的销毁而销毁
2. 跟 `UI` 相关的都用 `Activity`



## █ Activity 、Context 、Application 有什么区别？

Activity 和 Application 都是 Context 的子类，在 Activity 中，Context 就是 Activity，在 Application 中，Context 就是 Application。在 Activity 中 调用`getApplicationContext` 获取到的 Context 就是一个 Application 对象。



Activity 和 Application 的生命周期也是不一样的，Activity 表示一个界面的声明州区，Application 表示整个应用的生命周期。



## █ Context 是什么？一个应用有多少个 Context ？

Context 表示应用的上下文，可以通过 Context 获取到系统的服务、资源等，并且可以通过 Context 进行页面跳转。

一个应用 Context 数量 = Activity 数量 + Service 数量 + 1个 Application