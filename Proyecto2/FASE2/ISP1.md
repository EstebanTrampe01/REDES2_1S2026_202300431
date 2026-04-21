# FASE 2 - ISP1

## Objetivo

Dejar operativo `ISP1 Telecom Uno` con `OSPF`, gateways de usuarios, `LACP`, `ip helper-address` y el servidor `DNS/HTTP`.

## Orden De Trabajo

1. Configurar `R1-TU`.
2. Configurar `R2-TU`.
3. Configurar `R3-TU`.
4. Configurar `R4-TU`.
5. Configurar `R5-TU`.
6. Configurar switches de Administracion.
7. Configurar switches de Atencion al Cliente.
8. Configurar `SRV-DNS-HTTP`.
9. Poner PCs en DHCP.
10. Verificar vecinos OSPF, `LACP`, DHCP y acceso al servidor.

## Direccionamiento Base

| Enlace o segmento | IP principal |
| --- | --- |
| ICP1 - R1-TU | R1-TU `172.16.11.98/30` |
| R1-TU - R2-TU | R1 `172.16.11.81/30`, R2 `172.16.11.82/30` |
| R1-TU - R3-TU | R1 `172.16.11.85/30`, R3 `172.16.11.86/30` |
| R2-TU - R4-TU | R2 `172.16.11.89/30`, R4 `172.16.11.90/30` |
| R3-TU - R5-TU | R3 `172.16.11.93/30`, R5 `172.16.11.94/30` |
| Administracion | R4-TU `172.16.11.1/27` |
| Atencion al Cliente | R5-TU `172.16.11.33/27` |
| Servidores | R4-TU `172.16.11.65/28`, SRV `172.16.11.66/28` |

## Dispositivo: R1-TU

```cisco
enable
configure terminal
hostname R1-TU
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia ICP1
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

## Dispositivo: R2-TU

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

## Dispositivo: R3-TU

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

## Dispositivo: R4-TU

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

## Dispositivo: R5-TU

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

## Dispositivo: SW-ADM-DIST

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

## Dispositivo: SW-ADM-ACC

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

## Dispositivo: SW-ATC-DIST

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

## Dispositivo: SW-ATC-ACC

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

## Dispositivo: SRV-DNS-HTTP

Configura en la pestana Desktop > IP Configuration:

- IP: `172.16.11.66`
- Mask: `255.255.255.240`
- Default Gateway: `172.16.11.65`
- DNS Server: `172.16.11.66`

Configura en Services > DNS:

- DNS: `On`
- Name: `www.proyecto2_202300431.com`
- Type: `A Record`
- Address: `172.16.11.66`

Configura en Services > HTTP:

- HTTP: `On`
- Edita `index.html` con tu nombre, carne y datos del curso.

## PCs De ISP1

- En cada PC de Administracion y Atencion al Cliente, selecciona `DHCP` en Desktop > IP Configuration.

## Verificacion

En routers:

```cisco
show ip interface brief
show ip ospf neighbor
show ip route ospf
```

En switches:

```cisco
show etherchannel summary
show interfaces port-channel 1
show interfaces port-channel 2
```

Pruebas esperadas:

```cisco
ping 172.16.11.90
ping 172.16.11.94
ping 172.16.11.66
```

En PCs:

- Deben recibir IP por DHCP.
- Deben resolver `www.proyecto2_202300431.com` cuando la ruta al DNS ya exista.

## Resultado Esperado

- `OSPF` debe formar vecinos entre `ICP1`, `R1-TU`, `R2-TU`, `R3-TU`, `R4-TU` y `R5-TU` segun enlaces directos.
- Los dos `Port-channel` de ISP1 deben quedar `SU` o `P` en `show etherchannel summary`.
- El servidor DNS/HTTP debe responder por IP local.
