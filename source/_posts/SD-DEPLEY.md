---
uuid: 6c6db7c0-d892-11f0-b00f-852cbeaeb8c7
title: SD_DEPLEY
date: 2025-12-14 03:13:04
tags:
---


## 1. Informe de Desarrollo

### 1.1 Descripción General del Proyecto

Esta práctica implementa un sistema distribuido de gestión de puntos de recarga para vehículos eléctricos (EVCharging Network) con arquitectura SOA (Service Oriented Architecture), utilizando tecnología REST API y mecanismos de seguridad(Socket seguro y https).

### 1.2 Componentes Desarrollados

#### 1.2.1 EV_Registry (Registro de Puntos de Recarga)

**Descripción**: Módulo de registro y gestión de Charging Points (CPs).

**Funcionalidades**:

- Alta de nuevos CPs en el sistema
- Baja de CPs existentes
- Generación y gestión de credenciales de autenticación(token JWT)
- API REST con métodos GET, DELETE

**Tecnologías utilizadas**:

- Python (Flask)
- API REST
- Canal seguro HTTPS/SSL/RSA
- Base de datos compartida con EV_Central

#### Imagen de ejecución:

![1765680704890](image/readme/1765680704890.png)

![1766031717025](image/readme/1766031717025.png)

#### Código fuente:

[Registry](Charging_point/Registry/EV_Registry.py)

---

#### 1.2.2 EV_Central (Central de Control)

**Descripción**: Componente central del sistema que gestiona la comunicación con CPs

**Funcionalidades**:

- Autenticación de CPs con credenciales del Registry
- Generación de claves de cifrado simétricas únicas por CP
- Consumo de API REST de EV_W (Weather Control Office)
- Gestión de alertas climatológicas
- Sistema de auditoría de eventos
- Revocación y restauración de claves de seguridad

**Implementaciones de Seguridad**:

1. **Autenticación**: Verificación de credenciales proporcionadas por EV_Registry
2. **Cifrado de canal**: Sistema de cifrado simétrico para mensajes CP-Central
3. **Auditoría**: Registro estructurado de eventos con:
   - Fecha y hora del evento
   - Tipo de evento(autenticación, cambio de estado, error, etc.)
   - Actor involucrado(CP, Driver, Sistema)
   - Resouce afectado(ID del CP, ID del Driver, etc.)
   - Acción realizada(registro, autenticación, revocación, etc.)
   - Resultado del evento(éxito, fallo, error específico)

**Tecnologías utilizadas**:

- Kafka
- Sockets seguro para autenticación [SocketClient seguro](Common/Network/MySocketClient.py), [SocketServer seguro](Common/Network/MySocketServer.py)
- Cifrado simétrico para los mensajes de kafka: [Libreria Fernet](Common/Segurity/EncriptionManager.py)
- Base de datos compartida(Postgresql): [Modelos de Base de Datos](Common/Database/Models.py)

![1766031817698](image/readme/1766031817698.png)

![1766031848195](image/readme/1766031848195.png)

**Código fuente**:

[Central](Core/Central/EV_Central.py)
[Control de base de datos(Charging point)](Core/Central/DataManager/ChargingPoint.py)
[Control de base de datos(Drivers)](Core/Central/DataManager/DriverManager.py)
[Control de base de datos(Charging sessions)](Core/Central/DataManager/ChargingSession.py)
[Modulo de CLI](Core/Central/CLI/AdminCLI.py)

---

#### 1.2.3 API_Central

**Descripción**: API REST expuesta por Central para consulta de estado del sistema.

**Funcionalidades**:

- Consulta de estado de Drivers
- Consulta de estado de CPs
- Visualización de transacciones en curso
- Recepción de notificaciones de alertas climatológicas desde EV_W

**Métodos implementados**:

- GET: Obtención de estado del sistema
- PUT: Actualización de alertas climáticas

**Código fuente**:

[API_Central](Core/Central/API/EV_CENTRAL_API.py)

![1765680716820](image/readme/1765680716820.png)

![1766031907110](image/readme/1766031907110.png)

---

#### 1.2.4 EV_W (Weather Control Office)

**Descripción**: Módulo de control climatológico que determina la viabilidad de operación de CPs.

**Funcionalidades**:

