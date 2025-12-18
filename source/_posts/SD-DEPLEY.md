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
