# Ejercicios tema 4

## 4.1

IBM (datos algo desactualizados) tiene mainframes desde 800.000\$ hasta 33M\$, produciendo éste último 78426 MIPS. Por otro lado posee servidores sencillos (con los que se podría configurar una granja web), mucho más asequibles (entre 6K$ y 20K\$).



## 4.2

En la siguiente imagen se puede ver una comparativa entre diferentes balanceadores de carga y su precio.

![](https://github.com/harvestcore/SWAP/blob/master/ejercicios/tema4/images/loadbalancersprice.PNG)

> Fuente: http://www.sintel.com/bibli/telechargement/425/document_IT.pdf



## 4.3

Los diferentes métodos de balanceo son los siguientes:

- **Round Robin**
- **Ponderado**
- **Proporción dinámica** - Selecciona un servidor basado en varios aspectos del análisis del rendimiento del servidor en tiempo real.
- **El más rápido** - Selecciona un servidor basado en el menor número de sesiones actuales.
- **Conexiones mínimas**
- **Conexiones menos ponderadas** - Selecciona nodos en función del número de conexiones activas y de la capacidad de los nodos.
- **Observado** - Los nodos se clasifican en función del número de conexiones.  Se rastrean el número de conexiones a cada nodo a lo largo del tiempo y crean una relación para el equilibrio de carga.
- **Predictivo** - Los servidores se clasifican de acuerdo con el número de conexiones actuales. Se analiza la tendencia de la clasificación a lo largo del tiempo, determinando si el rendimiento de un nodo está mejorando o disminuyendo actualmente. Los servidores con clasificaciones de rendimiento que actualmente mejoran, en lugar de disminuir, reciben una mayor proporción de conexiones.
- **Sesiones mínimas** - Selecciona el servidor que actualmente tiene la menor cantidad de entradas en la tabla de persistencia.
- **Relación Mínima Conexiones** - El sistema selecciona al nodo de acuerdo con la proporción del número de conexiones que cada nodo tiene activo. 



## 4.5

HTTP permite a los servidores redirigir una solicitud de cliente a una ubicación diferente, lo que tiene algunas aplicaciones útiles.

- Una aplicación web puede usar la redirección para navegar entre partes de la aplicación.
- Si el contenido se ha movido a una URL o nombre de dominio diferente, se puede utilizar la redirección para evitar que se rompan las URL o los marcadores antiguos.
- Es posible convertir una solicitud POST a una solicitud GET utilizando la redirección.
- Se puede indicar a un cliente que use su memoria caché local para contenido que no ha cambiado.

Un servidor especifica la redirección devolviendo un código de estado 3xx:

301 - Esto indica que el contenido ahora reside permanentemente en la ubicación especificada por el encabezado ubicación y las futuras solicitudes deben dirigirse a esta ubicación. 

302 -  Igual que 301 , excepto que la nueva ubicación es temporal y las solicitudes futuras aún deben enviarse a la ubicación original. Otra característica de este código de estado es que si la solicitud original era un POST, el cliente cambiará a usar un GET cuando vuelva a emitir la solicitud (consulte a continuación para obtener más detalles). 

303 - Este código de estado fue el único código de estado que provocó que un POST se convirtiera en un GET . Sin embargo, la mayoría de los navegadores tratan un 302 como un 303. 

304 -  Se utiliza en respuesta a un encabezado If-Modified para redirigir una solicitud a la memoria caché local del navegador. 

305 -  Se utiliza una respuesta 305 para especificar que una solicitud debe reenviarse a través del proxy HTTP en el encabezado ubicación.



## 4.6

```php
<?php
	$ip = $_SERVER['REMOTE_ADDR'];
	$json = json_decode(file_get_contents("http://ipinfo.io/{$ip}/json"));
	$pais = $json->country;
	
	switch($pais) {
		case "ES":
			header("location: http://web.es");
			break;
		case "FR":
			header("location: http://web.fr");
			break;
		case "DE":
			header("location: http://web.de");
			break;
		case "GB":
			header("location: http://web.gb");
			break;
		case "PT":
			header("location: http://web.pt");
			break;
		case "JP":
			header("location: http://web.jp");
			break;
		default:
			header("location: http://web.es");
			break;
	}
?>
```



## 4.7

Algunos métodos de balanceo en GSLB son:

- **Recuperación de desastres** - Suministra un nivel extra de alta disponibilidad para aplicaciones importantes. Ofrece solicitudes directas con base en la disponibilidad o en la salud de la aplicación.
- **RTT Activo** - Envía las conexiones del cliente rápidamente para que el data center dé  la respuesta. Basado en el ping en el tiempo de respuesta del DNS
- **Capacidad de sesiones** - Envía conexiones del cliente al data center con la mejor capacidad disponible.
- **Valores ponderados** - Envía conexiones del cliente al data center que tiene la mayor puntuación acordada. Envía conexiones del cliente al data center con los servidores activos con más disponibilidad.
- **Costo del ancho de banda** - Consulta el uso de ancho de banda de cada sitio web. Selecciona los sitios cuyo uso de acho de banda no haya excedido un umbral configurado, durante el intervalo de consulta más reciente.
- **Geolocalización** - Envía conexiones de cliente al data center físico más "cercano".
