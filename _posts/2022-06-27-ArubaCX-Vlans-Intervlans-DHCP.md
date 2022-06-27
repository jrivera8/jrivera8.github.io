---
layout: single
title: ARUBA VLANS-LAG-DHCP
excerpt: "Configuración básica de vlans-intevlans-lag-dhcp"
date: 2022-06-27
classes: wide
header:
  teaser: assets/images/Aruba-vlans-lag-dhcp/escenario.png
  teaser_home_page: true
categories:
  - aruba
tags:  
  - aruba
---
# Topología
![](/assets/images/Aruba-vlans-lag-dhcp/escenario.png)
# Configuración básica
## ArubaCX1
```go
switch# conf
switch(config)# hostname ArubaCX1  
ArubaCX1(config)# int vlan 1
ArubaCX1(config-if-vlan)# ip add 10.0.1.11/24
ArubaCX1(config-if-vlan)# exit
ArubaCX1(config)# user jose.rivera group administrators password 
Enter password: ****
Confirm password: ****
```
ArubaCX1(config)# end
## ArubaCX2
```go
switch# conf
switch(config)# hostname ArubaCX2  
ArubaCX2(config)# int vlan 1
ArubaCX2(config-if-vlan)# ip add 10.0.1.12/24
ArubaCX2(config-if-vlan)# exit
ArubaCX2(config)# user jose.rivera group administrators password
Enter password: ****
Confirm password: ****
ArubaCX2(config)# end
```
## ArubaCX3
```go
switch# conf
switch(config)# hostname ArubaCX3
exitArubaCX3(config)# int vlan 1
ArubaCX3(config-if-vlan)# ip add 10.0.1.13/24
ArubaCX3(config-if-vlan)# exit
ArubaCX3(config)# user jose.rivera group administrators password
Enter password: ****
Confirm password: ****
ArubaCX3(config)# end
```
# Configuración Etherchannel
## ArubaCX1
```go
ArubaCX1# conf
ArubaCX1(config)# int lag 1
ArubaCX1(config-lag-if)# no routing
ArubaCX1(config-lag-if)# no shutdown
ArubaCX1(config-lag-if)# lacp mode active
ArubaCX1(config-lag-if)# lacp rate fast
ArubaCX1(config-lag-if)# exit
ArubaCX1(config)# int lag 2
ArubaCX1(config-lag-if)# no routing
ArubaCX1(config-lag-if)# no shutdown
ArubaCX1(config-lag-if)# lacp mode active
ArubaCX1(config-lag-if)# lacp rate fast
ArubaCX1(config-lag-if)# exit
ArubaCX1(config)# int 1/1/1,1/1/2
ArubaCX1(config-if-<1/1/1,1/1/2>)# no shutdown
ArubaCX1(config-if-<1/1/1,1/1/2>)# no routing
ArubaCX1(config-if-<1/1/1,1/1/2>)# lag 1
ArubaCX1(config-if-<1/1/1,1/1/2>)# exit
ArubaCX1(config)# int 1/1/3,1/1/4
ArubaCX1(config-if-<1/1/3,1/1/4>)# no shutdown
ArubaCX1(config-if-<1/1/3,1/1/4>)# no routing
ArubaCX1(config-if-<1/1/3,1/1/4>)# lag 2
ArubaCX1(config-if-<1/1/3,1/1/4>)# end
```
## ArubaCX2
```go
ArubaCX2# conf
ArubaCX2(config)# int lag 1
ArubaCX2(config-lag-if)# no routing
ArubaCX2(config-lag-if)# no shutdown
ArubaCX2(config-lag-if)# lacp mode active
ArubaCX2(config-lag-if)# lacp rate fast
ArubaCX2(config-lag-if)# exit
ArubaCX2(config)# int 1/1/5,1/1/6
ArubaCX2(config-if-<1/1/5,1/1/6>)# no shutdown
ArubaCX2(config-if-<1/1/5,1/1/6>)# no routing
endArubaCX2(config-if-<1/1/5,1/1/6>)# lag 1
ArubaCX2(config-if-<1/1/5,1/1/6>)# end
```
## ArubaCX3
```go
ArubaCX3# conf
ArubaCX3(config)# int lag 2
ArubaCX3(config-lag-if)# no routing
ArubaCX3(config-lag-if)# no shutdown
ArubaCX3(config-lag-if)# lacp mode active
ArubaCX3(config-lag-if)# lacp rate fast
ArubaCX3(config-lag-if)# exit
ArubaCX3(config)# int 1/1/5,1/1/6
ArubaCX3(config-if-<1/1/5,1/1/6>)# no shutdown
endArubaCX3(config-if-<1/1/5,1/1/6>)# no routing
ArubaCX3(config-if-<1/1/5,1/1/6>)# lag 2
ArubaCX3(config-if-<1/1/5,1/1/6>)# end
```
## Show Commands
```go
ArubaCX1# show lag brief 
-----------------------------------------------------------------------
LAG     Type               Aggregate  Mode    Enabled  Status   Speed   
Name                       Mode                                 (Mb/s)  
-----------------------------------------------------------------------
lag1    normal             active     access  yes      up       2000    
lag2    normal             active     access  yes      up       2000
```
# Configuración Vlans
## ArubaCX1
```go
ArubaCX1# conf
ArubaCX1(config)# vlan 10,20,30
ArubaCX1(config-vlan-<10,20,30>)# exit
ArubaCX1(config)# int lag 1
ArubaCX1(config-lag-if)# vlan trunk allowed all
ArubaCX1(config-lag-if)# vlan trunk native 1
ArubaCX1(config-lag-if)# int lag 2
ArubaCX1(config-lag-if)# vlan trunk allowed all
ArubaCX1(config-lag-if)# vlan trunk native 1
ArubaCX1(config-lag-if)# end
```
## ArubaCX2
```go
ArubaCX2# conf
ArubaCX2(config)# vlan 10,20,30
ArubaCX2(config-vlan-<10,20,30>)# exit
ArubaCX2(config)# int 1/1/1
ArubaCX2(config-if)# no shutdown
ArubaCX2(config-if)# no routing
ArubaCX2(config-if)# vlan access 10
ArubaCX2(config-if)# int 1/1/2
ArubaCX2(config-if)# no shutdown
ArubaCX2(config-if)# no routing
ArubaCX2(config-if)# vlan access 20
ArubaCX2(config)# int lag 1
ArubaCX2(config-lag-if)# vlan trunk allowed all 
ArubaCX2(config-lag-if)# vlan trunk native 1
ArubaCX2(config-lag-if)# end
```
## ArubaCX3
```go
ArubaCX3# conf
ArubaCX3(config)# vlan 10,20,30
ArubaCX3(config-vlan-<10,20,30>)# exit
ArubaCX3(config)# int 1/1/1
ArubaCX3(config-if)# no shutdown
ArubaCX3(config-if)# no routing
ArubaCX3(config-if)# vlan access 10
ArubaCX3(config-if)# int 1/1/2
ArubaCX3(config-if)# no shutdown
ArubaCX3(config-if)# no routing
ArubaCX3(config-if)# vlan access 30
ArubaCX3(config)# int lag 2
ArubaCX3(config-lag-if)# vlan trunk allowed all 
ArubaCX3(config-lag-if)# vlan trunk native 1
ArubaCX3(config-lag-if)# end
```
## Show Commands
```go
ArubaCX1# show vlan 

----------------------------------------------------------------------------------------------------------------
VLAN  Name                              Status  Reason                  Type      Interfaces                    
----------------------------------------------------------------------------------------------------------------
1     DEFAULT_VLAN_1                    up      ok                      default   lag1-lag2
10    VLAN10                            up      ok                      static    lag1-lag2
20    VLAN20                            up      ok                      static    lag1-lag2
30    VLAN30                            up      ok                      static    lag1-lag2
```
# Configuración DHCP
## ArubaCX1
```go
ArubaCX1# conf
ArubaCX1(config)# int vlan 10
ArubaCX1(config-if-vlan)# ip add 10.0.10.1/24
ArubaCX1(config-if-vlan)# int vlan 20
ArubaCX1(config-if-vlan)# ip add 10.0.20.1/24
ArubaCX1(config-if-vlan)# int vlan 30
ArubaCX1(config-if-vlan)# ip add 10.0.30.1/24
ArubaCX1(config-if-vlan)# exit
ArubaCX1(config)# dhcp-server vrf default 
ArubaCX1(config-dhcp-server)# pool Vlan10 
ArubaCX1(config-dhcp-server-pool)# range 10.0.10.10 10.0.10.254 prefix-len 24
ArubaCX1(config-dhcp-server-pool)# default-router 10.0.10.1 
ArubaCX1(config-dhcp-server-pool)# dns-server 8.8.8.8 8.8.4.4
ArubaCX1(config-dhcp-server-pool)# domain-name arubacx.lab 
ArubaCX1(config-dhcp-server-pool)# lease 00:12:00
ArubaCX1(config-dhcp-server-pool)# exit
ArubaCX1(config-dhcp-server)# pool Vlan20 
ArubaCX1(config-dhcp-server-pool)# range 10.0.20.10 10.0.20.254 prefix-len 24
ArubaCX1(config-dhcp-server-pool)# default-router 10.0.20.1 
ArubaCX1(config-dhcp-server-pool)# dns-server 8.8.8.8 8.8.4.4
ArubaCX1(config-dhcp-server-pool)# domain-name arubacx.lab 
ArubaCX1(config-dhcp-server-pool)# lease 00:12:00
ArubaCX1(config-dhcp-server-pool)# exit
ArubaCX1(config-dhcp-server)# pool Vlan30 
ArubaCX1(config-dhcp-server-pool)# range 10.0.30.10 10.0.30.254 prefix-len 24
ArubaCX1(config-dhcp-server-pool)# default-router 10.0.30.1 
ArubaCX1(config-dhcp-server-pool)# dns-server 8.8.8.8 8.8.4.4
ArubaCX1(config-dhcp-server-pool)# domain-name arubacx.lab 
ArubaCX1(config-dhcp-server-pool)# lease 00:12:00
ArubaCX1(config-dhcp-server-pool)# exit 
ArubaCX1(config-dhcp-server)# authoritative 
ArubaCX1(config-dhcp-server)# enable
ArubaCX1(config-dhcp-server)# end
```
## Show commands
```go
ArubaCX1# show dhcp-server 

VRF Name           : default
DHCP Server        : enabled
Operational State  : operational
Authoritative Mode : true
```
## Pruebas de conectidad
## VPC5
```go
VPCS> ip dhcp
DORA IP 10.0.20.102/24 GW 10.0.20.1
```
## VPC4
```go
VPCS> ip dhcp
DORA IP 10.0.10.101/24 GW 10.0.10.1

VPCS> ping 10.0.20.102

84 bytes from 10.0.20.102 icmp_seq=1 ttl=63 time=14.362 ms
84 bytes from 10.0.20.102 icmp_seq=2 ttl=63 time=12.545 ms
84 bytes from 10.0.20.102 icmp_seq=3 ttl=63 time=9.995 ms
84 bytes from 10.0.20.102 icmp_seq=4 ttl=63 time=9.489 ms
84 bytes from 10.0.20.102 icmp_seq=5 ttl=63 time=12.892 ms
```