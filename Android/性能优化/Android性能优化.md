数据结构优化





内存优化





电量优化





网络优化





启动优化





Crash监控方案





ANR





APK瘦身



## █ 什么是 ANR ，如何避免？

ANR 即 Application Not Responding，应用未响应。应用在规定的时间内没有完成操作，再次接受到输入事件时就会弹出 ANR 提醒。如 Activity 在5秒内、BroadcastReceiver 在10秒内、Service 在20秒内没有完成操作时系统弹出该对话框。





## █ 如何监控线上 App 的性能问题？

## █ 5个G的数据，如何在500M的内存情况下实现排序？

## █ 大文件在传输过程中药考虑哪些问题？如何保证大文件的一致性？

## █ 内存泄漏是什么？怎么查找？

## █ 项目中做了哪些网络优化？

## █ 怎么分析内存泄漏

内存泄漏工具：

- MAT 内存分析工具
- AndroidStudio 的 Profiler
- LeakCanary



常见内存泄漏造成原因：

- 资源对象未关闭
- BroadcastReceiver、EventBus 等注册后未反注册
- 类的静态变量持有大数据对象
- 单例
- 非静态内部类
- Handler