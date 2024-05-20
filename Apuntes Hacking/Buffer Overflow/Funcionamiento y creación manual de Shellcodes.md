


Dependiendo del sistema operativo el shellcode va a cambiar por ejemplo vamos a crear un binario para linux que imprima un hola mundo para eso ejecutamos el siguiente comando 


![[Pasted image 20240312081250.png]]


Al ejecutar el binario que se ha creado podemos ver que efectivamente imprime hola mundo


![[Pasted image 20240312081349.png]]


Ahora lo vamos a ver en formato raw para eso ejecutamos el siguiente comando

![[Pasted image 20240312081527.png]]

Y para poder trabajar mejor lo pasamos por xxd 


![[Pasted image 20240312081620.png]]

Podemos ver que al ser de 32 bits y tener little endian algunas cadenas estan al reves pero claramenente podemos ver que usa /bin/sh con el parametro -c y luego ya coloca el echo hola mundo 

![[Pasted image 20240312082114.png]]

Esta haciendo practicamente esto

![[Pasted image 20240312082219.png]]


Ahora lo veremos en codigo ensamblador con el siguiente comando 

![[Pasted image 20240312083309.png]]

Al ejecutarlo nos muestra diferentes instrucciones al final de este nos muestra algo llamado int 0x80 esto lo que hace es que aplica una interrupcion en el sistema en si lo que hace es aplicar llamadas a nivel de sistema


![[Pasted image 20240312083743.png]]

Esto como tal necesita un numero con el cual le indicas que llamada quieres aplicar entonces a modo de ejemplo vamos a utilizar strace con la cual podemos ver a mas bajo nivel un binario entonces para eso ejecutamos este comando en donde utiliaremos el binario antes creado 

![[Pasted image 20240312085345.png]]

Al ejecutralo podemos ver que para hacer el hola mundo esta utilizando write


![[Pasted image 20240312085503.png]]

Para entender mejor como es la estructura de write algo que podemos hacer es ver el manual para eso podemos ejecutar el siguiente comando

![[Pasted image 20240312085642.png]]

Aqui podemos ver la estructura de la funcion write la cual se compone de tres campos el primero corresponde a un entero en donde se define el tipo de salida el segundo campo es el que contiene la direccion de donde esta contenida la cadena que quieres mostrar por consola y el tercer campo corresponde al tamaño de la cadena 

![[Pasted image 20240312090836.png]]


Ahora que entendemos como es que funciona write tenemos que saber cual es el identificador de esta llamada esto lo podemos ver en el archivo /usr/include/asm/unistd_32.h en este caso el identificado de write es el numero 4

![[Pasted image 20240312091937.png]]


Entonces si quisieramos hacer un shellcode que permitiera mostrar algo por consola se tendira que hacer una interrupcion cargando el numero 4 para indicar que queremos usar write teniendo esto en cuenta vamos a crear un archivo el cual va a contener un poco de codigo ensamblador para eso primero creamos un archivo con la extension .asm 


![[Pasted image 20240312093304.png]]


Ya creado el archivo vamos a comenzar creando una seccion .text a traves de la cual vamo a definir una variable global que se llame start que es donde vamos a indicar el comienzo del programa 

![[Pasted image 20240312093533.png]]


Podemos crear un comentario de la siguiente manera


![[Pasted image 20240312094447.png]]


Entonces si queremos crear una interupcion para aplicar un write tenemos que colocar un int 0x80 o en su defecto int 80h al final 

![[Pasted image 20240312094658.png]]

Existen ciertos registros donde los programas a nivel de argumentos lee como lo son los registros eax, ebx, ecx y edx entonces cuando aplicamos una interrupcion aqui lo va a pasar es que va a leer a nivel de argumentos por el registro eax entonces lo que tienes que hacer es cargar en eax el valor correspondiente al tipo de llamada que quieres hacer en este caso es un write por lo tanto podemos hacer lo siguiente en donde el numero 4 se mueve al registro eax

![[Pasted image 20240312095133.png]]

Teniendo esto ahora si vamos a ejecutar el write necesitamos indicar cual es tipo de salida entonces para eso movemos el numero 1 al registro a ebx este numero lo que indica es que usaremos el stdout para asi mostrar la cadena en consola 


![[Pasted image 20240312095436.png]]


