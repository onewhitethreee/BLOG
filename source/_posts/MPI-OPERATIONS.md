---
uuid: dc489550-dadc-11f0-9076-23736bdd8346
title: MPI_OPERATIONS
date: 2025-12-17 01:10:57
tags:
---

# C 中使用 MPI

只需要一行头文件：

```c
#include <mpi.h>
```

---

## 编译方式

**不用 gcc，用 mpicc：**

```bash
mpicc program.c -o program
```

---

## 运行方式

```bash
mpirun -np 4 ./program   # 用4个进程运行
```

---

## 最简示例

```c
#include <mpi.h>
#include <stdio.h>

int main(int argc, char *argv[]) {
    MPI_Init(&argc, &argv);
    
    int rank;
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);
    
    printf("Hello from process %d\n", rank);
    
    MPI_Finalize();
    return 0;
}
```

编译运行：
```bash
mpicc hello.c -o hello
mpirun -np 4 ./hello
```

# MPI_Comm_size 和 MPI_Comm_rank

## 简单解释

| 函数 | 作用 |
|------|------|
| `MPI_Comm_size` | 获取**总共有多少个进程** |
| `MPI_Comm_rank` | 获取**我是第几个进程** |

---

## 举例说明

假设运行 `mpirun -np 4 ./program`（4个进程）：

```c
MPI_Comm_size(MPI_COMM_WORLD, &numprocs);  // numprocs = 4
MPI_Comm_rank(MPI_COMM_WORLD, &rank);      // rank = 0, 1, 2, 3（每个进程不同）
```

| 进程 | numprocs | rank |
|------|----------|------|
| 进程0 | 4 | 0 |
| 进程1 | 4 | 1 |
| 进程2 | 4 | 2 |
| 进程3 | 4 | 3 |

---

## 用途

```c
if (rank == 0) {
    // 只有主进程执行
    printf("我是老大\n");
} else {
    // 其他进程执行
    printf("我是worker %d\n", rank);
}
```

**rank** 用来区分不同进程，让它们干不同的活。

# MPI_COMM_WORLD

## 一句话解释

**包含所有进程的"群聊"**

---

## 类比理解

把它想象成微信群：

| 概念 | 类比 |
|------|------|
| MPI_COMM_WORLD | 包含所有人的大群 |
| rank | 你在群里的编号 |
| size | 群里总人数 |

---

## 为什么需要它

MPI 的所有通信都要指定"在哪个群里"：

```c
// 在"全体群"里获取信息
MPI_Comm_size(MPI_COMM_WORLD, &size);
MPI_Comm_rank(MPI_COMM_WORLD, &rank);

// 在"全体群"里发消息
MPI_Send(data, ..., MPI_COMM_WORLD);
```

---

## 简单理解

**90%的情况下，直接用 MPI_COMM_WORLD 就行**，不用想太多。

它就是"所有进程"的意思。


# MPI_Barrier

## 一句话解释

**让所有进程在这里等，等齐了再一起往下走**

---

## 类比

就像旅游团的集合点：

- 跑得快的人先到，**必须等**
- 跑得慢的人后到
- **全到齐了**，大家再一起出发

---

## 例子

```c
printf("进程 %d 到达\n", rank);

MPI_Barrier(MPI_COMM_WORLD);  // 全员等待

printf("进程 %d 继续执行\n", rank);
```

**输出：**
```
进程 2 到达
进程 0 到达
进程 1 到达
进程 3 到达
（等所有人都到达后）
进程 0 继续执行
进程 1 继续执行
...
```

---

## 常见用途

- 计时前同步
- 确保某阶段全部完成再进入下一阶段

# MPI_Bcast

## 一句话解释

**一个进程把数据广播给所有其他进程**

---

## 类比

群主发群公告，所有人都收到同样的内容。

---

## 例子

```c
int data;

if (rank == 0) {
    data = 100;  // 只有进程0有数据
}

MPI_Bcast(&data, 1, MPI_INT, 0, MPI_COMM_WORLD);
// 从进程0广播给所有人

// 现在所有进程的 data 都是 100
```

---

## 参数解释

```c
MPI_Bcast(&data, 1, MPI_INT, 0, MPI_COMM_WORLD);
//        数据   数量  类型  谁发   在哪个群
```

| 参数 | 含义 |
|------|------|
| `&data` | 要广播的数据 |
| `1` | 数据个数 |
| `MPI_INT` | 数据类型 |
| `0` | **谁是发送者**（root进程） |
| `MPI_COMM_WORLD` | 通信范围 |

---

## 图示

