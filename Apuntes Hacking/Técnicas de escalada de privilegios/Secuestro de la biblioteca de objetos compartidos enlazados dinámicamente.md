



Tenemos un ejemplo en donde tenemos una maquina que tiene un binario que esta hecho en c el cual al ejeuctarlo nos muestra un numero random


![[Pasted image 20240302191157.png]]


Con ayuda de la herramienta https://github.com/namhyung/uftrace vamos a usar el siguiente comando el cual nos mostrara cual es la ejecucion del programa y que funciones usa


```bash
uftrace --force -a binario
```


Al ejecutarlo nos muestra algunas de las funciones que utiliza

![[Pasted image 20240302192537.png]]

Entonces vamos a tratar de secuestrar la funcion rand para que el valor que nos retorne sea un siempre un numero que nosotros le hayamos especificado entonces para eso primero tendremos que hacer una funcion rand falsa que coincida a nivel de firma con el rand es decir que contemple nombre, argumentos y tipos de retorno para esto podemos usar el manual y ver cual es la firma de esta funcion la cual es la siguiente 

![[Pasted image 20240302193749.png]]

Entonces si queremos secuestrar la funcion rand tenemos que copiar la misma estructura por lo que podemos crear un archivo en c que tenga una funcion con lo mismo solo que ahora esta retornara el numero 42 como se muestra a continuacioan

![[Pasted image 20240302194202.png]]

Ahora bien cual es el punto cuando ejecutas un programa el enlazador dinamico carga las bibliotecas compartidas necesarias para que todo este programa funcione correctamente pero el enlazador dinamico funciona algo peculiar ya que toma como prioritario aquello que venga de una variable de entorno LD_PRELOAD entonces si esa variable se iguala a un binario correspondiente a una biblioteca compartida que previamente se haya compilado en este caso podemos crear una biblioteca compartida con el anterior archivo que hemos realizado de la siguiente manera

```bash
gcc -shared -fPIC archivo.c -o nombre_biblioteca
```

Entonces al ejecutar esto ya tendriamos una biblioteca con la funcion rand y entonces lo que podriamos hacer es lo siguiente para que ahora LD_PRELOAD cargue nuestra biblioteca y la funcion rand ahora retorne lo que nosotros hemos definido anteriormente


![[Pasted image 20240302195439.png]]


Ahora tenemos otro ejemplo en el cual existe un binario que tiene permisos suid y su propietario es root

![[Pasted image 20240302200816.png]]

Al ejecutarlo nos muestra un error ya que al parecer no puede cargar las bibliotecas compartidas

![[Pasted image 20240302200940.png]]

Cuando hacemos un ldd no vemos nada interesante solo nos dice lo mismo que no puede encontrar las bibliotecas compartidas

![[Pasted image 20240302201209.png]]


Lamentablemente LD_PRELOAD no funciona en este ejemplo entonces lo que podiamos hacer es ver si tenemos capacidad de escritura en /etc/ld.so.conf.d/ 

![[Pasted image 20240302203317.png]]

Desafortunadamente no tenemos permisos para escribir si se hubiera podido escribir dentro de el se habria creado un archivo de configuracion a traves del cual indicar a que ruta tiene que ir para encontrar el libwelcome.so al investigar un poco mas dentro del directorio vemos un archivo donde la parecer toma bibliotecas de un directorio especificado


![[Pasted image 20240302204605.png]]


Al dirigrnos al directorio que muestra al parecer este no existe por lo que lo podemos crear 

![[Pasted image 20240302204801.png]]

Ahora lo que podemos hacer es crear la biblioteca compartida que no encuentra en el directorio lib para eso creamos un archivo con lo siguiente este¿o lo que hara es falsificar la funcion welcome y como el usuario del binario welcome es root y tiene permiso suid va  a lanzarse una bash como root

![[Pasted image 20240302205802.png]]

Ahora solo compilamos como biblioteca compartida con el nombre de la biblioteca que se muestra al hacer ldd


![[Pasted image 20240302210159.png]]


Si vemos cual biblioteca compartida usa el binario de welcome podemos ver que ahora toma la biblioteca compartida que acabamos de crear 

![[Pasted image 20240302210333.png]]

Entonces ahora si ejecutamos el binario welcome podemos ver que nos da una bash como usuario root

![[Pasted image 20240302210805.png]]


Esos han sido algunos ejemplos de como nos podemos aprovechar de la bibiliotecas compartidas y asi secuestrarlas para poder escalar privilegios
