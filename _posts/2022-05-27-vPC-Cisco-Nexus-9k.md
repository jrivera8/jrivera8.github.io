---
layout: single
title: NEXUS-VPC
excerpt: "Configuración vPC en Nexus"
date: 2022-06-27
classes: wide
header:
  teaser: assets/images/vpc-cisco/escenario.png
  teaser_home_page: true
categories:
  - cisco
tags:  
  - nexus
  - cisco
---
# Topología
![](/assets/images/vpc-cisco/escenario.png)
## Nexus-1
#### Hostname
```go
switch# conf t
switch(config)# hostname Nexus-1
```
#### Habilitamos los features
```go
Nexus-1(config)# feature vpc
Nexus-1(config)# feature lacp
```
#### Configuramos la interfaz de managment
```go
Nexus-1(config)# inte mgmt 0
Nexus-1(config-if)# no sh
Nexus-1(config-if)# description PKA
Nexus-1(config-if)# vrf member management 
Nexus-1(config-if)# ip add 10.0.0.1/30
Nexus-1(config-if)# end
```
#### Probamos conectividad con Nexus-2
```go
Nexus-1# ping 10.0.0.2 vrf management 
PING 10.0.0.2 (10.0.0.2): 56 data bytes
64 bytes from 10.0.0.2: icmp_seq=0 ttl=254 time=6.768 ms
64 bytes from 10.0.0.2: icmp_seq=1 ttl=254 time=4.477 ms
64 bytes from 10.0.0.2: icmp_seq=2 ttl=254 time=3.744 ms
64 bytes from 10.0.0.2: icmp_seq=3 ttl=254 time=4.305 ms
64 bytes from 10.0.0.2: icmp_seq=4 ttl=254 time=6.987 ms

--- 10.0.0.2 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 3.744/5.256/6.987 ms
```
#### Configuramos nuestro vPC Domain
```go
Nexus-1(config)# vpc domain 1
Nexus-1(config-vpc-domain)# peer-switch
Nexus-1(config-vpc-domain)# role priority 4096
Nexus-1(config-vpc-domain)# peer-keepalive destination 10.0.0.2 source 10.0.0.1
Nexus-1(config-vpc-domain)# peer-gateway 
Nexus-1(config-vpc-domain)# ip arp synchronize 
Nexus-1(config-vpc-domain)# exit
```
#### Verificamos el estado del vPC keep-alive
```go
Nexus-1(config-vpc-domain)# show vpc brief | inc keep-alive
vPC keep-alive status             : peer is alive
```
![](/assets/images/vpc-cisco/1.png)
#### Configuramos vPC Peer-Link
```go
Nexus-1(config)# int e1/1-2
Nexus-1(config-if-range)# description Peer-Link
Nexus-1(config-if-range)# channel-group 1 mode active 
Nexus-1(config-if-range)# no sh
Nexus-1(config-if-range)# exit
Nexus-1(config)# int port-channel 1
Nexus-1(config-if)# description Peer-Link
Nexus-1(config-if)# switchport
Nexus-1(config-if)# switchport mode trunk
Nexus-1(config-if)# vpc peer-link
Nexus-1(config-if)# exit 
```
![](/assets/images/vpc-cisco/3.png)
#### Configuramos vPC Members-Ports
```go
Nexus-1(config)# int e1/3
Nexus-1(config-if)# no sh
Nexus-1(config-if)# description Member-Port
Nexus-1(config-if)# channel-group 2 mode active 
Nexus-1(config-if)# int port-channel 2
Nexus-1(config-if)# description Member-Port
Nexus-1(config-if)# no sh 
Nexus-1(config-if)# switchport    
Nexus-1(config-if)# switchport mode trunk 
Nexus-1(config-if)# vpc 2
Nexus-1(config-if)# exit
Nexus-1(config)# int e1/4
Nexus-1(config-if)# description Member-Port
Nexus-1(config-if)# no sh
Nexus-1(config-if)# channel-group 3 mode active 
Nexus-1(config-if)# exit
Nexus-1(config)# int port-channel 3
Nexus-1(config-if)# no sh
Nexus-1(config-if)# description Member-Port
Nexus-1(config-if)# switchport    
Nexus-1(config-if)# switchport mode trunk 
Nexus-1(config-if)# vpc 3
Nexus-1(config-if)# exit
```
![](/assets/images/vpc-cisco/11.png)
## Nexus-2
#### Hostname
```go
switch# conf t
switch(config)# hostname Nexus-2
```
#### Habilitamos los features
```go
Nexus-2(config)# feature vpc
Nexus-2(config)# feature lacp
```
#### Configuramos la interfaz de managment
```go
Nexus-2(config)# int mgmt 0
Nexus-2(config-if)# no sh
Nexus-2(config-if)# description PKA
Nexus-2(config-if)# vrf member management
Nexus-2(config-if)# ip add 10.0.0.2/30
Nexus-2(config-if)# end
```
#### Probamos conectividad con Nexus-1
```go
Nexus-2# ping 10.0.0.1 vrf management 
PING 10.0.0.1 (10.0.0.1): 56 data bytes
64 bytes from 10.0.0.1: icmp_seq=0 ttl=254 time=14.854 ms
64 bytes from 10.0.0.1: icmp_seq=1 ttl=254 time=4.298 ms
64 bytes from 10.0.0.1: icmp_seq=2 ttl=254 time=5.53 ms
64 bytes from 10.0.0.1: icmp_seq=3 ttl=254 time=4.606 ms
64 bytes from 10.0.0.1: icmp_seq=4 ttl=254 time=2.8 ms

--- 10.0.0.1 ping statistics ---
5 packets transmitted, 5 packets received, 0.00% packet loss
round-trip min/avg/max = 2.8/6.417/14.854 ms
```
#### Configuramos nuestro vPC Domain
```go
Nexus-2(config)# vpc domain 1
Nexus-2(config-vpc-domain)# peer-switch 
Nexus-2(config-vpc-domain)# role priority 8192
Nexus-2(config-vpc-domain)# peer-keepalive destination 10.0.0.1 source 10.0.0.2
Nexus-2(config-vpc-domain)# peer-gateway 
Nexus-2(config-vpc-domain)# ip arp synchronize 
Nexus-2(config-vpc-domain)# exit
```
#### Verificamos el estado del vPC keep-alive
```go
Nexus-2(config-vpc-domain)# show vpc brief | inc keep-alive
vPC keep-alive status             : peer is alive
```
![](/assets/images/vpc-cisco/2.png)
#### Configuramos vPC Peer-Link
```go
Nexus-2(config)# int e1/1-2
Nexus-2(config-if-range)# description Peer-Link
Nexus-2(config-if-range)# channel-group 1 mode active 
Nexus-2(config-if-range)# no sh
Nexus-2(config-if-range)# exit
Nexus-2(config)# int port-channel 1
Nexus-2(config-if)# description Peer-Link
Nexus-2(config-if)# switchport
Nexus-2(config-if)# switchport mode trunk
Nexus-2(config-if)# vpc peer-link
Nexus-2(config-if)# exit 
```
![](/assets/images/vpc-cisco/4.png)
#### Configuramos vPC Members-Ports
```go
Nexus-2(config)# int e1/3
Nexus-2(config-if)# no sh
Nexus-2(config-if)# description Member-Port
Nexus-2(config-if)# channel-group 2 mode active 
Nexus-2(config-if)# int port-channel 2
Nexus-2(config-if)# description Member-Port
Nexus-2(config-if)# no sh 
Nexus-2(config-if)# switchport    
Nexus-2(config-if)# switchport mode trunk 
Nexus-2(config-if)# vpc 2
Nexus-2(config-if)# exit
Nexus-2(config)# int e1/4
Nexus-2(config-if)# description Member-Port
Nexus-2(config-if)# no sh
Nexus-2(config-if)# channel-group 3 mode active 
Nexus-2(config-if)# exit
Nexus-2(config)# int port-channel 3
Nexus-2(config-if)# no sh
Nexus-2(config-if)# description Member-Port
Nexus-2(config-if)# switchport    
Nexus-2(config-if)# switchport mode trunk 
Nexus-2(config-if)# vpc 3
Nexus-2(config-if)# exit
```
![](/assets/images/vpc-cisco/12.png)
## L2SW1
```go
Switch(config)#hostname L2SW1
L2SW1(config)#vlan 10
L2SW1(config-vlan)#name TAC
L2SW1(config-vlan)#vlan 20
L2SW1(config-vlan)#name SALES
L2SW1(config-vlan)#exit
L2SW1(config)#int range g0/1-2
L2SW1(config-if-range)#description Member-Port
L2SW1(config-if-range)#channel-group 2 mode active 
L2SW1(config-if-range)#exit
L2SW1(config)#int port-channel 2
L2SW1(config-if)#no sh
L2SW1(config-if)#switchport trunk encapsulation dot1q 
L2SW1(config-if)#switchport mode trunk 
L2SW1(config-if)#exit
L2SW1(config)#int g0/2
L2SW1(config-if)#switchport mode access 
L2SW1(config-if)#switchport access vlan 10
L2SW1(config-if)#exit
```
![](/assets/images/vpc-cisco/13.png)
## L2SW2
```go
Switch>en
Switch#conf t
Switch(config)#hostname L2SW2
L2SW2(config)#int range g0/0-1
L2SW2(config-if-range)#description Members-Ports
L2SW2(config-if-range)#channel-group 3 mode active 
L2SW2(config-if-range)#exit
L2SW2(config)#int port-channel 3
L2SW2(config-if)#description Members-Ports
L2SW2(config-if)#no sh
L2SW2(config-if)#switchport trunk encapsulation dot1q 
L2SW2(config-if)#switchport mode trunk 
L2SW2(config-if)#exit
L2SW2(config)#vlan 10
L2SW2(config-vlan)#name TAC
L2SW2(config-vlan)#vlan 20
L2SW2(config-vlan)#name SALES
L2SW2(config-vlan)#exit
L2SW2(config)#int g0/2
L2SW2(config-if)#switchport mode access 
L2SW2(config-if)#switchport access vlan 20
L2SW2(config-if)#exit
```
![](/assets/images/vpc-cisco/14.png)
## Para la comunicacion inter-vlan habilitamos los siguientes features
#### Nexus-1
```go
Nexus-1(config)# int vlan 10
Nexus-1(config-if)# no sh
Nexus-1(config-if)# ip add 192.168.10.2/24
Nexus-1(config-if)# hsrp 10
Nexus-1(config-if-hsrp)# ip 192.168.10.1
Nexus-1(config-if-hsrp)# priority 255
Nexus-1(config-if-hsrp)# preempt 
Nexus-1(config-if-hsrp)# exit
Nexus-1(config-if)# int vlan 20
Nexus-1(config-if)# no sh
Nexus-1(config-if)# ip add 192.168.20.2/24
Nexus-1(config-if)# hsrp 20
Nexus-1(config-if-hsrp)# ip 192.168.20.1
Nexus-1(config-if-hsrp)# priority 255
Nexus-1(config-if-hsrp)# preempt 
Nexus-1(config-if-hsrp)# exit
```
![](/assets/images/vpc-cisco/15.png)
#### Nexus-2
```go
Nexus-2(config)# int vlan 10
Nexus-2(config-if)# no sh
Nexus-2(config-if)# ip add 192.168.10.3/24
Nexus-2(config-if)# hsrp 10
Nexus-2(config-if-hsrp)# ip 192.168.10.1 
Nexus-2(config-if-hsrp)# priority 254
Nexus-2(config-if-hsrp)# exit
Nexus-2(config-if)# int vlan 20
Nexus-2(config-if)# no sh
Nexus-2(config-if)# ip add 192.168.20.3/24
Nexus-2(config-if)# hsrp 20
Nexus-2(config-if-hsrp)# ip 192.168.20.1  
Nexus-2(config-if-hsrp)# priority 254
Nexus-2(config-if-hsrp)# exit
```
![](/assets/images/vpc-cisco/16.png)
## Pruebas de conectividad
#### Toolbox-1 -> Toolbox-2
![](/assets/images/vpc-cisco/19.png)
#### Toolbox-2 -> Toolbox-1
![](/assets/images/vpc-cisco/20.png)