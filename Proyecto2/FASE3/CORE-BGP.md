# FASE 3 - CORE BGP

## Objetivo

Integrar los tres ISP por medio de `BGP` usando un modelo sencillo:

- `BGP` solo entre `ICP1`, `ICP2` y `ICP3`
- cada `ICP` anuncia el prefijo resumen de su ISP
- cada `ICP` tiene una ruta estatica hacia el bloque local de su ISP

## AS Definidos

| Equipo | AS |
| --- | --- |
| ICP1 | 100 |
| ICP2 | 200 |
| ICP3 | 300 |

## Orden De Trabajo

1. Confirmar que `ICP1`, `ICP2` y `ICP3` ya se alcanzan entre si por `192.168.31.x`.
2. Configurar las rutas estaticas locales en los tres `ICP`.
3. Configurar `BGP` en `ICP1`.
4. Configurar `BGP` en `ICP2`.
5. Configurar `BGP` en `ICP3`.
6. Verificar vecinos con `show ip bgp summary`.
7. Verificar rutas con `show ip bgp` y `show ip route`.

## Nota Importante

El comando `network` de `BGP` solo anuncia una red si esa red ya existe en la tabla de enrutamiento local. Por eso primero se agregan las rutas estaticas resumen hacia cada ISP.

## Dispositivo: ICP1

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

## Dispositivo: ICP2

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

## Dispositivo: ICP3

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

## Verificacion

En cada `ICP`:

```cisco
show ip bgp summary
show ip bgp
show ip route
```

### Resultado Esperado

- `ICP1` debe ver las redes `172.16.21.0/24` y `172.16.32.0/24` por BGP.
- `ICP2` debe ver las redes `172.16.11.0/24` y `172.16.32.0/24` por BGP.
- `ICP3` debe ver las redes `172.16.11.0/24` y `172.16.21.0/24` por BGP.
- El estado de los vecinos debe aparecer `Established`.
