___

- Tags: #enumeración #hydra #ssh #herramientas #protocolos 

___
# Conexión al Servicio SSH

```bash 
ssh usuario@ip -p puerto
```

Este comando se utiliza para establecer una conexión al servicio [[SSH]] especificando un nombre de usuario, la dirección [[Dirección IP|IP]] del servidor y, opcionalmente, el puerto al que se debe conectar.

# Ataque de Fuerza Bruta con Hydra

```bash 
hydra -l usuario -P wordlist ssh://ip -s puerto
```

[[Hydra]] se emplea para realizar ataques de fuerza bruta al servicio [[SSH]]. La opción `-l` especifica el usuario, `-P` indica la wordlist de contraseñas, y `-s` permite definir el puerto al que dirigir el ataque.

# Identificación de Versión de Ubuntu a través de SSH

- [Launchpad](https://launchpad.net/ubuntu): Al ingresar a esta página web y proporcionar la versión de [[SSH]], se puede obtener información sobre la versión de Ubuntu en la que está corriendo el servicio.

