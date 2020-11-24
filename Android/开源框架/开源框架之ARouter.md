## █ ARouter 初始化做了什么事情？

在 ARouter 的 Init() 方法中调用了 _ARouter  的 init() 方法，在初始化过后接着调用了 _ARouter 的 afterInit() 方法做后续的工作。

在 _ARouter 的 init() 中调用了 `LogisticsCenter.init()`，并初始化了一个主线程的 Handler。关键代码都在 `LogisticsCenter.init()`方法中。在此方法中调用`ClassUtils.getFileNameByPackageName(context, packageName)`方法在子线程中扫描出第二个参数（包名）开头的类添加到 Set 集合中，然后遍历 Set 集合调用对应接口的 `loadInto()`方法，这些接口的`loadInto`方法的具体实现在编译器自动生成的类中。在这些自动生成的类中，将对应组的路由信息添加到`Warehouse`类中的静态 Map 中。

## █  ARouter 是怎么启动 Activity 或者 Fragment 的？

在编译期，注解处理器会获取到 `@Router`注解中的类相关信息 Element，然后遍历所有的 Element，检查注解使用的合法性，然后用 JavePoet 生成相关的路由类，这些类中保存着跳转信息的相关信息。这些信息根据模块名分组，每个模块中加上`@Router`注解的类被添加到对应的组中，每个组中保存着该组中类的路由信息。

在运行期 ，ARouter 初始化的时候遍历 dex 文件中所有的类过滤出包名以`com.alibaba.android.arouter.routes`开头的类添加到 Set 集合中，然后遍历该 Set 集合，然后调用对应类的`loadInto()`方法，将路由信息添加到`Warehouse`类中的静态`Map`中。在路由跳转时，`build()`方法根据传入的 path 提取出 group，并封装成 `Postcard` 对象，`navigation()`方法中，从静态 Map 中根据之前封装到 Postcard 中的 path 查询出 `RouteMeta`，然后将要跳转的类信息设置给 Postcard 对象，最终调用到了 `_ARouter`类的`_navigation()`方法，在该方法中根据`Postcard`对象中的信息用 Intent 进行跳转。

![](https://i.loli.net/2020/11/18/WVv3hcUkQJP5lSu.png)

