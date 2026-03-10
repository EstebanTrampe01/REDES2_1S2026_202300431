# MANUAL TÉCNICO COMPLETO - PROYECTO REDES 2

---

**Proyecto:** Redes 2 - Proyecto 1  
**Autor:** Juan Esteban Chacón Trampe  
**Carné:** 202300431  
**Fecha:** 10 de Marzo 2026

---

## ÍNDICE GENERAL

### PARTE I: TOPOLOGÍA Y DISEÑO

1. [Descripción General del Proyecto](#parte-i-topología-y-diseño)
2. [Topología de Red](#2-topología-de-red)
   - 2.1 [Red MAN - Backbone Principal](#21-red-man---backbone-principal)
   - 2.2 [Edificio Izquierdo](#22-edificio-izquierdo)
   - 2.3 [Edificio Derecho](#23-edificio-derecho)
3. [Esquema de Direccionamiento](#3-esquema-de-direccionamiento)
   - 3.1 [Direccionamiento FLSM](#31-direccionamiento-flsm)
   - 3.2 [Direccionamiento VLSM](#32-direccionamiento-vlsm)
   - 3.3 [Red MAN - Enlaces Punto a Punto](#33-red-man---enlaces-punto-a-punto)
4. [Configuración de Servicios](#4-configuración-de-servicios)
   - 4.1 [Configuración de VLANs](#41-configuración-de-vlans)
   - 4.2 [Configuración de VTP](#42-configuración-de-vtp)
   - 4.3 [Configuración de DHCP](#43-configuración-de-dhcp)
5. [Enrutamiento Dinámico](#5-enrutamiento-dinámico)
6. [EtherChannel](#6-etherchannel)
   - 6.1 [LACP - Edificio Izquierdo](#61-lacp---edificio-izquierdo)
   - 6.2 [PAgP - Edificio Derecho](#62-pagp---edificio-derecho)
7. [ACLs (Listas de Control de Acceso)](#7-acls-listas-de-control-de-acceso)
8. [Pruebas de Conectividad](#8-pruebas-de-conectividad-diseño)
9. [Protocolos Implementados](#9-protocolos-implementados)
10. [Comandos de Verificación](#10-comandos-de-verificación-parte-i)
11. [Conclusiones](#11-conclusiones)

### PARTE II: PRUEBAS Y EVIDENCIAS

1. [Comprobación de DHCP](#parte-ii-1-comprobación-de-dhcp)
   - 1.1 [Edificio Izquierdo](#parte-ii-11-edificio-izquierdo)
   - 1.2 [Edificio Derecho](#parte-ii-12-edificio-derecho)
   - 1.3 [Red Administrativa](#parte-ii-13-red-administrativa)
2. [Switches Multicapa Funcionales](#parte-ii-2-switches-multicapa-funcionales)
   - 2.1 [Edificio Izquierdo](#parte-ii-21-edificio-izquierdo)
   - 2.2 [Edificio Derecho](#parte-ii-22-edificio-derecho)
   - 2.3 [Red MAN](#parte-ii-23-red-man)
3. [Pruebas de Conectividad](#parte-ii-3-pruebas-de-conectividad)
   - 3.1 [VLAN Naranja](#parte-ii-31-vlan-naranja)
   - 3.2 [VLAN Verde](#parte-ii-32-vlan-verde)
   - 3.3 [VLAN Admin](#parte-ii-33-vlan-admin)
4. [Bloqueo ACL](#parte-ii-4-bloqueo-acl)
   - 4.1 [Verde hacia Naranja](#parte-ii-41-verde-hacia-naranja)
   - 4.2 [Naranja hacia Verde](#parte-ii-42-naranja-hacia-verde)
   - 4.3 [VLANs hacia Admin](#parte-ii-43-vlans-hacia-admin)
5. [Tolerancia a Fallos](#parte-ii-5-tolerancia-a-fallos)
   - 5.1 [Test 1 - LACP (Edificio Izquierdo)](#parte-ii-51-test-1---lacp-edificio-izquierdo)
   - 5.2 [Test 2 - PAgP (Edificio Derecho)](#parte-ii-52-test-2---pagp-edificio-derecho)

### PARTE III: COMANDOS DE CONFIGURACIÓN

1. [Red MAN - Backbone](#parte-iii-1-red-man---backbone)
2. [Servidores DHCP](#parte-iii-2-servidores-dhcp)
3. [Edificio Izquierdo - LACP](#parte-iii-3-edificio-izquierdo---lacp)
4. [Edificio Derecho - PAgP](#parte-iii-4-edificio-derecho---pagp)
5. [ACLs](#parte-iii-5-acls)
6. [Comandos de Verificación](#parte-iii-6-comandos-de-verificación)
7. [Configuraciones Completas por Dispositivo](#parte-iii-7-configuraciones-completas-por-dispositivo)

---
---
---

# PARTE I: TOPOLOGÍA Y DISEÑO

---

# 1. Descripción General del Proyecto

La infraestructura incluye:

- **Red MAN (Metropolitan Area Network):** Interconexión de switches multicapa (MS1, MS2, MS6, MS7) mediante enlaces por EIGRP
- **Dos edificios independientes:** Edificio Izquierdo y Edificio Derecho, cada uno con su propia topología de acceso
- **Segmentación mediante VLANs:** Cinco VLANs configuradas para separar tráfico por edificio y función.
- **Servicios DHCP:** Dos servidores DHCP redundantes para asignación dinámica de direcciones IP
- **Protocolos de gestión:** VTP para propagación de VLANs y DHCP Relay para enrutamiento de solicitudes DHCP

---

# 2. Topología de Red

## 2.1 Red MAN - Backbone Principal

### Diagrama de Red Ampliada

![Diagrama de Red MAN](img/image.png)

### DIAGRAMA DE EDIFICIO CENTRAL

![alt text](img/image-57.png)

### Interconexiones del EDIFICIO CENTRAL

| Dispositivo A | Interfaz A | Dispositivo B | Interfaz B |
| ------------- | ---------- | ------------- | ---------- |
| MS1           | Gig1/1/1   | MS2           | Gig1/1/1   |
| MS2           | Gig1/1/2   | MS6           | Gig1/1/2   |
| MS6           | Gig1/1/1   | MS7           | Gig1/1/1   |
| MS7           | Gig1/1/2   | MS1           | Gig1/1/2   |
| MS7           | Gig1/1/3   | MS2           | Gig1/1/3   |

### Direccionamiento MS1

| Dispositivo | Interfaz | IP         |
| ----------- | -------- | ---------- |
| MS1         | Gig1/1/1 | 10.4.31.1  |
| MS1         | Gig1/1/2 | 10.4.31.14 |

### Direccionamiento MS2

| Dispositivo | Interfaz | IP         |
| ----------- | -------- | ---------- |
| MS2         | Gig1/1/1 | 10.4.31.2  |
| MS2         | Gig1/1/2 | 10.4.31.5  |
| MS2         | Gig1/1/3 | 10.4.31.18 |

### Direccionamiento MS6

| Dispositivo | Interfaz | IP        |
| ----------- | -------- | --------- |
| MS6         | Gig1/1/2 | 10.4.31.6 |
| MS6         | Gig1/1/1 | 10.4.31.9 |

### Direccionamiento MS7

| Dispositivo | Interfaz | IP         |
| ----------- | -------- | ---------- |
| MS7         | Gig1/1/1 | 10.4.31.10 |
| MS7         | Gig1/1/2 | 10.4.31.13 |
| MS7         | Gig1/1/3 | 10.4.31.17 |

### Servidores DHCP

Conexión de servidores DHCP a la red MAN:

| Servidor | Puerto MS1 | Puerto Servidor |
| -------- | ---------- | --------------- |
| DHCP1    | Gig1/0/1   | Fa0/0           |
| DHCP2    | Gig1/0/2   | Fa0/0           |

### Switch de Administración a PC0 Admin

| Switch | Dispositivo | Puerto   |
| ------ | ----------- | -------- |
| MSW6   | PC0 (Admin) | Gig1/0/1 |

---

## 2.2 Edificio Izquierdo

### DIAGRAMA DE RED IZQUIERDO

![alt text](img/image1.png)

### Interconexión de Switches Multicapa

#### MS7 - MS9 (Core)

| MS7   | MS9      |
| ----- | -------- |
| Fa0/1 | Gig1/0/1 |
| Fa0/2 | Gig1/0/2 |
| Fa0/3 | Gig1/0/3 |

#### MS7 - MS8 (Distribuidor)

| MS7   | MS8      |
| ----- | -------- |
| Fa0/4 | Gig1/0/4 |
| Fa0/5 | Gig1/0/5 |
| Fa0/6 | Gig1/0/6 |

#### MS9 (Core) - MS8 (Distribuidor)

| MS9   | MS8   |
| ----- | ----- |
| Fa0/7 | Fa0/7 |
| Fa0/8 | Fa0/8 |
| Fa0/9 | Fa0/9 |

### Conexión de Switches de Acceso

#### MS9 -> SW1

| MS9    | SW1    |
| ------ | ------ |
| Fa0/11 | Fa0/11 |
| Fa0/12 | Fa0/12 |

#### MS8 -> SW2

| MS8    | SW2    |
| ------ | ------ |
| Fa0/11 | Fa0/11 |
| Fa0/12 | Fa0/12 |

### Dispositivos Finales

| Switch | Dispositivo       | Puerto | VLAN |
| ------ | ----------------- | ------ | ---- |
| SW1    | PC1 (naranja)     | Fa0/1  | 10   |
| SW1    | PC2 (verde)       | Fa0/2  | 20   |
| SW2    | Laptop0 (naranja) | Fa0/1  | 10   |
| SW2    | Laptop1 (verde)   | Fa0/2  | 20   |

---

## 2.3 Edificio Derecho

### DIAGRAMA DE RED DERECHO

![alt text](img/image-1.png)

### Interconexión de Switches Multicapa

#### MS2 -> MS3

| MS3   | MS2      |
| ----- | -------- |
| Fa0/1 | Gig1/0/1 |
| Fa0/2 | Gig1/0/2 |
| Fa0/3 | Gig1/0/3 |

#### MS3 -> MS4

| MS3   | MS4   |
| ----- | ----- |
| Fa0/4 | Fa0/4 |
| Fa0/5 | Fa0/5 |
| Fa0/6 | Fa0/6 |

#### MS4 -> MS5

| MS4   | MS5   |
| ----- | ----- |
| Fa0/1 | Fa0/2 |
| Fa0/2 | Fa0/3 |

### Conexión de Switches de Acceso

#### MS5 -> SW3

| MS5    | SW3    |
| ------ | ------ |
| Fa0/21 | Fa0/21 |

#### MS5 -> SW4

| MS5    | SW4    |
| ------ | ------ |
| Fa0/22 | Fa0/22 |

### Dispositivos Finales

| Switch | Dispositivo     | Puerto | VLAN |
| ------ | --------------- | ------ | ---- |
| SW3    | Laptop2 (verde) | Fa0/11 | 40   |
| SW3    | PC3 (naranja)   | Fa0/12 | 30   |
| SW4    | Laptop3 (verde) | Fa0/11 | 40   |
| SW4    | PC4 (naranja)   | Fa0/12 | 30   |

---

# 3. Esquema de Direccionamiento

## 3.1 Direccionamiento FLSM

### Tabla Teórica FLSM

| VLAN   | Red            | Máscara | Hosts disponibles |
| ------ | -------------- | ------- | ----------------- |
| VLAN10 | 192.188.31.0   | /27     | 30                |
| VLAN20 | 192.188.31.32  | /27     | 30                |
| VLAN30 | 192.188.31.64  | /27     | 30                |
| VLAN40 | 192.188.31.96  | /27     | 30                |
| VLAN99 | 192.188.31.128 | /27     | 30                |

> **Nota:** En el esquema FLSM todas las subredes utilizan la misma máscara (/27). Aunque simplifica el diseño, provoca desperdicio de direcciones IP en redes que requieren menos hosts, como la VLAN de administración.

---

## 3.2 Direccionamiento VLSM

### Tabla de Direccionamiento VLSM Aplicada

| VLAN | Nombre         | Red/Subred        | Máscara         | Gateway        | Inicio DHCP    | Último host usable | Broadcast      | Máx. hosts |
| ---- | -------------- | ----------------- | --------------- | -------------- | -------------- | ------------------ | -------------- | ---------: |
| 10   | VLAN10         | 192.188.31.0/27   | 255.255.255.224 | 192.188.31.1   | 192.188.31.2   | 192.188.31.30      | 192.188.31.31  |         30 |
| 20   | VLAN20         | 192.188.31.32/27  | 255.255.255.224 | 192.188.31.33  | 192.188.31.34  | 192.188.31.62      | 192.188.31.63  |         30 |
| 30   | VLAN30         | 192.188.31.64/27  | 255.255.255.224 | 192.188.31.65  | 192.188.31.66  | 192.188.31.94      | 192.188.31.95  |         30 |
| 40   | VLAN40         | 192.188.31.96/27  | 255.255.255.224 | 192.188.31.97  | 192.188.31.98  | 192.188.31.126     | 192.188.31.127 |         30 |
| 99   | VLAN99 / ADMIN | 192.188.31.128/28 | 255.255.255.240 | 192.188.31.129 | 192.188.31.130 | 192.188.31.142     | 192.188.31.143 |         10 |

> **Nota:** En el diseño final se utilizó VLSM para optimizar el uso de direcciones IP. Las VLANs principales utilizan subredes /27 mientras que la VLAN de administración utiliza /28, ya que requiere menos hosts.

### Conceptos Aplicados

**FLSM (Fixed Length Subnet Mask):**  
Utiliza subredes con la misma máscara en toda la red. En este proyecto se aplicó FLSM para los enlaces de enrutamiento utilizando subredes /30.

**VLSM (Variable Length Subnet Mask):**  
Permite utilizar subredes con diferentes máscaras para optimizar el uso de direcciones IP. En este caso se aplicó VLSM para las VLANs, utilizando subredes /27 y /28 según la cantidad de hosts requeridos.

---

## 3.3 Red MAN - Enlaces Punto a Punto

### Direccionamiento de Enlaces con FLSM

| Enlace    | Red           | IP lado A  | IP lado B  | Broadcast  |
| --------- | ------------- | ---------- | ---------- | ---------- |
| MS1 — MS2 | 10.4.31.0/30  | 10.4.31.1  | 10.4.31.2  | 10.4.31.3  |
| MS2 — MS6 | 10.4.31.4/30  | 10.4.31.5  | 10.4.31.6  | 10.4.31.7  |
| MS6 — MS7 | 10.4.31.8/30  | 10.4.31.9  | 10.4.31.10 | 10.4.31.11 |
| MS7 — MS1 | 10.4.31.12/30 | 10.4.31.13 | 10.4.31.14 | 10.4.31.15 |
| MS7 — MS2 | 10.4.31.16/30 | 10.4.31.17 | 10.4.31.18 | 10.4.31.19 |

> **Nota:** La red 10.4.31.0/24 fue utilizada para los enlaces de enrutamiento entre switches capa 3. Esta red se segmentó utilizando FLSM en subredes /30, ya que cada enlace punto a punto requiere únicamente dos direcciones IP. Cada subred proporciona dos direcciones utilizables para los dispositivos conectados y permite una administración ordenada del direccionamiento.

---

# 4. Configuración de Servicios

## 4.1 Configuración de VLANs

### VLANs Creadas

Se configuraron VLANs para segmentar la red por edificios y departamentos.

| VLAN | Nombre            | Ubicación | Red               |
| ---- | ----------------- | --------- | ----------------- |
| 10   | Naranja Izquierdo | MS9       | 192.188.31.0/27   |
| 20   | Verde Izquierdo   | MS9       | 192.188.31.32/27  |
| 30   | Naranja Derecho   | MS3       | 192.188.31.64/27  |
| 40   | Verde Derecho     | MS3       | 192.188.31.96/27  |
| 99   | Administración    | MS6       | 192.188.31.128/28 |

### Comandos de Configuración

Ejemplo de creación de VLANs:

```cisco
vlan 10
name VLAN_Naranja_EdificioIZQ_202300431

vlan 20
name VLAN_Verde_EdificioIZQ_202300431

vlan 30
name VLAN_Naranja_EdificioDER_202300431

vlan 40
name VLAN_Verde_EdificioDER_202300431

vlan 99
name VLAN_ADMIN_202300431
```

---

## 4.2 Configuración de VTP

Se utilizó VTP (VLAN Trunking Protocol) para propagar las VLANs automáticamente entre switches.

### Configuración de Switch Servidor

```cisco
vtp mode server
vtp domain CHAPINRED
vtp password redes2
vtp version 2
```

### Configuración de Switch Cliente

```cisco
vtp mode client
vtp domain CHAPINRED
vtp password redes2
vtp version 2
```

---

## 4.3 Configuración de DHCP

Para la asignación de direcciones IP a los dispositivos finales se utilizaron dos servidores DHCP, uno para cada edificio. El servidor DHCP1 también asigna direcciones para la VLAN99 de administración. 

Se configuraron pools de direcciones para cada VLAN, permitiendo que las PCs y laptops reciban automáticamente su dirección IP, máscara de red, gateway y DNS.

### DHCP Edificio Izquierdo

El servidor DHCP izquierdo se encarga de asignar direcciones IP a las VLANs del edificio izquierdo y VLAN de administrador.

**Pools configurados:**

| VLAN                  | Gateway        | Inicio DHCP    | Máscara         |
| --------------------- | -------------- | -------------- | --------------- |
| VLAN 10 (Naranja Izq) | 192.188.31.1   | 192.188.31.2   | 255.255.255.224 |
| VLAN 20 (Verde Izq)   | 192.188.31.33  | 192.188.31.34  | 255.255.255.224 |
| VLAN 99 (ADMIN)       | 192.188.31.129 | 192.188.31.130 | 255.255.255.240 |

### DHCP Edificio Derecho

El servidor DHCP derecho asigna direcciones IP a las VLANs del edificio derecho.

**Pools configurados:**

| VLAN                  | Gateway       | Inicio DHCP   | Máscara         |
| --------------------- | ------------- | ------------- | --------------- |
| VLAN 30 (Naranja Der) | 192.188.31.65 | 192.188.31.66 | 255.255.255.224 |
| VLAN 40 (Verde Der)   | 192.188.31.97 | 192.188.31.98 | 255.255.255.224 |

### DHCP Relay

Para permitir que los dispositivos obtengan su dirección IP desde los servidores DHCP ubicados en otras redes, se utilizó DHCP Relay en los switches multicapa mediante el comando:

```cisco
ip helper-address <direccion_servidor_dhcp>
```

| Servidor | IP         | Default Gatawey | DNS SERVER | Subnet Mask
| -------- | ---------- | --------------- | ---------- | -------- |
| DHCP1    | 10.4.31.22 | 10.4.31.21      | 8.8.8.8    | 255.255.255.252 
| DHCP2    | 10.4.31.26 | 10.4.31.25      | 8.8.8.8    | 255.255.255.252

Este comando redirige las solicitudes DHCP broadcast hacia el servidor DHCP correspondiente, permitiendo que los dispositivos en diferentes VLANs y edificios obtengan configuración de red de manera centralizada

---

# 5. Enrutamiento Dinámico

Para permitir la comunicación entre los diferentes edificios y redes se implementó un protocolo de enrutamiento dinámico.

En este proyecto se utilizó **EIGRP (Enhanced Interior Gateway Routing Protocol)**, configurado en los switches multicapa encargados del enrutamiento entre redes.

## Configuración EIGRP

```cisco
router eigrp 100
 network 10.4.31.0 0.0.0.255
 network 192.188.31.0 0.0.0.255
 no auto-summary
```

> **Nota:** Packet Tracer puede reactivar `auto-summary` al reiniciar. Si esto ocurre, ejecutar nuevamente el comando `no auto-summary` dentro de la configuración del router EIGRP.

## Verificación

```cisco
show ip route
show ip eigrp neighbors
```

Con esta configuración, los dispositivos pueden intercambiar información de rutas automáticamente y mantener actualizadas las tablas de enrutamiento.

---

# 6. EtherChannel

Para mejorar el rendimiento y la redundancia de la red se implementó **EtherChannel**, el cual permite agrupar varios enlaces físicos en un solo enlace lógico.

Esto proporciona:

- Mayor ancho de banda
- Balanceo de carga
- Tolerancia a fallos

---

## 6.1 LACP - Edificio Izquierdo

En el edificio izquierdo se utilizó **LACP (Link Aggregation Control Protocol)** para configurar la agregación de enlaces entre switches.

Se configuraron **cinco enlaces EtherChannel**, los cuales permiten redundancia y balanceo de tráfico entre los dispositivos de la red.

### Tabla de EtherChannels LACP

| Port-Channel | Switch A | Puertos A | Switch B | Puertos B | Protocolo | VLANs Permitidas |
| ------------ | -------- | --------- | -------- | --------- | --------- | ---------------- |
| PO1          | MS7      | Gi1/0/1–3 | MS9      | Fa0/1–3   | LACP      | 10, 20           |
| PO2          | MS7      | Gi1/0/4–6 | MS8      | Fa0/4–6   | LACP      | 10, 20           |
| PO3          | MS9      | Fa0/7–9   | MS8      | Fa0/7–9   | LACP      | 10, 20           |
| PO4          | MS9      | Fa0/11–12 | SW1      | Fa0/11–12 | LACP      | 10, 20           |
| PO5          | MS8      | Fa0/11–12 | SW2      | Fa0/11–12 | LACP      | 10, 20           |

### Configuración LACP

```cisco
interface range gi1/0/1-3
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 channel-protocol lacp
 channel-group 1 mode active
```

Los enlaces fueron configurados en modo **active** permitiendo la negociación automática entre dispositivos.

---

## 6.2 PAgP - Edificio Derecho

En el edificio derecho se utilizó **PAgP (Port Aggregation Protocol)**, un protocolo propietario de Cisco para la agregación de enlaces.

Se configuraron **tres enlaces EtherChannel** utilizando el modo **desirable**, el cual permite establecer automáticamente la agregación con el dispositivo vecino.

### Tabla de EtherChannels PAgP

| Port-Channel | Switch A | Puertos A | Switch B | Puertos B | Protocolo | VLANs Permitidas |
| ------------ | -------- | --------- | -------- | --------- | --------- | ---------------- |
| PO1          | MS2      | Gi1/0/1–3 | MS3      | Fa0/1–3   | PAgP      | 30, 40           |
| PO2          | MS3      | Fa0/4–6   | MS4      | Fa0/4–6   | PAgP      | 30, 40           |
| PO3          | MS4      | Fa0/1–2   | MS5      | Fa0/2–3   | PAgP      | 30, 40           |

### Configuración PAgP

```cisco
interface range gi1/0/1-3
 switchport mode trunk
 switchport trunk allowed vlan 30,40
 channel-protocol pagp
 channel-group 1 mode desirable
```

Los enlaces fueron configurados en modo **desirable** para establecer automáticamente la agregación.

---

# 7. ACLs (Listas de Control de Acceso)

Se implementaron ACLs extendidas para controlar el tráfico entre VLANs según los siguientes requisitos:

- **VLAN Verde NO puede comunicarse con VLAN Naranja** (en ambos sentidos)
- **VLAN Naranja NO puede comunicarse con VLAN Verde** (en ambos sentidos)
- **VLANs de usuario NO pueden acceder a VLAN Admin (99)**
- **VLAN Admin (99) SÍ puede acceder a todas las demás VLANs**

### ACLs Configuradas en MS7 (Edificio Izquierdo)

```cisco
! Bloquear Verde (20) hacia Naranja (10)
ip access-list extended BLOCK_VERDE_TO_NARANJA
 deny ip 192.188.31.32 0.0.0.31 192.188.31.0 0.0.0.31
 deny ip 192.188.31.32 0.0.0.31 192.188.31.64 0.0.0.31
 permit ip any any

! Bloquear Naranja (10) hacia Verde (20)
ip access-list extended BLOCK_NARANJA_TO_VERDE
 deny ip 192.188.31.0 0.0.0.31 192.188.31.32 0.0.0.31
 deny ip 192.188.31.0 0.0.0.31 192.188.31.96 0.0.0.31
 deny ip 192.188.31.0 0.0.0.31 192.188.31.128 0.0.0.15
 permit ip any any

! Aplicar ACLs a interfaces VLAN
interface vlan 20
 ip access-group BLOCK_VERDE_TO_NARANJA in

interface vlan 10
 ip access-group BLOCK_NARANJA_TO_VERDE in
```

### ACLs Configuradas en MS2 (Edificio Derecho)

```cisco
! Bloquear Verde (40) hacia Naranja (30)
ip access-list extended BLOCK_VERDE_TO_NARANJA
 deny ip 192.188.31.96 0.0.0.31 192.188.31.0 0.0.0.31
 deny ip 192.188.31.96 0.0.0.31 192.188.31.64 0.0.0.31
 permit ip any any

! Bloquear Naranja (30) hacia Verde (40)
ip access-list extended BLOCK_NARANJA_TO_VERDE
 deny ip 192.188.31.64 0.0.0.31 192.188.31.32 0.0.0.31
 deny ip 192.188.31.64 0.0.0.31 192.188.31.96 0.0.0.31
 deny ip 192.188.31.64 0.0.0.31 192.188.31.128 0.0.0.15
 permit ip any any

! Aplicar ACLs a interfaces VLAN
interface vlan 40
 ip access-group BLOCK_VERDE_TO_NARANJA in

interface vlan 30
 ip access-group BLOCK_NARANJA_TO_VERDE in
```

---

# 8. Pruebas de Conectividad (Diseño)

Se realizaron pruebas exhaustivas para verificar el correcto funcionamiento de la red:

1. **Pruebas DHCP:** Verificación de asignación automática de IPs en todas las VLANs
2. **Pruebas de conectividad intra-VLAN:** Comunicación entre dispositivos de la misma VLAN
3. **Pruebas de conectividad inter-VLAN:** Comunicación entre VLANs permitidas
4. **Pruebas de ACLs:** Verificación de bloqueos entre VLANs según políticas
5. **Pruebas de tolerancia a fallos:** Verificación de redundancia en EtherChannel (LACP y PAgP)

---

# 9. Protocolos Implementados

### Resumen de Protocolos

| Protocolo | Función                                | Ubicación                      |
| --------- | -------------------------------------- | ------------------------------ |
| EIGRP     | Enrutamiento dinámico                  | MS1, MS2, MS6, MS7             |
| VTP       | Propagación automática de VLANs        | Todos los switches             |
| DHCP      | Asignación dinámica de direcciones IP  | Servidores DHCP1 y DHCP2       |
| LACP      | Agregación de enlaces (estándar)       | Edificio Izquierdo             |
| PAgP      | Agregación de enlaces (Cisco)          | Edificio Derecho               |
| STP       | Prevención de bucles en capa 2         | Todos los switches             |
| ACL       | Control de acceso entre VLANs          | MS7 (izquierdo) y MS2 (derecho |

---

# 10. Comandos de Verificación (Parte I)

### Verificación de VLANs

```cisco
show vlan brief
show vtp status
```

### Verificación de EtherChannel

```cisco
show etherchannel summary
show etherchannel port-channel
```

### Verificación de Enrutamiento

```cisco
show ip route
show ip eigrp neighbors
show ip eigrp topology
```

### Verificación de ACLs

```cisco
show access-lists
show ip interface <interface>
```

### Verificación de Interfaces

```cisco
show ip interface brief
show interfaces trunk
show spanning-tree
```

---

# 11. Conclusiones

El proyecto implementó una red MAN con dos edificios interconectados, utilizando tecnologías avanzadas de switching y routing:

- **Segmentación eficiente:** Las VLANs proporcionan segmentación lógica del tráfico
- **Alta disponibilidad:** EtherChannel (LACP y PAgP) garantiza redundancia y balanceo de carga
- **Enrutamiento dinámico:** EIGRP permite convergencia rápida y rutas óptimas
- **Seguridad:** ACLs controlan el tráfico entre VLANs según políticas establecidas
- **Escalabilidad:** VTP facilita la administración de VLANs en múltiples switches
- **Automatización:** DHCP simplifica la configuración de dispositivos finales

La topología demostró ser robusta, escalable y cumple con todos los requisitos de conectividad y seguridad establecidos.

---
---
---

# PARTE II: PRUEBAS Y EVIDENCIAS

---

# PARTE II: 1. Comprobación de DHCP

Esta sección verifica que todos los dispositivos finales reciben correctamente su dirección IP mediante DHCP.

## PARTE II: 1.1 Edificio Izquierdo

### PC1 (VLAN 10 - Naranja Izquierdo)

![alt text](img/image-2.png)

### Laptop1 (VLAN 20 - Verde Izquierdo)

![alt text](img/image-3.png)

## PARTE II: 1.2 Edificio Derecho

### PC3 (VLAN 30 - Naranja Derecho)

![alt text](img/image-5.png)

### Laptop3 (VLAN 40 - Verde Derecho)

![alt text](img/image-6.png)

## PARTE II: 1.3 Red Administrativa

### PC0 (VLAN 99 - Administrador)

![alt text](img/image-4.png)

---

# PARTE II: 2. Switches Multicapa Funcionales

Verificación de configuración y estado operativo de switches multicapa en cada edificio.

## PARTE II: 2.1 Edificio Izquierdo

### MS9 (Core)

**Configuración VTP y VLANs:**

![alt text](img/image-8.png)

**Port-Channels configurados:**

![alt text](img/image-14.png)

### MS7 (Borde)

**Interfaces VLAN y direccionamiento:**

![alt text](img/image-9.png)

**Configuración de rutas EIGRP:**

![alt text](img/image-10.png)

**Port-Channels LACP:**

![alt text](img/image-11.png)

### MS8 (Distribuidor)

**Configuración de Port-Channels:**

![alt text](img/image-12.png)

**Estado de interfaces trunk:**

![alt text](img/image-13.png)

## PARTE II: 2.2 Edificio Derecho

### MS2 (Borde)

**Interfaces VLAN y direccionamiento:**

![alt text](img/image-15.png)

**Configuración EIGRP:**

![alt text](img/image-16.png)

**ACLs aplicadas:**

![alt text](img/image-17.png)

**Vecinos EIGRP:**

![alt text](img/image-18.png)

### MS3 (Core)

**Configuración VTP Server:**

![alt text](img/image-19.png)

**Port-Channels PAgP:**

![alt text](img/image-20.png)

### MS4 (Distribuidor)

**Port-Channels configurados:**

![alt text](img/image-21.png)

**Estado de interfaces:**

![alt text](img/image-22.png)

### MS5 (Distribuidor)

**Configuración general:**

![alt text](img/image-23.png)

**Port-Channels y trunks:**

![alt text](img/image-24.png)

## PARTE II: 2.3 Red MAN

### MS6 (Backbone)

**Interfaces del backbone:**

![alt text](img/image-25.png)

**Configuración VLAN 99:**

![alt text](img/image-26.png)

**EIGRP y rutas:**

![alt text](img/image-27.png)

---

# PARTE II: 3. Pruebas de Conectividad

Se realizaron pruebas de conectividad entre dispositivos para verificar el correcto funcionamiento de VLANs y enrutamiento.

## PARTE II: 3.1 VLAN Naranja

### PC1 a Laptop0 (Mismo Edificio Izquierdo)

![alt text](img/image-28.png)

### Laptop0 a PC3 (Edificio Izquierdo -> Edificio Derecho)

![alt text](img/image-29.png)

### PC3 a PC4 (Mismo Edificio Derecho)

![alt text](img/image-30.png)

## PARTE II: 3.2 VLAN Verde

### PC2 a Laptop1 (Mismo Edificio Izquierdo)

![alt text](img/image-31.png)

### Laptop1 a Laptop2 (Izquierdo -> Derecho)

![alt text](img/image-32.png)

### Laptop2 a Laptop3 (Mismo Edificio Derecho)

![alt text](img/image-33.png)

## PARTE II: 3.3 VLAN Admin

### Admin hacia Edificio Izquierdo (Verde/Naranja)

![alt text](img/image-34.png)

### Admin hacia Edificio Derecho (Verde/Naranja)

![alt text](img/image-35.png)

---

# PARTE II: 4. Bloqueo ACL

Verificación de que las ACLs bloquean correctamente el tráfico no autorizado entre VLANs.

## PARTE II: 4.1 Verde hacia Naranja

### Verde Izquierdo -> Naranja (Izquierdo y Derecho)

![alt text](img/image-36.png)

### Verde Derecho -> Naranja (Derecho e Izquierdo)

![alt text](img/image-38.png)

## PARTE II: 4.2 Naranja hacia Verde

### Naranja Izquierdo -> Verde (Izquierdo y Derecho)

![alt text](img/image-37.png)

### Naranja Derecho -> Verde (Derecho e Izquierdo)

![alt text](img/image-39.png)

## PARTE II: 4.3 VLANs hacia Admin

### Naranja Izquierdo -> Admin (Bloqueado)

![alt text](img/image-40.png)

![alt text](img/image-42.png)

### Verde Derecho -> Admin (Bloqueado)

![alt text](img/image-41.png)

![alt text](img/image-43.png)

---

# PARTE II: 5. Tolerancia a Fallos

Se realizaron pruebas de tolerancia a fallos en los EtherChannels configurados con LACP y PAgP.

## PARTE II: 5.1 Test 1 - LACP (Edificio Izquierdo)

### Descripción

Se realizó una prueba de tolerancia a fallos en el EtherChannel configurado con LACP en el edificio izquierdo.

Inicialmente se verificó mediante el comando `show spanning-tree` que el Port-Channel 1 se encontraba en estado **Forwarding**, mientras que otro enlace redundante se encontraba bloqueado por Spanning Tree para evitar bucles de capa 2.

### Procedimiento

1. Se deshabilitó el Port-Channel activo para simular la caída del enlace principal
2. El protocolo Spanning Tree detectó la falla y desbloqueó el enlace alternativo
3. El tráfico continuó circulando por la ruta redundante

### Resultados

Durante la prueba se ejecutó un **ping continuo** entre dispositivos ubicados en diferentes edificios, observándose que la conectividad se mantuvo operativa después de la reconvergencia de la red.

**Evidencias:**

![alt text](img/image-49.png)

![alt text](img/image-44.png)

![alt text](img/image-50.png)

![alt text](img/image-51.png)

### Restablecimiento

Al restablecer los puertos se restableció la conexión correctamente:

![alt text](img/image-52.png)

### Conclusión

La prueba demostró la tolerancia a fallos y la redundancia implementada mediante EtherChannel y STP.

---

## PARTE II: 5.2 Test 2 - PAgP (Edificio Derecho)

### Descripción

Se realizó una prueba de tolerancia a fallos en el EtherChannel configurado mediante el protocolo **PAgP** en el edificio derecho.

Inicialmente se verificó el estado del Port-Channel utilizando el comando `show etherchannel summary`, observando que los enlaces físicos se encontraban correctamente agregados al canal lógico con estado **(P)**.

### Procedimiento

1. Se deshabilitó uno de los enlaces físicos pertenecientes al EtherChannel para simular la falla de un cable de red
2. Después de ejecutar el comando `shutdown` sobre el puerto, el estado del enlace cambió a **(D)** indicando que ya no formaba parte del canal
3. El tráfico continuó circulando a través de los enlaces restantes del Port-Channel

### Resultados

A pesar de la falla, el tráfico continuó circulando a través de los enlaces restantes sin interrupciones en la conectividad.

**Evidencias:**

![alt text](img/image-53.png)

![alt text](img/image-54.png)

![alt text](img/image-55.png)

![alt text](img/image-56.png)

### Conclusión

La prueba demuestra que el EtherChannel con PAgP proporciona redundancia y tolerancia a fallos en la red.

---

**Fin de las pruebas y evidencias**

---
---
---

# PARTE III: COMANDOS DE CONFIGURACIÓN

---

# PARTE III: 1. Red MAN - Backbone

## MS1

```cisco
enable
conf t
hostname MS1
ip routing

! Enlaces del backbone
interface g1/1/1
 no switchport
 ip address 10.4.31.1 255.255.255.252
 no shutdown

interface g1/1/2
 no switchport
 ip address 10.4.31.14 255.255.255.252
 no shutdown

! Enlaces a servidores DHCP
interface g1/0/1
 no switchport
 ip address 10.4.31.21 255.255.255.252
 no shutdown

interface g1/0/2
 no switchport
 ip address 10.4.31.25 255.255.255.252
 no shutdown

! Enrutamiento EIGRP
router eigrp 100
 no auto-summary
 network 10.4.31.0 0.0.0.255
end
wr
```

## MS2

```cisco
enable
conf t
hostname MS2
ip routing

interface g1/1/1
 no switchport
 ip address 10.4.31.2 255.255.255.252
 no shutdown

interface g1/1/2
 no switchport
 ip address 10.4.31.5 255.255.255.252
 no shutdown

interface g1/1/3
 no switchport
 ip address 10.4.31.18 255.255.255.252
 no shutdown

! SVIs para VLANs 30 y 40 (Gateways)
interface vlan 30
 ip address 192.188.31.65 255.255.255.224
 ip helper-address 10.4.31.26
 no shutdown

interface vlan 40
 ip address 192.188.31.97 255.255.255.224
 ip helper-address 10.4.31.26
 no shutdown

router eigrp 100
 no auto-summary
 network 10.4.31.0 0.0.0.255
 network 192.188.31.64 0.0.0.31
 network 192.188.31.96 0.0.0.31
end
wr
```

## MS6

```cisco
enable
conf t
hostname MS6

vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode server

vlan 99
 name VLAN_ADMIN_202300431

interface g1/1/1
 no switchport
 ip address 10.4.31.9 255.255.255.252
 no shutdown

interface g1/1/2
 no switchport
 ip address 10.4.31.6 255.255.255.252
 no shutdown

interface g1/0/1
 switchport mode access
 switchport access vlan 99
 no shutdown

! SVI para VLAN 99 (Gateway)
interface vlan 99
 ip address 192.188.31.129 255.255.255.240
 ip helper-address 10.4.31.22
 no shutdown

ip routing
router eigrp 100
 no auto-summary
 network 10.4.31.0 0.0.0.255
 network 192.188.31.128 0.0.0.15
end
wr
```

## MS7

```cisco
enable
conf t
hostname MS7

vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode client

interface g1/1/1
 no switchport
 ip address 10.4.31.10 255.255.255.252
 no shutdown

interface g1/1/2
 no switchport
 ip address 10.4.31.13 255.255.255.252
 no shutdown

interface g1/1/3
 no switchport
 ip address 10.4.31.17 255.255.255.252
 no shutdown

ip routing

interface vlan 10
 ip address 192.188.31.1 255.255.255.224
 ip helper-address 10.4.31.22
 no shutdown

interface vlan 20
 ip address 192.188.31.33 255.255.255.224
 ip helper-address 10.4.31.22
 no shutdown

router eigrp 100
 no auto-summary
 network 10.4.31.0 0.0.0.255
 network 192.188.31.0 0.0.0.31
 network 192.188.31.32 0.0.0.31
end
wr
```

---

# PARTE III: 2. Servidores DHCP

## Servidor DHCP1 (Edificio Izquierdo + Admin)

**Interfaz:**
- IP: 10.4.31.22
- Máscara: 255.255.255.252
- Gateway: 10.4.31.21

**Pools configurados:**

### Pool VLAN 10 (Naranja Izquierdo)
- Red: 192.188.31.0
- Máscara: 255.255.255.224
- Gateway: 192.188.31.1
- DNS: 192.188.31.1
- Inicio: 192.188.31.2

### Pool VLAN 20 (Verde Izquierdo)
- Red: 192.188.31.32
- Máscara: 255.255.255.224
- Gateway: 192.188.31.33
- DNS: 192.188.31.33
- Inicio: 192.188.31.34

### Pool VLAN 99 (Admin)
- Red: 192.188.31.128
- Máscara: 255.255.255.240
- Gateway: 192.188.31.129
- DNS: 192.188.31.129
- Inicio: 192.188.31.130

## Servidor DHCP2 (Edificio Derecho)

**Interfaz:**
- IP: 10.4.31.26
- Máscara: 255.255.255.252
- Gateway: 10.4.31.25

**Pools configurados:**

### Pool VLAN 30 (Naranja Derecho)
- Red: 192.188.31.64
- Máscara: 255.255.255.224
- Gateway: 192.188.31.65
- DNS: 192.188.31.65
- Inicio: 192.188.31.66

### Pool VLAN 40 (Verde Derecho)
- Red: 192.188.31.96
- Máscara: 255.255.255.224
- Gateway: 192.188.31.97
- DNS: 192.188.31.97
- Inicio: 192.188.31.98

---

# PARTE III: 3. Edificio Izquierdo - LACP

## MS9 (Core - VTP Server)

### Configuración VTP y VLANs

```cisco
enable
conf t
hostname MS9

vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode server

vlan 10
 name VLAN_Naranja_EdificioIZQ_202300431

vlan 20
 name VLAN_Verde_EdificioIZQ_202300431

end
wr
```

### Port-Channel 1: MS9 ↔ MS7

```cisco
enable
conf t
default interface fa0/1
default interface fa0/2
default interface fa0/3
no interface port-channel 1

interface port-channel 1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/1-3
 switchport trunk encapsulation dot1q
 channel-protocol lacp
 channel-group 1 mode active
 no shutdown
exit

interface port-channel 1
 switchport trunk allowed vlan 10,20
end
wr
```

### Port-Channel 3: MS9 ↔ MS8

```cisco
enable
conf t
default interface fa0/7
default interface fa0/8
default interface fa0/9
no interface port-channel 3

interface port-channel 3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/7-9
 switchport trunk encapsulation dot1q
 channel-protocol lacp
 channel-group 3 mode active
 no shutdown
exit

interface port-channel 3
 switchport trunk allowed vlan 10,20
end
wr
```

### Port-Channel 4: MS9 ↔ SW1

```cisco
enable
conf t
default interface fa0/11
default interface fa0/12
no interface port-channel 4

interface port-channel 4
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/11-12
 switchport trunk encapsulation dot1q
 channel-protocol lacp
 channel-group 4 mode active
 no shutdown
exit

interface port-channel 4
 switchport trunk allowed vlan 10,20
end
wr
```

---

## MS8 (Distribuidor)

### Configuración VTP

```cisco
enable
conf t
hostname MS8

vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode client
end
wr
```

### Port-Channel 2: MS8 ↔ MS7

```cisco
enable
conf t
default interface fa0/4
default interface fa0/5
default interface fa0/6
no interface port-channel 2

interface port-channel 2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/4-6
 switchport trunk encapsulation dot1q
 channel-protocol lacp
 channel-group 2 mode active
 no shutdown
exit

interface port-channel 2
 switchport trunk allowed vlan 10,20
end
wr
```

### Port-Channel 3: MS8 ↔ MS9

```cisco
enable
conf t
default interface fa0/7
default interface fa0/8
default interface fa0/9
no interface port-channel 3

interface port-channel 3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/7-9
 switchport trunk encapsulation dot1q
 channel-protocol lacp
 channel-group 3 mode active
 no shutdown
exit

interface port-channel 3
 switchport trunk allowed vlan 10,20
end
wr
```

### Port-Channel 5: MS8 ↔ SW2

```cisco
enable
conf t
default interface fa0/11
default interface fa0/12
no interface port-channel 5

interface port-channel 5
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/11-12
 switchport trunk encapsulation dot1q
 channel-protocol lacp
 channel-group 5 mode active
 no shutdown
exit

interface port-channel 5
 switchport trunk allowed vlan 10,20
end
wr
```

---

## MS7 (Borde) - Port-Channels

### Port-Channel 1: MS7 ↔ MS9

```cisco
enable
conf t
default interface gi1/0/1
default interface gi1/0/2
default interface gi1/0/3
no interface port-channel 1

interface port-channel 1
 switchport mode trunk
 no shutdown
exit

interface range gi1/0/1-3
 channel-protocol lacp
 channel-group 1 mode active
 no shutdown
exit

interface port-channel 1
 switchport trunk allowed vlan 10,20
end
wr
```

### Port-Channel 2: MS7 ↔ MS8

```cisco
enable
conf t
default interface gi1/0/4
default interface gi1/0/5
default interface gi1/0/6
no interface port-channel 2

interface port-channel 2
 switchport mode trunk
 no shutdown
exit

interface range gi1/0/4-6
 channel-protocol lacp
 channel-group 2 mode active
 no shutdown
exit

interface port-channel 2
 switchport trunk allowed vlan 10,20
end
wr
```

---

## SW1 (Acceso)

### Configuración VTP

```cisco
enable
conf t
hostname SW1

vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode client
end
wr
```

### Port-Channel 4: SW1 ↔ MS9

```cisco
enable
conf t
default interface fa0/11
default interface fa0/12
no interface port-channel 4

interface port-channel 4
 switchport mode trunk
 no shutdown
exit

interface range fa0/11-12
 channel-protocol lacp
 channel-group 4 mode active
 no shutdown
exit

interface port-channel 4
 switchport trunk allowed vlan 10,20
end
wr
```

### Puertos de Acceso

```cisco
enable
conf t

interface fa0/1
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 no shutdown

interface fa0/2
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 no shutdown

end
wr
```

---

## SW2 (Acceso)

### Configuración VTP

```cisco
enable
conf t
hostname SW2

vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode client
end
wr
```

### Port-Channel 5: SW2 ↔ MS8

```cisco
enable
conf t
default interface fa0/11
default interface fa0/12
no interface port-channel 5

interface port-channel 5
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/11-12
 switchport trunk encapsulation dot1q
 channel-protocol lacp
 channel-group 5 mode active
 no shutdown
exit

interface port-channel 5
 switchport trunk allowed vlan 10,20
end
wr
```

### Puertos de Acceso

```cisco
enable
conf t

interface fa0/1
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 no shutdown

interface fa0/2
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 no shutdown

end
wr
```

---

# PARTE III: 4. Edificio Derecho - PAgP

## MS3 (Core - VTP Server)

### Configuración VTP y VLANs

```cisco
enable
conf t
hostname MS3

vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode server

vlan 30
 name VLAN_Naranja_EdificioDER_202300431

vlan 40
 name VLAN_Verde_EdificioDER_202300431

end
wr
```

### Port-Channel 1: MS3 ↔ MS2

```cisco
enable
conf t

default interface fa0/1
default interface fa0/2
default interface fa0/3
no interface port-channel 1

interface port-channel 1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/1-3
 switchport trunk encapsulation dot1q
 channel-protocol pagp
 channel-group 1 mode desirable
 no shutdown
exit

interface port-channel 1
 switchport trunk allowed vlan 30,40
end
wr
```

### Port-Channel 2: MS3 ↔ MS4

```cisco
enable
conf t

default interface fa0/4
default interface fa0/5
default interface fa0/6
no interface port-channel 2

interface port-channel 2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/4-6
 switchport trunk encapsulation dot1q
 channel-protocol pagp
 channel-group 2 mode desirable
 no shutdown
exit

interface port-channel 2
 switchport trunk allowed vlan 30,40
end
wr
```

---

## MS2 (Borde) - Port-Channel

### Port-Channel 1: MS2 ↔ MS3

```cisco
enable
conf t

default interface gi1/0/1
default interface gi1/0/2
default interface gi1/0/3
no interface port-channel 1

interface port-channel 1
 switchport mode trunk
 no shutdown
exit

interface range gi1/0/1-3
 channel-protocol pagp
 channel-group 1 mode desirable
 no shutdown
exit

interface port-channel 1
 switchport trunk allowed vlan 30,40
end
wr
```

---

## MS4 (Distribuidor)

### Configuración VTP

```cisco
enable
conf t
hostname MS4

vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode client
end
wr
```

### Port-Channel 2: MS4 ↔ MS3

```cisco
enable
conf t

default interface fa0/4
default interface fa0/5
default interface fa0/6
no interface port-channel 2

interface port-channel 2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/4-6
 switchport trunk encapsulation dot1q
 channel-protocol pagp
 channel-group 2 mode desirable
 no shutdown
exit

interface port-channel 2
 switchport trunk allowed vlan 30,40
end
wr
```

---

## MS5 (Distribuidor)

### Configuración VTP

```cisco
enable
conf t
hostname MS5

vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode client
end
wr
```

### Configuración de Trunks a Switches de Acceso

```cisco
enable
conf t

interface fa0/21
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 30,40
 no shutdown

interface fa0/22
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 30,40
 no shutdown

end
wr
```

---

## SW3 (Acceso)

### Configuración VTP

```cisco
enable
conf t
hostname SW3

vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode client
end
wr
```

### Configuración Trunk e Interfaces de Acceso

```cisco
enable
conf t

interface fa0/21
 switchport mode trunk
 switchport trunk allowed vlan 30,40
 no shutdown

interface fa0/11
 switchport mode access
 switchport access vlan 40
 spanning-tree portfast
 no shutdown

interface fa0/12
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
 no shutdown

end
wr
```

---

## SW4 (Acceso)

### Configuración VTP

```cisco
enable
conf t
hostname SW4

vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode client
end
wr
```

### Configuración Trunk e Interfaces de Acceso

```cisco
enable
conf t

interface fa0/22
 switchport mode trunk
 switchport trunk allowed vlan 30,40
 no shutdown

interface fa0/11
 switchport mode access
 switchport access vlan 40
 spanning-tree portfast
 no shutdown

interface fa0/12
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
 no shutdown

end
wr
```

---

# PARTE III: 5. ACLs

## MS7 (Edificio Izquierdo)

```cisco
enable
conf t

! ACL para bloquear Verde (VLAN 20) hacia Naranja (VLANs 10 y 30)
ip access-list extended BLOCK_VERDE_TO_NARANJA
 deny ip 192.188.31.32 0.0.0.31 192.188.31.0 0.0.0.31
 deny ip 192.188.31.32 0.0.0.31 192.188.31.64 0.0.0.31
 permit ip any any

! ACL para bloquear Naranja (VLAN 10) hacia Verde y Admin
ip access-list extended BLOCK_NARANJA_TO_VERDE
 deny ip 192.188.31.0 0.0.0.31 192.188.31.32 0.0.0.31
 deny ip 192.188.31.0 0.0.0.31 192.188.31.96 0.0.0.31
 deny ip 192.188.31.0 0.0.0.31 192.188.31.128 0.0.0.15
 permit ip any any

! Aplicar ACLs a interfaces VLAN
interface vlan 20
 ip access-group BLOCK_VERDE_TO_NARANJA in

interface vlan 10
 ip access-group BLOCK_NARANJA_TO_VERDE in

end
wr
```

---

## MS2 (Edificio Derecho)

```cisco
enable
conf t

! ACL para bloquear Verde (VLAN 40) hacia Naranja (VLANs 10 y 30)
ip access-list extended BLOCK_VERDE_TO_NARANJA
 deny ip 192.188.31.96 0.0.0.31 192.188.31.0 0.0.0.31
 deny ip 192.188.31.96 0.0.0.31 192.188.31.64 0.0.0.31
 permit ip any any

! ACL para bloquear Naranja (VLAN 30) hacia Verde y Admin
ip access-list extended BLOCK_NARANJA_TO_VERDE
 deny ip 192.188.31.64 0.0.0.31 192.188.31.32 0.0.0.31
 deny ip 192.188.31.64 0.0.0.31 192.188.31.96 0.0.0.31
 deny ip 192.188.31.64 0.0.0.31 192.188.31.128 0.0.0.15
 permit ip any any

! Aplicar ACLs a interfaces VLAN
interface vlan 40
 ip access-group BLOCK_VERDE_TO_NARANJA in

interface vlan 30
 ip access-group BLOCK_NARANJA_TO_VERDE in

end
wr
```

---

# PARTE III: 6. Comandos de Verificación

## Verificación de VLANs

```cisco
show vlan brief
show vlan id <vlan-id>
show vtp status
show vtp password
```

## Verificación de Interfaces

```cisco
show ip interface brief
show interfaces status
show interfaces trunk
show interfaces <interface>
```

## Verificación de EtherChannel

```cisco
show etherchannel summary
show etherchannel port-channel
show etherchannel load-balance
show interfaces port-channel <number>
show lacp neighbor
show pagp neighbor
```

## Verificación de Spanning Tree

```cisco
show spanning-tree
show spanning-tree vlan <vlan-id>
show spanning-tree interface <interface>
show spanning-tree summary
```

## Verificación de Enrutamiento

```cisco
show ip route
show ip route eigrp
show ip eigrp neighbors
show ip eigrp topology
show ip eigrp interfaces
show ip protocols
```

## Verificación de ACLs

```cisco
show access-lists
show ip access-lists
show ip interface <interface>
show running-config | include access-list
```

## Verificación de DHCP Relay

```cisco
show ip interface <interface>
show running-config interface <interface>
```

## Comandos de Diagnóstico

```cisco
ping <ip-address>
traceroute <ip-address>
show arp
show mac address-table
show cdp neighbors
show cdp neighbors detail
```

---

# PARTE III: 7. Configuraciones Completas por Dispositivo

Esta sección consolida todas las configuraciones por switch para facilitar la implementación completa de cada dispositivo.

---

## 7.1 Configuración Completa: MS1

```cisco
enable
conf t
hostname MS1
ip routing

! Enlaces del backbone
interface g1/1/1
 no switchport
 ip address 10.4.31.1 255.255.255.252
 no shutdown

interface g1/1/2
 no switchport
 ip address 10.4.31.14 255.255.255.252
 no shutdown

! Enlaces a servidores DHCP
interface g1/0/1
 no switchport
 ip address 10.4.31.21 255.255.255.252
 no shutdown

interface g1/0/2
 no switchport
 ip address 10.4.31.25 255.255.255.252
 no shutdown

! Enrutamiento EIGRP
router eigrp 100
 no auto-summary
 network 10.4.31.0 0.0.0.255
end
wr
```

---

## 7.2 Configuración Completa: MS2

```cisco
enable
conf t
hostname MS2
ip routing

! VTP Configuration
vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode client

! Enlaces MAN
interface g1/1/1
 no switchport
 ip address 10.4.31.2 255.255.255.252
 no shutdown

interface g1/1/2
 no switchport
 ip address 10.4.31.5 255.255.255.252
 no shutdown

interface g1/1/3
 no switchport
 ip address 10.4.31.18 255.255.255.252
 no shutdown

! SVIs para VLANs 30 y 40 (Gateways)
interface vlan 30
 ip address 192.188.31.65 255.255.255.224
 ip helper-address 10.4.31.26
 no shutdown

interface vlan 40
 ip address 192.188.31.97 255.255.255.224
 ip helper-address 10.4.31.26
 no shutdown

! Port-Channel 1 hacia MS3
default interface gi1/0/1
default interface gi1/0/2
default interface gi1/0/3
no interface port-channel 1

interface port-channel 1
 switchport mode trunk
 no shutdown
exit

interface range gi1/0/1-3
 channel-protocol pagp
 channel-group 1 mode desirable
 no shutdown
exit

interface port-channel 1
 switchport trunk allowed vlan 30,40
exit

! ACLs
ip access-list extended BLOCK_VERDE_TO_NARANJA
 deny ip 192.188.31.96 0.0.0.31 192.188.31.0 0.0.0.31
 deny ip 192.188.31.96 0.0.0.31 192.188.31.64 0.0.0.31
 permit ip any any

ip access-list extended BLOCK_NARANJA_TO_VERDE
 deny ip 192.188.31.64 0.0.0.31 192.188.31.32 0.0.0.31
 deny ip 192.188.31.64 0.0.0.31 192.188.31.96 0.0.0.31
 deny ip 192.188.31.64 0.0.0.31 192.188.31.128 0.0.0.15
 permit ip any any

interface vlan 40
 ip access-group BLOCK_VERDE_TO_NARANJA in

interface vlan 30
 ip access-group BLOCK_NARANJA_TO_VERDE in

! EIGRP
router eigrp 100
 no auto-summary
 network 10.4.31.0 0.0.0.255
 network 192.188.31.64 0.0.0.31
 network 192.188.31.96 0.0.0.31
end
wr
```

---

## 7.3 Configuración Completa: MS6

```cisco
enable
conf t
hostname MS6

! VTP Server para VLAN 99
vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode server

vlan 99
 name VLAN_ADMIN_202300431

! Enlaces MAN
interface g1/1/1
 no switchport
 ip address 10.4.31.9 255.255.255.252
 no shutdown

interface g1/1/2
 no switchport
 ip address 10.4.31.6 255.255.255.252
 no shutdown

! Puerto de acceso para PC Admin
interface g1/0/1
 switchport mode access
 switchport access vlan 99
 no shutdown

! SVI para VLAN 99 (Gateway)
interface vlan 99
 ip address 192.188.31.129 255.255.255.240
 ip helper-address 10.4.31.22
 no shutdown

! Enrutamiento
ip routing
router eigrp 100
 no auto-summary
 network 10.4.31.0 0.0.0.255
 network 192.188.31.128 0.0.0.15
end
wr
```

---

## 7.4 Configuración Completa: MS7

```cisco
enable
conf t
hostname MS7

! VTP Client
vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode client

! Enlaces MAN
interface g1/1/1
 no switchport
 ip address 10.4.31.10 255.255.255.252
 no shutdown

interface g1/1/2
 no switchport
 ip address 10.4.31.13 255.255.255.252
 no shutdown

interface g1/1/3
 no switchport
 ip address 10.4.31.17 255.255.255.252
 no shutdown

! Port-Channel 1 hacia MS9
default interface gi1/0/1
default interface gi1/0/2
default interface gi1/0/3
no interface port-channel 1

interface port-channel 1
 switchport mode trunk
 no shutdown
exit

interface range gi1/0/1-3
 channel-protocol lacp
 channel-group 1 mode active
 no shutdown
exit

interface port-channel 1
 switchport trunk allowed vlan 10,20
exit

! Port-Channel 2 hacia MS8
default interface gi1/0/4
default interface gi1/0/5
default interface gi1/0/6
no interface port-channel 2

interface port-channel 2
 switchport mode trunk
 no shutdown
exit

interface range gi1/0/4-6
 channel-protocol lacp
 channel-group 2 mode active
 no shutdown
exit

interface port-channel 2
 switchport trunk allowed vlan 10,20
exit

! SVIs (Gateways para VLANs 10 y 20)
ip routing

interface vlan 10
 ip address 192.188.31.1 255.255.255.224
 ip helper-address 10.4.31.22
 no shutdown

interface vlan 20
 ip address 192.188.31.33 255.255.255.224
 ip helper-address 10.4.31.22
 no shutdown

! ACLs
ip access-list extended BLOCK_VERDE_TO_NARANJA
 deny ip 192.188.31.32 0.0.0.31 192.188.31.0 0.0.0.31
 deny ip 192.188.31.32 0.0.0.31 192.188.31.64 0.0.0.31
 permit ip any any

ip access-list extended BLOCK_NARANJA_TO_VERDE
 deny ip 192.188.31.0 0.0.0.31 192.188.31.32 0.0.0.31
 deny ip 192.188.31.0 0.0.0.31 192.188.31.96 0.0.0.31
 deny ip 192.188.31.0 0.0.0.31 192.188.31.128 0.0.0.15
 permit ip any any

interface vlan 20
 ip access-group BLOCK_VERDE_TO_NARANJA in

interface vlan 10
 ip access-group BLOCK_NARANJA_TO_VERDE in

! EIGRP
router eigrp 100
 no auto-summary
 network 10.4.31.0 0.0.0.255
 network 192.188.31.0 0.0.0.31
 network 192.188.31.32 0.0.0.31
end
wr
```

---

## 7.5 Configuración Completa: MS8

```cisco
enable
conf t
hostname MS8

! VTP Client
vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode client

! Port-Channel 2 hacia MS7
default interface fa0/4
default interface fa0/5
default interface fa0/6
no interface port-channel 2

interface port-channel 2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/4-6
 switchport trunk encapsulation dot1q
 channel-protocol lacp
 channel-group 2 mode active
 no shutdown
exit

interface port-channel 2
 switchport trunk allowed vlan 10,20
exit

! Port-Channel 3 hacia MS9
default interface fa0/7
default interface fa0/8
default interface fa0/9
no interface port-channel 3

interface port-channel 3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/7-9
 switchport trunk encapsulation dot1q
 channel-protocol lacp
 channel-group 3 mode active
 no shutdown
exit

interface port-channel 3
 switchport trunk allowed vlan 10,20
exit

! Port-Channel 5 hacia SW2
default interface fa0/11
default interface fa0/12
no interface port-channel 5

interface port-channel 5
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/11-12
 switchport trunk encapsulation dot1q
 channel-protocol lacp
 channel-group 5 mode active
 no shutdown
exit

interface port-channel 5
 switchport trunk allowed vlan 10,20
end
wr
```

---

## 7.6 Configuración Completa: MS9

```cisco
enable
conf t
hostname MS9

! VTP Server para VLANs 10 y 20
vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode server

vlan 10
 name VLAN_Naranja_EdificioIZQ_202300431
vlan 20
 name VLAN_Verde_EdificioIZQ_202300431

! Port-Channel 1 hacia MS7
default interface fa0/1
default interface fa0/2
default interface fa0/3
no interface port-channel 1

interface port-channel 1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/1-3
 switchport trunk encapsulation dot1q
 channel-protocol lacp
 channel-group 1 mode active
 no shutdown
exit

interface port-channel 1
 switchport trunk allowed vlan 10,20
exit

! Port-Channel 3 hacia MS8
default interface fa0/7
default interface fa0/8
default interface fa0/9
no interface port-channel 3

interface port-channel 3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/7-9
 switchport trunk encapsulation dot1q
 channel-protocol lacp
 channel-group 3 mode active
 no shutdown
exit

interface port-channel 3
 switchport trunk allowed vlan 10,20
exit

! Port-Channel 4 hacia SW1
default interface fa0/11
default interface fa0/12
no interface port-channel 4

interface port-channel 4
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/11-12
 switchport trunk encapsulation dot1q
 channel-protocol lacp
 channel-group 4 mode active
 no shutdown
exit

interface port-channel 4
 switchport trunk allowed vlan 10,20
end
wr
```

---

## 7.7 Configuración Completa: MS3

```cisco
enable
conf t
hostname MS3

! VTP Server para VLANs 30 y 40
vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode server

vlan 30
 name VLAN_Naranja_EdificioDER_202300431

vlan 40
 name VLAN_Verde_EdificioDER_202300431

! Port-Channel 1 hacia MS2
default interface fa0/1
default interface fa0/2
default interface fa0/3
no interface port-channel 1

interface port-channel 1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/1-3
 switchport trunk encapsulation dot1q
 channel-protocol pagp
 channel-group 1 mode desirable
 no shutdown
exit

interface port-channel 1
 switchport trunk allowed vlan 30,40
exit

! Port-Channel 2 hacia MS4
default interface fa0/4
default interface fa0/5
default interface fa0/6
no interface port-channel 2

interface port-channel 2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/4-6
 switchport trunk encapsulation dot1q
 channel-protocol pagp
 channel-group 2 mode desirable
 no shutdown
exit

interface port-channel 2
 switchport trunk allowed vlan 30,40
end
wr
```

---

## 7.8 Configuración Completa: MS4

```cisco
enable
conf t
hostname MS4

! VTP Client
vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode client

! Port-Channel 2 hacia MS3
default interface fa0/4
default interface fa0/5
default interface fa0/6
no interface port-channel 2

interface port-channel 2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/4-6
 switchport trunk encapsulation dot1q
 channel-protocol pagp
 channel-group 2 mode desirable
 no shutdown
exit

interface port-channel 2
 switchport trunk allowed vlan 30,40
end
wr
```

---

## 7.9 Configuración Completa: MS5

```cisco
enable
conf t
hostname MS5

! VTP Client
vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode client

! Trunks hacia switches de acceso
interface fa0/21
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 30,40
 no shutdown

interface fa0/22
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 30,40
 no shutdown

end
wr
```

---

## 7.10 Configuración Completa: SW1

```cisco
enable
conf t
hostname SW1

! VTP Client
vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode client

! Port-Channel 4 hacia MS9
default interface fa0/11
default interface fa0/12
no interface port-channel 4

interface port-channel 4
 switchport mode trunk
 no shutdown
exit

interface range fa0/11-12
 channel-protocol lacp
 channel-group 4 mode active
 no shutdown
exit

interface port-channel 4
 switchport trunk allowed vlan 10,20
exit

! Puertos de acceso
interface fa0/1
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 no shutdown

interface fa0/2
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 no shutdown

end
wr
```

---

## 7.11 Configuración Completa: SW2

```cisco
enable
conf t
hostname SW2

! VTP Client
vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode client

! Port-Channel 5 hacia MS8
default interface fa0/11
default interface fa0/12
no interface port-channel 5

interface port-channel 5
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

interface range fa0/11-12
 switchport trunk encapsulation dot1q
 channel-protocol lacp
 channel-group 5 mode active
 no shutdown
exit

interface port-channel 5
 switchport trunk allowed vlan 10,20
exit

! Puertos de acceso
interface fa0/1
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 no shutdown

interface fa0/2
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 no shutdown

end
wr
```

---

## 7.12 Configuración Completa: SW3

```cisco
enable
conf t
hostname SW3

! VTP Client
vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode client

! Trunk hacia MS5
interface fa0/21
 switchport mode trunk
 switchport trunk allowed vlan 30,40
 no shutdown

! Puertos de acceso
interface fa0/11
 switchport mode access
 switchport access vlan 40
 spanning-tree portfast
 no shutdown

interface fa0/12
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
 no shutdown

end
wr
```

---

## 7.13 Configuración Completa: SW4

```cisco
enable
conf t
hostname SW4

! VTP Client
vtp domain CHAPINRED
vtp password redes2
vtp version 2
vtp mode client

! Trunk hacia MS5
interface fa0/22
 switchport mode trunk
 switchport trunk allowed vlan 30,40
 no shutdown

! Puertos de acceso
interface fa0/11
 switchport mode access
 switchport access vlan 40
 spanning-tree portfast
 no shutdown

interface fa0/12
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
 no shutdown

end
wr
```
