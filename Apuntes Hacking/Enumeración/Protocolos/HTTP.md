___

- Tags: #redes #protocolos #http 

___
# Descripción

**HTTP** (Hypertext Transfer Protocol) es el protocolo de comunicación utilizado para la transferencia de información en la [World Wide Web](https://es.wikipedia.org/wiki/World_Wide_Web). Se basa en un modelo cliente-servidor, donde los clientes, como navegadores web, solicitan recursos y los servidores web responden proporcionando dichos recursos.

___
# Características Principales

- **Protocolo sin Estado:** Cada solicitud del cliente al servidor es independiente, sin almacenar información sobre el estado de la conexión.
- **Métodos de Solicitud:** Utiliza métodos como **GET** para obtener recursos, **POST** para enviar datos al servidor, y otros para diversas operaciones.
- **Encabezados HTTP:** Contienen información adicional sobre la solicitud o respuesta, como tipo de contenido, codificación, y cookies.
- **Códigos de Estado:** Indican el resultado de la solicitud, como 200 OK, 404 Not Found, entre otros.

___
# Ejemplos de Uso

### Solicitud GET:

```bash
curl http://ejemplo.com/pagina
```

### Solicitud POST con Datos:

```bash
curl -X POST -d "usuario=nombre&clave=contrasena" http://ejemplo.com/login
```

___
# Seguridad y Consideraciones

- **HTTPS (HTTP Seguro):** Se utiliza para cifrar la comunicación entre el cliente y el servidor, proporcionando mayor seguridad.
- **Encabezados de Seguridad:** Encabezados como Content-Security-Policy y Strict-Transport-Security ayudan a mejorar la seguridad de las aplicaciones web.
- **Pruebas de Seguridad:** Herramientas como OWASP Zap y Burp Suite se utilizan para evaluar la seguridad de aplicaciones web.

