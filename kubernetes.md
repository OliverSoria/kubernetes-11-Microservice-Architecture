### Arquitectura de Microservicios

En esta sección vamos a crear una arquitectura de la siguiente manera:<br/>

* **Position Simulator** simula la posición de los vehículos y envía esta posición a una cola
* **Active Mq** es una cola que retiene las posiciones de los vehículos
* **Position Tracker**  es un tercer microservicio que va y obtiene la posición de los vehículos en la cola
* **API Gateway** es un cuarto servicio por que es accedido por medio de una url; obtiene la posición de los vehículos del servicio anterior
* **Front** en un extremo del flujo tenemos la vista desarrollada en Angular y que se utiliza en un navegador web

Habiendo descrito la arquitectura de nuestro sistema empezamos por desplegar el corazón del sistema, que en este caso es la cola (Active Mq), tenemos entonces el siguiente archivo yml:<br/>

