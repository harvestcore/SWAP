# Servidores web de altas prestaciones en entornos virtualizados

## Índice

1. [Introducción](#id1)
2. [Hardware](#id2)
3. [Hypervisor](#id3)
4. [HTTP Server](#id4)
5. [Networking](#id5)
6. [Load Balancer](#id6)
7. [Firewall](#id7)
8. [SSL](#id8)
9. [Benchmarking](#id10)
10. [Web](#id11)
11. [Referencias](#id12)



## 1. Introducción

<div id='id1' />

La idea de este trabajo surgió una tarde que me encontraba jugando online con unos amigos. Necesitabamos un pc para hostear una serie de juegos y ya de paso un lugar común donde tener las IP, puertos y configuraciones de estos servidores. Tuve la feliz idea de convertir todas aquellas ideas en algo real que pudieramos usar cuanto antes y que además me serviera como tema para el trabajo de la asignatura SWAP.

Lo que muestro a continuación, más que un servidor de juegos, sería la web (tanto front como backend) que se utilizará para lo descrito anteriormente. Por otro lado, puesto que el tema de la virtualización me atrae bastante, el los nodos (2) del servidor web estarán en máquinas virtuales sobre el hipervisor XenServer.

Destacar también que ya que tenía un par de Raspberry Pi 3B rondando por casa, las he utilizado como balanceador de carga y como firewall.

## 2. Hardware

<div id='id2' />

El hardware del que dispongo y que utilizaré para toda la instalación es el siguiente:

| System |      Raspberry PI 3B (x2)       |             Web Host              |
| :----: | :-----------------------------: | :-------------------------------: |
|  CPU   | 4C/4T @ 1.2GHz Broadcom BCM2837 | 4C/4T @ 4.3GHz Intel Core i5-4430 |
|  MoBo  |           Proprietary           |          Gigabyte H81M-C          |
|  RAM   |       1GB @ 900MHz LPDDR2       |        8GB @ 1600MHz DDR3         |
|  SSD   |        16GB Flash Drive         |               240GB               |
|  HDD   |              none               |               none                |
|  GPU   |      Broadcom VideoCore IV      |           NVidia GT 630           |
|  NIC   |            1x 10/100            |       2x Gigabit, 1x 10/100       |
|   SO   |          Raspbian 4.9           |          Ubuntu 16.04.3           |

## 3. Hypervisor

<div id='id3' />

Un **hipervisor** una plataforma que permite aplicar diversas técnicas de control de virtualización para utilizar, al mismo tiempo, diferentes sistemas operativos (sin modificar o modificados, en el caso de paravirtualización) en una misma máquina. Presentan a los sistemas operativos virtualizados (sistemas invitados) una plataforma operativa virtual (hardware virtual), a la vez que ocultan a dicho sistema operativo virtualizado las características físicas reales del equipo sobre el que operan.

### 	Tipo 1

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/hypervisor1.png)

También denominado *nativo*, *unhosted* o *bare metal*, es software que se ejecuta directamente sobre el hardware, para ofrecer la funcionalidad descrita. Algunos ejemplos de este tipo son VMware ESXi, Hyper-V, Oracle VM ... y el que voy a utilizar, **XenServer 7.4**, que es de software libre y sin licencia alguna.



### 	Tipo 2

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/hypervisor2.png)

También denominado *hosted*, es software que se ejecuta sobre un sistema operativo para ofrecer la funcionalidad descrita. Ejemplos de este tipo son VirtualBox, VMware Workstation, QEMU ...



#### Instalación y configuración de XenServer

Una vez tenemos la imagen montada en un USB y conectada al servidor comienza a cargar el instalador de XenServer.

> **Nota**: Las capturas de la instalación de XenServer han sido tomadas en una VM en mi máquina principal, pues me era imposible capturar la instalación real en el servidor.

Lo primero que nos pide es la distribución de teclado que queremos usar:

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/install/1.PNG)

Tras esto se acepta el EULA y se selecciona el disco donde se va a instalar Xen:

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/install/2.PNG)

Ahora introducimos un password para el root del sistema:

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/install/3.PNG)

Seleccionamos la interfaz de red que queremos usar para el management del sistema; en mi caso utilizo la que ofrece menor velocidad (10/100):

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/install/4.PNG)

Y ahora podemos hacer dos cosas, o dejar que el servidor DHCP le asigne una ip, o asignarle una IP estática. Yo le asigno 192.168.1.125.

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/install/5.PNG)

Finalmente introducimos servidores DNS que deseemos, así como un nombre para el host, yo he elegido *Juggernaut*.

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/install/6.PNG)

Tras esto comenzaría la instalación, que tras finalizar y reiniciarse la máquina nos muestra lo siguiente:

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/install/7.PNG)

Es la única interfaz que tiene XenServer en local. En ella se pueden hacer algunas configuraciones, pero no todas las que necesitamos. XenCenter es un programa bastante interesante que complementa XenServer.

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/xencenter/1.PNG)

En mi caso lo he instalado en Windows, pero también tiene versión para Linux. Lo primero es añadir un servidor:

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/xencenter/2.PNG)

