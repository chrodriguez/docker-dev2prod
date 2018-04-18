***
# Docker en producción

---
## Dos enfoques

* Usar Docker para iniciar servicios de forma aislada.
* Usar un cluster de Docker.

---
## Docker en forma aislada

* Cada servidor Linux corre el servicio de Docker.
* Los contenedores pueden iniciarse automáticamente durante el booteo usando:
  * Manejadores de procesos: [upstart](http://upstart.ubuntu.com/), [systemd](https://freedesktop.org/wiki/Software/systemd/) o [supervisor](http://supervisord.org/), etc
  * A través de políticas de reinicio (Docker >= 1.2).

---
## Clusters docker

Un cluster dispone de nodos corriendo Docker Engine de tal forma de poder utilizarlos para correr contenedores.

Estos nodos pueden usar SO muy pequeños (~ 50MB) dado que su única razón de ser es la de proveer un kernel con docker engine:
* [RancherOS](https://rancher.com/rancher-os/)
* [CoreOS](https://coreos.com/products/container-linux-subscription/)
* [VMWare Photon](https://vmware.github.io/photon/)
* [Boot2docker](https://github.com/boot2docker/boot2docker)

---
### Los clusters más conocidos

<table class="product_logos" >

<tr>
<td> <img alt="swarm" src="images/docker-whales.png" /> </td>
<td><a href="https://docs.docker.com/engine/swarm/">Swarm </a></td>
</tr>

<tr>
<td> <img alt="rancher" src="images/rancher-logo.png" /> </td>
<td> <a href="http://rancher.com/">Cattle/Rancher</a> </td>
</tr>

<tr>
<td> <img alt="kubernetes" src="images/kubernetes-logo.png" /> </td>
<td> <a href="http://kubernetes.io/">Kubernetes</a> </td>
</tr>

<tr>
<td> <img alt="mesos" src="images/mesos-logo.png" /> </td>
<td> <a href="http://mesos.apache.org/">Apache Mesos</a> </td>
</tr>

</table>

---
## Características de todos los clusters

* Diseño descentralizado.
* Servicios, pods o stacks en vez de contenedores.
* Posibilidad de escalar.
* Conciliación para alcanzar el estado deseado.
* Service discovery.
* Load balancing.
* Actualizaciones en caliente.

---
## Consideraciones

* El scheduler es el encargado de determinar dónde se inicia cada contenedor.
* Asociado al scheduler trabajan los health checks que garantizan la
  conciliación de un estado deseado: que haya N contenedores para el servicio X.
* La distribución mágica del scheduler complica el manejo de volúmenes.
  * Los volúmenes pertenecen a un nodo.
  * Si el nodo cambia, se pierden los datos.

---
## Volúmenes distribuidos

* Necesidad de compartir datos entre los nodos del cluster.
* Aparecen diferentes implementaciones de volúmenes compartidos. Las más
  populares son:
  * NFS
  * [Flocker](https://clusterhq.com/flocker/introduction/)

---
## Rancher

* Interfaz web amigable para gestionar un cluster de Docker.
* Incluye una API que permite administrar el cluster.
* Utilidades de línea de comandos.
* Soporta múltiples plataformas de clustering para Docker:
  * Cattle
  * Kubernetes
  * Swarm
  * Apache Mesos
  * Windows

---
## Ejemplo Rancher

![sample](images/rancher-sample.png)
***
