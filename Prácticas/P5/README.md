# Práctica 5. Replicación de bases de datos MySQL

## Crear una BD e insertar datos

Para crear la BD tenemos que entrar en `MySQL`. Para ello ejecutamos el siguiente comando:

```
mysql -uroot -p
```

Introducimos la `contraseña` y ya estaremos dentro de la terminal de MySQL. Ahora creamos una BD:

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P5/Capturas/captura1.png)

Como podemos ver, hemos insertado un dato en la BD para asi poder tener algo de lo que hacer copia de seguridad.

## Replicar una BD MySQL con mysqldump

Para ello, tenemos que tener en cuenta que los datos pueden estar actualizándose constantemente en el servidor de BD principal. En esta caso, antes de hacer la copia de seguridad en el el archivo `.SQL` debemos evitar que se acceda a la BD para cambiar nada.

Así, en el servidor de BD principal (maquina 1) hacemos:

```
mysql -u root -p
FLUSH TABLES WITH READ LOCK;
quit
```

Una vez realizado esto, ya podemos hacer el mysqldump para guardar los datos. En el servidor principal (maquina 1) hacemos:

```
mysqldump contactos -u root -p > /tmp/contactos.sql
```

Como habíamos bloqueado las tablas, debemos desbloquearlas:

```
mysql -u root -p
UNLOCK TABLES;
quit
```

Ahora ya podemos ir a la maquina esclavo (maquina2), para copiar el archivo `.SQL` con todos los datos salvados desde la maquina principal (maquina1).

```
scp 192.168.56.105:/tmp/contactos.sql /tmp/
```


![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P5/Capturas/captura2.png)

Con el archivo de copia de seguridad en el esclavo ya podemos importar la BD completa en el MySQL (maquina2). Para ello, en un primer paso creamos la BD:

```
mysql -u root -p
CREATE DATABASE ‘ejemplodb’;
quit
``` 

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P5/Capturas/captura3.png)

Y en un segundo paso restauramos los datos contenidos en la BD:

```
mysql -u root -p contactos < /tmp/contactos.sql 
```


![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P5/Capturas/captura4.png)

## Replicación de BD mediante una configuración maestro-esclavo

La opción anterior funciona perfectamente, pero es algo tedioso, lo tiene que realizar un operador a mano. Sin embargo, MySQL tiene la opción de configurar el demonio para hacer la replicación de las BD sobre un esclavo a partir de los datos que almacena el maestro.

Se trata de un proceso automatico que resulta muy adecuado en un entorno de producción real.

Lo primero que debemos hacer es la configuracion de `mysql` del maestro. Para ello editamos, como root, el archivo `/etc/mysql/mysql.conf.d/mysqld.cnf`. 

Comentamos el siguiente parámetro que sirve para que escuche a un servidor:

```
#bind-address 127.0.0.1
```

Le indicamos donde tiene que almacenar el log de errores:

```
log_error = /var/log/mysql/error.log
```

Establecemos el identificador del servidor:

```
server-id = 1
```

El registro binario contiene toda la información que está disponible en el registro de actualizaciones, en un formato más eficiente y de una manera que es segura para las transacciones:

```
log_bin = /var/log/mysql/bin.log
```

Guardamos el documento y reiniciamos el servicio:

```
/etc/init.d/mysql restart
```

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P5/Capturas/captura5.png)

Si no ha dado ningún error podemos pasar a configurar el mysql del esclavo:

Hacemos la configuaración del esclavo exactamente igual que antes pero cambiando el `server-id = 2`

```
server-id = 2
```

Guardamos y reiniciamos:

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P5/Capturas/captura6.png)

Ahora creamos un usuario en el maestro y le damos permisos para la replicación. Dicho usuario será `esclavo` y su contraseña también será `esclavo`.

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P5/Capturas/captura7.png)

Ahora nos vamos a la maquina del esclavo (maquina2) y le indicamos los datos del maestro y reiniciamos: 

```
CHANGE MASTER TO MASTER_HOST='192.168.56.105', MASTER_USER='esclavo', MASTER_PASSWORD='esclavo', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=980, MASTER_PORT=3306;

```

Para finalizar, arrancamos el esclavo y ya está todo listo para que los demonios de `MySQL`de las dos máquinas repliquen automáticamente los datos que se introduzcan/modifiquen/borren en el servidor maestro:

```
mysql -u root -p
START SLAVE;
```

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P5/Capturas/captura8.png)

Por último, volvemos al maestro y volvemos a activar las tablas para que puedan meterse nuevos datos en el maestro:

```
mysql -u root -p
UNLOCK TABLES;
```

