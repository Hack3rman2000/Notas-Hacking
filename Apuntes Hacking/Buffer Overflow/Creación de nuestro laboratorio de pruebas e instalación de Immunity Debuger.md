
Para crear nuestro laboratorio para practicar buffer overflow  primero lo que tendremos que hacer es descargar el iso de windows 7 home premium de 32 bits lo podemos descargar en el siguiente enlace https://tech-latest.com/download-windows-7-iso/ para descaraglo nos vamos a la parte inferior de la pagina y damos click aqui


![[Pasted image 20240305123656.png]]


Al darle click nos manda a este otro sitio en donde le tendremos que dar click en el siguiente boton 


![[Pasted image 20240305123850.png]]

Entonces ahora nos mostrara algunos mirror links para descargar la iso de windows en este caso le damos click al primero que se nos recomienda y nos comenzara a descargar la iso  si este no funciona entonces probamos con los demas


![[Pasted image 20240305124151.png]]

Ya descargada la iso nos dirigimos a virtual box donde crearemos una nueva maquina e instalaremos windows 7 para esto primero damos click en la opcion de nueva 

![[Pasted image 20240305130938.png]]

Al darle click nos mostrara una ventana donde tendremos que indicar el nombre para la maquina virutal y la iso de windows 7 tambien es muy importante tener marcada la opcion de omitir la instalacion desatendida ya que con esto podemos hacer una instalacion personalizada ya aviendo configurado todo le damos click al bootn de de siguiente 


![[Pasted image 20240305131431.png]]

Ahora nos pedira la memoria ram y cuantos cpus le queremos asignar a nuestra maquina en este caso le asignamos 4 gb de ram y 2 cpus y le damos a siguiente

![[Pasted image 20240305131657.png]]

Nos pide el tamaño del disco virtual entonces le indicamos que necesitaremos 60 gb de almacenamiento y queremos crear un disco virtual ahora hecho esto le damos a siguiente

![[Pasted image 20240305132121.png]]

Finalmente nos mostrara un resumen de la configuracion si todo esta correcto le podemos dar click al boton de terminar


![[Pasted image 20240305132221.png]]




Y listo como podemos ver ya tendriamos creada la maquina 


![[Pasted image 20240305132512.png]]

Como ultimo paso seleccionamos la maquina windows y damos click en la opcion de configuracion 


![[Pasted image 20240305134950.png]]

Ahora en la ventana seleccionamos el apartado de red y donde dice conectado a seleccionamos adaptador puente luego indicamos nuestra interfaz de red y finalmente damos click al boton de aceptar


![[Pasted image 20240305134746.png]]

Ahora si ya con la maquina configurada lo que falta es instalar windows 7 para esto primero iniciamos la maquina seleccionando la maquina a iniciar y dandole click al boton de iniciar


![[Pasted image 20240305135045.png]]

Ya iniciada la maquina podemos ver que nos pide que seleccionemos idioma pero en este caso no nos deja escoger otro idioma que no sea ingles asi que solo le damos a siguiente

![[Pasted image 20240305135354.png]]


Ahora nos muestra esto aqui solo le damos click a instalar 


![[Pasted image 20240305135638.png]]


Luego nos mostrara esto aqui solo aceptamos los terminos y condiciones y le damos a siguiente 


![[Pasted image 20240305135926.png]]

Ahora nos pregunta en que disco queremos intalar el windows en este caso seleccionamos el unico disco y damos siguiente 


![[Pasted image 20240305140123.png]]


Y listo ahora solo esperamos a que se termine de instalarse


![[Pasted image 20240305141138.png]]


Cuando se termine este paso nos pedira un nombre de ususuario asi que lo indicamos y le damos click al boton de siguiente

![[Pasted image 20240305142436.png]]


Ahora nos pedira una contraseña para el usuario la indicamos y damos click a siguiente 


![[Pasted image 20240305142627.png]]


Nos pedira que le demos una clave para activar windows aqui solo damos click al boton de skip sin proporcionar nada


![[Pasted image 20240305142718.png]]

Aqui solo selecciomanos la siguiente opcion 

![[Pasted image 20240305142844.png]]


En esta ventana indicamos la zona horaria y damos siguiente 


![[Pasted image 20240305143005.png]]

Nos pedira el tipo de conexion aqui solo selccionamos la siguiente opcion 

![[Pasted image 20240305143249.png]]



Y listo ya tendremos windows 7 home premium de 32 bits instalado


![[Pasted image 20240305144715.png]]

Si queremos instalar los guest additions nos vamos al siguiente link https://download.virtualbox.org/virtualbox/7.0.14/ ya estando dentro damos click en el siguiente enlace y comenzara la descarga de los guest adittions 


![[Pasted image 20240305154718.png]]


Ahora en virtual box entramos a la configuracion de la maquina y nos vamos al apartado de almacenamiento en donde le daremos click al siguiente boton 

![[Pasted image 20240305155132.png]]

Esto desplegara una ventana en donde seleccionaremos la iso de los guest additions 


![[Pasted image 20240305155410.png]]

Ahora solo de la damos a aceptar y esto guardara todas las configuraciones realizadas


![[Pasted image 20240305155514.png]]

Si iniciamos de nuevo la maquina y nos dirigimos al explorador de archivo podemos ver que ya esta montada la iso de las guest additions entonces para empezar la instalacion damos doble click en esta



![[Pasted image 20240305191406.png]]


Luego nos mostratra esto le damos doble  click al siguiente ejecutable  y 

