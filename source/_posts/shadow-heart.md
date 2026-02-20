---
uuid: 2c8cbd50-0eb0-11f1-9a67-2781ab18abd9
title: shadow_heart
date: 2026-02-21 00:02:04
tags:
---

# Plan de Negocio — Shadow Heart

## 1. Resumen ejecutivo

Las personas mayores de 70 años enfrentan una barrera silenciosa: la monitorización del pulso requiere wearables que no comprenden, dispositivos médicos que no poseen o visitas hospitalarias que no pueden realizar con frecuencia. Shadow Heart resuelve este problema mediante tecnología rPPG (fotopletismografía remota), que estima la frecuencia cardíaca a partir de la cámara frontal del teléfono móvil — sin contacto, sin hardware adicional, sin curva de aprendizaje.

Nuestra propuesta de valor diferencial se resume en tres ceros: **cero hardware** (solo un smartphone), **cero aprendizaje** (un botón con forma de corazón que ocupa el 80% de la pantalla) y **cero frialdad** (los resultados se expresan con emojis, colores y voz, no con datos clínicos). El producto no es una herramienta médica: es un **vínculo emocional familiar** donde cada medición es un "mamá está bien" silencioso que llega al hijo/a.

El prototipo actual (nivel TRL 4) es una app Flutter con pipeline rPPG funcional: captura de cámara nativa → detección facial MediaPipe → extracción ROI → algoritmo POS en C++ vía FFI → estimación BPM con puntuación de calidad. Incluye pantalla de resultados emocional, historial cifrado local (Drift + SQLCipher), gráfico de tendencias y sistema de tolerancia a fallos progresivo ("nunca dice fallo").

**Buscamos:** partners piloto en teleasistencia y residencias de mayores, acceso a entornos de validación con personas mayores reales, mentoría en regulación sanitaria, y financiación semilla para completar la validación ampliada 

---

## 2. El reto y el contexto

### 2.1 Problema a resolver

La monitorización regular del pulso en personas mayores presenta fricciones críticas:

**Adherencia:** Los wearables (smartwatches, pulseras de actividad) requieren carga diaria, mantenimiento y comprensión de interfaces táctiles pequeñas. 

**Comodidad:** Los pulsioxímetros de dedo y los tensiómetros digitales requieren colocación precisa, calibración, y lectura de pantallas pequeñas. Para una persona de 72 años con artritis leve o temblor en las manos, el simple acto de colocar un clip en el dedo puede generar frustración y abandono.

**Coste:** Un Apple Watch (la solución wearable más completa) cuesta 200€-399€. Un sistema de telemonitorización domiciliaria profesional puede costar 50€-1500€/mes(Valor estimado). 

El resultado: **millones de personas mayores quedan sin monitorización regular**, y sus familias viven con una preocupación constante que no pueden resolver fácilmente.

### 2.2 Por qué ahora

Tres fuerzas convergen para crear una oportunidad sin precedentes:

**Smartphones ubicuos:** A diferencia de hace 5 años, hoy incluso personas mayores de bajo poder adquisitivo poseen smartphones (modelos Xiaomi/Samsung básicos con cámaras frontales capaces). El hardware necesario para rPPG ya está en el bolsillo del usuario.

**Madurez del rPPG:** Los algoritmos de fotopletismografía remota (POS, CHROM, ICA) han alcanzado precisión ≤±5 BPM en condiciones controladas. La investigación académica (Wang et al., de Haan & Jeanne) y las implementaciones comerciales (Binah.ai, Nuralogix) han demostrado la viabilidad técnica. Lo que falta es una implementación centrada en el usuario más vulnerable.

**Cambio de paradigma en salud:** La pandemia aceleró la aceptación de la teleasistencia y el seguimiento remoto. Los sistemas de salud buscan activamente soluciones de bajo coste para descongestionar la atención primaria y prevenir hospitalizaciones evitables.

---

## 3. Colectivo vulnerable y necesidades reales

### 3.1 Perfil de la persona usuaria

**Persona principal — Carmen, 72 años, persona mayor autónoma**
- Vive sola en Barcelona. Su hija Isabel (38 años) vive en Murcia.
- Usa el teléfono para llamadas, WhatsApp (solo mensajes de voz) y mirar fotos de los nietos.
- Hipertensión leve. Nunca se acordaría de medir su pulso si nadie se lo recuerda.
- Cualquier mensaje de error en pantalla le genera miedo: "lo rompí".
- **Necesidad real:** No necesita "medir su frecuencia cardíaca". Necesita sentirse cuidada y dar tranquilidad a su hija.

**Subperfiles:**

