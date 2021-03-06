# 图（Graph）

## 一、图的定义与术语

### 1、定义：

图是由顶点的有穷非空集合和顶点之间边的集合组成，通常表示为：*G(V,E)，其中，*G*表示一个图，*V*是图G中顶点的集合，*E*是图G中边的集合。

### 2、分类

图是按照方向分为无向图和有向图；按照边分为稀疏图和稠密图

### 3、基本术语







## 二、图的存储结构

图的结构比价复杂，任意两个顶点之间都可能存在关系，不能用简单的顺序存储结构来表示。如果运用多重链表，即一个数据域多个指针域组成的结点表示图中一个结点，则造成大量存储单元浪费。

### 1、邻接矩阵

邻接矩阵用两个数组保存数据。一个一维数组存储图中顶点信息，一个二维数组存储图中边或弧的信息

邻接矩阵适合表示**稠密图（Dense Graph）**，边较多。

![img](E:/black user/Java/有道云截图/1588993660175-ee1f720e-f62c-4c5d-9551-16d5f51e7668.jpeg)

### 2、邻接表

邻接表适合表示**稀疏图（Sparse Graph）**，边较少；

![img](E:/black user/Java/有道云截图/1588993909600-dee85dcc-f2e9-4412-8d9c-070bba27b5ae.jpeg)



# 三、

## 1、如何判断有环

对于无向图

- 求出图中所有顶点的度，
- 删除图中所有度<=1的顶点以及与该顶点相关的边，把与这些边相关的顶点的度减一
- 如果还有度<=1的顶点重复步骤2
- 最后如果还存在未被删除的顶点，则表示有环；否则没有环



对于有向图

- 计算图中所有点的**入度**，把入度为0的点加入栈
- 如果栈非空：
  - 取出栈顶顶点a，输出该顶点值，删除该顶点
  - 从图中删除所有以a为起始点的边；如果删除的边的另一个顶点入度为0，则把它入栈
- 如果图中还存在顶点，则表示图中存在环；否则输出的顶点就是一个拓扑排序序列