- Consulta periódica (cada 4 segundos) a OpenWeather API
- Monitorización de temperaturas en localizaciones de CPs
- Notificación de alertas cuando temperatura < 0°C
- Notificación de cancelación de alertas cuando temperatura > 0°C
- Gestión manual de localizaciones mediante menú/archivo

**Integración con APIs externas**:

- **OpenWeather API**:
  - Endpoint: `api.openweathermap.org/data/2.5/weather?q={city name}&appid={API key}`
  - API Key: Clave obtenida de OpenWeather(Requiere registro previo)
  - Datos extraídos: Temperatura (°C) y nombre de ciudad

**Lógica de negocio**:

```
SI temperatura < 0°C ENTONCES
    Notificar alerta a EV_Central vía API_Central
    CP pasa a modo "fuera de servicio" (tras finalizar suministro en curso)
SI temperatura > 0°C ENTONCES
    Notificar cancelación de alerta a EV_Central
    CP se restaura a modo operativo
```

**Código fuente**:

[Weather Control Office](Core/Weather/EV_W.py)

![1765680726045](image/readme/1765680726045.png)

![1766031967430](image/readme/1766031967430.png)

---

#### 1.2.5 EV_CP_M (Monitor del Charging Point)

**Descripción**: Módulo de gestión y monitorización del CP.

**Nuevas funcionalidades Release 2**:

- Opción de registro en EV_Registry mediante consumo de API REST
- Autenticación segura en EV_Central con credenciales del Registry
- Recepción de clave de cifrado simétrico durante autenticación
- Re-autenticación en caso de revocación de claves

**Seguridad implementada**:

- Canal seguro (HTTPS/SSL) con EV_Registry
- Cifrado de credenciales en tránsito
- Almacenamiento seguro de clave de cifrado simétrica

**Código fuente**:

[Monitor del CP](Charging_point/Monitor/EV_CP_M.py)

![1766031989656](image/readme/1766031989656.png)

---

#### 1.2.6 EV_CP_E (Engine del Charging Point)

**Descripción**: Motor de procesamiento del CP que gestiona el suministro eléctrico.

**Modificaciones Release 2**:

- Cifrado de todos los mensajes enviados a Central mediante clave simétrica
- Descifrado de mensajes recibidos de Central

**Algoritmo de cifrado utilizado**: AES

**Código fuente**:

[Engine del CP](Charging_point/Engine/EV_CP_E.py)

![1766032012015](image/readme/1766032012015.png)

#### 1.2.7 Front (Interfaz Web de Monitorización)

**Descripción**: Página web pública para visualización del estado del sistema.

**Funcionalidades**:

- Visualización en tiempo real del estado de CPs
- Visualización del estado de Drivers
- Visualización de monitorización de diferentes servicios(Api Central, Registry, Weather)
- Mostrar temperatura de cada localización
- Alertas de errores y fallos del sistema
- Auditoría básica (eventos recientes)

**Tecnología utilizada**: HTML, CSS, JavaScript (Fetch API)

**Consumo de API**: API_Central (peticiones GET)

**Código fuente**:

[Frontend](Core/Front)

![1765682140779](image/readme/1765682140779.png)

![1765682145891](image/readme/1765682145891.png)

---

#### 1.2.8 EV_Drivers (Aplicación de Conductores)

**Descripción**: Aplicación de simulación de conductores que solicitan recarga.

**Funcionalidades**: Sin cambios respecto a Release 1

**Código fuente**:

[Driver](Driver/EV_Driver.py)

![1765682208563](image/readme/1765682208563.png)

---

#### 1.2.9 Base de Datos

**Descripción**: Sistema de almacenamiento .

**Acceso**:

- EV_Central
- EV_Registry

**Información almacenada**:

- Datos de CPs registrados (ID, localización)
- Credenciales de autenticación
- Claves de cifrado simétricas por CP
- Eventos de auditoría
- Datos de Drivers y sesiones de recarga

**Tecnología utilizada**: PostgreSQL

![1765682275205](image/readme/1765682275205.png)

![1765682291598](image/readme/1765682291598.png)

![1765682320044](image/readme/1765682320044.png)

![1765682336149](image/readme/1765682336149.png)

---

### 1.3 Arquitectura del Sistema

