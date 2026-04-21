# Manual Tecnico - Proyecto 2

**Curso:** Redes de Computadoras 2  
**Proyecto:** Proyecto 2 - Telecom Uno, Redes Nacionales y Conexiones Futuras  
**Nommbre:** Juan Esteban Chacón trampe  
**Carne:** `202300431`  

## Resumen

Este manual tecnico consolida el desarrollo completo del proyecto: diseno de la topologia, subneteo, implementacion de los tres ISP, integracion BGP, aplicacion de ACLs y validaciones finales. El objetivo del laboratorio fue interconectar tres proveedores de servicio por medio de BGP, mantener protocolos internos independientes por ISP y sostener servicios centrales de `DHCP`, `DNS`, `HTTP`, `HSRP`, `LACP` y `WiFi`.

La solucion final utiliza:

- `OSPF` en ISP1 e ISP2
- `EIGRP` en ISP3
- `BGP` entre `ISP1`, `ISP2` y `ISP3`
- `DHCP` central en ISP2
- `DNS` y `HTTP` central en ISP1
- `HSRP` en ISP2
- `LACP` en los tres ISP
- `ACLs` por departamento para cumplir la matriz de comunicacion

## Fase 1 - Diseno

### Alcance

La Fase 1 definio el analisis de requisitos por ISP, el subneteo, el diseno de topologias, la frontera inter-ISP y la matriz de comunicacion que luego se implemento en las fases siguientes.

### Requisitos Por ISP

| ISP | Red base | Topologia | Protocolo interno | Equipo frontera | Servicio principal | Requisitos especiales |
| --- | --- | --- | --- | --- | --- | --- |
| ISP1 Telecom Uno | 172.16.11.0/24 | Arbol | OSPF | ISP1 | DNS y HTTP | 5 routers, 5 hosts, 2 enlaces LACP |
| ISP2 Redes Nacionales | 172.16.21.0/24 | Jerarquica | OSPF | ISP2 | DHCP | 5 routers, 5 hosts, 2 enlaces LACP, HSRP obligatorio |
| ISP3 Conexiones Futuras | 172.16.32.0/24 | Hub and Spoke | EIGRP | ISP3 | WiFi | 5 routers, 5 hosts, 2 enlaces LACP, router inalambrico |

### Servicios Centrales

| Servicio | Equipo | IP | Mascara | Gateway |
| --- | --- | --- | --- | --- |
| DNS y HTTP | SRV-DNS-HTTP | 172.16.11.66 | 255.255.255.240 | 172.16.11.65 |
| DHCP | SRV-DHCP | 172.16.21.66 | 255.255.255.240 | 172.16.21.65 |

El dominio configurado para el proyecto es `www.proyecto2_202300431.com`.

### Diseno Inter-ISP

La interconexion nacional utiliza tres equipos frontera en topologia triangular. Cada ISP mantiene su protocolo interno y anuncia su bloque principal al resto de ISPs por medio de BGP.

| ISP | Equipo frontera | AS propuesto | Aprende rutas internas con |
| --- | --- | --- | --- |
| ISP1 | ISP1 | 100 | OSPF area 0 |
| ISP2 | ISP2 | 200 | OSPF area 0 |
| ISP3 | ISP3 | 300 | EIGRP AS 1 |

#### Enlaces Inter-ISP

| Enlace | Red | Mascara | IP lado A | IP lado B | Broadcast |
| --- | --- | --- | --- | --- | --- |
| ISP1 - ISP2 | 192.168.31.0/30 | 255.255.255.252 | 192.168.31.1 | 192.168.31.2 | 192.168.31.3 |
| ISP2 - ISP3 | 192.168.31.4/30 | 255.255.255.252 | 192.168.31.5 | 192.168.31.6 | 192.168.31.7 |
| ISP3 - ISP1 | 192.168.31.8/30 | 255.255.255.252 | 192.168.31.9 | 192.168.31.10 | 192.168.31.11 |

#### Criterio De Borde

- `ISP1` es la frontera entre el dominio OSPF de ISP1 y el AS 100.
- `ISP2` es la frontera entre el dominio OSPF de ISP2 y el AS 200.
- `ISP3` es la frontera entre el dominio EIGRP de ISP3 y el AS 300.
- Esta separacion mantiene claro el limite entre el enrutamiento interno y el intercambio interdominio.

### Subneteo ISP1 - Telecom Uno

| Segmento | Red | Mascara | Primer host | Ultimo host | Broadcast | Gateway sugerido | Proposito |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Administracion | 172.16.11.0/27 | 255.255.255.224 | 172.16.11.1 | 172.16.11.30 | 172.16.11.31 | 172.16.11.1 | Hosts del departamento |
| Atencion al Cliente | 172.16.11.32/27 | 255.255.255.224 | 172.16.11.33 | 172.16.11.62 | 172.16.11.63 | 172.16.11.33 | Hosts del departamento |
| Servidores DNS/HTTP | 172.16.11.64/28 | 255.255.255.240 | 172.16.11.65 | 172.16.11.78 | 172.16.11.79 | 172.16.11.65 | Servicios centrales |
| Enlace R1-R2 | 172.16.11.80/30 | 255.255.255.252 | 172.16.11.81 | 172.16.11.82 | 172.16.11.83 | - | Punto a punto |
| Enlace R1-R3 | 172.16.11.84/30 | 255.255.255.252 | 172.16.11.85 | 172.16.11.86 | 172.16.11.87 | - | Punto a punto |
| Enlace R2-R4 | 172.16.11.88/30 | 255.255.255.252 | 172.16.11.89 | 172.16.11.90 | 172.16.11.91 | - | Punto a punto |
| Enlace R3-R5 | 172.16.11.92/30 | 255.255.255.252 | 172.16.11.93 | 172.16.11.94 | 172.16.11.95 | - | Punto a punto |
| Enlace ISP1-R1-TU | 172.16.11.96/30 | 255.255.255.252 | 172.16.11.97 | 172.16.11.98 | 172.16.11.99 | - | Borde del ISP |

### Subneteo ISP2 - Redes Nacionales

| Segmento | Red | Mascara | Primer host | Ultimo host | Broadcast | Gateway sugerido | Proposito |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Ventas | 172.16.21.0/27 | 255.255.255.224 | 172.16.21.1 | 172.16.21.30 | 172.16.21.31 | HSRP VIP 172.16.21.1 | Hosts del departamento |
| Facturacion | 172.16.21.32/27 | 255.255.255.224 | 172.16.21.33 | 172.16.21.62 | 172.16.21.63 | HSRP VIP 172.16.21.33 | Hosts del departamento |
| Servidor DHCP | 172.16.21.64/28 | 255.255.255.240 | 172.16.21.65 | 172.16.21.78 | 172.16.21.79 | 172.16.21.65 | Servicio central |
| Enlace R1-R2 | 172.16.21.80/30 | 255.255.255.252 | 172.16.21.81 | 172.16.21.82 | 172.16.21.83 | - | Punto a punto |
| Enlace R1-R3 | 172.16.21.84/30 | 255.255.255.252 | 172.16.21.85 | 172.16.21.86 | 172.16.21.87 | - | Punto a punto |
| Enlace R2-R4 | 172.16.21.88/30 | 255.255.255.252 | 172.16.21.89 | 172.16.21.90 | 172.16.21.91 | - | Punto a punto |
| Enlace R3-R5 | 172.16.21.92/30 | 255.255.255.252 | 172.16.21.93 | 172.16.21.94 | 172.16.21.95 | - | Punto a punto |
| Enlace ISP2-R1-RN | 172.16.21.96/30 | 255.255.255.252 | 172.16.21.97 | 172.16.21.98 | 172.16.21.99 | - | Borde del ISP |

