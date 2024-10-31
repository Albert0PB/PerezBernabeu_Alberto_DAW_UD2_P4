# Práctica 2-4. Balanceo de carga

Para realizar esta tarea es necesario clonar la MV que funciona como servidor web. 
De esta manera, tendremos dos servidores web y un servidor que funciona como proxy 
inverso y balanceador de carga.

Un recordatorio de las IPs que se están utilizando en cada máquina: 
	- Anfitriona: 192.168.0.13
	- Servidor web 1: 192.168.0.20	
	- Servidor web 2: 192.168.0.21
	- Servidor proxy: 192.168.0.22


## Configuración del primer servidor web

Será necesario cambiar el nombre del directorio que contiene los archivos 
del servidor web a webserver1. Lo mismo con el archivo de configuración y 
con el enlace simbólico, en los directorios /etc/nginx/sites-available y 
/etc/nginx/sites-enabled.

Además, se le añade una cabecera al archivo de configuración. En mi caso:
```console
location /{
	add-header Serv_Web1_PerezBernabeuAlberto "Servidor Web 1";
}
```

También he modificado el contenido del HTML del servidor, para que muestre:
```html
<html>
	<head>
		<title> Prueba de balanceo con NGINX</title>
	</head>
	<body>
		<h2>Este es el servidor web 1</h2>
	</body>
</html>
```



## Configuración del segundo servidor web

La configuración del segundo servidor es la misma que la del primer servidor web, 
cambiando "webserver1" por "webserver2" en los lugares que es necesario.


## Configuración del servidor proxy inverso

En el servidor proxy inverso, para configurar el balanceo de carga, 
modificamos el archivo de configuración de /etc/nginx/sites-available/ de 
la siguiente manera: 
```console
upstream backend_hosts {
	random;
	server 192.168.0.20:8080;
	server 192.168.0.21:8080;
}

server {
	listen 80;
	listen [::]:80;
	server_name webserver www.webserver.com;
	location / {
		proxy_pass http://backend_hosts;
		# add_header Host proxy_inverso_albertoperez;
	}
}
```
