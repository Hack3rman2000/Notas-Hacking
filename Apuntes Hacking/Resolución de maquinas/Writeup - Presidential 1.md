
## Información General

- **Nombre de la máquina**: Presidential 1
- **Fecha de realización**: [[2024-04-19]]
- **Nivel de dificultad**: Medio
- **Sistema Operativo**: Linux
- **Plataforma**: Vulnhub
- **Enlace de la máquina**: [Enlace a la plataforma donde se encuentra la máquina]

## Resumen
[Un breve resumen de la máquina y las técnicas utilizadas para comprometerla.]

## Enumeración y Reconocimiento


Comenzamos enumerando los puertos de la maquina


![[Pasted image 20240419194747.png]]


Podemos observar que hay dos puertos abiertos: uno es el puerto 80, que utiliza el servicio HTTP, y el otro es el puerto 2082, donde está ejecutándose un servicio algo inusual. Si ahora realizamos un escaneo para enumerar las versiones, podemos ver lo siguiente:


![[Pasted image 20240419201807.png]]


Observamos que el puerto desconocido corresponde al servicio SSH. Bueno, ahora que ya lo sabemos, procedamos a investigar el puerto 80. Al entrar al navegador, podemos ver que hay un sitio web sobre elecciones, el cual parece bastante normal.

![[Pasted image 20240419203732.png]]


Lo que sí podemos ver es un correo electrónico que tiene como dominio "votenow.local". Por lo tanto, podemos suponer que ese es el dominio de la página y añadirlo a nuestro archivo "/etc/hosts"

![[Pasted image 20240419205249.png]]


Con esto configurado, podemos comenzar con la enumeración. Al hacerla en los archivos, notamos uno que parece interesante, el cual corresponde al nombre "config.php".


![[Pasted image 20240419212509.png]]



Al acceder a este archivo desde el navegador no logramos ver nada 



![[Pasted image 20240419212606.png]]


Entonces vamos a intentar probar con más extensiones para ver si hay algún respaldo o algo parecido. En este caso, hemos encontrado un archivo llamado config.php.bak.


![[Pasted image 20240419214906.png]]


Accedemos a este recurso en el navegador, pero no muestra nada.


![[Pasted image 20240419215028.png]]


Pero al ver su código fuente, podemos observar unas credenciales que parecen corresponder a una base de datos.


![[Pasted image 20240419215126.png]]

Lo primero que se me ocurre es intentar acceder al servicio de SSH con las credenciales, pero no parece funcionar.

![[Pasted image 20240419215329.png]]


Entonces debe de haber alguna otra manera de utilizar estas credenciales, así que procedí a enumerar los subdominios. Logré encontrar uno bastante interesante.


![[Pasted image 20240419215842.png]]


Por lo tanto, ahora lo incluimos en el archivo /etc/hosts y luego accedemos a él desde el navegador. Al hacerlo, podemos ver que PHPMyAdmin está en ejecución y nos solicita credenciales.


![[Pasted image 20240419220307.png]]

Probamos las credenciales que encontramos anteriormente y logramos ingresar exitosamente.


![[Pasted image 20240419220532.png]]

Investigando las bases de datos, encontramos una denominada "votebox" que almacena un usuario y una contraseña encriptada.


![[Pasted image 20240420213321.png]]


La contraseña parece estar encriptada en Blowfish, por lo que lo que podemos hacer es usar John the Ripper. Como podemos ver, nos da una contraseña.

![[Pasted image 20240420222938.png]]

De igual manera, si intentamos acceder con las credenciales al servicio SSH, podemos ver que no nos deja entrar.

![[Pasted image 20240421130057.png]]


Por lo tanto, guardamos las credenciales, ya que posiblemente se puedan utilizar más adelante. Al parecer, el phpMyAdmin está en la versión 4.8.1. Al ingresar esto en Searchsploit, podemos ver que hay varios exploits disponibles, incluidos dos de LFI.


![[Pasted image 20240421130524.png]]


Podemos observar que uno de estos exploits consiste en realizar primero una consulta SQL que contiene código PHP. Luego, accedemos a una URL en la que proporcionamos la sesión actual.


![[Pasted image 20240421131303.png]]


Por lo tanto, ahora utilizaremos lo visto anteriormente. Lo primero que haremos es ejecutar una consulta SQL, la cual provocará una reverse shell.


![[Pasted image 20240421132151.png]]


Después de realizar la consulta, abrimos el puerto especificado en nuestra máquina atacante.


![[Pasted image 20240421132716.png]]


Ahora debemos localizar nuestra sesión; en este caso, es la siguiente:


![[Pasted image 20240421154159.png]]


  
Finalmente, ingresamos a la URL indicada por el exploit, modificando únicamente la parte final donde especificamos nuestra sesión.

![[Pasted image 20240421154259.png]]


  
Cuando presionamos "Enter", observamos que se ha establecido la conexión de la reverse shell con éxito.


![[Pasted image 20240421154713.png]]



Enumerando los usuarios, podemos ver que hay un usuario llamado "admin". Al intentar ingresar con la contraseña que encontramos, observamos que nos permite acceder.


![[Pasted image 20240421171014.png]]


Enumerando el sistema, podemos ver que hay un binario que tiene una capacidad que le otorga el permiso de leer cualquier archivo. El binario parece ser exactamente igual que el binario 'tar'.

![[Pasted image 20240421173944.png]]



Lo que se me ocurre hacer es comprimir el directorio `.ssh` y verificar si contiene una clave privada, para luego acceder como root. Así que comprimo la carpeta `.ssh` de la siguiente manera:

![[Pasted image 20240421190554.png]]


Una vez comprimida, procedo a descomprimir y al acceder a la carpeta `.ssh`, podemos verificar que, efectivamente, hay una clave pública.

![[Pasted image 20240421191114.png]]




Así que la utilizamos para conectarnos al SSH como root. Listo, como vemos, hemos elevado correctamente los privilegios y ahora somos superusuarios.

![[Pasted image 20240421191137.png]]



