___

- Tags: 

___
# Definición 

Los ataques de Client-Side Template Injection (**CSTI**) son una clase de vulnerabilidades de seguridad que pueden tener consecuencias graves en la seguridad de las aplicaciones web. Estos ataques son similares a Server-Side Template Injection ([[SSTI]]) pero ocurren en el lado del cliente.

____
# Relación con XSS

Una de las preocupaciones principales con **CSTI** es que puede derivar en Cross-Site Scripting ([[XSS]]), una vulnerabilidad común en aplicaciones web. Los ataques **CSTI** pueden permitir la ejecución de scripts maliciosos en el navegador del usuario, lo que podría comprometer la seguridad de la aplicación y la privacidad del usuario.

___
# Similaridades con SSTI

**CSTI** comparte similitudes con Server-Side Template Injection ([[SSTI]]). Ambos involucran la manipulación de plantillas para ejecutar código arbitrario. Sin embargo, mientras [[SSTI]] ocurre en el servidor, **CSTI** tiene lugar en el navegador del cliente.

___
# Cómo se produce CSTI

La vulnerabilidad **CSTI** suele ocurrir cuando una aplicación web permite la entrada de datos no validados o no escapados en plantillas del lado del cliente. Esto puede ocurrir en situaciones como la interpolación de cadenas en frameworks de JavaScript como Angular, Vue.js, o React.

___
# Ejemplo de Payload en Angular:

Supongamos que la aplicación Angular utiliza una expresión en doble corchete (`{{ expresion }}`) para mostrar datos en el navegador. Un [[Payload Staged|payload]] **CSTI** podría ser:

```javascript
{{ 7 * 7 }}
```

Este [[Payload Staged|payload]] hará que se ejecute el código `7 * 7` en el contexto del navegador del usuario.

____
# Uso de String.fromCharCode()

En casos donde un [[Payload Non-Staged|payload]] **CSTI** no funcione directamente, se puede intentar usar la función `String.fromCharCode()` para ofuscar el código malicioso. Por ejemplo:

```javascript
{{ String.fromCharCode(97, 108, 101, 114, 116, 40, 49, 41) }}
```

Este [[Payload Staged|payload]] ejecutará la función `alert(1)` después de ser convertido por `String.fromCharCode()`.

___
# Payloads de CSTI

Para comprender mejor los posibles [[Payload Staged|payloads]] y escenarios de **CSTI**, se pueden consultar los recursos a continuación:

- [PayloadsAllTheThings - XSS in Angular](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/XSS%20in%20Angular.md)
- [HackTricks - Client-Side Template Injection (CSTI)](https://book.hacktricks.xyz/pentesting-web/client-side-template-injection-csti)

Estos recursos proporcionan ejemplos de [[Payload Staged|payloads]] y técnicas que pueden ser utilizadas para identificar y explotar vulnerabilidades **CSTI** en aplicaciones web.

Recuerda siempre mantener tus aplicaciones actualizadas y realizar pruebas de seguridad regulares para identificar y remediar posibles vulnerabilidades.
