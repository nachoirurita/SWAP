# Ejercicios del Tema 2

## 1.Calcular la disponibilidad del sistema si tenemos dos réplicas de cada elemento (en total 3 elementos en cada subsistema).

| Componentes |   Normal  |   Duplicado  |   Triplicado |
|:------------|----------:|-------------:|-------------:|
Web           |85%        |97.75%        |99.66%        |
Application   |90%        |99%           |99.9          |
Database      |99.9%      |99.9999%      |99.9999999%   |
DNS           |98%        |99.96%        |99.9992%      |
Firewall      |85%        |97.75%        |99.66%        |
Switch        |99%        |99.99%        |99.9999%      |
Data Center   |99.99%     |99.99%        |99.99%        |
ISP           |95%        |99.75%        |99.2035%      |

## 2.Buscar frameworks y librerías para diferentes lenguajes que permitan hacer aplicaciones altamente disponibles con relativa facilidad.

1. *AngularJS:* es un framework de JavaScript de código abierto, mantenido por Google, que se utiliza para crear y mantener aplicaciones web de una sola página. Su objetivo es aumentar las aplicaciones basadas en navegador con capacidad de Modelo Vista Controlador (MVC), en un esfuerzo para hacer que el desarrollo y las pruebas sean más fáciles. Se puede combinar con el entorno en tiempo de ejecución Node.js, el framework para servidor Express.js y la base de datos MongoDB para formar el conjunto MEAN.

2. *jQuery:* es una biblioteca multiplataforma de JavaScript, creada inicialmente por John Resig, que permite simplificar la manera de interactuar con los documentos HTML, manipular el árbol DOM, manejar eventos, desarrollar animaciones y agregar interacción con la técnica AJAX a páginas web. Fue presentada el 14 de enero de 2006 en el BarCamp NYC. jQuery es la biblioteca de JavaScript más utilizada. Es software libre y de código abierto, posee un doble licenciamiento bajo la Licencia MIT y la Licencia Pública General de GNU v2, permitiendo su uso en proyectos libres y privados.2​ jQuery, al igual que otras bibliotecas, ofrece una serie de funcionalidades basadas en JavaScript que de otra manera requerirían de mucho más código, es decir, con las funciones propias de esta biblioteca se logran grandes resultados en menos tiempo y espacio.

## 3. ¿Cómo analizar el nivel de carga de cada uno de los subsistemas en el servidor? Buscar herramientas y aprender a usarlas.

Usando una herramienta que monitorice cada servicio por separado. Por ejemplo podemos usar:

*Munin:* presenta gráficos de carga de distintos servicios y componentes de un servidor.

## 4. Buscar ejemplos de balanceadores software y hardware (productos comerciales). Buscar productos comerciales para servidores de aplicaciones. Buscar productos comerciales para servidores de almacenamiento.

### Balanceadores de carga software:
`nginx` permite actuar como un balanceador de carga HTTP.

### Balanceadores de carga hardware:
`Barracuda` ofrece balanceadores de carga hardware de alto rendimiento.

### Servidores de aplicaciones:
`Apache Tomcat` ofrece una implementación libre de Java Servlet, JavaServer Pages, Java WebSocket, etc. para poder crear un servidor de aplicaciones, tanto para Windows como para Linux.

### Servidores de Almacenamiento:
`Microsoft Azure` permite tener nuestro propio servidor de base de datos, SQL o NoSQL. Al ser en la nube, es altamente escalable y tiene una alta disponibilidad.