```
                    Core System
                         |
    +--------------------+--------------------+
    |                    |                    |
Registry            CENTRAL              API_Central
    |                    |                    |
    |              +-----+-----+              |
    |              |           |              |
    |             BD       Auditoría          |
    |                          |              |
    +---(HTTPS)----> CP_M      |              |
                      |        |              |
                    CP_E -(Cifrado)-> Kafka   |
                                     |        |
                                  Drivers     |
                                              |
                                           FRONT (Browser)
                                              |
                          EV_W ---------------+
                            |
                      OpenWeather API
```

---

### 1.4 Mecanismos de Seguridad Implementados

#### 1.4.1 Autenticación Segura (CP - Registry - Central)

**Flujo de autenticación**:

1. **Registro en EV_Registry**:

   - CP envía solicitud de registro con ID y localización
   - Canal seguro: HTTPS/SSL/RSA
   - Registry genera credenciales (token jwt)
   - Registry almacena CP en BD
   - Registry devuelve credenciales al CP

2. **Autenticación en EV_Central**:

   - CP envía credenciales a Central
   - Central verifica credenciales contra BD
   - Si éxito: Central genera clave de cifrado simétrica única para el CP
   - Central devuelve clave de cifrado al CP
   - CP almacena clave para cifrado de mensajes

3. **Revocación de claves**:

   - Opción manual en Central para borrar claves de CP específico
   - CP queda fuera de servicio
   - CP debe re-autenticarse para obtener nuevas claves

**Implementación técnica**:

- Monitor utiliza HTTPS para comunicarse con Registry(Certificado autofirmado)
- Central utiliza sockets seguros para pasos de mensajes entre kafka
- API Central dispone un endpoint para autenticación con CP
- Claves de cifrado simétricas generadas con [Libreria Fernet](Common/Segurity/EncriptionManager.py)

---

#### 1.4.2 Cifrado de Canal (CP - Central)

**Objetivo**: Prevenir ataques Man-In-The-Middle (MITM) en comunicación CP-Central.

**Método de cifrado**: AES, DES, 3DES, etc.

**Implementación**:

- Cada CP tiene clave simétrica única
- Todos los mensajes CP→Central van cifrados
- Central descifra con clave específica del CP
- Mensajes viajan por Kafka topics cifrados

**Flujo de cifrado**:

```
CP_E: mensaje_original → cifrado(mensaje, clave_CP) → mensaje_cifrado → Kafka
Central: mensaje_cifrado → descifrado(mensaje, clave_CP) → mensaje_original
```

Si la clave es revocada, CP debe re-autenticarse para obtener nueva clave. Hasta que no lo haga, CP queda fuera de servicio(Stopped).

---

#### 1.4.3 Auditoría de Eventos

**Objetivo**: Registrar todos los eventos del sistema para trazabilidad y análisis de seguridad.

**Estructura del registro de auditoría**:

![1766029779413](image/readme/1766029779413.png)

**Eventos auditados**:

- Autenticaciones exitosas y fallidas
- Cambios de estado de CPs
- Incidencias durante el servicio
- Bloqueo de usuarios o CPs
- Errores del sistema
- Revocación de claves

**Almacenamiento**: A base de datos PostgreSQL compartida con Central

**Código fuente**:

[Auditoría](Common/Database/AuditEvent/AuditEventService.py)

---

## 2. Guía de Despliegue

### 2.1 Requisitos Previos

**Software**:

- **Sistema Operativo**: Windows 10/11 o Linux
- **Python**: 3.8 o superior
- **Docker Desktop**: Para ejecutar Kafka y PostgreSQL
- **Git**: Para clonar el repositorio
- **OpenWeather API Key**: Registrarse en https://openweathermap.org/api

**Hardware recomendado**:

- 4 GB RAM mínimo (8 GB recomendado)
- 2 GB espacio en disco
- Conexión a Internet para API de OpenWeather

---

### 2.2 Escenario de Despliegue

**Distribución de componentes recomendada**:

**PC 1** (Servidor Central):

- Kafka
- PostgreSQL
- EV_Central
- API_Central
- EV_Registry

**PC 2** (Charging Point):

- EV_CP_M
- EV_CP_E
- EV_W

**PC 3** (Servicios Externos):

- EV_Drivers
- Servidor Frontend

**Nota**: Para pruebas locales, todos los componentes pueden ejecutarse en una sola máquina.

---