Una vez añadido lo veremos en el menú lateral. Si clicamos en él podremos ver un resumen de sus características y demás. La organización en pestañas facilita mucho la gestión de las diferentes partes del servidor.

En la pestaña *Networking* podremos configurar las interfaces de red que tiene el servidor:

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/xencenter/3.PNG)

Debemos pulsar en el boton de la parte inferior, *Configure...*

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/xencenter/4.PNG)

... y asignar una IP a cada interfaz (o usar DHCP). Cabe destacar una cosa importante, la ip que aquí asignemos se va a usar solo internamente en Xen, es decir, más tarde tendremos que configurar una IP en cada máquina virtual.

Una vez configuradas las interfaces pasamos a añadir un servidor de almacenamiento para almacenar ahí las ISO de los SO que vayamos a instalar. Podemos también conectar una unidad flash o HDD al servidor, pero en mi caso veo más sencillo añadir el servidor de almacenamiento (que en realidad es una carpeta compartida en mi PC principal).

Seleccionamos el tipo de almacenamiento (Windows File Sharing en mi caso):

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/addstorage/1.PNG)

Le ponemos un nombre:

![2](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/addstorage/2.PNG)

Y nos conectamos a él:

![3](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/addstorage/3.PNG)

Una vez configurado todo esto ya está preparado XenServer para albergar máquinas virtuales.



## 4. HTTP Server

<div id='id4' />

Cada nodo del servidor web tiene como sistema operativo Ubuntu Server 16.04.3 y cuenta con un servidor web (LAMP) instalado por defecto, sin configuración alguna. La instalación de las máquinas en el hypervisor es muy sencilla ya que XenCenter facilita el proceso bastante.

Clickamos en *New VM* y comenzamos a configurarla. Lo primero es seleccionar el tipo de SO.

![1](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/createvm/1.PNG)

Le asignamos un nombre:

![2](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/createvm/2.PNG)

Seleccionamos una ISO, que previamente ha sido almacenada en el servidor de almacenamiento antes configurado.

![3](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/createvm/3.PNG)

Seleccionamos el host donde instalar la VM. Observamos que nos indica la RAM que podemos asignarle a la máquina.

![4](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/createvm/4.PNG)

Seleccionamos los cores que le queremos asignar a la VM. La topología es interesante comentarla, pues se pueden tener configuraciones curiosas. Por ejemplo, si queremos que nuestra VM tenga 2 cores, podemos hacer que se comporten como 2 procesadores con 1 core cada uno; o 1 procesador con dos cores. A mayor número de cores las configuraciones se incrementan.

También le asignamos la RAM que deseemos, en mi caso cada nodo cuenta con 1 core y 2GB de RAM.

![5](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/createvm/5.PNG)

Seleccionamos dónde queremos instalar la VM. Este disco puede estar en cualquier lugar, es decir, puede ser un disco local que tenga el servidor internamente o puede estar en un servidor remoto (al igual que cuando configurabamos el servidor de almacenamiento para las imágenes ISO).

![6](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/createvm/6.PNG)

En mi caso instalo las VM en local, asignándoles 15GB de almacenamiento a cada una.

![7](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/createvm/7.PNG)

