

Tenemos este ejemplo donde al momento de analizar la maquina podemos ver que podemos ejecutar un archivo con el comando python como el usuario prueba2


![[Pasted image 20240226202702.png]]

Entonces aprovechandonos de esto podemos ejeuctar ese archivo como prueba2 y sin contraseña pero al hacerlo solo vemos esto


![[Pasted image 20240226205821.png]]

Al analizar el archivo de python que se esta ejecutando podemos ver que el propietario es prueba2 y que solo tenemos permiso de lectura 

![[Pasted image 20240226204624.png]]

Para un usuario sin experiencia este codigo podria parecer normal y no presentar ningun riesgo pero es aqui cuando nos podemos aprovechar del python libray hijaking primero para dejarlo claro pdoemos ver como python busca y carga las bibliotecas ejecutando el siguiente comando


```bash
python3 -c 'import sys; print(sys.path)'
```

Ahora si ejecutamos el comando podemos ver las rutas de las librerias que va cargando 

![[Pasted image 20240226211300.png]]

En este caso la libreria qye esta utilizando el archivo python es hashlib la cual se encuentra la siguiente ruta

![[Pasted image 20240226211805.png]]

Entonces esto significa que va buscando de ruta en ruta desde la que tiene mayor prioridad hasta la que tiene menos y usa la primera libreria que encuentre en este caso esta utilizando la de ruta de /usr/lib/python3.10 ya que solo en esta se encuentra esa libreria de hashlib entonces el riesgo aqui es que al ver de nuevo las rutas de donde se importan las librerias esta este campo vacio el cual hace alucion al directorio actual de trabajo el cual tiene mas prioridad que las demas rutas

![[Pasted image 20240226213610.png]]

Entonces teniendo en cuenta el concepto de python library hijacking podriamos crear un archivo hashlib.py en el directorio actual el cual contenga lo siguiente y asi tratar de obtener una bash como prueba2

![[Pasted image 20240226214613.png]]

Ahora al ejecutar con python el archivo example.py como el usuario prueba2 podemos ver que nos  nos da una bash como el usuario prueba2


![[Pasted image 20240226215111.png]]

Aislado a este ejemplo puede que se presente un caso en el que la libreria en cuestion este en un directorio con menos prioridad pero tengas capacidad de escritura en un directorio con mas prioridad entonces podrias crear una libreria en el directorio con mas prioridad y asi tratar lanzarte una bash o hacer algo malicioso







# Python Library Hijacking

El Python Library Hijacking es una técnica de manipulación utilizada en entornos Python que se aprovecha de la forma en que Python busca y carga las bibliotecas durante la ejecución de un script. Cuando un script Python utiliza una biblioteca, el intérprete de Python busca la biblioteca en una serie de rutas predefinidas, y utiliza la primera versión de la biblioteca que encuentra. Esto puede ser explotado por un atacante para substituir o manipular una biblioteca utilizada por un script, llevando a la ejecución de código malicioso o a la escalada de privilegios.

En este ejemplo, al analizar la máquina, podemos observar que podemos ejecutar un archivo con el comando python como el usuario "prueba2".

![[Pasted image 20240226202702.png]]

Aprovechándonos de esto, ejecutamos ese archivo como "prueba2" y sin contraseña, pero al hacerlo, solo vemos una salida limitada.

![[Pasted image 20240226205821.png]]

Al analizar el archivo de Python que se está ejecutando, podemos ver que el propietario es "prueba2" y que solo tenemos permiso de lectura.

![[Pasted image 20240226204624.png]]

Para un usuario sin experiencia, este código podría parecer normal y no presentar ningún riesgo, pero es aquí cuando nos podemos aprovechar del "Python Library Hijacking". Para clarificar, podemos ver cómo Python busca y carga las bibliotecas ejecutando el siguiente comando:

```bash
python3 -c 'import sys; print(sys.path)'
```

Al ejecutar este comando, podemos ver las rutas de las bibliotecas que va cargando.

![[Pasted image 20240226211300.png]]

En este caso, la biblioteca que está utilizando el archivo Python es "hashlib", la cual se encuentra en la siguiente ruta.

![[Pasted image 20240226211805.png]]

Esto significa que va buscando de ruta en ruta desde la que tiene mayor prioridad hasta la que tiene menos, y usa la primera biblioteca que encuentre. En este caso, está utilizando la de la ruta `/usr/lib/python3.10`, ya que solo en esta se encuentra la biblioteca "hashlib". El riesgo aquí es que, al ver de nuevo las rutas de donde se importan las bibliotecas se encuentra este campo esté vacío, lo que hace alusión al directorio actual de trabajo, el cual tiene más prioridad que las demás rutas.

![[Pasted image 20240226213610.png]]

Entonces, teniendo en cuenta el concepto de "Python Library Hijacking", podríamos crear un archivo `hashlib.py` en el directorio actual, el cual contenga lo siguiente, y así tratar de obtener una bash como "prueba2".

![[Pasted image 20240226214613.png]]

Ahora, al ejecutar con Python el archivo `example.py` como el usuario "prueba2", podemos ver que nos da una bash como el usuario "prueba2".

![[Pasted image 20240226215111.png]]

Aislado a este ejemplo, puede que se presente un caso en el que la biblioteca en cuestión esté en un directorio con menos prioridad, pero tengas capacidad de escritura en un directorio con más prioridad. Entonces, podrías crear una biblioteca en el directorio con más prioridad y así intentar lanzarte una bash o hacer algo malicioso.
