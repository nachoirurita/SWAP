# Practica 2. Clonar la información de un sitio web.

## Crear un tar con ficheros locales en un equipo

```
tar czf - prueba | ssh 192.168.56.105 'cat > tar.tgz'
```

### Máquina 2

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P2/Capturas/2-tar/Maquina2.png)

### Máquina 1

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P2/Capturas/2-tar/Maquina1.png)

## Instalar la herramienta rsync

En ambas máquinas hay que hacer que el usuario sea dueño de las carpetas, para ello ejecutamos el siguiente comando:

```
sudo chown nacho:nacho -R /var/www/
```

Para clonar la carpeta */var/www/* de la máquina 1 a la máquina 2:

```
rsync -avz -e ssh 192.168.56.105:/var/www/ /var/www/
```

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P2/Capturas/3-rsync/1.png)

Comprobamos que se ha realizado correctamente:

```
ls -la /var/www/
ls -la /var/www/html/
```

### Máquina 1

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P2/Capturas/3-rsync/2.png)

### Máquina 2

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P2/Capturas/3-rsync/3.png)

## Acceso sin contraseña para ssh

Generamos la clave en la máquina 2:

```
ssh-keygen -b 4096 -t rsa
```

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P2/Capturas/4-ssh/1.png)

Ahora copiamos la clave pública a la máquina 1:

```
ssh-copy-id 192.168.56.105
```

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P2/Capturas/4-ssh/2.png)

A continuación ya podremos conectarnos a dicho equipo sin contraseña:

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P2/Capturas/4-ssh/3.png)

Para ejecutar comandos lo añadimos al final del comando ssh para conectarnos:

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P2/Capturas/4-ssh/4.png)

## Programar tareas con crontab

Para añadir una nueva regla:

```
sudo vi /etc/crontab
```

Y añadimos la siguiente regla:

```
* * * * * root rsync -avz -e ssh 192.168.56.105:/var/www/ /var/www/
```

Comprobamos que se ha añadido correctamente:

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Prácticas/P2/Capturas/5-crontab/1.png)
