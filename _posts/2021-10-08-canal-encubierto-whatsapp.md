---
layout: single
title: Canal encubierto WhatsApp
excerpt: En este post aprenderemos a crear un canal encubierto en Whastsapp, usando Heroku.
date: 2021-10-08
classes: wide
tags:
  - WhatsApp
  - Heroku
  - Canal encubierto
---

En el siguiente artículo os mostraré la manera de mantener una conversaciñón privado entre dos personas sin que quede ningún tipo de registro en WhatsApp.

## Requerimientos
Antes de iniciar tenemos que hacer las siguientes cosas:

* Instalar [Heroku](https://www.heroku.com/) una herramienta que nos permitirá crear un servidor gratis ```go snap install heroku --classic ``` o podeis ver más formas [aquí](https://devcenter.heroku.com/articles/heroku-cli)
* Crear una cuenta en https://heroku.com
* Crear un proyecto en Heroku de la siguiente forma: Create proyect > Ingresar nombre proyecto > Save Changes

Una vez hecho esto tienes que iniciar sesión en Heroku desde la terminal con el comando ```go heroku login``` y devería dar un outup como el siguiente:
```go
┌─[root@parrot]─[/home/rijaba1/Desktop]
└──╼ #heroku login
heroku: Press any key to open up the browser to login or q to exit:
Opening browser to https://cli-auth.heroku.com/auth/cli/browser/35e6XXXX-XXXX-XXXX-87d0-bf7ff75XXXXX
heroku: Waiting for login... ⣟
```
Una vez hecho este comado se os debería abrir una ventana de navegador para iniciar sesión, si no sale simplmente teneis que pegar la indicada en un navegador.
Al final os dará un outup como la siguiente:
```go
┌─[root@parrot]─[/home/rijaba1/Desktop]
└──╼ #heroku login
heroku: Press any key to open up the browser to login or q to exit: 
Opening browser to https://cli-auth.heroku.com/auth/cli/browser/35e6XXXX-XXXX-XXXX-87d0-bf7ff75XXXXX
Logging in... done
Logged in as tuemail@mail.com
```

## Creación del canal encubierto

En una ruta crearemos una carpeta en al cual alojaremos los archivos que necesitamos.
Dentro de la carpeta crearemos un archivo index.html como el siguiente

  ```go
  ┌─[root@parrot]─[/home/rijaba1/Desktop]
  └──╼ #mkdir Archivos

  ┌─[root@parrot]─[/home/rijaba1/Desktop]
  └──╼ #cd Achivos

  ┌─[root@parrot]─[/home/rijaba1/Desktop/Archivos]
  └──╼ #echo '<p>Test</p>' > index.html

  ```

Este pequeño archivos nos servirá para poder hacer una petición al servidor.

El siguiente paso es ejecutar ```go heroku logs -t -a NOMBRE-PROYECTO```

<p align="center">
<img src="/assets/images/whatsapp-canal-encubierto/Img1.jpg">
</p>

Llegados a este punto solo necesitamos *escrbir* un mensaje con `http://NOMBRE-PROYECTO.herokuapp.com/MENSAJE-AQUÍ`

<p align="center">
<img src="/assets/images/whatsapp-canal-encubierto/Img3.jpg">

</p>
<p align="center">
<img src="/assets/images/whatsapp-canal-encubierto/Img2.jpg">
</p>  


## ¿Por qué sucede esto?
Siempre que ingreses una URL a WhatsApp, se cargará la web en segundo plano para poder ofrecernos la previsualización del sitio. Al poner el comando de Heroku lo que conseguimos es que nos muestre todas las peticiones, incluyendo las de WhatsApp.

