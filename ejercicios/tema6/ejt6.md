# Ejercicios tema 6

## 6.1

Para denegar todo el tráfico:

```bash
iptable -F
iptables -P INPUT DROP
iptables -P OUTPUT DROP
iptables -P FORWARD DROP
```

Para permitir todo el tráfico:

```bash
iptables -F
iptables -I INPUT -j ACCEPT
```



## 6.2

Para comprobarlo he usado `sudo netstat -tulnp`:

![](C:\Users\Angel\Dropbox\Universidad\Tercero\Segundo Cuatri\SWAP\ejercicios\tema6\images\puertos.png)



## 6.3

Tipos de ataques a sitios web y servidores:

- **DoS** - En un ataque de denegación de servicio (DoS), un atacante intenta evitar la legitimidad de que los usuarios accedan a información o al servicios. 
  - Prevención:
    - Aplicación de filtrado de enrutador.
    - Bloquear direcciones IP sin usar.
    - Permitir el acceso a la red solo al tráfico deseado.
    - Deshabilitar servicios de red innecesarios.
    - Actualización de antivirus regularmente.
    - Tener una muy buena política de contraseñas.
    - Limitar la cantidad de ancho de banda de la red.
    - Uso de la red de filtrado de acceso.
- **Ping flood** - Ping flood se basa en enviar a la víctima una cantidad abrumadora de paquetes ping, usualmente usando el comando "ping" de UNIX como hosts.
- **Ping de la muerte** - El atacante envía un paquete ICMP de más de 65.536 bytes. Como el sistema operativo no sabe cómo manejar un paquete tan grande, se congela o se cuelga en el momento de volver a montarlo. Hoy en día, el sistema operativo descarta dichos paquetes por sí mismo. 
- **Escaneo de puertos** - Todas las máquinas conectadas a una red de área local (LAN) o Internet ejecutan muchos servicios que escuchan en puertos conocidos y no tan conocidos. Un escaneo de puertos ayuda al atacante a encontrar qué puertos están disponibles (es decir, qué servicio podría estar enumerando un puerto). 
- **ARP Spoofing** - ARP Poison Routing (APR), es una técnica utilizada para atacar una red cableada o inalámbrica de Ethernet. ARP Spoofing puede permitir que un atacante detecte frameworks de datos en una red de área local (LAN), modifique el tráfico o detenga el tráfico por completo. El ataque solo se puede usar en redes que realmente usan ARP y no en otro método de resolución de direcciones. 
- **ACK flood** - Esta es una técnica para enviar un paquete TCP / ACK al objetivo a menudo con una dirección IP falsificada. Es muy similar a los ataques de inundación TCP / SYN.
- **FTP Bounce** - El atacante puede conectarse a los servidores FTP y tiener la intención de enviar archivos a otros usuarios / máquinas que usan el comando PORT. Para que el servidor FTP intente enviar el archivo a otras máquinas en un puerto específico y verifique que el puerto esté abierto. 
- **TCP Session Hijacking** - El "Hacker" toma la sesión TCP existente, ya establecido entre las dos partes. En la mayoría de las sesión TCP la autenticación ocurre al comienzo de la sesión, los piratas informáticos realizan este ataque en dicho momento. 
- **Man-In-The-Middle** - Un ataque MITM ocurre cuando una comunicación entre dos sistemas es interceptada por una entidad externa. Esto puede suceder en cualquier forma de comunicación en línea, como correo electrónico, redes sociales, navegación web, etc. No solo están tratando de escuchar nuestras conversaciones privadas, sino que también pueden dirigir toda la información dentro de los dispositivos. 
  - Prevención:
    - Certificados de autenticación.
    - S / MIME
- **Ingeniería social** - La ingeniería social es el arte de manipular a las personas para que renuncien a la información confidencial. Los tipos de información que buscan estos delincuentes pueden variar, pero cuando los individuos son blanco, los delincuentes generalmente intentan engañarlo para que le dé su contraseña o información bancaria, o acceda a su pc para instalar en secreto el software malicioso, que le dará acceso a su contraseñas e información bancaria, así como para darles control sobre el mismo. 
- **OS Finger Printing** - Método utilizado para determinar qué sistema operativo se ejecuta en una máquina remota. Al analizar ciertos indicadores de protocolo, opciones y datos en los paquetes que un dispositivo envía a la red, podemos hacer conjeturas relativamente precisas sobre el sistema operativo que envió esos paquetes. Al identificar el sistema operativo exacto de un host, un atacante puede lanzar un ataque preciso contra una máquina destino. 
  - Prevención:
    - OS Finger Printing activa
    - OS Finger Printing pasivo
