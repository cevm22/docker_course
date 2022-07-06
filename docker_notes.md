Que es un contenedor ?

* Es una agrupación de procesos.

* Es una entidad lógica, no tiene el limite estricto de las máquinas virtuales, emulación del sistema operativo simulado por otra más abajo.

* Ejecuta sus procesos de forma nativa.

* Los procesos que se ejecutan adentro de los contenedores ven su universo como el contenedor lo define, no pueden ver mas allá del contenedor, a pesar de estar corriendo en una maquina más grande.

* No tienen forma de consumir más recursos que los que se les permite. Si esta restringido en memoria ram por ejemplo, es la única que pueden usar.

* A fines prácticos los podemos imaginar cómo maquinas virtuales, pero NO lo son. Máquinas virtuales livianas.

* Docker corre de forma nativa solo en Linux.

* Sector del disco: Cuando un contenedor es ejecutado, el daemon de docker asigna los limites de los recursos y no puede acceder a más

* Docker hace que los procesos adentro de un contenedor este aislados del resto del sistema, no le permite ver más allá.

* Cada contenedor tiene un ID único, también tiene un nombre.

===================
# Ciclo de vida de un contenedor
Cada vez que un contendor se ejecuta, en realidad lo que ejecuta es un proceso del sistema operativo. Este proceso se le conoce como Main process.

Main process
Determina la vida del contenedor, un contendor corre siempre y cuando su proceso principal este corriendo.

Sub process
Un contenedor puede tener o lanzar procesos alternos al main process, si estos fallan el contenedor va a seguir encedido a menos que falle el main.

Ejemplos manejados en el video

Batch como Main process
Agujero negro (/dev/null) como Main process

El siguiente comando crea un contenedor  llamado "alwaysup", con la opcion "-d" (detached) para correrlo en background, levantando un sistema "ubuntu" y el comando a ejecutar en el contenedor ubuntu"tail -f /dev/null" para que este proceso se quede corriendo: 
> docker run --name alwaysup -d ubuntu tail -f /dev/null 
el anterior comando retorna el ID del proceso.

Para poder acceder al contenedor Ubuntu sería correr el comand "exec" de docker:

> docker exec -it alwaysup bash


Importante saber que por cada contenedor que ejecuta docker, este crea un proceso en el sistema operativo, por lo que si quieres eliminarlo en Linux es necesario saber que Process ID tiene el contenedor con el siguiente comando.
> docker inspect --format '{{.State.Pid}}' alwaysup
y después usas el comando > kill proces_id para detener el contenedor en docker.