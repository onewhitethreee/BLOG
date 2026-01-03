---
uuid: 0c0d6920-e66b-11f0-aad7-1978c6b97adb
title: security_sd
date: 2025-12-31 18:06:28
tags:
---

# Canal seguro y sus propiedades

Propiedad que debe de cumplir:

- Cada proceso está seguro de la indentificación del otro proceso.
- Los datos son privados y protegidos.
- Protección contra la reproducción de mensajes.

Conceptos clave:

- Confidencialidad: Solo el emisor y el receptor pueden entender el mensaje.
- Integridad: El mensaje no ha sido alterado durante la transmisión.
- Disponibilidad: El servicio está disponible cuando se necesita.

# Amenazas y vulnerabilidades

- **Servicio expuesto por el Internet.**
  - Visibile por herramientas como nmap, SHODAN, wireshark.
  - Posibilidad de acceso no autorizado.
  - Sistema desactualizado.
  - Errores de programación.
- **Acceso físico a la infraestructura.**
  - Routes en lugares públicos.
- **Proximidad a la infraestructura.**
  - Wifi, Bluetooth, NFC, etc.
- **Desastres naturales.**
  - Incendios.
  - Inundaciones.

## El panorama de amenazas

- Malware
- Phishing
- Vishing
- Reversing
- Zero-day

# Criptografía simétrica: La clave secreta compartida

Utliza la misma clave para cifrar y descifrar.

## Algoritmos simétricos comunes

- DES (Data Encryption Standard), 3DES (Triple DES), AES (Advanced Encryption Standard) -> Cifrado por bloques
- ChaCha20, RC4 -> Cifrado por flujo

### KDC(Key Distribution Center)

KDC = 大家都信任的"密钥管理员"

帮用户和服务安全地交换密钥，不用直接传密码

