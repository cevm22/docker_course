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

El siguiente comando crea un contenedor  llamado "alwaysup", con la opcion "-d" (detached) para correrlo en background, levantando un sistema "ubuntu" y el comando a ejecutar en el contenedor ubuntu"tail -f /dev/null" para que este proceso se quede corriendo: 
> docker run --name alwaysup -d ubuntu tail -f /dev/null 
el anterior comando retorna el ID del proceso.

Para poder acceder al contenedor Ubuntu sería correr el comand "exec" de docker:

> docker exec -it alwaysup bash


Para eliminar el proceso, utilizar el comando
> docker kill alwaysup

Para detener y eliminar el contenedor
> docker rm -f docker_name

Para poder exponer un contenedor y poder acceder a este se debe indicar por que puertos del host/pc/server se comunicaran.
En este caso se corre un contenedor -d en modo "background" con --name "proxy", indicando que el host escuchará en el puerto 8080 y apuntará al container en el 80 (puerto_host:puerto_container), el cual correrá un servidor ngix
>docker run -d --name proxy -p 8080:80 nginx

Para obtener TODOS logs de un container
>docker logs docker_name

Para acceder a la terminal y ver los logs se utiliza -f (significa follow)
>docker logs -f docker_name

Obtener los ultimos logs de docker se agrega -tail y los ultimos de logs por mostrar. En este caso se muestran los ultimos 10 logs
>docker logs --tail 10 -f docker_name

## Volumenes
Estos se utilizan para crear un espacio en el disco del host/pc/server en donde lo administra Docker y guarda toda la informacion que quieres que sea persistente, para cuando se destruyan los contenedores, los archivos no sean borrados con ellos. Esto es util para guardar Bases de Datos y se pueda compartir entre contenedores de manera segura, sin poder acceder a los archivos del host/pc/server.

lista de volumenes que tiene docker
>docker volume ls 

crear un volumen
>docker volume create Volume_name

crear volumen y arrancar un contenedor. donde 'src=' es el nombre del volumen  y despues de una 'comma' se indica el destino de los datos que se deasean almacenar 'dst='. En el siguiente ejemplo se arranca un contenedor con MongoDB y se crea un volumen con su destino de almacenamiento
>docker run -d --name db --mount src=dbdata,dst=/data/db mongo

Existen 3 tipos de almacenamiento o manejo de archivos.
- Bind mount: es la forma más insegura, ya que abre la posibilidad de que se pueda acceder al host/pc/server.
- Volume: es más seguro que bind mount porque Docker administra los archivos y los almacena como quiera. Pero esto dificulta un poco la manera para poder acceder a ellos, ya que sería necesario ingresar al contenedor o utilizar el comando copy
- tmpfs: es temporal file system, este es poco usado y consiste en crear un almacenamiento de información que es guardada temporalmente en la memoria virtual, por lo que no toca el disco duro y es borrado una vez se apague o borre el contenedor

En el manejo de archivos por medio de volumenes, se utiliza el comando 'cp'. Y no necesita el contenedor estar corriendo para poder manipular estos archivos

En este caso en la terminal se encuentra ubicado con un archivo llamado 'first.txt' y el contenedor se llama 'testing_copy' que seguido de dos puntos ':' se indica la ruta de guardado dentro del contenedor.
>docker cp first.txt testing_copy:/folder_test/second.txt

Para poder sacar archivos de un docker, se hace de manera inversa. Primero la ruta donde se encuentra el archivo y luego renombramos el archivo
>docker cp testing_copy:/folder_test/second.txt third.txt

# Imagenes
Las imagenes son como blueprints en donde indican las instrucciones de lo que se necesita para crear un contenedor, estas se almacenan en docker hub y por defecto cuando se busca una imagen por su nombre, esta es buscada en el docker hub. Tambien se pueden almacenar en lugares privados.

Las imagenes siempre se basan de otro software y se pone con "FROM"
los comandos RUN son acciones que quieres que haga el build en el momento de construccion de la imagen, utilizando comandos BASH

para crear un Build o imagen, se utiliza docker build -t es 'tag', por nomenclatura de docker los tags van Image_system:version y finalmente la ruta de origen donde se encuentra el contexto de build y tambien está el Dockerfile.

> docker build -t ubuntu:platzi .

# Layes/capas
Estos representan cada comando del Dockerfile, y cada capa aumenta el peso del contenedor. Por lo que es recomendable que si se van a instalar dependencias para despues borrarlos, estos se hagan en una sola capa y no en multiples. Ya que las capas funcionan como GIT, con diferencias y ocasiona aumento del peso del contenedor final. Para ver las capas en docker se utiliza el siguiente comando:
> docker history nombre_imagen:nombre_tag

Si se requiere una mejor visualizacion de las capas, se puede visitar el hub de docker o instalar una herramienta llamada Dive que se obtiene en  https://github.com/wagoodman/dive.

Tener en consideración primero instalar lo que casi no tiene cambios, en este caso serían todas las dependencias y por ultimo hacer las copias de los archivos que se agregan o modifican, esto ayudará a que no tenga que estar instalando todo nuevamente y solo haga los cambios a los archivos nuevos

# Networks entre contenedores

Para poder conectarse entre contenedores es necesario de crear "una red virtual" y agregar los contenedores a esa red, para que estas se puedan encontrar por el nombre del contenedor.

Con este comando, puedes ver todas las redes creadas
>docker network ls

En el siguiente comando, se utiliza para crear una red virtual en docker
>docker network create --attachable <network_name>

Ver la informacion de la red, es igual con inspect
>docker network inspect <network_name>

Para conectar un contenedor a la red virtual 
>docker network connect  <network_name> <container_name> 

Si es necesario de variables de entorno se utiliza lo siguiente
> docker run --env MY_VAR='' <image_name>

En este ejemplo se puede ver que en la variable de entorno está despues del doble slash // encuentras 'db', este es el nombre del contenedor que tiene el servicio corriendo de mongo db. y finalmente se coloca el nombre que se usará para el contenedor
> docker run -d --name app -p 3000:3000 --env MONGO_URL=mondodb://db:27017/db_name platziapp 

# Docker compose
Describir de forma declarativa la arquitectura de servicios que nuestra aplicacion necesita, como se comunicaran entre si, manejo de archivos, etc.
se utiliza un archivo llamado docker-compose.yml donde viene toda la configuración de docker.

y se ejecuta con el comando
> docker-compose up -d