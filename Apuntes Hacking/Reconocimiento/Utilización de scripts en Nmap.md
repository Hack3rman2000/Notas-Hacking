___

- Tags: #herramientas #tcp #udp #nmap #nmap #redes #protocolos  #ipv4 #scripting #reconocimiento 

___
# Enumeración de Puertos con Scripts en Nmap

 ```bash 
 nmap -sC ip
 ```
 
Utiliza los principales scripts de [[Nmap]] para enumerar puertos en la dirección [[Dirección IP|IP]] especificada.

```bash 
nmap --script script ip
```

Enumera los puertos y además utiliza el script proporcionado para obtener información adicional.

```bash 
nmap --script http-enumi ip
```

Enumera los puertos y busca una serie de directorios en servicios [[HTTP]] para identificar posibles vulnerabilidades.

 ```bash 
 nmap --script=="vuln and safe" ip
 ```

Utiliza los scripts de las categorías vuln y safe para identificar posibles vulnerabilidades y verificar la seguridad.

---

# Script en Lua para Identificar Puertos Abiertos

### Creación del Script

1. Crear un archivo con extensión **.nse**.
2. Desarrollar el script con el siguiente contenido:

```lua
-- HEAD --

description = [[
Descripción del script
]]

-- RULE --

portrule = function(host,port)
  return port.protocol == "tcp"
    and port.state == "open"
end

-- ACTION -- 
action = function (host,port)
  return "Este puerto está abierto"
end
```

### Ejecución del Script

Para ejecutar el script, utiliza el siguiente comando:

```bash
nmap --script ruta_del_script.nse ip
```

Esto ejecutará el script personalizado, proporcionando información sobre los puertos abiertos en la dirección [[Dirección IP|IP]] especificada.


