---
layout: single
title: ARUBA VLANS
excerpt: "Configuración básica de vlans-intevlans"
date: 2022-05-04
classes: wide
header:
  teaser: assets/images/Aruba-vlans/vlan-scenario-01.png
  teaser_home_page: true
categories:
  - aruba
tags:  
  - aruba
---
## Topología
![](/assets/images/Aruba-vlans/1.png)
## Configuramos el hostname y las vlans a cada Switch
#### Aruba-SW1
```go
switch# conf 
switch(config)# hostname Aruba-SW1
Aruba-SW1(config)# vlan 4,6,25,17,100    
Aruba-SW1(config-vlan-<4,6,25,17,100>)# exit
```
#### Aruba-SW2
```go
switch# conf
switch(config)# hostname Aruba-SW2
Aruba-SW2(config)# vlan 4,25
Aruba-SW2(config-vlan-<4,25>)# exit
```
#### Aruba-SW3
```go
switch# conf
switch(config)# hostname Aruba-SW3
Aruba-SW3(config)# vlan 6,17
Aruba-SW3(config-vlan-<6,17>)# exit
```
## Configuramos LAG 1 y le asignamos las vlans
#### Aruba-SW1
```go
Aruba-SW1(config)# interface lag 1
Aruba-SW1(config-lag-if)# no shutdown
Aruba-SW1(config-lag-if)# no routing
Aruba-SW1(config-lag-if)# vlan trunk native 25
Aruba-SW1(config-lag-if)# vlan trunk allowed 4,25
Aruba-SW1(config-lag-if)# exit
```
#### Aruba-SW2
```go
Aruba-SW2(config)# interface lag 1
Aruba-SW2(config-lag-if)# no shutdown
Aruba-SW2(config-lag-if)# no routing
Aruba-SW2(config-lag-if)# vlan trunk native 25
Aruba-SW2(config-lag-if)# vlan trunk allowed 4,25
Aruba-SW2(config-lag-if)# exit
```
## Agregamos los puertos a LAG 1
#### Aruba-SW1
```go
Aruba-SW1(config)# interface 1/1/1
Aruba-SW1(config-if)# no shutdown 
Aruba-SW1(config-if)# no routing
Aruba-SW1(config-if)# lag 1
Aruba-SW1(config-if)# interface 1/1/2
Aruba-SW1(config-if)# no shutdown    
Aruba-SW1(config-if)# no routing     
Aruba-SW1(config-if)# lag 1          
Aruba-SW1(config-if)# exit
```
#### Aruba-SW2
```go
Aruba-SW2(config)# interface 1/1/1 
Aruba-SW2(config-if)# no shutdown
Aruba-SW2(config-if)# no routing
Aruba-SW2(config-if)# lag 1
Aruba-SW2(config-if)# interface 1/1/2
Aruba-SW2(config-if)# no shutdown    
Aruba-SW2(config-if)# no routing     
Aruba-SW2(config-if)# lag 1          
Aruba-SW2(config-if)# exit
```
## Configuramos LAG 3 y le asignamos las vlans
#### Aruba-SW1
```go
Aruba-SW1(config)# interface lag 3
Aruba-SW1(config-lag-if)# no shutdown
Aruba-SW1(config-lag-if)# no routing
Aruba-SW1(config-lag-if)# vlan trunk native 6 tag 
Aruba-SW1(config-lag-if)# vlan trunk allowed 6,17
Aruba-SW1(config-lag-if)# exit
```
#### Aruba-SW3
```go
Aruba-SW3(config)# interface lag 3
Aruba-SW3(config-lag-if)# no shutdown
Aruba-SW3(config-lag-if)# no routing
Aruba-SW3(config-lag-if)# vlan trunk native 6 tag
Aruba-SW3(config-lag-if)# vlan trunk allowed 6,17
Aruba-SW3(config-lag-if)# exit
```
## Agregamos los puertos a LAG 3
#### Aruba-SW1
```go
Aruba-SW1(config)# interface 1/1/3
Aruba-SW1(config-if)# no shutdown
Aruba-SW1(config-if)# no routing 
Aruba-SW1(config-if)# lag 3
Aruba-SW1(config-if)# interface 1/1/4
Aruba-SW1(config-if)# no shutdown    
Aruba-SW1(config-if)# no routing     
Aruba-SW1(config-if)# lag 3
Aruba-SW1(config-if)# exit
```
#### Aruba-SW3
```go
Aruba-SW3(config)# interface 1/1/3
Aruba-SW3(config-if)# no shutdown
Aruba-SW3(config-if)# no routing
Aruba-SW3(config-if)# lag 3
Aruba-SW3(config-if)# interface 1/1/4
Aruba-SW3(config-if)# no shutdown
Aruba-SW3(config-if)# no routing
Aruba-SW3(config-if)# lag 3
Aruba-SW3(config-if)# exit
```
## Asignamos vlan a los puertos de acceso
#### Aruba-SW1
```go
Aruba-SW1(config)# interface 1/1/7
Aruba-SW1(config-if)# no shutdown
Aruba-SW1(config-if)# no routing 
Aruba-SW1(config-if)# vlan access 100
Aruba-SW1(config-if)# exit
```
#### Aruba-SW2
```go
Aruba-SW2(config)# interface 1/1/3
Aruba-SW2(config-if)# no shutdown
Aruba-SW2(config-if)# no routing
Aruba-SW2(config-if)# vlan access 25
Aruba-SW2(config-if)# interface 1/1/4
Aruba-SW2(config-if)# no shutdown
Aruba-SW2(config-if)# no routing
Aruba-SW2(config-if)# vlan access 4
Aruba-SW2(config-if)# exit
```
#### Aruba-SW3
```go
Aruba-SW3(config)# interface 1/1/1
Aruba-SW3(config-if)# no shutdown
Aruba-SW3(config-if)# no routing
Aruba-SW3(config-if)# vlan access 6
Aruba-SW3(config-if)# interface 1/1/2
Aruba-SW3(config-if)# no shutdown
Aruba-SW3(config-if)# no routing
Aruba-SW3(config-if)# vlan access 17
Aruba-SW3(config-if)# exit
```
## Configuración inter-vlan
```go
Aruba-SW1(config)# interface vlan 4
Aruba-SW1(config-if-vlan)# no shutdown
Aruba-SW1(config-if-vlan)# ip address 192.168.4.1/24
Aruba-SW1(config-if-vlan)# interface vlan 6        
Aruba-SW1(config-if-vlan)# no shutdown              
Aruba-SW1(config-if-vlan)# ip address 192.168.6.1/24 
Aruba-SW1(config-if-vlan)# interface vlan 17        
Aruba-SW1(config-if-vlan)# no shutdown              
Aruba-SW1(config-if-vlan)# ip address 192.168.17.1/24
Aruba-SW1(config-if-vlan)# interface vlan 25       
Aruba-SW1(config-if-vlan)# no shutdown              
Aruba-SW1(config-if-vlan)# ip address 192.168.25.1/24  
Aruba-SW1(config-if-vlan)# interface vlan 100        
Aruba-SW1(config-if-vlan)# no shutdown               
Aruba-SW1(config-if-vlan)# ip address 192.168.100.1/24  
Aruba-SW1(config-if-vlan)# exit
```
## Verificación de tabla de enrutamiento
```go
Aruba-SW1# show ip route | begin Prefix
Prefix              Nexthop          Interface     VRF(egress)       Origin/   Distance/    Age
                                                                     Type      Metric
---------------------------------------------------------------------------------------------------------
192.168.4.0/24      -                vlan4         -                 C         [0/0]        -            
192.168.4.1/32      -                vlan4         -                 L         [0/0]        -            
192.168.6.0/24      -                vlan6         -                 C         [0/0]        -            
192.168.6.1/32      -                vlan6         -                 L         [0/0]        -            
192.168.17.0/24     -                vlan17        -                 C         [0/0]        -            
192.168.17.1/32     -                vlan17        -                 L         [0/0]        -            
192.168.25.0/24     -                vlan25        -                 C         [0/0]        -            
192.168.25.1/32     -                vlan25        -                 L         [0/0]        -            
192.168.100.0/24    -                vlan100       -                 C         [0/0]        -            
192.168.100.1/32    -                vlan100       -                 L         [0/0]        -            

Total Route Count : 10
```
## Pruebas de conectividad
#### PC-1 a su gateway
root@PC-1:~# ping 192.168.25.1 -c 1
PING 192.168.25.1 (192.168.25.1) 56(84) bytes of data.
64 bytes from 192.168.25.1: icmp_seq=1 ttl=64 time=9.12 ms