```
广播前：        广播后：
进程0: 100      进程0: 100
进程1: ?   -->  进程1: 100
进程2: ?        进程2: 100
进程3: ?        进程3: 100
```


# MPI_Scatter

## 一句话解释

**一个进程把数据切开，分别发给每个进程**

---

## 类比

发牌：一副牌分给每个玩家，每人拿一部分。

---

## 图示

```
分发前：                分发后：
进程0: [A,B,C,D]        进程0: A
进程1: ?           -->  进程1: B
进程2: ?                进程2: C
进程3: ?                进程3: D
```

---

## 例子

```c
int send_buf[4] = {10, 20, 30, 40};  // 只在进程0有
int recv_data;  // 每人收一个

MPI_Scatter(send_buf, 1, MPI_INT,   // 发：每份1个
            &recv_data, 1, MPI_INT, // 收：每人收1个
            0, MPI_COMM_WORLD);     // 从进程0发

// 结果：进程0收到10，进程1收到20，进程2收到30...
```

---

## 对比 Bcast

| 操作 | 效果 |
|------|------|
| Bcast | 所有人收到**相同**数据 |
| Scatter | 每人收到**不同**的一块 |


# MPI_Gather

## 一句话解释

**每个进程把数据交给一个进程，汇总到一起**

---

## 类比

收作业：老师把每个学生的作业收上来，汇成一摞。

---

## 图示

```
收集前：                收集后：
进程0: A                进程0: [A,B,C,D]
进程1: B           -->  进程1: ?
进程2: C                进程2: ?
进程3: D                进程3: ?
```

---

## 例子

```c
int send_data = rank * 10;  // 每人有自己的数据
int recv_buf[4];            // 只在进程0有意义

MPI_Gather(&send_data, 1, MPI_INT,  // 发：每人发1个
           recv_buf, 1, MPI_INT,    // 收：每份收1个
           0, MPI_COMM_WORLD);      // 收到进程0

// 结果：进程0的recv_buf = [0, 10, 20, 30]
```

---

## 对比

| 操作 | 方向 | 效果 |
|------|------|------|
| Scatter | 1 → 多 | 切开分发 |
| **Gather** | 多 → 1 | 汇总收集 |

---

## 记忆

**Scatter 和 Gather 是反操作**

```
Scatter: 发牌
Gather:  收牌
```

# MPI_Allgather

## 一句话解释

**每个进程都收集到所有人的数据**

---

## 类比

交换名片：每人把自己的名片发给所有人，最后人人都有一套完整名片。

---

## 图示

```
收集前：                收集后：
进程0: A                进程0: [A,B,C,D]
进程1: B           -->  进程1: [A,B,C,D]
进程2: C                进程2: [A,B,C,D]
进程3: D                进程3: [A,B,C,D]
```

---

## 例子

```c
int send_data = rank * 10;  // 每人有自己的数据
int recv_buf[4];            // 每人都要收

MPI_Allgather(&send_data, 1, MPI_INT,  // 发
              recv_buf, 1, MPI_INT,    // 收
              MPI_COMM_WORLD);         // 没有root！

// 结果：所有进程的 recv_buf 都是 [0, 10, 20, 30]
```

---

## 对比

| 操作 | 结果在谁那 |
|------|-----------|
| Gather | 只有root有完整数据 |
| **Allgather** | **人人都有**完整数据 |

---

## 记忆

```
Gather   = 收集到一人
Allgather = 收集到所有人
```

**All = 所有人都得到结果**


# MPI_Alltoall

## 一句话解释

**每个进程给每个进程发一份数据，全员互换**

---

## 类比

交换礼物：每人准备n份礼物，分别送给每个人，同时也收到每人送来的礼物。

---

## 图示

```
交换前：                    交换后：
进程0: [A0,A1,A2,A3]        进程0: [A0,B0,C0,D0]
进程1: [B0,B1,B2,B3]   -->  进程1: [A1,B1,C1,D1]
进程2: [C0,C1,C2,C3]        进程2: [A2,B2,C2,D2]
进程3: [D0,D1,D2,D3]        进程3: [A3,B3,C3,D3]
```

每人的第i份 → 发给进程i

---

## 例子

```c
int send_buf[4] = {rank*10, rank*10+1, rank*10+2, rank*10+3};
int recv_buf[4];

MPI_Alltoall(send_buf, 1, MPI_INT,  // 发：给每人1个
             recv_buf, 1, MPI_INT,  // 收：从每人收1个
             MPI_COMM_WORLD);

// 进程0收到：[0, 10, 20, 30]（每人的第0份）
// 进程1收到：[1, 11, 21, 31]（每人的第1份）
```

