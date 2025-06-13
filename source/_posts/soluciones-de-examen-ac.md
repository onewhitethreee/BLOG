---
uuid: 575833a0-47bb-11f0-ad60-2b8b7e448256
title: soluciones de examen ac
date: 2025-06-12 20:30:39
tags: [ac]
---

# Junio 2023

## 1 

![](https://img.164314.xyz/2025/06/582fe3cadf6cf771261bb1233ff8a777.png)

![](https://img.164314.xyz/2025/06/dd0f705e1685a613b69e1bdd5c954835.png)

## 2 

![](https://img.164314.xyz/2025/06/0c9ced8a56726ea2e82544d1b738225c.png)

![](https://img.164314.xyz/2025/06/c6ea8a9914aa7048641aa62bfd8dcc0e.png)

## 5

![](https://img.164314.xyz/2025/06/260da68be0efaeb7335ee2632898ce03.png)

![](https://img.164314.xyz/2025/06/d3e400891141410aff85924dca6fd1ed.png)

# Junio 2024

## 1 

![](https://img.164314.xyz/2025/06/0edab1ecdc4ca9c3c9de6c31eb4938be.png)

![](https://img.164314.xyz/2025/06/90aaef562a5507cd1dfca3b093c4a1b8.png)

## 2 

### a

![](https://img.164314.xyz/2025/06/b9ecfb426e84e35590c706dbe7963bbe.png)

### b

![](https://img.164314.xyz/2025/06/1a85a8af0fd8696654b65fe84d235579.png)

![](https://img.164314.xyz/2025/06/84c99d85924502a34cf9afa6d1def03e.png)

### c

![](https://img.164314.xyz/2025/06/28da15bd2a0e133ef8ca7c8ae4af492a.png)

![](https://img.164314.xyz/2025/06/5cd0f3586aaac89bf85a9fdf0be29246.png)

## 4

![](https://img.164314.xyz/2025/06/94fe14d891dab59739ca8ee86855ae4e.png)

### a

![](https://img.164314.xyz/2025/06/5b8e93b12c15babbc25838dc87df544d.png)

### b

![](https://img.164314.xyz/2025/06/16ce69cf724487b398c2011b0e52cacf.png)

### c

![](https://img.164314.xyz/2025/06/c80a044e88fc11136575436c9c75f5ab.png)

### d

![](https://img.164314.xyz/2025/06/aec1a4011eeb4625ee0552b3373ec73a.png)

![](https://img.164314.xyz/2025/06/1927bee14d93a3cb2d648873d8d03a24.png)

![](https://img.164314.xyz/2025/06/613722b6ea53732938deffbd472353bd.png)

# 2018

## 1

![](https://img.164314.xyz/2025/06/6f2b0904f2a273eae4b2e6e701534eb6.png)

### a

![](https://img.164314.xyz/2025/06/4b5f17cd4e98e33e6f7bae4c47a0cbc4.png)

### c

![](https://img.164314.xyz/2025/06/3982b562060ee5185d333b23e7620273.png)

![](https://img.164314.xyz/2025/06/ff12c0bc2b44ede90e400ebca2fa0c5e.png)

# 2017

## 3

![](https://img.164314.xyz/2025/06/20ebb2fdcaeb12f39578e3e26435782d.png)

![](https://img.164314.xyz/2025/06/da8351ab7a5468189b0459ef5d79910f.png)

## 5 

![](https://img.164314.xyz/2025/06/1402fc4039f539f0ef6e878e12e50d7c.png)

![](https://img.164314.xyz/2025/06/0d8875e35cf44d428bf707c04574d976.png)

# Ejercicios de Práctica: Segmentación en Arquitectura de Computadores

## **Resumen de Conceptos Clave**

### **Definiciones Fundamentales**
- **Segmentación (Pipelining)**: Técnica que permite solapar la ejecución de instrucciones dividiendo el procesamiento en etapas especializadas
- **Paralelismo temporal**: Múltiples instrucciones en diferentes etapas ejecutándose simultáneamente
- **Cauce (Pipeline)**: Estructura segmentada que procesa instrucciones en flujo continuo
- **Etapas MIPS**: IF (Instruction Fetch), ID (Instruction Decode), EX (Execute), MEM (Memory), WB (Write Back)

### **Fórmulas Importantes**
- **Tiempo segmentado**: `T_SEG = k·CLK + (n-1)·CLK = (k + n - 1)·CLK`
- **Tiempo secuencial**: `T_SEC = K·CLK·n`
- **Ganancia de velocidad**: `G_k = T_SEC/T_SEG = (n·k)/(k + n - 1)`
- **Eficiencia**: `E_k = (k·n·CLK)/[k·(k + n - 1)·CLK] = n/(k + n - 1) = G_k/k`
- **Productividad**: `P_k = n/[(k + n - 1)·CLK] = E_k/CLK`
- **Periodo de reloj**: `CLK ≥ max{t_i} + t_r + s` (donde s es el sesgo de reloj)

---

## **EJERCICIOS DE PRÁCTICA**

### **Ejercicio 1: Conceptos Básicos de Segmentación** 
**Nivel: Básico**

**Enunciado**: Explica las diferencias principales entre la ejecución secuencial y la ejecución segmentada. ¿Qué ventajas y desventajas presenta cada enfoque?

**Conceptos involucrados**: Fundamentos de segmentación, paralelismo temporal

**Solución**:

**Ejecución Secuencial:**
- Cada instrucción se ejecuta completamente antes de comenzar la siguiente
- Utiliza todos los recursos durante toda la ejecución de una instrucción
- **Ventajas**: Simplicidad de diseño, ausencia de riesgos de segmentación
- **Desventajas**: Subutilización de recursos, menor rendimiento

**Ejecución Segmentada:**
- Las instrucciones se dividen en etapas que se ejecutan en paralelo temporal
- Diferentes etapas procesan diferentes instrucciones simultáneamente
- **Ventajas**: Mayor productividad, mejor utilización de recursos, mayor rendimiento del sistema
- **Desventajas**: Complejidad de diseño, riesgos de segmentación (estructurales, de datos, de control), necesidad de hardware adicional

**Errores comunes**: Confundir paralelismo temporal (segmentación) con paralelismo espacial (múltiples procesadores)

---

### **Ejercicio 2: Cálculo de Prestaciones Básico**
**Nivel: Básico**

**Enunciado**: Un procesador segmentado tiene 4 etapas con tiempos de 15ns, 20ns, 18ns y 12ns respectivamente. El retardo de los registros intermedios es de 2ns. Calcula:
a) El periodo mínimo de reloj
b) El tiempo para ejecutar 8 instrucciones de forma segmentada
c) El tiempo que tomaría ejecutar las mismas instrucciones secuencialmente

**Datos proporcionados**: 
- k = 4 etapas
- Tiempos: t₁=15ns, t₂=20ns, t₃=18ns, t₄=12ns
- Retardo registros: t_r = 2ns
- n = 8 instrucciones

**Conceptos involucrados**: Análisis de prestaciones, periodo de reloj, comparación secuencial vs segmentado

**Solución paso a paso**:

**a) Periodo mínimo de reloj:**
```
CLK ≥ max{t_i} + t_r = max{15, 20, 18, 12} + 2 = 20 + 2 = 22ns
```

**b) Tiempo segmentado para 8 instrucciones:**
```
T_SEG = (k + n - 1) × CLK = (4 + 8 - 1) × 22 = 11 × 22 = 242ns
```

**c) Tiempo secuencial:**
```
Tiempo por instrucción = Σt_i = 15 + 20 + 18 + 12 = 65ns
T_SEC = n × 65 = 8 × 65 = 520ns
```

**Conceptos teóricos aplicados**: El periodo de reloj está limitado por la etapa más lenta. La segmentación reduce significativamente el tiempo total.

---

### **Ejercicio 3: Análisis de Ganancia y Eficiencia**
**Nivel: Intermedio**

**Enunciado**: Para el procesador del ejercicio anterior, calcula la ganancia de velocidad y la eficiencia cuando se procesan 8, 50 y 1000 instrucciones. Analiza qué sucede cuando n tiende a infinito.

**Datos proporcionados**: Usar datos del ejercicio 2
**Lo que se pide encontrar**: G_k para diferentes valores de n, E_k, y límites

**Conceptos involucrados**: Ganancia de velocidad, eficiencia, análisis asintótico

**Solución paso a paso**:

**Para n = 8:**
```
G_k = (n × k)/(k + n - 1) = (8 × 4)/(4 + 8 - 1) = 32/11 = 2.91
E_k = G_k/k = 2.91/4 = 0.73 = 73%
```

**Para n = 50:**
```
G_k = (50 × 4)/(4 + 50 - 1) = 200/53 = 3.77
E_k = 3.77/4 = 0.94 = 94%
```

**Para n = 1000:**
```
G_k = (1000 × 4)/(4 + 1000 - 1) = 4000/1003 = 3.99
E_k = 3.99/4 = 0.998 = 99.8%
```

**Cuando n → ∞:**
```
lim(n→∞) G_k = k = 4
lim(n→∞) E_k = 1 = 100%
```

**Conceptos teóricos aplicados**: La ganancia se aproxima al número de etapas conforme aumenta n. La eficiencia mejora con más instrucciones procesadas.

**Errores comunes**: No considerar que la ganancia máxima teórica es k, no el número de instrucciones.

---

### **Ejercicio 4: Riesgos de Segmentación**
**Nivel: Intermedio**

**Enunciado**: Identifica y clasifica los riesgos de segmentación en las siguientes situaciones:
a) Dos instrucciones consecutivas intentan acceder a memoria simultáneamente
b) Una instrucción depende del resultado de la instrucción anterior
c) Se ejecuta una instrucción de salto condicional
d) El banco de registros solo tiene un puerto de escritura

**Conceptos involucrados**: Riesgos estructurales, de datos y de control

**Solución paso a paso**:

**a) Riesgo estructural**
- **Tipo**: Conflicto de recursos hardware
- **Causa**: Memoria con un solo puerto de acceso
- **Solución**: Memorias separadas para instrucciones y datos, o memoria multipuerto

**b) Riesgo por dependencias de datos**
- **Tipo**: Dependencia RAW (Read After Write)
- **Causa**: Una instrucción necesita un dato que la anterior aún no ha escrito
- **Solución**: Adelantamiento (forwarding), inserción de NOPs, o reordenación

**c) Riesgo de control**
- **Tipo**: Dependencia de flujo de control
- **Causa**: Desconocimiento del destino del salto hasta la etapa EX
- **Solución**: Predicción de saltos, ejecución especulativa, retraso de salto

**d) Riesgo estructural**
- **Tipo**: Conflicto en recurso de escritura
- **Causa**: Múltiples instrucciones intentan escribir simultáneamente
- **Solución**: Múltiples puertos de escritura o arbitraje temporal

**Errores comunes**: Confundir los tipos de riesgos o no identificar correctamente las soluciones hardware vs software.

---

### **Ejercicio 5: Optimización de Cauce**
**Nivel: Intermedio**

**Enunciado**: Un diseñador tiene un procesador con 6 etapas de tiempos: 25ns, 30ns, 45ns, 20ns, 15ns, 35ns. Propone dividir la etapa más lenta en dos etapas de 22ns y 23ns. Analiza si esta modificación mejora el rendimiento para procesar 100 instrucciones.

**Datos proporcionados**: 
- Etapas originales: 6 con tiempos dados
- Modificación propuesta: dividir etapa de 45ns
- n = 100 instrucciones

**Conceptos involucrados**: Optimización de pipeline, análisis coste-beneficio

**Solución paso a paso**:

**Configuración original:**
```
CLK_original = max{25, 30, 45, 20, 15, 35} = 45ns
k_original = 6
T_SEG_original = (6 + 100 - 1) × 45 = 105 × 45 = 4725ns
```

**Configuración modificada:**
```
CLK_nuevo = max{25, 30, 22, 23, 20, 15, 35} = 35ns
k_nuevo = 7
T_SEG_nuevo = (7 + 100 - 1) × 35 = 106 × 35 = 3710ns
```

**Análisis de mejora:**
```
Mejora = (4725 - 3710)/4725 × 100% = 21.5%
```

**Conclusión**: La modificación mejora significativamente el rendimiento a pesar de añadir una etapa, ya que reduce considerablemente el periodo de reloj.

**Conceptos teóricos aplicados**: El cuello de botella determina la frecuencia máxima. Equilibrar las etapas es crucial para optimizar el rendimiento.

---

### **Ejercicio 6: Cauce MIPS Detallado**
**Nivel: Avanzado**

**Enunciado**: Analiza la ejecución de la siguiente secuencia de instrucciones en un procesador MIPS segmentado:
```
I1: ADD R1, R2, R3
I2: SUB R4, R1, R5  
I3: LW R6, 0(R1)
I4: SW R6, 4(R4)
```
Identifica dependencias y determina si se necesitan detenciones del cauce.

**Conceptos involucrados**: Pipeline MIPS, dependencias de datos, adelantamiento

**Solución paso a paso**:

**Análisis de dependencias:**

1. **I2 depende de I1** (R1): RAW sobre R1
   - I1 produce R1 en WB (ciclo 5)
   - I2 necesita R1 en ID (ciclo 3)
   - **Solución**: Adelantamiento desde EX/MEM o MEM/WB

2. **I3 depende de I1** (R1): RAW sobre R1 para dirección
   - Similar al caso anterior
   - **Solución**: Adelantamiento desde EX/MEM

3. **I4 depende de I2** (R4): RAW sobre R4 para dirección
   - I2 produce R4 en WB (ciclo 6)
   - I4 necesita R4 en EX (ciclo 6)
   - **Solución**: Adelantamiento desde MEM/WB

4. **I4 depende de I3** (R6): RAW sobre R6 para dato
   - I3 produce R6 en WB (ciclo 7)
   - I4 necesita R6 en MEM (ciclo 7)
   - **Problema**: Dependencia LW-SW requiere detención

**Cronograma con detenciones:**
```
Ciclo: 1  2  3  4  5  6  7  8  9
I1:   IF ID EX MEM WB
I2:   -- IF ID EX  MEM WB
I3:   -- -- IF ID  EX  MEM WB
I4:   -- -- -- IF  ID  ID  EX MEM WB
```

**Conceptos teóricos aplicados**: Las dependencias RAW pueden resolverse con adelantamiento, pero las dependencias de carga requieren detenciones.

---

### **Ejercicio 7: Comparación de Arquitecturas**
**Nivel: Avanzado**

**Enunciado**: Compara el rendimiento de tres arquitecturas para ejecutar 1000 instrucciones:
- A: Procesador secuencial, 200ns por instrucción
- B: Procesador segmentado de 5 etapas, 50ns por etapa
- C: Procesador segmentado de 10 etapas, 25ns por etapa

Incluye análisis de ganancia, eficiencia y productividad.

**Conceptos involucrados**: Análisis comparativo, métricas de rendimiento

**Solución paso a paso**:

**Arquitectura A (Secuencial):**
```
T_A = 1000 × 200ns = 200,000ns
```

**Arquitectura B (5 etapas):**
```
CLK_B = 50ns
T_B = (5 + 1000 - 1) × 50 = 1004 × 50 = 50,200ns
G_B = 200,000/50,200 = 3.98
E_B = 3.98/5 = 0.796 = 79.6%
P_B = 1000/50,200 = 19.92 instrucciones/μs
```

**Arquitectura C (10 etapas):**
```
CLK_C = 25ns  
T_C = (10 + 1000 - 1) × 25 = 1009 × 25 = 25,225ns
G_C = 200,000/25,225 = 7.93
E_C = 7.93/10 = 0.793 = 79.3%
P_C = 1000/25,225 = 39.64 instrucciones/μs
```

**Análisis comparativo**:
- **Rendimiento**: C > B > A
- **Eficiencia**: Similares B y C (~79-80%)
- **Productividad**: C duplica a B, ambos superan ampliamente a A

**Conclusión**: Más etapas permiten mayor frecuencia y mejor rendimiento, pero con eficiencia similar.

---

### **Ejercicio 8: Diseño de Cauce Aritmético**
**Nivel: Avanzado**

**Enunciado**: Diseña un sumador segmentado de 32 bits dividido en 4 etapas. Si cada sumador de 8 bits toma 15ns y los registros intermedios 3ns, calcula el rendimiento para sumar 500 pares de números comparado con un sumador secuencial de 60ns.

**Datos proporcionados**:
- 32 bits divididos en 4 etapas de 8 bits
- Tiempo por etapa: 15ns
- Retardo registros: 3ns
- Sumador secuencial: 60ns
- n = 500 operaciones

**Conceptos involucrados**: Cauces aritméticos, segmentación de operaciones

**Solución paso a paso**:

**Sumador segmentado:**
```
CLK = 15 + 3 = 18ns
T_segmentado = (4 + 500 - 1) × 18 = 503 × 18 = 9,054ns
```

**Sumador secuencial:**
```
T_secuencial = 500 × 60 = 30,000ns
```

**Análisis de prestaciones:**
```
Ganancia = 30,000/9,054 = 3.31
Eficiencia = 3.31/4 = 0.828 = 82.8%
Productividad_seg = 500/9,054 = 55.23 sumas/μs
Productividad_sec = 500/30,000 = 16.67 sumas/μs
```

**Conclusión**: El sumador segmentado es 3.31 veces más rápido y tiene una eficiencia del 82.8%.

**Conceptos teóricos aplicados**: La segmentación aritmética permite acelerar operaciones largas dividiéndolas en etapas más simples.

---

## **Lista de Verificación para el Examen**

### **Conceptos Fundamentales**
- [ ] Definición y principios de la segmentación
- [ ] Diferencias entre paralelismo temporal y espacial
- [ ] Etapas del pipeline MIPS (IF, ID, EX, MEM, WB)
- [ ] Clasificación de arquitecturas segmentadas

### **Fórmulas Esenciales**
- [ ] Periodo de reloj: `CLK ≥ max{t_i} + t_r + s`
- [ ] Tiempo segmentado: `T_SEG = (k + n - 1)×CLK`
- [ ] Ganancia de velocidad: `G_k = n×k/(k + n - 1)`
- [ ] Eficiencia: `E_k = G_k/k`
- [ ] Productividad: `P_k = E_k/CLK`

### **Riesgos de Segmentación**
- [ ] **Estructurales**: Conflictos de recursos hardware
- [ ] **De datos**: Dependencias RAW, WAR, WAW
- [ ] **De control**: Saltos y bifurcaciones

### **Técnicas de Optimización**
- [ ] Adelantamiento (forwarding)
- [ ] Predicción de saltos
- [ ] Reordenación de instrucciones
- [ ] Inserción de NOPs

### **Consejos para el Examen**
1. **Identifica el tipo de problema**: cálculo de prestaciones, análisis de riesgos, o diseño
2. **Dibuja diagramas temporales** para problemas de dependencias
3. **Verifica las unidades** en todos los cálculos
4. **Analiza el contexto** antes de aplicar fórmulas
5. **Considera los casos límite** (n→∞) en análisis de rendimiento