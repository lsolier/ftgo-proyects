# Proyectos FTGO
Desplegar Servicios Usando Kubernetes
- Fuente: https://www.manning.com/liveprojectseries/deploy-services-using-kubernetes-ser

## Arquitectura Logica

![logical-diagram](/assets/logical-architecture.png)

La aplicacion consta de los siguientes microservicios:

- _**Customer Service**_ - Gestiona los clientes. El servicio persiste la entidad _Customer_ en una base de datos MySQL/Postgres. Usanso el framework _Eventuate Tram Saga_ se procesa los mensajes de comando, actualiza la entidad _Customer_ y devuelve un mensaje de respuesta.

- _**Order Service**_ - Gestiona las ordenes. El servicio persiste las entidades _Order_ y  _CreateOrderSaga_ en una base de datos MySQL/Postgres. Usando el framework _Eventuate Tram Saga_, se envia los mensajes de comando y se procesan las respuestas.

- _**API Gateway**_ - Un API Gateway basado en Spring Cloud Gateway que enruta las solicitudes(HTTP Request) al servicio apropiado.

Además, existen los siguientes servicios de infraestructura:

- _**Postgres relational database**_ - Usado por los servicios _Customer Service_ y _Order Service_

- _**Apache Kafka message broker**_ - Usado por los servicios _Customer Service_ and _Order Service_

- _**Apache Zookeeper**_ - Usado por Apache Kafka

- _**Spring Authorization Service**_ - Usado por el API Gateway para autenticar las solicitudes(HTTP Request) y obtener un token JWT que será pasado a los servicios _Customer Service_ y _Order Service_

- _**Eventuate Tram CDC service**_ - Lee los mensajes desde las tablas _OUTBOX_ y publica los mensajes a Apache Kafka.