Seguidamente lo que podemos hacer es pushear a la pila la cadena que queremos imprimir para que esta este en la parte mas alta de la pila de forma que si ahora indicamos como direccion de donde se encuentra nuestra cadena el esp ahi va estar la cadena etonces si queremos mostrar en la pantalla un hola mundo  como estamos en 32 bits tenemos que partir la cadena de 4 bytes en 4 bytes para luego codificarla en hexadecimal y luego ponerla en little endian entonces sabiendo esto primero tenemos que partir la cadena hola mundo de 4 bytes en 4 bytes por lo que la primera parte seria Hola asi que ahora esta la codificamos en hexadecimal 


![[Pasted image 20240312104404.png]]

Ya con esta parte de la cadena en hexadecimal ahora en el script hacmeos un push con esta  pero en little endian

![[Pasted image 20240312104633.png]]

Entonces siguiendo con el mismo concepto hacemos esto con los siguiente 4 bytes en este caso corresponden a " mun" esto lo codificamos a hexadecimal 

![[Pasted image 20240312104948.png]]

Ahora igual que como lo hicmos anteriormente lo integramos al script pero en little endian quedaria de la siguiente manera

![[Pasted image 20240312105146.png]]

Luego hacemos lo mismo pero con los ultimos bytes que quedan en este caso estos corresponden a "do" asi que lo convertimos a hexadecimal

![[Pasted image 20240312112118.png]]

Hacemos un push con este valor mas un salto de linea todo esto lo ponemos en little endian


![[Pasted image 20240312120932.png]]

Ya con todo esto pusheado la cadena estara hasta arriba de la pila entonces ahora movemos la direccion del esp en ecx lo cual ocasionara que ahora ecx apunte a esp que es donde se encuentra nuestra cadena y ahora write ya sabra que cadena represntara 


![[Pasted image 20240312121114.png]]

Por ultimo indicaremos el tamaño de la cadena en este caso es de 11 bytes de longitud

![[Pasted image 20240312121151.png]]

Excelente entonces ya tendriamos una forma aplicar una interrupcion para que haga un write con el objetivo de que nos muestre en consola un hola mundo es script final seria el siguiente

```nasm
section .text
  global _start

_start:

  mov eax, 4 ; sys_write
  mov ebx, 1 ; stdout
  push 0x0a6f64 ; do
  push 0x6e756d20 ; mun
  push 0x616c6f48 ; Hola

  mov ecx, esp ; puntero a la cadena 
  mov edx, 11 ; longitud de la cadena
  int 80h ; Interrupción 
```

Ya con el codigo finalizado entonces tendiramos que crear un binario para eso primero tendremos que crear un archivo .o de la usando el comando nasm de la siguiente manera 

![[Pasted image 20240312114847.png]]

Ahora con este archivo .o vamos a hacer el binario final para eso vamos a utlizar el comando ld  como se muestra a continuacion 


![[Pasted image 20240312115220.png]]

Y listo esto nos ha creado un binario el cual al ejecutarlo nos muestra el mensaje que especificamos 

![[Pasted image 20240312121252.png]]

Nos esta mostrando un error el cual lo esta poniendo ya que como estamos en el tope de la pila cargando la cadena se queda configurado con esos valores y el EIP luego ya no sabe a donde apuntar por lo que podrimas generar una nueva interrupcion donde ahora cargemos otro nuevo identificador que corresponda a otra llamada a nivel de sistema tal es el caso de exit el cual como podemos ver vale 1

![[Pasted image 20240312121703.png]]

Viendo en el manual la estructura de exit podemos ver que solo necesitamos pasarle el codigo de estado 


![[Pasted image 20240312122815.png]]

Entonces ya sabiendo esto lo que tenemos que hacer es modificar el script para que se haga una nueva interrupcion correspondiente a un sys_exit entonces si queremos que aplique un exit tenemos que poner el identificador de llamada de exit en eax y luego especificar el codigo de estado exitoso haciendo un xor entre el mismo registro ebx 

![[Pasted image 20240312124141.png]]

Por lo que el codigo final quedaria de la siguiente manera 


```nasm
section .text
  global _start

_start:

  mov eax, 4 ; sys_write
  mov ebx, 1 ; stdout
  push 0x0a6f64 ; do
  push 0x6e756d20 ; mun
  push 0x616c6f48 ; Hola
  
  mov ecx, esp ; puntero de la cadena
  mov edx, 11 ; longitud de la cadena

  int 80h ; Interrupción 
  
  mov eax, 1 ; exit
  xor ebx, ebx ; exit(0)

  int 80h ; Interrupción 

```

Ahora repetimos los mismo pasos que hicmos para genera el binario primero cremaos el archivo .o de la siguiente manera 

![[Pasted image 20240312123758.png]]

