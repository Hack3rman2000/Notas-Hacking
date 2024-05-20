___

- Tags: #redes #reconocimiento #tcp #herramientas #protocolos 

___

```bash 
route -n
```

El comando `route -n` es una herramienta fundamental en sistemas Unix para gestionar la tabla de enrutamiento. Proporciona una visión detallada de las rutas de red, las puertas de enlace y otros detalles relevantes para optimizar la conectividad y solucionar problemas en una red.

```bash 
tcpdump -i interfaz -w archivo -v
```

El comando `tcpdump` es una potente herramienta de captura de paquetes que permite analizar el tráfico de red en tiempo real. Al agregar opciones específicas como `-i` para seleccionar una interfaz, `-w` para guardar la captura en un archivo y `-v` para aumentar el nivel de detalle, se obtiene una capacidad aún mayor. Ejecutando:

```bash
tcpdump -i interfaz -w archivo -v
```

Este comando realiza una captura de tráfico [[TCP]] en la interfaz especificada y guarda la información en un archivo para un análisis posterior. Las opciones detalladas (`-v`) proporcionan una visión exhaustiva del tráfico, siendo útiles para la solución de problemas y la comprensión profunda de las comunicaciones en red.

```bash
curl -s -X metodo "data_a_mandar" URL
```

Se utiliza para realizar solicitudes [[HTTP]], enviar datos al servidor utilizando un método específico y hacerlo en modo silencioso, sin mostrar información adicional.


```bash 
curl -s -X POST direccion -d@archivo
```

La instrucción es útil para enviar archivos a través del método POST. Este comando puede ser utilizado en situaciones donde se necesite interactuar con un servidor, por ejemplo, para realizar pruebas de carga o enviar datos a una aplicación web.
