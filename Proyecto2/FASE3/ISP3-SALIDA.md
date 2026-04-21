# FASE 3 - ISP3 SALIDA

## Objetivo

Dar salida al resto del pais desde `ISP3` apuntando al `ICP3`, y propagar esa ruta por defecto dentro de `EIGRP` para que los routers internos de ISP3 alcancen las redes remotas.

## Orden De Trabajo

1. Configurar la ruta por defecto en `R1-CF`.
2. Redistribuir esa default dentro de `EIGRP`.
3. Verificar que `R2-CF`, `R3-CF`, `R4-CF` y `R5-CF` aprendan la ruta por defecto.

## Dispositivo: R1-CF

```cisco
enable
configure terminal

ip route 0.0.0.0 0.0.0.0 172.16.32.113

router eigrp 1
 redistribute static

end
write memory
```

## Nota Importante

En este modelo sencillo, `redistribute static` se usa solo para inyectar la ruta por defecto de `R1-CF` dentro de `EIGRP`. Si luego agregas mas rutas estaticas en `R1-CF`, tambien se redistribuiran.

## Verificacion

En `R1-CF`:

```cisco
show ip route
show ip protocols
ping 172.16.11.66
ping 172.16.21.66
```

En `R2-CF`, `R3-CF` y `R4-CF`:

```cisco
show ip route
ping 172.16.11.66
ping 172.16.21.66
```

## Resultado Esperado

- `R1-CF` debe tener una ruta por defecto hacia `ICP3`.
- Los routers internos de ISP3 deben aprender una ruta `D*EX 0.0.0.0/0` por `EIGRP`.
- Soporte, Seguridad y WiFi deben poder salir hacia otras redes una vez que BGP ya este levantado.
