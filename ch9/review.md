# 并发控制

## 并发事务运行存在的异常问题

> 事务并发执行可能会存取和存储不正确的数据，破坏事务的隔离性 ( I ) 和数据库的一致性 ( C )

事务是并发控制的基本单位，但并发操作不当，可能会出现事务间的相互干扰，使事务的 ACID 特性遭到破坏。可能产生的主要问题有如下三个：**丢失更新、不可重复读和读“脏”数据。**

###  丢失更新 ( Lost Update )

两者在相同状态更新同一条记录时，导致更新被其中一者的更新操作覆盖，导致他人的更新丢失。

### 不可重复读取 ( Non-Repeatable Read )

事务 A 读取数据后，事务 B 对同一数据进行更新，而当 A 再次读取数据时，数据的值不同.

避免方法：对读取状态的行加锁，防止被修改

> 幻读（phantom read）: 由于增删操作导致的读取数据整体不一致。  
避免方法：在增删操作完成数据处理前，任何事务不可增删数据

### 脏读 ( Read Dirty Data )

事务 A 读取到了**未提交的操作 ( 回滚 ) 的数据**，导致事务 A 读取的数据和数据库中数据不一致

避免方法： 不允许读取未提交的记录

## 并发调度的可串行性

### 可串行性调度

事务的并发执行可以有多种顺序，因而并发操作是随机的。我们把并发事务的操作顺序称之为**调度 ( schedule )**。

显然，事务执行的顺序和读写数据的顺序不同，对数据库造成的影响就可能不同，即不同的事务调度可能会产生不同的结果。

**可串行化调度：多个事务的并发执行是正确的，当且仅当并发执行的结果与这些事务按照某一串行顺序执行结果相同。可串行化时并发事务正确调度的准则。**

> 如果多个事务的一个并行调度执行时正确的，只要它是可串行化的

### 调度的冲突等价性

冲突操作：**不同的事务对同一个数据的读写操作和写写操作**

调度冲突：不同事务的操作对同一个数据进行操作，次序不同操作结果不同（至少一者为写）

**如果一个调度 S 能通过一系列非冲突操作执行顺序变化为调度S'，则称调度S'与S冲突等价**

### 调度的状态等价性

调度的目的是为了**保持数据库状态的一致性，如果初始数据库状态相同，等价的调度能产生同样的数据库状态，这种等价性称为数据库状态等价性，简称为状态等价性。**

**如果 S 与 S' 时同一个事务集合的不同调度，S 与 S' 等价，当且仅当：**

+ 对每个数据项 A，如果 S 中事务 T1 读 A 初值，则 S' 中事务 T1 也必须读 A 初值。

+ 如果 S 中事务 T1 执行 Read ( A )，则 S' 中事务 T1 也必须执行 Read ( A ), 并且如果 S 中 T1 读到的 A 值是由事务 T2 产生的，则 S' 中 T1 读到的 A 值也必须是由 T2 产生的。

+ 如果 S 中事务 T 执行最后的 Write ( A )，则 S' 中事务 T 也必须执行最后的 Write ( A )。

**我们称一个调度是状态可串行的，如果它的状态等价于一个串行调度。**

> **冲突可串行的调度一定是状态可串行的，反之不一定。**

如果调度 S 的**前趋图中包括回路**，则 S **不是冲突可串行**的

## 基于封锁的并发控制技术

其基本思想是：当事务 T 要修改记录 A, 如果修改 A 之前先给 A 加锁，使得 T2 和其它事务不能读取和修改 A, 直到 T 修改并写回 A 后解除对 A 的封锁为止。这样，既不会丢失事务 T 的更新，也不会出现读“脏”数据的问题。

### 锁

所谓封锁就是事务先对数据加锁后再对其执行操作。事务 T 加锁后就对该数据有了控制，而其它事务在事务 T 对该数据对象解除封锁之前，不能修改此数据对象。因此，**锁是数据项上的并发控制标志**。给数据加锁的方式有多种，基本的锁类型有两种：

+ #### 共享锁 ( Share Locks, 又称 S 锁 )

    > 又叫读锁

    如果事务 T 对数据 A 加上了 S 锁，则事务 T 可以读数据 A，不能写 A。

+ #### 排他锁 ( Exclusive Locks, 又称 X 锁 )

    > 又叫写锁

    如果事务 T 对数据 A 加上了 X 锁，则事务 T 可以读写数据 A。

在基于封锁的并发控制中要求每个事务都要根据对数据 A 的操作需求申请相应类型的锁。该请求发送给事务管理器，只有获得所需锁后，事务才能继续其操作。

