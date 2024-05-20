
## Introducción
En el contexto de seguridad informática, la Insecure Direct Object Reference (IDOR) es una vulnerabilidad que ocurre cuando un sistema de software expone referencias a objetos internos, como archivos, directorios o claves de base de datos, sin autenticación adecuada o autorización de acceso.

## Ejemplo de IDOR

Tenemos un sitio web en el cual puedes ver las publicaciones de los usuarios, donde existe un hipervínculo hacia un punto final.

![[Pasted image 20240224084213.png]]

Al acceder a este enlace, se muestra información del usuario actualmente autenticado, como nombre, apellido, fecha de nacimiento y una clave API.

![[Pasted image 20240224084434.png]]

Mediante el uso de Burp Suite para interceptar el tráfico, se observa una solicitud POST a GraphQL que contiene una consulta.

![[Pasted image 20240224085831.png]]

Al enviar esta solicitud, se muestra la información de un usuario específico, utilizando aparentemente el parámetro UserInfo (id: numero_id) para mostrar los detalles del usuario según el ID proporcionado.

![[Pasted image 20240224090657.png]]

## Explotación de la Vulnerabilidad
La vulnerabilidad radica en la falta de control de acceso adecuado a los datos de usuario. Esto permite a un atacante potencialmente acceder a la información de otros usuarios simplemente cambiando el ID en la consulta GraphQL, lo que constituye un escenario clásico de IDOR.

## Ejemplo de Explotación
Para explotar esta vulnerabilidad, un atacante puede intentar enumerar información cambiando el ID en la consulta GraphQL. Al hacerlo, puede acceder a la información de diferentes usuarios sin autenticación o autorización adecuada.

![[Pasted image 20240224091232.png]]

Al realizar este tipo de enumeración, se confirma que el sistema es vulnerable a IDOR, ya que permite a los atacantes obtener información confidencial de otros usuarios.

## Conclusiones
La presencia de esta vulnerabilidad representa un riesgo significativo para la privacidad y seguridad de los usuarios. Se recomienda tomar medidas inmediatas para corregir este problema, como implementar controles de acceso adecuados y realizar pruebas exhaustivas de seguridad para identificar y remediar posibles vulnerabilidades en el sistema.