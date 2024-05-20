# Introducción 
El bit setuid (SUID) es un permiso especial en sistemas Unix y Unix-like que se puede aplicar a archivos ejecutables. Cuando se establece el bit SUID en un archivo ejecutable, permite que cualquier usuario que lo ejecute temporalmente adquiera los privilegios del propietario del archivo en lugar de los suyos propios.



Podemos buscar binarios con el permiso SUID de la siguiente manera:

```bash
find / -perm -4000 2>/dev/null
```

El permiso SUID se añade con el siguiente comando:

```bash
chmod u+s ruta_ejecutable
```

En el sitio de [https://gtfobins.github.io/](https://gtfobins.github.io/) podemos ver los binarios con los que podemos aprovecharnos del SUID haciendo lo siguiente:

![[Pasted image 20240225114553.png]]

En este caso, vamos a ver dos ejemplos.

En el primer ejemplo, tenemos el binario de base64 con permiso SUID.

![[Pasted image 20240225120050.png]]

En principio, podríamos ejecutar `base64` como el usuario propietario, en este caso, root, siendo cualquier usuario. Esto es malo, ya que podríamos aprovecharnos de esto para leer archivos que solo el usuario root puede leer. Lo que podemos hacer es ejecutar el siguiente comando:

![[Pasted image 20240225121806.png]]

Aquí podemos ver que desde el usuario prueba podemos codificar el `/etc/shadow` y luego decodificarlo con `base64`. Esto se puede lograr gracias a que `base64` tiene el permiso SUID y el propietario es root, pero todos los usuarios del sistema pueden hacerlo y en cualquier archivo del sistema donde solo se pueda acceder siendo root.

En el segundo ejemplo, tenemos el binario de `php` con permiso SUID.

![[Pasted image 20240225123947.png]]

Así que podemos ver en la página de [https://gtfobins.github.io/](https://gtfobins.github.io/) si por alguna razón hay una manera de conseguir una shell abusando de `php` con permiso SUID.

![[Pasted image 20240225145151.png]]

Al investigar, podemos ver que sí hay una manera de elevar privilegios y conseguir una shell con `php`. Ahora solo ejecutamos el comando y verificamos que seamos root.

![[Pasted image 20240225150628.png]]

Listo, hemos elevado privilegios y somos root.

Para más información sobre binarios a los que se les puede abusar con el permiso SUID, puedes consultar la siguiente página [https://gtfobins.github.io/](https://gtfobins.github.io/).