### 2.3 Pasos de Despliegue

#### Paso 0: Preparación Inicial

**0.1. Crear entorno virtual de Python** (recomendado):

```bash
python -m venv venv

# En Windows:
venv/Scripts/activate

# En Linux/Mac:
source venv/bin/activate
```

**0.2. Instalar dependencias**:

```bash
pip install -r requirements.txt
```

**0.3. Configurar archivo .env**:

```bash
# Copiar el archivo de ejemplo
cp .env.example .env

```

**0.4. Generar certificados SSL** (si no existen):

```bash
python ssl/generate_ssl_key.py
```

Nota: Si se ejecuta de forma seperada, se debe de modificar las lineas siguientes del script para que que los certificados generado son correctos:

```python
IP_ADDRESS_REGISTRY = "127.0.0.1"
IP_ADDRESS_CENTRAL = "127.0.0.1"
```

---

#### Paso 1: Configuración de PC 1

**1.1. Arrancar servicios Docker** (Kafka y PostgreSQL):

```bash
docker compose up -d
```

Nota: Se debe de cambiar KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092(localhost) por la IP de la máquina si se va a usar de forma remota.

Verificar que los contenedores están corriendo:

```bash
docker ps
```

Deberías ver:

- `broker` (Kafka) - Puerto 9092
- `postgres-evcharging` (PostgreSQL) - Puerto 5434

**1.2. Configuración del archivo .env para PC1**:

Editar `.env` y verificar/modificar las siguientes variables:

```env
# Kafka Configuration
BROKER_ADDRESS=<localhost>:9092

# Database Configuration
DB_HOST=<localhost>
DB_PORT=5434
DB_NAME=evcharging
DB_USER=evcharging
DB_PASSWORD=evcharging

# Central Configuration
IP_PORT_EV_CP_CENTRAL=<localhost>:6001

# API Central Configuration
API_CENTRAL_PORT=8000
API_CENTRAL_HOST=<localhost>

# Registry Configuration
REGISTRY_HOST=<localhost>
REGISTRY_PORT=40026
# Debug Mode
DEBUG_MODE=false
```

**1.3. Arranque de EV_Central**:

```bash
python Core/Central/EV_Central.py 6001 <localhost>:9092
```

Parámetros:

- `6001`: Puerto de escucha para autenticación de CPs
- `localhost:9092`: Dirección del broker Kafka

**1.4. Arranque de API_Central**:

```
python Core/Central/API/EV_CENTRAL_API.py
```

**1.4.1 Parámetro por defecto (puerto 8000):**

```bash
API_CENTRAL_PORT=8000
API_CENTRAL_HOST=<localhost>
```

La API estará disponible en: `http://<HOST_DE_CENTRAL>:8000`

Endpoints principales:

- `GET /ev/central/charging-points` - Estado de CPs
- `PUT /ev/central/city-temperature` - Actualizar alertas climáticas
- `GET /ev/central/audit-events` - Eventos de auditoría

**1.5 . Arranque de EV_Registry**:

```bash
python Charging_point/Registry/EV_Registry.py
```

En el archivo .env, asegurarse de que las siguientes variables están configuradas:

```
# Registry module enviroments
REGISTRY_HOST=<127.0.0.1>
REGISTRY_PORT=<7002>
```

Una vez arrancado, el Registry estará disponible en `https://<HOST_DE_REGISTRY>:<PORT>/ev/registry/health`

Endpoints API REST:

- `POST /ev/registry/charging-points` - Registrar nuevo CP
- `GET /ev/registry/charging-points/<cp_id>` - Consultar CP
- `DELETE /ev/registry/charging-points/<cp_id>` - Dar de baja CP

Nota: Cambiar aquellos <> por la IP de PC1 si es remoto.

---

#### Paso 2: Configuración de PC 3

Nota: copiar tambien .env.example a .env y cambiar debug_mode a false

**2.1. Arranque de EV_Drivers** :

```bash
python Driver/EV_Driver.py <localhost>:9092 driver_1
```

Parámetro:

- `localhost:9092`: Dirección del broker Kafka (cambiar por IP de PC1 si es remoto)
- `driver_1`: ID del conductor simulado

**2.2 Arranque del Frontend**:

```bash
npm install -g http-server
http-server
```

o si no tienes npm instalado:

```bash
python -m http.server 8080
```

