

Para añadir una capabilitie podemos ejecutar el siguiente comando

```bash
setcap <capacidad>=<opciones> <ruta_al_binario>
```

Donde:
- `<capacidad>` es el nombre de la capacidad que deseas agregar al binario.
- `<opciones>` son las opciones que indican cómo se aplicará la capacidad. Estas opciones pueden incluir `e` (efectiva), `i` (heredable) y `p` (permitida).
- `<ruta_al_binario>` es la ruta al binario al cual deseas añadir la capacidad.

Mientras que para eliminar una capabilite podemos usar el siguiente comando

```bash
setcap -r ruta_del_binario
```


Algo que podemos empezar a hacer es listar las capabiliteis existentes para binario que estan corriendo en el sistema los pid de los procesos los podemos ver en el directorio /proc/ como se ve a continuacion

![[Pasted image 20240227111019.png]]

Podemos ver que hay muy pocos ahora nos metemos a cualquiera y le hacemos un cat al recurso status

![[Pasted image 20240227111249.png]]

Este recurso tiene mucho contenido pero si filtramos pro Cap podemos ver los siguiente

![[Pasted image 20240227111415.png]]

Si ahora te copias el valor de uno y lo colocamos en el siguiente comando podremos ver las capabilites disponibles

![[Pasted image 20240227111855.png]]


Entonces ahora veremos un ejemplo de una maquina asi que enumeramos las capabilities con el siguient comando


```bash
getcap -r / 2> /dev/null
```

Este comando sirve para enumerar todas la capabilites del sistema

Al ejecutar este comando en la maquina podemos ver que el binario tcpdump las siguientes capabilites

![[Pasted image 20240227115625.png]]

Estas capabilities permiten que cualquier usuario del sistema pueda ejecutar el tcdump correctamente

Otra forma forma de poder ver las que capabilites esta utilizando un prooceso determinado es usando el siguiente comando 


```bash
getcaps pid
```

Este comando te mostra las capabiliteis que esta uliziando un proceso con solo pasarle su pid


Otro ejemplo el cual puede ser aun mas peligroso tenemos una maquina en el cual se hizo una enumeracion pero no se encontro nada pero al hacer el anterior comando para asi enumerar todas las capabilites existentes podemos ver lo sigueinte 

![[Pasted image 20240227123617.png]]

Al parecer el binario de python3 tiene la capabilitie del setuid por lo tanto podemos modificar nuestro identificador de usuario con ayuda de python de la siguiente manera y asi podernos lanzar una bash como usuario root

![[Pasted image 20240227124202.png]]

Listo asi es como nos podemos aprovechar de esta capabilite para escalar privilegios


Tambien podemos ver los binario que son suseptibles a escalada de privilegios por medio de capabilies con ayuda de https://gtfobins.github.io/

![[Pasted image 20240227124631.png]]

Como ejemplo final tenemos una maquina que al momento de enumerar las capabilities podemos ver que vim tiene la capabilitie de setuid

![[Pasted image 20240227130056.png]]

Al revisar en https://gtfobins.github.io/ para ver si hay una forma de aprovecharnos de esta capabilitie nos damos cuenta de que si se puede abusar de esta

![[Pasted image 20240227130221.png]]


Por lo que ahora insertamos el comando  que mencionan en el sitio y al ejecutarlo nos hemos convertido en root
![[Pasted image 20240227130510.png]]

![[Pasted image 20240227130528.png]]

Perfecto estos han sido algunos ejemplos de como aprovecharnos de las capabilities para elevar privilegios



# Detección y Explotación de Capabilities

Las capabilities en sistemas operativos tipo Unix son atributos de seguridad que permiten a un proceso realizar operaciones privilegiadas sin necesidad de tener todos los privilegios de root. Estas capacidades están diseñadas para proporcionar un control más granular sobre los permisos de los procesos en comparación con el modelo tradicional de permisos binarios de Unix. Sin embargo, a veces pueden ser mal implementadas o configuradas de manera incorrecta, lo que puede llevar a situaciones de vulnerabilidad que pueden ser explotadas por atacantes para elevar sus privilegios.

Para añadir una capability podemos ejecutar el siguiente comando:

```bash
setcap <capacidad>=<opciones> <ruta_al_binario>
```

Donde:
- `<capacidad>` es el nombre de la capability que deseas agregar al binario.
- `<opciones>` son las opciones que indican cómo se aplicará la capability. Estas opciones pueden incluir `e` (efectiva), `i` (heredable) y `p` (permitida).
- `<ruta_al_binario>` es la ruta al binario al cual deseas añadir la capability.

Mientras que para eliminar una capability podemos usar el siguiente comando:

```bash
setcap -r ruta_del_binario
```

Algo que podemos hacer es listar las capabilities existentes para binarios que están corriendo en el sistema. Los PID de los procesos los podemos ver en el directorio `/proc/`, como se ve a continuación:

![[Pasted image 20240227111019.png]]

Podemos ver que hay muy pocos. Ahora nos metemos a cualquiera y le hacemos un `cat` al recurso `status`:

![[Pasted image 20240227111249.png]]

Este recurso tiene mucho contenido, pero si filtramos por `Cap`, podemos ver lo siguiente:

![[Pasted image 20240227111415.png]]

Si ahora copiamos el valor de uno y lo colocamos en el siguiente comando, podremos ver las capabilities disponibles:

![[Pasted image 20240227111855.png]]

Entonces, veremos un ejemplo de una máquina donde enumeramos las capabilities con el siguiente comando:

```bash
getcap -r / 2>/dev/null
```

Este comando sirve para enumerar todas las capabilities del sistema.

Al ejecutar este comando en la máquina, podemos ver que el binario `tcpdump` tiene las siguientes capabilities:

![[Pasted image 20240227115625.png]]

Estas capabilities permiten que cualquier usuario del sistema pueda ejecutar el `tcpdump` correctamente.

Otra forma de ver las capabilities que está utilizando un proceso determinado es usando el siguiente comando:

```bash
getcaps pid
```

Este comando muestra las capabilities que está utilizando un proceso con solo pasarle su PID.

Otro ejemplo, el cual puede ser aún más peligroso, es una máquina en la cual se hizo una enumeración pero no se encontró nada. Sin embargo, al hacer el anterior comando para así enumerar todas las capabilities existentes, podemos ver lo siguiente:

![[Pasted image 20240227123617.png]]

Al parecer, el binario de `python3` tiene la capability del `setuid`. Por lo tanto, podemos modificar nuestro identificador de usuario con ayuda de Python de la siguiente manera y así podernos lanzar una bash como usuario root:

![[Pasted image 20240227124202.png]]

Listo, así es como nos podemos aprovechar de esta capability para escalar privilegios.

También podemos ver los binarios que son susceptibles a escalada de privilegios por medio de capabilities con ayuda de [GTFOBins](https://gtfobins.github.io/):

![[Pasted image 20240227124631.png]]

Como ejemplo final, tenemos una máquina en la que al momento de enumerar las capabilities podemos ver que `vim` tiene la capability de `setuid`:

![[Pasted image 20240227130056.png]]

Al revisar en [GTFOBins](https://gtfobins.github.io/) para ver si hay una forma de aprovecharnos de esta capability, nos damos cuenta de que sí se puede abusar de esta:

![[Pasted image 20240227130221.png]]

Por lo que ahora insertamos el comando que mencionan en el sitio y al ejecutarlo nos hemos convertido en root:

![[Pasted image 20240227130510.png]]
![[Pasted image 20240227130528.png]]

Perfecto, estos han sido algunos ejemplos de cómo aprovecharnos de las capabilities para elevar privilegios.