| Perfil | Características | Barreras específicas |
|---|---|---|
| Mayor autónomo/a | Vive solo/a, maneja básicamente el móvil | Brecha digital, olvido, falta de motivación para autocuidado |
| Mayor con apoyo | Familiar o cuidador/a presencial parcial | Dependencia del cuidador para configurar/resolver problemas |
| Cuidador/a familiar | Hijo/a o familiar remoto, 30-50 años | Preocupación constante, necesidad de información sin ser invasivo |
| Personal de teleasistencia | Profesional que monitorea múltiples usuarios | Necesita herramientas escalables, alertas priorizadas |
| Centro de día/residencia | Personal de atención con varios residentes | Necesita medición rápida, no intrusiva, sin hardware adicional por persona |


### 3.2 Escenarios de uso prioritarios

**Escenario 1 — Control de bienestar diario en casa:**
Carmen recibe una notificación suave cada mañana. Pulsa el botón corazón. En 30 segundos obtiene su resultado con una cara sonriente. Isabel recibe un SMS: "Mamá hoy 68 BPM, todo normal ". Toda la interacción dura menos de 30 segundos.

**Escenario 2 — Seguimiento tras episodio cardiovascular:**
Tras un ingreso por taquicardia, el médico recomienda monitorización diaria. En lugar de un Holter incómodo de 24h o visitas semanales, Carmen usa la aplicación varias veces al día. Si la frecuencia cardíaca supera el umbral, el sistema avisa automáticamente a Isabel y al contacto de emergencia.

**Escenario 3 — Apoyo a servicios de teleasistencia:**
Una operadora de teleasistencia puede indicar a los usuarios que descarguen la app. Las mediciones se reportan vía SMS o (en futuras versiones) panel web. Se reduce la necesidad de llamadas rutinarias de seguimiento.

**Escenario 4 — Campañas de prevención comunitaria:**
Asociaciones de mayores o centros de salud comunitarios pueden usar la app en talleres de salud. Sin necesidad de equipos médicos, cada asistente puede medir su pulso desde su propio móvil.

### 3.3 Riesgos y límites de uso

**Alcance declarado: Producto de bienestar general (General Wellness), NO dispositivo médico.**

Shadow Heart **no** diagnostica, **no** trata, **no** sustituye opinión médica. Es una herramienta de seguimiento y bienestar que ayuda a identificar tendencias y generar conciencia sobre la salud cardiovascular.

**Disclaimers integrados en el producto:**
- Cada pantalla de resultados muestra: *"Este resultado es solo de referencia. Si no te sientes bien, consulta a tu médico."* (texto de 18sp, siempre visible)
- La app nunca usa palabras como "diagnóstico", "tratamiento" o "prescripción"
- Los resultados se presentan como valores de referencia, no como mediciones clínicas

**Derivación a profesionales:**
- Resultado amarillo (100-110 BPM): sugerencia de descanso, sin alarma
- Resultado rojo (>110 o <50 BPM): doble medición de confirmación → SMS automático a familiar → botón de llamada de emergencia con un toque
- No se sustituye nunca la acción profesional: el sistema **facilita** la comunicación de la situación al entorno de apoyo

---

## 4. Propuesta de solución y valor diferencial

### 4.1 Qué es Shadow Heart

Shadow Heart es una aplicación móvil que utiliza **fotopletismografía remota (rPPG)** para estimar la frecuencia cardíaca a partir de la cámara frontal del smartphone. La tecnología rPPG detecta variaciones microscópicas en el color de la piel facial causadas por el flujo sanguíneo pulsátil. Mediante el algoritmo POS (Plane-Orthogonal-to-Skin), filtrado de paso de banda (0.7-4.0 Hz) y detección de picos, se calcula el BPM en tiempo real.

**Funcionamiento en 3 pasos:**
1. La persona mira la cámara frontal (como si fuera a hacer una videollamada)
2. El sistema detecta el rostro, localiza la región de interés (frente y la mejilla) y recoge señal RGB durante 10-15 segundos
3. Muestra el resultado con un emoji, un colo. 

**Sin contacto. Sin wearable. Sin aprendizaje.**

La app se construye con Flutter, con el núcleo de procesamiento de señales en C++ nativo (vía FFI) para garantizar rendimiento ≤5ms por frame, y detección facial mediante MediaPipe.

### 4.2 Beneficios clave

1. **Cero fricción:** Un botón, 15 segundos, resultado con voz. No hay nada que aprender, cargar, colocar ni mantener.
2. **Cero coste adicional:** Solo necesita el smartphone que la persona ya posee. Sin hardware, sin suscripciones (para la persona mayor).
3. **Privacidad por diseño:** Todo el procesamiento ocurre en el dispositivo. No se almacena vídeo. Base de datos local cifrada (SQLCipher). Cero transmisión a la nube.

### 4.3 Alcance del MVP (nivel TRL 4)

**Lo que HACE el MVP actual:**

