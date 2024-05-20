# Descripción general

___

- Tags: #redes #herramientas #reconocimiento #protocolos #ipv4 #udp #tcp #nmap

___
# Definición 

**Nmap** (Network Mapper) es una poderosa herramienta de escaneo de red que proporciona información detallada sobre los dispositivos y servicios en una red. Permite explorar la topología de la red, identificar puertos abiertos, y obtener detalles sobre sistemas operativos y servicios en ejecución.

___
# Comandos Básicos

### Escaneo de Todos los Puertos

```bash 
nmap -p- ip
```

Escanea todos los puertos disponibles en la dirección [[Dirección IP|IP]] especificada.

### Escaneo de Rango de Puertos

```bash 
nmap -p 1-10 ip
```

Escanea los puertos en un rango del 1 al 10 en la dirección [[Dirección IP|IP]]  especificada.

### Escaneo de Puertos Comunes

```bash 
nmap ip
```

Escanea los puertos más comunes en la dirección [[Dirección IP|IP]]  especificada.

### Estados de Puertos

 Los puertos pueden estar en tres estados: abierto, cerrado y filtrado. **Nmap** no puede determinar si un puerto está abierto o filtrado.

### Escaneo de los Puertos más Famosos

```bash 
nmap --top-ports cantidad ip
```
 
 Escanea según la cantidad dada de los puertos más famosos.

### Muestra Solo Puertos Abiertos

```bash 
nmap --open ip
```

Muestra solo los puertos que están abiertos.

### Sin Resolución DNS

```bash 
nmap -n ip
```

Desactiva la resolución [DNS](https://es.wikipedia.org/wiki/Sistema_de_nombres_de_dominio) y no muestra los nombres de dominio.

### Detalles Verbosos

```bash
nmap -v ip
```

Muestra en todo momento los puertos encontrados, proporcionando más detalles.

### Plantillas de Escaneo

```bash 
nmap -Tnumero ip
```

Utiliza una plantilla de escaneo del 0 al 5, donde 0 es el más pasivo y 5 el más agresivo.

### Escaneo por Protocolo TCP

```bash 
nmap -sT ip
```

Realiza un escaneo por el protocolo [[TCP]].

### Escaneo por Protocolo UDP

 ```bash
 nmap -sU ip
 ```
 
 Realiza un escaneo por el protocolo [[UDP]].

### Escaneo sin Importar el Estado del Host

```bash
nmap -Pn ip
```

Escanea los puertos sin importar si el host está activo o no.

### Muestra Hosts Activos en la Red

```bash 
nmap -sn ip/cidr
```

Muestra los hosts activos en la red.

### Identificación del Sistema Operativo

```bash 
nmap -O ip
```

Muestra el sistema operativo de la dirección [[Dirección IP|IP]] especificada.

### Muestra Versión y Servicio de Puertos

 ```bash 
 nmap -sV ip
 ```
 
 Muestra la versión y el servicio de los puertos encontrados en la dirección [[Dirección IP|IP]] especificada.

```bash 
nmap ip -oN archivo
```

Guarda el output de **Nmap** en un archivo.

```bash 
nmap ip -oG archivo
```

Guarda el output de **Nmap** en formato grepeable.


