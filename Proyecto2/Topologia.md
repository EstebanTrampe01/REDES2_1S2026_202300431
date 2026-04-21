# TOPOLOGIAS


## CENTRO

ICP1 g1/1/1 - g1/1/2 ICP2
ICP2 g1/1/1 - g1/1/2 ICP3
ICP3 g1/1/1 - g1/1/2 ICP1

| Equipo frontera | ISP asociado | AS | IGP interno |
| --------------- | ------------ | -- | ----------- |
| ICP1            | ISP1         | 100 | OSPF |
| ICP2            | ISP2         | 200 | OSPF |
| ICP3            | ISP3         | 300 | EIGRP |

## ISP1 

| Origen      | Puerto  | Destino      | Puerto | Nota                               |
| ----------- | ------- | ------------ | ------ | ---------------------------------- |
| ICP1        | Gi1/0/1 | R1-TU        | Gi0/0  | Enlace de ISP1 hacia interconexion |
| R1-TU       | Gi0/1   | R2-TU        | Gi0/0  | Rama Administracion                |
| R1-TU       | Gi0/2   | R3-TU        | Gi0/0  | Rama Atencion al Cliente           |
| R2-TU       | Gi0/1   | R4-TU        | Gi0/0  | Subrama ADM                        |
| R3-TU       | Gi0/1   | R5-TU        | Gi0/0  | Subrama ATC                        |
| R4-TU       | Gi0/1   | SW-ADM-DIST  | Fa0/24 | Subida a acceso ADM                |
| R4-TU       | Gi0/2   | SRV-DNS-HTTP | Fa0    | Red de servidores                  |
| SW-ADM-DIST | Gi0/1   | SW-ADM-ACC   | Gi0/1  | LACP Port-Channel 1 enlace A       |
| SW-ADM-DIST | Gi0/2   | SW-ADM-ACC   | Gi0/2  | LACP Port-Channel 1 enlace B       |
| SW-ADM-ACC  | Fa0/1   | PC-ADM-1     | Fa0    | Host                               |
| SW-ADM-ACC  | Fa0/2   | PC-ADM-2     | Fa0    | Host                               |
| SW-ADM-ACC  | Fa0/3   | PC-ADM-3     | Fa0    | Host                               |
| R5-TU       | Gi0/1   | SW-ATC-DIST  | Fa0/24 | Subida a acceso ATC                |
| SW-ATC-DIST | Gi0/1   | SW-ATC-ACC   | Gi0/1  | LACP Port-Channel 2 enlace A       |
| SW-ATC-DIST | Gi0/2   | SW-ATC-ACC   | Gi0/2  | LACP Port-Channel 2 enlace B       |
| SW-ATC-ACC  | Fa0/1   | PC-ATC-1     | Fa0    | Host                               |
| SW-ATC-ACC  | Fa0/2   | PC-ATC-2     | Fa0    | Host                               |
| SW-ATC-ACC  | Fa0/3   | PC-ATC-3     | Fa0    | Host                               |


## ISP2

| Origen         | Puerto  | Destino        | Puerto | Nota                               |
| -------------- | ------- | -------------- | ------ | ---------------------------------- |
| ICP2           | Gi1/0/1 | R1-RN          | Gi0/0  | Salida de ISP2 hacia interconexion |
| R1-RN          | Gi0/1   | R2-RN          | Gi0/0  | Core a distribucion A              |
| R1-RN          | Gi0/2   | R3-RN          | Gi0/0  | Core a distribucion B              |
| R2-RN          | Gi0/1   | R4-RN          | Gi0/0  | Distribucion A a gateway A         |
| R3-RN          | Gi0/1   | R5-RN          | Gi0/0  | Distribucion B a gateway B         |
| R4-RN          | Gi0/1   | SW-VENTAS-DIST | Fa0/23 | Gateway A hacia VLAN Ventas        |
| R5-RN          | Gi0/1   | SW-VENTAS-DIST | Fa0/24 | Gateway B hacia VLAN Ventas        |
| R4-RN          | Gi0/2   | SW-FACT-DIST   | Fa0/23 | Gateway A hacia VLAN Facturacion   |
| R5-RN          | Gi0/2   | SW-FACT-DIST   | Fa0/24 | Gateway B hacia VLAN Facturacion   |
| SW-VENTAS-DIST | Gi0/1   | SW-VENTAS-ACC  | Gi0/1  | LACP Port-Channel 1 enlace A       |
| SW-VENTAS-DIST | Gi0/2   | SW-VENTAS-ACC  | Gi0/2  | LACP Port-Channel 1 enlace B       |
| SW-FACT-DIST   | Gi0/1   | SW-FACT-ACC    | Gi0/1  | LACP Port-Channel 2 enlace A       |
| SW-FACT-DIST   | Gi0/2   | SW-FACT-ACC    | Gi0/2  | LACP Port-Channel 2 enlace B       |
| SW-VENTAS-DIST | Fa0/22  | SRV-DHCP       | Fa0    | Servidor DHCP                      |
| SW-VENTAS-ACC  | Fa0/1   | PC-VENTAS-1    | Fa0    | Host                               |
| SW-VENTAS-ACC  | Fa0/2   | PC-VENTAS-2    | Fa0    | Host                               |
| SW-VENTAS-ACC  | Fa0/3   | PC-VENTAS-3    | Fa0    | Host                               |
| SW-FACT-ACC    | Fa0/1   | PC-FACT-1      | Fa0    | Host                               |
| SW-FACT-ACC    | Fa0/2   | PC-FACT-2      | Fa0    | Host                               |
| SW-FACT-ACC    | Fa0/3   | PC-FACT-3      | Fa0    | Host                               |