是否能够获得锁是由锁的相容性决定的。当事务 T 对数据 A 加上共享锁后，其它事务只能再对 A 加共享锁，而不能加排它锁，直到 T 释放 A 上的共享锁。这可以保证在 T 释放 A 上的共享锁之前，事务 T 所读到的 A 的值是正确的。当事务 T 对数据 A 加上了排它锁后，其他任何事务都不能对 A 加上任何类型的锁，直到 T 释放 A 上的排它锁。这样，可以保证 T 释放 A 上的排它锁之前，其他事务都不能读写事务 T 所操作的数据 A。

锁的相容性：

![ ](./img/3-1.png)

锁的正确使用要保证：

+ 事务的一致性：事务只有已经申请了锁并且还没有释放锁时，才能读写该数据项；如果事务封锁某个数据项，它必须为该数据项解锁。

+ 调度的合法性：任何两个事务都不能使用不相容的锁封锁同一个数据项，除非一个事务已经为该数据项解锁。

### 封锁协议

在给数据对象加锁时，要考虑何时请求锁、持有锁的时间和何时释放等，要遵从一定规则。这些规则被称为封锁协议 ( Locking Protocol )。

1. 一级封锁协议

    事务 T 在修改数据 A 前必须先对其加 X 锁，直到**事务结束才释放**，可防止丢失更新，并保证事务 T 是可恢复的。

2. 二级封锁协议

    > 一级封锁协议解决了数据更新丢失的问题，但是无法解决脏读问题。

    在一级封锁协议的基础上，事务 T 在读取数据 A 之前必须先对其加 S 锁，**读取完毕后再释放**，可防止脏读。

3. 三级封锁协议

    > 二级封锁协议解决了脏读问题，但是无法解决不可重复读问题。因为第一次读和再读之间没有 S 锁保障。**三级封锁协议**能够**保证可串行化**

    在二级封锁协议的基础上，某一事务添加的 S 锁需要保持到**事务结束才释放**。

#### 活锁

#### 死锁

### 两阶段封锁协议

两阶段封锁协议(Two-Phase Locking，简称2PL)是最常用的一种封锁协议，理论上可以证明使用两段封锁协议产生的是可串行化调度

两阶段封锁协议指所有事务必须分两个阶段对数据项加锁和解锁：在对任何数据进行读、写操作之前，事务首先要获得对该数据的封锁；在释放一个封锁之后,事务不再获得任何其他封锁

两阶段封锁协议**不能防止死锁**，可以**保证并发事务调度的可串行化**，是可串行化的充分条件

“两阶段”锁的含义:事务分为两个阶段

+ 第一阶段是获得封锁，也称为扩展阶段。
在这阶段,事务可以申请获得任何数据项上的任何类型的锁，但是不能释放任何锁

+ 第二阶段是释放封锁，也称为收缩阶段。
在这阶段,事务可以释放任何数据项上的任何类型的锁，但是不能再申请任何锁。

> 一次封锁法遵守两阶段封锁协议

## 多粒度封锁

**封锁对象的大小称为封锁粒度**

多粒度封锁：**在一个系统中同时支持多种封锁粒度供不同的事务选择**

对一个结点加锁意味着这个结点的所有后裔结点也被加以同样类型的锁；在多粒度封锁中一个数据对象可能以两种方式封锁，封锁的效果是一样的

+ 显式封锁: 直接加到数据对象上的封锁
+ 隐式封锁: 由于其上级结点加锁而使该数据对象加上了锁

### 对某个数据对象加锁时系统检查的内容

该数据对象：

+ 有无显式封锁与之冲突

+ 所有上级结点

检查本事务的显式封锁是否与该数据对象上的隐式封锁冲突 ——由上级结点封锁造成的

+ 所有下级结点

检查本事务的隐式封锁是否与该数据对象上的显式封锁冲突——将加到下级结点的封锁

### 意向锁

引进意向锁目的：**提高对某个数据对象加锁时系统的检查效率**

#### 概念

+ 对任一结点加基本锁，必须先对它的上层结点加意向锁

+ 如果对一个结点加意向锁，则说明该结点的下层结点正在被加锁

#### 分类

+ 意向共享锁(Intent Share Lock，简称IS锁)
如果对一个数据对象加IS锁，表示它的后裔结点拟(意向)加S锁。例:要对某个元组加S锁，则要先对关系和数据库加IS锁
+ 意向排它锁(Intent Exclusive Lock，简称IX锁)
如果对一个数据对象加IX锁，表示它的后裔结点拟(意向)加X锁。例:要对某个元组加X锁，先要对关系和数据库加IX锁
+ 共享意向排它锁(Share Intent Exclusive Lock,简称SIX锁)
如果对一个数据对象加SIX锁，表示对它加S锁，再加IX锁，即SIX = S + IX。例：对某个表加SIX锁，则表示该事务要读整个表（所以要对该表加S锁），同时会更新个别元组（所以要对该表加IX锁）







