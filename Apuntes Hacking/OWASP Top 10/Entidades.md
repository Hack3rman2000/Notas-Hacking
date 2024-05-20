____

- Tags: 

___
# Introducción

Las entidades en [[XML]] son elementos que representan datos o caracteres especiales dentro de un documento [[XML]]. En esta nota, exploraremos diferentes tipos de entidades, incluyendo entidades genéricas o personalizadas, entidades externas ([[XXE]]), y entidades predefinidas como `&lt;` y `&gt;`.

___
# Entidades Genéricas/Personalizadas

Las entidades genéricas permiten definir y utilizar etiquetas personalizadas en un documento [[XML]]. Esto facilita la creación de estructuras de datos específicas para un uso particular.

```xml
<!DOCTYPE nota [
  <!ENTITY nombre "Juan Pérez">
  <!ENTITY edad "30">
]>

<informacion>
  <autor>&nombre;</autor>
  <edad>&edad;</edad>
</informacion>
```

En este ejemplo, las entidades `nombre` y `edad` se definen y luego se utilizan dentro del documento [[XML]].

___
# Entidades Externas (XXE)

Las Entidades Externas ([[XXE]]) permiten referenciar contenido externo dentro de un documento [[XML]]. Esto es especialmente útil para reutilizar información o datos almacenados en archivos externos.

```xml
<!DOCTYPE nota [
  <!ENTITY externa SYSTEM "archivo_externo.xml">
]>

<informacion>
  &externa;
</informacion>
```

En este caso, la entidad `externa` hace referencia al contenido del archivo externo `archivo_externo.xml`.

___
# Entidades Predefinidas (&lt; y &gt;)

Algunos caracteres especiales, como `<` y `>`, no pueden ser utilizados directamente en [[XML]] debido a su significado reservado. En su lugar, se utilizan entidades predefinidas.

- `&lt;` representa `<` (menor que).
- `&gt;` representa `>` (mayor que).

```xml
<etiqueta>
  Contenido &lt; especial &gt;
</etiqueta>
```

Esto asegura que los caracteres `<` y `>` no se interpreten como parte de la sintaxis [[XML]], sino como contenido literal.

___
# Conclusiones

Las entidades en [[XML]] ofrecen flexibilidad y reutilización en la construcción de documentos. Puedes personalizar tus propias entidades, referenciar contenido externo con [[XXE]], y manejar caracteres especiales mediante entidades predefinidas.