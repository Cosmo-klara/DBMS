#### SQL语言的主要功能分为哪几部分

数据定义, 数据操纵, 数据查询, 数据控制

#### 什么是基本表？什么是视图？视图和基本表有何异同

关系模式为基本表, 数据库中实际存储的是基本表，视图是建立在一个或多个基本表上的虚表,本身不存储在数据库中.

#### 什么是索引？什么是聚集索引？

索引是为了提高查询速度而对表字段附加的一种标识, 是单独的、物理的对数据库表中一列或多列的值进行排序的一种数据存储结构.

聚簇索引: 索引的顺序和数据库的物理存储顺序相同的

#### 试述视图的优点

+ 视图提供了逻辑数据独立性
+ 简化了用户视图
+ 视图使用户以不同角度看待相同的数
+ 视图提供了安全保护功能

#### 试述将SQL语言嵌入到主语言需要解决那些问题

+ 编译嵌入主语言的SQL语句成为可执行代码
+ 数据库和主语言程序间的通信
+ 数据库和主语言程序间的数据交换
+ 需要协调面向集合和面向记录两种不同的处理方式

#### 试述游标的概念及其作用

游标是系统为用户开设的一个数据缓冲区, 用来存放SQL语句的执行结果, 协调面向集合和面向记录两种不同的处理方式