Nota: La parte de Frontend se está sirviendo desde el directorio Core/Front. Para acceder a la interfaz web, abrir el navegador y navegar a `http://<HOST_DE_FRONTEND>:8080`. En cuanto a monitorización de fronted, se debe de configurar la variable API_CENTRAL_HOST en el archivo `Core/Front/dashboard.js` con la IP de PC1 si es remoto.

---

#### Paso 3: Configuración de PC 2 (Charging Point y EV_WEATHER)

**3.1. Configuración del archivo .env para PC2**:

```env

# Charging Configuration
CHARGING_POINT_PRICE_PER_KWH=0.20
MAX_CHARGING_DURATION=3000

# Heartbeat Configuration
MONITOR_TO_CENTRAL_HEARTBEAT_INTERVAL=30
MONITOR_TO_ENGINE_HEARTBEAT_INTERVAL=30
MONITOR_TO_ENGINE_HEARTBEAT_TIMEOUT=90

# SSL Configuration
MONITOR_SSL_ENABLED=true
MONITOR_SSL_VERIFY=false

# Registry Configuration
REGISTRY_HOST=localhost

# API_CENTRAL_IP, port
API_CENTRAL_PORT=8000
API_CENTRAL_HOST=192.168.24.1
```

Notar: Cambiar `REGISTRY_HOST` y `API_CENTRAL_HOST` por la IP de PC1 si es remoto.

**3.2. Arranque de EV_CP_E (Engine)**:

Primero arrancar el Engine:

```bash
python Charging_point/Engine/EV_CP_E.py localhost:9092 --debug_port 50032
```

Parámetros:

- `localhost:9092`: Dirección del broker Kafka(Cambiar por IP de PC1 si es remoto)
- `--debug_port 50032`: Puerto para depuración (opcional)

NOTA: Si Kafka está en otra máquina, usar su IP en lugar de localhost.

**3.3. Arranque de EV_CP_M (Monitor)**:

En una nueva terminal, arrancar el Monitor:

```bash
python Charging_point/Monitor/EV_CP_M.py localhost:50032 localhost:6001 cp_001 --location Madrid
```

Parámetros:

- `localhost:50032`: Dirección del Engine local
- `localhost:6001`: Dirección de EV_Central (IP de PC1 si es remoto)
- `cp_001`: ID del Charging Point
- **3.4. Arranque de EV_W (Weather Control Office)**:

```bash
python Core/Weather/EV_W.py
```

**entorno .env**:

```
# Weather module enviroments
WEATHER_HOST=<127.0.0.1>
WEATHER_PORT=7001
# Weather check interval in seconds
WEATHER_CHECK_INTERVAL=4
# Central API URL for weather updates
CENTRAL_API_URL_FOR_WEATHER=/ev/central/city-temperature
# Critical temperature threshold for alerts
CRITICAL_TEMPERATURE=0.0
# Location reload interval in seconds
LOCATION_RELOAD_INTERVAL=10
```

Nota: Una vez iniciado EV_W, se debe de cambiar la variable de entorno de PC1 en el archivo .env:

```
WEATHER_HOST=<IP_DE_PC3>
```

---

#### Paso 4: Registro y Autenticación del CP

**4.1. Registro del CP en Registry**:

Una vez arrancado EV_CP_M, aparecerá el menú CLI.

```

=== Menú Monitor del Charging Point ===

╭──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╮
│ EV_CP_M - MONITOR CONTROL MENU                                                                                               │
│ ============================================================                                                                 │
│                                                                                                                              │
│ INFORMATION:                                                                                                                 │
│   [2] Show connection details                                                                                                │
│                                                                                                                              │
│ Registered Commands:                                                                                                         │
│   [3] Register Charging Point(EV Registry)                                                                                   │
│   [4] Delete Charging Point(EV Registry)                                                                                     │
│                                                                                                                              │
│ Authentication:                                                                                                              │
│   [5] Authenticate with Central                                                                                              │
│                                                                                                                              │
│ Change CP location:                                                                                                          │
│   [6] Change Charging Point location                                                                                         │
│                                                                                                                              │
```

Seleccione una opción: 3

````

El sistema hace una petición para registrar el CP en EV_Registry.:

- **ID del CP**: Por ejemplo `CP_001`
- **Localización**: Por ejemplo `Madrid`

