


Tenemos un ejempmo el cual es un binario el cual tiene permisos suid y el propietario es root


![[Pasted image 20240225213505.png]]

Al ejecutarlo podemos ver que nos muestra dos veces cual usuario somos en este caso muestra root ya que tiene el permiso suid 

![[Pasted image 20240225213637.png]]

Al hacerle un file al archivo nos muestra que es un binario compilado para linux de 64 bits por lo cual no contiene contenido legble

![[Pasted image 20240225213841.png]]


Lo que podemos hacer es utilizar strings para asi poder listar las cadenas de caracteres imprimibles del binario y esto es mas legible

![[Pasted image 20240225214101.png]]

Podemos suponer que el comando que esta ejecutando es whoami asi que filtramos con grep por whoami y vemos lo siguiente una primera linea que ejecuta el comando whaomi con ruta absoluta y una segunda linea que lo ejecuta con ruta relativa

![[Pasted image 20240225214213.png]]

Al usar el comando de forma relativa eso supone un riesgo ya que nos podemos aprovechar y hacer el path hijacking lo que podemos hacer entonces es modificar la variable de entorno del path de la siguiente manera


```bash
export PATH=ruta_al_script_malicioso:$PATH
```

ahora si hacemos esto en maquina vemos que la ruta donde esta el script malicioso se vuelve prioritaria y ahora sera la primera ruta donde se verificara si existe un binario


![[Pasted image 20240226184047.png]]


Entonces haremos lo siguiente primero crearemos un archivo llamado whoami y le otorgaremos permisos de ejecucion 


![[Pasted image 20240226194658.png]]

Despues dentro del archivo colocamos lo que queremos que haga a modo de ejemplo podemos decirle que nos de una bash como usuario privilegiado con el siguiente comando aprovechando de que es root quien ejecuta el comando 

```bash
bash -p
```


Ahora al momento de ejecutar el binario lo que va a pasar es que al momento de que haga el segundo whoami como lo hace de forma relativa vamos a secuestrar el binario ya que el path contiene la ruta donde se encuentra el script malicioso y ahora el whoami que va a ejeuctar va a ser el que hemos definido anteriormente y nos dara una shell como usuario root


![[Pasted image 20240226195332.png]]

Perfecto asi es como nos podemos aprovechar del path para elevar privilegios





# PATH Hijacking


El PATH Hijacking es una técnica utilizada en seguridad informática que aprovecha la configuración de la variable de entorno PATH en sistemas Unix y Unix-like. Cuando un programa se ejecuta sin especificar la ruta completa del ejecutable, el sistema busca el archivo ejecutable en las rutas especificadas en la variable PATH en un orden específico. Los atacantes pueden aprovechar esta configuración manipulando el PATH para que apunte a un archivo malicioso que imite a un comando legítimo. Este método puede llevar a la ejecución de comandos no deseados o incluso a la escalada de privilegios si el comando se ejecuta con permisos elevados.

Tenemos un ejemplo en el que un binario tiene permisos SUID y el propietario es root.

![[Pasted image 20240225213505.png]]

Al ejecutarlo, podemos ver que muestra dos veces el usuario actual, en este caso, muestra "root" debido a que tiene el permiso SUID.

![[Pasted image 20240225213637.png]]

Al usar el comando "file" en el archivo, vemos que es un binario compilado para Linux de 64 bits y no contiene contenido legible.

![[Pasted image 20240225213841.png]]

Lo que podemos hacer es utilizar "strings" para listar las cadenas de caracteres imprimibles del binario, lo que resulta en una salida más legible.

![[Pasted image 20240225214101.png]]

Podemos suponer que el comando que se está ejecutando es "whoami". Al filtrar con "grep" por "whoami", vemos una primera línea que ejecuta el comando "whoami" con ruta absoluta y una segunda línea que lo ejecuta con ruta relativa.

![[Pasted image 20240225214213.png]]

Al usar el comando de forma relativa, esto supone un riesgo ya que nos podemos aprovechar y hacer el "path hijacking". Entonces, podemos modificar la variable de entorno del PATH de la siguiente manera:

```bash
export PATH=ruta_al_script_malicioso:$PATH
```

Al hacer esto en la máquina, vemos que la ruta donde está el script malicioso se vuelve prioritaria y ahora será la primera ruta donde se verificará si existe un binario.

![[Pasted image 20240226184047.png]]

Entonces, haremos lo siguiente: primero crearemos un archivo llamado "whoami" y le otorgaremos permisos de ejecución.

![[Pasted image 20240226194658.png]]

Luego, dentro del archivo, colocamos lo que queremos que haga. A modo de ejemplo, podemos decirle que nos dé una bash como usuario privilegiado con el siguiente comando, aprovechando que es root quien ejecuta el comando:

```bash
bash -p
```

Ahora, al ejecutar el binario, lo que sucederá es que, al momento de hacer el segundo "whoami" como lo hace de forma relativa, vamos a secuestrar el binario, ya que el PATH contiene la ruta donde se encuentra el script malicioso. Ahora, el "whoami" que va a ejecutar será el que hemos definido anteriormente y nos dará una shell como usuario root.

![[Pasted image 20240226195332.png]]

Perfecto, así es como nos podemos aprovechar del PATH para elevar privilegios.