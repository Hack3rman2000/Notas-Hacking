___

- Tags: #fuzzing #herramientas #ffuf #reconocimiento 

___
# Descripción 

**Ffuf** es una herramienta versátil de enumeración que permite buscar y descubrir directorios en un dominio específico. Con capacidades personalizables, es posible ajustar sus parámetros para obtener resultados específicos. En este caso, se busca un directorio presente en una wordlist solo si la página responde con el código de estado 200.

___
# Comando Ffuf

```bash 
ffuf -c -t 200 -w wordlist -u dominio/FUZZ -mc=200
```

- `bash -c`: Habilita la búsqueda recursiva.
- `-t 200`: Establece el número de hilos en 200 para una exploración rápida.
- `-w wordlist`: Especifica la wordlist que contiene los posibles directorios.
- `-u dominio/FUZZ`: Define la URL con el marcador de posición FUZZ que será reemplazado con elementos de la wordlist.
- `-mc=200`: Muestra solo los resultados donde la página responde con el código de estado 200.