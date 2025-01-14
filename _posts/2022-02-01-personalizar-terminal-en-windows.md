---
layout: single
title: Personalizar la terminal en Windows
excerpt: "Mis apuntes para personalizar la terminal en Windows 10 y 11"
date: 2022-02-01
classes: wide
header:
  teaser: /assets/images/personalizar-terminal/logo.png
  teaser_home_page: true
categories:
  - linux
tags:  
  - wsl
  - linux
---

## Instalamos WSL 2

Windows Subsystem for Linux (WSL 2) lo instalamos en nuestro Windows de la siguiente manera:

```sh
wsl --install
```

![](/assets/images/personalizar-terminal/16.png)

Una vez que reiniciamos nuestro Windows la distribución que se instala por defecto es la de Ubuntu 20.04.

![](/assets/images/personalizar-terminal/17.png)

Antes de comenzar a personalizar nuestra terminal debemos tener instalado `curl` y `git`.

    sudo apt install curl
    sudo apt install git

## Instalamos zsh

```sh
sudo apt install zsh
```

![](/assets/images/personalizar-terminal/1.png)

## Instalamos [Oh My Zsh!](https://ohmyz.sh/#install)

Copiamos la siguiente línea en nuestra terminal:

```sh
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

![](/assets/images/personalizar-terminal/2.png)

## Instalamos los siguientes plugins

#### 1. [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions/blob/master/INSTALL.md#oh-my-zsh)

Clonamos el siguiente repositorio:

```sh
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

![](/assets/images/personalizar-terminal/3.png)

#### 2. [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md#oh-my-zsh)

Clonamos el siguiente repositorio:

```sh
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

![](/assets/images/personalizar-terminal/4.png)

## Instalamos el tema [PowerLevel10k](https://github.com/romkatv/powerlevel10k#oh-my-zsh)

Clonamos el siguiente repositorio:

```sh
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

![](/assets/images/personalizar-terminal/5.png)

Ahora configuramos el archivo `~/.zshrc`.

```sh
nano ~/.zshrc
```

Cambiamos el tema `"robbyrussell"` por `"powerlevel10k/powerlevel10k"`.

![](/assets/images/personalizar-terminal/6.png)

Líneas abajo agregamos los plugins que previamente instalamos:

![](/assets/images/personalizar-terminal/7.png)

Finalmente guardamos el archivo con `Crtl+o`, `Enter` y `Crtl+x` para salir.

## Instalamos la fuente

Descargamos el siguiente archivo [ttf](https://github.com/romkatv/powerlevel10k#manual-font-installation).

![](/assets/images/personalizar-terminal/8.png)

La instalamos para todos los usuarios.

![](/assets/images/personalizar-terminal/9.png)

Le damos click en `instalar`

![](/assets/images/personalizar-terminal/10.png)

## Configuramos nuestro PowerLevel10k

En un inicio no notamos los íconos.

![](/assets/images/personalizar-terminal/11.png)

Procedemos a cambiar la fuente para poder visualizar los íconos.

![](/assets/images/personalizar-terminal/12.png)

En `configuracion>Ubuntu>Apariencia>Tipo de Fuente` seleccionamos `MesloLGS NF`.

![](/assets/images/personalizar-terminal/13.png)

Ahora sí se notan los íconos.

![](/assets/images/personalizar-terminal/14.png)

Finalmente seguimos la siguiente secuencia `yyyy11112143121y1y` para obtener nuestra terminal personalizada.

![](/assets/images/personalizar-terminal/15.png)

**Servido**