#### Criterio De HSRP En ISP2

- La VIP de `Ventas` es `172.16.21.1`.
- La VIP de `Facturacion` es `172.16.21.33`.
- Las IP fisicas recomendadas para los routers son:
  - `R4-RN`: `172.16.21.2` y `172.16.21.34`
  - `R5-RN`: `172.16.21.3` y `172.16.21.35`

### Subneteo ISP3 - Conexiones Futuras

| Segmento | Red | Mascara | Primer host | Ultimo host | Broadcast | Gateway sugerido | Proposito |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Soporte | 172.16.32.0/27 | 255.255.255.224 | 172.16.32.1 | 172.16.32.30 | 172.16.32.31 | 172.16.32.1 | Hosts del departamento |
| Seguridad | 172.16.32.32/27 | 255.255.255.224 | 172.16.32.33 | 172.16.32.62 | 172.16.32.63 | 172.16.32.33 | Hosts del departamento |
| Backbone Hub | 172.16.32.64/28 | 255.255.255.240 | 172.16.32.65 | 172.16.32.78 | 172.16.32.79 | - | Red central del hub |
| WiFi Clientes | 172.16.32.80/28 | 255.255.255.240 | 172.16.32.81 | 172.16.32.94 | 172.16.32.95 | 172.16.32.81 | Red inalambrica |
| Reserva / Expansion | 172.16.32.96/28 | 255.255.255.240 | 172.16.32.97 | 172.16.32.110 | 172.16.32.111 | 172.16.32.97 | Crecimiento futuro |
| Enlace ISP3-R1-CF | 172.16.32.112/30 | 255.255.255.252 | 172.16.32.113 | 172.16.32.114 | 172.16.32.115 | - | Borde del ISP |

#### Justificacion De La Topologia Hub And Spoke

El diseno de ISP3 concentra la comunicacion interna en `SW-HUB-CF`, que actua como punto central del dominio. Desde ese nodo salen los spokes hacia `R2-CF`, `R3-CF`, `R4-CF` y `R5-CF`, lo que permite centralizar el trafico, simplificar el crecimiento y agregar el servicio WiFi sin alterar la estructura principal.

### Reglas De Comunicacion

| Origen \ Destino | Seguridad | Soporte | Administracion | Atencion al Cliente | Facturacion | Ventas |
| --- | --- | --- | --- | --- | --- | --- |
| Seguridad | - | Si | Si | Si | Si | Si |
| Soporte | No | - | Si | Si | Si | Si |
| Administracion | No | Si | - | Si | Si | Si |
| Atencion al Cliente | No | No | No | - | No | Si |
| Facturacion | No | No | No | No | - | Si |
| Ventas | No | No | No | Si | Si | - |

### Ubicacion Prevista De ACLs

- Las ACL se aplican en las interfaces de gateway de cada departamento.
- Esto permite controlar el trafico cerca del origen y facilita la validacion de las reglas de comunicacion.
- Los puntos principales de control son los gateways de `Administracion`, `Atencion al Cliente`, `Ventas`, `Facturacion`, `Soporte` y `Seguridad`.

### Justificacion Del Subneteo

- Las subredes `/27` se usaron para departamentos porque ofrecen 30 hosts utiles, suficientes para el laboratorio y con margen de crecimiento.
- Las subredes `/28` se reservaron para servicios, WiFi y segmentos pequenos donde 14 hosts utiles son suficientes.
- Las subredes `/30` se eligieron para enlaces punto a punto porque solo requieren dos direcciones utiles y evitan desperdicio de espacio.

### VLSM

En el proyecto se implemento `VLSM` (`Variable Length Subnet Mask`), ya que a partir de cada red base `/24` se generaron subredes de distinto tamano segun la necesidad de cada segmento. No se uso `FLSM`, porque no todas las subredes comparten la misma mascara.

El criterio aplicado fue el siguiente:

- `/27` para departamentos con usuarios finales
- `/28` para servicios, WiFi y segmentos pequenos
- `/30` para enlaces punto a punto entre routers

#### Mascaras Utilizadas

| Mascara | Uso en el proyecto | Hosts utiles |
| --- | --- | --- |
| /27 | Departamentos | 30 |
| /28 | Servicios, WiFi y segmentos pequenos | 14 |
| /30 | Enlaces punto a punto | 2 |

#### Aplicacion De VLSM Por ISP

| ISP | Segmento | Red | Mascara | Justificacion |
| --- | --- | --- | --- | --- |
| ISP1 | Administracion | 172.16.11.0 | /27 | Red de usuarios |
| ISP1 | Atencion al Cliente | 172.16.11.32 | /27 | Red de usuarios |
| ISP1 | DNS/HTTP | 172.16.11.64 | /28 | Segmento pequeno de servicios |
| ISP1 | Enlaces internos | 172.16.11.80 en adelante | /30 | Enlaces router-router |
| ISP2 | Ventas | 172.16.21.0 | /27 | Red de usuarios |
| ISP2 | Facturacion | 172.16.21.32 | /27 | Red de usuarios |
| ISP2 | DHCP | 172.16.21.64 | /28 | Segmento pequeno de servicio |
| ISP2 | Enlaces internos | 172.16.21.80 en adelante | /30 | Enlaces router-router |
| ISP3 | Soporte | 172.16.32.0 | /27 | Red de usuarios |
| ISP3 | Seguridad | 172.16.32.32 | /27 | Red de usuarios |
| ISP3 | Backbone Hub | 172.16.32.64 | /28 | Segmento central pequeno |
| ISP3 | WiFi | 172.16.32.80 | /28 | Segmento inalambrico |

#### Conclusion De VLSM

El uso de `VLSM` permitio adaptar el tamano de cada subred al tipo de segmento, evitando desperdicio de direcciones IP. Esto hizo posible separar departamentos, servicios y enlaces de manera eficiente, mantener orden en el direccionamiento y dejar espacio para crecimiento futuro.

## Topologia

### Centro

`ISP1 Gi1/1/1 - Gi1/1/2 ISP2`  
`ISP2 Gi1/1/1 - Gi1/1/2 ISP3`  
`ISP3 Gi1/1/1 - Gi1/1/2 ISP1`

| Equipo frontera | ISP asociado | AS | IGP interno |
| --- | --- | --- | --- |
| ISP1 | ISP1 | 100 | OSPF |
| ISP2 | ISP2 | 200 | OSPF |
| ISP3 | ISP3 | 300 | EIGRP |

### ISP1