El Monitor enviará una petición HTTPS al Registry:

```json
POST https://192.168.1.102:8000/ev/register
{
  "cp_id": "CP_001",
  "location": "Madrid"
}
````

Si el registro es exitoso, recibirá un **token JWT** que se almacenará automáticamente para su uso en la autenticación.

```
{
    "cp_credential": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE3NjYxMjY4NTUsImlhdCI6MTc2NjA4MzY1NSwic3ViIjoiMSIsImxvY2F0aW9uIjoiTWFkcmlkIn0.SC48eUg8nktjSIWuvTKE_O04lcOLLntPZ4nHOrceSqc",
    "message": "Charging point CP_001 registered or updated at location Madrid"
}
```

**4.2. Autenticación del CP en Central**:

Después del registro exitoso, seleccionar la opción de autenticación:

```
Seleccione una opción: 5
```

El Monitor:

1. Establece conexión API Rest con API_CENTRAL (puerto 8000)
2. Envía el token JWT recibido del Registry
3. API_CENTRAL verifica el token contra la base de datos
4. Si es válido, API_CENTRAL genera una **clave de cifrado simétrica AES** única para este CP
5. API_CENTRAL devuelve la clave al Monitor
6. Monitor almacena la clave para cifrar todas las comunicaciones futuras

Estado esperado:

```
{
    "symmetric_token": "6HFZLA7TcTECt7YV6CQu0vNuKfyrVk5lqVDA-h3WPTw="
}
```

**4.3. Configurar localizaciones en Weather**:

Para cambiar o agregar una nueva localización a monitorizar en EV_W, usar el fronted o enviar una petición API, para enviar la petición API, el formato es el siguiente:

![1766084709518](image/readme/1766084709518.png)

---

#### Paso 5: Verificación del Sistema

**5.1. Verificar conectividad de componentes**:

Verificar que todos los servicios están activos:

```bash
# Verificar Docker
docker ps

# Verificar API_Central
curl http://localhost:8000/ev/central/charging-points

# Verificar Registry
curl -k https://localhost:7002/health

# Verificar base de datos
python scripts/test_db_connection.py
```

**5.2. Verificar API_Central** (consumir desde navegador o curl):

```bash
# Ver estado de todos los Charging Points
curl http://localhost:8000/ev/central/charging-points

# Ver estado de Drivers
curl http://localhost:8000/ev/central/drivers

