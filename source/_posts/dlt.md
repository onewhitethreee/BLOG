---
uuid: 4e626790-e976-11f0-b05c-c7074cbc7712
title: dlt
date: 2026-01-04 15:04:37
tags:
---

# El sistema de fichero clásico 

![](https://img.164314.xyz/2026/01/f28268dc5a452165533872f5c9d718a0.png)

# El sistema de fichero distribuido (DFS)

![](https://img.164314.xyz/2026/01/4bff0f74a8eec52d68a73aac5aa67b48.png)

# Comparación entre sistemas de ficheros clásicos y distribuidos

![](https://img.164314.xyz/2026/01/8b238493bf56f97afde9d7bc62158bd0.png)

# Desafío de diseñar un DFS

- Consistencia : Cómo garantizar que todos los nodos vean la misma versión de un fichero.
- Seguridad : Cómo proteger los datos distribuidos contra accesos no autorizados.
- Fiabilidad y disponibilidad : Cómo asegurar que los datos estén siempre accesibles, incluso en caso de fallos de nodos.
- Escalabilidad  : Qué pasa si añadimos 1000 nodos más al sistema.

# DFS : DFS cliente/servidor 

## Protocolos DFS (Distributed file system)

**Objetivo principal**: La transparencia de red. El usuario no debe saber que el fichero es de remoto. 
- Analisis de dasafíos:
  - Consistencia: Es el desafío clave. El cliente ve datos obsoletos?
  - Seguridad: Es un desafío historico. Cómo se autentica sin confiar en la IP?
  - Fiabilidad: El servidor es un punto único de fallo. El diseño debe manajar sus fallos.
- Ejemplos: **NFS, SMB, AFS.**

# FSS: Sincronización de ficheros distribuida

Objetivo principal: La disponibilidad y sincronización. El usuario quiere sus ficheros en todos sus dispositivos.

- Analisis de desafíos(Trade offs):
  - Prioriza la fiablidad y disponibilidad sobre la consistencia.
  - Prioriza la escalabilidad: Diseño para millones de usuarios.
  - Sacrifica la consistencia fuente. Utilizar un modelo de consistencia eventual.
- Ejemplos: **Dropbox, Google Drive, OneDrive.**

# VCS: Sistemas de control de versiones distribuidos

Objetivo principal: La trazabilidad y colaboración. El sistema debe gestionar el historia del cambios y la función de contribuciones. 

- Analisis de desafíos:
  - Consistencia: La consistencia no es automática (FSS) ni débil (DFS). Es atómica y manual, controlado por el usuario(commit)
  - Fiabilidad: El sistema debe ser robusto contra fallos y pérdidas de datos.
  - Sacrifica la transparencia: El usuario debe hacer operaciones explícitas (pull, push, commit).
- Ejemplos: **Git, SVN.**

# NFS: Network File System

NFS es un protocolo **DFS** clásico de unix. La arquitectura es **cliente/servidor**. El mecanismo de comunicación es **RPC** (Remote Procedure Call). El diseño de NFS se analizará en base a los 4 desafíos de diseño. **Consistencia, Seguridad, Fiablidad, Escalabilidad**

## RPC

![](https://img.164314.xyz/2026/01/bab573ca0524cb2f8f821e1c97c33f8c.png)

### El diseño stateless de NFS

Cómo asegurar que el cliente vea la versión más actualizada de un fichero? Pues NFS utiliza un diseño **stateless**(Priorizar a la fiabilidad).

- El servidor no mantiene estado de los clientes.
- Cada petición del cliente contiene toda la información necesaria para procesarla.
- Si el servidor falla y se reinicia, no pierde información sobre los clientes.

Conlleva un trade-off entre **consistencia** y fiabilidad. La consistencia puede verse afectada, pero la fiabilidad mejora significativamente.

### Consistencia en NFS: Caché con timeout y polling (Consistencia y fialbilidad)

1. Un cliente A pide un fichero al servidor S(Versión 1).
2. El servidor S envía el fichero a A, que lo guarda en su caché local para mejorar el rendimiento.
3. Otro cliente B modifica el fichero en el servidor S (Versión 2).
4. El cliente A sigue viendo la versión 1 en su caché local hasta que:
   - El timeout de la caché expira, y A vuelve a pedir el fichero al servidor S, obteniendo la versión 2.
   - O A realiza un polling explícito para verificar si el fichero ha cambiado.

## Seguridad: El modelo AUTH_SYS

El cliente simplemente envía su UID y GID con cada petición. El servidor confía en esta información para autenticar al cliente.

![](https://img.164314.xyz/2026/01/cba0f57cd2f8d6553d5e02cb5cf1e7b7.png)

### Problema

- Un atacante puede falsificar su UID/GID para obtener acceso no autorizado a los ficheros.

![](https://img.164314.xyz/2026/01/81fe38677bad3de7baf0111032e6d133.png)

## Escalabilidad

Ventajas de usar NFS: **La cahé cliente reduce la carga de lectura del servidor**
Desventajas: **Las escrituras siempre(polling) van al servidor, creando un cuello de botella.**

## Interoperabilidad(Incompletible de diferentes sistemas)

NFS debe de saber traducir diferentes formatos de peticiones y respuestas entre sistemas heterogéneos.

- Problema 1: **Permisos**: Diferentes sistemas tienen diferentes modelos de permisos (Unix vs Windows).
- Problema 2: **Formatos de datos**: Diferentes arquitecturas pueden tener diferentes formatos de datos (big-endian vs little-endian).
- Problema 3: **Caracteres**: Diferentes sistemas pueden usar diferentes conjuntos de caracteres (ASCII vs Unicode).
- Problema 4: **Semántica de nombres**

# AFS: Andrew File System

AFS es al contrario de NFS un sistema de ficheros distribuido basado en **clientes ligeros**. El objetivo principal de AFS es **la escalabilidad y la disponibilidad** y con **estado(stateful)**

## Callbacks

El mecanismo de callbacks permite al servidor notificar a los clientes cuando un fichero ha cambiado, invalidando sus cachés locales. **Al descargar el fichero, el cliente recibe una promise del servidor de que será notificado si el fichero cambia.**

Ventajas:
- Mejora la consistencia: Los clientes reciben actualizaciones en tiempo real.
- Reduce la carga del servidor: Menos polling.
Desventajas:
- Complejidad adicional: El servidor debe mantener el estado de los callbacks.
- Problemas de fiabilidad: Si el servidor falla, los clientes pueden tener cachés obsoletas.

![](https://img.164314.xyz/2026/01/f3d1c11dd67704f7893129cf3e2cc409.png)

![](https://img.164314.xyz/2026/01/9c485c700efea9a4c1fa086dfd2ce4d0.png)

![](https://img.164314.xyz/2026/01/d8b9e57734b33ee3564fe829e448983a.png)

## Afs Seguridad: Kerberos

AFS no utiliza AUTH_SYS como NFS, sino que emplea Kerberos para la autenticación. Un tercero de confianza para la auttenticación criptográfica. Esto soluciona los problemas de seguridad de AUTH_SYS.

![](https://img.164314.xyz/2026/01/9caad0c8825aef4426c0e3b928886b32.png)

## AFS Escalabilidad

La escalabilidad está resultada por reemplazar el callbacks. 

## Fiabilidad en AFS 

Si el servidor AFS falla, pierde toda sus informacion de estado. La recuperacion es compleja.

# Samba: Sistema de ficheros distribuido para entornos Windows

Un protocolo de DFS cliente/servidor desarrollado para entornos Windows. Samba implementa el protocolo SMB (Server Message Block) para compartir ficheros e impresoras en redes Windows. Es 

Acceso desde un cliente A a un servidor SMB. Samba bloquea temporalmente el fichero para evitar conflictos de escritura.

![](https://img.164314.xyz/2026/01/45066be44972c4d507e671f37e5ef20a.png)

Otro cliente desea acceder al mismo fichero. Samba le informa que el fichero está bloqueado por el cliente A. Por lo que no se puede acceder hasta que A termine su operación.

![](https://img.164314.xyz/2026/01/19b7196b0364c75aa732e86224111699.png)

## Samba V 1. 0

Versión de sabma antigua con vulnerabilidad de seguridad, como el ataque de EternalBlue. Que permitió la propagación del ransomware WannaCry en 2017. 

# Tabla de comparación entre NFS, AFS y Samba

![](https://img.164314.xyz/2026/01/a2673401d19adb1e7542ea636a9ef759.png)

# El desafío de C/S: Fiabilidad y replicación(single point of failure)

Un grande problema de los sistemas DFS cliente/servidor es que el servidor es un punto único de fallo. Si el servidor falla, todos los clientes pierden acceso a los ficheros compartidos. Para solucionar este problema, se pueden implementar técnicas de **replicación**.

Es decir, tomar una copia del servidor principal y mantenerla sincronizada. Si el servidor principal falla, un servidor secundario puede tomar su lugar. Pero introduce otro desafío: **Cerebro dividido(Split-Brain)**.

# Split-Brain

**中文：脑裂**

---

**一句话：集群中节点互相联系不上，各自认为自己是老大**

---

## 简单比喻

```
一个公司有2个老板
     ↓
突然电话断了
     ↓
两人都认为对方挂了
     ↓
各自下达不同命令
     ↓
公司乱套
```

---

## 发生场景

```
正常：
[节点A] ←心跳→ [节点B]
  主            从

脑裂：
[节点A]   ✗   [节点B]
 "我是主"     "我是主"
    ↓            ↓
  各自工作     各自工作
    ↓            ↓
     数据不一致！
```

---

## 危害

| 问题 | 说明 |
|------|------|
| 数据不一致 | 两边各写各的 |
| 数据丢失 | 恢复时冲突 |
| 服务混乱 | 用户连到不同节点 |
| 资源争抢 | 同时操作共享存储 |

---

## 常见原因

| 原因 | 例子 |
|------|------|
| 网络故障 | 心跳线断了 |
| 网络拥塞 | 心跳超时 |
| 节点过载 | 来不及响应 |

---

## 解决方案

| 方案 | 说明 |
|------|------|
| **仲裁机制** | 第三方投票决定谁是主 |
| **奇数节点** | 3/5/7台，少数服从多数 |
| **STONITH** | 直接关掉另一台（爆头） |
| **多条心跳线** | 冗余网络 |

---

## 仲裁示例

```
[A] [B] [C] 三个节点

网络断成两半：
[A]  |  [B] [C]
 1票     2票
  ↓       ↓
 下线    继续服务
```

---

## 一句话总结

**宁可一个不干活，不要两个乱干活**

# Escalabilidad y Balanceado de carga

El paradigma cliente/servidor también sufre de un cuello de botelle de **escalabilidad**

Un único servidor no puede manejar un número ilimitado de clientes. Para solucionar este problema, se pueden implementar técnicas de **balanceado de carga** y **replicación de servidores**.

![](https://img.164314.xyz/2026/01/2c34d0a2556ec4909e2e86b7ecc25314.png)


# Peer to Peer (P2P)

**中文：点对点 / 对等网络**

---

**一句话：没有中心服务器，大家互相传数据**

---

## 对比

```
传统模式（C/S）：
      [服务器]
     ↙  ↓  ↘
   用户 用户 用户

P2P模式：
   用户 ←→ 用户
    ↕  ╲ ╱  ↕
   用户 ←→ 用户
```

---

## 简单比喻

| 模式 | 比喻 |
|------|------|
| 传统 | 去商店买东西 |
| P2P | 邻居之间互相借东西 |

---

## 特点

| 优点 | 缺点 |
|------|------|
| 没有中心故障点 | 难管理 |
| 人越多越快 | 安全性差 |
| 成本低 | 内容难控制 |
| 抗封锁 | 法律风险 |

---

## 常见应用

| 类型 | 例子 |
|------|------|
| 下载 | BitTorrent、迅雷 |
| 聊天 | 早期Skype |
| 加密货币 | 比特币 |
| 文件分享 | IPFS |

---

## BT下载原理

```
你要下载一个电影

传统：
  服务器 → 你（1个来源）

BT：
  用户A → 你
  用户B → 你   （多个来源）
  用户C → 你
  
  同时你也给别人传
```

---

## 一句话总结

**人人为我，我为人人**

# Discovery Problem

**中文：发现问题 / 节点发现问题**

---

**一句话：在P2P网络中，怎么找到其他节点？**

---

## 问题本质

```
你刚加入P2P网络

问题：
  我是谁？
  其他人在哪？
  怎么联系他们？

没有中心服务器告诉你！
```

---

## 简单比喻

```
你搬到新城市

传统：查电话簿（中心服务器）

P2P：没有电话簿
     → 怎么认识人？
```

---

## 常见解决方案

| 方案 | 说明 | 例子 |
|------|------|------|
| **种子节点** | 先连几个固定节点 | 比特币 |
| **广播** | 喊一声谁在？ | 局域网发现 |
| **DHT** | 分布式哈希表 | BT下载 |
| **Tracker** | 中心登记处 | BT Tracker |
| **DNS** | 域名记录节点 | 一些P2P网络 |

---

## 方案对比

```
1. 种子节点（Bootstrap）
   [固定节点] → 告诉你其他节点
   缺点：节点挂了咋办

2. 广播
   "谁在？" → 全网喊
   缺点：网络大了不行

3. DHT
   每个节点存一部分信息
   缺点：复杂

4. Tracker
   [登记处] ← 大家来登记
   缺点：又变成中心化了
```

---

## 比特币的做法

```
1. 硬编码一些种子节点
2. 连上后获取更多节点
3. 保存节点列表
4. 下次直接用列表
```

---

## 一句话总结

**第一个朋友最难交**

# Napster

**中文：纳普斯特**

---

**一句话：最早的P2P音乐分享软件，改变了音乐产业**

---

## 是什么

```
1999年，一个大学生做的软件

功能：
  免费下载MP3音乐
  用户之间互相分享
```

---

## 工作原理

```
      [中心服务器]
       存歌曲目录
      ↙    ↓    ↘
   用户A  用户B  用户C
   有歌1  有歌2  有歌3

1. 你搜"周杰伦"
2. 服务器告诉你：用户A有
3. 你直接从用户A下载
```

---

## 架构特点

| 部分 | 说明 |
|------|------|
| 中心服务器 | 只存目录索引 |
| 用户电脑 | 存实际音乐文件 |
| 传输方式 | 用户之间直接传 |

```
半P2P：
  搜索 → 中心化
  下载 → 去中心化
```

---

## 历史影响

| 时间 | 事件 |
|------|------|
| 1999 | 发布，爆火 |
| 2000 | 8000万用户 |
| 2001 | 被唱片公司告，关闭 |

---

## 为什么重要

```
开创了：
  ✓ P2P文件分享概念
  ✓ 数字音乐时代
  ✓ 后来的BT、电驴等

逼出了：
  ✓ iTunes
  ✓ Spotify
  ✓ 正版流媒体
```

---

## 缺点

| 问题 | 说明 |
|------|------|
| 中心服务器 | 关了就全完 |
| 版权问题 | 被告倒闭 |
| 单点故障 | 不是纯P2P |

---

## 一句话总结

**P2P鼻祖，死于版权，但改变了世界**

# Ad Hoc

**中文：即席 / 临时 / 点对点直连**

---

**一句话：设备之间直接连接，不需要路由器**

---

## 对比

```
普通WiFi：
  手机 → [路由器] → 手机
  
Ad Hoc：
  手机 ←→ 手机
  （直接连）
```

---

## 简单比喻

| 模式 | 比喻 |
|------|------|
| 普通网络 | 打电话要通过电信公司 |
| Ad Hoc | 两个人直接喊话 |

---

## 特点

| 优点 | 缺点 |
|------|------|
| 不需要基础设施 | 范围有限 |
| 快速组建 | 管理困难 |
| 灵活 | 安全性差 |
| 免费 | 速度可能慢 |

---

## 常见应用

```
✓ AirDrop（苹果设备互传）
✓ WiFi Direct
✓ 蓝牙连接
✓ 军事战场通信
✓ 灾难救援
✓ 游戏联机（面对面）
```

---

## Ad Hoc网络类型

```
1. 简单直连
   A ←→ B

2. 多跳网络（MANET）
   A ←→ B ←→ C ←→ D
   （通过中间节点传递）
```

---

## 词源

```
Ad Hoc = 拉丁语

意思：
  "为此目的"
  "临时的"
  "专门的"

不只用于网络：
  - ad hoc会议（临时会议）
  - ad hoc委员会（临时委员会）
```

---

## 一句话总结

**不需要中间人，直接连**

# Gnutella

**中文：GNU特拉**

---

**一句话：第一个真正的纯P2P网络，没有中心服务器**

---

## 与Napster对比

```
Napster（半P2P）：
  用户 → [中心服务器] → 用户
         存目录

Gnutella（纯P2P）：
  用户 ←→ 用户 ←→ 用户
     没有中心服务器
```

---

## 工作原理

```
你要搜"周杰伦"

1. 问邻居：有吗？
2. 邻居问他的邻居：有吗？
3. 一层层传下去（洪泛）
4. 有人回应：我有！
5. 直接从他下载

像传话一样
```

---

## 搜索方式：洪泛（Flooding）

```
        [你]
       ↙ ↓ ↘
      A  B  C     ← 问第1层
     ↙↘ ↙↘ ↙↘
    D E F G H I   ← 问第2层
    ...继续扩散...

TTL：限制传几层，否则爆炸
```

---

## 特点

| 优点 | 缺点 |
|------|------|
| 真正去中心化 | 搜索效率低 |
| 关不掉 | 消耗带宽大 |
| 没有单点故障 | 不保证找到 |

---

## 历史

| 时间 | 事件 |
|------|------|
| 2000年 | 发布（Napster被告后） |
| 几天后 | 作者公司要求下架 |
| 但是 | 代码已开源，停不了 |

---

## 使用Gnutella的软件

```
LimeWire（最有名）
BearShare
Morpheus
FrostWire
```

---

## 问题

```
洪泛太浪费：
  问1000个人
  可能只有1个人有

后来的改进：
  → 超级节点（有些节点当小服务器）
  → DHT（分布式哈希表）
```

---

## 一句话总结

**Napster被告死了，Gnutella没有中心，告不死**

# DHT

**全称：Distributed Hash Table**

**中文：分布式哈希表**

---

**一句话：把"目录"分散存到所有人电脑上**

---

## 解决什么问题

```
Napster：
  中心服务器存目录 → 被关就死

Gnutella：
  洪泛问所有人 → 太浪费

DHT：
  目录分散存 → 高效又不死
```

---

## 核心思想

```
文件名 → 哈希值 → 负责的节点

例子：
  "周杰伦.mp3" 
  → 哈希 = 3A7F
  → 节点ID接近3A7F的人负责记录

想找这首歌？
  → 直接问3A7F附近的节点
```

---

## 简单比喻

| 方式 | 比喻 |
|------|------|
| Napster | 一个图书管理员知道所有书 |
| Gnutella | 大喊：谁有这本书？ |
| DHT | 姓王的书放A区，姓李的放B区 |

---

## 工作流程

```
存文件：
  1. 文件哈希 = ABC123
  2. 找节点ID≈ABC123的人
  3. 告诉他：我有这文件

找文件：
  1. 算哈希 = ABC123
  2. 问节点ID≈ABC123的人
  3. 他告诉你：去找用户X下载
```

---

## 查找效率

```
网络有N个节点

Gnutella洪泛：问O(N)个人
DHT：只问O(log N)个人

1000000个节点：
  洪泛：问1000000次
  DHT：问约20次
```

---

## 常见DHT算法

| 名称 | 用于 |
|------|------|
| Chord | 学术经典 |
| Kademlia | BitTorrent、IPFS |
| Pastry | 微软研究 |
| CAN | 多维空间 |

---

## 实际应用

```
✓ BitTorrent（磁力链接）
✓ IPFS
✓ 以太坊
✓ 各种区块链
```

---

## 特点

| 优点 | 缺点 |
|------|------|
| 去中心化 | 实现复杂 |
| 高效查找 | 节点变动要维护 |
| 可扩展 | 安全攻击风险 |

---

## 一句话总结

**把电话簿撕碎，每人记几页，但都能查到**

# Defafíos de P2P en mundo real 

## Volatilidad

# Churn

**中文：节点流失 / 节点变动**

---

**一句话：P2P网络中节点不断加入和离开**

---

## 什么意思

```
P2P网络：

  时刻1：A B C D 在线
  时刻2：A B 离开，E F 加入
  时刻3：C 离开，A G 加入
  
  节点一直在变 = Churn
```

---

## 简单比喻

```
像一个派对：

  人不断进来
  人不断离开
  但派对还在继续

这种人员流动 = Churn
```

---

## 为什么是问题

```
DHT存目录在各节点上：

  节点A负责记录"周杰伦.mp3"在哪
  ↓
  节点A突然离开
  ↓
  记录丢了！找不到了！
```

---

## Churn带来的问题

| 问题 | 说明 |
|------|------|
| 数据丢失 | 负责节点离开 |
| 路由失效 | 邻居表过期 |
| 查询失败 | 找不到目标 |
| 维护开销 | 要不断更新 |

---

## 解决方法

```
1. 复制
   同一数据存多个节点
   一个走了还有备份

2. 定期刷新
   经常更新邻居表

3. 冗余路由
   多条路径到目标

4. 快速检测
   发现节点离开立刻处理
```

---

## Churn率

```
高Churn：节点变动频繁
  → 手机网络、临时用户

低Churn：节点稳定
  → 服务器、长期在线用户
```

---

## 词源

```
Churn = 搅动 / 翻腾

像搅牛奶：
  不断翻动
  成分一直在变
```

---

## 一句话总结

**P2P网络里，人来人往是常态**

## Incentivos(Tit for Tat)

# Tit for Tat

**中文：以牙还牙 / 一报还一报**

---

**一句话：你对我好，我对你好；你不传给我，我也不传给你**

---

## 用在哪里

```
BitTorrent 的核心策略

解决问题：
  有人只下载不上传（白嫖）
  怎么治他们？
```

---

## 原理

```
你给我传数据 → 我也给你传
你不给我传   → 我也不给你传

简单公平
```

---

## BitTorrent中的实现

```
每个用户跟踪：
  谁给我传了多少

优先传给：
  传给我最多的人

惩罚：
  不传给我的人，我也不传给他
```

---

## 简单比喻

```
交换零食：

  小明给我薯片 → 我给他饼干
  小红什么都不给 → 我也不给她

公平交易
```

---

## 具体机制

```
1. 选择4个最好的伙伴
   （给我传最快的4个人）
   → 我也优先传给他们

2. 每30秒重新评估
   → 谁表现好就优先

3. Optimistic Unchoking
   → 偶尔随机选一个新人试试
```

---

## Optimistic Unchoking

```
为什么要随机选新人？

问题：新加入的人没机会证明自己
解决：偶尔给新人一个机会

每30秒随机选1个陌生人
给他传，看他表现
```

---

## 效果

| 行为 | 结果 |
|------|------|
| 上传多 | 下载快 |
| 只下载不上传 | 下载很慢 |
| 完全不传 | 几乎下不动 |

---

## 博弈论来源

```
囚徒困境的最优策略

1980年 Axelrod 比赛证明：
  Tit for Tat 长期表现最好

特点：
  - 善良（先合作）
  - 可激怒（被背叛就报复）
  - 宽容（对方改好就原谅）
```

---

## 一句话总结

**你帮我，我帮你；你不帮我，我也不帮你**

## Seguridad

# Security（安全）

**一句话：防止坏人搞破坏**

---

## P2P面临的安全问题

```
没有中心管理
  → 谁都能加入
  → 坏人也能进来
```

---

## 常见攻击

### 1. Sybil Attack（女巫攻击）

```
一个人假装成很多人

  坏人创建1000个假身份
  → 控制网络大部分节点
  → 操纵投票、路由、数据
```

---

### 2. Eclipse Attack（日蚀攻击）

```
包围受害者

  坏人控制你所有邻居
  → 你看到的都是假信息
  → 与真实网络隔离
```

---

### 3. Routing Attack（路由攻击）

```
篡改路由

  查询：谁有这个文件？
  坏人：我有！（其实没有）
  → 查询失败或得到假数据
```

---

### 4. Free Riding（白嫖）

```
只下载不上传

  → 消耗资源不贡献
  → 系统效率下降
```

---

### 5. Pollution Attack（污染攻击）

```
传播假文件

  文件名：热门电影.mp4
  实际：垃圾内容或病毒
```

---

## 防御方法

| 攻击 | 防御 |
|------|------|
| Sybil | 需要成本加入（算力/身份） |
| Eclipse | 随机选邻居 |
| 路由攻击 | 多路径验证 |
| 白嫖 | Tit for Tat |
| 污染 | 哈希校验 |

---

## 安全三要素

```
机密性：数据不被偷看
  → 加密

完整性：数据不被篡改
  → 哈希校验

可用性：服务不被中断
  → 冗余、分布式
```

---

## P2P vs 中心化 安全对比

| 方面 | 中心化 | P2P |
|------|--------|-----|
| 单点故障 | 有 | 无 |
| 管理容易 | 是 | 难 |
| Sybil攻击 | 不易 | 容易 |
| 审查 | 容易 | 困难 |

---

## 一句话总结

**P2P自由但危险，要防各种坏人**

# TOR（洋葱路由）

**全称：The Onion Router**

**一句话：像洋葱一层层加密，让人追踪不到你**

---

## 解决什么问题

```
正常上网：
  你 → 网站
  网站知道你是谁（IP地址）

TOR上网：
  你 → ? → ? → ? → 网站
  网站不知道你是谁
```

---

## 为什么叫洋葱

```
洋葱 = 一层包一层

加密也是一层包一层：
  
  消息
  ↓ 加密一层
  [消息]
  ↓ 再加密一层  
  [[消息]]
  ↓ 再加密一层
  [[[消息]]]
```

---

## 工作原理

```
你要访问网站，经过3个节点：

节点A（入口）→ 节点B（中间）→ 节点C（出口）→ 网站

加密过程：
  你用C的密钥加密：[消息]
  你用B的密钥加密：[[消息]]
  你用A的密钥加密：[[[消息]]]

发送出去
```

---

## 解密过程

```
[[[消息]]] → 节点A
  A解开一层 → [[消息]]
  A只知道：来自你，发给B

[[消息]] → 节点B
  B解开一层 → [消息]
  B只知道：来自A，发给C

[消息] → 节点C
  C解开一层 → 消息
  C只知道：来自B，发给网站

网站收到消息
  只知道来自C
```

---

## 关键：没人知道全貌

| 节点 | 知道什么 | 不知道什么 |
|------|----------|------------|
| A | 你是谁 | 你要去哪 |
| B | A和C | 你是谁、目的地 |
| C | 目的地 | 你是谁 |
| 网站 | C的地址 | 你是谁 |

---

## 简单比喻

```
寄信：

  你写信给朋友
  放进信封，写"给C"
  再放进信封，写"给B"  
  再放进信封，写"给A"

  A收到 → 拆一层 → 转给B
  B收到 → 拆一层 → 转给C
  C收到 → 拆一层 → 送到朋友

  没人知道完整路径
```

---

## TOR的用途

```
正当用途：
  - 保护隐私
  - 绕过审查
  - 记者保护线人
  - 敏感研究

非法用途：
  - 暗网交易
  - 隐藏犯罪
```

---

## 暗网（.onion网站）

```
普通网站：www.google.com
暗网网站：xxxx.onion

.onion网站：
  - 只能通过TOR访问
  - 服务器位置也隐藏
  - 双向匿名
```

---

## TOR的弱点

| 弱点 | 说明 |
|------|------|
| 速度慢 | 多层加密+多次转发 |
| 出口可监听 | 最后一段没加密 |
| 流量分析 | 大规模监控可能关联 |
| 入口已知 | 知道你在用TOR |

---

## TOR vs VPN

| 方面 | TOR | VPN |
|------|-----|-----|
| 匿名性 | 更强 | 较弱 |
| 速度 | 慢 | 快 |
| 信任 | 不需要 | 要信任VPN商 |
| 用途 | 高度匿名 | 日常隐私 |

---

## 一句话总结

**三层加密，三次转发，没人知道你是谁**

![](https://img.164314.xyz/2026/01/a3e6c476e0739e88b33bd0c58e638ad1.png)

# Bitrot（比特腐烂）

**中文：数据腐烂 / 静默数据损坏**

**一句话：存着存着，数据自己就坏了**

---

## 什么是Bitrot

```
文件放在硬盘里
没人动它
过一段时间
某些bit从1变成0，或0变成1

数据悄悄坏掉了
```

---

## 为什么会发生

```
物理原因：

  - 磁盘磁性衰减
  - 宇宙射线撞击
  - 电子元件老化
  - 温度/湿度影响
  - 硬盘读写头轻微错误
```

---

## 可怕在哪里

```
悄悄坏掉 = Silent Corruption

  没有报错
  没有提示
  你以为文件是好的
  打开才发现坏了

  照片打不开
  视频花屏
  文档乱码
```

---

## 简单比喻

```
书放在书架上

  10年没动
  纸张发黄
  字迹模糊
  有些字看不清了

  数据也一样会"老化"
```

---

## 多久会发生

```
概率很低但真实存在

  每年约 1/1000 的文件可能出问题
  
  存1000个文件10年
  → 大概率有几个会坏
```

---

## 在P2P/分布式中的影响

```
IPFS/BitTorrent等系统：

  文件分散存在很多节点
  有些节点的数据可能悄悄坏掉
  
  如果不检测
  → 坏数据可能被传播
```

---

## 如何防护

| 方法 | 说明 |
|------|------|
| 哈希校验 | 定期检查文件hash |
| 多副本 | 多处备份 |
| ECC内存 | 自动纠错 |
| ZFS/Btrfs | 自带校验的文件系统 |
| RAID | 冗余磁盘 |

---

## IPFS如何处理

```
内容寻址：
  
  文件 → 计算哈希 → CID
  
  下载时验证哈希
  → 哈希不对 = 数据坏了
  → 从其他节点重新获取
```

---

## 对比

| 情况 | 普通文件系统 | IPFS |
|------|--------------|------|
| 数据损坏 | 不知道 | 能检测到 |
| 坏了怎么办 | 没办法 | 从别处获取 |
| 自动修复 | 无 | 有 |

---

## 一句话总结

**数据不动也会坏，要定期校验和备份**

# De P2P a DLT

**中文：从点对点到分布式账本技术**

**一句话：DLT是P2P技术的进化，专注于去中心化的可信记录**

Componente 1: El ledger (Registro)
**Ledger = 账本 / 记录**
Componente 2: El consenso **(共识)**
**Consenso = 大家都同意某件事**
---

# Trusted Third Party（可信第三方）

**简称：TTP**

**一句话：你我不信任，找个中间人帮忙**

---

## 解决什么问题

```
买家和卖家互不认识：

  买家怕：付钱了不发货
  卖家怕：发货了不付钱

  谁先动？
```

---

## 引入第三方

```
找一个双方都信任的人：

  买家 → 把钱给第三方
  卖家 → 看到钱了，发货
  买家 → 收到货，确认
  第三方 → 把钱给卖家

  问题解决！
```

---

## 生活中的例子

| 场景 | 可信第三方 |
|------|------------|
| 网购 | 支付宝/淘宝 |
| 买房 | 银行托管 |
| 国际贸易 | 银行信用证 |
| 网站安全 | CA证书机构 |
| 公证 | 公证处 |

---

## 支付宝怎么工作的

```
你买东西：

  1. 你付钱 → 支付宝
  2. 支付宝告诉卖家：钱到了
  3. 卖家发货
  4. 你收货，点确认
  5. 支付宝 → 把钱给卖家

  支付宝 = 可信第三方
```

---

## 问题在哪里

```
你必须信任第三方：

  - 它不会跑路
  - 它不会偏袒
  - 它不会泄露信息
  - 它不会被黑客攻击

  但凭什么信它？
```

---

## 第三方的风险

| 风险 | 例子 |
|------|------|
| 跑路 | P2P平台暴雷 |
| 作恶 | 偏袒一方 |
| 被黑 | 数据泄露 |
| 审查 | 冻结你的钱 |
| 倒闭 | 服务停止 |

---

## 区块链的思路

```
传统：
  买家 ←→ 第三方 ←→ 卖家
            ↑
         要信任它

区块链：
  买家 ←→ 智能合约 ←→ 卖家
            ↑
         代码是公开的
         不需要信任任何人
```

---

## TTP vs 去中心化

| 方面 | 可信第三方 | 去中心化 |
|------|------------|----------|
| 速度 | 快 | 可能慢 |
| 成本 | 收手续费 | Gas费 |
| 信任 | 要信任它 | 信任代码 |
| 风险 | 可能作恶 | 代码可能有bug |
| 体验 | 好 | 较复杂 |

---

## 一句话总结

**不信任对方就找中间人，但中间人也可能出问题**

![](https://img.164314.xyz/2026/01/e4102b0d2a4b07bbefc6a85474c21343.png)

# DLT（Distributed Ledger Technology）

**中文：分布式账本技术**

**一句话：大家都有一本账，内容完全一样**

---

## 什么是账本

```
传统账本：

  银行有一本账
  记录谁有多少钱
  
  只有银行能看、能改
  你只能信任它
```

---

## 什么是分布式账本

```
分布式账本：

  每个人都有一本账
  内容完全相同
  任何改动大家都知道

  没有人能偷偷改
```

---

## 简单比喻

```
传统：
  老师记成绩本
  只有老师有
  老师说你60分就是60分

分布式：
  全班每人都有成绩本
  内容一样
  老师想改你分数
  → 全班都会发现
```

---

## 核心特点

| 特点 | 说明 |
|------|------|
| 分布式 | 多个节点都有副本 |
| 同步 | 所有副本保持一致 |
| 不可篡改 | 改了大家都知道 |
| 共识 | 大家同意才能写入 |

---

## DLT vs 区块链

```
区块链是DLT的一种

DLT（大类）
  ├── 区块链（数据按块链接）
  ├── DAG（有向无环图）
  ├── Hashgraph
  └── 其他结构
```

---

## 不同结构

| 类型 | 代表 | 特点 |
|------|------|------|
| 区块链 | 比特币、以太坊 | 一条链 |
| DAG | IOTA、Nano | 网状结构 |
| Hashgraph | Hedera | 八卦协议 |

---

## 和普通数据库的区别

| 方面 | 普通数据库 | DLT |
|------|------------|-----|
| 控制 | 一家公司 | 多方共同 |
| 副本 | 备份用 | 每个都是主本 |
| 信任 | 信任管理员 | 信任共识 |
| 修改 | 管理员可改 | 需要共识 |

---

## 用在哪里

```
适合多方协作但互不信任的场景：

  - 跨境支付（多家银行）
  - 供应链（多家公司）
  - 政府数据（多个部门）
  - 医疗记录（多家医院）
```

---

## 一句话总结

**大家都记账，谁也骗不了谁**

![](https://img.164314.xyz/2026/01/20b729adfa1b1c32de72cbce5a06dd80.png)

# 如果真有人骗过所有人怎么办

**好问题！这就是区块链安全的核心**

---

## 要骗所有人，需要什么

```
比特币为例：

  全网算力假设 100%
  
  你要骗所有人
  → 你需要 > 50% 算力
  → 这叫"51%攻击"
```

---

## 51%攻击能做什么

```
能做的：
  - 双花（同一笔钱花两次）
  - 阻止别人的交易确认
  - 回滚最近的交易

不能做的：
  - 偷别人的币（没私钥）
  - 凭空造币（规则不允许）
  - 修改很久以前的记录
```

---

## 代价有多大

```
攻击比特币51%需要：

  硬件成本：几十亿美元
  电费：每小时几十万美元
  
  而且一旦攻击成功
  → 币价暴跌
  → 你的设备变废铁
  
  赔本生意
```

---

## 真实发生过吗

| 币种 | 时间 | 后果 |
|------|------|------|
| Bitcoin Gold | 2018 | 损失1800万美元 |
| Ethereum Classic | 2019 | 被双花100万美元 |
| Verge | 2018 | 被攻击多次 |

**小币容易被攻击，大币很难**

---

## 为什么大币安全

```
比特币：

  节点数：几万个
  算力：巨大
  分布：全球各地

  要同时骗过所有人
  → 几乎不可能
```

---

## 其他防线

```
即使51%攻击成功：

  1. 社区会发现
  2. 可以硬分叉回滚
  3. 交易所会暂停充提
  4. 攻击者难以套现

  不是骗过就完事了
```

---

## 一句话总结

**理论上可能，实际上代价太大，得不偿失**

# Blockchain y DLT

**中文：区块链与分布式账本技术**

# Blockchain 

- El bloque(Cadena de bloques) : 区块链数据
- La cadena : 链接区块
- La red(Infraestructura) : P2P网络

# Poque no se puede hackear una blockchain?

![](https://img.164314.xyz/2026/01/7998ea629ba442eb818103a0040e268f.png)