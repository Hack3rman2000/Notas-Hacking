## Información General

- **Nombre de la máquina**: Infovore 1
- **Fecha de realización**: [[2024-04-19]]
- **Nivel de dificultad**: Medio
- **Sistema Operativo**: Linux
- **Plataforma**: Vulnhub
- **Enlace de la máquina**: [Enlace a la plataforma donde se encuentra la máquina]

## Resumen
[Un breve resumen de la máquina y las técnicas utilizadas para comprometerla.]

## Enumeración y Reconocimiento


Iniciamos enumerando los puertos de la máquina.

![[Pasted image 20240423203545.png]]


  
Vemos que la máquina solo tiene abierto el puerto 80 correspondiente al servicio HTTP. Al acceder desde el navegador, podemos ver una página muy básica, pero con un título algo extraño que podría ser una pista sugiriendo un LFI.


![[Pasted image 20240423204939.png]]


Al hacer la enumeración de archivos, podemos ver que encontramos algunos archivos con la extensión PHP, uno llamado index y otro llamado info.


![[Pasted image 20240423210149.png]]


El archivo 'index.php' es la página de inicio que hemos visto anteriormente. Como observamos, es algo extraño que tenga el título 'include me', por lo que me propuse tratar de encontrar algún parámetro en el cual pudiéramos colocar la ruta de un archivo y desde ahí verlo, lo que podría provocar un LFI. Al momento de enumerar parámetros, me encontré con uno llamado 'filename'.


![[Pasted image 20240424145304.png]]



Al probar varias rutas de archivos comunes para pasar de un LFI a un RCE]}, ninguna parece funcionar, excepto con /etc/passwd. Sin embargo, no se encuentra nada de interés, por lo que tendremos que encontrar otra manera de lograr una RCE.


![[Pasted image 20240424160141.png]]


El otro archivo que encontramos fue info.php, al ingresar a éste, vemos la página que genera la función `phpinfo()`.

![[Pasted image 20240423210932.png]]

Al analizar esta página, podemos ver algo muy interesante: tiene la configuración `file_uploads=on`.

![[Pasted image 20240424201413.png]]


Podemos aprovecharnos de esto ya que teniendo esta configuración activa, además de un LFI, podremos usar un script que nos permitirá derivarlo a un RCE. Para lograrlo, necesitaremos descargar este exploit: [https://www.insomniasec.com/downloads/publications/phpinfolfi.py](https://www.insomniasec.com/downloads/publications/phpinfolfi.py). Una vez descargado, tendremos que modificar varias cosas, incluido el comando que queremos ejecutar. En este caso, el típico oneliner para establecer una reverse shell.


![[Pasted image 20240424202558.png]]


También modificaremos el archivo donde se ejecuta la función phpinfo.


![[Pasted image 20240424202846.png]]


Además, modificaremos el archivo donde se indica el parámetro y el propio parámetro también.

![[Pasted image 20240424202819.png]]



Finalmente, ejecutaremos este comando para cambiar algunos caracteres que causan conflictos.

```bash
sed -i 's/\[tmp_name\] \=>/\[tmp_name\] =\&gt/g' phpinfolfi.py
```

Ya con esto adaptado, lo que tendremos que hacer es ponernos a la escucha desde la máquina atacante con netcat.


![[Pasted image 20240424203548.png]]

  
Ahora lo que tenemos que hacer es ejecutar el exploit de la siguiente manera, colocando la dirección IP y el puerto de la máquina víctima. Es muy importante ejecutarlo con Python 2.7, como se muestra a continuación:


![[Pasted image 20240424204032.png]]


  
Al ejecutarlo, podemos observar que nos proporciona una reverse shell en nuestra máquina atacante.


![[Pasted image 20240424204610.png]]



Entonces, teniendo acceso a la máquina, comenzamos con la enumeración. Sin embargo, tras buscar exhaustivamente, no encontramos nada para intentar elevar privilegios. Por lo tanto, tuvimos que recurrir a la herramienta Linpeas. Al ejecutarla, encontramos varias cosas interesantes. La primera de ellas es que nos encontramos en un contenedor.


![[Pasted image 20240424211510.png]]


La otra es que encontramos un archivo comprimido en la raíz, algo interesante.


![[Pasted image 20240424211759.png]]

  
Al copiar este archivo comprimido a nuestro directorio temporal y descomprimirlo, notamos que contiene una clave privada de SSH que está cifrada, y al parecer pertenece al usuario root del contenedor.


![[Pasted image 20240424212457.png]]

Por lo tanto, transferimos la clave a nuestra máquina atacante y utilizamos la herramienta ssh2john para generar un hash, como se muestra a continuación.


![[Pasted image 20240424213742.png]]

Seguidamente utilizamos John the Ripper con el uso del hash generado anteriormente, y como vemos, nos proporciona una contraseña correspondiente al usuario root.


![[Pasted image 20240424215105.png]]

Así que ingresamos la contraseña y, como podemos observar, hemos logrado acceder correctamente como el usuario root. Sin embargo, aún nos encontramos dentro del contenedor.

![[Pasted image 20240425174856.png]]

Investigando el directorio personal de root, en la carpeta .ssh, notamos la presencia de dos archivos de particular interés: una clave pública de SSH, la cual nos proporciona información sobre dónde conectarnos, aparentemente al usuario admin, y la dirección IP de la máquina principal.


![[Pasted image 20240425184558.png]]

El segundo archivo es una clave privada de SSH, la cual está encriptada.


![[Pasted image 20240425185905.png]]


  
Por lo tanto, copié esta clave privada a mi máquina y la convertí a un hash utilizando ssh2john de la siguiente manera:


![[Pasted image 20240425192919.png]]


Seguidamente, utilicé John the Ripper para intentar descifrar el hash, y como podemos observar, esto tuvo éxito al encontrar exactamente la misma contraseña que vimos anteriormente.


![[Pasted image 20240425193112.png]]

  
Entonces, si nos conectamos a la máquina principal por SSH como el usuario admin e ingresamos la clave correspondiente a la clave privada, observamos que nos logueamos exitosamente, por lo que hemos salido del contenedor.


![[Pasted image 20240425200926.png]]

Enumerando la máquina principal, podemos observar que el usuario "admin" pertenece al grupo "docker".


![[Pasted image 20240425205930.png]]


Por lo tanto, podemos aprovechar esto para crear un contenedor malicioso. Para ello, primero descargamos una imagen de la siguiente manera:


![[Pasted image 20240425211827.png]]


  
Después creamos el contenedor usando la imagen previamente descargada. Luego, jugando con monturas, hacemos una montura desde toda la raíz de la máquina principal en el contenedor de la siguiente manera:

![[Pasted image 20240425221740.png]]

Luego ejecutamos el contenedor y asignamos los permisos SUID a la bash. Dado que se están utilizando monturas, estos permisos también se aplicarán a la máquina principal, lo que nos permitirá ejecutar una bash como usuario root.

![[Pasted image 20240425223109.png]]

  
Por lo tanto, salimos del contenedor y al ejecutar una bash, podemos observar que hemos obtenido privilegios de root, lo que indica que hemos elevado nuestros privilegios.


![[Pasted image 20240425223246.png]]