___

- Tags: 

____

# Definición 

**CSRF** es un tipo de ataque en el que un atacante induce a un usuario a realizar acciones no deseadas en una aplicación web en la que el usuario está autenticado. Esto se logra mediante la ejecución de solicitudes [[HTTP]] no autorizadas en nombre del usuario autenticado.

___
# Ejemplos de URLs maliciosas:

```
http://sitio-malicioso.com/cambiar-contrasena?nueva-contrasena=123456
```

En este ejemplo, el atacante podría enviar al usuario un enlace que, al hacer clic, cambiaría la contraseña del usuario en el sitio vulnerable a "123456".

```
http://sitio-malicioso.com/transferir-fondos?destino=atacante&cantidad=1000
```

Aquí, un atacante podría engañar al usuario para que haga clic en un enlace que, sin su conocimiento, realiza una transferencia de fondos a la cuenta del atacante.

```
http://sitio-malicioso.com/publicar-mensaje?mensaje=Este es un mensaje malicioso
```

En este caso, el atacante podría hacer que el usuario publique un mensaje no deseado en su cuenta de una red social, simplemente haciendo clic en un enlace.

Recuerda que estos son ejemplos simplificados y la realidad puede ser más compleja. La prevención de **CSRF** implica el uso de **tokens anti-CSRF** y prácticas de seguridad en el desarrollo web, como verificar el origen de las solicitudes y implementar políticas de mismo origen (Same Origin Policy).

___
# Ejemplos de ataques:

En algunos casos, los ataques **CSRF** pueden involucrar el uso de imágenes maliciosas para inducir solicitudes no deseadas. Este método explota la capacidad de cargar imágenes desde ubicaciones externas y ejecutar scripts cuando se cargan en una página web.

1. **Imagen Maliciosa que Carga una URL:**

   - Un atacante podría incrustar una etiqueta de imagen en una página web con una URL maliciosa como su fuente. Cuando un usuario carga la página, la imagen intenta cargar la URL maliciosa, realizando así una acción no autorizada.

   ```html
   <img src="http://sitio-malicioso.com/ataque-csrf" alt="Imagen Maliciosa" width="1" height="1"> 
   ```

   - Es importante destacar que este método puede no ser efectivo en todos los contextos debido a restricciones de seguridad, como la política de mismo origen.

2. **Burp Suite y Manipulación de Métodos HTTP:**

   - En [[BurpSuite]], una herramienta de prueba de seguridad, es posible manipular solicitudes [[HTTP]]. Puedes convertir una solicitud que originalmente se realiza mediante el método **POST** a una solicitud **GET**. Esto se hace modificando la solicitud antes de enviarla al servidor.

   - Por ejemplo, si una aplicación web utiliza una solicitud **POST** para cambiar la contraseña:

     ```
     POST /cambiar-contrasena HTTP/1.1
     Host: ejemplo.com
     Content-Type: application/x-www-form-urlencoded

     nueva-contrasena=123456
     ```

- Puedes manipularla en [[BurpSuite]] para que use el método **GET**:

     ```
     GET /cambiar-contrasena?nueva-contrasena=123456 HTTP/1.1
     Host: ejemplo.com
     ```

- Esto demuestra la importancia de utilizar medidas de seguridad como **tokens anti-CSRF** y validar y restringir los métodos [[HTTP]] permitidos para acciones sensibles.

Recuerda que estos ejemplos se proporcionan con fines educativos y éticos. Los desarrolladores deben implementar medidas de seguridad adecuadas para proteger sus aplicaciones contra estos tipos de ataques.