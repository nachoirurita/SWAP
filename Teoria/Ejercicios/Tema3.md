# Ejercicios del Tema 3

## 1.Buscar con qué órdenes de terminal o herramientas gráficas podemos configurar bajo Windows y bajo Linux el enrutamiento del tráfico de un servidor para pasar el tráfico desde una subred a otra.

### Windows

Usamos el comando route que se encarga de mostrar o modificar la tabla de enrrutamiento y se usa de la siguiente forma:

```
ROUTE [-f] [comando [destino] [MASK mascara de red] [puerto de enlace]
```
### Linux

Para linux podemos usar también el comando `route`:

- `-n` Muestra la tabla de enrutamiento en formato numérico [dirección IP].
- `-e` Muestra la tabla de enrutamiento en formato hostname.
- `add`	Añade una nueva ruta a la tabla de enrutamiento.
- `del`	Elimina una ruta de la tabla de enrutamiento.
- `netmask` Se usa para especificar la máscara de subred del host o red de destino.

## 2.Buscar con qué órdenes de terminal o herramientas gráficas podemos configurar bajo Windows y bajo Linux el filtrado y bloqueo de paquetes.

### Windows

Para Windows se puede usar el propio cortafuegos (firewall) que posee un filtrado de paquetes, consiste en bloquear selectivamente el tráfico de red.

### Linux

Para Linux utilizamos la herramienta iptables que permite crear reglas para permitir o denegar que paquetes.