

La enumeración y explotación de proxies SQUID proporciona a los investigadores de seguridad y a los profesionales de la informática una oportunidad para comprender mejor la seguridad de una red y descubrir posibles vulnerabilidades. A través de herramientas simples como cURL y gobuster, es posible realizar una exploración exhaustiva y detectar posibles puntos débiles en la configuración del servidor proxy. Aquí se presentan algunas técnicas básicas para llevar a cabo esta tarea.

**1. Utilizando cURL para Realizar Solicitudes a través del Proxy:**
```bash
curl ip:puerto --proxy ip:puerto
```
Este comando permite realizar solicitudes a una dirección IP en un puerto específico a través de un proxy SQUID. Es esencial para verificar la accesibilidad y el funcionamiento del proxy.

**2. Acceso Directo al Servicio HTTP desde el Navegador:**
FoxyProxy es una herramienta que facilita el acceso a servicios HTTP a través de proxies desde el navegador. Al agregar el proxy SQUID en FoxyProxy, se puede acceder al servicio HTTP directamente desde el navegador, lo que simplifica la interacción y la evaluación del servicio.

![[Pasted image 20240220214901.png]]

![[Pasted image 20240220215101.png]]



**3. Enumeración de Directorios con Gobuster:**
```bash
gobuster dir -u url --proxy ip:puerto -w wordlist -t hilos
```
Gobuster es una herramienta muy útil para la enumeración de directorios en servidores web. Al utilizar un proxy SQUID, se puede realizar la enumeración de directorios de manera eficiente, lo que permite identificar posibles puntos de entrada o vulnerabilidades en la aplicación web alojada en el servidor.

**4. Ingreso con Credenciales en Caso de Autenticación:**
```bash
curl ip:puerto --proxy usuario:contraseña@ip:puerto
```
En situaciones donde se requieran credenciales para acceder al proxy, es posible proporcionarlas directamente a través del comando cURL. Esto facilita la autenticación y permite continuar con la exploración y la evaluación del sistema.

En resumen, la combinación de herramientas como cURL, FoxyProxy y Gobuster, junto con la capacidad de interactuar con proxies SQUID, proporciona a los investigadores de seguridad y a los profesionales de TI un conjunto de técnicas poderosas para evaluar la seguridad de una red y descubrir posibles vulnerabilidades. Es fundamental realizar estas actividades dentro del marco legal y ético correspondiente, obteniendo siempre el permiso adecuado antes de realizar cualquier evaluación de seguridad.