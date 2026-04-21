# FASE 4 - ACLs

## Objetivo

Aplicar ACLs simples y directas para cumplir las reglas de comunicacion entre departamentos sin romper los servicios obligatorios del proyecto:

- `DHCP`
- `DNS`
- `HTTP`

## Estrategia Elegida

Para mantener la solucion estable en Packet Tracer, las ACL se aplican en los gateways de cada departamento y se validan principalmente con `ping`, que es la forma de prueba indicada en el enunciado.

Las ACL:

- permiten `DHCP relay`
- permiten `DNS` hacia `172.16.11.66`
- permiten `HTTP` hacia `172.16.11.66`
- permiten respuestas `ICMP echo-reply`
- controlan que departamentos pueden iniciar `ping` hacia otros departamentos

## Redes De Referencia

| Departamento | Red |
| --- | --- |
| Administracion | 172.16.11.0/27 |
| Atencion al Cliente | 172.16.11.32/27 |
| Ventas | 172.16.21.0/27 |
| Facturacion | 172.16.21.32/27 |
| Soporte | 172.16.32.0/27 |
| Seguridad | 172.16.32.32/27 |
| WiFi | 172.16.32.80/28 |
| DNS/HTTP | 172.16.11.66 |
| DHCP | 172.16.21.66 |

## Interpretacion Operativa De Las Reglas

| Origen | Puede iniciar ping hacia |
| --- | --- |
| Seguridad | Todos los departamentos |
| Soporte | Todos menos Seguridad |
| Administracion | Todos menos Seguridad |
| Atencion al Cliente | Solo Ventas |
| Facturacion | Solo Ventas |
| Ventas | Solo Facturacion y Atencion al Cliente |

`WiFi` no aparece en la matriz del enunciado, por lo que no se restringe con ACL en esta fase.

## Orden De Trabajo

1. Configurar ACL de `Administracion` en `R4-TU`.
2. Configurar ACL de `Atencion al Cliente` en `R5-TU`.
3. Configurar ACL de `Ventas` en `R4-RN` y `R5-RN`.
4. Configurar ACL de `Facturacion` en `R4-RN` y `R5-RN`.
5. Configurar ACL de `Soporte` en `R2-CF`.
6. Configurar ACL de `Seguridad` en `R3-CF`.
7. Verificar `ping`, `DNS` y `HTTP` desde cada red.

## ACL - Administracion

## Dispositivo: R4-TU

```cisco
enable
configure terminal

ip access-list extended ACL-ADM-IN
 remark DHCP relay
 permit udp any eq bootpc any eq bootps
 remark DNS al servidor central
 permit udp any host 172.16.11.66 eq domain
 permit tcp any host 172.16.11.66 eq domain
 remark HTTP al servidor central
 permit tcp any host 172.16.11.66 eq 80
 remark Permitir respuestas de ping
 permit icmp any any echo-reply
 remark Administracion no puede iniciar hacia Seguridad
 deny icmp 172.16.11.0 0.0.0.31 172.16.32.32 0.0.0.31 echo
 remark Administracion si puede iniciar hacia los demas
 permit icmp 172.16.11.0 0.0.0.31 172.16.11.32 0.0.0.31 echo
 permit icmp 172.16.11.0 0.0.0.31 172.16.21.0 0.0.0.31 echo
 permit icmp 172.16.11.0 0.0.0.31 172.16.21.32 0.0.0.31 echo
 permit icmp 172.16.11.0 0.0.0.31 172.16.32.0 0.0.0.31 echo
 deny ip any any log

interface GigabitEthernet0/1
 ip access-group ACL-ADM-IN in

end
write memory
```

## ACL - Atencion al Cliente

## Dispositivo: R5-TU

```cisco
enable
configure terminal

ip access-list extended ACL-ATC-IN
 remark DHCP relay
 permit udp any eq bootpc any eq bootps
 remark DNS al servidor central
 permit udp any host 172.16.11.66 eq domain
 permit tcp any host 172.16.11.66 eq domain
 remark HTTP al servidor central
 permit tcp any host 172.16.11.66 eq 80
 remark Permitir respuestas de ping
 permit icmp any any echo-reply
 remark ATC solo puede iniciar hacia Ventas
 permit icmp 172.16.11.32 0.0.0.31 172.16.21.0 0.0.0.31 echo
 deny ip any any log

interface GigabitEthernet0/1
 ip access-group ACL-ATC-IN in

end
write memory
```

## ACL - Ventas

Se debe aplicar la misma ACL en `R4-RN` y `R5-RN` para no romper `HSRP`.

## Dispositivo: R4-RN y R5-RN