| Origen | Puerto | Destino | Puerto | Nota |
| --- | --- | --- | --- | --- |
| ISP1 | Gi1/0/1 | R1-TU | Gi0/0 | Enlace de ISP1 hacia interconexion |
| R1-TU | Gi0/1 | R2-TU | Gi0/0 | Rama Administracion |
| R1-TU | Gi0/2 | R3-TU | Gi0/0 | Rama Atencion al Cliente |
| R2-TU | Gi0/1 | R4-TU | Gi0/0 | Subrama ADM |
| R3-TU | Gi0/1 | R5-TU | Gi0/0 | Subrama ATC |
| R4-TU | Gi0/1 | SW-ADM-DIST | Fa0/24 | Subida a acceso ADM |
| R4-TU | Gi0/2 | SRV-DNS-HTTP | Fa0 | Red de servidores |
| SW-ADM-DIST | Gi0/1 | SW-ADM-ACC | Gi0/1 | LACP Port-Channel 1 enlace A |
| SW-ADM-DIST | Gi0/2 | SW-ADM-ACC | Gi0/2 | LACP Port-Channel 1 enlace B |
| SW-ADM-ACC | Fa0/1 | PC-ADM-1 | Fa0 | Host |
| SW-ADM-ACC | Fa0/2 | PC-ADM-2 | Fa0 | Host |
| SW-ADM-ACC | Fa0/3 | PC-ADM-3 | Fa0 | Host |
| R5-TU | Gi0/1 | SW-ATC-DIST | Fa0/24 | Subida a acceso ATC |
| SW-ATC-DIST | Gi0/1 | SW-ATC-ACC | Gi0/1 | LACP Port-Channel 2 enlace A |
| SW-ATC-DIST | Gi0/2 | SW-ATC-ACC | Gi0/2 | LACP Port-Channel 2 enlace B |
| SW-ATC-ACC | Fa0/1 | PC-ATC-1 | Fa0 | Host |
| SW-ATC-ACC | Fa0/2 | PC-ATC-2 | Fa0 | Host |
| SW-ATC-ACC | Fa0/3 | PC-ATC-3 | Fa0 | Host |

### ISP2

| Origen | Puerto | Destino | Puerto | Nota |
| --- | --- | --- | --- | --- |
| ISP2 | Gi1/0/1 | R1-RN | Gi0/0 | Salida de ISP2 hacia interconexion |
| R1-RN | Gi0/1 | R2-RN | Gi0/0 | Core a distribucion A |
| R1-RN | Gi0/2 | R3-RN | Gi0/0 | Core a distribucion B |
| R2-RN | Gi0/1 | R4-RN | Gi0/0 | Distribucion A a gateway A |
| R3-RN | Gi0/1 | R5-RN | Gi0/0 | Distribucion B a gateway B |
| R4-RN | Gi0/1 | SW-VENTAS-DIST | Fa0/23 | Gateway A hacia VLAN Ventas |
| R5-RN | Gi0/1 | SW-VENTAS-DIST | Fa0/24 | Gateway B hacia VLAN Ventas |
| R4-RN | Gi0/2 | SW-FACT-DIST | Fa0/23 | Gateway A hacia VLAN Facturacion |
| R5-RN | Gi0/2 | SW-FACT-DIST | Fa0/24 | Gateway B hacia VLAN Facturacion |
| SW-VENTAS-DIST | Gi0/1 | SW-VENTAS-ACC | Gi0/1 | LACP Port-Channel 1 enlace A |
| SW-VENTAS-DIST | Gi0/2 | SW-VENTAS-ACC | Gi0/2 | LACP Port-Channel 1 enlace B |
| SW-FACT-DIST | Gi0/1 | SW-FACT-ACC | Gi0/1 | LACP Port-Channel 2 enlace A |
| SW-FACT-DIST | Gi0/2 | SW-FACT-ACC | Gi0/2 | LACP Port-Channel 2 enlace B |
| SW-VENTAS-DIST | Fa0/22 | SRV-DHCP | Fa0 | Servidor DHCP |
| SW-VENTAS-ACC | Fa0/1 | PC-VENTAS-1 | Fa0 | Host |
| SW-VENTAS-ACC | Fa0/2 | PC-VENTAS-2 | Fa0 | Host |
| SW-VENTAS-ACC | Fa0/3 | PC-VENTAS-3 | Fa0 | Host |
| SW-FACT-ACC | Fa0/1 | PC-FACT-1 | Fa0 | Host |
| SW-FACT-ACC | Fa0/2 | PC-FACT-2 | Fa0 | Host |
| SW-FACT-ACC | Fa0/3 | PC-FACT-3 | Fa0 | Host |

### ISP3

| Origen | Puerto | Destino | Puerto | Nota |
| --- | --- | --- | --- | --- |
| ISP3 | Gi1/0/1 | R1-CF | Gi0/0 | Salida de ISP3 hacia interconexion |
| R1-CF | Gi0/1 | SW-HUB-CF | Fa0/24 | Enlace del borde al hub central |
| R2-CF | Gi0/0 | SW-HUB-CF | Fa0/1 | Spoke Soporte |
| R3-CF | Gi0/0 | SW-HUB-CF | Fa0/2 | Spoke Seguridad |
| R4-CF | Gi0/0 | SW-HUB-CF | Fa0/3 | Spoke WiFi |
| R5-CF | Gi0/0 | SW-HUB-CF | Fa0/4 | Spoke Expansion |
| R2-CF | Gi0/1 | SW-SOP-DIST | Fa0/24 | Enlace a red Soporte |
| SW-SOP-DIST | Gi0/1 | SW-SOP-ACC | Gi0/1 | LACP Port-Channel 1 enlace A |
| SW-SOP-DIST | Gi0/2 | SW-SOP-ACC | Gi0/2 | LACP Port-Channel 1 enlace B |
| SW-SOP-ACC | Fa0/1 | PC-SOP-1 | Fa0 | Host |
| SW-SOP-ACC | Fa0/2 | PC-SOP-2 | Fa0 | Host |
| SW-SOP-ACC | Fa0/3 | PC-SOP-3 | Fa0 | Host |
| R3-CF | Gi0/1 | SW-SEG-DIST | Fa0/24 | Enlace a red Seguridad |
| SW-SEG-DIST | Gi0/1 | SW-SEG-ACC | Gi0/1 | LACP Port-Channel 2 enlace A |
| SW-SEG-DIST | Gi0/2 | SW-SEG-ACC | Gi0/2 | LACP Port-Channel 2 enlace B |
| SW-SEG-ACC | Fa0/1 | PC-SEG-1 | Fa0 | Host |
| SW-SEG-ACC | Fa0/2 | PC-SEG-2 | Fa0 | Host |
| SW-SEG-ACC | Fa0/3 | PC-SEG-3 | Fa0 | Host |
| R4-CF | Gi0/1 | WRT300N | Ethernet 1 | Punto de acceso WiFi |
| Laptop WiFi | Wireless0 | WRT300N | Wireless | Cliente WiFi |

#### Parametros De WiFi

- SSID: `ISP3_WIFI`
- Seguridad: `WPA2 Personal`
- Passphrase: `20-JE-04`

## Fase 2 - Implementacion Interna

### Objetivo General

La Fase 2 implemento cada ISP por separado antes de la integracion BGP. Se configuraron protocolos internos, servicios obligatorios, enlaces LACP, HSRP y la red inalambrica de ISP3.

### Resumen De Implementacion

#### ISP1

- `OSPF` para interconexion interna.
- `ip helper-address` hacia el servidor DHCP central.
- `DNS` y `HTTP` alojados en `SRV-DNS-HTTP`.
- Dos `Port-channel` mediante `LACP`.

#### ISP2

- `OSPF` interno.
- `HSRP` en `Ventas` y `Facturacion`.
- `DHCP` central para toda la topologia.
- `LACP` en redes de acceso.
- Ajuste practico: `SRV-DHCP` usa `172.16.21.66/28` y gateway `172.16.21.65`.

#### ISP3

- `EIGRP` interno.
- Backbone tipo `Hub and Spoke`.
- `LACP` en Soporte y Seguridad.
- `WRT300N` como punto de acceso, sin NAT y sin DHCP local.