---

## 对比

| 操作 | 模式 |
|------|------|
| Gather | 多 → 1 |
| Allgather | 多 → 所有 |
| **Alltoall** | **所有 ↔ 所有** |

---

## 记忆

```
Alltoall = 矩阵转置
行变列，列变行
```

# MPI_Reduce

## 一句话解释

**每个进程的数据做运算，结果汇总到一个进程**

---

## 类比

统计总分：每人报自己的分数，老师加起来得到总分。

---

## 图示

```
规约前：                规约后（求和）：
进程0: 1                进程0: 10 (=1+2+3+4)
进程1: 2           -->  进程1: ?
进程2: 3                进程2: ?
进程3: 4                进程3: ?
```

---

## 例子

```c
int my_data = rank + 1;  // 进程0是1，进程1是2...
int result;

MPI_Reduce(&my_data, &result, 1, MPI_INT,
           MPI_SUM,              // 操作：求和
           0, MPI_COMM_WORLD);   // 结果在进程0

// 进程0的result = 1+2+3+4 = 10
```

---

## 常用操作

| 操作 | 含义 |
|------|------|
| MPI_SUM | 求和 |
| MPI_MAX | 最大值 |
| MPI_MIN | 最小值 |
| MPI_PROD | 乘积 |

---

## 对比

| 操作 | 作用 |
|------|------|
| Gather | 收集数据（不运算） |
| **Reduce** | 收集并**运算** |

---

## 记忆

```
Gather = 收作业
Reduce = 收作业 + 算总分
```

# MPI_Allreduce

## 一句话解释

**每个进程的数据做运算，结果人人都有**

---

## 类比

统计总分：每人报分数，加起来后，**每人都知道**总分是多少。

---

## 图示

```
规约前：                规约后（求和）：
进程0: 1                进程0: 10
进程1: 2           -->  进程1: 10
进程2: 3                进程2: 10
进程3: 4                进程3: 10
```

---

## 例子

```c
int my_data = rank + 1;
int result;

MPI_Allreduce(&my_data, &result, 1, MPI_INT,
              MPI_SUM,           // 求和
              MPI_COMM_WORLD);   // 没有root！

// 所有进程的result都是10
```

---

## 对比

| 操作 | 结果在谁那 |
|------|-----------|
| Reduce | 只有root有 |
| **Allreduce** | **人人都有** |

---

## 记忆

```
Reduce    = 算完给老师
Allreduce = 算完给所有人

All = 所有人都得到结果
```

---

## 常见用途

- 判断全局收敛
- 计算全局最大/最小值
- 统计全局总和
# MPI_Scan

## 一句话解释

**前缀运算，每个进程得到"自己及之前所有进程"的累计结果**

---

## 类比

报数累加：每人报出从第一个人到自己的总和。

---

## 图示

```
扫描前：                扫描后（求和）：
进程0: 1                进程0: 1        (=1)
进程1: 2           -->  进程1: 3        (=1+2)
进程2: 3                进程2: 6        (=1+2+3)
进程3: 4                进程3: 10       (=1+2+3+4)
```

---

## 例子

```c
int my_data = rank + 1;
int result;

MPI_Scan(&my_data, &result, 1, MPI_INT,
         MPI_SUM, MPI_COMM_WORLD);

// 进程0: result=1
// 进程1: result=3
// 进程2: result=6
// 进程3: result=10
```

---

## 对比

| 操作 | 结果 |
|------|------|
| Reduce | 只有root有总和 |
| Allreduce | 人人有总和 |
| **Scan** | **每人有前缀和** |

---

## 记忆

```
Scan = 扫描累加

进程i得到: 进程0 + 进程1 + ... + 进程i
```

---

## 常见用途

- 并行前缀和
- 分配全局编号
- 负载均衡计算偏移量

# MPI_Finalize 和 MPI_Finalized

---

## MPI_Finalize

### 一句话解释

**结束MPI，清理资源**

### 用法

```c
MPI_Finalize();
```

### 注意

- 调用后不能再用任何MPI函数
- 每个进程都要调用
- 程序结尾必须调用

---

## MPI_Finalized

### 一句话解释

**查询MPI是否已结束**

### 用法

```c
int flag;
MPI_Finalized(&flag);

if (flag) {
    // MPI已结束
} else {
    // MPI还在运行
}
```

---

## 对比

