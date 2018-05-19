# Ejercicios tema 5

## 5.1

Se pueden ver las conexiones de muchas maneras diferentes, algunas son:

`netstat | grep http | wc -l`

`netstat -na`

`netstat -an | grep :80 | sort`

`netstat -an | grep :443 | sort`

`netstat -n -p | grep SYN_REC | sort -u`

`apache2ctl status | grep request `



## 5.2

He instalado un Apache en mi portátil y he hecho las peticiones desde mi PC de sobremesa. Para monitorizar el tráfico he usado el filtro:

`ip.dst == 192.168.1.48 && tcp-port == 80`

![](https://github.com/harvestcore/SWAP/blob/master/ejercicios/tema5/images/wireshark.jpg)

## 5.3

Existen muchas herramientas para monitorear sistemas, algunas son las siguientes:

- Línea de comandos
  - top
  - htop
  - vmstat
  - netstat
- GUI
  - Nagios
  - Zabbix
  - Netdata
  - Ganglia
  - CollectD
  - Cacti
  - Monitor.us
  - AppOptics