## Fase 3 - Integracion BGP

### Modelo Elegido

Se utilizo un modelo sencillo de integracion BGP:

- `BGP` solo entre `ISP1`, `ISP2` y `ISP3`
- cada `ISP` anuncia el prefijo resumen de su ISP
- cada `ISP` tiene una ruta estatica hacia su bloque local
- cada router borde `R1-*` tiene ruta por defecto hacia su `ISP`

### Justificacion Del Modelo Sencillo

El enunciado pide configurar BGP entre routers principales, establecer vecinos, intercambiar rutas y validar conectividad inter-ISP. Este modelo cumple esos requisitos, reduce la complejidad operativa, evita errores de redistribucion innecesaria entre IGP y BGP y facilita la validacion en Packet Tracer.

### Prefijos Anunciados

| Equipo | AS | Prefijo anunciado |
| --- | --- | --- |
| ISP1 | 100 | 172.16.11.0/24 |
| ISP2 | 200 | 172.16.21.0/24 |
| ISP3 | 300 | 172.16.32.0/24 |

### Resultado Esperado De Fase 3

- vecinos BGP en estado `Established`
- cada `ISP` aprende los otros dos bloques `172.16.x.0/24`
- los routers internos reciben salida por defecto
- `DHCP`, `DNS` y `HTTP` quedan disponibles entre ISPs

## Fase 4 - ACLs Y Validacion

### Estrategia De ACL

Se aplicaron ACLs simples y directas en los gateways de departamento para cumplir la matriz de comunicacion sin romper `DHCP`, `DNS` y `HTTP`. El criterio operativo fue permitir servicios centrales y controlar la comunicacion entre departamentos principalmente con `ICMP`, que es la forma de prueba indicada en la calificacion.

### Principios Aplicados

- permitir `DHCP relay`
- permitir `DNS` hacia `172.16.11.66`
- permitir `HTTP` hacia `172.16.11.66`
- permitir `ICMP echo-reply`
- bloquear o permitir `ICMP echo` segun la matriz de comunicacion

### Interpretacion Operativa

| Origen | Puede iniciar ping hacia |
| --- | --- |
| Seguridad | Todos los departamentos |
| Soporte | Todos menos Seguridad |
| Administracion | Todos menos Seguridad |
| Atencion al Cliente | Solo Ventas |
| Facturacion | Solo Ventas |
| Ventas | Solo Facturacion y Atencion al Cliente |

`WiFi` no aparece en la matriz del enunciado, por lo que no se restringio con ACL en esta fase.

### Resultado Esperado De Fase 4

- las ACL cumplen la matriz de comunicacion
- `DHCP` sigue funcionando en todos los clientes
- `DNS` sigue resolviendo el dominio del proyecto
- `HTTP` sigue respondiendo desde el servidor central

## Comandos

En esta seccion se incluyen los bloques completos de configuracion usados durante el proyecto, organizados por fase y por dispositivo.

### Fase 2 - CORE

#### ISP1

```cisco
enable
configure terminal
hostname ISP1
no ip domain-lookup
ip routing

interface GigabitEthernet1/1/1
 description Hacia ISP2
 no switchport
 ip address 192.168.31.1 255.255.255.252
 no shutdown

interface GigabitEthernet1/1/2
 description Hacia ISP3
 no switchport
 ip address 192.168.31.10 255.255.255.252
 no shutdown

interface GigabitEthernet1/0/1
 description Hacia R1-TU
 no switchport
 ip address 172.16.11.97 255.255.255.252
 no shutdown

end
write memory
```

#### ISP2

```cisco
enable
configure terminal
hostname ISP2
no ip domain-lookup
ip routing

interface GigabitEthernet1/1/2
 description Hacia ISP1
 no switchport
 ip address 192.168.31.2 255.255.255.252
 no shutdown

interface GigabitEthernet1/1/1
 description Hacia ISP3
 no switchport
 ip address 192.168.31.5 255.255.255.252
 no shutdown

interface GigabitEthernet1/0/1
 description Hacia R1-RN
 no switchport
 ip address 172.16.21.97 255.255.255.252
 no shutdown

end
write memory
```

#### ISP3

```cisco
enable
configure terminal
hostname ISP3
no ip domain-lookup
ip routing

interface GigabitEthernet1/1/2
 description Hacia ISP2
 no switchport
 ip address 192.168.31.6 255.255.255.252
 no shutdown

interface GigabitEthernet1/1/1
 description Hacia ISP1
 no switchport
 ip address 192.168.31.9 255.255.255.252
 no shutdown

interface GigabitEthernet1/0/1
 description Hacia R1-CF
 no switchport
 ip address 172.16.32.113 255.255.255.252
 no shutdown

end
write memory
```

### Fase 2 - ISP1

#### R1-TU

```cisco
enable
configure terminal
hostname R1-TU
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia ISP1
 ip address 172.16.11.98 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 description Hacia R2-TU
 ip address 172.16.11.81 255.255.255.252
 no shutdown

interface GigabitEthernet0/2
 description Hacia R3-TU
 ip address 172.16.11.85 255.255.255.252
 no shutdown

router ospf 1
 router-id 1.1.1.1
 network 172.16.11.96 0.0.0.3 area 0
 network 172.16.11.80 0.0.0.3 area 0
 network 172.16.11.84 0.0.0.3 area 0

end
write memory
```

#### R2-TU

```cisco
enable
configure terminal
hostname R2-TU
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia R1-TU
 ip address 172.16.11.82 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 description Hacia R4-TU
 ip address 172.16.11.89 255.255.255.252
 no shutdown

router ospf 1
 router-id 2.2.2.2
 network 172.16.11.80 0.0.0.3 area 0
 network 172.16.11.88 0.0.0.3 area 0

end
write memory
```

#### R3-TU

```cisco
enable
configure terminal
hostname R3-TU
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia R1-TU
 ip address 172.16.11.86 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 description Hacia R5-TU
 ip address 172.16.11.93 255.255.255.252
 no shutdown

router ospf 1
 router-id 3.3.3.3
 network 172.16.11.84 0.0.0.3 area 0
 network 172.16.11.92 0.0.0.3 area 0

end
write memory
```

#### R4-TU

```cisco
enable
configure terminal
hostname R4-TU
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia R2-TU
 ip address 172.16.11.90 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 description Gateway Administracion
 ip address 172.16.11.1 255.255.255.224
 ip helper-address 172.16.21.66
 no shutdown

interface GigabitEthernet0/2
 description Red Servidores DNS-HTTP
 ip address 172.16.11.65 255.255.255.240
 no shutdown

router ospf 1
 router-id 4.4.4.4
 network 172.16.11.88 0.0.0.3 area 0
 network 172.16.11.0 0.0.0.31 area 0
 network 172.16.11.64 0.0.0.15 area 0
 passive-interface GigabitEthernet0/1
 passive-interface GigabitEthernet0/2

end
write memory
```

#### R5-TU

```cisco
enable
configure terminal
hostname R5-TU
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia R3-TU
 ip address 172.16.11.94 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 description Gateway Atencion al Cliente
 ip address 172.16.11.33 255.255.255.224
 ip helper-address 172.16.21.66
 no shutdown

router ospf 1
 router-id 5.5.5.5
 network 172.16.11.92 0.0.0.3 area 0
 network 172.16.11.32 0.0.0.31 area 0
 passive-interface GigabitEthernet0/1

end
write memory
```

#### SW-ADM-DIST

