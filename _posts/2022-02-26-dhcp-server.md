---
layout: single
title: DHCP Server en GNS3
excerpt: "Aprederemos a como configurar nuestro servidor DHCP en GNS3 con un appliance descargado de Marketplace"
date: 2022-02-26
classes: wide
header:
  teaser: /assets/images/dhcp-server/Captura de pantalla 2022-02-26 114744.png
  teaser_home_page: true
categories:
  - cisco
tags:  
  - linux
  - cisco
---

## Escenario en GNS3

![](/assets/images/vlans-intervlans-switchmultilayer/Captura de pantalla 2022-02-26 113722.png)

## Instalamos Appliance Toollkit

Ingresamos a [GNS3-Marketplace](https://gns3.com/marketplace/appliances/networkers-toolkit) lo descargamos e instalamos en nuestro GNS3.

![](/assets/images/dhcp-server/Captura de pantalla 2022-02-26 095653.png)

> En caso no sepan instalar appliances en GNS3 les recomiento buscar tutoriales en YouTube que no es complicado!

## Asignamos una IP a nuestro servidor DHCP

```go
root@DHCP:~# ip add add 192.168.20.100/24 dev eth0
root@DHCP:~# ip route add default via 192.168.20.1
```

```go
root@DHCP:~# ip add
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
7: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UNKNOWN group default qlen 1000
    link/ether c6:c6:f5:bd:ef:08 brd ff:ff:ff:ff:ff:ff
    inet 192.168.20.100/24 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::c4c6:f5ff:febd:ef08/64 scope link 
       valid_lft forever preferred_lft forever
```

```go
root@DHCP:~# ping -c 1 192.168.20.1
PING 192.168.20.1 (192.168.20.1) 56(84) bytes of data.
64 bytes from 192.168.20.1: icmp_seq=1 ttl=255 time=11.2 ms

--- 192.168.20.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 11.169/11.169/11.169/0.000 ms
```

## Configuramos nuestro servidor DHCP

### 1. Vamos a la siguiente ruta y hacemos un backup al archivo `dhcpd.conf`

```go
root@DHCP:~# cd /etc/dhcp/
root@DHCP:/etc/dhcp# ls -l
total 12
drwxr-x--- 2 root dhcpd 4096 Apr  8  2021 ddns-keys
-rw-r--r-- 1 root root  3646 Mar  9  2021 dhcpd.conf
-rw-r--r-- 1 root root  3331 Mar  9  2021 dhcpd6.conf
```

```go
root@DHCP:/etc/dhcp# mv dhcpd.conf dhcpd.conf.backup
root@DHCP:/etc/dhcp# ls -l
total 12
drwxr-x--- 2 root dhcpd 4096 Apr  8  2021 ddns-keys
-rw-r--r-- 1 root root  3646 Mar  9  2021 dhcpd.conf.backup
-rw-r--r-- 1 root root  3331 Mar  9  2021 dhcpd6.conf
```

### 2. Ahora creamos nuestro archivo `dhcpd.conf` y lo personalizamos

```go
root@DHCP:/etc/dhcp# touch dhcpd.conf 
root@DHCP:/etc/dhcp# nano dhcpd.conf
```

### Copiamos lo siguiente en el archivo `dhcpd.conf` y lo guardamos:

```go
default-lease-time 600;
max-lease-time 7200;

subnet 192.168.199.0 netmask 255.255.255.0 {
  range 192.168.199.101 192.168.199.254;
  option domain-name-servers 192.168.199.200;
  option routers 192.168.199.1;
}

default-lease-time 600;
max-lease-time 7200;

subnet 192.168.10.0 netmask 255.255.255.0 {
  range 192.168.10.10 192.168.10.254;
  option domain-name-servers 192.168.199.200;
  option routers 192.168.10.1;
}

default-lease-time 600;
max-lease-time 7200;

subnet 192.168.20.0 netmask 255.255.255.0 {
  range 192.168.20.10 192.168.20.254;
  option domain-name-servers 192.168.199.200;
  option routers 192.168.20.1;
}
```

#### 3. Ahora modificamos el archivo `isc-dhcp-server` que está en la siguiente ruta y asignamos la interfaz por donde comenzara a responder las solicitudes de DHCP, en nuestro caso la interfaz en la `eth0`

```go
root@DHCP:/etc/dhcp# nano /etc/default/isc-dhcp-server
```

```go
# Defaults for isc-dhcp-server (sourced by /etc/init.d/isc-dhcp-server)

# Path to dhcpd's config file (default: /etc/dhcp/dhcpd.conf).
#DHCPDv4_CONF=/etc/dhcp/dhcpd.conf
#DHCPDv6_CONF=/etc/dhcp/dhcpd6.conf

# Path to dhcpd's PID file (default: /var/run/dhcpd.pid).
#DHCPDv4_PID=/var/run/dhcpd.pid
#DHCPDv6_PID=/var/run/dhcpd6.pid

# Additional options to start dhcpd with.
# Don't use options -cf or -pf here; use DHCPD_CONF/ DHCPD_PID instead
#OPTIONS=""

# On what interfaces should the DHCP server (dhcpd) serve DHCP requests?
# Separate multiple interfaces with spaces, e.g. "eth0 eth1".
INTERFACESv4="eth0"
INTERFACESv6=""
```

## Activamos nuestro servidor DHCP

> Por defecto el servidor DHCP esta inactivo asi que procedemos a activarlo

```go
root@DHCP:~# service isc-dhcp-server status
Status of ISC DHCPv4 server: dhcpd is not running.
```

```go
root@DHCP:~# service isc-dhcp-server start
Launching IPv4 server only.
 * Starting ISC DHCPv4 server dhcpd                                                                                [ OK ] 
```

## Ahora configuramos DHCP Relay

```go
L3SW1(config)#int vlan 10
L3SW1(config-if)#ip helper-address 192.168.20.100 
L3SW1(config-if)#int vlan 20                     
L3SW1(config-if)#ip helper-address 192.168.20.100
L3SW1(config-if)#exit
```

## Validamos que nuestro servidor DHCP este funcionando

#### 1. En la PC-TAC ingresamos a la ruta /etc/network/interfaces y descomentamos las dos ultimas líneas para configuración por DHCP

```go
root@DHCP:~# nano /etc/network/interfaces
```

```go
#
# This is a sample network config uncomment lines to configure the network
#


# Static config for eth0
#auto eth0
#iface eth0 inet static
#       address 192.168.0.2
#       netmask 255.255.255.0
#       gateway 192.168.0.1
#       up echo nameserver 192.168.0.1 > /etc/resolv.conf

# DHCP config for eth0
 auto eth0
 iface eth0 inet dhc
```

#### 2. Apagamos y encendemos la PC-TAC

```go
root@TAC:~# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.10.11  netmask 255.255.255.0  broadcast 0.0.0.0
        ether 76:1a:f8:e0:a9:6f  txqueuelen 1000  (Ethernet)
        RX packets 7  bytes 1284 (1.2 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 4  bytes 864 (864.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

#### 3. Visualizamos los mensajes DHCP intercambiamos entre el cliente y servidor

```go
root@DHCP:/var/log# tail syslog 
Feb 26 15:57:26 DHCP dhcpd[231]: Wrote 0 leases to leases file.
Feb 26 15:57:26 DHCP dhcpd[231]: Server starting service.
Feb 26 16:09:55 DHCP dhcpd[231]: DHCPDISCOVER from 12:9a:eb:09:c8:2c via 192.168.10.1
Feb 26 16:09:56 DHCP dhcpd[231]: DHCPOFFER on 192.168.10.10 to 12:9a:eb:09:c8:2c via 192.168.10.1
Feb 26 16:09:56 DHCP dhcpd[231]: DHCPREQUEST for 192.168.10.10 (192.168.20.100) from 12:9a:eb:09:c8:2c via 192.168.10.1
Feb 26 16:09:56 DHCP dhcpd[231]: DHCPACK on 192.168.10.10 to 12:9a:eb:09:c8:2c via 192.168.10.1
Feb 26 16:10:31 DHCP dhcpd[231]: DHCPDISCOVER from 76:1a:f8:e0:a9:6f via 192.168.10.1
Feb 26 16:10:32 DHCP dhcpd[231]: DHCPOFFER on 192.168.10.11 to 76:1a:f8:e0:a9:6f via 192.168.10.1
Feb 26 16:10:32 DHCP dhcpd[231]: DHCPREQUEST for 192.168.10.11 (192.168.20.100) from 76:1a:f8:e0:a9:6f via 192.168.10.1
Feb 26 16:10:32 DHCP dhcpd[231]: DHCPACK on 192.168.10.11 to 76:1a:f8:e0:a9:6f via 192.168.10.1
```

**Servido**