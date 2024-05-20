___

- Tags: #redes #enumeración  #hydra #herramientas #ftp #protocolos 

___

# Conexión al Servicio FTP:
```bash 
ftp usuario@ip
```

Este comando se utiliza para establecer una conexión al servicio [[FTP]] especificando un nombre de usuario y la dirección [[Dirección IP|IP]] del servidor.

# Ataque de Fuerza Bruta con Hydra:

```bash 
hydra -l/L usuario -P/p contraseña/wordlist ftp://ip
```

 [[Hydra]] permite realizar ataques de fuerza bruta al servicio [[FTP]]. La opción `-l` especifica el usuario o una lista de usuarios (`-L`) a probar, mientras que `-p` indica la contraseña o una wordlist (`-P`) a utilizar. El ataque se dirige a la dirección [[FTP]] especificada.

# Ataque de Fuerza Bruta con Hilos:

```bash 
hydra -l usuario -P wordlist ftp://ip -t hilos
```

Este comando de [[Hydra]] realiza un ataque de fuerza bruta al servicio [[FTP]] utilizando hilos. La opción `-t` permite especificar el número de hilos a utilizar durante el ataque.

