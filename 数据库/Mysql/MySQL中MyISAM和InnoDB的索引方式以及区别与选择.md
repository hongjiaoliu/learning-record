
> [MySQL中MyISAM和InnoDB的索引方式以及区别与选择](https://blog.csdn.net/ljfphp/article/details/80029968)


MyISAM 存储引擎下的索引文件和数据文件是分离的，索引文件仅保存记录所在页的指针（物理位置），通过这些地址来读取页，进而读取被索引的行

与 MyISAM相同的一点是，InnoDB 也采用 B+Tree这种数据结构来实现 B-Tree索引

InnoDB 存储引擎采用“聚集索引”的数据存储方式实现B-Tree索引，所谓“聚集”，就是指数据行和相邻的键值紧凑地存储在一起