```cisco
enable
configure terminal

ip access-list extended ACL-VENTAS-IN
 remark DHCP local
 permit udp any eq bootpc any eq bootps
 remark DNS al servidor central
 permit udp any host 172.16.11.66 eq domain
 permit tcp any host 172.16.11.66 eq domain
 remark HTTP al servidor central
 permit tcp any host 172.16.11.66 eq 80
 remark Permitir respuestas de ping
 permit icmp any any echo-reply
 remark Ventas solo puede iniciar hacia Facturacion y ATC
 permit icmp 172.16.21.0 0.0.0.31 172.16.21.32 0.0.0.31 echo
 permit icmp 172.16.21.0 0.0.0.31 172.16.11.32 0.0.0.31 echo
 deny ip any any log

interface GigabitEthernet0/1.10
 ip access-group ACL-VENTAS-IN in

end
write memory
```

## ACL - Facturacion

Se debe aplicar la misma ACL en `R4-RN` y `R5-RN` para no romper `HSRP`.

## Dispositivo: R4-RN y R5-RN

```cisco
enable
configure terminal

ip access-list extended ACL-FACT-IN
 remark DHCP relay
 permit udp any eq bootpc any eq bootps
 remark DNS al servidor central
 permit udp any host 172.16.11.66 eq domain
 permit tcp any host 172.16.11.66 eq domain
 remark HTTP al servidor central
 permit tcp any host 172.16.11.66 eq 80
 remark Permitir respuestas de ping
 permit icmp any any echo-reply
 remark Facturacion solo puede iniciar hacia Ventas
 permit icmp 172.16.21.32 0.0.0.31 172.16.21.0 0.0.0.31 echo
 deny ip any any log

interface GigabitEthernet0/2
 ip access-group ACL-FACT-IN in

end
write memory
```

## ACL - Soporte

## Dispositivo: R2-CF

```cisco
enable
configure terminal

ip access-list extended ACL-SOP-IN
 remark DHCP relay
 permit udp any eq bootpc any eq bootps
 remark DNS al servidor central
 permit udp any host 172.16.11.66 eq domain
 permit tcp any host 172.16.11.66 eq domain
 remark HTTP al servidor central
 permit tcp any host 172.16.11.66 eq 80
 remark Permitir respuestas de ping
 permit icmp any any echo-reply
 remark Soporte no puede iniciar hacia Seguridad
 deny icmp 172.16.32.0 0.0.0.31 172.16.32.32 0.0.0.31 echo
 remark Soporte si puede iniciar hacia los demas
 permit icmp 172.16.32.0 0.0.0.31 172.16.11.0 0.0.0.31 echo
 permit icmp 172.16.32.0 0.0.0.31 172.16.11.32 0.0.0.31 echo
 permit icmp 172.16.32.0 0.0.0.31 172.16.21.0 0.0.0.31 echo
 permit icmp 172.16.32.0 0.0.0.31 172.16.21.32 0.0.0.31 echo
 deny ip any any log

interface GigabitEthernet0/1
 ip access-group ACL-SOP-IN in

end
write memory
```

## ACL - Seguridad

## Dispositivo: R3-CF

```cisco
enable
configure terminal

ip access-list extended ACL-SEG-IN
 remark DHCP relay
 permit udp any eq bootpc any eq bootps
 remark DNS al servidor central
 permit udp any host 172.16.11.66 eq domain
 permit tcp any host 172.16.11.66 eq domain
 remark HTTP al servidor central
 permit tcp any host 172.16.11.66 eq 80
 remark Permitir respuestas de ping
 permit icmp any any echo-reply
 remark Seguridad puede iniciar hacia todos
 permit icmp 172.16.32.32 0.0.0.31 172.16.11.0 0.0.0.31 echo
 permit icmp 172.16.32.32 0.0.0.31 172.16.11.32 0.0.0.31 echo
 permit icmp 172.16.32.32 0.0.0.31 172.16.21.0 0.0.0.31 echo
 permit icmp 172.16.32.32 0.0.0.31 172.16.21.32 0.0.0.31 echo
 permit icmp 172.16.32.32 0.0.0.31 172.16.32.0 0.0.0.31 echo
 deny ip any any log

interface GigabitEthernet0/1
 ip access-group ACL-SEG-IN in

end
write memory
```

## Verificacion Inicial

En cada router donde apliques ACL:

```cisco
show access-lists
show running-config interface gigabitEthernet0/1
show running-config interface gigabitEthernet0/2
```

En `R4-RN` y `R5-RN` tambien:

```cisco
show running-config interface gigabitEthernet0/1.10
show standby brief
```

## Resultado Esperado

- `DNS`, `HTTP` y `DHCP` deben seguir funcionando.
- Los pings entre departamentos deben cumplir la matriz del proyecto.
- `HSRP` no debe romperse por aplicar ACLs distintas entre `R4-RN` y `R5-RN`.
