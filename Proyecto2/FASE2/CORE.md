# FASE 2 - CORE

## Objetivo

Preparar los equipos frontera `ICP1`, `ICP2` y `ICP3` con hostname, `ip routing`, direccionamiento base y conectividad fisica hacia cada ISP y entre ICPs. En esta fase no se configura BGP todavia.

## Orden De Trabajo

1. Configurar `ICP1`.
2. Configurar `ICP2`.
3. Configurar `ICP3`.
4. Verificar `show ip interface brief`.
5. Probar `ping` entre ICPs por las redes `192.168.31.x`.
6. Probar `ping` desde cada ICP al router borde de su ISP.

## Nota Importante

En los multilayer switches `ICP1`, `ICP2` y `ICP3`, las interfaces fisicas usadas para enlaces L3 deben convertirse primero a puertos enrutados con `no switchport`. Si omites ese paso, el comando `ip address` dara error.

## Direccionamiento Usado En Esta Fase

### Enlaces inter-ISP

| Enlace | IP lado A | IP lado B |
| --- | --- | --- |
| ICP1 - ICP2 | 192.168.31.1/30 | 192.168.31.2/30 |
| ICP2 - ICP3 | 192.168.31.5/30 | 192.168.31.6/30 |
| ICP3 - ICP1 | 192.168.31.9/30 | 192.168.31.10/30 |

### Enlaces ICP hacia cada ISP

| Enlace | Red | IP ICP | IP router ISP |
| --- | --- | --- | --- |
| ICP1 - R1-TU | 172.16.11.96/30 | 172.16.11.97 | 172.16.11.98 |
| ICP2 - R1-RN | 172.16.21.96/30 | 172.16.21.97 | 172.16.21.98 |
| ICP3 - R1-CF | 172.16.32.112/30 | 172.16.32.113 | 172.16.32.114 |

## Dispositivo: ICP1

```cisco
enable
configure terminal
hostname ICP1
no ip domain-lookup
ip routing

interface GigabitEthernet1/1/1
 description Hacia ICP2
 no switchport
 ip address 192.168.31.1 255.255.255.252
 no shutdown

interface GigabitEthernet1/1/2
 description Hacia ICP3
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

## Dispositivo: ICP2

```cisco
enable
configure terminal
hostname ICP2
no ip domain-lookup
ip routing

interface GigabitEthernet1/1/2
 description Hacia ICP1
 no switchport
 ip address 192.168.31.2 255.255.255.252
 no shutdown

interface GigabitEthernet1/1/1
 description Hacia ICP3
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

## Dispositivo: ICP3

```cisco
enable
configure terminal
hostname ICP3
no ip domain-lookup
ip routing

interface GigabitEthernet1/1/2
 description Hacia ICP2
 no switchport
 ip address 192.168.31.6 255.255.255.252
 no shutdown

interface GigabitEthernet1/1/1
 description Hacia ICP1
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

## Verificacion

En cada ICP:

```cisco
show ip interface brief
show interfaces gigabitEthernet1/1/1 switchport
show interfaces gigabitEthernet1/1/2 switchport
show interfaces gigabitEthernet1/0/1 switchport
show running-config interface gigabitEthernet1/1/1
show running-config interface gigabitEthernet1/1/2
show running-config interface gigabitEthernet1/0/1
```

Pruebas esperadas:

```cisco
ping 192.168.31.2
ping 192.168.31.6
ping 172.16.11.98
ping 172.16.21.98
ping 172.16.32.114
```

## Resultado Esperado

- Los tres ICP deben tener sus interfaces `up/up`.
- Las interfaces usadas en el core deben aparecer como routed ports, no como switchport de capa 2.
- Debe haber conectividad L3 entre ICPs por los enlaces `192.168.31.x`.
- Cada ICP debe poder alcanzar al router borde de su ISP.
