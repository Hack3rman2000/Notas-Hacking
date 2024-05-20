___

- Tags: #reconocimiento #herramientas #gobuster 
# Definición 

**Gobuster** es una herramienta de enumeración de directorios ampliamente utilizada en el ámbito de seguridad informática y auditoría web. Su función principal es explorar un servidor web en busca de directorios y archivos, proporcionando información valiosa para identificar posibles puntos de entrada y vulnerabilidades.

# Comandos Gobuster:

```bash 
gobuster dir -u ip -w wordlist -t 200
```
    
Este comando utiliza **Gobuster** para escanear la dirección [[Dirección IP|IP]] especificada, empleando una wordlist para identificar directorios. La opción `-t` define el número de hilos, lo que agiliza el proceso de enumeración.
    
```bash 
gobuster dir -u ip -w wordlist --add-slash -b código_de_estado -x extensiones
```
    
Similar al anterior, este comando añade funcionalidades adicionales. La opción `--add-slash` evita mostrar directorios con un código de estado específico, y la opción `-x` permite buscar archivos con extensiones específicas.
    
```bash 
gobuster dir -u ip -w wordlist -s código_de_estado -b ''
```
    
Este comando amplía la funcionalidad al permitir la enumeración de directorios y archivos solo si el código de estado coincide con el especificado. Es útil para limitar la salida a elementos específicos basados en el código de estado.