# Ver eventos de auditoría
curl http://localhost:8000/ev/central/audit-events
```

Respuesta esperada para CPs:

```json
{
  "charging_points": [
    {
      "cp_id": "1",
      "created_at": "2025-12-18T18:58:31.122240",
      "last_connection_time": null,
      "location": "Madrid",
      "price_per_kwh": 0.2,
      "status": "ACTIVE"
    }
  ]
}
```

**5.3. Simular Driver y recarga**:

En el CLI de EV_Driver:

```
╭──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╮
│ EV_DRIVER - DRIVER CONTROL MENU                                                                                              │
│ ============================================================                                                                 │
│                                                                                                                              │
│ CHARGING OPERATIONS:                                                                                                         │
│   [1] List available charging points                                                                                         │
│   [2] Request charging (requires CP ID)                                                                                      │
│   [3] Stop current charging session                                                                                          │
│                                                                                                                              │
│ INFORMATION:                                                                                                                 │
│   [4] Show current charging status                                                                                           │
│   [5] Show charging history                                                                                                  │
│                                                                                                                              │
│ AUTO MODE:                                                                                                                   │
│   [6] Start auto-charging (load from file)                                                                                   │
│                                                                                                                              │
│ ============================================================
Seleccione una opción: 1
Introducir el ID del Charging Point para solicitar recarga: CP_001
```

El sistema:

1. Driver envía solicitud por Kafka topic
2. Central recibe y asigna recarga
3. Engine procesa la recarga

**5.4. Verificar auditoría de eventos**:

Los eventos se registran automáticamente en la base de datos. Consultar vía API:

```bash
curl http://localhost:8000/ev/central/audit-events | python -m json.tool
```

o desde postman/navegador.

![1766084847711](image/readme/1766084847711.png)

Eventos esperados:

- Registro de CP_001
- Autenticación de CP_001
- Asignación de clave de cifrado
- Inicio de recarga DRIVER_001
- etc.

**5.5. Verificar alerta climatológica**:

Para probar este escenario:

1. **Agregar ciudad con temperatura baja** en EV_W:

   - En invierno: Oslo, Helsinki, Reykjavik, Alaska
   - Ejemplo: `Oslo` (temperatura típica < 0°C en invierno)

2. **Observar logs de EV_W**:

![1766084933159](image/readme/1766084933159.png)

3. **Verificar que CP en Oslo pasa a "fuera de servicio"**:

   ```bash
   curl http://localhost:8000/ev/central/all-charging-points
   ```

4. **Cuando temperatura suba > 0°C**:

   - EV_W envía cancelación de alerta
   - CP vuelve a estado operativo

**5.6. Verificar revocación y restauración de claves**:

En el CLI de EV_Central (AdminCLI):

```
╭──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╮
│ EV_CENTRAL - ADMIN CONTROL MENU                                                                                              │
│ ============================================================                                                                 │
│                                                                                                                              │
│ CHARGING POINT MANAGEMENT:                                                                                                   │
│   [1] List all charging points                                                                                               │
│   [2] List available charging points                                                                                         │
│   [3] List active charging points                                                                                            │
│   [4] Show charging point status (requires CP ID)                                                                            │
│                                                                                                                              │
│ SESSION MANAGEMENT:                                                                                                          │
│   [5] List all charging sessions                                                                                             │
│   [6] Show session details (requires Session ID)                                                                             │
│                                                                                                                              │
│ CONTROL:                                                                                                                     │
│   [9] Stop charging point (requires CP ID or 'all')                                                                          │
│   [10] Resume charging point (requires CP ID or 'all')                                                                       │
│   [11] Revoke symmetric key for a charging point (requires CP ID)                                                            │
│                                                                                                                              │
│ SYSTEM:                                                                                                                      │
│   [12] Show system statistics                                                                                                │
│                                                                                                                              │
│ ============================================================                                                                 │
╰──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
...
Seleccione una opción: 11
Ingrese ID del CP: CP_001
✓ Claves de CP_001 revocadas
```

El CP_001 quedará fuera de servicio(stopped). Para restaurar se debe de re-autenticar desde el Monitor

En EV_CP_M, seleccionar opción de re-autenticación:

```
El Monitor detectará que las claves fueron revocadas y solicitará re-autenticación automáticamente.
```

**5.7. Verificar cifrado de mensajes** (opcional - análisis técnico):

Usar herramienta de lectura de topics Kafka:

```bash
python scripts/tools/kafka_topic_reader.py
```

Los mensajes del CP deberían aparecer cifrados:

```
Topic: cp_to_central
Message: {"data": "U2FsdGVkX1+vupppZksvRf5pq5g5XjFRl..."}  # Cifrado AES
```

Para descifrar (solo para verificación):

```bash
python scripts/decryMessageExample.py
```

Nota: cambiar la clave en el script por la clave simétrica del CP.

---

### 2.4 Solución de Problemas Comunes

| Problema                          | Causa posible                                                 | Solución                                                                                                                        |                          |
| --------------------------------- | ------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- | ------------------------ |
| **Docker no inicia**              | Docker Desktop no corriendo                                   | Iniciar Docker Desktop y esperar a que esté listo                                                                               |                          |
| **Error: Module not found**       | Entorno virtual no activado o dependencias no instaladas      | `pip install -r requirements.txt`                                                                                               |                          |
| **PostgreSQL connection refused** | Puerto 5434 bloqueado o contenedor no corriendo               | `docker ps` para verificar, `docker compose restart postgresql_db`                                                              |                          |
| **Kafka connection timeout**      | Kafka no está listo o puerto bloqueado                        | Esperar 30s después de `docker compose up`, verificar logs: `docker logs broker`                                                |                          |
| **CP no puede registrarse**       | Certificado SSL inválido o Registry no escuchando             | `python ssl/generate_ssl_key.py` y verificar que Registry está en puerto funcionando                                            |                          |
| **Registry HTTPS error**          | MONITOR_SSL_VERIFY=true pero usando certificados autofirmados | Cambiar a `MONITOR_SSL_VERIFY=false` en .env                                                                                    |                          |
| **Autenticación fallida**         | CP no registrado previamente                                  | Primero registrar CP en Registry, luego autenticar                                                                              |                          |
| **Mensajes no llegan a Central**  | Firewall bloqueando puertos o Kafka topics no creados         | Abrir puertos 9092, 6001. Verificar topics:`docker exec broker kafka-topics.sh --list --bootstrap-server localhost:9092`        |                          |
| **EV_W no obtiene temperatura**   | API Key inválida o sin conexión a Internet                    | Verificar OPENWEATHER_API_KEY en .env, probar:`curl "http://api.openweathermap.org/data/2.5/weather?q=Madrid&appid=TU_API_KEY"` |                          |
| **API_Central 404**               | Ruta incorrecta o API no iniciada                             | Verificar que API_Central está corriendo, usar rutas completas:`/ev/central/...`                                                |                          |
| **JWT token expired**             | Token expiró                                                  | Volver a registrar CP en Registry para obtener nuevo token                                                                      |                          |
| **Clave de cifrado no funciona**  | Clave fue revocada                                            | Re-autenticar CP en Central                                                                                                     |                          |
| **Database migration error**      | Esquema de BD no inicializado                                 | Borrar contenedor PostgreSQL y recrear:`docker compose down -v && docker compose up -d`                                         |                          |
| **Puerto ya en uso**              | Otro proceso usando el puerto                                 | En Windows:`netstat -ano                              findstr :6001 `y matar proceso.                                           | En Linux:`lsof -i :6001` |

**Logs para diagnóstico**:

```bash
# Logs de Kafka
docker logs broker

