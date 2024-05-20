

Uno de los vectores de ataque comunes es la manipulación de JSON Web Tokens (JWT), una forma popular de transmitir información de forma segura entre partes en un formato compacto. En este artículo, exploraremos dos técnicas de enumeración y explotación de JWT que pueden comprometer la integridad de un sistema si no se manejan adecuadamente.

**JWT Nulo**

Una vulnerabilidad significativa en JWT es la posibilidad de usar un algoritmo de firma "NONE". Esto permite a un atacante omitir la firma y modificar el contenido del token sin invalidarlo. Para comenzar, podemos examinar la estructura de un JWT visitando el sitio web https://jwt.io/.

![[Pasted image 20240223190110.png]]

Aprovechando esta vulnerabilidad, podemos cambiar el encabezado (header) para que el algoritmo (alg) sea "NONE", lo que significa que no se utilizará ninguna firma. Esto se puede lograr codificando el siguiente encabezado en base64:

```bash
echo -n '{"alg":"NONE", "typ":"JWT"}' | base64
```

El siguiente paso implica codificar el payload deseado en base64:

```bash
echo -n '{"id":2, "iat":1677770355, "exp":1677773955}' | base64
```

Con estos elementos, podemos construir un JWT válido sin firma:

```bash
eyJhbGciOiJOT05FIiwgInR5cCI6IkpXVCJ9.eyJpZCI6MiwgImlhdCI6MTY3Nzc3MDM1NSwgImV4cCI6MTY3Nzc3Mzk1NX0.
```

Al substituir el token existente con este JWT, se puede acceder a la cuenta correspondiente al usuario con id 2 sin necesidad de una firma.

**JWT Secreto**

Si el servidor no acepta JWT sin firma, es probable que utilice un secreto en la firma. Para descubrir este secreto, se puede realizar un ataque de fuerza bruta al token utilizando herramientas como "John the Ripper":

```bash
john jwt.txt --wordlist=lista --format=HMAC-SHA256
```

Una vez que se ha descubierto el secreto, se puede utilizar para firmar un nuevo token. Esto se puede hacer a través de la interfaz en https://jwt.io/, donde se pueden modificar varias partes del JWT, incluido el payload para cambiar el ID del usuario.

![[Pasted image 20240223184346.png]]

Al generar un nuevo JWT con el secreto añadido y el payload modificado, este token será válido. Al acceder al sistema con este JWT, se asumirá la identidad del usuario modificado.

En conclusión, es crucial para los desarrolladores y administradores de sistemas comprender las vulnerabilidades asociadas con JWT y tomar medidas adecuadas para mitigar estos riesgos, como el uso de algoritmos de firma seguros y la gestión adecuada de secretos. La seguridad de un sistema depende de la atención a estos detalles y la adopción de las mejores prácticas en el diseño y la implementación de la autenticación y la autorización.