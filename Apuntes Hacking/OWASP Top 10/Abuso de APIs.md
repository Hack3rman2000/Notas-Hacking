![[Pasted image 20240214213419.png]]

Si inspeccionamos la pagina nos metemos a redes y filtramos por xhr podremos las peticiones que se hacen en esto caso seleccionaremos las del login 


![[Pasted image 20240214214015.png]]

Al ver los encabezados podems ver que se hace una peticion por post a una api en este caso se mandan las credenciales de inicio de sesion en formato json 

![[Pasted image 20240214214309.png]]


Si estas credenciales son validas nos dara un token en formato JWT como respuesta 

![[Pasted image 20240214214434.png]]

#  Usando postman para la enumeracion de endpoints de la api

Creamos una nueva coleccion y le asignamos un nuevo nombre

![[Pasted image 20240214222423.png]]

![[Pasted image 20240214222544.png]]

Ahora creamos una nueva peticion http haciendo lo siguiente: 

![[Pasted image 20240215195631.png]]

![[Pasted image 20240215195659.png]]

Para registrar un endopoint de la api primero tenemos que ingresar la url donde se hace la peticion en este caso sera el login tambien tendremos que seleccionar el metodo finalmente mandarlos: 

![[Pasted image 20240215200122.png]]

Esto no funciona ya que no se le estan mandando datos al endpoint de login en este caso este necesita las credenciales del usuario paro esto copiamos las credenciales en formato json nos vamos a la parte de body seleccionamos raw y seleccionamos la opcion de JSON y lo enviamos

![[Pasted image 20240215202109.png]]


Y listo ahora como respuesta nos da devuelve un token: 

![[Pasted image 20240215202439.png]]

Ahora lo podemos guardar dandole al siguiente boton: 
![[Pasted image 20240215202523.png]]

Lo nombramos con un nombre descriptivo, seleccionamos una coleccion y lo guardamos: 
![[Pasted image 20240215202704.png]]

Y listo ahora tendriamos representado nuestro endpoint de login: 

![[Pasted image 20240215202756.png]]

Al hacer el mismo proceso pero para añadir el endpoint de dashboard nos dara un error ya que no se le ha pasado la cabecera del token:


![[Pasted image 20240215210515.png]]

Para evitar este error tenemos que trabajar con variables para eso tenemos que irnos a variables y crear una nueva variable para el token que devuelve el login y la guardamos:

![[Pasted image 20240215211332.png]]


Finalmente definimos el tipo de la variable en este caso bearer token y en el token indicamos la variable

![[Pasted image 20240215212517.png]]

Y listo ya nos responde correctamente el endpoint y  ahora en todas la solicitudes que se tramiten se arrastrara la cabecera del token: 

![[Pasted image 20240215213617.png]]


Ahora añadiremos algunos enpoints haciendo el mismo proceso:


![[Pasted image 20240216091203.png]]

Este es el endpoint de los productos el cual es por metodo get si por alguna razon al momento de enviarlo da un error es porque no se a usado el token para eso tenemos que guardarlo y luego ya se enviara correctamente


Ahora igualmente hacemos el mismo proceso y añadimos un nuevo en point con la excepcion de que le tenemos que pasar algunos datos por post

![[Pasted image 20240216091902.png]]

Y finalmente añadiremos un enpoint por metodo post haciendo el mismo proceso: 

![[Pasted image 20240216092421.png]]


# Fuerza bruta para cambiar la contraseña


Al ver como se cambia la contraseña se puede ver que se manda los datos ingresados a un endpoint que verifica si es correcto el otp 

![[Pasted image 20240216100944.png]]

![[Pasted image 20240216101121.png]]

Al parecer el codigo otp se conforma de 4 digitos asi que probablemente se pueda hacer un ataque de fuerza bruta y asi cambiar la contraseña pero antes representaremos el endpoint en postman

Haremos el mismo proceso mandando los datos en formato json por metodo post y listo ahora quedaria representado ese endpoint

![[Pasted image 20240216101803.png]]

Ahora ya podemos hacer el ataque de fuerza bruta con ayuda de fuff


```bash
ffuf -u http://localhost:8888/identity/api/auth/v3/check-otp -w /usr/share/seclists/Fuzzing/4-digits-0000-9999.txt -X POST -d '{"email":"juan@juan.com","otp":"FUZZ","password":"Hola1234#"}' -H "Content-Type: application/json" -p 1 -mc 200
```


Con este comando tratamos de hacer fuerza bruta probando todos los numeros posibles con 4 digitos y estos los mandamos al endpoint que verifica el otp por metodo post 


Lamentablemente esto no va a funcionar ya que al llegar a un numero intentos fallidos ya no dejara ingresar mas datos:


![[Pasted image 20240216131858.png]]