```cisco
enable
configure terminal
hostname SW-ADM-DIST
no ip domain-lookup

interface range GigabitEthernet0/1 - 2
 description LACP hacia SW-ADM-ACC
 switchport mode access
 channel-group 1 mode active
 no shutdown

interface Port-channel1
 description LACP hacia SW-ADM-ACC
 switchport mode access

interface FastEthernet0/24
 description Hacia R4-TU
 switchport mode access
 spanning-tree portfast

end
write memory
```

#### SW-ADM-ACC

```cisco
enable
configure terminal
hostname SW-ADM-ACC
no ip domain-lookup

interface range GigabitEthernet0/1 - 2
 description LACP hacia SW-ADM-DIST
 switchport mode access
 channel-group 1 mode active
 no shutdown

interface Port-channel1
 description LACP hacia SW-ADM-DIST
 switchport mode access

interface range FastEthernet0/1 - 3
 switchport mode access
 spanning-tree portfast

end
write memory
```

#### SW-ATC-DIST

```cisco
enable
configure terminal
hostname SW-ATC-DIST
no ip domain-lookup

interface range GigabitEthernet0/1 - 2
 description LACP hacia SW-ATC-ACC
 switchport mode access
 channel-group 2 mode active
 no shutdown

interface Port-channel2
 description LACP hacia SW-ATC-ACC
 switchport mode access

interface FastEthernet0/24
 description Hacia R5-TU
 switchport mode access
 spanning-tree portfast

end
write memory
```

#### SW-ATC-ACC

```cisco
enable
configure terminal
hostname SW-ATC-ACC
no ip domain-lookup

interface range GigabitEthernet0/1 - 2
 description LACP hacia SW-ATC-DIST
 switchport mode access
 channel-group 2 mode active
 no shutdown

interface Port-channel2
 description LACP hacia SW-ATC-DIST
 switchport mode access

interface range FastEthernet0/1 - 3
 switchport mode access
 spanning-tree portfast

end
write memory
```

#### SRV-DNS-HTTP

- IP: `172.16.11.66`
- Mask: `255.255.255.240`
- Default Gateway: `172.16.11.65`
- DNS Server: `172.16.11.66`
- DNS Record: `www.proyecto2_202300431.com -> 172.16.11.66`
- HTTP: `On`

### Fase 2 - ISP2

#### R1-RN

```cisco
enable
configure terminal
hostname R1-RN
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia ISP2
 ip address 172.16.21.98 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 description Hacia R2-RN
 ip address 172.16.21.81 255.255.255.252
 no shutdown

interface GigabitEthernet0/2
 description Hacia R3-RN
 ip address 172.16.21.85 255.255.255.252
 no shutdown

router ospf 1
 router-id 11.11.11.11
 network 172.16.21.96 0.0.0.3 area 0
 network 172.16.21.80 0.0.0.3 area 0
 network 172.16.21.84 0.0.0.3 area 0

end
write memory
```

#### R2-RN

```cisco
enable
configure terminal
hostname R2-RN
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia R1-RN
 ip address 172.16.21.82 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 description Hacia R4-RN
 ip address 172.16.21.89 255.255.255.252
 no shutdown

router ospf 1
 router-id 12.12.12.12
 network 172.16.21.80 0.0.0.3 area 0
 network 172.16.21.88 0.0.0.3 area 0

end
write memory
```

#### R3-RN

```cisco
enable
configure terminal
hostname R3-RN
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia R1-RN
 ip address 172.16.21.86 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 description Hacia R5-RN
 ip address 172.16.21.93 255.255.255.252
 no shutdown

router ospf 1
 router-id 13.13.13.13
 network 172.16.21.84 0.0.0.3 area 0
 network 172.16.21.92 0.0.0.3 area 0

end
write memory
```

#### R4-RN

```cisco
enable
configure terminal
hostname R4-RN
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia R2-RN
 ip address 172.16.21.90 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 no ip address
 no shutdown

interface GigabitEthernet0/1.10
 encapsulation dot1Q 10
 description VLAN Ventas
 ip address 172.16.21.2 255.255.255.224
 standby 10 ip 172.16.21.1
 standby 10 priority 110
 standby 10 preempt
 ip helper-address 172.16.21.66

interface GigabitEthernet0/1.30
 encapsulation dot1Q 30
 description VLAN SRV-DHCP
 ip address 172.16.21.67 255.255.255.240
 standby 30 ip 172.16.21.65
 standby 30 priority 110
 standby 30 preempt

interface GigabitEthernet0/2
 description Hacia SW-FACT-DIST
 ip address 172.16.21.34 255.255.255.224
 standby 20 ip 172.16.21.33
 standby 20 priority 110
 standby 20 preempt
 ip helper-address 172.16.21.66
 no shutdown

router ospf 1
 router-id 14.14.14.14
 network 172.16.21.88 0.0.0.3 area 0
 network 172.16.21.0 0.0.0.31 area 0
 network 172.16.21.32 0.0.0.31 area 0
 network 172.16.21.64 0.0.0.15 area 0
 passive-interface GigabitEthernet0/1.10
 passive-interface GigabitEthernet0/1.30
 passive-interface GigabitEthernet0/2

end
write memory
```

#### R5-RN

```cisco
enable
configure terminal
hostname R5-RN
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia R3-RN
 ip address 172.16.21.94 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 no ip address
 no shutdown

interface GigabitEthernet0/1.10
 encapsulation dot1Q 10
 description VLAN Ventas
 ip address 172.16.21.3 255.255.255.224
 standby 10 ip 172.16.21.1
 standby 10 priority 100
 ip helper-address 172.16.21.66

interface GigabitEthernet0/1.30
 encapsulation dot1Q 30
 description VLAN SRV-DHCP
 ip address 172.16.21.68 255.255.255.240
 standby 30 ip 172.16.21.65
 standby 30 priority 100

interface GigabitEthernet0/2
 description Hacia SW-FACT-DIST
 ip address 172.16.21.35 255.255.255.224
 standby 20 ip 172.16.21.33
 standby 20 priority 100
 ip helper-address 172.16.21.66
 no shutdown

router ospf 1
 router-id 15.15.15.15
 network 172.16.21.92 0.0.0.3 area 0
 network 172.16.21.0 0.0.0.31 area 0
 network 172.16.21.32 0.0.0.31 area 0
 network 172.16.21.64 0.0.0.15 area 0
 passive-interface GigabitEthernet0/1.10
 passive-interface GigabitEthernet0/1.30
 passive-interface GigabitEthernet0/2

end
write memory
```

#### SW-VENTAS-DIST

```cisco
enable
configure terminal
hostname SW-VENTAS-DIST
no ip domain-lookup

vlan 10
 name VENTAS

vlan 30
 name SRV_DHCP

interface FastEthernet0/23
 description Trunk hacia R4-RN
 switchport mode trunk
 switchport trunk allowed vlan 10,30

interface FastEthernet0/24
 description Trunk hacia R5-RN
 switchport mode trunk
 switchport trunk allowed vlan 10,30

interface FastEthernet0/22
 description Hacia SRV-DHCP
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast

interface range GigabitEthernet0/1 - 2
 description LACP hacia SW-VENTAS-ACC
 switchport mode access
 switchport access vlan 10
 channel-group 1 mode active
 no shutdown

interface Port-channel1
 description LACP hacia SW-VENTAS-ACC
 switchport mode access
 switchport access vlan 10

end
write memory
```

