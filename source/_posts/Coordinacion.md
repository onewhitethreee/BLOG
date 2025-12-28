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

1.  NTP
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

#### Qué es lo que no podemos saber? 

Los relojes de Lamport garantiza la condición de reloj lógico: Si a -> b, entonces L(a) < L(b). Pero no garantiza la condición inversa: Si L(a) < L(b), no necesariamente a -> b.

Por qué? Porque los relojes lógicos no capturan la concurrencia de eventos. Dos eventos pueden tener valores de reloj lógico diferentes sin que exista una relación de orden entre ellos.

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

![](https://img.164314.xyz/2025/12/6671ab14fa01dfc25b3f7ead9d9b54da.png)


## Exclusión mutua 