Y luego con el archivo .o hacemos el binario final como se muestra a continuacion 

![[Pasted image 20240312123837.png]]

Si ejecutamos el binario resultante podemos ver que nos imprime el mensaje sin ningun error 


![[Pasted image 20240312124248.png]]


Ahora al hacer utlizar el objdump en nuestro binario final podemos ver que nos muestra algo muy parecido a lo que hicimos el script

![[Pasted image 20240312125818.png]]

Entonces ahora de lo que nos vamos a aprovechar va a ser de lo siguiente 

![[Pasted image 20240312125859.png]]

Para eso con el siguiente comando vamos a generar nuestro shellcode

![[Pasted image 20240312130249.png]]


Y listo al ejecutarlo podemos ver que nos da un shellcode el cual aplica un echo hola mundo a nivel de sistam  lamentablemente esto incluira algunos badchars


![[Pasted image 20240312130328.png]]

De la misma forma tambien podraiamos generar un shellcode que se encarguen de darnos una /bin/sh por ejemplo al ver los identificadores de las llamadas al sistema podemos ver que existe una que se llama execve que tiene el identificador 11 y con la cual podriamos hacer una llamada a nivel de sistema que ejecute un comando


![[Pasted image 20240312131257.png]]


Ahora si verificamos la estructura de execve en el manual nos podemos dar cuenta que necesitamos proporcionar 3 valores el primero es el pathname el cual va a ser la direccion del ESP que es donde se va a pushear la cadena /bin/sh y al segundo y tercero se les cargara un cero 

![[Pasted image 20240312132845.png]]

Entonces si queremos adaptar el script lo que podemos hacer es aplicar una interrupcion para luego mover al registro eax el identificador de la llamada al sistema que queremos hacer en este caso es el numero 11 


![[Pasted image 20240312133324.png]]

Ya que execv va a leer de 3 valores por tanto el primero tiene que estar en ebx que va ser la direccion de donde esta nuestra cadena /bin/sh entonces para cuadrarlo en pares de 4 lo podemos cuadrar colocando una barra adicional (/bin//sh)ya que esto a fin de cuentas es lo mismo que ejecutarlo son lo con una barra entonces para eso primero codifcamos en hexadecimal los primero 4 bytes de cadena como se muestra a conitnuacion 


![[Pasted image 20240312134443.png]]


Y entonces en el script hacemos un push incluyendo este valor en hexadecimal pero en little endian 


![[Pasted image 20240312181211.png]]

Ahora convertimos los siguientes 4 bytes a hexadecimal los cuales corresponden a //sh

![[Pasted image 20240312181408.png]]

Ahora lo incluimos al script haciendo un push pero con este en little endian 


![[Pasted image 20240312181602.png]]


Ya por ultimo como esto lo estamos pusheando al tope de la pila vamos a indicarle que hay una terminacion de cadena por lo que hacemos un push con un valor nulo 


![[Pasted image 20240312181847.png]]

Por tanto si execve lo que hace es que lee por tres argumentos el primero va ser de ebx por tanto en ebx tiene que estar la direccion de ESP por tanto movemos la direccion de esp a ebx

![[Pasted image 20240312182051.png]]


Y ahora ecx  y edx tiene que tener un valor ya que execve va a leer de estos se le puede cargar un cero esto lo puedes hacer haciendo un xor entre ellos mismos


![[Pasted image 20240312182336.png]]


Entonces este script ya estaria finalizado este seria el script final 


```nasm
section .text

  global _start

_start:

  mov eax, 11 ; sys_execve
  push 0x0 ; Terminación de la cadena
  push 0x68732f ; //sh
  push 0x6e69622f; /bin

  mov ebx, esp
  xor ecx, ecx ; ecx -> 0


  xor edx, edx ; edx -> 0

  int 80h ; Interrupción 

```


Ahora lo que tenemos que hacer es generar el binario por lo que para eso primero generamos un archivo .o de la siguiente manera

![[Pasted image 20240312182616.png]]

Ya con el archvio .o este lo utilizamos para genera el binario final con el siguiente comando 

![[Pasted image 20240312182657.png]]

Entonces ahora si nos ejecutamos el binario final podemos ver que nos ha dado una sh

![[Pasted image 20240312183007.png]]

Si esto lo queremos hacer un shellcode podemos utilizar el comando que utlizamos anteriormente el cual al ejecutarlo nos daria el siguiente shellcode

![[Pasted image 20240312183141.png]]

Y listo de esta manera ya tendrias una shellcode que te da una /bin/sh




