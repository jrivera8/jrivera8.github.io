---
layout: single
title: Uso de la librería Paramiko en redes
excerpt: "Aumatización de redes con python."
date: 2022-02-04
classes: wide
header:
  teaser: /assets/images/personalizar-terminal/logo.png
  teaser_home_page: true
categories:
  - cisco
tags:  
  - automatizacion
  - cisco
  - python
---

## Intalamos la librería Paramiko

```sh
pip install paramiko
```

## Escenario en GNS3

![](/assets/images/automatizacion-cisco-python-1/1.png)

## Configuramos la IP de cada Router

```go
R1#conf t
R1(config)#int g0/0
R1(config-if)#ip add 192.168.1.31 255.255.255.0
R1(config-if)#no sh
R1(config-if)#exit
```

```go
R2#conf t
R2(config)#int g0/0
R2(config-if)#ip add 192.168.1.30 255.255.255.0
R2(config-if)#no sh
R2(config-if)#exit
```

## Configuramos SSH en cada Router

```go
R1#conf t
R1(config)#hostname R1
R1(config)#ip domain-name cisco.com
R1(config)#crypto key generate rsa modulus 1024
R1(config)#username admin priv 15 secret admin
R1(config)#line vty 0 4
R1(config)#login local
R1(config)#transport input ssh
R1(config)#exit
```

```go
R2#conf t
R2(config)#hostname R2
R2(config)#ip domain-name cisco.com
R2(config)#crypto key generate rsa modulus 1024
R2(config)#username admin priv 15 secret admin
R2(config)#line vty 0 4
R2(config)#login local
R2(config)#transport input ssh
R2(config)#exit
```
## Librería Paramiko

Usamos `paramiko` para conectarnos a los routers vía `SSH` y obtener cualquier información de los equipos.

#### 1. R1

```python
import paramiko

# Agregamos la dirección IP y las credenciales SSH
r1_ip = "192.168.1.31"
r1_username = "admin"
r1_password = "admin"

ssh = paramiko.SSHClient()

# Agregamos SSH host key automáticamente si es necesario.
ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())

# Conectamos al router usando credenciales.
ssh.connect(r1_ip, 
            username=r1_username, 
            password=r1_password,
            look_for_keys=False )

# Ejecutamos un comando.
ssh_stdin, ssh_stdout, ssh_stderr = ssh.exec_command("dir")

#Obtenemos la salido de nuestro comando.
output = ssh_stdout.read().decode()
print(output)

# Cerramos la conección.
ssh.close()
```

Ejecutamos el código y obtenemos la siguiente salida:

```go
PS C:\Users\joser\Documents\AUTOMATIZACION CON PYTHON> python .\ssh-cisco-ios.py

Directory of flash0:/

    1  drw-           0  Jan 30 2013 00:00:00 +00:00  boot
  264  drw-           0  Oct 14 2013 00:00:00 +00:00  config
  267  -rw-   143178592  Mar 22 2016 00:00:00 +00:00  vios-adventerprisek9-m
  268  -rw-      524288   Feb 3 2022 18:07:22 +00:00  nvram
  269  -rw-          79   Feb 3 2022 18:07:26 +00:00  e1000_bia.txt

2142715904 bytes total (1994403840 bytes free)
```

#### 2. R2

```python
import paramiko

# Agregamos la dirección IP y las credenciales SSH
r2_ip = "192.168.1.30"
r2_username = "admin"
r2_password = "admin"

ssh = paramiko.SSHClient()

# Agregamos SSH host key automáticamente si es necesario.
ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())

# Conectamos al router usando credenciales.
ssh.connect(r1_ip, 
            username=r2_username, 
            password=r2_password,
            look_for_keys=False )

# Ejecutamos un comando.
ssh_stdin, ssh_stdout, ssh_stderr = ssh.exec_command("show ip route")

#Obtenemos la salido de nuestro comando.
output = ssh_stdout.read().decode()
print(output)

# Cerramos la conección.
ssh.close()
```

Ejecutamos el código y obtenemos la siguiente salida:

```go
PS C:\Users\joser\Documents\AUTOMATIZACION CON PYTHON> python .\ssh-cisco-ios.py

Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override, p - overrides from PfR

Gateway of last resort is 192.168.1.1 to network 0.0.0.0

S*    0.0.0.0/0 [254/0] via 192.168.1.1
      192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.1.0/24 is directly connected, GigabitEthernet0/0
L        192.168.1.30/32 is directly connected, GigabitEthernet0/0
```

**Gracias**