___

- Tags: #redes #protocolos #https

___
# Descripción

**HTTPS** (Hypertext Transfer Protocol Secure) es una versión segura de [[HTTP]] diseñada para proporcionar una comunicación cifrada y segura a través de la [World Wide Web](https://es.wikipedia.org/wiki/World_Wide_Web). Utiliza una capa adicional de seguridad llamada [SSL/TLS](https://es.wikipedia.org/wiki/Seguridad_de_la_capa_de_transporte) (Secure Sockets Layer/Transport Layer Security) para proteger la confidencialidad e integridad de los datos transmitidos entre el cliente y el servidor.

___
# Características Principales

- **Cifrado de Datos:** Proporciona cifrado de extremo a extremo, asegurando que la información transmitida entre el cliente y el servidor no sea interceptada o modificada por terceros.
- **Certificados SSL/TLS:** Requiere certificados emitidos por autoridades de certificación para autenticar la identidad del servidor y establecer una conexión segura.
- **Puerto Predeterminado:** Opera por lo general en el puerto 443, en contraste con el puerto 80 utilizado por [[HTTP]].

___
# Beneficios y Consideraciones de Seguridad

### Beneficios:

- **Protección contra Ataques de Intercepción:** Evita que los datos confidenciales sean interceptados durante la transmisión.
- **Autenticación del Servidor:** Certificados [SSL/TLS](https://es.wikipedia.org/wiki/Seguridad_de_la_capa_de_transporte) permiten a los clientes verificar la autenticidad del servidor al que se están conectando.
- **Mejora de la Confianza del Usuario:** La presencia del icono de candado en la barra de direcciones indica una conexión segura, aumentando la confianza del usuario.

### Consideraciones:

- **Configuración de Certificados:** Requiere la implementación adecuada de certificados [SSL/TLS](https://es.wikipedia.org/wiki/Seguridad_de_la_capa_de_transporte) en el servidor.
- **Actualizaciones y Buenas Prácticas de Configuración:** Mantener el software actualizado y seguir las buenas prácticas de configuración es esencial para la seguridad continua.

___
# Ejemplo de URL Segura

```
https://www.ejemplo.com
```