![[Pasted image 20240305192042.png]]


He indicamos que si lo queremos ejecutar


![[Pasted image 20240305192140.png]]


Ahora no mostrara esto aqui solo damos click en el boton de siguiente

![[Pasted image 20240305192245.png]]

Indicamos en que ruta lo deseamos instalar y le damos al boton de siguiente


![[Pasted image 20240305192330.png]]


Esto lo dejamos por defecto y solo de damos al boton de instalar


![[Pasted image 20240305192449.png]]


Nos mostrara esto aqui solo le damos click a instalar


![[Pasted image 20240305192557.png]]


Seguidamente aparecera esto de igual manera le damos a instalar

![[Pasted image 20240305192657.png]]


Apracera esto que nos pide que reiniciemos la maquina aqui solo seleccionamos reinciar ahora y le damos al boton de finalizar esto reinciara el equipo por lo tanto tenemso que esperar que encienda de nuevo




![[Pasted image 20240305192838.png]]

Al momento de reinciar el sistema vemos que ahora la resolucion ha cambiado ya que los guest additions instalaron los controladres


![[Pasted image 20240305193934.png]]

Ahora podemos trabajar de una mejor manera


Como paso siguiente descargamos nuestro navegador de confianza en este caso google chrome y ya con este instalado nos vamos a descargar Immunity Debugger el cual es un depurador de 32 bits para Windows lo podemos descargar del siguiente link https://immunityinc.com/products/debugger/ ya en la pagina damos click en el siguiente enlace

![[Pasted image 20240305201313.png]]

Nos mandara a una pagina donde nos pide llenar un formulario para poder descargar el ejecutable entoces llenamos todo los datos correspondientes y damos click en el boton de descargar


![[Pasted image 20240305201440.png]]

Ya descargado el ejecutable le damos doble click nos pedira que si en verdad lo queremos ejecutar y le damos que si

![[Pasted image 20240305203148.png]]


Luego nos preguntara que si queremos instalar python 2.7 y le indicamos que si

![[Pasted image 20240305203242.png]]

Nos mostrara esta venta aqui solo aceptamos los terminos y condiciones y le damos a siguiente

![[Pasted image 20240305203330.png]]


Ahora nos pedira que le indiquemos la ruta de instalacion la dejamos por defecto y le damos click al boton de instalar

![[Pasted image 20240305203740.png]]

Cuando este se acabe de instalar procedera el proceso de instalacion de python nos mostrara esta ventana donde seleccionaremos la siguiente opcion y le daremos click al boton de siguiente +


![[Pasted image 20240305204006.png]]

Ahora nos mostrara esto lo dejamos asi y le damos a siguiente

![[Pasted image 20240305204305.png]]

En la siguiente ventana que nos muestre tambien lo dejamos por defecto y le damos a siguiente en este momento comenzara la instalacion de python 2.7

![[Pasted image 20240305204354.png]]


Y listo ya finalizada la instalacion le damos click a finalizar



![[Pasted image 20240305204620.png]]


El siguiente paso es desactivar el DEP el cual es una tecnología de seguridad integrada en Windows que ayuda a prevenir la ejecución de código malicioso en áreas de memoria no autorizada para desactivarlo primero tendremos que ejecutar el cmd como administrador para despues ejecutar el siguiente comando 


![[Pasted image 20240305210715.png]]

Ya ejecutado reinciamos el sistema y ya deberiamos tener el DEP desactivado


Lo siguiente que vamos a hacer es instalar mona para eso copiamos todo este codigo https://raw.githubusercontent.com/corelan/mona/master/mona.py para despues crear una un archivo llamado mona.py y ahi pegamos todo el codigo

![[Pasted image 20240305212853.png]]

Ahora abrimos una nueva consola y le cambiamos el nombre al archivo para que asi sea reconocido por python 

![[Pasted image 20240305213200.png]]

Entonces ya con el nombre modificado movemos el archivo de python a la siguiente ruta C:\Program Files\Immunity Inc\Immunity Debugger\PyCommands y ahora si abrimos el immunity debugger y abrimos mona todo funciona a la perfeccion 

![[Pasted image 20240305213823.png]]

Otra cosa que tenemos que hacer es desactivar el firewall para eso presionamos windows y buscamos windows firewall y seleccionamos el siguiente 


![[Pasted image 20240305214640.png]]

Nos abrira el panel de control ahora damos click en el siguiente apartado

![[Pasted image 20240305214748.png]]


Ahora marcamos lo siguiente y le damos al boton de ok 


![[Pasted image 20240305215206.png]]

Ahora el firewall esta desctivado y ya podriamos ver los puertos abiertos de la maquina

Finalmente vamos a descargar SLmail para eso accedemos al siguiente link https://slmail.software.informer.com/download/ ya en el sitio bajamos un poco y le damos click al siguiente boton y comoenzara la descarga


![[Pasted image 20240305215901.png]]


Ya descargado damos doble click y le damos siguiente a todas las ventanas que aparezcan ya que no hay que proporcionar nada de datos

![[Pasted image 20240305220331.png]]

Este servicio te va a abrir el puerto 25 y el 110 este siendo el vulnerable 

Llegados a este punto nos dira que tenemos que reinicar la maquina asi que le decimos que la queremos reinicar ahora y le damos click al boton de finalizar la maquina se reinciara

![[Pasted image 20240305220850.png]]

Cuando la maquina se encienda nuevamente podremos ver que  ya tenemos el servicio vulnerable corriendo 

![[Pasted image 20240305221444.png]]