| 函数 | 作用 |
|------|------|
| MPI_Init | 开始MPI |
| MPI_Initialized | 查是否开始了 |
| **MPI_Finalize** | **结束MPI** |
| **MPI_Finalized** | **查是否结束了** |

---

## 典型程序结构

```c
int main() {
    MPI_Init(NULL, NULL);
    
    // ... MPI代码 ...
    
    MPI_Finalize();  // 必须调用
    return 0;
}
```

---

## 记忆

```
Init      = 开门
Finalize  = 关门

Initialized = 门开了吗？
Finalized   = 门关了吗？
```

# MPI_Send 和 MPI_Recv

---

## 一句话解释

**Send发送数据，Recv接收数据**

---

## 类比

寄信：
- Send = 寄出信
- Recv = 收到信

---

## 图示

```
进程0                    进程1
  |                        |
  |------- 数据 --------->|
  Send                   Recv
```

---

## 用法

### MPI_Send（发送）

```c
MPI_Send(
    &data,      // 发什么
    count,      // 几个
    MPI_INT,    // 什么类型
    dest,       // 发给谁（目标进程号）
    tag,        // 标签（信封标记）
    MPI_COMM_WORLD
);
```

### MPI_Recv（接收）

```c
MPI_Recv(
    &data,      // 收到哪
    count,      // 最多收几个
    MPI_INT,    // 什么类型
    source,     // 从谁收（源进程号）
    tag,        // 标签（要匹配）
    MPI_COMM_WORLD,
    &status     // 状态信息
);
```

---

## 完整例子

```c
int data;

if (rank == 0) {
    data = 100;
    MPI_Send(&data, 1, MPI_INT, 1, 0, MPI_COMM_WORLD);
} 
else if (rank == 1) {
    MPI_Recv(&data, 1, MPI_INT, 0, 0, MPI_COMM_WORLD, &status);
    // data现在是100
}
```

---

## 重要参数

| 参数 | 说明 |
|------|------|
| dest/source | 对方进程号 |
| tag | 必须匹配 |
| MPI_ANY_SOURCE | 接收任意来源 |
| MPI_ANY_TAG | 接收任意标签 |

---

## 注意

- **阻塞**：Send/Recv完成前不返回
- **匹配**：source、dest、tag要对应
- **死锁**：两个进程互相等对方会卡住

---

## 记忆

```
Send = 寄信（目的地 + 标签）
Recv = 收信（来源 + 标签）

标签要匹配，否则收不到
```

# MPI 中有阻塞和非阻塞两种

---

## 阻塞（Blocking）- 等完才继续

```c
MPI_Send()      // 发送完才返回
MPI_Recv()      // 收到才返回
MPI_Bcast()     // 广播完才返回
MPI_Reduce()    // 规约完才返回
```

**必须等操作完成，程序才往下走。**

---

## 非阻塞（Non-blocking）- 不等，继续干别的

```c
MPI_Isend()     // I = Immediate，立即返回
MPI_Irecv()
MPI_Ibcast()
MPI_Ireduce()
```

**发起请求就返回，可以边算边通信。**

---

## 简单对比

| 类型 | 函数名 | 特点 |
|------|--------|------|
| 阻塞 | MPI_Send | 等待完成 |
| 非阻塞 | MPI_Isend | 立即返回，需要 MPI_Wait |

---

## 非阻塞需要配合 Wait

```c
MPI_Request req;
MPI_Isend(&data, 1, MPI_INT, 1, 0, MPI_COMM_WORLD, &req);

// 这里可以做其他计算...

MPI_Wait(&req, &status);  // 最后等待完成
```

---

## 你代码里用的都是阻塞的

```c
MPI_Bcast()   // 阻塞
MPI_Reduce()  // 阻塞
```

**大多数情况用阻塞就够了，简单安全。**

# MPI 考试复习要点

---

## 1. 基础函数（必考）

```c
MPI_Init(&argc, &argv);      // 初始化
MPI_Finalize();              // 结束
MPI_Comm_rank(comm, &rank);  // 获取进程号
MPI_Comm_size(comm, &size);  // 获取总进程数
```

---

## 2. 点对点通信

```c
MPI_Send(&data, count, type, dest, tag, comm);
MPI_Recv(&data, count, type, source, tag, comm, &status);
```

**注意：Send 和 Recv 必须配对，否则死锁！**

---

## 3. 集合通信（重要）

| 函数 | 作用 |
|------|------|
| MPI_Bcast | 一对多广播 |
| MPI_Reduce | 多对一规约 |
| MPI_Allreduce | 规约后所有进程都有结果 |
| MPI_Scatter | 一对多分发（每人不同数据） |
| MPI_Gather | 多对一收集 |
| MPI_Barrier | 同步，等所有进程到达 |

