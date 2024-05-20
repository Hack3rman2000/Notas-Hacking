___

- Tags: 

____

# Introducción

El ataque de **Padding Oracle** es una técnica criptográfica que explota las debilidades en el manejo de errores relacionados con el padding en esquemas de cifrado por bloques. Este tipo de ataque puede comprometer la seguridad de sistemas que utilizan cifrado simétrico. A continuación, se explorarán los conceptos fundamentales y se analizará cómo se puede llevar a cabo este ataque, junto con el uso de la herramienta [PadBuster](https://github.com/AonCyberLabs/PadBuster).

___
# Proceso de Cifrado

En el cifrado por bloques, el mensaje original se divide en bloques antes de ser cifrado. Se utiliza padding para rellenar el último bloque si es necesario. Un esquema común de padding es el PKCS#7, que agrega bytes con el valor del número de bytes de padding al final del bloque.

### Ejemplo de Cifrado (AES en modo CBC con PKCS#7 Padding):

```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad

key = b'clave_secreta'
iv = b'vector_inicial'
plaintext = b'Mensaje secreto'

cipher = AES.new(key, AES.MODE_CBC, iv)
ciphertext = cipher.encrypt(pad(plaintext, AES.block_size))
```

___
# Proceso de Descifrado

Durante el descifrado, se realiza la operación inversa, y se elimina el padding después de descifrar.

### Ejemplo de Descifrado:

```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad

cipher = AES.new(key, AES.MODE_CBC, iv)
decrypted_data = unpad(cipher.decrypt(ciphertext), AES.block_size)
```

____
# Vulnerabilidad y Ataque

El ataque de Padding Oracle explota la información revelada por el servidor al procesar un mensaje cifrado con padding incorrecto. Un atacante puede iterativamente cambiar los bytes de padding hasta que se descifre el mensaje completo.

___
# Uso de PadBuster

```bash
padbuster ip texto_cifrado multipo_de_ocho --cookies 'nombre-cookie=valor'
```

En este caso, se proporciona la dirección [[Dirección IP|IP]] del objetivo (`ip`), el texto cifrado a analizar (`texto_cifrado`), y se especifica que el tamaño del bloque es un múltiplo de ocho (`multipo_de_ocho`). También se incluye la opción `--cookies` para proporcionar información de cookies al servidor.

```bash
padbuster ip texto_cifrado multiplo_de_ocho --cookies 'nombre=valor' --plaintext 'user=admin'
```

Se proporciona la dirección [[Dirección IP|IP]] del objetivo (`ip`), el texto cifrado a analizar (`texto_cifrado`), y se especifica que el tamaño del bloque es un múltiplo de ocho (`multiplo_de_ocho`). La opción `--cookies` se utiliza para enviar información de cookies al servidor, y `--plaintext` se utiliza para especificar un texto plano a inyectar durante el ataque, en este caso, la cadena 'user=admin'. 

___
# Usando Burpsuite

La técnica de **Padding Oracle** también se puede implementar con la ayuda de [[BurpSuite]] y el [[Payload Staged|payload]] de **bit flipping**. Además, al crear un usuario, es crucial que sea extremadamente similar al usuario con el que deseamos iniciar sesión. En este caso, el usuario creado para probar será "cdmin", muy parecido a "admin", y mediante este método, intentaremos capturar la cookie de administrador, como se describe a continuación.


![[Pasted image 20240122194238.png]]

Primero, es necesario interceptar la consulta con la ayuda de [[BurpSuite]] y luego enviarla al módulo **Intruder**.

![[Pasted image 20240122194409.png]]

Dentro de **Intruder**, seleccionamos el tipo de ataque; en este caso, será de tipo **Sniper**. Posteriormente, elegimos la cookie en la que se aplicará el [[Payload Staged|payload]] y la añadimos.

![[Pasted image 20240122194558.png]]

A continuación, seleccionamos el tipo de [[Payload Staged|payload]]; en este caso, el **bitflipper**, y marcamos la opción de "literal value".

![[Pasted image 20240122194629.png]]

Es importante desmarcar la opción de codificar los caracteres especiales.

![[Pasted image 20240122195032.png]]

Finalmente, iniciamos el ataque y esperamos obtener la respuesta esperada. En este caso, buscamos identificar la cookie con la cual podemos iniciar sesión como administrador.