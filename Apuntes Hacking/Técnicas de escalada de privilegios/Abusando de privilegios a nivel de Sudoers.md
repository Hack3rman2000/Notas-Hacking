# Introducción


Los archivos `/etc/sudoers` contienen configuraciones que permiten a usuarios específicos ejecutar comandos con privilegios elevados en sistemas basados en Unix. Estas configuraciones definen qué usuarios pueden ejecutar qué comandos y con qué privilegios. El abuso de estas configuraciones puede conducir a vulnerabilidades de seguridad significativas, permitiendo a usuarios maliciosos realizar acciones no autorizadas en el sistema.

```bash
sudo -l 
```

Sirve para listar los privilegios del usuario.

Como usuario root, puedes definir una política para que algún usuario pueda ejecutar un comando como root sin necesitar contraseña modificando el archivo `/etc/sudoers`, solo agregando lo siguiente:

```bash
usuario ALL(root) NOPASSWD: ruta_comando 
```

Entonces, a modo de ejemplo, podemos añadir como comando que se ejecute como root y sin contraseña el `awk` con lo siguiente:

![[Pasted image 20240225001255.png]]

Al hacer el siguiente comando, se estaría ejecutando como root pero usándolo desde otro usuario:

```bash
sudo awk
```

Lo que podemos hacer para tratar de elevar privilegios es buscar en la siguiente página [https://gtfobins.github.io/](https://gtfobins.github.io/) si con el binario de `awk` se pueden elevar privilegios y conseguir una shell. Al buscar `awk` en la página, podemos ver que aparentemente con el `awk` sí se puede sacar una shell:

![[Pasted image 20240224233650.png]]

Al entrar, podemos ver que menciona un comando para conseguir una shell:

![[Pasted image 20240224233749.png]]

Desde un usuario sin privlegios, ejecutamos el comando:

![[Pasted image 20240225001459.png]]

Y listo, al hacer un `whoami`, podemos ver que somos el usuario root.

Podemos ver todos los binarios con los que se puede sacar una shell al colocar lo siguiente en [https://gtfobins.github.io/](https://gtfobins.github.io/):

![[Pasted image 20240225001825.png]]


Otro ejemplo puede ser que al momento de hacer un `sudo -l`, vemos lo siguiente:

![[Pasted image 20240225004101.png]]

Se puede ver que el comando `nmap` se está ejecutando como el usuario `prueba2`. Entonces, en principio, al ejecutar el siguiente comando, estaríamos ejecutando el comando `nmap` como el usuario `prueba2`:

```bash
sudo -u prueba2 nmap
```

Ahora, podemos consultar en la página de [https://gtfobins.github.io/](https://gtfobins.github.io/) si hay alguna forma de conseguir una shell con el comando `nmap` y al parecer sí la hay:

![[Pasted image 20240225005354.png]]

Esta forma está bien, pero no es la mejor, así que haré lo siguiente: entraremos a un directorio donde se tenga permiso de escritura, en este caso `tmp`, y ejecutamos los siguientes comandos:

![[Pasted image 20240225010454.png]]

Perfecto, ya con esto tendremos una shell y seremos el usuario `prueba2`.

Finalmente, quiero mencionar que la página de [https://gtfobins.github.io/](https://gtfobins.github.io/) es demasiado buena para la escalada de los binarios, ya que muestra mucha información sobre cómo aprovecharnos de malas prácticas para así elevar privilegios.