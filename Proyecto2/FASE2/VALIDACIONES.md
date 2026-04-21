# FASE 2 - VALIDACIONES

## Orden Recomendado De Pruebas

1. Validar interfaces en `CORE`.
2. Validar routing interno de `ISP1`.
3. Validar routing interno de `ISP2`.
4. Validar routing interno de `ISP3`.
5. Validar `LACP` en los tres ISP.
6. Validar `HSRP` en ISP2.
7. Validar `DHCP` en clientes cableados y WiFi.
8. Validar `DNS/HTTP` por IP local y, cuando exista ruta, por nombre.

## CORE

En `ICP1`, `ICP2` y `ICP3`:

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

Resultado esperado:

- Interfaces `up/up`.
- Las interfaces del core deben indicar `Switchport: Disabled`.
- Pings exitosos entre ICPs por enlaces directos.

## ISP1

En routers de ISP1:

```cisco
show ip ospf neighbor
show ip route ospf
show ip interface brief
```

En switches de ISP1:

```cisco
show etherchannel summary
```

Pruebas esperadas:

- `R1-TU` ve vecinos OSPF.
- `R4-TU` y `R5-TU` tienen gateways activos.
- El servidor `SRV-DNS-HTTP` responde a `ping 172.16.11.66` desde `R4-TU`.

## ISP2

En routers de ISP2:

```cisco
show ip ospf neighbor
show standby brief
show ip route ospf
show ip interface brief
```

En switches de ISP2:

```cisco
show etherchannel summary
show interfaces trunk
show vlan brief
```

Pruebas esperadas:

- `show standby brief` muestra `Active` y `Standby`.
- `SRV-DHCP` responde por `172.16.21.66`.
- PCs de Ventas y Facturacion obtienen IP por DHCP.

## ISP3

En routers de ISP3:

```cisco
show ip eigrp neighbors
show ip route eigrp
show ip interface brief
```

En switches de ISP3:

```cisco
show etherchannel summary
```

Pruebas esperadas:

- `R1-CF` ve como vecinos a `R2-CF`, `R3-CF`, `R4-CF` y `R5-CF` por la red del hub.
- PCs de Soporte y Seguridad obtienen IP por DHCP.
- La laptop WiFi recibe IP y se asocia al SSID `ISP3_WIFI`.

## Pruebas De DHCP

En cada PC o laptop:

1. Ir a Desktop > IP Configuration.
2. Seleccionar `DHCP`.
3. Verificar que reciba:
   - IP valida del rango correcto
   - mascara correcta
   - gateway correcto
   - DNS `172.16.11.66`

## Pruebas De DNS Y HTTP

Antes de BGP puedes validar localmente:

1. Desde `SRV-DNS-HTTP`, confirma que DNS y HTTP esten `On`.
2. Desde un host que ya tenga ruta al servidor, prueba:

```text
ping 172.16.11.66
```

3. Luego abre Browser y entra a:

```text
http://www.proyecto2_202300431.com
```

## Criterio Para Cerrar Fase 2

La Fase 2 se puede dar por completa cuando:

- las interfaces base del core esten arriba
- `OSPF` funcione en ISP1 e ISP2
- `EIGRP` funcione en ISP3
- `LACP` funcione en los tres ISP
- `HSRP` funcione en ISP2
- los clientes obtengan direccionamiento por DHCP
- el servidor DNS/HTTP quede listo para pruebas de Fase 3
