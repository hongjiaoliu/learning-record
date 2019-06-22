
> [Redis学习第五课：Redis Set类型及操作](https://www.cnblogs.com/anny-1980/p/4583514.html)

Set是集合，它是string类型的无序集合。set是通过hash table实现的，添加、删除和查找的复杂度都是O(1)。

对集合我们可以取并集、交集、差集。通过这些操作我们可以实现SNS中的好友推荐和blog的tag功能。