---

## 4. 常用数据类型

| MPI类型 | C类型 |
|---------|-------|
| MPI_INT | int |
| MPI_DOUBLE | double |
| MPI_FLOAT | float |
| MPI_CHAR | char |

---

## 5. 常用规约操作

| 操作 | 含义 |
|------|------|
| MPI_SUM | 求和 |
| MPI_MAX | 最大值 |
| MPI_MIN | 最小值 |
| MPI_PROD | 乘积 |

---

## 6. 死锁问题（常考）

```c
// ❌ 死锁：都在等对方
if (rank == 0) {
    MPI_Recv(...);  // 等进程1发
    MPI_Send(...);
} else {
    MPI_Recv(...);  // 等进程0发
    MPI_Send(...);
}
```

```c
// ✅ 正确：一个先发一个先收
if (rank == 0) {
    MPI_Send(...);
    MPI_Recv(...);
} else {
    MPI_Recv(...);
    MPI_Send(...);
}
```

---

## 7. 运行命令

```bash
mpicc -o prog prog.c      # 编译
mpirun -np 4 ./prog       # 用4个进程运行
```

---

## 8. 并行计算加速比

```
加速比 = 串行时间 / 并行时间
理想情况：用 n 个进程，加速 n 倍
```

---

## 9. Reduce vs Allreduce

```c
MPI_Reduce()    // 结果只在 root 进程
MPI_Allreduce() // 结果所有进程都有
```

---

## 10. 常见考题类型

1. **填空**：补全 MPI 函数参数
2. **改错**：找死锁、类型不匹配
3. **编程**：并行求和、求π、矩阵运算
4. **分析**：计算加速比、通信开销

---

还有什么具体想深入的吗？


# 常见 MPI 考题类型详解

---

## 1. 填空题

**题目形式**：给不完整代码，补全参数

```c
// 把 n 从进程0广播给所有进程，填空
MPI_Bcast(&n, ___, ___, ___, MPI_COMM_WORLD);
```

**答案**：
```c
MPI_Bcast(&n, 1, MPI_INT, 0, MPI_COMM_WORLD);
//            ↑    ↑      ↑
//          数量  类型   root进程
```

---

## 2. 改错题

**题目形式**：给有 bug 的代码，找出并修正

```c
// 找出错误
int n = 100;
MPI_Bcast(&n, 1, MPI_DOUBLE, 0, MPI_COMM_WORLD);
```

**答案**：
```c
// n 是 int，应该用 MPI_INT
MPI_Bcast(&n, 1, MPI_INT, 0, MPI_COMM_WORLD);
```

**常见错误类型**：
- 类型不匹配
- 死锁
- 忘记 Init/Finalize
- Send/Recv 不配对

---

## 3. 编程题

**题目形式**：写完整并行程序

**例1：并行求数组和**
```c
// 4个进程并行求 a[0]+a[1]+...+a[99] 的和
```

**答案思路**：
```c
// 每个进程算一部分
int local_sum = 0;
for (i = rank * 25; i < (rank + 1) * 25; i++) {
    local_sum += a[i];
}
// 汇总
MPI_Reduce(&local_sum, &total, 1, MPI_INT, MPI_SUM, 0, MPI_COMM_WORLD);
```

---

**例2：并行求 π**（你代码里那个）

```c
// 用梯形法则并行计算 π
```

---

## 4. 分析题

**题目形式**：计算或分析性能

**例1：计算加速比**
```
串行程序运行时间：8秒
用4个进程并行：3秒
求加速比
```

**答案**：
```
加速比 = 8 / 3 = 2.67
```

---

**例2：分析通信开销**
```
程序中调用了多少次通信？
哪些可以优化？
```

---

## 总结表格

| 题型 | 考什么 | 难度 |
|------|--------|------|
| 填空 | 记住函数参数 | ⭐ |
| 改错 | 理解常见错误 | ⭐⭐ |
| 编程 | 会写并行程序 | ⭐⭐⭐ |
| 分析 | 理解并行原理 | ⭐⭐ |

---

需要我出几道练习题给你做吗？


# MPI 练习题

---

## 一、填空题（每空 2 分）

### 第 1 题
```c
// 进程0把数组data广播给所有进程
double data[100];
MPI_Bcast(_____, _____, _____, _____, MPI_COMM_WORLD);
```

---

