# FASE 3 - ISP2 SALIDA

## Objetivo

Dar salida al resto del pais desde `ISP2` apuntando al `ICP2`, y propagar esa salida dentro de `OSPF` para que todo el dominio interno alcance las redes remotas aprendidas por BGP.

## Orden De Trabajo

1. Configurar la ruta por defecto en `R1-RN`.
2. Anunciar esa default dentro de `OSPF`.
3. Verificar que `R2-RN`, `R3-RN`, `R4-RN` y `R5-RN` aprendan la ruta por defecto.

## Dispositivo: R1-RN

```cisco
enable
configure terminal

ip route 0.0.0.0 0.0.0.0 172.16.21.97

router ospf 1
 default-information originate

end
write memory
```

## Verificacion

En `R1-RN`:

```cisco
show ip route
show running-config | section router ospf
ping 172.16.11.66
ping 172.16.32.114
```

En `R4-RN` y `R5-RN`:

```cisco
show ip route
ping 172.16.11.66
ping 172.16.32.81
```

## Resultado Esperado

- `R1-RN` debe tener una ruta por defecto hacia `ICP2`.
- Los routers internos de ISP2 deben aprender `O*E2 0.0.0.0/0` por `OSPF`.
- Los gateways de Ventas y Facturacion deben poder alcanzar redes de ISP1 e ISP3 cuando BGP ya este operativo.
