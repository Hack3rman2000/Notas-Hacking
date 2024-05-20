
```bash
find / -writable 2>/dev/null
```

Con este comando podemos encontrar todos los archivos del sistema que tienen permiso de escritura 

Tenemos un ejemplo donde al enumerar la maquina nos podemos dar cuenta de que el /etc/passwd tiene el permiso de escritura para otros

![[Pasted image 20240226223003.png]]

Entonces lo que podemos hacer es hardcodear una contraseña  haseada directamente en el archivo /etc/passwd en cualquier usuario y asi si tratamos de logernos no consulte al /etc/shadow ya que la contraseña harcodeada en el /etc/passwd tendira mas prioridad asi que para lograr esto hacemos lo siguiente

![[Pasted image 20240226224241.png]]

Este comando nos pedira ingresar una contraseña la cual posteriormente nos las imprimira ya cifrada en este caso la contraseña cifrada es hola123

Ahora podemos entrar al archivo de /etc/passwd y sustituir la x de cualquier usuario por la contraseña cifrada en este caso modifcaremos la contraseña del usuario root y lo guardamos 

![[Pasted image 20240226225223.png]]

Ahora al querer entrar como root y ingresar la contraseña que añadimos al /etc/passwd podemos acceder correctamente 

![[Pasted image 20240226230030.png]]

Otro ejemplo es que al enumerar la maquina podemos ver que hay una tarea cron la cual al parecer esta ejecutando un archivo en el directorio de prueba 


![[Pasted image 20240226232725.png]]

Viendo el archivo no tiene ningun permiso con el cual nos puedamos aprovechar y ademas el codigo parecer que no tiene nada malo 

![[Pasted image 20240226233034.png]]

Sin embargo al estrar este archivo en nuestro directorio personal podemos alterar archivos que esten dentro de nuestro directorio personal por lo tanto podemos borrar el archivo crear un nuevo archivo con el mismo nombre que tenga permisos de ejecucion y este lo que haga es otorgarle permiso suid a la bash

![[Pasted image 20240226233657.png]]

![[Pasted image 20240226233742.png]]


Y listo ahora solo esperamos a que se ejecute la tarea cron y ya  podriamos ejecutar una bash como root

![[Pasted image 20240226234156.png]]


Para automatizar toda la enumeracion existen herramientas como https://github.com/diego-treitos/linux-smart-enumeration o https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS


En el caso de la herramienta https://github.com/diego-treitos/linux-smart-enumeration podemos enumerar toda la informacion interesante de la siguiente manera

```bash
./lse.sh -l 1
```




# Abuso de Permisos Incorrectamente Implementados

En sistemas Linux, los archivos y directorios tienen permisos que se utilizan para controlar el acceso a ellos. Los permisos se dividen en tres categorías: propietario, grupo y otros. Cada categoría puede tener permisos de lectura, escritura y ejecución. Los permisos de un archivo pueden ser modificados por el propietario o por el superusuario del sistema.

El abuso de permisos incorrectamente implementados ocurre cuando los permisos de un archivo crítico son configurados incorrectamente, permitiendo a un usuario no autorizado acceder o modificar el archivo. Esto puede permitir a un atacante leer información confidencial, modificar archivos importantes, ejecutar comandos maliciosos o incluso obtener acceso de superusuario al sistema.


Utilizando el comando `find / -writable 2>/dev/null`, podemos encontrar todos los archivos del sistema que tienen permisos de escritura.

Tenemos un ejemplo donde, al enumerar la máquina, nos damos cuenta de que `/etc/passwd` tiene permisos de escritura para otros.

![[Pasted image 20240226223003.png]]

Entonces, lo que podemos hacer es hardcodear una contraseña hasheada directamente en el archivo `/etc/passwd` en cualquier usuario, y así, si tratamos de logearnos, no consultará al `/etc/shadow`, ya que la contraseña hardcodeada en el `/etc/passwd` tendría más prioridad. Para lograr esto, ejecutamos lo siguiente:

![[Pasted image 20240226224241.png]]

Este comando nos pedirá ingresar una contraseña, la cual posteriormente nos la imprimirá ya cifrada. En este caso, la contraseña cifrada es "hola123".

Ahora podemos entrar al archivo `/etc/passwd`, sustituir la "x" de cualquier usuario por la contraseña cifrada. En este caso, modificaremos la contraseña del usuario "root" y guardamos los cambios.

![[Pasted image 20240226225223.png]]

Ahora, al querer entrar como "root" e ingresar la contraseña que añadimos al `/etc/passwd`, podemos acceder correctamente.

![[Pasted image 20240226230030.png]]

Otro ejemplo es que al enumerar la máquina, podemos ver que hay una tarea cron que aparentemente está ejecutando un archivo en el directorio de prueba.

![[Pasted image 20240226232725.png]]

Al ver el archivo, no tiene ningún permiso con el cual nos podamos aprovechar, y además, el código parece que no tiene nada malo.

![[Pasted image 20240226233034.png]]

Sin embargo, al extraer este archivo en nuestro directorio personal, podemos alterar archivos que estén dentro de nuestro directorio personal. Por lo tanto, podemos borrar el archivo, crear un nuevo archivo con el mismo nombre que tenga permisos de ejecución y este lo que haga es otorgarle permiso SUID a la bash.

![[Pasted image 20240226233657.png]]
![[Pasted image 20240226233742.png]]

Y listo, ahora solo esperamos a que se ejecute la tarea cron y ya podremos ejecutar una bash como root.

![[Pasted image 20240226234156.png]]

Para automatizar toda la enumeración existen herramientas como [Linux Smart Enumeration](https://github.com/diego-treitos/linux-smart-enumeration) o [PEASS-ng](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS).

En el caso de la herramienta [Linux Smart Enumeration](https://github.com/diego-treitos/linux-smart-enumeration), podemos enumerar toda la información interesante de la siguiente manera:

```bash
./lse.sh -l 1
```



