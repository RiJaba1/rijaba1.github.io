---
layout: single
title: Herramienta whichSystem.sh
date: 2021-12-16
classes: wide
tags:
  - Herramienta
  - Hacking
  - RiJaba1
categories:
  - Herramientas
---
## Utilidad
Esta herramienta nos permita averiguar el SO (Sistema Operativo) de una equipo sin hacer ruido y de manera instantánea.

Para ello esta herramienta hace uso del comando `ping` disponible en todas las versiones de Linux de manera predeterminada.

<p align="center">
<img src="/assets/images/herramienta-whichSystem.sh/1.PNG">
</p>

## Uso de la herramienta
```
┌─[root@rijaba1-pc]─[~]
└──╼ #git clone https://github.com/rijaba1/whichSystem.sh
Clonando en 'whichSystem.sh'...
remote: Enumerating objects: 24, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 24 (delta 0), reused 0 (delta 0), pack-reused 21
Recibiendo objetos: 100% (24/24), 5.75 KiB | 5.75 MiB/s, listo.
Resolviendo deltas: 100% (4/4), listo.

┌─[root@rijaba1-pc]─[~]
└──╼ #cd whichSystem.sh/

┌─[root@rijaba1-pc]─[~/whichSystem.sh]
└──╼ #chmod +x whichSystem.sh 

┌─[root@rijaba1-pc]─[~/whichSystem.sh]
└──╼ #./whichSystem.sh 

 - Inserte la IP: 127.0.0.1

El TTL es: 64

  [+] Linux

┌─[root@rijaba1-pc]─[~/whichSystem.sh]
└──╼ #
```

## GitHub

Link de la herramienta: <a href="https://github.com/RiJaba1/whichSystem.sh" target="_blank">https://github.com/RiJaba1/whichSystem.sh</a>
