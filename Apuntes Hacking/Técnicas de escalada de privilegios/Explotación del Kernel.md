

Tenemos como ejemplo una maquina que al momento de enumerarla nos muestra que es un ubuntu 12.04 y tiene una version de kernel 3.2.0-23 esto es bastante antiguo esto es perfecto para tratar de explotar el kernel y elevar nuestro privilegios 

![[Pasted image 20240227162147.png]]

Si con ayuda de searchsploit buscamos algun exploit para esta version de kernel vemos demasiados exploits

![[Pasted image 20240227162535.png]]

Pero a nosotros solo nos interesa el llamado dirty cow que se aprovecha de una race condition para sobrescribir el /etc/passwd

![[Pasted image 20240227163219.png]]

Ahora lo que hacmeos es descargar el exploit en nuestra maquina para luego montar un servidor en python y poderlo descargar en la maquina victima 

![[Pasted image 20240227164734.png]]

![[Pasted image 20240227164931.png]]

si por alguna razon la maquina victima no puede compilar el binario lo deberas compilar en tu maquina y luego compartirlo con la maquina victima  pero en este caso no sera asi y lo compilaremo directamente desde la maquina victima este se compilara como lo dice el propio archivo de c de la siguiente manera 

![[Pasted image 20240227170906.png]]

![[Pasted image 20240227171453.png]]

Perfecto ahora tendriamos nuestro ejecutable el cual despues de que explote el kernel va a modifcar el /etc/passwd creara un nuevo usuario llamado firefart nos pediara una contraseña para este y eliminara completamente al usuario root 


![[Pasted image 20240227172820.png]]

Entonces si leemos el /etc/passwd podemos ver que este ha sido modificado y se ha añadido el usuario firefart con la contraseña que hemos introducido

![[Pasted image 20240227194708.png]]

Ahora si migramos al usuario firefart  y ponemos la contraseña antes definida nos dejara entrar  y si ejecutamos el comando id  vemos que en si que somos root 

![[Pasted image 20240227195333.png]]

Tambien si por alguna razon quisieras volver a la normaldiad el exploit te hace un backup o directamente podrias utilizar la copia por defecto que es /etc/passwd-

![[Pasted image 20240227200340.png]]

Ademas cabe señalar de esta no es la unica forma ya que hay miles de exploits lo unico que tiens que hacer cuando tienes acceso a una maquina es poner atencion buscar si no hay un exploit para una version de kernel determinada lo puedes hacer con la herramienta de searchsploit o directamente buscando la version del sistema en google

Tambien si por alguna razon te cuesta encontrar un exploit en concreto existen herramientas como https://github.com/The-Z-Labs/linux-exploit-suggester el cual te dice de forma automatica que exploits puedes lanzar a nivel de kernel para elevar tu privilegio donde su modo se uso seria el siguiente

```bash 
./les.sh
```

Y listo al ejcutar esto te tendrian que aparcer exploits que se podrian usar para abusar del kernel junto a su respectivo enlace 

![[Pasted image 20240227201903.png]]


La conclusion aqui es que mientras mas antigua la version del sistema habra mas exploits para explotar el kernel y elevar tu privilegio


