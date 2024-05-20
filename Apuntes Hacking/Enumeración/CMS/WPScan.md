
___

- Tags: #herramientas #cms #enumeración #wordpress 

---

# Definición 

**WPScan** es una herramienta de seguridad ampliamente utilizada para la detección de vulnerabilidades en instalaciones de [[WordPress]]. Su función principal es escanear sitios web basados en [[WordPress]] en busca de debilidades de seguridad, como plugins desactualizados, temas vulnerables o configuraciones incorrectas.

## Características Principales:

1. **Escaneo de Versiones:** **WPScan** puede identificar la versión de [[WordPress]] instalada, así como las versiones de temas y plugins activos. Esto es crucial para determinar si hay versiones desactualizadas que podrían tener vulnerabilidades conocidas.

2. **Detección de Plugins y Temas Vulnerables:** Analiza la lista de plugins y temas instalados en el sitio web, proporcionando información sobre vulnerabilidades conocidas asociadas con esas extensiones.

3. **Fuerza Bruta de Contraseñas:** **WPScan** puede realizar ataques de fuerza bruta contra cuentas de usuario para evaluar la solidez de las contraseñas y la seguridad de las credenciales.

4. **Enumeración de Usuarios:** Permite identificar nombres de usuario válidos en el sitio, facilitando posibles intentos de acceso no autorizado.

___
### Uso Básico:

```bash
wpscan --url http://sitio-web.com
```

Este comando básico inicia un escaneo en el sitio proporcionado, brindando información valiosa sobre las vulnerabilidades potenciales.

### Escaneo de Plugins

Realizar un escaneo específico para encontrar vulnerabilidades en plugins instalados:

```bash
wpscan --url http://sitio-web.com --enumerate p
```

### Escaneo de Temas

Escanear en busca de vulnerabilidades en temas activos:

```bash
wpscan --url http://sitio-web.com --enumerate t
```

### Escaneo de Usuarios

Enumerar usuarios del sitio para identificar posibles puntos de entrada:

```bash
wpscan --url http://sitio-web.com --enumerate u
```

### Escaneo de Fuerza Bruta para Contraseñas

Realizar un ataque de fuerza bruta contra las credenciales del sitio:

```bash
wpscan --url http://sitio-web.com --passwords lista-contraseñas.txt --usernames lista-usuarios.txt
```

### Escaneo de Todo en Uno

Ejecutar un escaneo completo que incluya información sobre versiones, plugins, temas, usuarios y realizar ataques de fuerza bruta:

```bash
wpscan --url http://sitio-web.com --enumerate --passwords lista-contraseñas.txt --usernames lista-usuarios.txt
```

Recuerda ajustar las listas de contraseñas y usuarios según tus necesidades, y ten en cuenta que el uso de WPScan debe cumplir con las leyes y regulaciones locales, así como los términos de servicio del sitio web que estás escaneando.