#### SW-VENTAS-ACC

```cisco
enable
configure terminal
hostname SW-VENTAS-ACC
no ip domain-lookup

vlan 10
 name VENTAS

interface range GigabitEthernet0/1 - 2
 description LACP hacia SW-VENTAS-DIST
 switchport mode access
 switchport access vlan 10
 channel-group 1 mode active
 no shutdown

interface Port-channel1
 description LACP hacia SW-VENTAS-DIST
 switchport mode access
 switchport access vlan 10

interface range FastEthernet0/1 - 3
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast

end
write memory
```

#### SW-FACT-DIST

```cisco
enable
configure terminal
hostname SW-FACT-DIST
no ip domain-lookup

vlan 20
 name FACTURACION

interface FastEthernet0/23
 description Hacia R4-RN
 switchport mode access
 switchport access vlan 20

interface FastEthernet0/24
 description Hacia R5-RN
 switchport mode access
 switchport access vlan 20

interface range GigabitEthernet0/1 - 2
 description LACP hacia SW-FACT-ACC
 switchport mode access
 switchport access vlan 20
 channel-group 2 mode active
 no shutdown

interface Port-channel2
 description LACP hacia SW-FACT-ACC
 switchport mode access
 switchport access vlan 20

end
write memory
```

#### SW-FACT-ACC

```cisco
enable
configure terminal
hostname SW-FACT-ACC
no ip domain-lookup

vlan 20
 name FACTURACION

interface range GigabitEthernet0/1 - 2
 description LACP hacia SW-FACT-DIST
 switchport mode access
 switchport access vlan 20
 channel-group 2 mode active
 no shutdown

interface Port-channel2
 description LACP hacia SW-FACT-DIST
 switchport mode access
 switchport access vlan 20

interface range FastEthernet0/1 - 3
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast

end
write memory
```

#### SRV-DHCP

- IP: `172.16.21.66`
- Mask: `255.255.255.240`
- Default Gateway: `172.16.21.65`
- DNS Server: `172.16.11.66`

| Pool | Default Gateway | DNS | Start IP | Subnet Mask |
| --- | --- | --- | --- | --- |
| POOL-ADM | 172.16.11.1 | 172.16.11.66 | 172.16.11.2 | 255.255.255.224 |
| POOL-ATC | 172.16.11.33 | 172.16.11.66 | 172.16.11.34 | 255.255.255.224 |
| POOL-VENTAS | 172.16.21.1 | 172.16.11.66 | 172.16.21.4 | 255.255.255.224 |
| POOL-FACT | 172.16.21.33 | 172.16.11.66 | 172.16.21.36 | 255.255.255.224 |
| POOL-SOP | 172.16.32.1 | 172.16.11.66 | 172.16.32.2 | 255.255.255.224 |
| POOL-SEG | 172.16.32.33 | 172.16.11.66 | 172.16.32.34 | 255.255.255.224 |
| POOL-WIFI | 172.16.32.81 | 172.16.11.66 | 172.16.32.83 | 255.255.255.240 |

### Fase 2 - ISP3

#### R1-CF

```cisco
enable
configure terminal
hostname R1-CF
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia ISP3
 ip address 172.16.32.114 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 description Hacia SW-HUB-CF backbone
 ip address 172.16.32.65 255.255.255.240
 no shutdown

router eigrp 1
 no auto-summary
 network 172.16.32.112 0.0.0.3
 network 172.16.32.64 0.0.0.15

end
write memory
```

#### R2-CF

```cisco
enable
configure terminal
hostname R2-CF
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia SW-HUB-CF
 ip address 172.16.32.66 255.255.255.240
 no shutdown

interface GigabitEthernet0/1
 description Gateway Soporte
 ip address 172.16.32.1 255.255.255.224
 ip helper-address 172.16.21.66
 no shutdown

router eigrp 1
 no auto-summary
 network 172.16.32.64 0.0.0.15
 network 172.16.32.0 0.0.0.31
 passive-interface GigabitEthernet0/1

end
write memory
```

#### R3-CF

```cisco
enable
configure terminal
hostname R3-CF
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia SW-HUB-CF
 ip address 172.16.32.67 255.255.255.240
 no shutdown

interface GigabitEthernet0/1
 description Gateway Seguridad
 ip address 172.16.32.33 255.255.255.224
 ip helper-address 172.16.21.66
 no shutdown

router eigrp 1
 no auto-summary
 network 172.16.32.64 0.0.0.15
 network 172.16.32.32 0.0.0.31
 passive-interface GigabitEthernet0/1

end
write memory
```

#### R4-CF

```cisco
enable
configure terminal
hostname R4-CF
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia SW-HUB-CF
 ip address 172.16.32.68 255.255.255.240
 no shutdown

interface GigabitEthernet0/1
 description Gateway WiFi
 ip address 172.16.32.81 255.255.255.240
 ip helper-address 172.16.21.66
 no shutdown

router eigrp 1
 no auto-summary
 network 172.16.32.64 0.0.0.15
 network 172.16.32.80 0.0.0.15
 passive-interface GigabitEthernet0/1

end
write memory
```

#### R5-CF

```cisco
enable
configure terminal
hostname R5-CF
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia SW-HUB-CF
 ip address 172.16.32.69 255.255.255.240
 no shutdown

router eigrp 1
 no auto-summary
 network 172.16.32.64 0.0.0.15

end
write memory
```

#### SW-HUB-CF

```cisco
enable
configure terminal
hostname SW-HUB-CF
no ip domain-lookup

interface FastEthernet0/24
 description Hacia R1-CF
 switchport mode access

interface range FastEthernet0/1 - 4
 switchport mode access

end
write memory
```

#### SW-SOP-DIST

```cisco
enable
configure terminal
hostname SW-SOP-DIST
no ip domain-lookup

interface FastEthernet0/24
 description Hacia R2-CF
 switchport mode access
 spanning-tree portfast

interface range GigabitEthernet0/1 - 2
 description LACP hacia SW-SOP-ACC
 switchport mode access
 channel-group 1 mode active
 no shutdown

interface Port-channel1
 description LACP hacia SW-SOP-ACC
 switchport mode access

end
write memory
```

#### SW-SOP-ACC

```cisco
enable
configure terminal
hostname SW-SOP-ACC
no ip domain-lookup

interface range GigabitEthernet0/1 - 2
 description LACP hacia SW-SOP-DIST
 switchport mode access
 channel-group 1 mode active
 no shutdown

interface Port-channel1
 description LACP hacia SW-SOP-DIST
 switchport mode access

interface range FastEthernet0/1 - 3
 switchport mode access
 spanning-tree portfast

end
write memory
```

#### SW-SEG-DIST

```cisco
enable
configure terminal
hostname SW-SEG-DIST
no ip domain-lookup

interface FastEthernet0/24
 description Hacia R3-CF
 switchport mode access
 spanning-tree portfast

interface range GigabitEthernet0/1 - 2
 description LACP hacia SW-SEG-ACC
 switchport mode access
 channel-group 2 mode active
 no shutdown

interface Port-channel2
 description LACP hacia SW-SEG-ACC
 switchport mode access

end
write memory
```

#### SW-SEG-ACC

```cisco
enable
configure terminal
hostname SW-SEG-ACC
no ip domain-lookup

interface range GigabitEthernet0/1 - 2
 description LACP hacia SW-SEG-DIST
 switchport mode access
 channel-group 2 mode active
 no shutdown

interface Port-channel2
 description LACP hacia SW-SEG-DIST
 switchport mode access

interface range FastEthernet0/1 - 3
 switchport mode access
 spanning-tree portfast

end
write memory
```

