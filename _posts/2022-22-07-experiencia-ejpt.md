---
layout: single
title: Mi experiencia con el eJPT + TIPS
excerpt: En este artículo comentaré mi experiencia con la certificación eJPT y daré unos tips para aprobarlo de manera sencilla.
date: 2022-07-22
classes: wide
header:
  teaser: /assets/images/eJPT/eJPT.png
  teaser_home_page: true
tags:
  - eJPT
  - Certificación
  - Curso
  - Tips
  - Experiencia
  - Hacking
  - RiJaba1
---

Hace un par de semanas me certifiqué del ElearnSecurity Junior Penetration Tester (eJPT), en este artículo daré mi experiencia con el curso y con el examen, también comentaré mi conocimiento previo antes del examen y la mejor manera de prepararse para el examen con unos pequeños tips.

## Antecedentes

Personalmente ya tenía ciertos conocimientos en el sector del hacking, con más de 150 máquinas completadas entre las plataformas de [HackTheBox](https://app.hackthebox.com/users/719273) y [HackMyVM](https://hackmyvm.eu/profile/?user=RiJaba1), también disponía de conocimientos de Linux. Esto me permitió poder hacer el examen de una manera tranquila y sin requerir de demasiado esfuerzo.

## El curso
El [curso de preparación para el eJPT](https://my.ine.com/path/a223968e-3a74-45ed-884d-2d16760b8bbd), impartido por el INE, es 100% gratuito en la parte teórica y práctica. Como cualquier curso tiene sus puntos buenos y sus puntos malos.
El curso dispone de diapositivas, vídeos explicativos de las herramientas y laboratorios prácticos de preparación, con estos recursos deberías estar preparado para certificarte del eJPT sin ningún problema.

En las siguientes listas expondré los _pros y contras_ del curso:

**_Puntos malos:_**
1. **Muchas** diapositivas con **teoría** que no hacen falta para certificarse.
2. Explicación de **herramientas obsoletas**.
3. Empleo de **versiones antiguas** para explicar herramientas.
4. No te permiten conectarte a los laboratorios mediante VPN _(sólo con el Kali del navegador)_.
5. Las **Black-Boxes** del final del curso **no** se corresponden con la dificultad del **examen**, están bien para practicar conocimientos y técnicas alternativas _(Metasploit)_ pero no se corresponden para nada con el examen _(el examen es más fácil)_.

**_Puntos buenos:_**
1. **Teoría de refuerzo** que ayuda a reforzar conocimientos.
2. Módulos de programación.
3. **Bastantes laboratorios** para practicar herramientas específicas y conceptos generales.
4. Obviamente que sea **gratis**.

El curso contiene los siguientes módulos:
- Prerequisites
- Preliminary Skills & Programming
- Penetration Testing Basics
- eJPT Preparation
    
De estos módulos, es muy recomendable **acabar “eJPT Preparation”**, en este pondrás en práctica los conocimientos adquiridos durante el curso. Los módulos de programación no son necesarios para el examen, pero son recomendables para ir cogiendo soltura en el campo del scripting.

## El examen

El examen tiene un precio de **$200** (a mi me costó $100 gracias a un descuento), en cambio a otras certificaciones como el eCPPT **no** hay que entregar ningún **informe** sobre la intrusión, en este examen sólo hay que contestar **20 preguntas tipo test de**: respuesta múltiple, cuatro opciones y verdadero o falso. Para pasar el examen de manera exitosa hay que responder como **mínimo 15 preguntas bien** _(o cinco mal para suspender)_.
**_Las preguntas se dividen en los siguientes tipos:_**
- Preguntas sobre la red.
- Preguntas web.
- Preguntas post-explotación.
    
Las preguntas para mi gusto más difíciles fueron las de red, posiblemente provocado a la poca experiencia que tenía en comparación a otras preguntas que son tipo más CTF (Capture the Flag).

Cuándo empiezas el examen, te dan unos recursos para descargar:
1. Carta de explicación.
2. Lista de usuarios.
3. Lista de contraseñas.
4. Captura del tráfico de red para abrir con WireShark.

Es **_MUY_** importante leerse de manera detallada la carta, no tienes prisa y te ayudará mucho para responder las preguntas. En la captura de red no hay muchísima información importante, pero va a ser importante analizarla para hacer _un proceso necesario en el examen_. La lista de usuarios y contraseñas ya sabes para qué sirve.

En cuanto a mi experiencia en el propio examen tengo varias cosas que comentar, lo detallaré en los siguientes puntos:
- Ya sabía que el examen **no era un CTF**, pero hasta que lo vives no sabes cómo es la experiencia de una auditoría simulada.
- Tardé tres horas y media en terminar el examen, lo que es muy poco en comparación con el tiempo que te dan, por eso **no te tienes que dar prisa** en ningún momento, tómate el examen con calma _(así de paso lo intentas disfrutar)_.
- Este punto es malo, pero tengo que decirlo porque fue una cosa que me disgustó mucho en el examen, el **laboratorio va bastante lento**, al principio creí que podía ser culpa mía, pero no lo era. Para evitar problemas os recomiendo que no escaneéis todos los puertos TCP (65535), con los 5000 más comunes (con 5000 vais de sobra, pero mejor asegurar), el examen está diseñado para que se hagan **escaneos específicos**. Otra cosa es evitar el multi-tasking, mejor no hagáis varias cosas a la vez, **centraros en una tarea**. Con estos consejos evitareis ralentizaros durante el proceso de la certificación. 

Cabe aclarar que en este examen no hay monitorización y puedes consultar, y hacer, cualquier cosa que quieras.

## Recomendaciones para el examen
1. **Anota todo**, todo es todo. Te ayudará a no hacerte un lío, recuerda, esto no es un CTF.
2. Enumera. Es una de las partes más importante para el examen.
3. Intenta explotar todo de manera manual. A veces es complicado, pero si tienes la opción acostúmbrate a hacerlo de manera manual, te ayudará para otras certificaciones como el OSCP.
4. **Tómate descansos**. En varios momentos del examen es posible que te atasques, es ese momento descansa un poco.
5. **No tengas miedo**. Al principio verás que no es cómo un CTF y no sabrás por dónde empezar, es normal, nos ha pasado a todos, sólo vete poco a poco e irás descubriendo las cosas.
6. Una cosa que me ayudó mucho es leer antes del examen artículos como este.

## Cómo prepararse para el eJPT
Este es el momento que estabais esperando, os diré mis recomendaciones para el examen.
1. Haz las **máquinas Blue de HTB y THM** _(se llaman igual)_, estas máquinas te ayudarán a una cosa muy importante para el examen.
2. Haz el laboratorio **“Find the secret server”** del curso del INE.
3. Entiende y aprende a usar estas herramientas
  - Nmap ← Muy importante
  - Hydra
  - Sqlmap
  - BurpSuite
4. Haz varias máquinas con dificultad fácil de las plataformas de [HackTheBox](https://app.hackthebox.com/users/719273), [HackMyVM](https://hackmyvm.eu/profile/?user=RiJaba1) y [TryHackMe](https://tryhackme.com/p/RiJaba1). Con esto conseguirás muy buena base para el examen.
5. Practica con los laboratirios del INE networking.
6. Cómo ya comenté antes, lee varios artículos sobre la experiencia de otras personas.
   
   
   
## Despedida
    
Espero que con estos consejos podais aprobar el eJPT de manera más sencilla y sin mucho esfuerzo.
