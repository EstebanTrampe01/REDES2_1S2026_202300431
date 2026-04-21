# Proyecto 2 - Fase 1

**Carne:** `202300431`

## Alcance

Este documento consolida la Fase 1 del proyecto: analisis de requisitos por ISP, subneteo, diseno de topologias, definicion del borde inter-ISP y reglas de comunicacion. La configuracion CLI se documentara con mayor detalle en fases posteriores.

## Requisitos Por ISP

| ISP | Red base | Topologia | Protocolo interno | Equipo frontera | Servicio principal | Requisitos especiales |
| --- | --- | --- | --- | --- | --- | --- |
| ISP1 Telecom Uno | 172.16.11.0/24 | Arbol | OSPF | ICP1 | DNS y HTTP | 5 routers, 5 hosts, 2 enlaces LACP |
| ISP2 Redes Nacionales | 172.16.21.0/24 | Jerarquica | OSPF | ICP2 | DHCP | 5 routers, 5 hosts, 2 enlaces LACP, HSRP obligatorio |
| ISP3 Conexiones Futuras | 172.16.32.0/24 | Hub and Spoke | EIGRP | ICP3 | WiFi | 5 routers, 5 hosts, 2 enlaces LACP, router inalambrico |

## Servicios Centrales

| Servicio | Equipo | IP | Mascara | Gateway |
| --- | --- | --- | --- | --- |
| DNS y HTTP | SRV-DNS-HTTP | 172.16.11.66 | 255.255.255.240 | 172.16.11.65 |
| DHCP | SRV-DHCP | 172.16.21.65 | 255.255.255.240 | 172.16.21.65 |

El dominio solicitado por el enunciado es `www.proyecto2_202300431.com`.

## Diseno Inter-ISP

La interconexion nacional utiliza tres equipos frontera en topologia triangular. Cada ISP mantiene su protocolo interno y anuncia su bloque principal al resto de ISPs por medio de BGP.

| ISP | Equipo frontera | AS propuesto | Aprende rutas internas con |
| --- | --- | --- | --- |
| ISP1 | ICP1 | 100 | OSPF area 0 |
| ISP2 | ICP2 | 200 | OSPF area 0 |
| ISP3 | ICP3 | 300 | EIGRP AS 1 |

### Enlaces Inter-ISP

| Enlace | Red | Mascara | IP lado A | IP lado B | Broadcast |
| --- | --- | --- | --- | --- | --- |
| ICP1 - ICP2 | 192.168.31.0/30 | 255.255.255.252 | 192.168.31.1 | 192.168.31.2 | 192.168.31.3 |
| ICP2 - ICP3 | 192.168.31.4/30 | 255.255.255.252 | 192.168.31.5 | 192.168.31.6 | 192.168.31.7 |
| ICP3 - ICP1 | 192.168.31.8/30 | 255.255.255.252 | 192.168.31.9 | 192.168.31.10 | 192.168.31.11 |

### Criterio De Borde

- `ICP1` sera la frontera entre el dominio OSPF de ISP1 y el AS 100.
- `ICP2` sera la frontera entre el dominio OSPF de ISP2 y el AS 200.
- `ICP3` sera la frontera entre el dominio EIGRP de ISP3 y el AS 300.
- El objetivo de esta separacion es mantener claro el limite entre el enrutamiento interno del ISP y el intercambio de rutas entre proveedores.

## Subneteo ISP1 - Telecom Uno

ISP1 utiliza topologia en arbol, OSPF y aloja los servicios DNS y HTTP para toda la topologia.

| Segmento | Red | Mascara | Primer host | Ultimo host | Broadcast | Gateway sugerido | Proposito |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Administracion | 172.16.11.0/27 | 255.255.255.224 | 172.16.11.1 | 172.16.11.30 | 172.16.11.31 | 172.16.11.1 | Hosts del departamento |
| Atencion al Cliente | 172.16.11.32/27 | 255.255.255.224 | 172.16.11.33 | 172.16.11.62 | 172.16.11.63 | 172.16.11.33 | Hosts del departamento |
| Servidores DNS/HTTP | 172.16.11.64/28 | 255.255.255.240 | 172.16.11.65 | 172.16.11.78 | 172.16.11.79 | 172.16.11.65 | Servicios centrales |
| Enlace R1-R2 | 172.16.11.80/30 | 255.255.255.252 | 172.16.11.81 | 172.16.11.82 | 172.16.11.83 | - | Punto a punto |
| Enlace R1-R3 | 172.16.11.84/30 | 255.255.255.252 | 172.16.11.85 | 172.16.11.86 | 172.16.11.87 | - | Punto a punto |
| Enlace R2-R4 | 172.16.11.88/30 | 255.255.255.252 | 172.16.11.89 | 172.16.11.90 | 172.16.11.91 | - | Punto a punto |
| Enlace R3-R5 | 172.16.11.92/30 | 255.255.255.252 | 172.16.11.93 | 172.16.11.94 | 172.16.11.95 | - | Punto a punto |

## Subneteo ISP2 - Redes Nacionales

ISP2 utiliza topologia jerarquica, OSPF, HSRP obligatorio y centraliza el servicio DHCP para toda la topologia.

