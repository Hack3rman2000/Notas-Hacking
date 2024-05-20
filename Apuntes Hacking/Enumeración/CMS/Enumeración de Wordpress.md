___

- Tags: #herramientas #cms #wpscan #enumeración  #wordpress 

___

# Escaneo de Plugins y Usuarios

```bash
wpscan --url url -e vp,u --api-token "apitoken"
``` 

Es utilizado para realizar un escaneo de seguridad en un sitio web construido con [[WordPress]]. Aquí hay un desglose de sus principales funciones:

- **Enumeración de Plugins (vp):** Este parámetro permite identificar los plugins instalados en el sitio, lo que es crucial para evaluar posibles vulnerabilidades.

- **Enumeración de Usuarios (u):** Permite obtener información sobre los usuarios registrados en el sitio, lo que puede ser útil para identificar posibles puntos de entrada.

- **API Token:** Al proporcionar un token de API válido de [[WPScan]], se amplían las capacidades del escaneo, permitiendo acceder a información más detallada y posiblemente descubrir exploits específicos.

___
# Identificación de Vulnerabilidades

El escaneo revela información sobre posibles vulnerabilidades, especialmente en archivos y directorios críticos como `xmlrpc.php` y `wp-admin`. Estos puntos son comunes en ataques y deben ser monitoreados y asegurados adecuadamente.

```bash 
curl -s -X POST direccion -d@archivo
```

Es útil para enviar archivos a través del método POST. Este comando puede ser utilizado en situaciones donde se necesite interactuar con un servidor, por ejemplo, para realizar pruebas de carga o enviar datos a una aplicación web.

___
# Ataque de Fuerza Bruta con WPScan

```bash 
wpscan --url url -U usuario -P wordlist
``` 

Se emplea para realizar un ataque de fuerza bruta contra un sitio [[WordPress]] mediante el protocolo `xmlrpc`. Aquí, se intenta descifrar contraseñas utilizando una lista de contraseñas (wordlist) con un nombre de usuario específico.