#### WRT300N

Configuracion como punto de acceso:

1. Conectar `R4-CF` a un puerto LAN del `WRT300N`.
2. Configurar:
   - Local IP Address: `172.16.32.82`
   - Subnet Mask: `255.255.255.240`
   - Default Gateway: `172.16.32.81`
   - DHCP Server: `Disable`
3. SSID: `ISP3_WIFI`
4. Security Mode: `WPA2 Personal`
5. Passphrase: `20-JE-04`

### Fase 3 - Salida Hacia El Core

#### R1-TU

```cisco
enable
configure terminal

ip route 0.0.0.0 0.0.0.0 172.16.11.97

router ospf 1
 default-information originate

end
write memory
```

#### R1-RN

```cisco
enable
configure terminal

ip route 0.0.0.0 0.0.0.0 172.16.21.97

router ospf 1
 default-information originate

end
write memory
```

#### R1-CF

```cisco
enable
configure terminal

ip route 0.0.0.0 0.0.0.0 172.16.32.113

router eigrp 1
 redistribute static

end
write memory
```

### Fase 3 - CORE BGP

#### ISP1

```cisco
enable
configure terminal

ip route 172.16.11.0 255.255.255.0 172.16.11.98

router bgp 100
 neighbor 192.168.31.2 remote-as 200
 neighbor 192.168.31.9 remote-as 300
 network 172.16.11.0 mask 255.255.255.0

end
write memory
```

#### ISP2

```cisco
enable
configure terminal

ip route 172.16.21.0 255.255.255.0 172.16.21.98

router bgp 200
 neighbor 192.168.31.1 remote-as 100
 neighbor 192.168.31.6 remote-as 300
 network 172.16.21.0 mask 255.255.255.0

end
write memory
```

#### ISP3

```cisco
enable
configure terminal

ip route 172.16.32.0 255.255.255.0 172.16.32.114

router bgp 300
 neighbor 192.168.31.10 remote-as 100
 neighbor 192.168.31.5 remote-as 200
 network 172.16.32.0 mask 255.255.255.0

end
write memory
```

### Fase 4 - ACLs

Nota: en Packet Tracer algunas imagenes IOS no aceptan `deny ip any any log`, por lo que en la implementacion final se recomienda usar `deny ip any any` o dejar el `deny` implicito. Los permisos centrales para `DHCP`, `DNS`, `HTTP` y `ICMP echo-reply` se mantienen iguales.

#### R4-TU - ACL-ADM-IN

```cisco
enable
configure terminal

ip access-list extended ACL-ADM-IN
 remark DHCP relay
 permit udp any eq bootpc any eq bootps
 remark DNS al servidor central
 permit udp any host 172.16.11.66 eq domain
 permit tcp any host 172.16.11.66 eq domain
 remark HTTP al servidor central
 permit tcp any host 172.16.11.66 eq 80
 remark Permitir respuestas de ping
 permit icmp any any echo-reply
 remark Administracion no puede iniciar hacia Seguridad
 deny icmp 172.16.11.0 0.0.0.31 172.16.32.32 0.0.0.31 echo
 remark Administracion si puede iniciar hacia los demas
 permit icmp 172.16.11.0 0.0.0.31 172.16.11.32 0.0.0.31 echo
 permit icmp 172.16.11.0 0.0.0.31 172.16.21.0 0.0.0.31 echo
 permit icmp 172.16.11.0 0.0.0.31 172.16.21.32 0.0.0.31 echo
 permit icmp 172.16.11.0 0.0.0.31 172.16.32.0 0.0.0.31 echo
 deny ip any any

interface GigabitEthernet0/1
 ip access-group ACL-ADM-IN in

end
write memory
```

#### R5-TU - ACL-ATC-IN

```cisco
enable
configure terminal

ip access-list extended ACL-ATC-IN
 remark DHCP relay
 permit udp any eq bootpc any eq bootps
 remark DNS al servidor central
 permit udp any host 172.16.11.66 eq domain
 permit tcp any host 172.16.11.66 eq domain
 remark HTTP al servidor central
 permit tcp any host 172.16.11.66 eq 80
 remark Permitir respuestas de ping
 permit icmp any any echo-reply
 remark ATC solo puede iniciar hacia Ventas
 permit icmp 172.16.11.32 0.0.0.31 172.16.21.0 0.0.0.31 echo
 deny ip any any

interface GigabitEthernet0/1
 ip access-group ACL-ATC-IN in

end
write memory
```

#### R4-RN y R5-RN - ACL-VENTAS-IN

```cisco
enable
configure terminal

ip access-list extended ACL-VENTAS-IN
 remark DHCP local
 permit udp any eq bootpc any eq bootps
 remark DNS al servidor central
 permit udp any host 172.16.11.66 eq domain
 permit tcp any host 172.16.11.66 eq domain
 remark HTTP al servidor central
 permit tcp any host 172.16.11.66 eq 80
 remark Permitir respuestas de ping
 permit icmp any any echo-reply
 remark Ventas solo puede iniciar hacia Facturacion y ATC
 permit icmp 172.16.21.0 0.0.0.31 172.16.21.32 0.0.0.31 echo
 permit icmp 172.16.21.0 0.0.0.31 172.16.11.32 0.0.0.31 echo
 deny ip any any

interface GigabitEthernet0/1.10
 ip access-group ACL-VENTAS-IN in

end
write memory
```

#### R4-RN y R5-RN - ACL-FACT-IN

```cisco
enable
configure terminal

ip access-list extended ACL-FACT-IN
 remark DHCP relay
 permit udp any eq bootpc any eq bootps
 remark DNS al servidor central
 permit udp any host 172.16.11.66 eq domain
 permit tcp any host 172.16.11.66 eq domain
 remark HTTP al servidor central
 permit tcp any host 172.16.11.66 eq 80
 remark Permitir respuestas de ping
 permit icmp any any echo-reply
 remark Facturacion solo puede iniciar hacia Ventas
 permit icmp 172.16.21.32 0.0.0.31 172.16.21.0 0.0.0.31 echo
 deny ip any any

interface GigabitEthernet0/2
 ip access-group ACL-FACT-IN in

end
write memory
```

#### R2-CF - ACL-SOP-IN

```cisco
enable
configure terminal

ip access-list extended ACL-SOP-IN
 remark DHCP relay
 permit udp any eq bootpc any eq bootps
 remark DNS al servidor central
 permit udp any host 172.16.11.66 eq domain
 permit tcp any host 172.16.11.66 eq domain
 remark HTTP al servidor central
 permit tcp any host 172.16.11.66 eq 80
 remark Permitir respuestas de ping
 permit icmp any any echo-reply
 remark Soporte no puede iniciar hacia Seguridad
 deny icmp 172.16.32.0 0.0.0.31 172.16.32.32 0.0.0.31 echo
 remark Soporte si puede iniciar hacia los demas
 permit icmp 172.16.32.0 0.0.0.31 172.16.11.0 0.0.0.31 echo
 permit icmp 172.16.32.0 0.0.0.31 172.16.11.32 0.0.0.31 echo
 permit icmp 172.16.32.0 0.0.0.31 172.16.21.0 0.0.0.31 echo
 permit icmp 172.16.32.0 0.0.0.31 172.16.21.32 0.0.0.31 echo
 deny ip any any

interface GigabitEthernet0/1
 ip access-group ACL-SOP-IN in

end
write memory
```

