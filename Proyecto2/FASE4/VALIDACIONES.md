# FASE 4 - VALIDACIONES

## Orden Recomendado

1. Aplicar ACL de `Administracion`.
2. Aplicar ACL de `Atencion al Cliente`.
3. Aplicar ACL de `Ventas`.
4. Aplicar ACL de `Facturacion`.
5. Aplicar ACL de `Soporte`.
6. Aplicar ACL de `Seguridad`.
7. Verificar servicios centrales.
8. Verificar matriz de pings permitidos y denegados.

## Servicios Que Deben Seguir Funcionando

Desde un host de cada departamento verifica:

1. `DHCP` entrega IP valida.
2. `nslookup` o Browser resuelve `www.proyecto2_202300431.com`.
3. El navegador abre `http://www.proyecto2_202300431.com`.

## Pruebas Permitidas

## Seguridad

- `PC-SEG -> PC-SOP` debe responder.
- `PC-SEG -> PC-ADM` debe responder.
- `PC-SEG -> PC-VENTAS` debe responder.
- `PC-SEG -> PC-FACT` debe responder.
- `PC-SEG -> PC-ATC` debe responder.

## Soporte

- `PC-SOP -> PC-ADM` debe responder.
- `PC-SOP -> PC-VENTAS` debe responder.
- `PC-SOP -> PC-FACT` debe responder.
- `PC-SOP -> PC-ATC` debe responder.

## Administracion

- `PC-ADM -> PC-SOP` debe responder.
- `PC-ADM -> PC-VENTAS` debe responder.
- `PC-ADM -> PC-FACT` debe responder.
- `PC-ADM -> PC-ATC` debe responder.

## Atencion al Cliente

- `PC-ATC -> PC-VENTAS` debe responder.

## Facturacion

- `PC-FACT -> PC-VENTAS` debe responder.

## Ventas

- `PC-VENTAS -> PC-ATC` debe responder.
- `PC-VENTAS -> PC-FACT` debe responder.

## Pruebas Denegadas

## Hacia Seguridad

- `PC-SOP -> PC-SEG` debe fallar.
- `PC-ADM -> PC-SEG` debe fallar.
- `PC-VENTAS -> PC-SEG` debe fallar.
- `PC-FACT -> PC-SEG` debe fallar.
- `PC-ATC -> PC-SEG` debe fallar.

## Atencion al Cliente

- `PC-ATC -> PC-ADM` debe fallar.
- `PC-ATC -> PC-SOP` debe fallar.
- `PC-ATC -> PC-SEG` debe fallar.
- `PC-ATC -> PC-FACT` debe fallar.

## Facturacion

- `PC-FACT -> PC-ADM` debe fallar.
- `PC-FACT -> PC-SOP` debe fallar.
- `PC-FACT -> PC-SEG` debe fallar.
- `PC-FACT -> PC-ATC` debe fallar.

## Ventas

- `PC-VENTAS -> PC-ADM` debe fallar.
- `PC-VENTAS -> PC-SOP` debe fallar.
- `PC-VENTAS -> PC-SEG` debe fallar.

## Comandos De Revision

En routers con ACLs:

```cisco
show access-lists
```

Debes ver contadores incrementando en:

- lineas `permit` cuando el trafico esta permitido
- lineas `deny` cuando el trafico esta bloqueado

## Validacion Final De Servicios

Desde un host de `Administracion`, `Soporte` y `WiFi`:

1. renovar `DHCP`
2. hacer `ping 172.16.11.66`
3. abrir `http://www.proyecto2_202300431.com`

## Criterio De Cierre De Fase 4

La fase se considera cerrada cuando:

- las ACL cumplen la matriz de comunicacion
- `DHCP` sigue funcionando en todos los clientes
- `DNS` sigue resolviendo el dominio del proyecto
- `HTTP` sigue respondiendo desde el servidor central
