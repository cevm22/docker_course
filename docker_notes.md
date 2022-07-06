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