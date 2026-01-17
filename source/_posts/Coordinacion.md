---
uuid: 75e79c30-e413-11f0-97e9-5b68fda673d9
layout: sistemas
title: Coordinación
date: 2025-12-28 18:34:28
tags:
---
# Problema de la sincronización de procesos

## Por qué es tan complejo gestionar el tiempo?

Porque no existe un reloj global que todos los procesos puedan consultar para saber la hora exacta. Cada proceso tiene su propio reloj, y estos relojes pueden desincronizarse debido a variaciones en la velocidad de los procesadores, retrasos en la comunicación y otros factores.

Introduce 3 problemas que pueden surgir debido a la falta de sincronización entre procesos:

1. **sincronizacion entre emisor y receptor**
2. **control de actividad comun entre procesos coperativo**
3. **Serializacion de accesos concurrente a recursos compartidos**

### Deriva(drift) y desviacion(Skew)

**Deriva(Drift)** es la tasa(velocidad) a la que un reloj se adelanta o atrasa con respecto a un reloj de referencia. Por ejemplo, si un reloj tiene una deriva de 0.0001 segundos por segundo, significa que cada segundo, el reloj se adelanta o atrasa 0.0001 segundos con respecto al reloj de referencia.

**Desviación(Skew)** es la diferencia absoluta entre dos relojes en un momento dado. Por ejemplo, si el reloj A marca las 12:00:05 y el reloj B marca las 12:00:10, la desviación entre los dos relojes es de 5 segundos.

### Algoritmos de sincronización de relojes

1. NTP
2. Berkeley

El objetivo de estos algoritmos es corregir el skew por debajo de limite establecido.

