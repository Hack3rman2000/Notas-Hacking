___

- Tags: #herramientas #reconocimiento #wfuzz #fuzzing 
# Definición 

**Wfuzz** es una herramienta avanzada de enumeración que permite realizar búsquedas exhaustivas de directorios, archivos y parámetros en un sitio web. Utiliza listas proporcionadas y criterios específicos para ocultar o mostrar resultados basados en códigos de estado, cantidad de líneas, o extensiones de archivo.

# Comandos Wfuzz:

```bash 
wfuzz -c -t hilos -w wordlist url/FUZZ --sc/hc(muestra/oculta)=codigos_de_estado_a_ocultar
```

Este comando utiliza **Wfuzz** para enumerar directorios o archivos en una URL proporcionada. Los códigos de estado especificados con `--sc` u `--hc` determinan si se muestran o ocultan los resultados.

```bash 
wfuzz -c -t hilos -w wordlist --sl/hl=total_de_lineas -w wordlist dominio/FUZZ
```

Este comando controla la visibilidad de **endpoints** en función del total de líneas. Los parámetros `--sl` y `--hl` permiten mostrar u ocultar los resultados según la cantidad de líneas en la respuesta.

```bash 
wfuzz -c -t hilos -w wordlist -z list,html-php-txt dominio/FUZZ.FUZ2Z
    ```

Busca archivos con extensiones específicas proporcionadas por la lista `-z`. En este ejemplo, se busca archivos con extensiones html, php y txt en el dominio con la estructura FUZZ.FUZ2Z.

```bash 
wfuzz -c -t hilos -z range,num1-num2 dominio/parametro=FUZZ
```

Busca productos o elementos en un dominio que tengan el parámetro especificado entre el rango del num1 y num2. Este comando es útil para buscar elementos específicos en un sitio web.