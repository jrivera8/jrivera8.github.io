---
layout: single
title: IPSEC ENTRE CISCO Y FORTINET
excerpt: "Tunel IPSec entre Cisco y Fortinet"
date: 2022-03-31
classes: wide
header:
  teaser: assets/images/ipsec-cisco-fortinet/11.png
  teaser_home_page: true
categories:
  - cisco
  - fortinet
tags:  
  - cisco
  - fortinet
---
## TOPOLOGIA
![](/assets/images/ipsec-cisco-fortinet/11.png)
## CONFIGURACION BASICA
## INTERNET
```go
Router(config)#host INTERNET
INTERNET(config-if)#int g0/0
INTERNET(config-if)#ip add 100.100.100.2 255.255.255.252
INTERNET(config-if)#no sh
INTERNET(config-if)#int g0/1
INTERNET(config-if)#ip add 200.200.200.1 255.255.255.252
INTERNET(config-if)#no sh
INTERNET(config-if)#int g0/2
INTERNET(config-if)#ip add 8.8.8.1 255.255.255.0
INTERNET(config-if)#no sh
INTERNET(config)#int g0/3
INTERNET(config-if)#ip add 50.50.50.1 255.255.255.252
INTERNET(config-if)#no sh
INTERNET(config-if)#end
```
## FORTIGATE
```go
FortiGate-VM64-KVM # conf sys global
FortiGate-VM64-KVM (global) # set hostname SUCURSAL-2
FortiGate-VM64-KVM (global) # end
SUCURSAL-2 # conf sys interface 
SUCURSAL-2 (interface) # edit port1
SUCURSAL-2 (port1) # append allowaccess http
SUCURSAL-2 (port1) # set mode static
SUCURSAL-2 (port1) # set ip 50.50.50.2/30
SUCURSAL-2 (port1) # set alias WAN-1
SUCURSAL-2 (port1) # show
config system interface
    edit "port1"
        set vdom "root"
        set ip 50.50.50.2 255.255.255.252
        set allowaccess ping https ssh http fgfm
        set type physical
        set alias "WAN-1"
        set snmp-index 1
    next
end
SUCURSAL-2 (port1) # next 
SUCURSAL-2 (interface) # edit port2
SUCURSAL-2 (port2) # set mode static
SUCURSAL-2 (port2) # set ip 30.30.30.1/24
SUCURSAL-2 (port2) # set alias LAN-MGMT
SUCURSAL-2 (port2) # set allowaccess ping https ssh http fgfm
SUCURSAL-2 (port2) # show
config system interface
    edit "port2"
        set vdom "root"
        set ip 30.30.30.1 255.255.255.0
        set allowaccess ping https ssh http fgfm
        set type physical
        set alias "LAN-MGMT"
        set snmp-index 2
    next
end
SUCURSAL-2 # end
```
## NAT
## R1
```go
Router(config)#host R1
R1(config)#int g0/0
R1(config-if)#ip add 100.100.100.1 255.255.255.252
R1(config-if)#no sh
R1(config-if)#int g0/1
R1(config-if)#ip add 10.10.10.1 255.255.255.0
R1(config-if)#exit
R1(config)#ip route 0.0.0.0 0.0.0.0 100.100.100.2
R1(config)#ip access-list extended NAT
R1(config-ext-nacl)#permit ip 10.10.10.0 0.0.0.255 any
R1(config-ext-nacl)#exit
R1(config)#ip nat inside source list NAT int g0/0 overload 
R1(config)#int g0/1
R1(config-if)#ip nat inside
R1(config-if)#int g0/0
R1(config-if)#ip nat outside
R1(config-if)#exit
```
```sh
root@KaliLinuxCLI-1:/# ping 8.8.8.8 -c 5             
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=62 time=5.56 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=62 time=2.47 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=62 time=3.43 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=62 time=3.03 ms
64 bytes from 8.8.8.8: icmp_seq=5 ttl=62 time=2.82 ms
--- 8.8.8.8 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4004ms
rtt min/avg/max/mdev = 2.475/3.465/5.562/1.093 ms
```
## IPSEC
## R1
#### PHASE-1
```go
R1(config)#crypto isakmp policy 1
R1(config-isakmp)#encryption aes 256
R1(config-isakmp)#hash sha512
R1(config-isakmp)#authentication pre-share 
R1(config-isakmp)#group 16
R1(config-isakmp)#exit
R1(config)#crypto isakmp key SuperPass add 50.50.50.2
```
#### PHASE-2
```go
R1(config)#ip access-list extended NAT
R1(config-ext-nacl)#5 deny ip 10.10.10.0 0.0.0.255 30.30.30.0 0.0.0.255
R1(config-ext-nacl)#exit
R1(config)#ip access-list extended VPN
R1(config-ext-nacl)#permit ip 10.10.10.0 0.0.0.255 30.30.30.0 0.0.0.255
R1(config-ext-nacl)#exit
R1(config)#crypto ipsec transform-set SET-SUCURSAL2 esp-aes 256 esp-sha512-hmac 
R1(cfg-crypto-trans)#mode tunnel 
R1(cfg-crypto-trans)#exit
R1(config)#crypto map MAP-SUCURSAL2 1 ipsec-isakmp 
R1(config-crypto-map)#match address VPN
R1(config-crypto-map)#set peer 50.50.50.2
R1(config-crypto-map)#set transform-set SET-SUCURSAL2
R1(config-crypto-map)#set pfs group16
R1(config-crypto-map)#exit
R1(config)#int g0/0
R1(config-if)#crypto map MAP-SUCURSAL2
R1(config-if)#exit
```
```go
R1#show crypto session 
Crypto session current status

Interface: GigabitEthernet0/0
Session status: UP-ACTIVE     
Peer: 50.50.50.2 port 500 
  Session ID: 0  
  IKEv1 SA: local 100.100.100.1/500 remote 50.50.50.2/500 Active 
  IPSEC FLOW: permit ip 10.10.10.0/255.255.255.0 30.30.30.0/255.255.255.0 
        Active SAs: 2, origin: crypto map
```
## FORTIGATE
#### PHASE-1
![](/assets/images/ipsec-cisco-fortinet/1.png)
![](/assets/images/ipsec-cisco-fortinet/2.png)
![](/assets/images/ipsec-cisco-fortinet/3.png)
#### PHASE-2
![](/assets/images/ipsec-cisco-fortinet/4.png)
![](/assets/images/ipsec-cisco-fortinet/5.png)
#### RUTA ESTATICA
![](/assets/images/ipsec-cisco-fortinet/6.png)
#### POLITICAS
![](/assets/images/ipsec-cisco-fortinet/10.png)
![](/assets/images/ipsec-cisco-fortinet/7.png)
#### VERIFICAMOS EL TUNEL IPSEC
![](/assets/images/ipsec-cisco-fortinet/8.png)
#### COMPROBAMOS CON UN PING
#### PC ADMIN-2
![](/assets/images/ipsec-cisco-fortinet/9.png)
#### PC KALI
```sh
root@KaliLinuxCLI-1:/# ping 30.30.30.30 -c 5
PING 30.30.30.30 (30.30.30.30) 56(84) bytes of data.
64 bytes from 30.30.30.30: icmp_seq=1 ttl=62 time=9.61 ms
64 bytes from 30.30.30.30: icmp_seq=2 ttl=62 time=7.06 ms
64 bytes from 30.30.30.30: icmp_seq=3 ttl=62 time=9.52 ms
64 bytes from 30.30.30.30: icmp_seq=4 ttl=62 time=8.52 ms
64 bytes from 30.30.30.30: icmp_seq=5 ttl=62 time=9.64 ms

--- 30.30.30.30 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss
```