#### R3-CF - ACL-SEG-IN

```cisco
enable
configure terminal

ip access-list extended ACL-SEG-IN
 remark DHCP relay
 permit udp any eq bootpc any eq bootps
 remark DNS al servidor central
 permit udp any host 172.16.11.66 eq domain
 permit tcp any host 172.16.11.66 eq domain
 remark HTTP al servidor central
 permit tcp any host 172.16.11.66 eq 80
 remark Permitir respuestas de ping
 permit icmp any any echo-reply
 remark Seguridad puede iniciar hacia todos
 permit icmp 172.16.32.32 0.0.0.31 172.16.11.0 0.0.0.31 echo
 permit icmp 172.16.32.32 0.0.0.31 172.16.11.32 0.0.0.31 echo
 permit icmp 172.16.32.32 0.0.0.31 172.16.21.0 0.0.0.31 echo
 permit icmp 172.16.32.32 0.0.0.31 172.16.21.32 0.0.0.31 echo
 permit icmp 172.16.32.32 0.0.0.31 172.16.32.0 0.0.0.31 echo
 deny ip any any

interface GigabitEthernet0/1
 ip access-group ACL-SEG-IN in

end
write memory
```

## Validaciones Finales

### Fase 2

#### CORE

```cisco
show ip interface brief
show interfaces gigabitEthernet1/1/1 switchport
show interfaces gigabitEthernet1/1/2 switchport
show interfaces gigabitEthernet1/0/1 switchport
ping 192.168.31.1
ping 192.168.31.2
ping 192.168.31.5
ping 192.168.31.6
ping 192.168.31.9
ping 192.168.31.10
```

#### ISP1

```cisco
show ip ospf neighbor
show ip route ospf
show ip interface brief
show etherchannel summary
```

#### ISP2

```cisco
show ip ospf neighbor
show standby brief
show ip route ospf
show ip interface brief
show etherchannel summary
show vlan brief
show interfaces trunk
```

#### ISP3

```cisco
show ip eigrp neighbors
show ip route eigrp
show ip interface brief
show etherchannel summary
```

### Fase 3

#### BGP

```cisco
show ip bgp summary
show ip bgp
show ip route
```

#### Pruebas Entre ISP

```cisco
ping 172.16.21.66
ping 172.16.11.66
ping 172.16.32.81
ping 172.16.32.33
```

### Fase 4

#### Revision De ACL

```cisco
show access-lists
show running-config interface gigabitEthernet0/1
show running-config interface gigabitEthernet0/2
show standby brief
```

#### Pruebas Permitidas

- `PC-SEG -> PC-SOP` responde.
- `PC-SEG -> PC-ADM` responde.
- `PC-SEG -> PC-VENTAS` responde.
- `PC-SEG -> PC-FACT` responde.
- `PC-SEG -> PC-ATC` responde.
- `PC-SOP -> PC-ADM` responde.
- `PC-SOP -> PC-VENTAS` responde.
- `PC-SOP -> PC-FACT` responde.
- `PC-SOP -> PC-ATC` responde.
- `PC-ADM -> PC-SOP` responde.
- `PC-ADM -> PC-VENTAS` responde.
- `PC-ADM -> PC-FACT` responde.
- `PC-ADM -> PC-ATC` responde.
- `PC-ATC -> PC-VENTAS` responde.
- `PC-FACT -> PC-VENTAS` responde.
- `PC-VENTAS -> PC-ATC` responde.
- `PC-VENTAS -> PC-FACT` responde.

#### Pruebas Denegadas

- `PC-SOP -> PC-SEG` falla.
- `PC-ADM -> PC-SEG` falla.
- `PC-VENTAS -> PC-SEG` falla.
- `PC-FACT -> PC-SEG` falla.
- `PC-ATC -> PC-SEG` falla.
- `PC-ATC -> PC-ADM` falla.
- `PC-ATC -> PC-SOP` falla.
- `PC-ATC -> PC-SEG` falla.
- `PC-ATC -> PC-FACT` falla.
- `PC-FACT -> PC-ADM` falla.
- `PC-FACT -> PC-SOP` falla.
- `PC-FACT -> PC-SEG` falla.
- `PC-FACT -> PC-ATC` falla.
- `PC-VENTAS -> PC-ADM` falla.
- `PC-VENTAS -> PC-SOP` falla.
- `PC-VENTAS -> PC-SEG` falla.

## Evidencia

### Evidencia 1 - Topologia General

![alt text](img/image.png)

#### CENTRO

![alt text](img/image-1.png)

#### ISP1

![alt text](img/image-2.png)

#### ISP2

![alt text](img/image-3.png)

#### ISP3

![alt text](img/image-4.png)


### Evidencia 2 - Vecinos BGP-ISP

Comando: `show ip bgp summary`

![alt text](img/image-5.png)

![alt text](img/image-6.png)

### Evidencia 3 - Tabla De Rutas Inter-ISP

Comando: `show ip route`

![alt text](img/image-7.png)

![alt text](img/image-8.png)

### Evidencia 4 - HSRP Funcionando, R4-RN Y R5RN

Comando: `show standby brief`

![alt text](img/image-9.png)

### Evidencia 5 - LACP Funcionando

Comando: `show etherchannel summary`

#### SW-ADM-DIST Y SW-ATC-DIST

![alt text](img/image-10.png)

#### SW-VENTAS-DIST Y SW-FACT-DIST

![alt text](img/image-11.png)

#### SW-SOP-DIST Y SW-SEG-DIST

![alt text](img/image-12.png)

### Evidencia 6 - DHCP En Un Cliente

IP, mascara, gateway y DNS obtenidos por DHCP.

![alt text](img/image-25.png)

#### PC-ADM-1 Y PC-ATC-2

![alt text](img/image-13.png)


#### PC-VENTAS-2 Y PC-FACT-1

![alt text](img/image-14.png)

#### PC-SOP-3 Y PC-SWG-3

![alt text](img/image-15.png)

### Evidencia 7 - DNS Funcionando

![alt text](img/image-24.png)
![alt text](img/image-13.png)

### Evidencia 8 - HTTP Funcionando

Mostrar el navegador con `http://www.proyecto2_202300431.com`.

![alt text](img/image-16.png)

### Evidencia 9 - WiFi Funcionando

Mostrar asociacion de la laptop al SSID `ISP3_WIFI` y su direccionamiento.

![alt text](img/image-17.png)
![alt text](img/image-18.png)


### Evidencia 10 - ACL Permitida

Prueba de ping permitida

![alt text](img/image-22.png)
![alt text](img/image-20.png)

### Evidencia 11 - ACL Denegada

Mostrar una prueba de ping denegada

![alt text](img/image-21.png)
![alt text](img/image-23.png)

## Conclusiones

- El proyecto cumple con la interconexion de tres ISP mediante `BGP`.
- ISP1 implementa `OSPF`, `DNS`, `HTTP` y enlaces `LACP`.
- ISP2 implementa `OSPF`, `DHCP`, `HSRP` y enlaces `LACP`.
- ISP3 implementa `EIGRP`, `WiFi` y enlaces `LACP`.
- La matriz de comunicacion se implemento por medio de `ACLs` sin afectar `DHCP`, `DNS` ni `HTTP`.
- El modelo sencillo de `BGP` fue suficiente para cumplir el enunciado y mantener la solucion estable en Packet Tracer.
