# Práctica 4. Asegurar la granja web

## Instalar un certificado SSL autofirmado para configurar el acceso por HTTPS

Para generar un certificado SSL autofirmado en Ubuntu Server debemos activar el módulo SSL de Apache, generar los certificados y especificarle la ruta a los certificados en la configuración.

Por lo que ejecutamos el comando `a2enmod ssl` y pasarán a activarse una serie de módulos.

```
a2enmod ssl
```

Ahora tenemos que reiniciar `Apache2`.

```
service apache2 restart
```

Creamos la carpeta `ssl` en el directorio de Apache. Aquí guardaremos nuestro certificado SSL.

```
mkdir /etc/apache2/ssl
```

Una vez hecho esto, generamos nuestro certificado SSL, dónde nos pedirá una serie de datos para configurar el dominio.

```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt
```

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P4/Capturas/captura1.png)

Editamos el archivo de configuración del sitio default-ssl:

```
nano /etc/apache2/sites-available/default-ssl
```

Y agregamos estas lineas: 

```
SSLCertificateFile /etc/apache2/ssl/apache.crt 
SSLCertificateKeyFile /etc/apache2/ssl/apache.key
```

Activamos el sitio default-ssl y reiniciamos apache:

```
a2ensite default-ssl 
service apache2 reload
```

Para hacer peticiones por HTTPS utilizando la herramienta curl, ejecutaremos:

```
curl –k https://192.168.56.105/hola.html
```

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P4/Capturas/captura2.png)

Por último falta que funcione el https en el balanceador, para ello copiamos la pareja de archivos `.crt` y el `.key`.

Los generamos sólo una vez en la máquina 1, y luego los copiamos a todas las demás máquinas de la granja, podemos usar scp:

```
scp 192.168.56.105:/etc/apache2/ssl/* /tmp/
``` 

Para finalizar, editamos el archivo `/etc/nginx/conf.d/default.conf ` del balanceador (nginx) y añadimos lo siguiente:

```

listen 443 ssl;
ssl on;
ssl_certificate      /tmp/apache.crt;
ssl_certificate_key  /tmp/apache.key;
```

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P4/Capturas/captura6.png)

## Configuración del cortafuegos

### Configuración del cortafuegos iptables en Linux

#### Uso de la aplicación iptables

Toda información sobre la herramienta está disponible usando la opcion de ayuda:

```
man iptables
iptables -h
```

Para comprobar el estado del cortafuegos, debemos ejecutar:

```
iptables –L –n -v
```

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P4/Capturas/captura3.png)

Para lanzar, reiniciar o parar el cortafuegos, y para salvar las reglas establecidas hasta ese momento, ejecutaremos respectivamente:

```
service iptables start
service iptables restart
service iptables stop 
service iptables save
```

Para configurar el cortafuegos utilizamos el siguiente script:

```
# (1) se eliminan todas las reglas que hubiera 
# para hacer la configuración limpia:
iptables -F
iptables -X

# (2) establecer las políticas por defecto (denegar todo el tráfico): 
iptables -P INPUT DROP
iptables -P OUTPUT DROP 
iptables -P FORWARD DROP

# (3) permitir cualquier acceso desde localhost (interface lo): 
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# (4) permitir la salida del equipo (output) con conexiones nuevas que 
# solicitemos, conexiones establecidas y relacionadas. 
# Permitir la entrada (input) solo de conexiones establecidas y relacionadas: 
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT 
iptables -A OUTPUT -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT
```

Volvemos a comprobar el estado del cortafuegos:

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P4/Capturas/captura4.png)

Y hacemos peticiones desde localhost:

```
curl -k https://192.168.56.105/hola.html
```

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P4/Capturas/captura5.png)

## Script para configurar iptables al arrancar

```
#!/bin/sh
# (1) se eliminan todas las reglas que hubiera 
# para hacer la configuración limpia:
iptables -F
iptables -X

# (2) establecer las políticas por defecto (denegar todo el tráfico): 
iptables -P INPUT DROP
iptables -P OUTPUT DROP 
iptables -P FORWARD DROP

# (3) permitir cualquier acceso desde localhost (interface lo): 
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# (4) permitir la salida del equipo (output) con conexiones nuevas que 
# solicitemos, conexiones establecidas y relacionadas. 
# Permitir la entrada (input) solo de conexiones establecidas y relacionadas: 
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT 
iptables -A OUTPUT -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT
```

Para ello, añadir en `/etc/crontab` la siguiente linea:

```
@reboot /home/nacho/iptables_script.sh
```