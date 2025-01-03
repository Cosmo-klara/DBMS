1. **事务的持久性**要求**对数据库的更新应当永久的反映在数据库中**

    > ACID：原子性、一致性、隔离性、持久性

    + 原子性：事务时数据库操作的基本逻辑单位，不可再分，事务中的所有操作要么执行要么都不执行。

    + 一致性：事务执行的结果在状态转移时保持一致，事务提交不成功时，需要撤销事务以保证数据库的一致性

    + 隔离性：一个事务的执行不影响另一个事务，也就是说一个事务的内部操作对外部事务是隔离的

    + 持久性：事务一旦提交成功，数据修改是永久的

2. 在具有检查点技术的数据库系统中，操作顺序：**写日志，写检查点记录，写数据库，写重新开始文件**

    > 写一个检查点系统需要做以下操作：(在写检查点过程中，不允许事务执行任何更新操作，如：写缓冲块和写日志记录)

    + 将日志缓冲区中的内容写入日志文件

    + 在日志文件中写入一个检查点记录

    + 将数据库缓冲区的内容写入数据库

    + 把检查点记录在日志文件中的地址写入重启动文件

    > 具有检查点的恢复步骤为：

    + 读取重启文件，获取最后一个检查点记录的地址

    + 得到检查点时刻的事务清单

    + 正向扫描日志文件，对已经完成的事务执行 Redo 操作，对未完成的事务执行 Undo 操作

3. 对日志文件设立检查点目的是为了**提高故障恢复的效率**，系统故障的恢复需要执行 **Redo和 Undo 操作**；事务日志记录了**事务处理过程中的重要事件**；

4. **介质故障**需要**后备副本**参与恢复

5. **事务内部的故障**需要**日志文件**

6. **原子性：事务中的所有操作要么执行要么都不执行**

7. 保持了**数据库一致性**的转储方式是**静态转储**

8. **当数据库系统将事务中对数据的更改完全写入磁盘时**，写入一条事务日志消息，标志着事务完成，事务进入提交状态。

+ 活动状态：事务执行时处于活动状态

+ 部分提交状态：事务的最后一条语句执行完毕，事务进入部分提交状态，结果数据还在内存中

+ 失败状态：事务不能正常执行，事务进入失败状态，需要回滚，撤销书屋已经做出的任何数据更改

+ 中止状态：事务回滚且数据库已经恢复到事务执行前的状态

9. 数据库恢复技术**不能确保**数据库状态一直是一致的

> 静态转储？

10. **检查点恢复技术**提高了恢复效率