![](https://img.164314.xyz/2025/12/ca40cb179c693d175efd5c9a68736c81.png)

# Criptografía asimétrica: Clave pública y clave privada

Utiliza un par de claves: una clave pública para cifrar y una clave privada para descifrar.

## Algoritmos asimétricos comunes

- RSA (Rivest-Shamir-Adleman)
- ECC (Elliptic Curve Cryptography)

# Soluciones híbridas de clave simétrica y asimétrica

Debido a la eficiencia de la criptografía simétrica y la seguridad de la criptografía asimétrica, muchas aplicaciones utilizan una combinación de ambas. -> **SSL/TLS**

![](https://img.164314.xyz/2025/12/eb5a77e5d6d89c3a3fbe5e642ff57394.png)

# Funciones hash criptográficas

Resuelve el problema de **integridad**, **auntenticidad** y **no repudio** de mensajes.

Resumen de hash: Convertir un mensaje de cualquier longitud en una cadena de longitud fija.

Características:

- Determinista: El mismo mensaje siempre produce el mismo hash.
- Undireccionalidad
- Resistencia a colisiones(débil)
- Resistencia a colisiones(fuerte)

## Rainbow tables(Falsificación de hash)

1. Preparación: Crear una tabla de hashes precomputados para posibles contraseñas.
2. Ataque: Cuando se obtiene un hash, buscar en la tabla para encontrar la contraseña

![](https://img.164314.xyz/2025/12/21cc43575064939a9a2a195e51143961.png)

### Algoritmos hash comunes

- MD5 (Message Digest Algorithm 5) -> Obsoleto
- SHA-1 -> Obsoleto
- SHA-256
- SHA-512
- Bcrypt

# Firmas digitales

![](https://img.164314.xyz/2025/12/ab313ed99af147b9c197ed0b373a7786.png)

# PKI（公钥基础设施）

---

## 一句话解释

**PKI = 一套管理数字证书和公钥的系统，用来证明"你是你"**

---

## 简单比喻

```
现实世界                    数字世界
─────────                   ─────────
身份证                  →   数字证书
公安局/政府             →   CA（证书颁发机构）
身份证防伪标志          →   数字签名
验证身份证真假          →   验证证书链
```

---

## PKI 解决什么问题？

```
问题：你怎么确定对方的公钥是真的？

小明说："这是我的公钥"
      ↓
你怎么知道这真的是小明的，不是黑客伪造的？

      ↓
PKI：让权威机构（CA）担保！
```

---

## PKI 核心组件

```
┌─────────────────────────────────────────────┐
│                    PKI                       │
│                                             │
│   ┌─────────┐    ┌─────────┐    ┌────────┐  │
│   │   CA    │    │  证书   │    │  用户  │  │
│   │证书颁发 │    │         │    │        │  │
│   │  机构   │    │         │    │        │  │
│   └─────────┘    └─────────┘    └────────┘  │
│        │              │              │      │
│   ┌─────────┐    ┌─────────┐    ┌────────┐  │
│   │   RA    │    │  CRL    │    │ 密钥对 │  │
│   │注册机构 │    │吊销列表 │    │公钥私钥│  │
│   └─────────┘    └─────────┘    └────────┘  │
│                                             │
└─────────────────────────────────────────────┘
```

| 组件     | 作用                      |
| -------- | ------------------------- |
| **CA**   | 颁发证书的权威机构        |
| **RA**   | 审核申请者身份            |
| **证书** | 包含公钥+身份信息+CA 签名 |
| **CRL**  | 被吊销的证书列表          |

---

## 证书里有什么？

```
┌─────────────────────────────────────┐
│           数字证书                   │
├─────────────────────────────────────┤
│  持有者：www.google.com             │
│  公钥：MIIBIjANBgkq...              │
│  颁发者：DigiCert CA                │
│  有效期：2024-01-01 到 2025-01-01   │
│  序列号：1234567890                  │
│  用途：服务器身份认证               │
├─────────────────────────────────────┤
│  CA的数字签名：xxxxxxxx...          │
│  (用CA私钥签的，证明这证书是真的)    │
└─────────────────────────────────────┘
```

---

## 证书链（信任链）

```
┌──────────────────┐
│    根证书 CA     │  ← 预装在系统/浏览器里（自签名）
│   (最高权威)      │
└────────┬─────────┘
         │ 签发
         ↓
┌──────────────────┐
│   中间证书 CA    │  ← 由根CA签发
│                  │
└────────┬─────────┘
         │ 签发
         ↓
┌──────────────────┐
│  网站证书        │  ← 你看到的证书
│  www.google.com  │
└──────────────────┘
```

### 验证过程

```
1. 收到网站证书
2. 检查：谁签发的？→ 中间CA
3. 检查：中间CA谁签的？→ 根CA
4. 检查：根CA在信任列表里吗？→ ✅ 在
5. 整条链都有效 → 信任这个证书
```

---

## PKI 工作流程

```
1. 申请证书
   网站 → 生成密钥对 → 提交申请给CA

2. 审核身份
   CA → 验证你确实拥有这个域名

3. 颁发证书
   CA → 用CA私钥签名 → 发给你证书

4. 使用证书
   用户访问网站 → 网站出示证书 → 用户验证

5. 吊销（如果私钥泄露）
   通知CA → CA加入吊销列表（CRL）
```

---

## 常见 CA 机构

| CA            | 说明             |
| ------------- | ---------------- |
| DigiCert      | 商业 CA，很贵    |
| Let's Encrypt | **免费**，自动化 |
| Comodo        | 商业 CA          |
| GlobalSign    | 商业 CA          |

---

## 实际例子：HTTPS

```
浏览器访问 https://google.com

1. 服务器发来证书
2. 浏览器检查：
   - 证书没过期？ ✓
   - 域名匹配？ ✓
   - CA签名有效？ ✓
   - CA在信任列表？ ✓
3. 验证通过 → 显示🔒
4. 用证书里的公钥进行TLS握手
```

---

## PKI vs 其他概念

| 概念  | 关系            |
| ----- | --------------- |
| PKI   | 整套体系        |
| CA    | PKI 的核心组件  |
| 证书  | PKI 颁发的产物  |
| HTTPS | 使用 PKI 的应用 |
| TLS   | 使用证书的协议  |

---

## 一句话总结

> **PKI** = 用权威机构（CA）担保公钥真实性的信任体系
>
> 核心：CA 颁发证书 → 证书包含公钥 → 形成信任链
>
> 应用：HTTPS、数字签名、代码签名、VPN 等

# UEFI Secure boot

![](https://img.164314.xyz/2026/01/6a76aed81860fffb559bbf11cd260993.png)

# El marco de seguridad AAA

## Autenticación(Quién eres?)

Verifica la identidad de un usuario mediante sus credenciales. 

## Autorización(Qué puedes hacer?)

Define los permismos especificos del usuario ya autenticado.

## Accounting(Registro de actividades)

Registra las acciones realizadas por los usuarios para auditoría y monitoreo.

## Ejemplo de implementación AAA

- RADIUS (Remote Authentication Dial-In User Service)
- TACACS+ (Terminal Access Controller Access-Control System Plus)
- DIAMETER
- Kerberos

### Kerberos(un flujo de autenticación centralizada)

1. Autenticación inicial: El cliente se autentica en el AS y obtiene un Ticket Granting Ticket (TGT).
2. Autorización de servicios: El cliente usa el TGT para solicitar un ticket de servicio al TGS.
3. Acceso al servicio: El cliente presenta el ticket de servicio al servidor para acceder al recurso.

![](https://img.164314.xyz/2026/01/cb3dc4823ccf0795b6d26e127abc3ab4.png)

# OAuth 2.0

1. El cliente redirige al usuario al servidor de autorización(Google ej).
2. El servidor de autorizacion devuelve un código temporal para el cliente.
3. El cliente intercambia el código temporal por un token de acceso en el servidor de autorización
4. El cliente usa el token de acceso para acceder a los recursos protegidos en el servidor de recursos.

![](https://img.164314.xyz/2026/01/ad798d7a6ac0a522f1e480ca2ab30940.png)

# Defensa en Profundidad（纵深防御）

## 一句话
**就是"多层保护"，一层被攻破还有下一层**

---

## 生活类比

```
保护城堡：

    护城河 → 城墙 → 卫兵 → 内城 → 密室
    
    敌人要攻破所有层才能得手！
```

---

## 在计算机安全中

```
┌─────────────────────────────────┐
│         第1层：防火墙            │  ← 挡住外部攻击
│  ┌───────────────────────────┐  │
│  │      第2层：入侵检测        │  │  ← 发现可疑行为
│  │  ┌─────────────────────┐  │  │
│  │  │   第3层：访问控制     │  │  │  ← 验证身份权限
│  │  │  ┌───────────────┐  │  │  │
│  │  │  │ 第4层：加密数据 │  │  │  │  ← 即使被偷也看不懂
│  │  │  └───────────────┘  │  │  │
│  │  └─────────────────────┘  │  │
│  └───────────────────────────┘  │
└─────────────────────────────────┘
```

---

## 常见的防御层

| 层次 | 例子 |
|-----|------|
| 物理层 | 机房门禁、监控 |
| 网络层 | 防火墙、VPN |
| 主机层 | 杀毒软件、补丁 |
| 应用层 | 输入验证、WAF |
| 数据层 | 加密、备份 |

---

## 核心思想

> **不要把所有鸡蛋放一个篮子里**
> 
> 任何单一防护都可能失效，所以要叠加多层！

**简单说：就是"不信任任何一层，层层设防"** 🛡️🛡️🛡️


# Pricipios de diseño seguro

- Minimo privilegio: Cada usuario o proceso debe tener solo los permisos necesarios para realizar su función.
- Economía de mecanismos: Utilizar el menor número posible de mecanismos de seguridad para reducir la complejidad.
- Minima superficie de ataque: Reducir las posibles vías de ataque limitando las funciones y servicios expuestos.
- Diseño abierto: La seguridad no debe depender del secreto del diseño o la implementación.
- Separación de privilegios: Dividir las funciones críticas entre múltiples entidades para evitar el abuso de poder.
- Seguridad por defecto: Configurar los sistemas para que sean seguros desde el inicio, sin necesidad de ajustes adicionales.

# Principio de Kerckhoffs（柯克霍夫原则）

## 一句话
**加密系统的安全性应该只依赖于密钥，而不是算法本身的保密**

---

## 简单类比

```
好的锁：
  - 锁的设计可以公开（所有人都知道怎么造）
  - 安全靠钥匙（只有你有钥匙）
  
坏的锁：
  - 靠"别人不知道锁怎么开"来保护
  - 一旦被人研究明白，就完蛋了
```

---

## 举个例子

| 做法 | 安全性 |
|-----|--------|
| ❌ 自己发明一个加密算法，不告诉别人 | 脆弱（被逆向就完了） |
| ✅ 用公开的 AES 算法 + 保密的密钥 | 安全 |

---

## 为什么这样更安全？

```
公开算法的好处：

1. 全世界专家帮你找漏洞
2. 算法经过充分测试
3. 即使敌人知道算法，没密钥也没用

隐藏算法的坏处：

1. 你以为安全，其实可能很弱
2. 一旦泄露，全线崩溃
3. 没人帮你审查
```

---

## 现实应用

| 系统 | 是否遵循 |
|-----|---------|
| AES、RSA | ✅ 算法完全公开 |
| HTTPS/TLS | ✅ 协议公开 |
| 某些私有加密 | ❌ 靠隐藏算法 |

---

## 总结

> **算法可以公开，密钥必须保密**
> 
> 安全不应该靠"别人不知道"来维持

**简单说：好的加密 = 公开的算法 + 保密的钥匙** 🔑

# El factor humano en la seguridad y el sistema 

- Concienciación y formación del usuario. 
- Robustecimiento del sistema(Hardening).

## Defensa de la red y auditoría

1. Defensa de la red
   - Firewalls
   - Sistemas de detección y prevención de intrusiones (IDS/IPS)
   - Segmentación de red
2. Auditoría y monitoreo

## Controles avanzados

- Virtualización de seguridad y contenedores
- Herramientas de análisis de vulnerabilidades(rkhunter)
- Enjaular de servicios (chroot, sandboxing)