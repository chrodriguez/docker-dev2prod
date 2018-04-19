***
# Docker
## Introducción rápida

---
## ¿Qué es docker?

* Contenedores de software.
  * Empaqueta aplicaciones en una unidad estándar de intercambio.
* Única pieza de software en un filesystem completo que contiene **todo lo necesario** para ejecutar una aplicación: código, librerías, herramientas, etc.
* Garantiza que el software **siempre correrá de igual forma** sin importar su ambiente.

---
## ¿Por qué Docker?

* Rápida configuración de entornos de desarrollo.
* Favorece las arquitecturas de microservicios.
* Diferencias entre el ambiente de desarrollo, testing y producción.
* Instalación de una aplicación en diferentes plataformas.
* Deploy de aplicaciones complejas.
* Ejecución de código antiguo.
* Escalamiento horizontal.

---
## ¿Dónde corre Docker?

* Docker puede correrse en:
  * Linux.
  * MacOS.
  * Windows.
---
## La idea detrás de los contenedores

* Sistema Operativo recortado.
* Un único proceso corriendo: *buena práctica*.
  * No utilizan manejadores de procesos tipo systemd.
* Red pirvada bridgeada en los contenedores.
  * Si se quiere exponer un puerto se debe realizar explícitamente.
* El filesystem utiliza Union File System ([UFS](https://en.wikipedia.org/wiki/UnionFS)). 
  * Basado en capas.
  * Al eliminar un contenedor, su filesystem desaparece.

---
## Imágenes y contenedores

* Imagen:
  * Filesystem y parámetros para utilizarla.
  * No cambia nunca y no tiene estados.
* Contenedor:
  * Instancia de una imagen (resultado de ejecutarla).
  * Tiene una capa de RW volátil.

---
## Imágenes y contenedores

<img alt="Imagen de Docker" src="images/image-layers.jpg" height="400px" />

---
## Imágenes y contenedores

<img alt="Contenedor de Docker" src="images/container-layers.jpg" height="400px" />

---
## Imágenes y contenedores

<img alt="Compartiendo una imagen" src="images/sharing-layers.jpg" height="480px" />

---
## Imágenes y contenedores

<img alt="Imagen derivada" src="images/saving-space.png" height="400px" />

---
## Comandos básicos

```bash
# Más usados
docker run
docker ps
docker build
docker images
docker logs
docker inspect
docker volume

# Otros comandos comunes
docker commit
docker pull
docker push
docker tag
```
---
## Un ejemplo de uso de docker

El siguiente es un ejemplo interactivo

```bash
$ docker run --rm -it ubuntu:14.04
  Unable to find image 'ubuntu:14.04' locally
  14.04: Pulling from library/ubuntu
  c2c80a08aa8c: Pull complete 
  6ace04d7a4a2: Pull complete 
  f03114bcfb25: Pull complete 
  99df43987812: Pull complete 
  9c646cd4d155: Pull complete 
  Digest: sha256:b92dc7814b2656da61a52a50020443223445fdc2caf1ea0c51fa38381d5608ad
  Status: Downloaded newer image for ubuntu:14.04

  
  root@99a3403db59a:/# cat /etc/issue
  Ubuntu 14.04.5 LTS \n \l
```

---
## Creando imágenes

* Las imágenes docker pueden crearse: 
  * A partir de un contenedor ó
  * Usando Dockerfiles

---
## Imágenes a partir de un contenedor

Como los contenedores establecen una capa volátil por encima de la pila de capas de una imagen origen, una vez editado todo lo necesario en un contenedor, los cambios pueden comitirse en una imagen.

```
$ docker run -it  ubuntu:14.04 
root@7c78d0a777df:/# apt-get update \
  && apt-get install -y nginx && apt-get clean
root@7c78d0a777df:/# exit
```
---
## Imágenes a partir de un contenedor

Verificamos el contenedor anterior


```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS                    NAMES
7c78d0a777df        ubuntu:14.04        "/bin/bash"              4 minutes ago Exited (0) 3 seconds ago                            dreamy_joliot
```
### Creamos la imagen

```
$ docker commit 7c78d0a777df chrodriguez/nginx:ubuntu-14.04
```
<small>
A partir de ahora es posible utilizar la imagen `chrodriguez/nginx:ubuntu-14.04`
</small>

```
$ docker run --rm -p 8080:80 chrodriguez/nginx:ubuntu-14.04 \
  nginx -g "daemon off;"
```

---
## Dockerfile

* Archivo de texto plano para crear imágenes de Docker.
* Permite escribir instrucciones a ejecutar.
* Automatiza el proceso de la creación de imágenes.
* Permite repetir y modificar fácilmente una imagen.
* Generar de forma simple imágenes derivadas.

---
## Dockerfile

```bash
FROM ubuntu:16.04

# Instalar Nginx y configurar una página personalizada
RUN apt-get update && apt-get install -y nginx
RUN mkdir /var/www/html/ejemplo
RUN echo "<html><h1>Nginx en Docker</h1></html>" > /var/www/html/ejemplo/index.html

EXPOSE 80
CMD    ["nginx", "-g", "daemon off;"]
```

### Creamos la imagen

```
docker build -f Dockerfile -t chrodriguez/nginx:ubuntu-16.04 .
```


---
## Historia de imagen

```
$ docker history chrodriguez/nginx:ubuntu-16.04
  IMAGE          CREATED         CREATED BY                                      SIZE
  8fd110f3364a   9 minutes ago   /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon    0 B
  df1abe4570d5   9 minutes ago   /bin/sh -c #(nop)  EXPOSE 80/tcp                0 B
  4c9ad433769b   9 minutes ago   /bin/sh -c echo "<html><h1>Nginx en Docker</h   38 B
  aff42d6aa899   9 minutes ago   /bin/sh -c mkdir /var/www/html/ejemplo          0 B
  136943551ea1   9 minutes ago   /bin/sh -c apt-get update && apt-get install    96.07 MB
  f753707788c5   4 weeks ago     /bin/sh -c #(nop)  CMD ["/bin/bash"]            0 B
  <missing>      4 weeks ago     /bin/sh -c mkdir -p /run/systemd && echo 'doc   7 B
  <missing>      4 weeks ago     /bin/sh -c sed -i 's/^#\s*\(deb.*universe\)$/   1.895 kB
  <missing>      4 weeks ago     /bin/sh -c rm -rf /var/lib/apt/lists/*          0 B
  <missing>      4 weeks ago     /bin/sh -c set -xe   && echo '#!/bin/sh' > /u   745 B
  <missing>      4 weeks ago     /bin/sh -c #(nop) ADD file:b1cd0e54ba28cb1d6d   127.2 MB
```
---
## La registry

* Servicio para almacenar y distribuir imágenes de Docker.
* Disponible en forma local o usar servicios en la nube:
  * Instalación local:
    * Acceso local para mayor velocidad de descarga.
    * Imágenes privadas en un ambiente controlado y gestionado por la organización.
  * Servicios en la nube:
    * Generalmente las registries privadas tienen costo.

---
## Registry como servicios

* [Docker Hub](https://hub.docker.com)
  * Gratis para imágenes públicas.
  * Plan pago para imágenes privadas.
  * Soporta builds automáticos.
  * Cuentas para organizaciones.
* [Gitlab Registry](https://docs.gitlab.com/ee/user/project/container_registry.html)
  * Gratis para imágenes públicas o privadas.
  * Soporta builds automáticos.
  * Cuentas para organizaciones.

***
