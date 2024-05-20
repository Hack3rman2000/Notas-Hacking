____

- Tags: #ftp #redes #protocolos  #tcp 

____

# Introducción

El Protocolo de Transferencia de Archivos (**FTP**) es un estándar de red utilizado para la transferencia de archivos entre un cliente y un servidor en una red [[TCP|TCP/IP]]. **FTP** permite la carga y descarga de archivos, así como operaciones básicas en sistemas de archivos remotos.

___
# Comandos Básicos de FTP

```bash 
ftp dirección_del_servidor
```

Inicia una sesión **FTP** con el servidor especificado.

```bash 
open dirección_del_servidor
```

Abre una conexión al servidor **FTP**.

```bash
user nombre_de_usuario contraseña
```

Autentica al usuario en el servidor **FTP**.

```bash 
ls
```

Lista los archivos y directorios en el directorio remoto.

```bash
cd directorio
```

Cambia al directorio remoto especificado.

```bash
get nombre_del_archivo
```

Descarga un archivo desde el servidor **FTP** al sistema local.

```bash 
put nombre_del_archivo
```

Sube un archivo desde el sistema local al servidor **FTP**.

```bash
delete nombre_del_archivo
```

Elimina un archivo en el directorio remoto.

```bash
quit
```

Cierra la sesión **FTP** y sale del cliente.

___
# Modos de Transferencia

- **Modo Activo:** El cliente abre un puerto para la transferencia de datos.
- **Modo Pasivo:** El servidor abre un puerto para la transferencia de datos.

___
# Seguridad FTP

- **FTP Seguro (FTPS):** Versión segura de **FTP** que utiliza [TLS/SSL](https://es.wikipedia.org/wiki/Seguridad_de_la_capa_de_transporte) para cifrar la conexión.

  ___
# Configuraciones Adicionales

- **Configuración de Puertos:** **FTP** utiliza los puertos 20 (datos) y 21 (control) de forma predeterminada.
- **Usuarios Anónimos:** Algunos servidores **FTP** permiten conexiones anónimas sin necesidad de autenticación.

___
# Ejemplo de Uso Básico

1. Iniciar sesión en el servidor **FTP**:

   ```bash
   ftp nombre_del_servidor
   ```

2. Autenticarse:
   
   ```bash
   user nombre_de_usuario contraseña
   ```

3. Listar archivos remotos:

   ```bash
   ls
   ```

4. Descargar un archivo:

   ```bash
   get nombre_del_archivo
   ```

5. Subir un archivo:
   
   ```bash
   put nombre_del_archivo
   ```

6. Cerrar sesión **FTP**:
   
   ```bash
   quit
   ```

