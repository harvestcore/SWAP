#trabajo xd

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

## 2. Hardware

<div id='id2' />

El hardware del que dispongo y que utilizaré para toda la instalación es el siguiente:

| System |      Raspberry PI 3B (x2)       |             Node (x2)             |
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

Un **hipervisor** una plataforma que permite aplicar diversas técnicas de control de [virtualización](https://es.wikipedia.org/wiki/Virtualizaci%C3%B3n) para utilizar, al mismo tiempo, diferentes [sistemas operativos](https://es.wikipedia.org/wiki/Sistema_operativo) (sin modificar o modificados, en el caso de [paravirtualización](https://es.wikipedia.org/wiki/Paravirtualizaci%C3%B3n)) en una misma computadora. 

### 	Tipo 1

![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\hypervisor1.png)

También denominado *nativo*, *unhosted* o *bare metal*, es software que se ejecuta directamente sobre el hardware, para ofrecer la funcionalidad descrita. Algunos ejemplos de este tipo son VMware ESXi, Hyper-V, Oracle VM ... y el que voy a utilizar, **XenServer 7.4**, que es de software libre y sin licencia alguna.



### 	Tipo 2

![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\hypervisor2.png)

También denominado *hosted*, es software que se ejecuta sobre un sistema operativo para ofrecer la funcionalidad descrita. Ejemplos de este tipo son VirtualBox, VMware Workstation, QEMU ...



#### Instalación y configuración de XenServer

Una vez tenemos la imagen montada en un USB y conectada al servidor comienza a cargar el instalador de XenServer.

> **Nota**: Las capturas de la instalación de XenServer han sido tomadas en una VM en mi máquina principal, pues me era imposible capturar la instalación real en el servidor.

Lo primero que nos pide es la distribución de teclado que queremos usar:

![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\install\1.PNG)

Tras esto se acepta el EULA y se selecciona el disco donde se va a instalar Xen:

![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\install\2.PNG)

Ahora introducimos un password para el root del sistema:

![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\install\3.PNG)

Seleccionamos la interfaz de red que queremos usar para el management del sistema; en mi caso utilizo la que ofrece menor velocidad (10/100):

![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\install\4.PNG)

Y ahora podemos hacer dos cosas, o dejar que el servidor DHCP le asigne una ip, o asignarle una IP estática. Yo le asigno 192.168.1.125.

![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\install\5.PNG)

Finalmente introducimos servidores DNS que deseemos, así como un nombre para el host, yo he elegido *Juggernaut*.

![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\install\6.PNG)

Tras esto comenzaría la instalación, que tras finalizar y reiniciarse la máquina nos muestra lo siguiente:

![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\install\7.PNG)

Es la única interfaz que tiene XenServer en local. En ella se pueden hacer algunas configuraciones, pero no todas las que necesitamos. XenCenter es un programa bastante interesante que complementa XenServer

![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\xencenter\1.PNG)

En mi caso lo he instalado en Windows, pero también tiene versión para Linux. Lo primero es añadir un servidor:

![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\xencenter\2.PNG)

Una vez añadido lo veremos en el menú lateral. Si clicamos en él podremos ver un resumen de sus características y demás. La organización en pestañas facilita mucho la gestión de las diferentes partes del servidor.

En la pestaña *Networking* podremos configurar las interfaces de red que tiene el servidor:

![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\xencenter\3.PNG)

Debemos pulsar en el boton de la parte inferior, *Configure...*

![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\xencenter\4.PNG)

... y asignar una IP a cada interfaz (o usar DHCP). Cabe destacar una cosa importante, la ip que aquí asignemos se va a usar solo internamente en Xen, es decir, más tarde tendremos que configurar una IP en cada máquina virtual.

Una vez configuradas las interfaces pasamos a añadir un servidor de almacenamiento para almacenar ahí las ISO de los SO que vayamos a instalar. Podemos también conectar una unidad flash o HDD al servidor, pero en mi caso veo más sencillo añadir el servidor de almacenamiento (que en realidad es una carpeta compartida en mi PC principal).

Seleccionamos el tipo de almacenamiento (Windows File Sharing en mi caso):

![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\addstorage\1.PNG)

Le ponemos un nombre:

![2](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\addstorage\2.PNG)

Y nos conectamos a él:

![3](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\addstorage\3.PNG)

Una vez configurado todo esto ya está preparado XenServer para albergar máquinas virtuales.



## 4. HTTP Server

<div id='id4' />

Cada nodo del servidor web tiene como sistema operativo Ubuntu Server 16.04.3 y cuenta con un servidor web (LAMP) instalado por defecto, sin configuración alguna. La instalación de las máquinas en el hypervisor es muy sencilla ya que XenCenter facilita el proceso bastante.

Clickamos en *New VM* y comenzamos a configurarla. Lo primero es seleccionar el tipo de SO.

![1](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\createvm\1.PNG)

Le asignamos un nombre:

![2](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\createvm\2.PNG)

Seleccionamos una ISO, que previamente ha sido almacenada en el servidor de almacenamiento antes configurado.

![3](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\createvm\3.PNG)

Seleccionamos el host donde instalar la VM. Observamos que nos indica la RAM que podemos asignarle a la máquina.

![4](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\createvm\4.PNG)

Seleccionamos los cores que le queremos asignar a la VM. La topología es interesante comentarla, pues se pueden tener configuraciones curiosas. Por ejemplo, si queremos que nuestra VM tenga 2 cores, podemos hacer que se comporten como 2 procesadores con 1 core cada uno; o 1 procesador con dos cores. A mayor número de cores las configuraciones se incrementan.

También le asignamos la RAM que deseemos, en mi caso cada nodo cuenta con 1 core y 2GB de RAM.

![5](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\createvm\5.PNG)

Seleccionamos dónde queremos instalar la VM. Este disco puede estar en cualquier lugar, es decir, puede ser un disco local que tenga el servidor internamente o puede estar en un servidor remoto (al igual que cuando configurabamos el servidor de almacenamiento para las imágenes ISO).

![6](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\createvm\6.PNG)

En mi caso instalo las VM en local, asignándoles 15GB de almacenamiento a cada una.

![7](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\createvm\7.PNG)

Además les asigno una interfaz de red, siguiendo el siguiente esquema:

- Network 0 - 192.168.1.141 - WEB01
- Network 1 - 192.168.1.142 - WEB01
- Network 2 - 192.168.1.125 - Management

> Nota: Estas IP las he asignado para configurar Xen y las VM desde mi máquina principal (dentro de la red 192.168.1.0), más tarde configuraré Xen, firewall y balanceador en una red interna 10.10.10.0.

![8](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\createvm\8.PNG)

Al final nos muestra un resumen de cómo queda la VM:

![9](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\createvm\9.PNG)



Una vez hecho todo esto para ambas VM podemos arrancarlas e instalar Ubuntu Server, el proceso es sencillo puesto que se requiere una instalación por defecto.

Finalmente, con toda la anterior configuración terminada podremos ver un resumen del servidor:

![3](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\3.PNG)

Por otro lado también podemos comprobar el rendimiento en tiempo real del servidor, para ello abrimos la pestaña *Performance*:

![4](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\XEN\4.PNG)



Llegados a este punto podemos comprobar si funcionan los servidores web:

- WEB01

  ![web01](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\https\web01.PNG)

  ​

- WEB02

  ![web02](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\https\web02.PNG)



La web que se ve se encuentra explicada [aquí](#id12).



## 5. Networking

<div id='id5' />

El esquema de la red es el siguiente:

![networking](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\networking.jpg)

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

  ![web01](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\networking\web01.PNG)

- WEB02

  ![web02](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\networking\web02.PNG)

- WEB Host

  ![jugger](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\networking\jugger.PNG)

- Load Balancer

  ![loadbalancer](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\networking\loadbalancer.PNG)

- Firewall. En este caso, como la Raspberry Pi 3B sólo tiene un adaptador de red ethernet, he utilizado un adaptador USB a ethernet, que es el que uso como enlace a la red interna.

  ![firewall](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\networking\firewall.PNG)

Finalmente, todo el barullo de cables queda tal que así:

![setup](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\networking\setup.jpg)

Siendo la Raspberry Pi superior el firewall y la inferior el balanceador de carga. A la izquierda de la imagen se ve el adaptador USB-Ethernet que uso (es 3.0 y gigabit, pero la RPI al no tener puertos USB 3.0 no puedo aprovechar todo su potencial).



## 6. Load Balancer

<div id='id6' />

Como balanceador de carga elijo HAProxy, ya que es el que más fácil me resulta configurar y el que mejor resultado dio en los benchmarks que realicé [aquí](https://github.com/harvestcore/SWAP/blob/master/practicas/p3/p3.md).

Tengo en cuenta las siguientes cosas:

- La web cuenta con un login, por lo que la persistencia de las sesiones es vital.
- La potencia de los nodos es exactamente la misma.
- Quiero añadirle un certificado SSL.

La configuración, bastante verde todavía, de HAProxy es la siguiente:

![loadbalancer](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\loadbalancer\loadbalancer.PNG)

He configurado sólo para que funcione con HTTP. El peso que le he dado a cada máquina es 1, por lo que usará un algoritmo round-robin (destacar también que la web no tiene contenido pesado, por lo que RR es adecuado como algoritmo). Por otro lado le he puesto que utilice una cookie para las sesiones. Una vez un usuario acceda a la web se guardará en su navegador una cookie con el identificador del servidor web donde ha hecho login. Ahora será siempre el mismo nodo el que sirva a este cliente.

Ahora podemos comprobar que funciona:![workinglb](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\loadbalancer\workinglb.PNG)

Gracias al No-IP tengo un dominio para el servidor web [jugger.sytes.net](http://jugger.sytes.net), por lo que abriendo el puerto 80 en el router puedo acceder al servidor:

![workingurl](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\loadbalancer\workingurl.PNG)





## 7. Firewall

<div id='id7' />

activar forwardign



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

	​	

## 8. SSL

<div id='id8' />

## 9. Benchmarking

<div id='id10' />

## 10. Web

<div id='id11' />

La web que he diseñado y programado es [ésta](https://jugger.sytes.net), programada en PHP y con base de datos en MySQL. El código de la misma se puede encontrar [aquí](https://github.com/harvestcore/harvest-web).

![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\web.PNG)

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

![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\home.PNG)

La única utilidad que tiene es ser la página principal una vez ha hecho login un usuario. También se puede acceder aquí sin estar logueado, pero las funcionalidades están restringidas. Además incluyo un logo.

Tiene un menú superior dinámico, que cambia en función del usuario que acceda a la web.

- Usuario administrador: Permite añadir/eliminar servidores y códigos, además de permitir a los usuarios subir o no archivos al repositorio.

  ![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\images\admin.png)

  ​

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




Y ya estaría. El sistema en su conjunto funciona perfectamentamente. No se trata de un sistema de altas prestaciones, pero pienso que para el uso que va a tener (mis amigos y yo) es más que suficiente. El sistema debería escalar bien para el tráfico que le vamos a dar.



## 11. Referencias

<div id='id12' />

https://es.wikipedia.org/wiki/Hipervisor

https://es.wikipedia.org/wiki/VMware_ESXi

https://www.vmware.com/products/esxi-and-esx.html

https://www.youtube.com/watch?v=-KmgwQORAX8