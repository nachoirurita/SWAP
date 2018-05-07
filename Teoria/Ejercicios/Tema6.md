# Ejercicios del Tema 6

## 1.Aplicar con iptables una política de denegar todo el tráfico en una de las máquinas de prácticas.
Comprobar el funcionamiento. Aplicar con iptables una política de permitir todo el tráfico en una de las máquinas de prácticas.
Comprobar el funcionamiento.

### Denegar todo el tráfico

```
iptables -F
iptables -P INPUT DROP
iptables -P OUTPUT DROP
iptables -P FORWARD DROP
```

### Aceptar todo el tráfico

```
iptables -F
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
```

## 2. Comprobar qué puertos tienen abiertos nuestras máquinas, su estado, y qué programa o demonio lo ocupa.

El siguiente comando, determina cuáles puertos están escuchando por conexiones TCP desde la red:

![imagen](https://github.com/nachoirurita/SWAP/blob/master/Teoria/Ejercicios/imagenes/tema6.png)