# Ejercicios tema 4

## 4.1

IBM (datos algo desactualizados) tiene mainframes desde 800.000\$ hasta 33M\$, produciendo éste último 78426 MIPS. Por otro lado posee servidores sencillos (con los que se podría configurar una granja web), mucho más asequibles (entre 6K$ y 20K\$).



## 4.2

## 4.3

## 4.4

## 4.5

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