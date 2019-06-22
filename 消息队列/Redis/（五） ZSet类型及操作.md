
> [Redis学习第六课：Redis ZSet类型及操作](https://www.cnblogs.com/anny-1980/p/4583806.html)

Sorted set是set的一个升级版本，它在set的基础上增加了一个顺序属性，这一属性在添加修改元素时候可以指定，每次指定后，zset会自动重新按新的值调整顺序。可以理解为有两列字段的数据表，一列存value,一列存顺序编号。操作中key理解为zset的名字。
