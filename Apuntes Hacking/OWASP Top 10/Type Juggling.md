____

- Tags:

___

# Definición 

Los ataques de **Type Juggling** son una técnica utilizada en seguridad informática para manipular el tipo de datos de manera que se produzcan comportamientos inesperados en el código. Aquí exploraremos ejemplos que involucran arrays, la función strcmp, la comparación con dobles iguales, y colisiones **MD5**.

___
# Type Juggling con Arrays

Considera el siguiente código PHP:

```php
$expected_password = "secreto123";
$user_input = "secreto" . str_repeat("\0", 6) . "456";

if ($user_input == $expected_password) {
    echo "Contraseña válida";
} else {
    echo "Contraseña inválida";
}
```

En este ejemplo, se utiliza la función `str_repeat` para agregar caracteres nulos al final de la cadena. Aunque visualmente las cadenas pueden parecer diferentes, la comparación de PHP puede considerarlas iguales, lo que puede conducir a un acceso no autorizado.

___
# Type Juggling con strcmp y MD5

La función `strcmp` en PHP puede producir resultados inesperados debido a **Type Juggling**. Considera este ejemplo, donde explotamos la exponenciación con **MD5**:

```php
$expected_hash = "5ebe2294ecd0e0f08eab7690d2a6ee69"; // MD5 hash de la cadena "password"
$user_input = "0e462097431906509019562988736854"; // Valor que evalúa a 0 cuando se compara con doble igual

if (strcmp($user_input, $expected_hash) == 0) {
    echo "Contraseña válida";
} else {
    echo "Contraseña inválida";
}
```

En este caso, ciertos **MD5** hashes que comienzan con "0e" en PHP se evalúan a 0 cuando se comparan con doble igual, lo que puede conducir a falsos positivos.

____
# Type Juggling con Doble Igual

La comparación con doble igual (`==`) en algunos lenguajes puede ser susceptible a **Type Juggling**. Por ejemplo, en PHP:

```php
$expected_value = "42";
$user_input = 42;

if ($user_input == $expected_value) {
    echo "Valores iguales";
} else {
    echo "Valores diferentes";
}
```

Aunque los tipos de datos son diferentes, la comparación devuelve verdadero debido a la conversión automática de tipo.

____
# Type Juggling en Colisiones MD5

Las colisiones **MD5** pueden explotarse para realizar ataques de **Type Juggling**. Considera el siguiente ejemplo:

```python
import hashlib

original_data = "hello"
malicious_data = "5d41402abc4b2a76b9719d911017c592"  # MD5 hash de "hello"

if hashlib.md5(original_data.encode()).hexdigest() == malicious_data:
    print("Colisión MD5 exitosa")
else {
    print("Colisión MD5 no exitosa")
}
```

En este caso, se puede generar un hash malicioso que coincide con el hash de la cadena original.

Recuerda siempre tener en cuenta la seguridad en el desarrollo de software y utilizar técnicas de mitigación adecuadas para prevenir ataques de **Type Juggling**.