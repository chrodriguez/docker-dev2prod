***
# Workflow completo
---
## Un ejemplo en gitlab

https://gitlab.com/chrodriguez/demo-lb
---
## El proyecto en desarrollo

Según el readme, corremos:

```
cd docker
docker-compose -p demo-lb-dev \
  -f docker-compose.yml -f docker-compose.dev.yml up
```
<small>
Notar que pasamos 2 docker-compose.yml
</small>

En esta modalidad podemos trabajar sin instalar nada en nuestra PC, editando el
archivo php de la raíz del proyecto

---
## Entrega continua

El archivo `.gitlab-ci.yml`

```yml
image: docker:latest

services:
  - docker:dind

stages:
  - build

before_script:
  - export IMAGE_TAG="$CI_BUILD_REF_NAME"
  - docker login -u "gitlab-ci-token" -p "$CI_BUILD_TOKEN" $CI_REGISTRY

build-docker-image:
  stage: build
  script:
    - docker build --pull -f docker/Dockerfile -t "$CI_REGISTRY_IMAGE:$IMAGE_TAG" .
    - docker push "$CI_REGISTRY_IMAGE:$IMAGE_TAG"
  only:
    - /^\d+\.\d+\.\d+/
```
Un nuevo tag equivale a una nueva imagen docker

---
## La aplicación en rancher

Usaremos rancher-cli con el siguiente docker-compose.yml

```yml
version: '2'
services:
  web:
    image: registry.gitlab.com/chrodriguez/demo-lb:0.0.1
    labels:
      application: demo-lb
```

```bash
$ rancher up
```
Configuraremos el load balancer a partir del label seteado

<small class="fragment">
rancher-cli utiliza variables de ambiente que lo configuran
</small>
---
## El potencial de Rancher

* Load balancer.
* Servicios externos.
* Escaliamiento horizontal.
* Políticas de schedulling.
* Healthchecks.
* Auto scalling: por servicio o hosts de la infraestructura.
* Integracion con virtualizadores y proveedores de cloud.
* Integracion con DNS: powerdns o servicios de cloud.
* Catalogo.
---
## Las preguntas...

* ¿Y los logs?
* ¿Y el monitoreo?
* ¿Y las bases de datos?
* ¿Y los backups?

<div class="fragment">
<strong>Todas tienen respuesta</strong>
</div>
---
## Logs

* Pueden manipularse con:
  * syslog
  * [Fluentd](https://www.fluentd.org/)
  * [Graylog](https://www.graylog.org/)
  * [Logstash](https://www.elastic.co/products/logstash)
  * [Kibana](https://www.elastic.co/products/kibana)

---
## Monitoreo

* Hoy están de *moda*:
  * [Prometheus](https://prometheus.io/)
  * [Influx DB](https://www.influxdata.com/)
  * [Bosun](http://bosun.org/)
  * [Grafana](https://grafana.com/)

---
## Las bases de datos y backups

* Las bases de datos pueden dockerizarse o no
* Los backups deben hacerse sólo de los volúmenes
  * En mi caso, [rsnapshot](http://rsnapshot.org/) es una excelente alternativa
  * **Backupeando la base de datos de rancher, podemos recuperar nuestra infra completa**

***
