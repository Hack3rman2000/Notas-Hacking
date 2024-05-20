# Vulnerabilidad de Deserialización YAML en Python (DES-YAML)

La deserialización YAML es una funcionalidad común en aplicaciones Python que permite convertir datos serializados en formato YAML en objetos Python. Sin embargo, esta característica puede ser explotada por atacantes para ejecutar código malicioso o realizar acciones no deseadas, lo que lleva a lo que se conoce como Ataque de Deserialización YAML (DES-YAML).

## Descripción del Ataque

El Ataque de Deserialización YAML (DES-YAML) aprovecha la función de deserialización YAML de una aplicación para ejecutar código no autorizado. Este ataque puede ser especialmente peligroso cuando se permite la entrada de datos no confiables, ya que el código YAML malicioso puede ser utilizado para ejecutar operaciones indeseadas en el sistema.

## Ejemplo de Código Vulnerable

Consideremos el siguiente código Flask que muestra una vulnerabilidad de deserialización YAML:

```python
from flask import Flask, render_template, redirect
import yaml
import base64

app = Flask(__name__, static_url_path='/static', static_folder='static')
app.config['DEBUG'] = True

@app.route("/")
def start():
    return redirect("/information/eWFtbDogVGhlIGluZm9ybWF0aW9uIHBhZ2UgaXMgc3RpbGwgdW5kZXIgY29uc3RydWN0aW9uLCB1cGRhdGVzIGNvbWluZyBzb29uIQ==", code=302)

@app.route("/information/<input>", methods=['GET'])
def deserialization(input):
    try:
        if not input:
            return render_template("information/index.html")
        yaml_file = base64.b64decode(input)
        content = yaml.load(yaml_file)
    except:
        content = "The application was unable to deserialize the object!"
    return render_template("index.html", content = content['yaml'])

@app.errorhandler(404)
def page_not_found(e):
    return render_template("404.html")

if __name__ == "__main__":
    app.run(host='0.0.0.0')
```

En este código, la ruta `/information/<input>` toma un parámetro de entrada que espera ser una cadena codificada en base64 que contiene datos YAML. Luego, el código intenta deserializar estos datos YAML utilizando la función `yaml.load()`. Sin embargo, esta operación de deserialización no está protegida contra ataques maliciosos, lo que podría permitir la ejecución de código no deseado.

## Ejemplo de Ataque

Un atacante podría aprovechar esta vulnerabilidad para ejecutar comandos del sistema operativo utilizando el siguiente código YAML malicioso:

```yaml
!!python/object/apply:os.system ["whoami"]
```

Para llevar a cabo este ataque, el código YAML malicioso debe ser codificado en base64 y luego ser incluido en la URL de la siguiente manera:

```plaintext
/information/eWFtbDogISFweXRob24vb2JqZWN0L2FwcGx5Om9zLnN5c3RlbSBbIndob2FtaSJd
```

## Prevención

Para prevenir el Ataque de Deserialización YAML, es crucial implementar medidas de seguridad adecuadas:

- **Validación de Entrada:** Validar y sanitizar todos los datos de entrada antes de deserializarlos para evitar la ejecución de código malicioso.
  
- **Utilizar Funciones Seguras:** En lugar de usar funciones de deserialización YAML peligrosas como `yaml.load()`, se recomienda utilizar funciones más seguras como `yaml.safe_load()` que solo permiten la deserialización de tipos de datos simples.

- **Limitar Privilegios:** Ejecutar la aplicación con los privilegios mínimos necesarios para limitar el impacto en caso de una explotación exitosa.

Al seguir estas mejores prácticas, los desarrolladores pueden mitigar el riesgo de deserialización YAML maliciosa y proteger sus aplicaciones contra este tipo de ataques.


La deserialización YAML en Python es un proceso vulnerable que convierte datos YAML en objetos Python. Para obtener más información sobre esta vulnerabilidad, pueden acceder a los siguientes enlaces: [PKMurphy - Is it YAML?](https://www.pkmurphy.com.au/isityaml/) y [HackTricks - Deserialización Python YAML](https://book.hacktricks.xyz/v/es/pentesting-web/deserialization/python-yaml-deserialization).