# Logs de PostgreSQL
docker logs postgres-evcharging

# Activar modo DEBUG en .env
DEBUG_MODE=true  # Mostrará logs detallados en todos los componentes
```

**Reinicio completo del sistema** (si nada funciona):

```bash
# Detener todos los procesos Python (Ctrl+C en cada terminal)

# Reiniciar Docker
docker compose down
docker compose up -d

```

---

## 3. Conclusiones

Esta nueva versión ha mejorado considerablemente la seguridad y estabilidad del sistema.

Se han incorporado medidas de protección que incluyen:

- Verificación segura de credenciales
- Protección de las comunicaciones(SSL, Sockets seguros y cifrado simétrico)
- Registro detallado de todas las actividades

Esto asegura que los datos se manejen de forma segura y privada.

La conexión con el servicio meteorológico OpenWeather permite al sistema anticiparse a condiciones climáticas desfavorables, lo cual representa una ventaja importante para la operación diaria.

El diseño del sistema permite que pueda crecer fácilmente y sea sencillo de mantener.

En resumen, esta versión representa un paso importante, eficiente y fiable.

---

## 4. Referencias

- OpenWeather API Documentation: https://openweathermap.org/current
- PostgreSQL Documentation: https://www.postgresql.org/docs/
- SqlAlchemy Documentation: https://docs.sqlalchemy.org/
- AES Encryption Overview: https://en.wikipedia.org/wiki/Advanced_Encryption_Standard
- Socket seguro en Python: https://docs.python.org/3/library/ssl.html

---

## Anexos

### Anexo A: Certificados SSL

[Generador de certificados SSL](ssl/generate_ssl_key.py)

### Anexo B: Fichero de Configuraciones

[.env.example](.env.example)

### Anexo C: Scripts de despliegue

**Sólo válido si quiere desplegar todos en un único equipo para pruebas rápidas. En otro caso, ajustar los variables de entorno según corresponda.**

[Scripts de despliegue](scripts/tools)

### Anexo D: Leer topic de Kafka

[Script para leer topics de Kafka](scripts/tools/kafka_topic_reader.py)

![1766030361195](image/readme/1766030361195.png)

### Anexo E: Ejemplo de descifrado de mensajes

[Script de descifrado de mensajes](scripts/decryMessageExample.py)

### Anexo F: Prueba de API(Postman Collection)

[Postman Collection](postman/Api.postman_collection.json)

or

[Postman URL](https://gold-eclipse-429463.postman.co/workspace/Ev-charging~41312af9-48e5-43f8-901e-59a5ddf5510c/collection/25509473-a4257ef9-0dbc-4942-8900-6bc4671bbdcd?action=share&creator=25509473&active-environment=25509473-a2d8af31-6354-404c-bec7-5c84678a31ca)

---
