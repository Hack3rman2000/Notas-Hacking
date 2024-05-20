___

- Tags: #herramientas #tcp #udp #nmap #nmap #redes #protocolos #ipv4 #reconocimiento  

___
# Definición 

Un **firewall** es un sistema de seguridad diseñado para supervisar el tráfico de red entrante y saliente. Actúa como una barrera entre la red interna y externa, permitiendo o bloqueando el flujo de datos según reglas predefinidas. Es esencial para proteger una red contra amenazas potenciales.

___
# Evasión de Firewalls con Nmap

[[Nmap]], una herramienta de escaneo de red, también puede utilizarse para evadir **firewalls** mediante técnicas específicas. A continuación, se presentan algunos comandos útiles de [[Nmap]] para evadir **firewalls**:

### Paquetes Fragmentados

```bash 
nmap -f ip
```

Envía un paquete fragmentado, dividiendo la información en fragmentos más pequeños. Este enfoque puede ayudar a evadir la detección de **firewalls**.

### Manipulación del MTU

```bash 
nmap --mtu multipo de 8 ip
```

Ajusta el Maximum Transmission Unit ([MTU](https://es.wikipedia.org/wiki/Unidad_m%C3%A1xima_de_transferencia)) para evadir **firewalls**. Específicamente, utiliza un múltiplo de 8 para modificar el tamaño de los paquetes.
### Escaneo con Lista de IPs

```bash 
nmap ip -D lista de ips
```

Realiza escaneos utilizando una lista de direcciones [[Dirección IP|IP]] proporcionadas. Este método puede dificultar la detección al distribuir el tráfico entre varias IPs.

### Cambio de Puerto de Origen

```bash 
nmap ip --source-port puerto
```

Cambia el puerto de origen en los escaneos, lo que puede ayudar a eludir la detección al ocultar la verdadera naturaleza del tráfico.

### Modificación de Longitud del Paquete

```bash 
nmap ip --data-length longitud
```

Modifica la longitud del paquete agregando la longitud proporcionada al valor original. Esta técnica puede confundir la inspección de paquetes en **firewalls**.

### Falsificación de MAC

```bash
nmap ip --spoof-mac mac/nombre de fabricante
```

Falsifica la dirección [[MAC]] del emisor para ocultar la verdadera identidad del dispositivo que realiza el escaneo.

### Escaneo Silencioso

```bash 
nmap -sS ip
```

Realiza un escaneo sigiloso y un poco más rápido al no establecer conexiones completas, lo que puede ayudar a evadir la detección de **firewalls** que verifican conexiones establecidas.

### Control de Tasa Mínima

```bash 
nmap --min-rate cantidad
```

Controla la tasa de envío de paquetes, agilizando el escaneo. Se recomienda usar 5000 para acelerar el proceso.