## ISP3

| Origen      | Puerto    | Destino     | Puerto   | Nota                               |
| ----------- | --------- | ----------- | -------- | ---------------------------------- |
| ICP3        | Gi1/0/1   | R1-CF       | Gi0/0    | Salida de ISP3 hacia interconexion |
| R1-CF       | Gi0/1     | SW-HUB-CF   | Fa0/24   | Enlace del borde al hub central    |
| R2-CF       | Gi0/0     | SW-HUB-CF   | Fa0/1    | Spoke Soporte                      |
| R3-CF       | Gi0/0     | SW-HUB-CF   | Fa0/2    | Spoke Seguridad                    |
| R4-CF       | Gi0/0     | SW-HUB-CF   | Fa0/3    | Spoke Wifi                         |
| R5-CF       | Gi0/0     | SW-HUB-CF   | Fa0/4    | Spoke Expansion                    |
| R2-CF       | Gi0/1     | SW-SOP-DIST | Fa0/24   | Enlace a red Soporte               |
| SW-SOP-DIST | Gi0/1     | SW-SOP-ACC  | Gi0/1    | LACP Port-Channel 1 enlace A       |
| SW-SOP-DIST | Gi0/2     | SW-SOP-ACC  | Gi0/2    | LACP Port-Channel 1 enlace B       |
| SW-SOP-ACC  | Fa0/1     | PC-SOP-1    | Fa0      | Host                               |
| SW-SOP-ACC  | Fa0/2     | PC-SOP-2    | Fa0      | Host                               |
| SW-SOP-ACC  | Fa0/3     | PC-SOP-3    | Fa0      | Host                               |
| R3-CF       | Gi0/1     | SW-SEG-DIST | Fa0/24   | Enlace a red Seguridad             |
| SW-SEG-DIST | Gi0/1     | SW-SEG-ACC  | Gi0/1    | LACP Port-Channel 2 enlace A       |
| SW-SEG-DIST | Gi0/2     | SW-SEG-ACC  | Gi0/2    | LACP Port-Channel 2 enlace B       |
| SW-SEG-ACC  | Fa0/1     | PC-SEG-1    | Fa0      | Host                               |
| SW-SEG-ACC  | Fa0/2     | PC-SEG-2    | Fa0      | Host                               |
| SW-SEG-ACC  | Fa0/3     | PC-SEG-3    | Fa0      | Host                               |
| R4-CF       | Gi0/1     | WRT300N     | Internet | Router inalámbrico                 |
| Laptop Wifi | Wireless0 | WRT300N     | Wireless | Cliente WiFi                       |



WRT300N: 
    SSID: ISP3_WIFI
    WPA2-PSK PSK: 20-JE-04




## FIXERS:

 El SRV-DHCP está en 172.16.21.65. Para que sirva IPs a hosts en ISP1 e ISP3, los routers de gateway de esas redes deben redirigir los broadcasts DHCP hacia él.
Fix — ip helper-address en cada gateway:

! En R4-TU (gateway de Administración ISP1):
interface GigabitEthernet0/1
 ip helper-address 172.16.21.65

! En R5-TU (gateway de Atención al Cliente ISP1):
interface GigabitEthernet0/1
 ip helper-address 172.16.21.65

! En R2-CF (gateway de Soporte ISP3):
interface GigabitEthernet0/1
 ip helper-address 172.16.21.65

! En R3-CF (gateway de Seguridad ISP3):
interface GigabitEthernet0/1
 ip helper-address 172.16.21.65

