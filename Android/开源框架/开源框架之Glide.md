## █ 图片缓存怎么做？你会怎么设计一个图片缓存框架？



## █ Glide 有什么优点

- 使用简单，一般使用只需链式调用一句代码 `Glide.with(context).load(url).into(imageview)`
- 支持加载 gif 动态图片，甚至是 video
- 内存占用更小，就相对于 Picasso 而言，内存占用减少一半，Glide 默认使用 RGB_565 格式，而 Picasso 则使用 RGB_8888 格式
- 自动与生命周期绑定，无需手动设置
- 缓存更合理，Glide 会根据控件大小来决定图片的缓存大小，同一图片对于不同尺寸的控件来说会保存不同的缓存



## █ Glide 的缓存机制

Glide 缓存细分的话一共分为三级，分别为`活动缓存`、`内存缓存`、`磁盘缓存`，而`活动缓存`和`内存缓存`都是属于内存缓存的，因此 Glide 缓存又可以归纳为`内存缓存`、`磁盘缓存`这两级。



活动缓存是当前正在使用的图片的缓存，采用弱引用的方式存储在 Map 中，并且用一个整型的`acquired`变量来记录图片被使用的次数，每使用一次该值加一，释放一次该值减一，当该值为 0 时则表示该图片没有在使用了，就从 Map 中删除，并且将该图片添加到内存缓存中。



内存缓存是不同于活动缓存的一个缓存，它与活动缓存互斥，不再使用的图片会从活动缓存中删除保存到内存缓存中，如果再次使用则会从内存缓存中删除保存到活动缓存中。内存缓存采用`LruCache`最近最少使用算法，内部用`LinkedHashMap`实现。至于为什么用`LinkedHashMap`实现？因为需要根据不同的 Key 来取不同的图片，无疑是需要`HashMap`，但是`HashMap`是无序的，而缓存需要根据使用状态来决定缓存顺序，所以`LinkedHashMap`是最好的选择了。在初始化内存缓存时需要先设置一个最大大小，当添加一个新的图片到内存缓存时发现此时已经存不下了，就会把最近最少使用的图片给清理掉以扩大内存空间存放新的图片。



磁盘缓存缓存的是从网络或者是从本地文件中读取解码后的图片，再次使用的时候就不需要重新解码了，该级缓存节省了解码的步骤。



缓存的存储顺序：活动缓存 > 内存缓存 > 磁盘缓存

缓存的读取顺序：活动缓存 > 内存缓存 > 磁盘缓存



## █ Glide 是怎么绑定生命周期的？

在`Glide.with(context)`中调用了`getRetriever()`，该方法中初始化了 Glide，在初始化 Glide 之前调用了 GlideBuilder 的 build()，在 build() 方法中初始化了一个 RequestManagerRetriever 对象，在 RequestManagerRetriever 的构造函数中又初始化了 RequestManager 对象。初始化 RequestManager 时调用了 `LifeCycler.addListener()`，即 ActivityFragmentLifecycle 的 addListener()。在隐藏 Fragment（SupportRequestManagerFragment）的生命周期方法 onStart() 中调用了 ActivityFragmentLifecycle  的 onStart()，该 onStart() 中遍历所有添加到 Set 集合中的 LifecycleListener，调用到每个 LifecycleListener 的 onStart() 中，其它生命周期方法类似。