Además les asigno una interfaz de red, siguiendo el siguiente esquema:

- Network 0 - 192.168.1.141 - WEB01
- Network 1 - 192.168.1.142 - WEB01
- Network 2 - 192.168.1.125 - Management

> Nota: Estas IP las he asignado para configurar Xen y las VM desde mi máquina principal (dentro de la red 192.168.1.0), más tarde configuraré Xen, firewall y balanceador en una red interna 10.10.10.0.

![8](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/createvm/8.PNG)

Al final nos muestra un resumen de cómo queda la VM:

![9](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/createvm/9.PNG)



Una vez hecho todo esto para ambas VM podemos arrancarlas e instalar Ubuntu Server, el proceso es sencillo puesto que se requiere una instalación por defecto.

Finalmente, con toda la anterior configuración terminada podremos ver un resumen del servidor:

![3](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/3.PNG)

Por otro lado también podemos comprobar el rendimiento en tiempo real del servidor, para ello abrimos la pestaña *Performance*:

![4](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/XEN/4.PNG)



Llegados a este punto podemos comprobar si funcionan los servidores web:

- WEB01

  ![web01](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/https/web01.PNG)


- WEB02

  ![web02](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/https/web02.PNG)



La web que se ve se encuentra explicada [aquí](#id12).



## 5. Networking

<div id='id5' />

El esquema de la red es el siguiente:

![networking](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/networking.jpg)

Las IP asignadas que se muestran en el esquema son las siguientes:

|    Máquina    |             IP             |
| :-----------: | :------------------------: |
|   Firewall    | 192.168.1.120 / 10.10.10.2 |
| Load Balancer |         10.10.10.3         |
|     WEB01     |         10.10.10.4         |
|     WEB02     |         10.10.10.5         |
|      Xen      |        10.10.10.10         |

Utilizo dos switch en la configuración de red, el primero (192.168.1.100) es gestionable, el segundo no. Éste último es el que uso para la red interna del servidor web. La elección de la ubicación de cada switch está pensada con antelación. Teniendo el switch gestionable antes del firewall del servidor web puedo tener más control aún sobre el tráfico que va hacia éste, además de poder crear VLANs, limitar la velocidad, etc.

Por otro lado, como desde mi ordenador personal sólo tengo conexión al firewall, para realizar cualquier tipo de configuración en las máquinas la hago desde mi ordenador portátil, conectandolo con un cable de red al switch de la red interna web.

La configuración de IP que realizo ahora es la siguiente:

- WEB01

  ![web01](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/networking/web01.PNG)

- WEB02

  ![web02](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/networking/web02.PNG)

- WEB Host

  ![jugger](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/networking/jugger.PNG)

- Load Balancer

  ![loadbalancer](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/networking/loadbalancer.PNG)

- Firewall. En este caso, como la Raspberry Pi 3B sólo tiene un adaptador de red ethernet, he utilizado un adaptador USB a ethernet, que es el que uso como enlace a la red interna.

  ![firewall](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/networking/firewall.PNG)

Finalmente, todo el barullo de cables queda tal que así:

![setup](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/networking/setup.jpg)

Siendo la Raspberry Pi superior el firewall y la inferior el balanceador de carga. A la izquierda de la imagen se ve el adaptador USB-Ethernet que uso (es 3.0 y gigabit, pero la RPI al no tener puertos USB 3.0 no puedo aprovechar todo su potencial).



## 6. Load Balancer

<div id='id6' />

Como balanceador de carga elijo HAProxy, ya que es el que más fácil me resulta configurar y el que mejor resultado dio en los benchmarks que realicé [aquí](https://github.com/harvestcore/SWAP/blob/master/practicas/p3/p3.md).

Tengo en cuenta las siguientes cosas:

- La web cuenta con un login, por lo que la persistencia de las sesiones es vital.
- La potencia de los nodos es exactamente la misma.
- Quiero añadirle un certificado SSL.

La configuración, bastante verde todavía, de HAProxy es la siguiente:

![loadbalancer](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/loadbalancer/loadbalancer.PNG)

He configurado sólo para que funcione con HTTP. El peso que le he dado a cada máquina es 1, por lo que usará un algoritmo round-robin (destacar también que la web no tiene contenido pesado, por lo que RR es adecuado como algoritmo). Por otro lado le he puesto que utilice una cookie para las sesiones. Una vez un usuario acceda a la web se guardará en su navegador una cookie con el identificador del servidor web donde ha hecho login. Ahora será siempre el mismo nodo el que sirva a este cliente.

Ahora podemos comprobar que funciona:

![workinglb](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/loadbalancer/workinglb.PNG)

Gracias a No-IP tengo un dominio para el servidor web [jugger.sytes.net](http://jugger.sytes.net), por lo que abriendo el puerto 80 en el router puedo acceder al servidor:

![workingurl](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/loadbalancer/workingurl.PNG)


## 7. Firewall

<div id='id7' />

Una de las Raspberry Pi 3B la he configurado como firewall. Ésta es la que recibe todo el tráfico ya que se sitúa delante del balanceador de carga. Como sistema operativo tiene Raspbian 4.9, sin ningún software específico instalado. La configuración como firewall la he realizado con iptables usando el siguiente script:

```bash
#!/bin/bash

if [ "$EUID" -ne 0  ]
	then echo "Please run as root."
	exit
else
	# Borrar todas las reglas que hubiera previamente
	iptables -F
	iptables -X
	iptables -Z
	iptables -t nat -F

	# Política por defecto (denegar todo)
	iptables -P INPUT DROP
	iptables -P OUTPUT DROP
	iptables -P FORWARD DROP

	# Permitir forwarding
	iptables -P FORWARD ACCEPT

	# Permitir loopback
	iptables -A INPUT -i lo -j ACCEPT
	iptables -A OUTPUT -o lo -j ACCEPT

	# Permitir ssh
	iptables -A INPUT -p tcp --dport 22 -m state --state NEW,ESTABLISHED  -j ACCEPT
	iptables -A OUTPUT -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT

	# Permitir puerto 80 (http)
	iptables -A INPUT -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
	iptables -A OUTPUT -p tcp --sport 80 -m state --state ESTABLISHED -j ACCEPT

	# Permitir puerto 443 (https)
	iptables -A INPUT -p tcp --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT
	iptables -A OUTPUT -p tcp --sport 443 -m state --state ESTABLISHED -j ACCEPT

	# Permitir ping (desde fuera hacia dentro)
	iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT
	iptables -A OUTPUT -p icmp --icmp-type echo-reply -j ACCEPT

	# Permitir ping (desde dentro hacia fuera)
	iptables -A OUTPUT -p icmp --icmp-type echo-request -j ACCEPT
	iptables -A INPUT -p icmp --icmp-type echo-reply -j ACCEPT

	# Permitir dns
	iptables -A OUTPUT -p udp --dport 53 -m state --state NEW,ESTABLISHED -j ACCEPT
	iptables -A INPUT -p udp --sport 53 -m state --state ESTABLISHED -j ACCEPT
	iptables -A OUTPUT -p tcp --dport 53 -m state --state NEW,ESTABLISHED -j ACCEPT
	iptables -A INPUT -p tcp --sport 53 -m state --state ESTABLISHED -j ACCEPT

	# Redireccionamiento http y https
	iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to 10.10.10.3:80
	iptables -A FORWARD -d 10.10.10.3 -p tcp --dport 80 -j ACCEPT
	iptables -t nat -A PREROUTING -p tcp --dport 443 -j DNAT --to 10.10.10.3:443
	iptables -A FORWARD -d 10.10.10.3 -p tcp --dport 443 -j ACCEPT
	iptables -t nat -A POSTROUTING -j MASQUERADE

fi
```

Resumiendo: Como política por defecto he denegado todo tipo de conexión y a partir de aquí he ido permitiendo lo que me interesa.

- Permito el forwarding (para HTTP y HTTPS).
- Permito el tráfico (entrada y salida) del puerto 22 (para SSH).
- Permito el tráfico (entrada y salida) del puerto 80 (para HTTP).
- Permito el tráfico (entrada y salida) del puerto 443 (para HTTPS).
- Permito el tráfico (entrada y salida) del puerto 53 (para DNS).
- Redirecciono todo el tráfico que entra por los puertos 80 y 443 a los puertos 80 y 443 del balanceador de carga.


Para que el forwarding funcione es necesario descomentar la siguiente línea del archivo */etc/sysctl.conf*:

```bash
net.ipv4.ip_forward=1
```

Finalmente, para que este script se ejecute cada vez que arranque el sistema incluyo al archivo */etc/rc.local* lo siguiente:

```bash
sudo ./scripts/firewall.sh
```

> Nota: El script lo he guardado en /scripts.

## 8. SSL

<div id='id8' />

He configurado un certificado SSL de CertBot (Let's Encrypt) para el servidor web. Lo primero que hice fue descargar el instalador que crea el certificado:

```bash
wget https://dl.eff.org/certbot-auto
chmod a+x certbot-auto
```

Y luego ejecutarlo, pero con los flags para que solo genere el cerficado:

```bash
certbot-auto certonly --standalone
```

Una vez tengo el cerficado creado (se almacena en */etc/letsencrypt*), creo la siguiente carpeta y la protejo:

```bash
sudo mkdir -p /etc/haproxy/ssl
sudo chmod -R go-rwx /etc/haproxy/ssl
```

HAProxy necesita en un solo archivo la clave privada y la cadena, para unirlos y guardar el archivo en */etc/haproxy/ssl* ejecuto:

```bash
DOMAIN='jugger.sytes.net' sudo -E bash -c 'cat /etc/letsencrypt/live/$DOMAIN/fullchain.pem /etc/letsencrypt/live/$DOMAIN/privkey.pem > /etc/haproxy/ssl/$DOMAIN.pem'
```

Finalmente modifico el archivo de configuración para incluir el certificado SSL:
![configssl](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/loadbalancer/configssl.PNG)

Por último compruebo que el puerto 443 está abierto tanto en los nodos como en el balanceador. Tras eso observamos que la web funciona perfectamente, con un certificado SSL seguro.

![workingssl](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/loadbalancer/workingssl.PNG)

![workingssl2](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/loadbalancer/workingssl2.png)

## 9. Benchmarking

<div id='id10' />

Para comprobar el rendimiento del servidor he realizado una serie de benchmarks con AB, haciendo las peticiones al firewall que hay frente al balanceador de carga, al archivo *index.html*. Los resultados los he resumido en la tabla siguiente:

| Concurrencia | Nº de peticiones | Pet/sec |  Kbps  |
| :----------: | :--------------: | :-----: | :----: |
|      1       |        10        | 1026.02 | 336.64 |
|      1       |       100        | 1733.38 | 358.86 |
|      1       |       1000       | 3091.84 | 640.11 |
|      1       |      10000       | 3191.76 | 660.8  |
|      1       |      100000      | 3212.64 | 665.12 |
|      10      |        10        | 2009.65 | 416.06 |
|      10      |       100        | 2070.35 | 428.63 |
|      10      |       1000       | 3634.74 | 752.5  |
|      10      |      10000       | 3877.75 | 802.81 |
|      10      |      100000      | 3873.87 | 802.01 |
|     100      |       100        | 1985.55 | 411.07 |
|     100      |       1000       | 3437.36 | 711.64 |
|     100      |      10000       | 3838.54 | 794.7  |
|     100      |      100000      | 3762.11 | 778.87 |
|     1000     |       1000       | 2519.38 | 521.59 |
|     1000     |      10000       | 3750.82 | 776.54 |
|     1000     |      100000      | 3892.27 | 806.65 |
|     1000     |     1000000      | 3928.16 | 813.25 |

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/benchmark/ps1.png)

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/benchmark/tr1.png)

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/benchmark/ps10.png)

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/benchmark/tr10.png)

