


Para crear un servicio y un temporizador que trabajen en conjunto, puedes seguir los siguientes pasos:


Claro, aquí tienes un ejemplo que combina tanto el servicio como el temporizador en Systemd:

1. **Crear un archivo de unidad de servicio**:

   Creamos un archivo llamado `mi_servicio.service` en el directorio `/etc/systemd/system/`.

   ```
   sudo nvim /etc/systemd/system/mi_servicio.service
   ```

   Contenido del archivo `mi_servicio.service`:

   ```plaintext
   [Unit]
   Description=Mi servicio personalizado
   
   [Service]
   Type=oneshot
   ExecStart=ruta al programa
   ```

2. **Crear un archivo de unidad de temporizador**:

   Creamos un archivo llamado `mi_servicio.timer` en el mismo directorio `/etc/systemd/system/`.

   ```
   sudo nvim /etc/systemd/system/mi_servicio.timer
   ```

   Contenido del archivo `mi_servicio.timer`:

   ```plaintext
   [Unit]
   Description=Temporizador para mi servicio

   [Timer]
   OnUnitActiveSec=intervalalo de tiempo
   Unit=mi_servicio.service

   [Install]
   WantedBy=timers.target
   ```

3. **Recargar Systemd**:

   ```
   sudo systemctl daemon-reload
   ```


4. **Gestionar el servicio y el temporizador**:

   - Iniciar el servicio:

     ```
     sudo systemctl start mi_servicio
     ```

   - Habilitar el temporizador y el servicio para que se inicien automáticamente en el arranque:

     ```
     sudo systemctl enable mi_servicio.timer
     ```

   - Iniciar el temporizador:

     ```
     sudo systemctl start mi_servicio.timer
     ```

Con estos pasos, el servicio se iniciará automáticamente una vez y luego será controlado por el temporizador según los intervalos especificados.

Tenemos un ejemplo en el cual hemos obtenido una webshell 

![[Pasted image 20240228213846.png]]

Al ingrsar el siguiente comando podemos ver que hay un servicio http en el puerto 8000


![[Pasted image 20240228214443.png]]

Tambien podemos ver esto al verficar los procsoso que se encuentra corriendo 

![[Pasted image 20240228214410.png]]


Entonces como el usuario que esta ejecutando el servicio es el usuario root podriamos aprovecharnos de un recurso que nos da una webshell que se encuentra en un directorio del servicio asi que con curl nos podemos pasarle un parametro a este archivo de ese servicio y ejecutar comandos como root

![[Pasted image 20240228215910.png]]

Y listo asi nos hemos aprovechado de un servicio interno que se estaba ejecutando de forma privilegiada y asi elevar privilegios



Otro ejemplo es que tenemos una maquina donde al listar los temporizadores que gestionan los servicios podemos ver que existe un temporizador que se ejecuta un servicio cada 30 segundos

![[2024-02-29_15-23.png]]

Al utilizar pspy para ver los procesos que se estan ejecutando podemos ver que ese servicio  esta ejecutando lo siguiente


![[2024-02-29_15-29.png]]


Tambien lo que podemos hacer para ver lo que esta haciendo el servicio interno es buscar por el servicio que se muestra al listar los temporizadores

![[2024-02-29_15-30.png]]

Entonces lo buscamos y vemos directamente el contendio de este para ver lo que esta ejecutando al parecer solo esta actualizando el sistema

![[2024-02-29_15-33.png]]

Al investigar un pcoo mas vemos tambien que tenemos permitido escribir en el directorio apt.conf.d 

![[2024-02-29_16-22.png]]


por lo tanto nos podriamos aprovechar de esto para indicar un comando que se ejecute antes de hacer la actualizacion o despues de esta a esto se le llama preinvoke o postinovke entonces para esto primero tendremos que hacer un archivo con el siguiente contenido el cual hace que antes de que se actualize el sistema se le otorgara el permiso suid a la bash

![[2024-02-29_16-36.png]]


Y listo al crear el archivo y esperar 30 segundos el permiso suid se le ha puesto a la bash y ahora podemos ejecutar una bash como root

![[2024-02-29_16-41.png]]
