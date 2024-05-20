___

- Tags: #docker #redes 

___
# Definición 

**Docker** es una plataforma que simplifica el empaquetado y ejecución de aplicaciones en entornos aislados llamados contenedores. A continuación, se presentan algunos comandos básicos para trabajar con **Docker**.

___
# Comandos Básicos:


```bash 
docker network rm $(docker network ls -q)
```

Borra todas las redes.

   ```bash
   docker network create --driver=bridge nombre-red --subnet=ip/cidr
   ```

Este comando crea una red de Docker y la configura como una red de tipo puente (`bridge`). Además, se especifica una subred con la dirección IP y el CIDR proporcionados. Esta red puede ser utilizada para conectar contenedores y facilitar la comunicación entre ellos.

   ```bash
   docker network ls
   ```

Muestra una lista de todas las redes de Docker disponibles en el sistema. Esto incluye tanto las redes predeterminadas como las creadas por el usuario, como la que acabamos de crear en el primer comando.


   ```bash
   docker network connect nombre-red contenedor
   ```

Este comando conecta el contenedor especificado a una red. La conexión a una red permite que el contenedor pueda comunicarse con otros contenedores en la misma red.

   ```bash
   docker run -dit --name contenedor --network=nombre-red imagen
   ```
   
   Inicia un nuevo contenedor a partir de la imagen especificada. Además, el contenedor se conecta a la red. La opción `-dit` indica que el contenedor se ejecuta en modo daemonizado e interactivo en segundo plano.



```bash
docker pull ubuntu:latest 
```

Crea una imagen con la ultima versión de ubuntu.

```bash 
docker build -t nombre_de_la_imagen .
```

Busca un Dockerfile en el directorio actual y crea una imagen con el nombre especificado.

```bash 
docker run -dit --name nombre_contenedor nombre_imagen
```


Crea un contenedor con un nombre específico, ejecutándolo en segundo plano e interactuando con una terminal propia.


```bash
docker run -dit --privileged --name nombre_contenedor nombre_imagen
```

Este comando crea un contenedor utilizando una imagen con acceso privilegiado

```bash
docker ps
```

Lista los contenedores actualmente en ejecución.

```bash 
docker exec -it nombre_contenedor comando(bash)
```

Interactúa con el contenedor desde una consola virtual.

```bash
docker stop id_contenedor
```

Detiene el contenedor.

```bash 
docker ps -a
```

Muestra todos los contenedores, incluyendo los inactivos.

```bash 
docker rm id_contenedor --force
```

Elimina el contenedor de forma forzada.

```bash 
docker ps -q
```

Muestra solo las IDs de los contenedores.

```bash 
docker rmi id_imagen
```

Borra una imagen.

```bash 
docker images -q
```

Lista todas las IDs de las imágenes.

```bash 
docker images --filter "dangling=true" -q
```

Muestra las IDs de las imágenes en estado "none".
```bash 
docker run -dit -p 80:80 --name nombre_contenedor nombre_imagen
```

Crea un contenedor donde el puerto 80 de la máquina principal se mapea al puerto 80 del contenedor.

```bash 
docker port Contenedor
```

Muestra los puertos utilizados por el contenedor.

```bash 
docker run -dit -p 80:80 -v directorio_maquina:directorio_docker --name nombre_contenedor nombre_imagen
```

Mapea un directorio de la máquina principal a un directorio específico en el contenedor.

```bash 
svn checkout url_carpeta/trunk
```

Descarga una carpeta específica de un repositorio SVN.

____
# Comandos de Docker Compose:

```bash 
docker-compose up -d
```

Despliega los contenedores definidos en un archivo YAML.

```bash 
docker-compose logs
```

Muestra los logs de los contenedores definidos en el archivo YAML.
```
bash docker-compose exec
```

Lanza una terminal en un contenedor definido en el archivo YAML.

___
# Comandos para Volumenes:

```bash 
docker volume ls
```

Lista los volúmenes.
```bash
docker volume ls -q
```

Lista las IDs de los volúmenes.
```bash 
docker volume rm id_volumen
```

Borra un volumen por su ID.