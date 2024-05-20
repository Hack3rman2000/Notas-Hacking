___

- Tags: #enumeración #https #herramientas #redes #protocolos 

___
# Inspección del Certificado HTTPS
 
 ```bash 
 openssl s_client -connect tinder.com:443
 ```
 
 Este comando se utiliza para inspeccionar el certificado [SSL](https://es.wikipedia.org/wiki/Seguridad_de_la_capa_de_transporte) de un sitio web que utiliza [[HTTPS]]. Proporciona información detallada sobre el certificado, incluyendo su validez, autoridad de certificación, y otros detalles relacionados con la seguridad.

___
# Escaneo SSL para Identificar Vulnerabilidades

```bash 
sslscan sitio
```

Es una herramienta que realiza un escaneo [SSL](https://es.wikipedia.org/wiki/Seguridad_de_la_capa_de_transporte) en un sitio web específico para identificar posibles vulnerabilidades. Proporciona información detallada sobre los protocolos [SSL/TLS](https://es.wikipedia.org/wiki/Seguridad_de_la_capa_de_transporte) soportados, cifrados utilizados y posibles problemas de seguridad.

___
# Consideraciones

- La inspección del certificado [SSL](https://es.wikipedia.org/wiki/Seguridad_de_la_capa_de_transporte) ayuda a confirmar la autenticidad y seguridad de la conexión [HTTPS](https://es.wikipedia.org/wiki/Seguridad_de_la_capa_de_transporte).
- El escaneo [SSL](https://es.wikipedia.org/wiki/Seguridad_de_la_capa_de_transporte) es una práctica recomendada para identificar y abordar posibles vulnerabilidades en la configuración de seguridad de un servidor.