### 第 2 题
```c
// 把所有进程的 local_sum 求和，结果存到进程0的 total 中
int local_sum, total;
MPI_Reduce(&local_sum, &total, _____, _____, _____, _____, MPI_COMM_WORLD);
```

---

### 第 3 题
```c
// 获取当前进程的编号和总进程数
int rank, size;
MPI_Comm_rank(_____, _____);
MPI_Comm_size(_____, _____);
```

---

## 二、改错题（每题 5 分）

### 第 4 题
找出下面代码的错误并改正：
```c
double x = 3.14;
MPI_Bcast(&x, 1, MPI_INT, 0, MPI_COMM_WORLD);
```

---

### 第 5 题
下面代码会死锁，请改正：
```c
if (rank == 0) {
    MPI_Recv(&data, 1, MPI_INT, 1, 0, MPI_COMM_WORLD, &status);
    MPI_Send(&data, 1, MPI_INT, 1, 0, MPI_COMM_WORLD);
} else if (rank == 1) {
    MPI_Recv(&data, 1, MPI_INT, 0, 0, MPI_COMM_WORLD, &status);
    MPI_Send(&data, 1, MPI_INT, 0, 0, MPI_COMM_WORLD);
}
```

---

### 第 6 题
找出错误：
```c
int main() {
    int rank;
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);
    printf("I am process %d\n", rank);
    MPI_Finalize();
    return 0;
}
```

---

## 三、编程题

### 第 7 题（10 分）
用 4 个进程并行计算数组求和：
```
数组 a[100] = {1, 2, 3, ..., 100}
每个进程计算 25 个元素的和
最后把结果汇总到进程 0
```

写出核心代码。

---

### 第 8 题（10 分）
用 MPI 实现并行求最大值：
- 每个进程有一个局部变量 `local_max`
- 找出所有进程中的最大值
- 结果存到进程 0

---

## 四、分析题

### 第 9 题（5 分）
```
串行程序运行 12 秒
用 4 个进程并行运行 4 秒
用 8 个进程并行运行 2.5 秒

(1) 计算 4 进程和 8 进程的加速比
(2) 哪个效率更高？为什么？
```

---

### 第 10 题（5 分）
解释 `MPI_Reduce` 和 `MPI_Allreduce` 的区别，各在什么场景下使用？

---

---

# 参考答案

<details>
<summary>点击展开答案</summary>

## 填空题答案

**第 1 题**
```c
MPI_Bcast(data, 100, MPI_DOUBLE, 0, MPI_COMM_WORLD);
```

**第 2 题**
```c
MPI_Reduce(&local_sum, &total, 1, MPI_INT, MPI_SUM, 0, MPI_COMM_WORLD);
```

**第 3 题**
```c
MPI_Comm_rank(MPI_COMM_WORLD, &rank);
MPI_Comm_size(MPI_COMM_WORLD, &size);
```

---

## 改错题答案

**第 4 题**
```c
// x 是 double，应该用 MPI_DOUBLE
MPI_Bcast(&x, 1, MPI_DOUBLE, 0, MPI_COMM_WORLD);
```

**第 5 题**
```c
// 一个先发，一个先收
if (rank == 0) {
    MPI_Send(&data, 1, MPI_INT, 1, 0, MPI_COMM_WORLD);
    MPI_Recv(&data, 1, MPI_INT, 1, 0, MPI_COMM_WORLD, &status);
} else if (rank == 1) {
    MPI_Recv(&data, 1, MPI_INT, 0, 0, MPI_COMM_WORLD, &status);
    MPI_Send(&data, 1, MPI_INT, 0, 0, MPI_COMM_WORLD);
}
```

**第 6 题**
```c
// 缺少 MPI_Init
int main(int argc, char *argv[]) {
    MPI_Init(&argc, &argv);  // 加上这行
    int rank;
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);
    printf("I am process %d\n", rank);
    MPI_Finalize();
    return 0;
}
```

---

## 编程题答案

**第 7 题**
```c
int a[100];
int rank, size;
int local_sum = 0, total = 0;

MPI_Comm_rank(MPI_COMM_WORLD, &rank);
MPI_Comm_size(MPI_COMM_WORLD, &size);

// 初始化数组（进程0做）
if (rank == 0) {
    for (int i = 0; i < 100; i++) a[i] = i + 1;
}

// 广播数组
MPI_Bcast(a, 100, MPI_INT, 0, MPI_COMM_WORLD);

// 每个进程算自己那部分
int start = rank * 25;
int end = start + 25;
for (int i = start; i < end; i++) {
    local_sum += a[i];
}

// 汇总
MPI_Reduce(&local_sum, &total, 1, MPI_INT, MPI_SUM, 0, MPI_COMM_WORLD);

if (rank == 0) {
    printf("Total = %d\n", total);  // 应该是 5050
}
```

