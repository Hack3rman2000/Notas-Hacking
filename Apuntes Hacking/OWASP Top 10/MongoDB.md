___

- Tags: 

___

# Introducción:

**MongoDB** es una base de datos [[NoSQL]] documental que se destaca por su capacidad para manejar grandes volúmenes de datos no estructurados. Su enfoque flexible permite almacenar datos en forma de documentos [BSON](https://es.wikipedia.org/wiki/BSON) (JSON binario), proporcionando una solución eficiente para el manejo de información compleja y variada.

___
# Modelo de Datos Documental:

- **MongoDB** almacena los datos en documentos [BSON](https://es.wikipedia.org/wiki/BSON), lo que facilita la representación y manipulación de datos complejos de manera eficiente.
- Los documentos se organizan en colecciones, ofreciendo una estructura lógica y escalable para la gestión de datos en entornos dinámicos.

___
# Comandos Básicos:

```bash
use <nombre_db>
```

Cambia a la base de datos especificada o la crea si no existe, permitiendo una gestión eficiente de múltiples conjuntos de datos.

```bash
db.collection.insertOne(<documento>)
```

Inserta un documento en la colección especificada, brindando flexibilidad para agregar datos de manera individual.

```bash
db.collection.find(<filtro>)
```

Recupera documentos que coinciden con el filtro especificado en la colección, facilitando la consulta de datos específicos.

```bash
db.collection.updateOne(<filtro>, <actualización>)
```

Actualiza un documento que coincide con el filtro especificado en la colección, permitiendo la modificación de datos de manera controlada.

```bash
db.collection.deleteOne(<filtro>)
```

Elimina un documento que coincide con el filtro especificado en la colección, proporcionando una forma eficaz de gestionar la información.

```bash
db.collection.createIndex(<índice>)
```

Crea un índice en la colección para mejorar la velocidad de las consultas, optimizando el rendimiento de las operaciones.

### Operadores de Consulta:

```bash
$eq
```

Igual a.

```bash
$gt, $lt
```

Mayor que, menor que.

```bash
$in
```

Coincide con cualquier valor en un conjunto especificado.

```bash
$exists
```

Comprueba si un campo existe en un documento, facilitando la validación de datos.

### Comandos de Administración:

```bash
show dbs
```

Muestra todas las bases de datos disponibles, ofreciendo una visión global del entorno de datos.

```bash
show collections
```

Muestra todas las colecciones en la base de datos actual, facilitando la navegación y gestión de datos.

```bash
db.stats()
```

Proporciona estadísticas sobre la base de datos actual, permitiendo evaluar su rendimiento y salud.

### Comandos de Agregación:

```bash
db.collection.aggregate([...])
```

Permite realizar operaciones de agregación, como agrupamiento y proyección, ofreciendo flexibilidad en el análisis de datos.

___
# MongoDB Shell:

Interactúa con **MongoDB** principalmente a través de su shell, una interfaz de línea de comandos, proporcionando una forma directa y eficaz de gestionar la base de datos.

___
# Casos de Uso Comunes:

**MongoDB** es ampliamente utilizado en aplicaciones web, desarrollo ágil, proyectos de Big Data y sistemas que requieren flexibilidad y escalabilidad. Su capacidad para manejar datos no estructurados lo convierte en una opción ideal para entornos dinámicos.

___
# Herramientas y Ecosistema:

**MongoDB** cuenta con herramientas como **MongoDB Compass** y **MongoDB Atlas** para facilitar la gestión y visualización de datos. Estas herramientas complementan la experiencia de desarrollo al proporcionar interfaces gráficas y servicios en la nube que simplifican la administración de bases de datos.

**MongoDB** es una elección poderosa para aquellos que buscan una base de datos ágil y escalable, con comandos intuitivos y una sólida capacidad de manipulación de datos. Los desarrolladores pueden aprovechar su flexibilidad de esquema y su capacidad para manejar datos no estructurados con facilidad.