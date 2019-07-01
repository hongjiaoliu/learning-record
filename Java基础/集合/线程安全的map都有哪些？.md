JAVA中线程安全的map有：Hashtable、synchronizedMap、ConcurrentHashMap。
java中map中线程安全怎么实现：
1. 同步的map就是Hashtable, concurrenthashmap。
2. 你看到的Hashtable就是直接在hashmap上加了个锁，concurrenthashmap就是分成多个分段锁。

> [参考](https://www.jianshu.com/p/533bb7cf8901)
