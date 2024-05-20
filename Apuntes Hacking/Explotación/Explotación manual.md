___

- Tags: #explotación 

___

# Definición 

La **explotación manual** es una estrategia avanzada en el campo de la ciberseguridad, donde el atacante realiza la identificación y explotación de vulnerabilidades en un sistema de manera directa, sin depender en gran medida de herramientas automatizadas. Este enfoque requiere un conocimiento profundo del sistema objetivo y de las técnicas de explotación disponibles. A continuación, se destacan las características y consideraciones clave de la explotación manual:


____

# Características Principales

1. **Conocimiento Profundo:**
    
    - Los atacantes que optan por la explotación manual deben tener un conocimiento exhaustivo del sistema objetivo, incluidas sus configuraciones, servicios en ejecución y posibles vulnerabilidades.

2. **Precisión y Control:**
    
    - A diferencia de los métodos automatizados, la explotación manual permite un mayor control sobre el proceso. El atacante puede adaptar sus técnicas en tiempo real y ajustar la estrategia según la respuesta del sistema.

3. **Mayor Esfuerzo y Habilidad:**
    
    - La explotación manual implica un esfuerzo significativo por parte del atacante, ya que se requiere un entendimiento profundo de las vulnerabilidades y de las técnicas de explotación. La habilidad técnica y la paciencia son clave en este enfoque.

4. **Menos Detección:**
    
    - Al evitar el uso de herramientas automatizadas, la explotación manual puede ser menos detectable por los sistemas de defensa de seguridad. Esto se debe a que las tácticas son más específicas y pueden eludir firmas de detección comunes.

___

# Pasos en la Explotación Manual

1. **Reconocimiento:**
    
    - El atacante realiza una investigación exhaustiva para identificar información sobre el sistema objetivo, como puertos abiertos, servicios en ejecución y posibles puntos de vulnerabilidad.

2. **Identificación de Vulnerabilidades:**
    
    - Se analizan detalladamente los resultados del reconocimiento para identificar posibles vulnerabilidades en el sistema.

3. **Desarrollo de Estrategia:**
    
    - Se planifica una estrategia específica para aprovechar las vulnerabilidades identificadas. Esto puede incluir el desarrollo de exploits personalizados.

4. **Ejecución del Ataque:**
    
    - El atacante implementa la estrategia y realiza la explotación manual, aprovechando las vulnerabilidades para obtener acceso no autorizado o realizar acciones maliciosas.

5. **Cobertura de Huellas:**
    
    - Se realizan esfuerzos para minimizar las huellas del ataque y evitar la detección, como la eliminación de registros y la ocultación de actividades maliciosas.

____
# Ejemplos de explotación manual 

Entendido, aquí tienes algunos ejemplos específicos de explotación manual en el contexto de inyección SQL:

### Extracción de Datos:

```sql
SELECT username, password FROM users;
```

Obtener información sensible de la base de datos.
   
### Autenticación Bypass:

```sql
SELECT * FROM users WHERE username = 'admin' AND password = '' OR '1'='1';
```

Sortear la autenticación mediante inyección SQL.

### Obtención de Versiones:

```sql
SELECT @@version;
```

Identificar la versión del sistema de gestión de bases de datos (SGBD).
### Inyección en Consultas UPDATE:

```sql
UPDATE users SET password = 'new_password' WHERE username = 'target_user';
```

Sortear la autenticación mediante inyección SQL.

### Eliminación de Datos:

```sql
DELETE FROM users WHERE username = 'target_user';
```

Eliminar registros de la base de datos.

### Enumeración de Tablas:
   
   ```sql
   SHOW TABLES;
   ```
   
Identificar las tablas presentes en la base de datos.

### Enumeración de Columnas:**

```sql
DESCRIBE users;
```

Identificar las columnas de una tabla específica.
### Uso de Funciones de SQL:

   ```sql 
   SELECT CONCAT(username, '-', password) FROM users;
   ```

Aplicar funciones SQL para obtener resultados específicos.

### Obtención de Información del Sistema:

```sql
SELECT database(), user(), version();
```

Obtener detalles sobre el sistema de base de datos.

### Union-based SQL Injection:

 ```sql
SELECT username, password FROM users UNION SELECT table_name, column_name FROM information_schema.columns;
```

Utilizar la técnica de inyección basada en uniones para combinar resultados de consultas.

Para obtener una lista exhaustiva de payloads y técnicas de explotación, se recomienda explorar el repositorio [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings).