| Funcionalidad | Estado |
|---|---|
| Detección de frecuencia cardíaca por rPPG (cámara frontal) | ✅ Implementado |
| Interfaz de un solo botón para persona mayor | ✅ Implementado |
| Tolerancia a fallos progresiva (luz, cara perdida, temblor) | ✅ Implementado |
| Resultados emocionales (emoji + color + frase + voz) | ✅ Implementado |
| Historial cifrado local con gráfico de tendencias | ✅ Implementado |

**Lo que NO hace el MVP actual:**

| Funcionalidad | Estado | Planificado |
|---|---|---|
| Notificaciones de recordatorio programadas | Pendiente | (2 meses) |
| SMS de emergencia automático | Pendiente |  (2 meses) |
| Resumen diario SMS para familiares | Pendiente |  (2 meses) |
| Panel de familiar/cuidador | No incluido |  (2 meses) |
| Grabación de voz familiar para resultados | No incluido | Phase 2 |
| Sistema de logros/gamificación | No incluido | Phase 2 |
| Alertas escalonadas (amarillo/naranja/rojo) | No incluido | Phase 2 |
| Validación clínica formal | No realizado | 6-12 meses |

### 4.4 Evidencia de funcionamiento

El pipeline rPPG ha sido implementado y verificado a nivel técnico. La validación con referencia clínica está planificada como siguiente hito.

**Resultados técnicos preliminares (desarrollo interno):**

| Métrica | Definición | Resultado actual | Objetivo |
|---|---|---|---|
| MAE (BPM) | Error medio absoluto vs pulsioxímetro | [Pendiente validación formal] | ≤ 5 BPM |
| RMSE (BPM) | Raíz del error cuadrático medio | [Pendiente validación formal] | ≤ 7 BPM |
| % lecturas válidas | Quality score > umbral (0.7) | [Pendiente validación formal] | ≥ 95% (100-500 lux) |
| Tiempo a estabilidad | Segundos hasta BPM estable | 10-15 seg (observado) | ≤ 15 seg |

**Validación técnica completada:**
- CameraX → MediaPipe → ROI → C++ POS → BPM + confidence
- Procesamiento C++ FFI: ≤5ms por frame confirmado
- Rango de frecuencia cubierto: 0.7-4.0 Hz (42-240 BPM)
- Funcionamiento 100% offline 

**Plan de validación formal (próximos 3 meses):**
- 50 mediciones comparativas con pulsioxímetro certificado
- Diversidad de tonos de piel
- Condiciones de iluminación
- Población: personas mayores 65+ años
- Plataformas: Android y IOS

---

## 5. Benchmarking y alternativas

### 5.1 Wearables (smartwatches/pulseras)