! En R4-CF (gateway de WiFi ISP3):
interface GigabitEthernet0/1
 ip helper-address 172.16.21.65

! En R4-RN y R5-RN (gateway redundante de Facturacion ISP2):
interface GigabitEthernet0/2
 ip helper-address 172.16.21.65


 Y el SRV-DHCP necesita pools separados para cada subred de toda la topología:

Pool    RedDefault  Gateway
POOL-ADM172.16.11.0/27172.16.11.1
POOL-ATC172.16.11.32/27172.16.11.33
POOL-VENTAS172.16.21.0/27172.16.21.1 (VIP HSRP)
POOL-FACT172.16.21.32/27172.16.21.33 (VIP HSRP)
POOL-SOP172.16.32.0/27172.16.32.1
POOL-SEG172.16.32.32/27172.16.32.33
POOL-WIFI172.16.32.80/28172.16.32.81




— ISP2: Asignación correcta de IPs para HSRP
Las VIPs de HSRP son .1 y .33. Los routers físicos no pueden usar esa IP, deben usar las siguientes:
Para Ventas (172.16.21.0/27):

VIP HSRP: 172.16.21.1 (lo que los hosts usan como gateway)
R4-RN interfaz hacia Ventas: 172.16.21.2
R5-RN interfaz hacia Ventas: 172.16.21.3

Para Facturación (172.16.21.32/27):

VIP HSRP: 172.16.21.33
R4-RN interfaz hacia Facturación: 172.16.21.34
R5-RN interfaz hacia Facturación: 172.16.21.35

Comandos HSRP:
! En R4-RN (activo, prioridad más alta):
interface GigabitEthernet0/1
 description Hacia SW-VENTAS-DIST
 ip address 172.16.21.2 255.255.255.224
 standby 1 ip 172.16.21.1
 standby 1 priority 110
 standby 1 preempt
 no shutdown

interface GigabitEthernet0/2
 description Hacia SW-FACT-DIST
 ip address 172.16.21.34 255.255.255.224
 standby 2 ip 172.16.21.33
 standby 2 priority 110
 standby 2 preempt
 no shutdown

! En R5-RN (standby, prioridad default 100):
interface GigabitEthernet0/1
 description Hacia SW-VENTAS-DIST
 ip address 172.16.21.3 255.255.255.224
 standby 1 ip 172.16.21.1
 standby 1 priority 100
 no shutdown

interface GigabitEthernet0/2
 description Hacia SW-FACT-DIST
 ip address 172.16.21.35 255.255.255.224
 standby 2 ip 172.16.21.33
 standby 2 priority 100
 no shutdown


 ## FIX2
 Problema 4 — DNS: El dominio correcto según enunciado
El enunciado especifica que el dominio debe ser www.proyecto2_#carné.com. Para tu carné:

Dominio a configurar: www.proyecto2_202300431.com

En el SRV-DNS-HTTP de Packet Tracer, en la pestaña Services → DNS:

Name: www.proyecto2_202300431.com
Type: A Record
Address: 172.16.11.66 (IP del servidor)
Y en la pestaña HTTP, editar el index.html para que muestre tus datos y la información del curso.




Problema 5 — BGP entre los ICPs (Fase 3 — lo más importante)
Los 3 switches multicapa ICP forman una topología triangular y deben correr BGP. Usando tus subredes inter-ISP:

Enlace	Red	ICP lado A	ICP lado B
ICP1–ICP2	192.168.31.0/30	ICP1: .1	ICP2: .2
ICP2–ICP3	192.168.31.4/30	ICP2: .5	ICP3: .6
ICP3–ICP1	192.168.31.8/30	ICP3: .9	ICP1: .10
Comandos BGP (ejemplo para ICP1, AS 100):

! ICP1 - AS 100
router bgp 100
 neighbor 192.168.31.2 remote-as 200
 neighbor 192.168.31.10 remote-as 300
 network 172.16.11.0 mask 255.255.255.0
 redistribute ospf 1

! ICP2 - AS 200
router bgp 200
 neighbor 192.168.31.1 remote-as 100
 neighbor 192.168.31.6 remote-as 300
 network 172.16.21.0 mask 255.255.255.0
 redistribute ospf 1

! ICP3 - AS 300
router bgp 300
 neighbor 192.168.31.5 remote-as 200
 neighbor 192.168.31.9 remote-as 100
 network 172.16.32.0 mask 255.255.255.0
 redistribute eigrp 1
⚠️ En los switches multicapa (3650) primero activa ip routing para que funcionen como router.
