___

- Tags: #protocolos  #redes #ssh

___
# Descripción
**SSH** (Secure Shell) es un protocolo de red utilizado para acceder de forma segura a dispositivos remotos a través de una conexión encriptada. Proporciona una alternativa segura a protocolos de acceso remoto menos seguros, como [Telnet](https://es.wikipedia.org/wiki/Telnet).

___
# Características Principales

- **Cifrado de Datos:** **SSH** cifra toda la comunicación entre el cliente y el servidor, protegiendo la confidencialidad de la información transmitida.
- **Autenticación Segura:** Utiliza métodos de autenticación seguros, como contraseñas, claves públicas/privadas y autenticación de dos factores.
- **Transferencia de Archivos Segura:** Además del acceso remoto, **SSH** permite la transferencia segura de archivos a través de [SFTP](https://es.wikipedia.org/wiki/SSH_File_Transfer_Protocol) (SSH File Transfer Protocol) y [SCP](https://es.wikipedia.org/wiki/Secure_Copy) (Secure Copy Protocol).
- **Puertos Configurables:** Puede configurarse para utilizar puertos distintos al estándar (22) para mejorar la seguridad.

___
# Uso Básico

### Conexión SSH:

```bash
ssh usuario@ip
```

### Conexión con Puerto Específico:

```bash
ssh -p puerto usuario@ip
```

### Copiar Archivos con SCP:

```bash
scp archivo.txt usuario@ip:/ruta/destino
```

___
# Configuración Adicional

### Autenticación por Claves:

- Generar Claves: `ssh-keygen -t rsa`
- Copiar Clave al Servidor: `ssh-copy-id usuario@ip`

### Cambiar Puerto Predeterminado (Ejemplo: 2222):

Editar archivo `/etc/ssh/sshd_config` y reiniciar el servicio.

___
# Consideraciones de Seguridad
- Desactivar acceso como root por **SSH**.
- Mantener el software **SSH** actualizado.
- Limitar el acceso por dirección IP utilizando reglas de [[Firewall y evasión de firewalls en Nmap|firewall]].

