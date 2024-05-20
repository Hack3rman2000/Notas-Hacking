
## Introducción

Insecure Direct Object Reference (IDOR) es una vulnerabilidad común en aplicaciones web que ocurre cuando un usuario puede acceder directamente a objetos o recursos sin la debida autorización. Esto puede permitir a un atacante acceder, modificar o eliminar recursos sin restricciones, comprometiendo la seguridad y la integridad de la aplicación.

## Ejemplo

Supongamos que una aplicación de gestión de empleados tiene URLs como esta para acceder a los detalles de un empleado:

```
https://example.com/employee?id=123
```

Donde "123" es el ID del empleado. Si la aplicación no implementa un control adecuado de autorización, un atacante podría manipular el parámetro "id" en la URL para acceder a los detalles de otros empleados, como por ejemplo:

```
https://example.com/employee?id=124
```

## Ejemplo con Wfuzz 

Wfuzz es una herramienta de prueba de penetración que puede ser utilizada para explotar IDORs. Podemos usar la función Range de Wfuzz para enumerar los IDs de empleados.

```
wfuzz -c -z range,1-1000 https://example.com/employee?id=FUZZ
```

En este ejemplo, Wfuzz intentará realizar solicitudes a la URL proporcionada, reemplazando "FUZZ" con valores en el rango de 1 a 1000. Si la aplicación no está protegida contra IDORs, esto podría revelar detalles de empleados que no deberían ser accesibles.

Es crucial que los desarrolladores implementen adecuadas medidas de control de acceso y validación de autorización para prevenir este tipo de vulnerabilidades.

Recuerda siempre obtener autorización antes de realizar pruebas de penetración en sistemas en producción.