- **KeyLoggers** - Un keylogger puede ser un programa de software o un hardware que utiliza un atacante para registrar las pulsaciones de teclas en el teclado de un usuario. Con un Keylogger, un atacante puede conocer remotamente sus contraseñas, números de tarjetas de crédito / débito, mensajes, correos electrónicos y todo lo que escriba. 
- **ICMP Tunneling** - El tunneling se usa a menudo para eludir los firewalls que no bloquean los paquetes ICMP, o para establecer un canal de comunicación cifrado y difícil de rastrear entre dos máquinas sin interacción directa de la red. Un túnel ICMP establece una conexión encubierta entre dos máquinas remotas (un cliente y un proxy), utilizando solicitudes de eco ICMP y paquetes de respuesta. 
- **Secuencia TCP** - Un ataque de predicción de secuencia TCP es un intento de predecir el número de secuencia utilizado para identificar los paquetes en una conexión TCP, que se puede usar para duplicar paquetes que conducen al secuestro de la sesión. 
- **CAM Table Overflow** - La tabla CAM de una máquina contiene información de red, como las direcciones MAC disponibles en los puertos físicos del conmutador y los parámetros de VLAN asociados. Los desbordamientos de la tabla CAM ocurren cuando una entrada de direcciones MAC se inunda en la tabla y se alcanza el umbral de la tabla CAM. Esto hace que el conmutador actúe como un concentrador, inundando la red con tráfico fuera de todos los puertos. La inundación causada por un desbordamiento de tabla CAM está limitada a la VLAN de origen, por lo tanto, no afecta a otras VLAN en la red. 
- **Inyección SQL** - También se denomina Ataque de Inserción SQL que ayuda al hacker a ejecutar un código debido a la presencia de vulnerabilidad en la capa de la base de datos de la Aplicación. En consecuencia, el código obtendrá datos confidenciales o incluso comprometerá la aplicación en sí.
- **Robo de cookies** - Se realiza mediante scripts del lado del cliente como JavaScript. Cuando el usuario hace clic en un enlace, el script buscará la cookie almacenada en la memoria de la máquina para todas las cookies activas y las enviará (al parecer, los correos electrónicos) al atacante. 
- **Phishing** - Phishing es el proceso criminalmente fraudulento de intentar adquirir información sensible como nombres de usuario, contraseñas y detalles de tarjetas de crédito haciéndose pasar por una entidad confiable en una comunicación electrónica.
- **Virus** - Un virus informático es un programa informático que puede copiarse e infectar una máquina. El término "virus" también se usa comúnmente pero erróneamente para referirse a otros tipos de malware, incluidos, entre otros, los programas de adware y spyware que no tienen la capacidad reproductiva. Un virus verdadero puede propagarse de una máquina a otra (en algún tipo de código ejecutable) cuando su host se lleva a la máquina de destino.
- **Gusanos** - Un gusano informático es un programa informático de malware autoreplicante. Utiliza una red informática para enviar copias de sí mismo a otros nodos (maquinas en la red) y puede hacerlo sin intervención del usuario. Esto se debe a deficiencias de seguridad en la máquina de destino. A diferencia de un virus, no es necesario que se una a un programa existente. Los gusanos casi siempre causan al menos algún daño a la red, al consumir ancho de banda, mientras que los virus casi siempre corrompen o modifican archivos en una máqioma específica. 
- **Malware** - Software malicioso. 
  - Prevención:
    - **Instalar software Anti-Virus / Malware**.
    - **Mantener el software antivirus actualizado**.
    - **Ejecutar escaneos regulares programados del antivirus**.
    - **Mantener el sistema operativo al día**.
    - **Asegurar la red**.
    - **Mantener la información personal segura**.
    - **Usar múltiples contraseñas fuertes**.
  - Tipos:
    - Addware - Paquete de software que reproduce, muestra o descarga publicidades en una máquina automáticamente después de instalar el software o mientras se usa la aplicación.  
    - Spyware - Malware que se instala en las maquinas y recopila pequeñas porciones de información a la vez sobre los usuarios sin su conocimiento. La presencia de spyware generalmente está oculta para el usuario y puede ser difícil de detectar. 
    - Troyanos - Malware no autorreplicante que parece realizar una función deseable para el usuario pero que en cambio facilitar el acceso no autorizado al sistema informático del usuario. 
    - Root kit - Software que está diseñado para obtener el control de nivel de administrador sobre un sistema informático sin ser detectado. 