--- 192.168.25.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 9.121/9.121/9.121/0.000 ms
> Se aprecia en la captura wireshark que el paquete sale sin etiqueta debido a que es la vlan nativa.
![](/assets/images/Aruba-vlans/2.png)
#### PC-1 a PC-2
root@PC-1:~# ping 192.168.4.10 -c 1
PING 192.168.4.10 (192.168.4.10) 56(84) bytes of data.
64 bytes from 192.168.4.10: icmp_seq=1 ttl=63 time=16.6 ms

--- 192.168.4.10 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 16.662/16.662/16.662/0.000 ms
> Se aprecia en la captura wireshark que el paquete "echo request" sale sin etiqueta debido a que es la vlan nativa.
![](/assets/images/Aruba-vlans/3.png)
> Al llegar al gateway se enruta hacia la vlan 4 por lo tanto al paquete "echo request" se le agrega una etiqueta con ID: 4.
> La respuesta de la PC-2 es un "echo reply" que se lo envia al gateway con una etiqueta ID: 4 para que lo enrute hacia la vlan 25.
![](/assets/images/Aruba-vlans/4.png)
> Al llegar al gateway se enruta hacia la vlan 25 y no se agrega una etiqueta debido a que es la vlan nativa.
![](/assets/images/Aruba-vlans/5.png)
#### PC-2 a PC-4
root@PC-2:~# ping 192.168.17.10 -c 1
PING 192.168.17.10 (192.168.17.10) 56(84) bytes of data.
64 bytes from 192.168.17.10: icmp_seq=1 ttl=63 time=18.8 ms

