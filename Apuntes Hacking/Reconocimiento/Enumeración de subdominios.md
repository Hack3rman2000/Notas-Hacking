___

- Tags: #reconocimiento #herramientas #ipv4 #fuzzing #wfuzz #gobuster

___
# Enumeración de Subdominios con Herramientas

 ```bash 
 gobuster vhost -u ip -w wordlist -t número_de_hilos
 ```

El comando `gobuster` con la opción `vhost` es una herramienta eficaz para la enumeración de subdominios. Al especificar la dirección [[Dirección IP|IP]] (`-u`), una wordlist (`-w`) y el número de hilos (`-t`), se realiza un escaneo para descubrir subdominios asociados al dominio objetivo.

 ```bash
 wfuzz -c --hc=código_de_estado_a_ocultar -t hilos -w wordlist -H "Host: FUZZ.dominio"
 ```

El comando `wfuzz` es otra herramienta útil para encontrar subdominios. Al especificar parámetros como el código de estado a ocultar (`--hc`), el número de hilos (`-t`), una wordlist (`-w`), y utilizando la opción `-H` para manipular el encabezado **Host**, se realiza una búsqueda exhaustiva de subdominios en el dominio especificado.