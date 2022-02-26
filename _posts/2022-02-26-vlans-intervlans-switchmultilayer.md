---
layout: single
title: VLANS - INTERVLANS
excerpt: "Aprederemos a como configurar VLANS y comunicación INTER-VLANS en un Switch Multicapa Cisco"
date: 2022-02-26
classes: wide
header:
  teaser: /assets/images/vlans-intervlans-switchmultilayer/Captura de pantalla 2022-02-26 114956.png
  teaser_home_page: true
categories:
  - cisco
tags:  
  - cisco
---

## Escenario en GNS3

![](/assets/images/vlans-intervlans-switchmultilayer/Captura de pantalla 2022-02-26 113722.png)

## Creación de VLANS

#### 1. Crearemos las siguientes VLANS:
            
```go
-VLAN10: TAC (192.168.10.0/24)
-VLAN20: SERVERS (192.168.20.0/24)
-VLAN199: MANAGEMENT (192.168.199.0/24)
```

```go
L3SW1(config)#vlan 10
L3SW1(config-vlan)#name TAC
L3SW1(config-vlan)#vlan 20
L3SW1(config-vlan)#name SERVERS
L3SW1(config-vlan)#vlan 199
L3SW1(config-vlan)#name MANAGEMENT
L3SW1(config-vlan)#exit
```

```go
L3SW1#show vlan brief 

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Gi0/0, Gi0/1, Gi0/2, Gi0/3
                                                Gi1/0, Gi1/1, Gi1/2, Gi1/3
                                                Gi2/0, Gi2/1, Gi2/2, Gi2/3
                                                Gi3/0, Gi3/1, Gi3/2, Gi3/3
10   TAC                              active    
20   SERVERS                          active    
199  MANAGEMENT                       active    
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup
```

## Asignamos cada interfaz a su respectiva VLAN

```go
L3SW1(config)#int g1/0
L3SW1(config-if)#switchport mode access 
L3SW1(config-if)#switchport access vlan 10
L3SW1(config-if)#int range g0/2-3
L3SW1(config-if-range)#switchport mode access 
L3SW1(config-if-range)#switchport access vlan 20
L3SW1(config-if)#int g0/1                 
L3SW1(config-if)#switchport mode access   
L3SW1(config-if)#switchport access vlan 199
L3SW1(config-if-range)#exit
```

```go
L3SW1#show vlan brief 

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Gi0/0, Gi1/1, Gi1/2, Gi1/3
                                                Gi2/0, Gi2/1, Gi2/2, Gi2/3
                                                Gi3/0, Gi3/1, Gi3/2, Gi3/3
10   TAC                              active    Gi1/0
20   SERVERS                          active    Gi0/2, Gi0/3
199  MANAGEMENT                       active    Gi0/1
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup
```

## Configuramos default-gateway para cada VLAN

```go
L3SW1(config-if)#int vlan 10
L3SW1(config-if)#ip add 192.168.10.1 255.255.255.0
L3SW1(config-if)#no sh
L3SW1(config-if)#int vlan 20
L3SW1(config-if)#ip add 192.168.20.1 255.255.255.0
L3SW1(config-if)#no sh
L3SW1(config-if)#int vlan 199
L3SW1(config-if)#ip add 192.168.199.1 255.255.255.0
L3SW1(config-if)#no sh
L3SW1(config-if)#exit
```

```go
L3SW1#sh ip int br | i manual
Vlan10                 192.168.10.1    YES manual up                    up      
Vlan20                 192.168.20.1    YES manual up                    up      
Vlan199                192.168.199.1   YES manual up                    up
```

## Visualizamos la tabla de enrutamiento

```go
L3SW1#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is not set

      192.168.10.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.10.0/24 is directly connected, Vlan10
L        192.168.10.1/32 is directly connected, Vlan10
      192.168.20.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.20.0/24 is directly connected, Vlan20
L        192.168.20.1/32 is directly connected, Vlan20
      192.168.199.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.199.0/24 is directly connected, Vlan199
L        192.168.199.1/32 is directly connected, Vlan199
```

## Verificamos que haya comunicacion inter-VLANS

#### 1. Hacemos ping de la PC-TAC hacia el servidor DHCP y viceversa

#### PC-TAC

```go
root@TAC:~# hostname -I
192.168.10.11 
```

#### DHCP-SERVER

```go
root@DHCP:~# hostname -I
192.168.20.100
```

#### TAC --> DHCP

```go
root@TAC:~# ping -c 1 192.168.20.100
PING 192.168.20.100 (192.168.20.100) 56(84) bytes of data.
64 bytes from 192.168.20.100: icmp_seq=1 ttl=63 time=5.10 ms

--- 192.168.20.100 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 5.096/5.096/5.096/0.000 ms
```

#### DHCP --> TAC

```go
root@DHCP:~# ping -c 1 192.168.10.11 
PING 192.168.10.11 (192.168.10.11) 56(84) bytes of data.
64 bytes from 192.168.10.11: icmp_seq=1 ttl=63 time=5.42 ms

--- 192.168.10.11 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 5.417/5.417/5.417/0.000 ms
```

**Servido**