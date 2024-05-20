

Con el siguiente comando podemos añadir a un usuario a un grupo en especifio 

```bash
usermod -a -G grupo usuario
```

Y con este comando podemos quitar a un usuario de un grupo

```bash
gpasswd -d usuario grupo
```


Si al hacer un id en nuestro equipo es probable que nos salga que pertenecemos al grupo de sudo

![[Pasted image 20240227203258.png]]

Si comprometes un usuario y ves que pertenece a este grupo y dispones de su contraseña entonces estas autorizado a ejecutar tareas privilegiadas por ejemplo si hacemos el siguiente comando

![[Pasted image 20240227203522.png]]

Y como se puede ver se estan ejecutando los comandos como usuario root


Aislado al grupo sudo hay otros con los que se puede escalar privielgios como por ejemplo el grupo docker


Un ejemplo de esto es por ejemplo tenemos una maquina donde el usuario pertenece a usuario docker 


![[Pasted image 20240227213147.png]]

Una forma potencial de elevar privilegios perteneciendo a este grupo es crear una imagen de ubuntu de la siguiente manera gracias a que estamos en el grupo docker ya que de lo contrario no podriamos utilizar docker

![[Pasted image 20240227213615.png]]

Entonces aprovechandonos de la imagen podriamos crear un contenedor y con el uso de monturas hacer que monte toda la raiz en el directorio mnt/root 

![[Pasted image 20240227213948.png]]

Ahora si accedemos al contendor y entramos al directorio donde montamos toda la raiz tendriamos total movilidad sobre recursos privilegiados

![[Pasted image 20240227214300.png]]

Por ejemplo podriamos ver el /etc/shadow

![[Pasted image 20240227214536.png]]

Ademas ya que estamos trabajando con una montura es irnos al directorio bin y a la bash otorgarle permisos suid 

![[Pasted image 20240227214816.png]]

Con esto conseguimos que ese permiso suid realmente se le otorge a la bash orignal del equipo entonces con esto podremos lanzrnos una bash como root 

![[Pasted image 20240227215000.png]]

Como vemos si un usuario pertenece al grupo de docker esto puede suponer un riesgo ya que con esto se puede escalar privilegios


Aparte de este grupo tambien existe el grupo adm el cual se encarga en leer los logs de sistema  a continuacion tenemos un ejemplo donde un usuario esta en este grupo

![[Pasted image 20240227230211.png]]

Entonces podemos enumerar todo lo que este en /var/log y su grupo es adm con el siguiente comando

```bash
find /var/log -group adm 2> /dev/null
```

Y buen al ejecutar este comando podemos ver todos los logs a los cuales tenemos acceso

![[Pasted image 20240227230825.png]]

Con esto a lo mejor es posible ver en algun log con informacion confidencial o ciertamente critica y ya de ahi poder elevar privilegios


Finalmente tendremos un ultimo ejemplo el cual tenemos una maquina en la cual estamos en el grupo lxd lxd es algo parecido a docker  por lo que podriamos hacer algo parecido a lo que hicimos con el grupo docker

![[Pasted image 20240227233215.png]]

Entonces sabiendo que somos parte del grupo lxd podemos usar este exploit el cual automatiza la escalada de privilegios entonces lo descargamos

![[Pasted image 20240227233907.png]]

Al ver el codigo del exploit nos pide que descarguemos el siguiente codigo en nuestra maquina de atacante

![[Pasted image 20240227234145.png]]

Con el archivo descargado en la maquina atacante este lo ejecutamos como root 


![[Pasted image 20240227235321.png]]

Al terminar de ejecutarse podemos ver que nos deja este archivo

![[Pasted image 20240227235528.png]]

Lo que tenemos que hacer es mover el archivo generado y el exploit descargado a la maquina victima y ejecutar de la siguiente manera el script 


![[Pasted image 20240227235826.png]]

Al ejeuctar esto podremos ver que no ha dado una consola como root en un contenedor 


![[Pasted image 20240228000308.png]]

Pero al igual que en docker podemos ver que hay un directorio donde estan todos los recursos del sistema y puedes moverte libremente 

![[Pasted image 20240228000611.png]]

Entonces como es una montura podemos irnos al directorio bin y asignarle un permiso suid a la bash

![[Pasted image 20240228000823.png]]

Y finalmente podemos salir del contendor y ejecutar una bash como root


![[Pasted image 20240228001031.png]]


Y listo asi es como nos podemos aprovechar de los grupos para escalar privilegios