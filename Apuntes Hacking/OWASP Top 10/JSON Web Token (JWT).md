

## Introducción
Los JSON Web Tokens (JWT) son un estándar abierto (RFC 7519) que define un formato compacto y autónomo para la transmisión de información de forma segura entre partes como un objeto JSON. Se utilizan comúnmente para la autenticación y la transmisión de información entre sistemas.

## Estructura
Un JWT consta de tres partes separadas por puntos (`.`):

1. **Header (Encabezado)**: Contiene metadatos sobre el tipo de token y el algoritmo de cifrado utilizado. Este es un objeto JSON codificado en base64url.
   
2. **Payload (Carga útil)**: Es la parte donde se almacenan los datos que queremos transmitir. Esto podría ser información de usuario o cualquier otra información relevante. También se codifica en base64url.

3. **Signature (Firma)**: Es la parte que permite verificar que el token no ha sido alterado en el camino y que proviene de una fuente confiable. Se calcula utilizando el encabezado codificado, la carga útil codificada, una clave secreta y el algoritmo especificado en el encabezado. 

## Ejemplo
Supongamos que tenemos un JWT que contiene la información de autenticación de un usuario. Aquí está cómo se vería dividido en sus tres partes:

### Header
```
{
  "alg": "HS256",
  "typ": "JWT"
}
```
Codificado en base64url: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9`

### Payload
```
{
  "user_id": "1234567890",
  "username": "ejemplo_usuario",
  "exp": 1616422200
}
```
Codificado en base64url: `eyJ1c2VyX2lkIjoiMTIzNDU2Nzg5MCIsInVzZXJuYW1lIjoiZXN0ZXJvbF91c3VhcmlvIiwiZXhwIjoxNjE2NDIyMjAwfQ`

### Signature
La firma se genera utilizando la clave secreta y el algoritmo especificado en el encabezado.

## Conclusión
Los JWT son una forma eficaz y segura de transmitir información entre partes en un formato compacto y autónomo. Su estructura modular permite que sean fácilmente verificables y decodificables por las aplicaciones que los utilizan. Sin embargo, es crucial manejar la seguridad de las claves y los algoritmos de cifrado para garantizar la integridad y confidencialidad de la información transmitida.