--- 192.168.17.10 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 18.896/18.896/18.896/0.000 ms
> El paquete "echo request" sale con una etiqueta ID: 4 hacia el gateway para luego ser enrutado hacia la vlan 17
![](/assets/images/Aruba-vlans/6.png)
> El paquete "echo request" al llegar al gateway es enrutado hacia la vlan 17 con una etiqueta ID: 17
> La respuesta de la PC-4 es un "echo reply" que se lo envia al gateway con una etiqueta ID: 17 para que lo enrute hacia la vlan 4.
![](/assets/images/Aruba-vlans/8.png)
> El paquete "echo reply" al llegar al gateway es enrutado hacia la vlan 4 con una etiqueta ID: 4
![](/assets/images/Aruba-vlans/7.png)
#### PC-3 hacia el gateway
root@PC-3:~# ping 192.168.6.1 -c 1
PING 192.168.6.1 (192.168.6.1) 56(84) bytes of data.
64 bytes from 192.168.6.1: icmp_seq=1 ttl=64 time=26.5 ms

--- 192.168.6.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 26.551/26.551/26.551/0.000 ms
> El paquete "echo request" es enviado al gateway con un etiqueta ID: 6 debio a que la vlan nativa se configuro con tag.
> El paquete "echo reply" respuesta del gateway hacia la pc de la vlan 6 con un etiqueta ID: 6
![](/assets/images/Aruba-vlans/9.png)