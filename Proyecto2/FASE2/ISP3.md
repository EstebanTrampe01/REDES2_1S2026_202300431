# FASE 2 - ISP3

## Objetivo

Dejar operativo `ISP3 Conexiones Futuras` con `EIGRP`, `LACP`, red WiFi y `ip helper-address` hacia el servidor DHCP central.

## Nota Importante

Para que el DHCP central funcione con `WRT300N`, lo mas estable es usar el equipo como punto de acceso:

- conecta `R4-CF` a un puerto LAN del `WRT300N`
- desactiva el DHCP del `WRT300N`
- usa la red `172.16.32.80/28` para los clientes WiFi

Si usas el puerto `Internet`, el equipo tiende a hacer NAT y rompe el objetivo de DHCP centralizado.

## Orden De Trabajo

1. Configurar `R1-CF`.
2. Configurar `R2-CF`.
3. Configurar `R3-CF`.
4. Configurar `R4-CF`.
5. Configurar `R5-CF`.
6. Configurar `SW-HUB-CF`.
7. Configurar switches de Soporte.
8. Configurar switches de Seguridad.
9. Configurar `WRT300N`.
10. Poner clientes cableados y WiFi en DHCP.
11. Verificar `EIGRP`, `LACP` y conectividad interna.

## Direccionamiento Base

| Enlace o segmento | IP principal |
| --- | --- |
| ICP3 - R1-CF | R1-CF `172.16.32.114/30` |
| Backbone Hub | R1 `172.16.32.65`, R2 `172.16.32.66`, R3 `172.16.32.67`, R4 `172.16.32.68`, R5 `172.16.32.69` |
| Soporte | R2-CF `172.16.32.1/27` |
| Seguridad | R3-CF `172.16.32.33/27` |
| WiFi | R4-CF `172.16.32.81/28`, WRT `172.16.32.82/28` |

## Dispositivo: R1-CF

```cisco
enable
configure terminal
hostname R1-CF
no ip domain-lookup

interface GigabitEthernet0/0
 description Hacia ICP3
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

## Dispositivo: R2-CF

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

## Dispositivo: R3-CF

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

## Dispositivo: R4-CF

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

## Dispositivo: R5-CF

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

## Dispositivo: SW-HUB-CF

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

## Dispositivo: SW-SOP-DIST

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

## Dispositivo: SW-SOP-ACC

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

## Dispositivo: SW-SEG-DIST

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

## Dispositivo: SW-SEG-ACC

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

## Dispositivo: WRT300N

Configuralo como punto de acceso:

1. Conecta `R4-CF` a un puerto LAN del `WRT300N`.
2. En GUI > Setup:
   - Local IP Address: `172.16.32.82`
   - Subnet Mask: `255.255.255.240`
   - Default Gateway: `172.16.32.81`
   - DHCP Server: `Disable`
3. En GUI > Wireless:
   - SSID: `ISP3_WIFI`
4. En GUI > Wireless Security:
   - Security Mode: `WPA2 Personal`
   - Passphrase: `20-JE-04`

## Clientes De ISP3

- PCs de Soporte y Seguridad en `DHCP`.
- Laptop WiFi conectada al SSID `ISP3_WIFI` y en `DHCP`.

## Verificacion

En routers:

```cisco
show ip interface brief
show ip eigrp neighbors
show ip route eigrp
```

En switches:

```cisco
show etherchannel summary
```

Pruebas esperadas:

```cisco
ping 172.16.32.66
ping 172.16.32.67
ping 172.16.32.68
ping 172.16.32.69
```

En clientes:

- Deben recibir IP por DHCP.
- La laptop debe asociarse al SSID correcto.

## Resultado Esperado

- `EIGRP` debe formar vecinos sobre la red `172.16.32.64/28`.
- Los dos `Port-channel` de ISP3 deben levantar.
- La red WiFi debe quedar dentro de `172.16.32.80/28` sin NAT intermedio.
