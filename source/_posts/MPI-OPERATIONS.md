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