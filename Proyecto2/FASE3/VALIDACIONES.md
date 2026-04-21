# FASE 3 - VALIDACIONES

## Orden Recomendado

1. `ISP1-SALIDA.md`
2. `ISP2-SALIDA.md`
3. `ISP3-SALIDA.md`
4. `CORE-BGP.md`
5. Pruebas inter-ISP
6. Pruebas de DHCP, DNS y HTTP

## Validacion De Salida Interna

### ISP1

En `R4-TU` y `R5-TU`:

```cisco
show ip route
```

Debe existir una ruta por defecto `O*E2 0.0.0.0/0`.

### ISP2

En `R4-RN` y `R5-RN`:

```cisco
show ip route
```

Debe existir una ruta por defecto `O*E2 0.0.0.0/0`.

### ISP3

En `R2-CF`, `R3-CF` y `R4-CF`:

```cisco
show ip route
```

Debe existir una ruta por defecto `D*EX 0.0.0.0/0`.

## Validacion De BGP

En `ICP1`, `ICP2` y `ICP3`:

```cisco
show ip bgp summary
show ip bgp
show ip route
```

Resultado esperado:

- vecinos `Established`
- cada `ICP` aprende los otros dos bloques `172.16.x.0/24`

## Pruebas De Conectividad Entre ISP

### Desde routers borde

En `R1-TU`:

```cisco
ping 172.16.21.66
ping 172.16.32.81
ping 172.16.32.33
```

En `R1-RN`:

```cisco
ping 172.16.11.66
ping 172.16.32.81
ping 172.16.32.33
```

En `R1-CF`:

```cisco
ping 172.16.11.66
ping 172.16.21.66
ping 172.16.21.1
```

### Desde gateways de usuarios

En `R4-TU`:

```cisco
ping 172.16.21.66
```

En `R5-TU`:

```cisco
ping 172.16.21.66
```

En `R2-CF` y `R3-CF`:

```cisco
ping 172.16.11.66
```

## Pruebas De DHCP Remoto

1. En una PC de `Administracion`, elegir `DHCP`.
2. En una PC de `Atencion al Cliente`, elegir `DHCP`.
3. En una PC de `Soporte`, elegir `DHCP`.
4. En una PC de `Seguridad`, elegir `DHCP`.
5. En la laptop WiFi, elegir `DHCP`.

Resultado esperado:

- reciben IP del pool correcto
- reciben gateway correcto
- reciben DNS `172.16.11.66`

## Pruebas De DNS Y HTTP

Desde cualquier host final con ruta completa:

```text
ping 172.16.11.66
```

Luego abrir Browser y entrar a:

```text
http://www.proyecto2_202300431.com
```

## Criterio De Cierre De Fase 3

La Fase 3 se considera completa cuando:

- `BGP` esta levantado entre los tres `ICP`
- los tres prefijos `172.16.11.0/24`, `172.16.21.0/24` y `172.16.32.0/24` se intercambian correctamente
- los routers internos tienen salida por defecto al borde
- `DHCP` remoto funciona desde ISP1 e ISP3
- `DNS/HTTP` son accesibles entre ISPs