![image.png](https://img.164314.xyz/2025/12/1b182ccf571a4a55895909ba02e58d0a.png)

### Sincronización externa

Objetivo: Precisión
Fuente: Un reloj atómico externo. Ej: servidor externo
Métrica: El skew entre el reloj local y el reloj externo.
Algoritmos: NTP y Método de Cristian

### Sincronización interna

Objetivo: Consistencia
Fuente: No hay fuente externa. Los procesos se sincronizan entre sí.
Métrica: El skew entre los relojes de los procesos en el sistema distribuido.
Algoritmos: Algoritmo de Berkeley

### Sincrono vs Asincrono

**Sistemas síncronos**:

- Deterministas: Los eventos ocurren en tiempos predecibles.
- Establecen límites superiores para los tiempos de respuesta. Si no se cumple, se considera una falla.

**Sistemas asíncronos**:

- No deterministas: Los eventos pueden ocurrir en tiempos impredecibles.
- No establecen límites superiores para los tiempos de respuesta. Los procesos pueden retrasarse indefinidamente.

![](https://img.164314.xyz/2025/12/e63bf40354ee4fbe40b6924512c6b2b6.png)

### Método de Cristian

Un algoritmo para la sincronización externa en sistemas asíncronos.

![](https://img.164314.xyz/2025/12/3adb12ce1b304f7d50ace28668026dc4.png)

### Algoritmo de Berkeley

Objetivo: Sincronización interna para un sistema síncrono(o una Lan) que no tiene acceso a una fuente UTC.

1. Se elige un proceso maestro (coordinador) que será responsable de sincronizar los relojes de los demás procesos (esclavos). Nota: Si el maestro falla, se elige otro maestro mediante un algoritmo de elección de líder.
2. El maestro no envía una hora absoluta. Envía un deta de ajuste(positivo o negativo) a cada esclavo.

![](https://img.164314.xyz/2025/12/7b1798115ba77a16e8fa8abe2825f0cd.png)

![](https://img.164314.xyz/2025/12/31e63a6a8e2c36b2cce8bb4bc6880568.png)

![](https://img.164314.xyz/2025/12/b507a2bc83f135841122519eb6e8fcd4.png)

![](https://img.164314.xyz/2025/12/625d4f09c2e82331b6228528449c802b.png)

### NTP (Network Time Protocol)

Es el servicio estandar de Internet para la sincronización de relojes.

Precisión: < 1 milisegundos en redes LAN y 1-50 milisegundos en redes WAN.

#### Arquitectura jerárquica de NTP

![](https://img.164314.xyz/2025/12/42bb515756de118b3e7ef8e70e752ee9.png)

## Relojes lógicos

### El orden

#### Definiendo "el orden" formalmente:

Definida por Lamport. Permiter crear un orden parcial sin relojes físicos.

- Si un evento a ocurre antes que un evento b en el mismo proceso, entonces a -> b.
- Si a es el evento de envío de un mensaje m por un proceso P y b es el evento de recepción del mismo mensaje m por otro proceso Q, entonces a -> b(la causa envío sucede antes que el efecto recepción).
- Si a -> b y b -> c, entonces a -> c (transitividad).
- Eventos concurrentes: Si no se cumple ninguna de las condiciones anteriores, los eventos son concurrentes (no hay relación de orden entre ellos). a || b. No significa que ocurrieron al mismo tiempo, sino que son casualmente independientes.

#### Implementado la relacion happened-before

Cada proceso mantiene un contador de enteros (reloj lógico) que se incrementa con cada evento local.

- Regla R1: Evento Interno. Antes de ejecutar un evento(interno de envío), P incrementa su reloj lógico L(P) en 1(sumar 1).
- Regla R2: Envío de mensaje. Al enviar un mensaje m, P(después de R1) adjunta su reloj lógico L(P) al mensaje(mensaje = {datos, L(P)}).
- Regla R3: Recepción de mensaje. Al recibir un mensaje m con reloj lógico L(m), Q actualiza su reloj lógico L(Q) de la siguiente manera: L(Q)

![](https://img.164314.xyz/2025/12/ffcd54ce782c2971b38b2b64eafcc3bf.png)

![](https://img.164314.xyz/2025/12/3417d1962c4e09913185fa45dd0791cd.png)

#### Qué es lo que no podemos saber?

Los relojes de Lamport garantiza la condición de reloj lógico: Si a -> b, entonces L(a) < L(b). Pero no garantiza la condición inversa: Si L(a) < L(b), no necesariamente a -> b.

Por qué? Porque los relojes lógicos no capturan la concurrencia de eventos. Dos eventos pueden tener valores de reloj lógico diferentes sin que exista una relación de orden entre ellos.

##### 核心缺点：只能单向推断

```
如果 A → B（A导致B），则 C(A) < C(B) ✓

但是！

如果 C(A) < C(B)，不能推出 A → B ✗
```

**一句话总结：Lamport时钟能证明因果关系存在，但不能证明因果关系不存在。**

Cómo solucionarlo? Usando relojes vectoriales.

### Relojes vectoriales

El reloj vectorial es una extensión de los relojes lógicos que permite capturar la casualidad. El problema de lamport: Si L(a) < L(b), no necesariamente a -> b.

Utilizar un **vector** en lugar de un solo entero para representar el reloj de cada proceso.

##### Estructura del reloj vectorial

En un sistema de N procesos, cada proceso P_i mantiene un reloj vectorial V_i de tamaño N. (V_i = [c1, c2, ..., cN]), donde c_j representa el contador de eventos del proceso P_j según la perspectiva de P_i.

Todos los vectores se inicializan en cero: V_i = [0, 0, ..., 0] para todos los procesos P_i.

##### Reglas de actualización del reloj vectorial

- Regla RV1: Evento Interno. Antes de ejecutar un evento interno, P_i incrementa su propio contador en el vector: V_i[i] = V_i[i] + 1.
- Regla RV2: Envío de mensaje. Al enviar un mensaje m, P_i adjunta su reloj vectorial al mensaje: mensaje = {datos, V_i}.
- Regla RV3: Recepción de mensaje. Al recibir un mensaje m con reloj vectorial V_m, P_j actualiza su reloj vectorial de la siguiente manera:
  - Para cada k de 1 a N: V_j[k] = max(V_j[k], V_m[k])
  - Luego, P_j incrementa su propio contador: V_j[j] = V_j[j] + 1.

**三条规则**

```
1. 本地事件 → 自己的位置 +1

2. 发送消息 → 自己位置 +1，然后带上整个向量

3. 收到消息 → 逐个取max，然后自己位置 +1
```

---

**举个例子**

```
P1              P2              P3
[0,0,0]         [0,0,0]         [0,0,0]
   |               |               |
事件            |               |
[1,0,0]            |               |
   |               |               |
发送 -------→  收到              |
               max([1,0,0],[0,0,0])+1
               [1,1,0]             |
                  |               |
               发送 --------→  收到
                              max([1,1,0],[0,0,1])+1
                              [1,1,1]
```

![](https://img.164314.xyz/2025/12/6671ab14fa01dfc25b3f7ead9d9b54da.png)

![](https://img.164314.xyz/2025/12/157625d4fe2122cb2d7aa67a179d4d21.png)



## Exclusión mutua

Propósito: Asegurar que solo un proceso pueda acceder a una sección crítica a la vez.

En un sistema distribuido, no tenemos memoria compartida ni un reloj global, por lo que debemos coordinar el acceso a la sección crítica mediante mensajes.

### Algoritmo de exclusión mutua 

3 propiedades clave: 
1. **Correctitud**: Solo un proceso puede estar en la sección crítica a la vez.(Seguridad)
2. **Vivacidad**: Si un proceso desea entrar en la sección crítica, eventualmente lo hará.(Liveness)
3. **Justicia**: Todos los procesos tienen la misma oportunidad de acceder a la sección crítica.(Fairness). Generalmente utiliza el orden causal(Lamport) o FIFO. 

Se divide en 3 categorías:

1. **Algoritmos centralizados**: Un proceso central coordina todas las peticiones de entrada y salida de la sección crítica.
2. **Algoritmos basado en Token(Token ring)**: Un único token(un mensaje especial) circula por el sistema. 
3. **Algoritmos distribuidos**: No hay token ni proceso central. Los procesos se comunican entre sí para coordinar el acceso a la sección crítica.Ej: Algoritmo de Ricart-Agrawala y Lamport.


#### Algoritmo centralizado

1. El proceso 1 envía una petición al coordinador 
2. El coordinador gestiona una cola de peticiones. si está libre, envía un mensaje indicando permiso para entrar, si está ocupado, encola la petición y responde con una denegación.
3. Liberación: El proceso P notifica al coodinador cuando ha terminado, enviando un **RELEASE**, si no enviará este mensaje, el sistema se bloqueará(deadlock).
4. Al recibir el RELEASE, el coordinador atiende la siguiente petición en la cola.

**Ventajas:**
- Simplicidad: Fácil de implementar y entender.
- Eficiencia: Menor cantidad de mensajes(REQ, OK, RELEASE) en comparación con otros algoritmos.

**Desventajas:**
- Punto único de fallo: Si el coordinador falla, todo el sistema se bloquea.
- Cuenllo de botella

#### Algoritmo con Token ring 

Se crea un mensaje especial llamado token que circula continuamente entre los procesos en un orden lógico predefinido (anillo).

1. El token circula libremente entre los procesos mientra nadíe lo posee.
2. Cuando un proceso desea entrar en la sección crítica, espera a recibir el token.
3. Al recibir el token, el proceso entra en la sección crítica.
4. Al salir de la sección crítica, el proceso pasa el token al siguiente proceso en el anillo.

**Ventajas:**
- Sin inianicion(No startvation)

**Desventajas:**
- Perdida del token(ej: un nodo falla)
- Ineficiencia en baja carga

#### Algoritmo de Ricart-Agrawala

Un algoritmo que no requiere un coordinador ni un token.

1. El proceso P_i genera una marca de tiempo de Lamport. 
2. Envía un mensaje de petición (REQUEST) a todos los demás procesos con su marca de tiempo.
3. Espera respuestas (REPLY) de todos los demás procesos antes de entrar en la sección crítica.
   1. Caso 1: Si un proceso P_j no está en la SC y no quiere entrar, respoder OK
   2. Caso 2: Si P_j está en la SC, encola la petición de P_i y responde cuando salga.
   3. Caso 3: Si P_j quiere entrar en la SC pero la marca de tiempo de P_i es menor que la de P_j, responde OK. Si es mayor, encola la petición de P_i y responde cuando salga.
4. Al salir de la sección crítica , P_i envía mensajes REPLY a todos los procesos encolados.

**Ventajas:**
- Correctitud, vivacidad y justicia garantizadas.
- No hay punto único de fallo.

**Desventajas:**
- Alta sobrecarga de mensajes: Requiere 2(N-1) mensajes para cada entrada y salida de la sección crítica.
- No es tolerante a fallos: Si un proceso falla, puede bloquear el sistema.

![](https://img.164314.xyz/2025/12/43b0c3046aef50f718dbf22162654bfd.png)

### Cómo elegir el lider de un algoritmo centralizado en un sistema distribuido?

Cuando un lider falla, todos los procesos se detiense. Para un sistema que sea robusto, necesita un que sea capaz de elegir un nuevo lider cuando el actual falla.


#### Algoritmo de Garcia-Molina(Bully election)

**Bully 选举算法**

是什么？

一种**选举协调者**的算法。规则很简单：**ID最大的当老大**。

---

**触发条件**

当进程发现**协调者挂了**（超时无响应），就发起选举。

---

**三种消息**

| 消息 | 作用 |
|------|------|
| Election | 发起选举 |
| OK | 回应"我比你大，我来" |
| Coordinator | 宣布"我是老大" |

---

**算法流程**

```
1. 进程P发现协调者挂了
2. P向所有ID比自己大的进程发 Election
3. 等待回复：
   - 收到OK → 等别人选出结果
   - 没收到OK → 我最大，我当老大
4. 新老大向所有人发 Coordinator
```

---

**例子**

```
进程: P1, P2, P3, P4, P5(协调者)

P5挂了，P2发现：

P2 → P3, P4, P5 发 Election
P3 → OK 给 P2
P4 → OK 给 P2

P3 → P4, P5 发 Election
P4 → OK 给 P3

P4 → P5 发 Election
无回复

P4 成为新协调者
P4 → 所有人发 Coordinator
```


**优缺点**

| 优点 | 缺点 |
|------|------|
| 简单直接 | 消息多 O(n²) |
| 保证选出最大ID | 大ID进程频繁崩溃会反复选举 |

**一句话：谁ID大谁当老大，小的别争。**

#### Algoritmo cooperativo(no bully)


另一种选举算法，进程组成**逻辑环**，通过传递消息选举协调者。


**与 Bully 的对比**

| Bully | Ring (No-Bully) |
|-------|-----------------|
| 向上挑战 | 沿环传递 |
| 大欺小 | 平等传递 |
| 消息多 O(n²) | 消息少 O(n) |


**算法流程**

```
1. 进程P发现协调者挂了
2. P创建消息 [P的ID]，发给下一个存活进程
3. 每个进程收到后，把自己ID加入列表，继续传
4. 消息转一圈回到发起者
5. 发起者选出列表中最大ID作为协调者
6. 发送 Coordinator 消息绕环通知所有人
```

---

**例子**

```
进程: P1, P2, P3, P4（P5挂了）

P2 发现协调者挂了，发起选举：

P2 → P3: [2]
P3 → P4: [2, 3]
P4 → P1: [2, 3, 4]
P1 → P2: [2, 3, 4, 1]

P2 收到，选出 max = 4
P2 → 通知：P4 是新协调者
```

---

**消息复杂度**

| 阶段 | 消息数 |
|------|--------|
| 选举轮 | N |
| 通知轮 | N |
| **总计** | **2N** |

---

**优缺点**

| 优点 | 缺点 |
|------|------|
| 消息少 O(n) | 需要维护环结构 |
| 简单公平 | 环中断要修复 |

---

**一句话：大家排队传名单，转一圈选出最大的。**


#### Algoritmo de Chang-Roberts


Ring 选举的**优化版**，核心思想：**提前过滤掉不可能赢的消息**。


**与普通 Ring 对比**

| 普通 Ring | Chang & Roberts |
|-----------|-----------------|
| 收集所有ID | 只传递最大ID |
| 消息固定 2N | 消息最少 N，平均 O(N logN) |

---

**核心规则**

收到选举消息时：

```
如果 收到的ID > 我的ID：
    继续传递该消息
    
如果 收到的ID < 我的ID：
    丢弃，发送自己的ID
    
如果 收到的ID == 我的ID：
    我赢了！发 Coordinator 消息
```

---

**例子**

```
进程: P1, P2, P3, P4（顺时针）

P2 发起选举，发送 [2]

P2 → P3: [2]
P3 收到：3 > 2，丢弃，发 [3]

P3 → P4: [3]
P4 收到：4 > 3，丢弃，发 [4]

P4 → P1: [4]
P1 收到：1 < 4，继续传 [4]

P1 → P2: [4]
P2 收到：2 < 4，继续传 [4]

P2 → P3: [4]
P3 收到：3 < 4，继续传 [4]

P3 → P4: [4]
P4 收到：4 == 4，我是协调者！
P4 发 Coordinator 绕环通知
```

---

**消息复杂度分析**

| 情况 | 消息数 |
|------|--------|
| 最好 | N（最大ID发起） |
| 最坏 | 2N-1（最小ID发起） |
| 平均 | O(N logN) |

---

**优点**

```
✓ 消息量更少
✓ 小ID消息被过滤掉
✓ 网络负担小
```

---

**一句话：只传最大的，小的自动淘汰。**

#### Algoritmo de Raft

一种**分布式共识算法**，用于保证多个节点数据一致，比 Paxos 更易理解。

---

**三种角色**

| 角色 | 作用 |
|------|------|
| **Leader** | 老大，处理所有请求 |
| **Follower** | 跟随者，听老大的 |
| **Candidate** | 候选人，想当老大 |

---

**两个核心机制**

**1. Leader 选举**
**2. 日志复制**

---

**Leader 选举流程**

```
正常状态：
Leader 定期发心跳 → Follower

选举触发：
Follower 超时没收到心跳 → 变成 Candidate

选举过程：
1. Candidate 增加任期号（term）
2. 投票给自己
3. 向其他节点请求投票
4. 收到多数票 → 成为 Leader
   否则 → 重新选举
```

---

**投票规则**

```
每个任期只能投一票
先到先得
日志要够新才能投
```

---

**日志复制流程**

```
1. 客户端请求 → Leader
2. Leader 写入本地日志
3. Leader 发给所有 Follower
4. 多数确认 → 提交
5. 通知 Follower 提交
6. 回复客户端
```

---

**简单图示**

```
正常运行：
┌────────┐  心跳  ┌──────────┐
│ Leader │ ────→ │ Follower │
└────────┘       └──────────┘

选举：
┌───────────┐  请求投票  ┌──────────┐
│ Candidate │ ────────→ │ Follower │
└───────────┘  ←──────── └──────────┘
                 投票
```

---

**任期（Term）**

```
Term 1    Term 2    Term 3
选举|正常运行|选举|正常运行|选举...

- 每次选举 term+1
- Term 大的说了算
- 发现更大 term → 变回 Follower
```

---

**与其他算法对比**

| 算法 | 用途 | 特点 |
|------|------|------|
| Bully | 选举 | 简单，ID大当老大 |
| Ring | 选举 | 传递消息 |
| Raft | 共识+选举 | 多数投票，日志复制 |
| Paxos | 共识 | 复杂难懂 |

---

**关键特点**

```
✓ 强 Leader：所有操作经过 Leader
✓ 多数原则：超过半数同意才行
✓ 任期机制：防止旧 Leader 捣乱
✓ 易于理解：比 Paxos 简单
```

---

**一句话：选个老大，老大负责同步数据，多数同意才算成功。**

#### Algoritmo de invitación y fusión

![](https://img.164314.xyz/2026/01/cc8f9135342a2e1ffb58adf5a72c31a8.png)

![](https://img.164314.xyz/2026/01/ce5b78620b3fd54c5afc22f448ed66a5.png)

![](https://img.164314.xyz/2026/01/eabcc75088ff3dae52679da07bcfac1f.png)

##### Perdida de lider

Si un proceso detecta la perdida del lider(timeout), se declara a sí mismo candidato y comienza una nueva elección.**(Fase 1: Detección de fallo)**
**Fase 2: Invitación y fusión**: Periodicamente, el lider de cada grupo invita a otros grupos para fusionarse bajo su liderato. 
**Fase 3: Elección de nuevo lider**: Cuando dos lideres se encuentran, comparan sus IDs y el de mayor ID se convierte en el nuevo lider del grupo fusionado.

## El problema de estado global 

Un problema fundamental en sistemas distribuidos es cómo podemos de poner todos de acuerdo sobre el estado global del sistema cuando no existe un reloj global ni memoria compartida.

El estado global de un sistema distribuido se compone de dos partes:

1. El estado local de cada proceso individual.
2. El estado de los canales de comunicación entre los procesos (mensajes en tránsito).

Entonces, cómo podemos capturar una instantánea consistente del estado global?

### Definicion de corte(cut)

Un corte es una línea que divide los eventos del sistema en dos conjuntos: **El pasado y el futuro.**

#### Corte inconsistente

Es una instantánea invalida. Su característica definitoria es que viola la causalidad(happened-before). Es decir, incluye el efecto de un evento sin incluir su causa.

![](https://img.164314.xyz/2025/12/8df58aaa2557d51f0750304867172a75.png)


#### Corte consistente

Un corte consistente es el objetivo de un algoritmo de snapshot. Es una instantánea que respeta la causalidad. Por cada efento(recepción) que se captura en el pasado del core, la causa(envío) también debe estar en el pasado del corte.

![](https://img.164314.xyz/2025/12/c7a7e0906b46dafbc6ec65a1a225906d.png)

![](https://img.164314.xyz/2025/12/3893719305b7b88fbc1134cd85a33369.png)

### Algoritmo de Chandy-Lamport(Algoritmo de snapshot)

El objetivo de siste algoritmo es capturar un corte consistente sin interrumpir la ejecución normal del sistema. 

**Reglas del algoritmo:**

**Iniciador del snapshot:**

1. P_i graba su estado local. 
2. P_i envía un mensaje especial llamado marcador(MARKER) por todos sus canales de salida.
3. P_i empieza a grabar todos los mensajes que lleguen por sus canales de entrado hasta que reciba un marcador por cada canal de entrada.

**Proceso receptor del marcador:**

Al recibir un mensaje Market por un canal C_i:

1. Si es la primera vez que recibe:
   1. Graba su estado local.
   2. Marca el estado del canal C_i como vacío.
   3. Repetir la regla 1
   4. Empieza a grabar todos los mensajes que lleguen por sus canales de entrada excepto C_i.
2. Si ya ha recibido un marcador antes:
   1. Deja de grabar el estado del canal C_i. 
   2. Cierra la grabación del estado del canal C_i.

![](https://img.164314.xyz/2025/12/1361e02378db1fead7a6c05560c5473a.png)

![](https://img.164314.xyz/2025/12/18e486f9f7709f6e4fca96cf7a4898fa.png)

![](https://img.164314.xyz/2025/12/28e8afcb3a15762a5c4b6f3eff5df2eb.png)