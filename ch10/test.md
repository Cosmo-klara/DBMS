1. **在函数依赖的范围内**，能**使关系模式达到最大程度分离**的范式是 **BCNF**

2. 设关系模式R(U,F), U={A，B，C，D，E}, F={AB→C，BC→D，ACD→B，BE→C }, 则AB的属性闭包是 **ABCD**

3. **关系数据库的规范化**是为解决关系数据库的**存储异常**问题而引入的

4. 在关系模式 R 中，若**其函数依赖集中所有决定因素都是候选关键字(候选键)**，则 R 所属的最高范式使 **BCNF**

5. 在关系模式的规范化过程中，将 1NF 变为 3NF 需要消除**部分依赖和传递依赖**

6. 属于 **BCNF** 的关系模式并**没有完全消除存储异常**

    > BCNF 消除了整个关系模式的存储异常，但是数据之间可能存在多值依赖(1：n)也会引起存储异常，以及连接依赖也会引起存储异常

7. 关系模式规范化到 **BCNF 不一定能保持函数依赖**，但可以具有**无损连接性**

8. 某个关系模式**如果存在传递函数依赖，不一定存在部分函数依赖**，反之不然

9. 对于关系模式 R(U,F)，U 为属性集，F 为函数依赖集，X 为 U 的子集，如果 X 的属性闭包等于 U，则 X 为超键。
