# FASE 2 - ISP2

## Objetivo

Dejar operativo `ISP2 Redes Nacionales` con `OSPF`, `HSRP`, `LACP` y `DHCP` central para toda la topologia.

## Nota Importante

Para que el servidor DHCP en la red `172.16.21.64/28` funcione correctamente, se hara un pequeno ajuste practico:

- `SRV-DHCP` usara la IP `172.16.21.66/28`.
- La puerta de enlace de esa red sera `172.16.21.65`.
- `R4-RN` y `R5-RN` usaran subinterfaces en el enlace hacia `SW-VENTAS-DIST` para atender tanto `Ventas` como la red del servidor DHCP.

Con esto mantienes la subred propia del servidor y evitas dejarlo sin gateway.

## Orden De Trabajo

1. Configurar `R1-RN`.
2. Configurar `R2-RN`.
3. Configurar `R3-RN`.
4. Configurar `R4-RN`.
5. Configurar `R5-RN`.
6. Configurar `SW-VENTAS-DIST`.
7. Configurar `SW-VENTAS-ACC`.
8. Configurar `SW-FACT-DIST`.
9. Configurar `SW-FACT-ACC`.
10. Configurar `SRV-DHCP`.
11. Poner todos los clientes en DHCP.
12. Verificar `OSPF`, `HSRP`, `LACP` y asignacion IP.

## Direccionamiento Base

| Enlace o segmento | IP principal |
| --- | --- |
| ICP2 - R1-RN | R1-RN `172.16.21.98/30` |
| R1-RN - R2-RN | R1 `172.16.21.81/30`, R2 `172.16.21.82/30` |
| R1-RN - R3-RN | R1 `172.16.21.85/30`, R3 `172.16.21.86/30` |
| R2-RN - R4-RN | R2 `172.16.21.89/30`, R4 `172.16.21.90/30` |
| R3-RN - R5-RN | R3 `172.16.21.93/30`, R5 `172.16.21.94/30` |
| Ventas VIP | `172.16.21.1/27` |
| Facturacion VIP | `172.16.21.33/27` |
| DHCP gateway | `172.16.21.65/28` |
| DHCP server | `172.16.21.66/28` |

## Dispositivo: R1-RN

```cisco
enable
configure terminal
hostname R1-RN
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia ICP2
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

## Dispositivo: R2-RN

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

## Dispositivo: R3-RN

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

## Dispositivo: R4-RN

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

## Dispositivo: R5-RN

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

## Dispositivo: SW-VENTAS-DIST

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

## Dispositivo: SW-VENTAS-ACC

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

## Dispositivo: SW-FACT-DIST

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

## Dispositivo: SW-FACT-ACC

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

## Dispositivo: SRV-DHCP

Configura en Desktop > IP Configuration:

- IP: `172.16.21.66`
- Mask: `255.255.255.240`
- Default Gateway: `172.16.21.65`
- DNS Server: `172.16.11.66`

Configura en Services > DHCP los pools siguientes:

| Pool | Default Gateway | DNS | Start IP | Subnet Mask |
| --- | --- | --- | --- | --- |
| POOL-ADM | 172.16.11.1 | 172.16.11.66 | 172.16.11.2 | 255.255.255.224 |
| POOL-ATC | 172.16.11.33 | 172.16.11.66 | 172.16.11.34 | 255.255.255.224 |
| POOL-VENTAS | 172.16.21.1 | 172.16.11.66 | 172.16.21.4 | 255.255.255.224 |
| POOL-FACT | 172.16.21.33 | 172.16.11.66 | 172.16.21.36 | 255.255.255.224 |
| POOL-SOP | 172.16.32.1 | 172.16.11.66 | 172.16.32.2 | 255.255.255.224 |
| POOL-SEG | 172.16.32.33 | 172.16.11.66 | 172.16.32.34 | 255.255.255.224 |
| POOL-WIFI | 172.16.32.81 | 172.16.11.66 | 172.16.32.83 | 255.255.255.240 |

## PCs De ISP2

- En cada PC de Ventas y Facturacion, selecciona `DHCP` en Desktop > IP Configuration.

## Verificacion

En routers:

```cisco
show ip interface brief
show ip ospf neighbor
show standby brief
show ip route ospf
```

En switches:

```cisco
show etherchannel summary
show vlan brief
show interfaces trunk
```

Pruebas esperadas:

```cisco
ping 172.16.21.66
ping 172.16.21.1
ping 172.16.21.33
```

En clientes:

- Deben recibir IP por DHCP.
- El gateway debe ser la VIP correcta.

## Resultado Esperado

- `OSPF` debe levantar sobre los enlaces router-router de ISP2.
- `HSRP` debe mostrar un router `Active` y otro `Standby` para Ventas y Facturacion.
- Los `Port-channel` deben quedar levantados.
- El servidor DHCP debe poder responder a redes remotas cuando existan rutas.
