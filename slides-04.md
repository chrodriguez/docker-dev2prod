***
# Docker Compose
---
## ¿Qué es Docker Compose?

* Herramienta que permite correr aplicaciones compuestas por múltiples contenedores.
* La arquitectura se define y configura en un archivo de texto ([YAML](http://yaml.org)).
  * Simple e intuitivo.
* Se vale de un comando para:
  * Iniciar, detener y reconstruir servicios.
  * Ver el estado de los servicios, los logs, etc.

---
## Versiones de Docker Compose

* Hay tres versiones mayores diferentes, la 1, la 2 y la 3.
* Entre la 1 y la 2 no son compatibles entre sí, entre la 2 y la 3 comparten estructura, pero se quitan algunas opciones en la 3.
* Veremos la sintaxis de la versión 3.

---
## Docker Compose: ejemplo

* Instalación de Wordpress.
  * Vamos a crear un archivo llamado `docker-compose.yml`.
  * Definiremos allí la arquitectura de la aplicación.
  * Nos valdremos del comando `docker-compose` para levantar Wordpress e interactuar con los contenedores generados.

---
## Docker compose para wordpress

```bash
version: '3'

services:
  db:
    image: mysql:5.7
    volumes:
      - dbdata:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: super_secret
      MYSQL_DATABASE: wordpress

  wordpress:
    depends_on:
      - db
    image: wordpress:4.9.5-php7.0-apache
    links:
      - db
    ports:
      - "80:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_PASSWORD: super_secret
    volumes:
      - site:/var/www/html

volumes:
  dbdata:
  site:

```
<small>
Descargar [docker-compose.yml](images/ejemplos/docker-compose/docker-compose.yml)
</small>

---
## Iniciando los servicios de Wordpress

```bash
$ docker-compose up -d
Creating network "wordpress_default" with the default driver
Creating volume "wordpress_dbdata" with default driver
Creating volume "wordpress_site" with default driver
Creating wordpress_db_1 ... done
Creating wordpress_wordpress_1 ... done

$ docker-compose ps
        Name                       Command               State         Ports       
-----------------------------------------------------------------------------------
wordpress_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp          
wordpress_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp

```

---
## Usamos Wordpress

<img alt="" src="images/compose-wordpress.png" height="320px" />

```bash
$ docker-compose logs -f
wordpress_1  | 172.27.0.1 - - [18/Apr/2018:17:40:45 +0000] "GET / HTTP/1.1" 302 374 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36"
wordpress_1  | 172.27.0.1 - - [18/Apr/2018:17:40:45 +0000] "GET /wp-admin/install.php HTTP/1.1" 200 4310 "-" "Mozilla/5.0 (X11; Linux x86_64)
AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36"
wordpress_1  | 172.27.0.1 - - [18/Apr/2018:17:40:47 +0000] "GET /favicon.ico HTTP/1.1" 200 228 "http://localhost/wp-admin/install.php" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181
Safari/537.36"
```

---
## Iniciando y deteniendo Wordpress

Para detenerlo
```bash
$ docker-compose stop
  Stopping wordpress_wordpress_1 ... done
  Stopping wordpress_db_1 ... done
```

Para iniciarlo
```
$ docker-compose start
  Starting db ... done
  Starting wordpress ... done
```

<small>
docker-compose down detiene y luego remueve los contenedores
</small>
***

