---
uuid: 6c6db7c0-d892-11f0-b00f-852cbeaeb8c7
title: SD_DEPLEY
date: 2025-12-14 03:13:04
tags:
---

### 2.3 Pasos de Despliegue

#### Paso 0: Preparación Inicial

**0.1. Crear entorno virtual de Python** (recomendado):

```bash
python -m venv venv

# En Windows:
venv\Scripts\activate

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
REGISTRY_PORT=7002
```

Una vez arrancado, el Registry estará disponible en `https://<HOST_DE_REGISTRY>:<PORT>/ev/registry/health`

Endpoints API REST:

- `POST /ev/registry/charging-points` - Registrar nuevo CP
- `GET /ev/registry/charging-points/<cp_id>` - Consultar CP
- `DELETE /ev/registry/charging-points/<cp_id>` - Dar de baja CP

Nota: Cambiar aquellos <> por la IP de PC1 si es remoto.

---

#### Paso 2: Configuración de PC 3

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

```

Notar: Cambiar `REGISTRY_HOST` por la IP de PC3 si es remoto.

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
IP_PORT_EV_CP_CENTRAL=<IP>:6001
# Weather module enviroments
WEATHER_HOST=127.0.0.1
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

---
