***
# Workflow de desarrollo
---
## Los equipos de desarrollo...

* Deben
  * Versionar el código: SVN, GIT.
  * [Utilizar Versionado semántico](https://semver.org/).

* Deberían
  * Aplicar Testing: TDD o mejor aun BDD.
  * Code Review
  * Integración continua.
  * Flujos claros de versionado de código: git-flow, GitLab Flow, Github Flow.
---
## La infraestructura...

Debe proveer ambientes diferentes para poder aplicar controles de calidad previos a la puesta en producción:

* Preproducción
* QA
* Pruebas

Automatizar la creación y mantenimiento de estos ambientes

---
## El versionado

* Implementando un flujo para el uso del versionado de código, se realizan varios merge a la rama principal, *generalmente master*.
  * Integración continua.
  * Correr tests antes de cada merge.
  * Code review: *simple con flujos propuestos por GitHub/Gitlab a través PR/MR.*

---
## Nuevos releases
* Un nuevo release respeta semver, entonces su nombre será  **X.Y.Z**.
  * Según el lenguaje, será necesario compilar y subir el binario a un repositorio: [Artifactory](https://jfrog.com/artifactory/) o [Nexus](https://www.sonatype.com/nexus-repository-sonatype).
  * Con docker es el momento de crear una imagen docker y subirla a la registry

<small>
Si automatizamos estas tareas estamos implementando [entrega continua](https://en.wikipedia.org/wiki/Continuous_delivery)
</small>

---
## Despliegue de versiones

* No es lo mismo un despliegue de cero que una actualización
  * Existe estrategias de actualización:
    * [Canary deployment](https://martinfowler.com/bliki/CanaryRelease.html)
    * [Blue Green deployment](https://martinfowler.com/bliki/BlueGreenDeployment.html)
* Esta tarea es complicada si se realiza manual
  * Sobre todo con aplicaciones que escalan horizontalmente
  * **Docker es una gran simplificación a este problema**
***