**第 8 题**
```c
int local_max, global_max;

// 假设每个进程已经有了 local_max

MPI_Reduce(&local_max, &global_max, 1, MPI_INT, MPI_MAX, 0, MPI_COMM_WORLD);

if (rank == 0) {
    printf("Max = %d\n", global_max);
}
```

---

## 分析题答案

**第 9 题**
```
(1) 加速比：
    4进程：12 / 4 = 3
    8进程：12 / 2.5 = 4.8

(2) 4进程效率更高
    4进程效率：3 / 4 = 75%
    8进程效率：4.8 / 8 = 60%
    
    原因：进程越多，通信开销越大
```

**第 10 题**
```
区别：
- MPI_Reduce：结果只在 root 进程
- MPI_Allreduce：结果所有进程都有

使用场景：
- MPI_Reduce：只有一个进程需要结果（如最后输出）
- MPI_Allreduce：所有进程都需要结果继续计算
```

</details>

---

# 其他 MPI 考点补充

---

## 1. 非阻塞通信

**阻塞 vs 非阻塞**

```c
// 阻塞：必须等完成才能继续
MPI_Send();  // 等数据发完
MPI_Recv();  // 等数据收到

// 非阻塞：立刻返回，后面再等
MPI_Isend();  // 立刻返回
MPI_Irecv();  // 立刻返回
MPI_Wait();   // 等待完成
```

**用途**：提高效率，边通信边计算

---

## 2. 通信子（Communicator）

```c
MPI_COMM_WORLD  // 默认，包含所有进程
```

**可能考**：
- 什么是通信子？
- 答：一组可以互相通信的进程

---

## 3. 进程拓扑（可能考概念）

| 拓扑 | 说明 |
|------|------|
| 线性 | 进程排成一排 |
| 环形 | 首尾相连 |
| 网格 | 二维排列 |

---

## 4. MPI_Sendrecv

**一次同时发送和接收**：
```c
MPI_Sendrecv(&send_data, 1, MPI_INT, dest, tag,
             &recv_data, 1, MPI_INT, source, tag,
             MPI_COMM_WORLD, &status);
```

**好处**：避免死锁

---

## 5. MPI_Wtime

**计时函数**：
```c
double start, end;
start = MPI_Wtime();
// ... 代码 ...
end = MPI_Wtime();
printf("用时: %f 秒\n", end - start);
```

---

## 6. 数据划分方式

| 方式 | 说明 | 例子（4进程分100个） |
|------|------|---------------------|
| 块划分 | 连续分 | 0-24, 25-49, 50-74, 75-99 |
| 循环划分 | 轮流分 | 0,4,8... / 1,5,9... |

---

## 7. Scatter 和 Gather

```c
// Scatter：进程0把不同数据分给每个进程
int sendbuf[4] = {10, 20, 30, 40};
int recvbuf;
MPI_Scatter(sendbuf, 1, MPI_INT, &recvbuf, 1, MPI_INT, 0, MPI_COMM_WORLD);
// 进程0得10，进程1得20，进程2得30，进程3得40

// Gather：反过来，收集到进程0
MPI_Gather(&recvbuf, 1, MPI_INT, sendbuf, 1, MPI_INT, 0, MPI_COMM_WORLD);
```

---

## 8. 并行效率公式（常考）

```
加速比 S = T串行 / T并行

效率 E = S / P = T串行 / (P × T并行)

其中 P = 进程数
```

**理想情况**：S = P，E = 100%

**实际情况**：S < P，因为有通信开销

---

## 9. Amdahl 定律（可能考）

```
加速比 ≤ 1 / (f + (1-f)/P)

f = 串行部分比例
P = 进程数
```

**含义**：程序中串行部分越多，加速越有限

**例子**：
- 程序有 10% 必须串行
- 即使无限多进程，最多加速 10 倍

---

## 10. 常见并行模式

| 模式 | 说明 |
|------|------|
| 主从模式 | 进程0分配任务，其他执行 |
| 对等模式 | 所有进程地位相同 |
| 流水线 | 数据一级一级传递处理 |

---

## 补充练习题

### 第 11 题（填空）
```c
// 计算程序运行时间
double start, end;
start = _____();
// ... 计算 ...
end = _____();
printf("时间: %f\n", _____ - _____);
```

---

### 第 12 题（计算）
```
程序串行部分占 20%，用 4 个进程
根据 Amdahl 定律，理论最大加速比是多少？
```

