***
# Volúmenes
---
## ¿Cómo se persisten los datos?

* Los contenedores son volátiles e inmutables.
* Debemos preservar la información importante.
* ¿Dónde?
  * En volúmenes de datos.

---
## Características de los volúmenes

* No utilizan un sistema de archivos de unión (UFS).
* Pueden compartirse y reusarse entre contenedores.
* Los cambios se hacen directamente en el volumen.
* La información del volumen **no se incluye** en la imagen.
* Persisten aún cuando se eliminen todos los contenedores que los usan.
  * Pueden quedar volúmenes sin referenciar.

---
## Tipos de volúmenes

* Volúmenes anónimos.
* Volúmenes nombrados.
* Volúmenes desde el SO host.

---
## Tipos de volúmenes

* Al crear un volúmen anónimo o nombrado, la información que exista en el punto de montaje se copia al volumen.
* Con volúmenes desde el SO host o desde otro contenedor, se oculta la información que exista en el punto de montaje.
  * Correspondencia con el comando mount.

---
## Manejo de Volúmenes

```bash
$ docker run -it -v /prueba ubuntu:16.04 /bin/bash
  root@a9c1a6e6c0ea:/# ls /prueba/
  root@a9c1a6e6c0ea:/# echo "Prueba" > /prueba/archivo
  root@a9c1a6e6c0ea:/# exit

$ docker ps -a
  CONTAINER ID  IMAGE    COMMAND       CREATED        STATUS                         NAMES
  a9c1a6e6c0ea  ubuntu   "/bin/bash"   2 minutes ago  Exited (0) About a minute ago  small_jennings

$ docker volume ls
  DRIVER    VOLUME NAME
  local     e9c7022b8c7bec55891ca44b8c40de1e5f41cf0fe9505a334bca06a484a5ff1f

$ ls /var/lib/docker/volumes/e9c7022b8c7bec55891ca44b8c40de1e5f41cf0fe9505a334bca06a484a5ff1f/_data
  archivo
```

---
## Volúmenes nombrados

```bash
$ docker run -it -v test:/prueba ubuntu /bin/bash
  root@7def6f99f957:/# ls /prueba/
  root@7def6f99f957:/# echo "Prueba" > /opt/archivo
  root@7def6f99f957:/# exit

$ docker ps -a
  CONTAINER ID  IMAGE    COMMAND       CREATED        STATUS                         NAMES
  7def6f99f957  ubuntu   "/bin/bash"   2 minutes ago  Exited (0) 2 minutes ago       mad_mccarthy

$ docker volume ls
  DRIVER    VOLUME NAME
  local     test

$ ls /var/lib/docker/volumes/test/_data
  archivo
```

***
