Las mutaciones en GraphQL permiten modificar datos en el servidor de manera eficiente y flexible. Sin embargo, cuando no se implementan adecuadas medidas de seguridad, las mutaciones pueden ser vulnerables a ataques de manipulación de datos, como la suplantación de identidad o la modificación no autorizada de información. Es crucial validar y proteger las mutaciones GraphQL para prevenir posibles vulnerabilidades en la aplicación.


# Ejemplo de mutación:

Tenemos un sitio web donde los usuarios pueden crear publicaciones y ver las publicaciones de otros usuarios.

![[Pasted image 20240224153334.png]]

Al crear una nueva publicación haciendo clic en el botón, podemos ver que creamos una publicación como el usuario "jimcarry" con un mensaje y un título predefinidos.

![[Pasted image 20240224153639.png]]

Al interceptar con Burp Suite lo que sucede al dar clic en el botón de crear una nueva publicación, podemos ver que se está enviando una consulta que crea un nuevo post con un título, un cuerpo y un autor predefinidos.

![[Pasted image 20240224155824.png]]

Esto es vulnerable ya que se puede cambiar absolutamente todo en la consulta, como el título, el cuerpo y hasta el autor de la publicación, como se muestra a continuación.

![[Pasted image 20240224160101.png]]

Y ahora, si verificamos si esto ha funcionado, podemos ver que se ha creado un nuevo post con otro título y cuerpo, y además, el autor ha cambiado.

![[Pasted image 20240224160312.png]]

Las mutaciones en GraphQL ofrecen flexibilidad para realizar cambios en los datos del servidor, pero es crucial implementar medidas de seguridad para evitar vulnerabilidades como la que se muestra en este ejemplo.