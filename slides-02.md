***
# Consideraciones para trabajar con docker
---
## Introducción

* Ya sabemos que:
  * Las imágenes Docker son inmutables.
  * Los contenedores crean una capa con las diferencias correspondientes respecto de la imagen original.
* Entonces los contenedores deberían minimizar los cambios respecto de la imagen original.
  * Optimizando el uso de espacio y evitando impactos de performance.
  * Promoviendo la reusabilidad.

---
## Inmutabilidad en la infraestructura

* Desplegar una actualización de una aplicación, consiste en crear nuevas intancias y destruir las anteriores, en vez de actualizarlas sobre la instancia productiva.
* Una vez que una aplicación está corriendo, **¡evitamos tocarla!** promoviendo así:
  * Repetibilidad.
  * Reducir costos de mantenimiento.
  * Simplificar rollbacks.

---
## Lograr inmutabilidad

* Deben cumplirse los siguientes requerimientos:
  * La aplicación debe ser stateless. Su estado debe almacenarse en un servicio por fuera del alcance de la *infraestructura inmutable*.
  * Existe un template y/o conjunto de instrucciones que permiten desplegar una instancia de la aplicación desde cero.

<small class="fragment">
*El segundo punto lo resuelve fácilmente Docker.*
</small>
---
## ¿Qué es dinámico entonces?

* Archivos que se generan por la aplicación.
* Uploads desde la aplicación.
* Logs.
* Spool.

---
## Imágenes mal formadas

Un mal diseño de las imágenes impactará en la performance de los contenedores que generarán grandes capas con datos dinámicos.

Ante la actualización del contenedor, estos datos se perderán.

---
## ¿Cómo verificar un mal diseño?

La opción `-s` visualiza el tamaño de la capa del contenedor. Su valor **debe tender a cero**

```bash
$ docker ps -s

docker ps -s
CONTAINER ID        IMAGE          ....        SIZE
6ce39ac62830        ubuntu:16.04   ....        0B (virtual 120MB)
```

```bash
root@6ce39ac62830:/# echo "hola" > /tmp/prueba
```

```bash
$ docker ps -s

docker ps -s
CONTAINER ID        IMAGE          ....        SIZE
6ce39ac62830        ubuntu:16.04   ....        5B (virtual 120MB)
```

---
## ¿Cómo verificar un mal diseño?

<small>
El tamaño es lo que crece el contenedor respecto de la imagen. El tamaño virtual es lo que ocupa el contenedor sumado al tamaño de la imagen.
</small>

```bash
root@6ce39ac62830:/# dd if=/dev/zero of=/tmp/lala.img bs=1M count=10
10+0 records in
10+0 records out
10485760 bytes (10 MB, 10 MiB) copied, 0.0127865 s, 820 MB/s
```

```bash
$ docker ps -s

docker ps -s
CONTAINER ID        IMAGE          ....        SIZE
6ce39ac62830        ubuntu:16.04   ....        10.5MB (virtual 131MB)
```

```bash
$ docker diff 6ce39ac62830
C /tmp
A /tmp/lala.img
A /tmp/prueba
```

---
## Buenas prácticas

* **Los contenedores deben ser efímeros:** pararlos, destruirlos y volverlos a iniciar con una mínima configuración.
* **Evitar paquetes innecesarios:** las imágenes no deben incluir paquetes que no se utilicen.
* **Un proceso por contenedor:** en la mayoría de los casos, se debe correr un proceso por contenedor.
* **La (in)necesidad de ssh**: acceder a un contenedor es algo que debemos evitar.
***

