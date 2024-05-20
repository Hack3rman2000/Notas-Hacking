____

- Tags: #reconocimiento #herramientas #explotación #enumeración #burpsuite #http #https 

____
# Definición

**BurpSuite** es una herramienta de prueba de penetración ampliamente reconocida y utilizada en la industria de la seguridad informática. Compuesta por diversas herramientas, **BurpSuite** permite a los profesionales identificar y corregir vulnerabilidades de seguridad en aplicaciones web. A continuación, se describen las principales herramientas que componen esta suite y sus funciones:

___
# Proxy: Intercepción y Modificación del Tráfico

La herramienta principal de **BurpSuite**, el **Proxy**, actúa como intermediario entre el navegador web y el servidor. Permite la interceptación y modificación de solicitudes y respuestas [[HTTP]] y [[HTTPS]]. Esta funcionalidad es esencial para la identificación de vulnerabilidades, ya que facilita el análisis detallado del tráfico.

___
# Scanner: Identificación Automatizada de Vulnerabilidades

El **Scanner** de **BurpSuite** es una herramienta automatizada que utiliza técnicas avanzadas de exploración para identificar vulnerabilidades en aplicaciones web. Puede detectar amenazas como [[Inyección SQL|inyecciones SQL]], cross-site scripting ([[XSS]]) y vulnerabilidades de seguridad de la capa de aplicación ([[OWASP Top 10]]).

___
# Repeater: Repetición de Solicitudes para Pruebas Detalladas

**Repeater** permite a los usuarios reenviar y repetir solicitudes [[HTTP]] y [[HTTPS]], facilitando la prueba de diferentes entradas y la verificación de las respuestas del servidor. Esta herramienta es útil tanto para pruebas manuales como para la identificación de vulnerabilidades.

___
# Intruder: Automatización de Ataques de Fuerza Bruta

**Intruder** es una herramienta potente para la automatización de ataques de fuerza bruta. Permite definir diferentes [[Payload Staged|payloads]] para distintas partes de la solicitud, como la URL o el cuerpo de la solicitud. La automatización de solicitudes con diferentes [[Payload Non-Staged|payloads]] facilita la identificación de posibles vulnerabilidades.

___
# Comparer: Análisis de Diferencias entre Solicitudes

**Comparer** se utiliza para comparar dos solicitudes [[HTTP]] o [[HTTPS]], detectando diferencias significativas en las respuestas. Esta herramienta es valiosa para analizar la seguridad de la aplicación y identificar posibles debilidades.

**BurpSuite**, en su conjunto, ofrece a los profesionales de seguridad informática una herramienta integral y poderosa. Con la capacidad de realizar pruebas automatizadas o manuales, los usuarios pueden identificar y corregir vulnerabilidades antes de que sean explotadas por posibles atacantes.

___
# Modo de uso:

![[Pasted image 20240111133620.png]]

Al hacer clic en los ajustes de **proxy**, se pueden ver las configuraciones en estas se muestra la dirección donde corre el **proxy** de **BurpSuite**.

![[Pasted image 20240111132117.png]]

Para empezar a interceptar una página web, debemos entrar al **intercepter** y darle a interceptar.

![[Pasted image 20240111133952.png]]

Si por alguna razón no se muestra la página correspondiente, tendrás que darle a **forward**.

Al hacer Ctrl + R en el **intercepter**, la petición se mandará al **repeater**. También puedes acceder a este desde la pestaña de arriba:

![[Pasted image 20240111134247.png]]


![[Pasted image 20240111134338.png]]

En este, puedes gestionar tus peticiones organizándolas por pestañas.

![[Pasted image 20240111134537.png]]

En la pestaña de **target**, existe una opción que se llama **site map** que sirve para ver todo el mapa de un sitio. Muchas veces te aparecen sitios que no están relacionados directamente con el sitio. Para eso, tenemos que irnos a **opciones de proxy** y desmarcar esta casilla:

![[Pasted image 20240111134846.png]]

Y luego definimos el **scope** en la pestaña de **target**:

![[Pasted image 20240111135042.png]]

De esta forma, solo mostramos los sitios que estamos auditando.

En la pestaña de **proxy** hay una opción para ver el historial de todo lo que se ha interceptado:

![[Pasted image 20240111135448.png]]

Si quieres ver la respuesta directamente en el **intercepter**, puedes dar clic derecho en la petición interceptada, luego le das clic a "**do intercept**," le das a "**response to this request**," y finalmente le das a "**forward**," y verás la respuesta directamente en el **intercepter** sin necesidad del **repeater**.

![[Pasted image 20240111173132.png]]

Con esta configuración, siempre tendrás que realizar la misma selección anterior. Para evitar eso, tendrás que entrar a las **opciones del proxy** y darle al check a la siguiente opción:

![[Pasted image 20240111175400.png]]

Si quieres modificar algo en la respuesta, solo tienes que entrar a las **opciones del proxy** y darle a añadir e indicar si el texto a modificar es en la respuesta o en la petición. Claramente, indicas el texto a modificar y por el que será reemplazado:

![[Pasted image 20240111175753.png]]

Si quieres que una petición no vaya al servidor, simplemente presionas el botón de **drop** en la ventana del **intercepter**:

![[Pasted image 20240111175952.png]]

Si quieres filtrar en el historial, solo tendrás que presionar en el siguiente botón y te mostrará una ventana para buscar más personalizadamente en el historial:

![[Pasted image 20240111180455.png]] 

*(Esta opción es de pago)*

Al estar en **intercepter** y presionar Ctrl + I, mandarás la petición al **intruder**:

![[Pasted image 20240111182533.png]]

En este, podemos hacer diferentes ataques, tal es el caso del ataque de **sniper** donde podemos usar un diccionario y alguna palabra en la petición a remplazar para así poder acceder a alguna parte.

Ahora seleccionamos el texto que deseamos remplazar en la respuesta y le damos al botón **add** (puedes seleccionar diferentes partes del texto):

![[Pasted image 20240111182937.png]]

Ahora nos dirigimos a la pestaña de [[Payload Staged|payloads]] y cargamos la wordlist después iniciamos el ataque:

![[Pasted image 20240111183209.png]]

Y ahora comenzará el ataque:

![[Pasted image 20240111183318.png]]

Si no quieres que los datos a enviar no sean codificados, solo deseleccionas esta opción:

![[Pasted image 20240111185453.png]]


Si quieres ver en qué momento dejas de recibir una respuesta, puedes utilizar la opción de **grep extract** para así seleccionar una parte de la respuesta y veas cuando esa parte de la respuesta sea diferente. Solo te tienes que ir a los **settings de intruder**, luego añadir una nueva configuración en **grep extract** donde recargarás primero la respuesta y luego seleccionarás la parte de la respuesta que quieres monitorear para finalmente darle a a **ok** y comenzar el ataque:

![[Pasted image 20240111190146.png]]

Y mostraría algo así:

![[Pasted image 20240111190312.png]]

También existe otro tipo de ataque que es el **cluster bomb** el cual sirve para modificar más de una parte de la petición y así poder cargar dos [[Payload Staged|payloads]] al mismo tiempo:

![[Pasted image 20240111193045.png]]

Solo se tiene que seleccionar el [[Payload Staged|payload]] 2 y seleccionar el tipo de [[Payload Staged|payload]]; en este caso, **simple list**, y finalmente iniciar el ataque:

![[Pasted image 20240111193259.png]]



En el **intercepter**, si seleccionas un texto y presionas Ctrl + U cifrarás en texto a URL encode y si tienes un texto codificado puedes seleccionarlo y presionar Ctrl + Shift + U y te mostrará el texto decifrado.

También existe la ventana **decoder** donde puedes cifrar o decifrar un texto en diferentes cifrados ofrecidos:

![[Pasted image 20240111184601.png]]

En el **intercepter** tenemos una ventana la cual muestra algunos datos importantes:

![[Pasted image 20240111185128.png]]


También existe el ataque de **battering ram**:

![[Pasted image 20240111194310.png]]

En este, al seleccionar diferentes partes de la petición, solo dejará usar un [[Payload Staged|payload]] y este [[Payload Staged|payload]]  lo utilizará en todos los campos seleccionados; en este caso, será el [[Payload Staged|payload]]  de **simple list**:

![[Pasted image 20240111194531.png]]

Finalmente, existe el ataque de **pitchfork** que hace que el [[Payload Staged|payload]]  del primer campo seleccionado se sincronice con el segundo campo seleccionado:

![[Pasted image 20240111195521.png]]

En este caso, se sincronizará la primera línea con la primera línea de cada [[Payload Staged|payload]] ; en este caso, los dos son **simple list**:

![[Pasted image 20240111195717.png]]

También existe la herramienta **comparer**, pero para eso primero tenemos que seleccionar en el **http history** dos peticiones o dos respuestas y mandarlas al **comparer**. Ya en el **comparer**, podemos comparar por palabras o bytes:

![[Pasted image 20240111200724.png]]

Y este sería el resultado:

![[Pasted image 20240111200821.png]]

También existe la pestaña de **extensiones** la cual muestra algunas extensiones que se le puede instalar al **BurpSuite**:

![[Pasted image 20240111204003.png]]

___

En resumen, **BurpSuite** se presenta como una herramienta indispensable para cualquier experto en seguridad informática que busque garantizar la protección de aplicaciones web. En la siguiente sección, exploraremos en detalle el uso de **BurpSuite** para aprovechar al máximo sus capacidades.