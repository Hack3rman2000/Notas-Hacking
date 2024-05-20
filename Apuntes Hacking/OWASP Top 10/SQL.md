____

- Tags: #sql #sqli 

___
# Introducción

Structured Query Language (**SQL**) es un lenguaje estándar de programación diseñado para gestionar y manipular bases de datos relacionales. Utilizado en sistemas de gestión de bases de datos ([DBMS](https://es.wikipedia.org/wiki/Sistema_de_gesti%C3%B3n_de_bases_de_datos)) como [[MySQL]], [PostgreSQL](https://es.wikipedia.org/wiki/PostgreSQL), [SQLite](https://es.wikipedia.org/wiki/SQLite) y [Oracle](https://es.wikipedia.org/wiki/Oracle_Database), **SQL** proporciona una interfaz consistente para realizar operaciones como consultas, inserciones, actualizaciones y eliminaciones en los datos almacenados.

___
# Componentes Básicos de SQL

### 1. DDL - Lenguaje de Definición de Datos

- **CREATE TABLE:**

  ```sql
  CREATE TABLE nombre_tabla (
    columna1 tipo_dato,
    columna2 tipo_dato,
    ...
  );
  ```

  Crea una nueva tabla con columnas y tipos de datos especificados.

- **ALTER TABLE:**

  ```sql
  ALTER TABLE nombre_tabla ADD COLUMN nueva_columna tipo_dato;
  ```
  
  Modifica la estructura de una tabla, añadiendo una nueva columna en este caso.

- **DROP TABLE:**

  ```sql
  DROP TABLE nombre_tabla;
  ```

  Elimina una tabla y sus datos de la base de datos.

### 2. DML - Lenguaje de Manipulación de Datos

- **INSERT INTO:**

  ```sql
  INSERT INTO nombre_tabla (columna1, columna2, ...) VALUES (valor1, valor2, ...);
  ```
  
  Añade nuevos registros a una tabla.

- **SELECT:**

  ```sql
  SELECT columna1, columna2, ... FROM nombre_tabla WHERE condicion;
  ```

  Recupera datos de una tabla según una condición especificada.

- **UPDATE:**

  ```sql
  UPDATE nombre_tabla SET columna1 = nuevo_valor WHERE condicion;
  ```

  Modifica los datos existentes en una tabla.

- **DELETE:**

  ```sql
  DELETE FROM nombre_tabla WHERE condicion;
  ```

  Elimina registros de una tabla según una condición.

### 3. DCL - Lenguaje de Control de Datos

- **GRANT:**

  ```sql
  GRANT privilegio ON objeto TO usuario;
  ```

  Concede privilegios específicos sobre objetos (tablas, vistas) a usuarios.

- **REVOKE:**

  ```sql
  REVOKE privilegio ON objeto FROM usuario;
  ```

  Revoca privilegios previamente concedidos.

### 4. TCL - Lenguaje de Control de Transacciones

- **COMMIT:**

  ```sql
  COMMIT;
  ```

  Confirma una transacción y guarda los cambios realizados.

- **ROLLBACK:**

  ```sql
  ROLLBACK;
  ```

  Deshace los cambios en una transacción no confirmada.

___

# Buenas Prácticas y Recomendaciones

1. **Parámetros Preparados:**
   Utilizar parámetros preparados en consultas dinámicas para prevenir [[Inyección SQL|inyecciones SQL]].

2. **Principio de Menor Privilegio:**
   Conceder solo los privilegios necesarios a usuarios y roles.

3. **Índices Eficientes:**
   Crear índices en columnas utilizadas frecuentemente en condiciones de búsqueda.

4. **Copia de Seguridad Regular:**
   Realizar copias de seguridad periódicas para proteger los datos contra pérdidas.

5. **Evitar el Uso de \*:**
   Evitar el uso de `SELECT *` y especificar las columnas necesarias para reducir la carga y mejorar el rendimiento.

6. **Gestión de Transacciones:**
   Utilizar transacciones para garantizar la integridad de los datos.

**SQL** es una herramienta poderosa para interactuar con bases de datos relacionales. Comprender sus conceptos y practicar buenas medidas de seguridad contribuye a un desarrollo de aplicaciones robusto y eficiente.