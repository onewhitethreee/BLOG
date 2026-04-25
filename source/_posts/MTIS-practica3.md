---
uuid: ced78e20-40fe-11f1-8f97-f5eae19f0f38
title: MTIS_practica3
date: 2026-04-26 01:30:56
tags:
---

# Practica 3: ESB - muleSoft

En esta práctica se centra en el uso de Mulesoft. En los siguientes apartados se describen los pasos para la ejecución de la práctica.

## 0. Cliente

En esta práctica también se hace uso de un cliente para consumir todos los servicios de proyecto mulesoft. El cliente se ha desarrollado en HTML y JavaScript.

### Captura de pantalla del cliente

![](https://img.164314.xyz/2026/04/42afbb88d830ef08679ad594a98b0758.png)

## 1. Empleados

En este ejercicio se hace el uso de componente SOAP para consumir nuestro servicio web de empleados que se ha creado en la práctica 1. El objetivo es comprobar que el servicio web funciona correctamente y se pueden obtener los datos de los empleados a través de Mulesoft.

### Comprobación de crear un nuevo empleado

![](https://img.164314.xyz/2026/04/b9b6665beef98d8a17cd7e94109f96d2.png)

### Consultar del empleado creado

![](https://img.164314.xyz/2026/04/62ffeca8a7db6c1ca4c87d705ed695e0.png)

### Modificar el empleado creado

Cambiamos el nombre del empleado que era "Juan" por "Hola" y cambiar el usuario válido(valor 1) por no válido(valor 0).

![](https://img.164314.xyz/2026/04/82ee15862bec1974cd9f78c75b6fabff.png)

### Eliminar el empleado creado

![](https://img.164314.xyz/2026/04/1b79ec4c723d4d37bb90d735193ee306.png)

Nos pide también de que cuando la operación ha sido realizada, se escribe el resultado en formato JSON y que se guarde en un archivo.

EL directorio donde se encuentra el archivo JSON es el siguiente:

```
empleados\src\main\resources\output
```

Este es el ejemplo de mensaje JSON(consultar) que se escribe en el archivo después de realizar la operación:

![](https://img.164314.xyz/2026/04/a6a48fa4487a4bb9f4650b8b1e2663b7.png)

En caso de que la operación no se ha realizado correctamente, se enviará un email de error.

Ejemplo de email de error cuando intenta borrar un empleado que no existe:

![](https://img.164314.xyz/2026/04/bef2a6ccd256e1f6dee4a3365fe2eed9.png)

---

## 2. Niveles

En este ejercicio es similar a ejercicio anterior, pero en lugar de SOAP usa componente REST para consumir los servicios que esten disponible de la práctica 1.

### Consultar

![](https://img.164314.xyz/2026/04/90393fbe6d877ece5927761a12e39989.png)

### Modificar

![](https://img.164314.xyz/2026/04/90393fbe6d877ece5927761a12e39989.png)

### Borrar

![](https://img.164314.xyz/2026/04/99fe78346ae000ea0dc9db4e2027acca.png)

### Crear

![](https://img.164314.xyz/2026/04/bea9082ba7f4ae05a4b5b9156b6dbd46.png)

### Salida correcta(fichero xml) y incorrecta(envío de email)

La salida correcta se guarda en un fichero XML en el directorio:

```
niveles\src\main\resources\output
```

![](https://img.164314.xyz/2026/04/52d9a566b85431d60cd175ccd4521f3c.png)

En caso de que la operación no se ha realizado correctamente, se enviará un email de error.

![](https://img.164314.xyz/2026/04/637c63984ce749039152af118d29f0fd.png)

## 3. Notificar Empleados No Válidos

En este ejercicio, se notifica aquellos usuario que no está válido(valor 0).

Hacemos la comprobacion con dos usuarios que no están válidos y uno válido, el resultado es el siguiente:

![](https://img.164314.xyz/2026/04/441be46917541b71cb6b020c6d55d45f.png)

![](https://img.164314.xyz/2026/04/5b2edad60fe60d382d23fbf2cd598fd1.png)

## 4. NotificarUsuario No Válidos En Todas Las Salas

Igual que el ejercicio anterior, usamos dos usuarios no válidos dentro de la sala y el resultado es el siguiente

![](https://img.164314.xyz/2026/04/7a4f1dc895ef5173c568818c702deefe.png)

![](https://img.164314.xyz/2026/04/6cd905721ce922e96751b5278e58dde1.png)

## 5. Similación de una nueva oficina

En este ejercicio se hace la simulación de una nueva oficina, que tiene la misma funcionalidad que la práctica anterior(práctica 2)

Estos son los resultados de la simulación y la parte de cliente:

CALOR_ON 24–28 °C  
CALOR_OFF 19–22 °C  
FRIO_ON 14–17 °C  
FRIO_OFF 19–22 °C
LUZ_SUBE 700–900 lm  
LUZ_BAJA 100–250 lm

![](https://img.164314.xyz/2026/04/bc20f60206f841ffef2df17c95bfc857.png)

![](https://img.164314.xyz/2026/04/35c09f23cd9a2505875a9d6145d82e69.png)

![](https://img.164314.xyz/2026/04/f1fb093d6527fcdb223da18a8fba54b6.png)

![](https://img.164314.xyz/2026/04/0d840a0b4c30b18a089fdf7c9ad132cc.png)

![](https://img.164314.xyz/2026/04/b14034e05b6f99a1c6ea448c91729f91.png)
