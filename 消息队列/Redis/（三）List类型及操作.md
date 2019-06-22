
> [Redis学习第四课：Redis List类型及操作](https://www.cnblogs.com/anny-1980/p/4582635.html)

list是一个链表结构，主要功能是push、pop、获取一个范围的所有值等，操作中key理解为链表的名字。

Redis的list类型其实就是一个每个子元素都是string类型的双向链表。我们可以通过push、pop操作从链表的头部或者尾部添加删除元素，这样list既可以作为栈，又可以作为队列。
