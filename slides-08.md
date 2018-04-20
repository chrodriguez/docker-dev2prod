***
# ¿Preguntas?
***
## Algunas perlitas

Correr una aplicación X11:

```
$ xhost +
$ docker run -it --rm -e HOME=$HOME -e DISPLAY=$DISPLAY \
  -v /tmp/.X11-unix:/tmp/.X11-unix -v $HOME:$HOME --user `id -u` \
  jarfil/gimp-git
```

Compilar sin compilador:
```
$ docker run --rm -v "$PWD":/usr/src/myapp \
  -w /usr/src/myapp gcc:4.9 gcc -o app *c -Wall
```

Bases de datos para desarrollo

```
docker run --name=mysql-5.6 -d \
  -e MYSQL_ALLOW_EMPTY_PASSWORD=true -p 3307:3306 \
  --restart=always -v mysql-5.6:/var/lib/mysql \
  mysql:5.6
```

***
# ¡Gracias!
***