---

### 第 13 题（简答）
MPI_Send 和 MPI_Isend 有什么区别？

---

<details>
<summary>答案</summary>

**第 11 题**
```c
start = MPI_Wtime();
end = MPI_Wtime();
printf("时间: %f\n", end - start);
```

**第 12 题**
```
f = 0.2, P = 4
S = 1 / (0.2 + 0.8/4)
  = 1 / (0.2 + 0.2)
  = 1 / 0.4
  = 2.5
```

**第 13 题**
```
MPI_Send：阻塞，必须等数据发完才返回
MPI_Isend：非阻塞，立刻返回，后面用 MPI_Wait 等待完成
```

</details>

---

# PI monte Carlo 并行实现示例

```c
#include <math.h>
#include <stdlib.h>
#include <stdio.h>
#include <time.h>
#include <mpi.h>

// Calculo de PI utilizando montecarlos
int main(int argc, char *argv[])
{
    int rank, size;
    MPI_Init(&argc, &argv);
    MPI_Comm_size(MPI_COMM_WORLD, &size);
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);
    int n_puntos = 10000000;

    double PI25DT = 3.141592653589793238462643;
    double piR;
    MPI_Bcast(&n_puntos, 1, MPI_INT, 0, MPI_COMM_WORLD);
    if (n_puntos <= 0)
    {
        MPI_Finalize();
        exit(0);
    }
    else
    {

        int dentro = 0;
        double x, y, pi;

        srand(time(NULL)); // Semilla aleatoria

        for (int i = rank + 1; i < n_puntos; i += size)
        {
            x = (double)rand() / RAND_MAX * 2 - 1; // Entre -1 y 1
            y = (double)rand() / RAND_MAX * 2 - 1;

            if (x * x + y * y <= 1)
            {
                dentro++;
            }
        }

        pi = 4.0 * dentro / n_puntos;
        MPI_Reduce(&pi, &piR, 1, MPI_DOUBLE, MPI_SUM, 0, MPI_COMM_WORLD);
        if (rank == 0)
        {
            printf("Pi aproximado: %f\n", piR);
            printf("Puntos usados: %d\n", n_puntos);
            printf("Resultado de pi es: %f con un error de %f", piR, fabs(PI25DT - piR));
        }
    }
    MPI_Finalize();
    return 0;
}
```

---

# Pi Riemann 并行实现示例

```c
#include <mpi.h>
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
// calcular el valor de pi utilizando seria de Rieenman
int main(int argc, char **argv)
{
    int size, rank;

    int n = 20000;
    double PI = 3.141592653589793238462643;
    double pi;
    MPI_Init(&argc, &argv);
    MPI_Comm_size(MPI_COMM_WORLD, &size);
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);

    MPI_Bcast(&n, 1, MPI_INT, 0, MPI_COMM_WORLD);
    if (n <= 0)
    {
        MPI_Finalize();
        exit(0);
    }
    else
    {
        double h = 1.0 / (double)n;
        double sum = 0.0;

        for (int i = rank + 1; i <= n; i += size)
        {
            double x = h * ((double)i - 0.5);
            sum += (4.0 / (1.0 + x * x));
        }
        double mypi = sum * h;
        MPI_Reduce(&mypi, &pi, 1, MPI_DOUBLE, MPI_SUM, 0, MPI_COMM_WORLD);
        if (rank == 0)
        {
            printf("El valor aproximado de pi es: %f con un error de %f", pi, fabs(pi - PI));
        }
    }
    MPI_Finalize();
    return 0;
}
```

---

# Envio y Recepcion de Mensajes en MPI

```c
#include <mpi.h>
#include <stdio.h>
// enviar y recibir
int main(int argc, char **argv)
{
    int rank, size;
    int contador;

    MPI_Init(&argc, &argv);
    MPI_Comm_size(MPI_COMM_WORLD, &size);
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);

    if (rank == 0)
    {
        MPI_Send(&rank, 1, MPI_INT, rank + 1, 0, MPI_COMM_WORLD);
    }
    else
    {
        MPI_Recv(&contador, 1, MPI_INT, rank - 1, 0, MPI_COMM_WORLD, MPI_STATUS_IGNORE);
        printf("Soy el proceso %d y he recibido %d\n", rank, contador);
        contador++;
        if (rank != size - 1)
        {
            MPI_Send(&contador, 1, MPI_INT, rank + 1, 0, MPI_COMM_WORLD);
        }
    }

    MPI_Finalize();
    return 0;
}
```