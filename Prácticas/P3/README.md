# Práctica 3. Balanceo de carga

## Instalar nginx

Para realizar la instalación, se recomienda ejecutar las siguientes ordenes:

```
sudo apt-get update && sudo apt-get dist-upgrade && sudo apt-get autoremove

sudo apt-get install nginx 
sudo systemctl start nginx
```

## Balanceo de carga usando nginx

La configuración básica de nginx no nos vale como está, ya que corresponde a una funcionalidad de servidor web, así que tenemos que modificar el fichero de configuracion `/etc/nginx/conf.d/default.conf`:


```
upstream apaches 
{
	server 192.168.56.105; 
	server 192.168.56.110;
}

server
{
	listen 80;
	server_name balanceador;

	access_log /var/log/nginx/balanceador.access.log; 
	error_log /var/log/nginx/balanceador.error.log; 
	root /var/www/;
	
	location / 
	{
		proxy_pass http://apaches;
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; 
		proxy_http_version 1.1;
		proxy_set_header Connection ""; 
	}
}
```

Una vez configurado, podemos lanzar el sevicio `nginx`:

```
sudo systemctl start nginx
```

Si obtenemos algún error o no esta funcionando como balanceador, es posible que haya que comentar la linea que configura `nginx` como servidor web en el archivo `/etc/nginx/nginx.conf`. 

Para probar la configuración podemos hacer peticiones a la IP de la máquina balanceadora `192.168.56.20`.

```
curl http://192.168.56.20
curl http://192.168.56.20
```

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P3/Capturas/captura1.png)

## Balanceo de carga ponderado

Si sabemos que alguna de las máquinas finales es más potente, podemos modificar la definición del `upstream` para pasarle más tráfico que al resto de las del grupo. Para ello, tenemos un modificador llamado `weight`, al que le damos un valor numérico que indica la carga que le asignamos.

Por defecto tiene el valor 1, por lo que si no modificamos ninguna máquina del grupo, todas recibirán la misma cantidad de carga:

```

upstream apaches 
{
	server 192.168.56.105 weight=1; 
	server 192.168.56.110 weight=2;
}
```

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P3/Capturas/captura2.png)

Para probarlo, podemos hacer peticiones a la maquina balanceadora y el resultado sería:

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P3/Capturas/captura3.png)

## Instalar haproxy

Tras instalar el sistema básico, instalamos `haproxy`.

```
sudo apt-get install haproxy
```

## Configuración básica de haproxy como balanceador

Una vez instalado, debemos modificar el archivo `/etc/haproxy/haproxy.cfg` ya que la configuración que trae por defecto no nos vale.

```
cd /etc/
cd haproxy/
ifconfig
sudo nano haproxy.cfg
```

Un balanceador sencillo debe escuchar tráfico en el puerto 80 y redirigirlo a alguna de las máquinas servidoras finales. Usemos como configuración inicial la siguiente:

```
global
    daemon
	maxconn 256

defaults
	mode http
	contimeout 4000 
	clitimeout 42000 
	srvtimeout 43000

frontend http-in 
	bind *:80
	default_backend servers

backend servers
	server m1 192.168.56.105:80 maxconn 32 
	server m2 192.168.56.110:80 maxconn 32

```

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P3/Capturas/captura4.png)

## Comprobar el funcionamiento del balanceador

Una vez salvada la configuración en el fichero, lanzamos el servicio `haproxy` mediante el comando:

```
sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg
```

Ya podemos realizar peticiones a la IP del balanceador:

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P3/Capturas/captura5.png)

## Someter a una alta carga el servidor balanceado

Para medir el rendimiento de un servidor necesitaremos una herramienta que ejecutar en los clientes para crear una carga HTTP especifica. 

Para ello usaremos `Apache Benchmark (ab)` que es una utilidad que se instala junto con el servidor Apache y permite comprobar el rendimiento de cualquier servidor web,

### Nginx

Para probar nuestra granja web con un balanceador `nginx` , vamos a lanzar 50000 peticiones de 300 en 300.

```
ab -n 50000 -c 300 http://192.168.56.20/prueba.html
```


![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P3/Capturas/captura6.png)


### Haproxy

Hacemos lo mismo que en el caso anterior.

```
ab -n 50000 -c 300 http://192.168.56.20/prueba.html
```


![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P3/Capturas/captura7.png)

## Comparacion de balanceadores al someterlos a la misma carga

| Balanceador | Tiempo total | Tiempo mínimo | Peticiones/segundo | Tiempo/Petición |
|:-----------:|:------------:|:-------------:|:------------------:|:---------------:|
| __nginx__   | 145.144 s    | 96 ms         | 344.49             | 870.865 ms      |
| __haproxy__ | 126.984 s    | 197 ms        | 393.75             | 761.907 ms      |

Como podemos ver, el balanceador con mejor resultado es `haproxy`.