| Segmento | Red | Mascara | Primer host | Ultimo host | Broadcast | Gateway sugerido | Proposito |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Ventas | 172.16.21.0/27 | 255.255.255.224 | 172.16.21.1 | 172.16.21.30 | 172.16.21.31 | HSRP VIP 172.16.21.1 | Hosts del departamento |
| Facturacion | 172.16.21.32/27 | 255.255.255.224 | 172.16.21.33 | 172.16.21.62 | 172.16.21.63 | HSRP VIP 172.16.21.33 | Hosts del departamento |
| Servidor DHCP | 172.16.21.64/28 | 255.255.255.240 | 172.16.21.65 | 172.16.21.78 | 172.16.21.79 | 172.16.21.65 | Servicio central |
| Enlace R1-R2 | 172.16.21.80/30 | 255.255.255.252 | 172.16.21.81 | 172.16.21.82 | 172.16.21.83 | - | Punto a punto |
| Enlace R1-R3 | 172.16.21.84/30 | 255.255.255.252 | 172.16.21.85 | 172.16.21.86 | 172.16.21.87 | - | Punto a punto |
| Enlace R2-R4 | 172.16.21.88/30 | 255.255.255.252 | 172.16.21.89 | 172.16.21.90 | 172.16.21.91 | - | Punto a punto |
| Enlace R3-R5 | 172.16.21.92/30 | 255.255.255.252 | 172.16.21.93 | 172.16.21.94 | 172.16.21.95 | - | Punto a punto |

### Criterio De HSRP En ISP2

- La VIP de `Ventas` sera `172.16.21.1`.
- La VIP de `Facturacion` sera `172.16.21.33`.
- Las IP fisicas recomendadas para los routers seran:
  - `R4-RN`: `172.16.21.2` y `172.16.21.34`
  - `R5-RN`: `172.16.21.3` y `172.16.21.35`

## Subneteo ISP3 - Conexiones Futuras

ISP3 utiliza topologia hub and spoke, EIGRP y un router inalambrico para la red WiFi.

| Segmento | Red | Mascara | Primer host | Ultimo host | Broadcast | Gateway sugerido | Proposito |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Soporte | 172.16.32.0/27 | 255.255.255.224 | 172.16.32.1 | 172.16.32.30 | 172.16.32.31 | 172.16.32.1 | Hosts del departamento |
| Seguridad | 172.16.32.32/27 | 255.255.255.224 | 172.16.32.33 | 172.16.32.62 | 172.16.32.63 | 172.16.32.33 | Hosts del departamento |
| Backbone Hub | 172.16.32.64/28 | 255.255.255.240 | 172.16.32.65 | 172.16.32.78 | 172.16.32.79 | - | Red central del hub |
| WiFi Clientes | 172.16.32.80/28 | 255.255.255.240 | 172.16.32.81 | 172.16.32.94 | 172.16.32.95 | 172.16.32.81 | Red inalambrica |
| Reserva / Expansion | 172.16.32.96/28 | 255.255.255.240 | 172.16.32.97 | 172.16.32.110 | 172.16.32.111 | 172.16.32.97 | Crecimiento futuro |

### Justificacion De La Topologia Hub And Spoke

El diseno de ISP3 concentra la comunicacion interna en `SW-HUB-CF`, que actua como punto central del dominio. Desde ese nodo salen los spokes hacia `R2-CF`, `R3-CF`, `R4-CF` y `R5-CF`, lo que permite centralizar el trafico, simplificar el crecimiento y agregar el servicio WiFi sin alterar la estructura principal.

## Reglas De Comunicacion

La siguiente matriz consolida la politica final de trafico segun el enunciado. Esta sera la referencia para aplicar ACLs en Fase 2.

| Origen \ Destino | Seguridad | Soporte | Administracion | Atencion al Cliente | Facturacion | Ventas |
| --- | --- | --- | --- | --- | --- | --- |
| Seguridad | - | Si | Si | Si | Si | Si |
| Soporte | No | - | Si | Si | Si | Si |
| Administracion | No | Si | - | Si | Si | Si |
| Atencion al Cliente | No | No | No | - | No | Si |
| Facturacion | No | No | No | No | - | Si |
| Ventas | No | No | No | Si | Si | - |

## Ubicacion Prevista De ACLs

- Las ACLs se aplicaran en las interfaces de gateway de cada departamento.
- Esto permite controlar el trafico cerca del origen y facilita la validacion de las reglas de comunicacion.
- Los puntos principales de control seran los gateways de `Administracion`, `Atencion al Cliente`, `Ventas`, `Facturacion`, `Soporte`, `Seguridad` y `WiFi`.

## LACP Y Cantidad Minima De Equipos

- Cada ISP contempla al menos `2 enlaces LACP` entre switches de distribucion y acceso.
- Cada ISP mantiene un minimo de `5 routers`.
- La cantidad total de hosts finales por ISP supera el minimo requerido cuando se consideran PCs, servidores y cliente WiFi.

## Justificacion Del Subneteo

- Las subredes `/27` se usaron para departamentos porque ofrecen 30 hosts utiles, suficientes para el laboratorio y con margen de crecimiento.
- Las subredes `/28` se reservaron para servicios, WiFi y segmentos pequenos donde 14 hosts utiles son suficientes.
- Las subredes `/30` se eligieron para enlaces punto a punto porque solo requieren dos direcciones utiles y evitan desperdicio de espacio.

## Resumen De Fase 1

Con esta documentacion queda definido:

- el analisis de requisitos por ISP
- el subneteo completo de las redes base asignadas
- la topologia logica de cada proveedor
- la frontera entre IGP y BGP
- la matriz final de comunicacion para la futura implementacion de ACLs