![ps100](https://github.com/harvestcore/SWAP/blob/master/trabajo/benchmark/ps100.png)

![tr100](https://github.com/harvestcore/SWAP/blob/master/trabajo/benchmark/tr100.png)

![ps1000](https://github.com/harvestcore/SWAP/blob/master/trabajo/benchmark/ps1000.png)

![tr1000](https://github.com/harvestcore/SWAP/blob/master/trabajo/benchmark/tr1000.png)

Tiene una media de 3080.01 peticiones/segundo y una transferencia media de 637.65 Kbps, lo que es bastante respetable para el hardware sobre el que está montado el servidor web. Como se aprecia en las gráficas, una vez el tráfico aumenta en más de 1000 peticiones el rendimiento que ofrece aumenta considerablemente. Así mismo conforme aumenta también la concurrencia de usuarios el rendimiento también aumenta, aunque más levemente. Filtrando los datos (más de 1000 peticiones) obtenemos un promedio de 3539.63 peticiones/segundo y 723.81 Kbps.

## 10. Web

<div id='id11' />

La web que he diseñado y programado es [ésta](https://jugger.sytes.net), programada en PHP y con base de datos en MySQL. El código de la misma se puede encontrar [aquí](https://github.com/harvestcore/harvest-web).

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/web.PNG)

Sus funcionalidades principales son las siguientes:

- Login.
- Sistema de códigos para el registro.
- Almacena una tabla de direcciones IP.
- Permite almacenar archivos de hasta 5MB.
- Permite gestionar las IP, códigos y permisos de subida de archivos.



Las diferentes secciones que tiene son las siguientes:

#### Index

Es la página que se muestra al entrar en la web, se trata de una página de inicio que nos redirecciona a Home, Login, Insert Code, BotPerrete y Contact. Realmente no tiene más uso que éste. Quise hacer un index lo más ligero posible, además de claro y conciso. (El index es la imagen superior). 

#### Insert Code

Nos lleva a un formulario en el que tenemos que introducir un código (creados sólo por el admin) con los que podremos registrarnos en la web.

#### Botperrete

Nos lleva a una cuenta de Twitter que tengo con un bot. Cada vez que se accede al Apache se tweetea.

#### Home

Página principal "secundaria", por así llamarla.

![](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/home.PNG)

La única utilidad que tiene es ser la página principal una vez ha hecho login un usuario. También se puede acceder aquí sin estar logueado, pero las funcionalidades están restringidas. Además incluyo un logo.

Tiene un menú superior dinámico, que cambia en función del usuario que acceda a la web.

- Usuario administrador: Permite añadir/eliminar servidores y códigos, además de permitir a los usuarios subir o no archivos al repositorio.

  ![](https://github.com/harvestcore/SWAP/blob/master/trabajo/images/admin.png)


- Usuario regular: Le permite consultar la tabla de IP.

Por otro lado existen las secciones:

- Repositorio de archivos: Bastante simple, la verdad. Solo sirve para subir o bajar archivos.
- Specs: Especificaciones técnicas de las máquinas que conforman todo el sistema.
- Twitch: Contiene mi canal de Twitch embebido.
- Contact: Con mis datos de contacto.
- Logout: Botón para hacer logout.




Por otro lado, la base de datos se encuentra en el balanceador de carga (pues antes tenía esa Raspberry Pi como único servidor web). Ambos nodos acceden a esta BD. Para habilitar el acceso a la BD se ejecuta lo siguiente en mysql:

```mysql
GRANT ALL PRIVILEGES ON swap.* TO 'root'@'10.10.10.4' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON swap.* TO 'root'@'10.10.10.5' IDENTIFIED BY 'password';
```

También abro con ufw el puerto 3306 en los nodos.




Y ya estaría. El sistema en su conjunto funciona perfectamentamente. No se trata de un sistema de muy altas prestaciones, pero pienso que para el uso que va a tener (mis amigos y yo) es más que suficiente. El sistema debería escalar bien para el tráfico que le vamos a dar.



## 11. Referencias

<div id='id12' />

https://es.wikipedia.org/wiki/Hipervisor

https://es.wikipedia.org/wiki/VMware_ESXi

http://www.netstandard.com/virtual-servers-work

https://www.vmware.com/products/esxi-and-esx.html

https://www.youtube.com/watch?v=-KmgwQORAX8

https://fly.io/articles/load-balancing-https-with-lets-encrypt

https://es.wikipedia.org/wiki/Xen

https://www.citrix.es/products/xenserver

https://es.wikipedia.org/wiki/Paravirtualizaci%C3%B3n

https://jugger.sytes.net

