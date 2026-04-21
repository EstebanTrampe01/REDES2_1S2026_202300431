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