| Aspecto | Evaluación para personas mayores |
|---|---|
| **Precisión** | Alta (sensor óptico directo), validación médica en modelos premium |
| **Adherencia** | Baja en >65 años: carga diaria, olvido de colocación, incomodidad |
| **Usabilidad** | Pantallas pequeñas (<2"), texto ilegible, navegación compleja con corona/botones laterales |
| **Coste** | Alto: €249-€799 (Apple Watch), €100-€300 (Fitbit/Samsung). Barrera económica significativa |
| **Mantenimiento** | Carga cada 1-2 días, actualizaciones de firmware, emparejamiento Bluetooth |
| **Autonomía** | Requiere smartphone companion configurado, cuenta de usuario, app del fabricante |

**Conclusión:** Los wearables están diseñados para adultos activos tecnológicamente competentes. 

### 5.2 Dispositivos médicos / telemonitorización

| Aspecto | Evaluación para personas mayores |
|---|---|
| **Precisión** | Máxima: certificación médica|
| **Adherencia** | Media: sesiones programadas pero requieren colocación de sensores |
| **Usabilidad** | Requiere formación, visitas de instalación, soporte técnico |
| **Coste** | Muy alto: €50-€150/mes por usuario, hardware dedicado |
| **Logística** | Instalación profesional, mantenimiento de equipos, conectividad |
| **Acceso** | Limitado a programas de salud específicos o poder adquisitivo alto |

**Conclusión:** Excelente precisión, pero la fricción logística y el coste los hacen inaccesibles para monitorización rutinaria de bienestar a escala.

### 5.3 Soluciones rPPG existentes

| Solución | Posicionamiento | Público | Privacidad | UX para mayores |
|---|---|---|---|---|
| **Binah.ai** | SDK B2B para empresas | Desarrolladores/empresas | Procesamiento cloud | No diseñada para usuarios finales |
| **Nuralogix** | Plataforma B2B de evaluación de salud | Corporativo, aseguradoras | Requiere transmisión de vídeo | Sin enfoque geriátrico |
| **Google Health Studies** | Investigación | Voluntarios de investigación | Datos a Google | App de investigación, no producto |

**Conclusión:** Ninguna solución rPPG existente combina: procesamiento local + diseño para mayores de 70 años + ecosistema familiar + idioma español nativo.

### 5.4 Oportunidad y diferenciación

Shadow Heart ocupa un **espacio azul (Blue Ocean)** que ningún competidor cubre:

| Dimensión | Wearables | Dispositivos médicos | rPPG B2B | **Shadow Heart** |
|---|---|---|---|---|
| Coste usuario | €100-€799 | €50-€150/mes | N/A | **€0 (mayor), €5/mes (familiar)** |
| Hardware necesario | Sí (wearable) | Sí (sensores) | Varía | **Solo smartphone** |
| Curva de aprendizaje | Media-alta | Alta | N/A | **Cero (1 botón)** |
| Diseño para 70+ | No | Parcial | No | **Desde la primera línea de código** |
| Privacidad | Cloud del fabricante | Servidor hospitalario | Cloud | **100% local en dispositivo** |
| Ecosistema familiar | Limitado | No | No | **Core del producto** |
| Español nativo | Traducción | Varía | Inglés | **Diseñado en español** |

**Ventaja competitiva sostenible:** La combinación de diseño extremo para mayores + ecosistema familiar + privacidad local + procesamiento edge es excepcionalmente difícil de replicar. No se trata solo de tecnología rPPG (comoditizable), sino de una filosofía de producto completa: "nunca dice fallo" + "cada medición es un te quiero".

---

## 6. Mercado objetivo y estrategia de adopción

### 6.1 Segmentos y clientes

**Modelo: B2C con extensión B2B2C**

**Segmento primario — Familias con mayores a distancia (B2C):**
- Hijo/a (30-50 años) que vive lejos de su padre/madre mayor
- Disposición a pagar €5/mes por tranquilidad ("economía del cariño filial")

**Segmento secundario — Servicios de teleasistencia (B2B2C):**
- Empresas de teleasistencia domiciliaria que buscan reducir costes de monitorización
- Modelo: licencia por centro/operadora, integración con flujos de trabajo existentes
- Valor: reducción de llamadas rutinarias, alertas automatizadas

**Segmento terciario — Residencias y centros de día (B2B):**
- Centros que atienden a múltiples residentes
- Medición rápida sin hardware dedicado por persona
- Modelo: licencia por centro, panel de seguimiento (Phase 2)

### 6.2 Estrategia de despliegue inicial

**Piloto Fase 1 (meses 1-3): Validación con familias reales**
- **Dónde:** Entornos domésticos de 10 familias
- **Con quién:** 10 familias (10 mayores + 10 familiares)
- **Duración:** 8 semanas de uso diario
- **Criterios de éxito:**
  - Tasa de éxito primera medición sin ayuda ≥ 85%
  - Retención semanal (≥3 mediciones/semana) ≥ 70%
  - Satisfacción emocional (encuesta): 80%+ describen la experiencia como "fácil" o "tranquilizador"
  - Precisión MAE ≤ 5 BPM vs pulsioxímetro

**Piloto Fase 2 (meses 4-6): Validación con teleasistencia**
- **Dónde:** 1-2 centros de teleasistencia o residencias 
- **Con quién:** 50 usuarios mayores + personal de atención
- **Criterios de éxito:**
  - Reducción de llamadas rutinarias de seguimiento ≥ 20%
  - SMS de alerta con tasa de falsos positivos < 10%
  - Personal califica la herramienta como "útil" ≥ 80%

**Lanzamiento (meses 7-12): App pública + canal B2B**
- Publicación en Google Play y App Store
- Modelo freemium: mayor gratuito, familiar €5/mes
- Canal B2B: contacto directo con operadoras de teleasistencia

---

## 7. IA Responsable y ética aplicada

### 7.1 Privacidad y minimización

**Principio fundamental: Procesamiento en dispositivo (edge-first).**

| Medida | Implementación |
|---|---|
| Procesamiento local | 100% del análisis rPPG ocurre en el smartphone. Cero envío de datos al cloud |
| No almacenamiento de vídeo | Los frames de cámara se procesan en memoria y se descartan. Solo se guarda el BPM resultante |
| Base de datos cifrada | SQLCipher (AES-256) para todos los datos de salud almacenados localmente |
| Datos mínimos | Se almacena: BPM, timestamp, confidence score. No se almacena: vídeo, imagen facial, datos biométricos identificativos |
| Sin cuenta de usuario (mayor) | El mayor no necesita crear cuenta ni proporcionar datos personales |
| Sin SDKs de terceros | No se integra analytics, publicidad ni seguimiento de terceros |
| Sin identificadores | El PDF exportable no contiene huella de dispositivo |
| Consentimiento informado | Explicación en texto grande y lenguaje sencillo de qué datos se recogen y para qué, al inicio del uso |


### 7.2 Sesgos y equidad

**Riesgo identificado:** La tecnología rPPG puede presentar menor precisión en tonos de piel más oscuros (mayor absorción de luz, menor contraste en señal de reflectancia).

**Plan de pruebas con diversidad:**

| Dimensión | Cobertura de test |
|---|---|
| Tonos de piel | Escala Fitzpatrick I-VI (obligatorio: al menos 5 sujetos por categoría) |
| Iluminación | 100-500 lux (interiores domésticos típicos), mañana/tarde/noche |
| Edad | 60-90 años (el grupo objetivo real, no jóvenes como proxy) |
| Movilidad | Con y sin temblor en manos, con y sin gafas, movimiento ligero |
| Dispositivos | Gama baja (Samsung A14, Xiaomi Redmi Note 12), gama media-alta (Samsung S21, iPhone SE) |

**Métricas de rendimiento por subgrupo:**
- MAE por categoría Fitzpatrick (objetivo: ≤5 BPM en todas)
- Tasa de lecturas válidas por nivel de iluminación
- Tasa de éxito primera medición por rango de edad (65-74, 75-84, 85+)


### 7.3 Transparencia y explicabilidad práctica

- **Quality score visible:** Cada medición muestra un indicador de confianza. Si la puntuación es baja (<0.7), el resultado se marca con "≈" y "solo como referencia"
- **"No doy BPM":** Si la calidad de señal es insuficiente (datos válidos <8 segundos), el sistema NO inventa un resultado. Dice amablemente: "Intentemos más tarde" y no muestra ningún número
- **Mensajes claros:** Los resultados se expresan en lenguaje cotidiano ("Tu corazón está bien" vs "68 BPM dentro del rango normal")
- **Trazabilidad:** Cada medición se almacena con timestamp, BPM, confidence score y condiciones (iluminación estimada). Esto permite auditoría interna y análisis de rendimiento

### 7.4 Seguridad y prevención de mal uso

**Riesgos y mitigaciones:**

| Riesgo | Severidad | Mitigación |
|---|---|---|
| Interpretación errónea como diagnóstico | Alta | Disclaimer en cada resultado, lenguaje no médico, "referencia" nunca "diagnóstico" |
| Ansiedad por resultados anormales | Media | Diseño emocional con alertas suaves (emoji + colores cálidos), explicaciones tranquilizadoras, derivación a médico |
| Uso clínico indebido | Alta | Documentación clara de limitaciones, no reclamar precisión clínica, advertencia a profesionales |
| Falsa sensación de seguridad | Alta | Mensajes periódicos recordando visitar al médico, disclaimer de "no sustituye monitorización médica" |
| Monitorización excesiva → conflicto familiar | Media | Solo notificar al familiar en caso de anomalía real, no convertir la app en herramienta de vigilancia |
| Datos erróneos por condiciones inadecuadas | Media | Quality score que bloquea resultados de baja confianza, nunca mostrar un BPM falso |

### 7.5 Validación participativa

**Personas mayores como co-diseñadoras, no como sujetos de prueba.**

**Fase de validación UX (antes del piloto):**
- 5 personas mayores (70+ años) realizan test sin instrucciones
- Observación directa: ¿encuentran el botón? ¿entienden el resultado? ¿sienten miedo o confianza?
- Entrevista post-test: vocabulario emocional ("¿cómo te sentiste?"), no técnico ("¿fue preciso?")
- Criterio de éxito: tasa de primera medición sin ayuda ≥85%

**Fase de piloto (8 semanas):**
- 10 personas mayores + 10 familiares
- Check-in semanal con cuidadores: ¿ha habido incidentes? ¿frustraciones? ¿la persona mayor pide dejar de usarla?
- Encuesta de satisfacción emocional mensual
- Reunión de feedback con personas mayores (formato accesible: conversación, no cuestionario)

**Iteración continua:**
- Los resultados del piloto informan directamente las mejoras del producto
- Compromiso: no lanzar funcionalidades que no hayan sido probadas con usuarios reales del grupo objetivo

---

## 8. Arquitectura tecnológica

### 8.1 Flujo de procesamiento (pipeline rPPG)

```
[Cámara frontal]
    ↓ CameraX (Android) / AVFoundation (iOS)
[Captura de frames a 30fps]
    ↓ Capa nativa (Kotlin / Swift)
[Detección facial MediaPipe → Localización ROI (frente)]
    ↓ Extracción RGB promedio del ROI
[Dart FFI → C++ Signal Core]
    ↓ Algoritmo POS (Plane-Orthogonal-to-Skin)
    ↓ Filtro de paso de banda (0.7-4.0 Hz)
    ↓ Detección de picos
[BPM + Quality Score]
    ↓ Riverpod State Management
[UI: Resultado emocional (emoji + color)]
    ↓ Almacenamiento local (Drift + SQLCipher)
    ↓ (Futuro) SMS de alerta / resumen diario
```

Actualmente, sólo está implementada la parte de Android. 

### 8.2 Componentes

| Componente | Tecnología | Función |
|---|---|---|
| **Frontend** | Flutter 3.x + Riverpod v2 | UI multiplataforma, gestión de estado, navegación (GoRouter) |
| **Motor rPPG** | C++ nativo vía Dart FFI | Algoritmo POS, filtrado, detección de picos. ≤5ms/frame |
| **Captura de cámara** | CameraX (Android) + AVFoundation (iOS) | Frames 30fps, MethodChannel + EventChannel para comunicación |
| **Detección facial** | Google ML Kit (MediaPipe) | Localización de rostro y ROI de frente, multiplataforma |
| **Almacenamiento** | Drift ORM + SQLCipher | BD local cifrada AES-256, historial de mediciones |

### 8.3 Decisiones técnicas clave

**Edge vs Cloud: 100% Edge (procesamiento en dispositivo)**
- Razón: privacidad máxima, funcionamiento offline, cero dependencia de servidores
- Trade-off: no hay aprendizaje centralizado (compensado con algoritmo POS robusto probado)

**Latencia y rendimiento:**
- Procesamiento C++ FFI: ≤5ms/frame
- Pipeline completo (botón → resultado): ≤15 segundos
- Arranque de app en frío: ≤3 segundos (Android gama baja)
- Consumo de batería por medición: ≤1%

**Compatibilidad de dispositivos:**
- Android API 26+ (cubre 95%+ dispositivos)
- iOS 15+ (cubre 90%+ usuarios) (implementación iOS planificada para mes 2-3)
- Matriz de test: 10 Android + 5 iOS

**Requisitos de iluminación:**
- Rango funcional: interior doméstico estándar
- Detección automática de luz insuficiente
- Degradación progresiva (nunca fallo abrupto)

### 8.4 Requisitos no funcionales

| Categoría | Requisito | Especificación |
|---|---|---|
| **Rendimiento** | Medición completa | ≤15 seg |
| | Procesamiento por frame | ≤5ms (C++ FFI) |
| | Arranque en frío | ≤3 seg |
| | Carga de historial | ≤1 seg (1000 registros) |
| **Seguridad** | Cifrado BD | SQLCipher AES-256 |
| | Transmisión de datos | Cero (todo local) |
| | SDKs de terceros | Ninguno (analytics/ads) |
| **Accesibilidad** | Texto mínimo | 20sp (auxiliar: 18sp) |
| | Número de BPM | 64sp Bold |
| | Contraste | ≥ WCAG AA (4.5:1) |
| | Target táctil | ≥48×48dp |
| | Canales de feedback | Triple: visual + voz + vibración |
| **Fiabilidad** | Tasa de crash | <0.1% |
| | Tasa de éxito (100-500 lux) | ≥95% |
| | Precisión | ≤±5 BPM vs referencia |
| | Diferencia multiplataforma | ≤±2 BPM |
| **Disponibilidad** | Modo offline | 100% funcional |
| | Recuperación tras interrupción | Automática (llamada, notificación) |

---

## 9. Roadmap de desarrollo

### 9.1 Próximos hitos (3 meses)

**Estado actual:** Epics 1-5 completados (fundación de app, pipeline rPPG, detección con tolerancia a fallos, resultados emocionales, historial y tendencias).

| Hito | Entregable | Plazo |
|---|---|---|
| Validación de precisión | Informe: 50 mediciones vs pulsioxímetro, diversidad Fitzpatrick I-VI | Mes 1 |
| Recordatorios inteligentes (Epic 6) | Notificaciones locales programables, acceso directo a medición | Mes 1-2 |
| Alertas de emergencia (Epic 7) | SMS automático al familiar, doble confirmación, llamada de emergencia | Mes 2 |
| Resumen diario familiar (Epic 8) | SMS diario al familiar: "Mamá hoy 68 BPM, todo normal ❤️" | Mes 2-3 |
| Empaquetado demo | APK/IPA de demostración para pilotos, guía de instalación | Mes 3 |

### 9.2 Validación ampliada (6 meses)

| Hito | Entregable | Plazo |
|---|---|---|
| Piloto familias (10 familias) | Informe: retención, satisfacción, precisión en uso real | Mes 3-5 |
| Dataset diverso ampliado | 200+ mediciones comparativas, todas las categorías Fitzpatrick | Mes 4-5 |
| Ajuste de umbrales | Quality score optimizado según datos de uso real | Mes 5 |
| Panel familiar (Phase 2 inicio) | App/web para familiares: historial, tendencias, configuración remota | Mes 5-6 |
| Voz familiar personalizada | Grabación de voz del familiar para resultados (sustituyendo TTS genérico) | Mes 6 |

### 9.3 Pilotos y escalado (12 meses)

| Hito | Entregable | Plazo |
|---|---|---|
| Piloto teleasistencia | 50 usuarios + 1-2 partners B2B, informe operativo | Mes 6-9 |
| Publicación pública | App en Google Play + App Store  | Mes 8 |
| Modelo de suscripción | Familiar: €5/mes, Piloto B2B financiado | Mes 8 |
| Expansión España/LATAM | Evaluación regulatoria, adaptación cultural por país | Mes 10-12 |
| Integración teleasistencia | API/exportación para sistemas de teleasistencia existentes | Mes 12 |

### 9.4 Entregables por hito

| Hito | Entregables |
|---|---|
| **3 meses** | APK demo funcional, informe de validación de precisión (50 mediciones), guía de uso para pilotos |
| **6 meses** | App completa (Epics 1-8), informe de piloto familias (10 familias/8 semanas), dataset de validación ampliado, prototipo panel familiar |
| **12 meses** | App publicada en stores, informe de piloto teleasistencia, panel de seguimiento B2B, guía de integración, modelo de negocio validado |

---

## 10. Modelo de negocio y sostenibilidad

### 10.1 Propuesta de monetización

**Modelo: "Economía del cariño filial" — El mayor no paga nunca.**

| Canal | Modelo | Precio | Fase |
|---|---|---|---|
| **B2C Familiar** | Suscripción mensual del familiar | €5/mes por familia | Lanzamiento (mes 8) |
| **B2B Teleasistencia** | Licencia por operadora/centro | €200-€500/mes por centro | Mes 9+ |
| **B2B Residencias** | Pago por centro + panel de seguimiento | €300-€800/mes según nº residentes | Mes 12+ |
| **B2B Aseguradoras** | Acuerdo corporativo, subvención de licencias | Por negociación | Año 2+ |
| **Piloto financiado** | Grant/subvención de innovación social | Variable | Inmediato |

**Métricas de negocio objetivo:**

| Fase | Tiempo | Objetivo |
|---|---|---|
| Validación | 3 meses | 500 familias registradas, 15% conversión a pago |
| Crecimiento | 12 meses | 5,000+ familias pagantes, MRR €25,000+, 2 partners B2B |
| Escala | 24 meses | 30,000+ familias, expansión a 3 mercados hispanos, B2B = 30% ingresos |

### 10.2 Estructura de costes

| Categoría | Detalle | Estimación mensual |
|---|---|---|
| **Desarrollo** | 1 desarrollador Flutter + C++ (founder/equipo core) | Variable (bootstrap/salario) |
| **Validación** | Pulsioxímetros de referencia, compensación participantes piloto | €500-€1,000 (one-time por ronda) |
| **Accesibilidad** | Tests con usuarios reales (mayores), reclutamiento, facilitación | €300-€500/mes durante pilotos |
| **Seguridad/Privacidad** | Auditoría de código, revisión legal HIPAA/GDPR | €1,000-€3,000 (one-time) |
| **Infraestructura** | Mínima: cuenta de desarrollador Google Play (€25 one-time) + Apple (€99/año) | ~€10/mes |
| **Soporte** | FAQ, guía de uso, soporte básico (email/WhatsApp) | €200-€500/mes |
| **Gobernanza IA** | Monitorización de sesgos, auditorías internas periódicas | Incluido en desarrollo |

**Ventaja estructural de costes:** Al ser 100% edge computing, **no hay coste de servidores, cloud ni APIs de IA**. El coste marginal por usuario adicional es prácticamente cero.

### 10.3 Estrategia de control del gasto en IA

Shadow Heart **no utiliza servicios de IA en la nube** para el procesamiento de señales. Todo el cómputo rPPG se realiza en el dispositivo del usuario mediante algoritmos deterministas (POS + filtrado + detección de picos) implementados en C++ nativo.

**Estrategia de costes cero en IA:**
- **Edge processing:** El algoritmo POS es una transformación matemática determinista, no un modelo de ML que requiera inferencia en la nube
- **Sin APIs de terceros:** No se usa OpenAI, Google Cloud Vision, ni ningún servicio de IA externo
- **Sin entrenamiento de modelos:** El algoritmo POS es basado en fórmulas, no requiere GPU/TPU para entrenamiento
- **MediaPipe:** El modelo de detección facial se ejecuta localmente en el dispositivo (ML Kit on-device)

**Único uso de IA/ML:**
- MediaPipe face detection: modelo pre-entrenado ejecutado localmente, sin coste de inferencia
- Futuro (Phase 3): Aprendizaje de baseline personal de frecuencia cardíaca — también ejecutado localmente

**Resultado:** La estructura de costes de Shadow Heart es fundamentalmente diferente a la de competidores que dependen de cloud AI. No hay "factura de IA" que escale con el número de usuarios.

---

## 11. Impacto esperado y métricas de éxito

### 11.1 Impacto social

**Para personas mayores:**
- **Adherencia a la monitorización:** De "nunca mido mi pulso" a "lo hago cada día sin pensar" — objetivo: 50% de usuarios forman hábito de medición diaria en 30 días
- **Autonomía y dignidad:** La persona mayor puede cuidar de su salud sin depender de nadie para operar tecnología. El sistema le dice "tú puedes hacerlo"
- **Reducción de aislamiento:** Cada medición es un punto de conexión con la familia. El resultado no es un número — es un "mamá está bien"

**Para familias:**
- **Tranquilidad diaria:** Isabel sabe que mamá está bien antes de empezar su día laboral. Reducción de preocupación medida ≥30%
- **Detección temprana de cambios:** Identificar tendencias (frecuencia cardíaca subiendo progresivamente) antes de que se conviertan en episodios agudos
- **Comunicación facilitada:** "Mamá, vi que tu corazón estuvo un poco acelerado ayer" — datos que generan conversaciones, no vigilancia

**Para el sistema de salud:**
- **Reducción de fricción:** Menos visitas evitables al médico por "quiero saber si estoy bien"
- **Datos longitudinales:** Historial de frecuencia cardíaca disponible para el médico en la siguiente consulta (exportación PDF)
- **Prevención:** Identificación de anomalías antes de hospitalización, especialmente en poblaciones vulnerables con acceso limitado a atención primaria

### 11.2 Métricas de producto

| Métrica | Definición | Objetivo MVP | Objetivo 12 meses |
|---|---|---|---|
| **Activación** | Primera medición exitosa sin ayuda | ≥ 85% | ≥ 92% |
| **Retención 30d** | Usuarios que miden ≥3 veces/semana durante 30 días | ≥ 40% | ≥ 55% |
| **Tasa lecturas válidas** | Mediciones con quality score > 0.7 | ≥ 90% | ≥ 95% |
| **MAE** | Error medio absoluto vs referencia | ≤ 5 BPM | ≤ 3 BPM |
| **Tiempo a estabilidad** | Segundos desde botón hasta resultado | ≤ 15 seg | ≤ 12 seg |
| **Satisfacción emocional** | Usuarios que describen la experiencia como "fácil/tranquilizador" | ≥ 80% | ≥ 90% |
| **NPS** | Net Promoter Score | ≥ 40 | ≥ 55 |
| **Conversión pago (familiar)** | Familiares que pagan €5/mes | ≥ 15% | ≥ 25% |
| **Familiar: vista diaria** | Familiares que revisan estado diariamente | ≥ 50% | ≥ 65% |

### 11.3 Métricas de confianza

| Métrica | Definición | Objetivo |
|---|---|---|
| **Incidentes de seguridad** | Eventos donde datos de salud se expusieron o perdieron | 0 |
| **Quejas por resultado erróneo** | Usuarios que reportan resultados claramente incorrectos | < 2% de mediciones |
| **Sesgos detectados** | Diferencia de MAE entre subgrupos Fitzpatrick | ≤ 2 BPM entre cualquier par de subgrupos |
| **Auditorías internas** | Revisiones de sesgo y precisión por subgrupo | Trimestral |
| **Cumplimiento privacidad** | Verificación de que cero datos salen del dispositivo | Continuo (código abierto de la capa de datos en futuro) |
| **Tasa de falsos positivos (alarma)** | Alarmas que no corresponden a anomalía real | < 10% |
| **Tasa de "no doy resultado"** | Mediciones donde el sistema dice "intentemos después" | < 5% (100-500 lux) |

---

## 12. Cierre y llamada a la acción

### Lo que necesitamos

Shadow Heart tiene la tecnología funcionando, el diseño definido y la primera versión construida. Lo que necesitamos ahora es **validación con usuarios reales y el ecosistema para escalar:**

**1. Partner piloto (prioridad máxima)**
- Asociación de mayores hispanos, servicio de teleasistencia o residencia dispuesta a participar en un piloto de 8 semanas con 10-50 usuarios mayores reales
- Nos encargamos de la tecnología, la formación y el soporte. Solo necesitamos acceso a las personas y el entorno

**2. Acceso a entorno de validación**
- Espacio con personas mayores de 70+ años, diversidad de tonos de piel, condiciones reales de iluminación doméstica
- Para completar la validación de precisión (50+ mediciones comparativas con pulsioxímetro)

**3. Mentoría técnica y regulatoria**
- Asesoramiento en posicionamiento FDA (General Wellness Product vs. Medical Device)
- Revisión de cumplimiento HIPAA/GDPR
- Consejo sobre estrategia de propiedad intelectual

**4. Financiación semilla**
- €50,000-€100,000 para cubrir 12 meses de validación, piloto y lanzamiento inicial
- Destino: validación con usuarios (40%), desarrollo Phase 2 - panel familiar (30%), legal y regulatorio (15%), marketing comunitario (15%)

### Contacto

**Proyecto:** Shadow Heart — Monitorización cardíaca sin contacto para mayores
**Fundador:** Liu
**Email:** yangcliu4@gmail.com
**Web:** No disponible (desarrollo en curso)
**Repositorio:** Privado

---

*Shadow Heart: Porque cada latido es un "mamá está bien" que llega hasta ti.*
