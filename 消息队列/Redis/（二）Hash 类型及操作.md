
> [Redis学习第三课：Redis Hash类型及操作](https://www.cnblogs.com/anny-1980/p/4578753.html)

Redis hash是一个string类型的field和value的映射表。它的添加、删除操作都是O(1)(平均）。hash特别适用于存储对象。相较于对象的每个字段存在单个string类型。将一个对象存储在hash类型中会占用更小的内存，并且可以更方便的存取整个对象。
