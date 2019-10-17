### Arquitectura de Microservicios

En esta sección vamos a crear una arquitectura de la siguiente manera:<br/>

* **Position Simulator** simula la posición de los vehículos y envía esta posición a una cola
* **Active Mq** es una cola que retiene las posiciones de los vehículos
* **Position Tracker**  es un tercer microservicio que va y obtiene la posición de los vehículos en la cola
* **API Gateway** es un cuarto servicio por que es accedido por medio de una url; obtiene la posición de los vehículos del servicio anterior
* **Front** en un extremo del flujo tenemos la vista desarrollada en Angular y que se utiliza en un navegador web

##### Active Mq

Habiendo descrito la arquitectura de nuestro sistema empezamos por desplegar el corazón del sistema, que en este caso es la cola (Active Mq), tenemos entonces el siguiente archivo _mq-deploy.yml_:<br/>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: queue
spec:
  selector:
    matchLabels:
      app: queue
  template:
    metadata:
      labels:
        app: queue
    spec:
      containers:
        - name: whatever
          image: richardchesterwood/k8s-fleetman-queue:release1
  replicas: 1
```

Y su respectivo servicio _mq-service.yml_:<br/>

```yaml
apiVersion: v1
kind: Service
metadata:
  name: fleetman-queue
spec:
  selector:
    app: queue
  ports:
    - name: http
      port: 8161
      nodePort: 30010
    - name: endpoint
      port: 61616
  type: NodePort
```
**Nota:** es muy importante tener en cuenta que el nombre del servicio, ya que de lo contrario el simulador de posición no lo va a encontrar.<br/>

##### Position Simulator

El siguiente servicio a desplegar es el simulador de posición, y lo haremos por medio de un _Deployment_:<br/>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: position-simulator
spec:
  selector:
    matchLabels:
      app: position-simulator
  template:
    metadata:
      labels:
        app: position-simulator
    spec:
      containers:
        - name: whatever
          image: richardchesterwood/k8s-fleetman-position-simulator:release1
          env:
            - name: SPRING_PROFILES_ACTIVE
              value: production-microservice
  replicas: 1
```

Para efectos prácticos, hemos establecido que exista una sola réplica y además hace su aparición la etiqueta _env_, que nos permite definir variables de entorno, en este caso el perfil activo.<br/>

##### Tracker Simulator

El tercer servicio tiene la función de procesar los mensajes que se han depositado en la cola por el simulador de posociones, y lo haremos por medio de un _deployment_:<br/>



