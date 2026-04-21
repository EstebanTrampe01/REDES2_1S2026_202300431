# FASE 3 - ISP1 SALIDA

## Objetivo

Dar salida al resto del pais desde `ISP1` apuntando al `ICP1`, y propagar esa salida dentro de `OSPF` para que los routers internos de ISP1 conozcan una ruta por defecto.

## Orden De Trabajo

1. Configurar la ruta por defecto en `R1-TU`.
2. Hacer que `R1-TU` anuncie esa default a `OSPF`.
3. Verificar que `R2-TU`, `R3-TU`, `R4-TU` y `R5-TU` aprendan la ruta por defecto.

## Dispositivo: R1-TU

```cisco
enable
configure terminal

ip route 0.0.0.0 0.0.0.0 172.16.11.97

router ospf 1
 default-information originate

end
write memory
```

## Verificacion

En `R1-TU`:

```cisco
show ip route
show running-config | section router ospf
ping 172.16.21.66
ping 172.16.32.114
```

En `R4-TU` y `R5-TU`:

```cisco
show ip route
ping 172.16.21.66
```

## Resultado Esperado

- `R1-TU` debe tener una ruta por defecto hacia `ICP1`.
- Los routers internos de ISP1 deben aprender `O*E2 0.0.0.0/0` por `OSPF`.
- `R4-TU` y `R5-TU` deben poder alcanzar `172.16.21.66` una vez que BGP este levantado en el core.
