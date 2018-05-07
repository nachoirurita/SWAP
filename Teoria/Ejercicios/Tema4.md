# Ejercicios del Tema 4

## 7. Buscar información sobre métodos y herramientas para implementar GSLB.

La palabra usada para denominar habitualmente, una situación de balanceo entre sites es GSLB (Global Site Load Balance).

Hay distintas aproximaciones al GSLB, pero en esencia y simplificando, se trata de situar en cada uno de los sites un dispositivo con capacidad GSLB (casi todos los fabricante importantes tienen modelos de este tipo, CISCO, F5, RADWARE, etc.), de tal manera, que los diferentes dispositivos GSLB hablan entre ellos para decidir que Site debe atender a un determinado usuario.

La decisión de a que Datacenter se debe dirigir a un usuario se puede tomar en base a uno o diferentes criterios, por ejemplo:

- Carga: en función  de la carga que tenga el Datacenter se envía a uno u otro.
- Latencia: en función de los tiempos de respuesta al usuario.
- Vector distancia: en función de los bloques de direccionamiento.
- DNS: implementando una Round Robin.