Al verificar la url al parecer estan usando la version 3 de la api pero quizas exista una version mas desactualizada la cual no limite el envio de datos y si asi es al mandar datos pero en este caso  la version 2 al parecer no tiene ningun limite de solicitudes y al mandar una nueva solicitud todo vuelve a funcionar

![[Pasted image 20240216133936.png]]

Ahora solo renovamos el codigo opt y ejecutamos el mismo comando pero usando la version 2

```bash
ffuf -u http://localhost:8888/identity/api/auth/v2/check-otp -w /usr/share/seclists/Fuzzing/4-digits-0000-9999.txt -X POST -d '{"email":"juan@juan.com","otp":"FUZZ","password":"Hola1234#"}' -H "Content-Type: application/json" -p 1 -mc 200
```

Y si algun numero nos da un codigo de estado 200 entonces ese sera el opt y cambiara la contraseña en este caso ha encontrado este: 


![[Pasted image 20240216150856.png]]

y listo ahora podemos inciar sesion con la nueva contraseña


# Cambiar metodos en una api

```bash
ffuf -u http://localhost:8888/workshop/api/shop/products -w /usr/share/seclists/Fuzzing/http-request-methods.txt -X FUZZ -p 1 -mc 401,200
```

Con este comando puedes saber cuales metodos se puedes usar en un enpoint en este caso al momento de hacerlo a un enpoint especifico nos muestra que se pueden utilizar los siguientes metodos

![[Pasted image 20240216154957.png]]

Un metodo interesante es el de OPTIONS el cual al utilizarlo en postman y al ver los header de la respuesta no muestra los metodos que tambien estan disponibles:

![[Pasted image 20240216155207.png]]

Ahora a modo de prueba trataremos de cambiar el metodo a post :

![[Pasted image 20240216155906.png]]

Al parecer requiere varios datos entonces se los ingresamos en formato json y lo volvemos a enviar 

![[Pasted image 20240216161740.png]]

Cuando entramos accedemos al endpoint podemos ver que se ha añadido un nuevo producto con los datos que introducimos


![[Pasted image 20240216161920.png]]

Y al momento de comparlo ya que tiene un precio negativo nos aprovechamos para tener mas dinero en nuestra cuenta:

![[Pasted image 20240216162039.png]]


# Inyeccion NoSQL con api


Al investigar un poco mas nos damos cuenta que podemos ingresar un cupon y al momento de insepccionar nos damos cuenta que lo manda a un enpoint que manda el cupon por post y aqui se valida el cupon: 

![[Pasted image 20240216164211.png]]

![[Pasted image 20240216164257.png]]

Ahora esto lo enumeramos con ayuda de postman repitiendo el metodo que hemos utilizado:


![[Pasted image 20240216173147.png]]

Al momento de enviar un cupon que no es valido no nos devuelve nada pero al parecer esto tiene una vulnerbilidad ya que esta hecho con mongo db y al parecer esta funcionalidad es vulnerable a inyeccion nosql para poder vulnerarla podemos hacer una inyeccion nosql con condiciones como a continuacion que buscamos cupones los cuales no sean igual a 1 y los cupones que no sea iguales no sean iguales se mostraran y podremos aprovecharnos de esto para ganar dinero facil 


![[Pasted image 20240216173638.png]]

Asi es como gracias a esta inyeccion nosql nos da un cupon ahora solo seria probarlo


![[Pasted image 20240216173834.png]]

Excelente el cupon funciona


# Abusando de los BOLA


Al inspeccionar mas el sitio podemos ver que se puede añadir un vehículo:

![[Pasted image 20240216175743.png]]

Asi que lo añadimos con las credenciales que nos mandan a nuestro correo:

![[Pasted image 20240216180011.png]]

Ya añadido nos podemos fijar que existe un boton que refresca la ubicacion el cual hace una solicitud por el metodo get a un endpoint el cual da como respuesta la ubicacion de vehiculo y algunos datos del dueño del vehiculo esto podria tener una vulnerabilidad pero primero vamos a enumerar con postman:

![[Pasted image 20240216180652.png]]

![[Pasted image 20240216180911.png]]


Ahora para enumerar este endpoint tendremos que hacer el mismo proceso que ya hemos hecho antes en postman y nos tendria que dar el mismo resultado que al usar la pestaña de red en el navegador:


![[Pasted image 20240216181310.png]]


Investigando un poco mas el sitio podemos ver que hay una seccion para la comunidad donde diferentes usuarios postean difrenetes cosas

![[Pasted image 20240216181657.png]]

Al inspeccionar un poco podemos ver que al ver al darle click al comentario la api muestra mas informacion de la necesaria y nos esta mostrando datos como el correo o la id del vehiculo del usuario:


![[Pasted image 20240216182113.png]]


Ahora desde postman podemos usar este identificador para saber la ubiciacion de ese vehiculo 


![[Pasted image 20240216182345.png]]


Perfecto ahora sabemos la ubicacion de un vehiculo de otro usuario y hemos contemplado el BOLA

