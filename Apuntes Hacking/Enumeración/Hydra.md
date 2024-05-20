___

- Tags: #reconocimiento #herramientas #redes #protocolos  #ftp #ssh #https #http

___

# Descripción

**Hydra** es una herramienta de prueba de penetración que se utiliza para realizar ataques de fuerza bruta en servicios que requieren autenticación. Su versatilidad y capacidad para adaptarse a diversos protocolos lo convierten en una elección popular para evaluar la seguridad de sistemas.

___
# Características Principales

- **Ataques de Fuerza Bruta:** Hydra puede realizar ataques exhaustivos probando múltiples combinaciones de usuarios y contraseñas para obtener acceso no autorizado.
- **Protocolos Soportados:** Admite una amplia variedad de protocolos, incluyendo [[FTP]], [[HTTP]], [[SSH]], [MySQL](https://es.wikipedia.org/wiki/MySQL), entre otros.
- **Configuración de Hilos:** Permite configurar el número de hilos para acelerar los ataques y optimizar el rendimiento.
- **Wordlists Personalizadas:** Puede utilizar wordlists personalizadas para probar contraseñas comunes o débiles.

___
# Ejemplos de Uso

### Ataque a un Servicio FTP:

```bash
hydra -l usuario -P wordlist ftp://ip
```

### Ataque HTTP a través de POST:

```bash

hydra -l usuario -P wordlist http-post-form "/login:username=^USER^&password=^PASS^:Fallo de autenticación" -V
```

### Ataque a SSH:

```bash
hydra -l usuario -P wordlist ssh://ip
```

### Ataque a MySQL:

```bash
hydra -l usuario -P wordlist mysql://ip
```

### Ataque a RDP (Remote Desktop Protocol):

```bash
hydra -t 1 -V -f -l usuario -P wordlist rdp://ip
```

