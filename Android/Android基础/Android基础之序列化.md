## █ Bundle 传递对象为什么需要序列化？Serializable 与 Parcelable 有什么区别？







Serializable：将类的所有信息全部记录下来，通过 ObjectOutputStream 写入到文件中，通过 IO 来操作，产生很多的临时变量，所以性能较低

Parcelable：只将变量记录到 Parcel 中，将变量转成字节的方式拼接在一起，反序列化的时候按照顺序读取出对应字节的信息，因为 write 和 read 的顺序必须一致。这些信息是保存在内存中的，因此性能高。