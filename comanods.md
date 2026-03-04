
# MS1

en
conf t
hostname MS1
interface g1/1/1
 no switchport
 ip address 10.4.31.1 255.255.255.252
 no shutdown

interface g1/1/2
 no switchport
 ip address 10.4.31.14 255.255.255.252
 no shutdown

 
# MS2

en
conf t
hostname MS2
interface g1/1/1
 no switchport
 ip address 10.4.31.2 255.255.255.252
 no shutdown

interface g1/1/2
 no switchport
 ip address 10.4.31.5 255.255.255.252
 no shutdown

interface g1/1/3
 no switchport
 ip address 10.4.31.18 255.255.255.252
 no shutdown
exit
exit

# MS6

en
conf t
hostname MS6
interface g1/1/1
 no switchport
 ip address 10.4.31.6 255.255.255.252
 no shutdown

interface g1/1/2
 no switchport
 ip address 10.4.31.9 255.255.255.252
 no shutdown
exit
exit

# MS7

en
conf t
hostname MS7
interface g1/1/1
 no switchport
 ip address 10.4.31.10 255.255.255.252
 no shutdown

interface g1/1/2
 no switchport
 ip address 10.4.31.13 255.255.255.252
 no shutdown

interface g1/1/3
 no switchport
 ip address 10.4.31.17 255.255.255.252
 no shutdown
exit
exit


# Comando EIRG (202300431 - IMPAR)
### MS alls zone

en
conf t
ip routing
router eigrp 100
no auto-summary
network 10.4.31.0 0.0.0.255
end

### Verificar

show ip eigrp neighbors
show ip route