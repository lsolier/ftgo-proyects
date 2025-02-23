# Proyectos FTGO
Desplegar Servicios Usando Kubernetes
- Fuente: https://www.manning.com/liveprojectseries/deploy-services-using-kubernetes-ser

## Arquitectura Logica de componentes

![logical-diagram](/assets/logical-architecture.png)

La aplicacion consta de los siguientes microservicios:

- _**Customer Service**_ - Gestiona los clientes. El servicio persiste la entidad _Customer_ en una base de datos MySQL/Postgres. Usanso el framework _Eventuate Tram Saga_ se procesa los mensajes de comando, actualiza la entidad _Customer_ y devuelve un mensaje de respuesta.

- _**Order Service**_ - Gestiona las ordenes. El servicio persiste las entidades _Order_ y  _CreateOrderSaga_ en una base de datos MySQL/Postgres. Usando el framework _Eventuate Tram Saga_, se envia los mensajes de comando y se procesan las respuestas.

- _**API Gateway**_ - Un API Gateway basado en Spring Cloud Gateway que enruta las solicitudes(HTTP Request) al servicio apropiado.

Además, existen los siguientes servicios de infraestructura:

- _**Postgres relational database**_ - Usado por los servicios _Customer Service_ y _Order Service_

- _**Apache Kafka message broker**_ - Usado por los servicios _Customer Service_ y _Order Service_

- _**Apache Zookeeper**_ - Usado por Apache Kafka

- _**Spring Authorization Service**_ - Usado por el API Gateway para autenticar las solicitudes(HTTP Request) y obtener un token JWT que será pasado a los servicios _Customer Service_ y _Order Service_

- _**Eventuate Tram CDC service**_ - Lee los mensajes desde las tablas _OUTBOX_ y publica los mensajes a Apache Kafka.

## Arquitectura de despliegue

![logical-diagram](/assets/deployment-architecture.png)

La configuración se Kubernetes consta de las siguientes partes:

- Un cluster de Kubernetes basado en _kind_ ejecutandose localmente.

- Un pipeline de despliegue que usa _GitHub Actions_ para publicar las imagenes de los servicios al container registry de GitHub.

- Manifiestos de Kubernetes para desplegar los servicios de las aplicaciones. Cada servicio tiene un manifiesto de Desplyment y Service. Además, el servicio de _API Gateway_ tiene un manifiesto Ingress.

- Una capa superior de Kustomize que modifica los manifiestos para usar las imagenes desde el container registry de GitHub en lugar de las imagenes locales.

- Servicios de Infraestructura Infrastructure, como Apache Kafka y Postgres, instalado usando Helm.

- Un Ingress basado en NGINX que enruta las solicitudes externas (HTTP Request) a los servicios del API Gateway.

Para configurar cada clúster, simplemente necesitas ejecutar varios comandos:

- _**kind create cluster**_ - Para crear el cluster

- _**kubectl apply -f**_ - Para instalar el controlador Ingress Nginx

- _**helm install**_ - Para cada uno de los servicios de infraestructura

- _**kubectl apply -k**_ - Para cada uno de los servicios de la aplicación