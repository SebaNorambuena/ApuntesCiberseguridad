En Javascript, los prototipos definen la estructura y las propiedades de un objeto para que la aplicación sepa cómo manejar los datos. Pero resulta que si modificas el prototipo en un lugar, afectará el funcionamiento de los objetos en toda la aplicación.

```JS
var myOnject = {};
> undefined

myObject.__proto__.foo = "bar"
> "bar"

myObject.foo
> "bar"

console.log({}.foo)
> bar
```

Sobrescribir el prototipo de un objeto javascript predeterminado se considera una mala práctica durante mucho tiempo, Por lo tanto, es posible que no lo hagas en tu código. Pero ¿qué pasa si alguien más puede modificar los prototipos? Esa es, **Prototype Pollution** y ocurre debido a algunas operaciones inseguras **_merge_** , **_clone_** , **_extend_** y **_path assignment_** en objetos JSON obtenidos a través de entradas del usuario.

Así se ve un merge

```JS
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
```

Todo lo que el atacante tiene que hacer para contaminar su prototipo es proporcionarle datos JSON que tengan la propiedad **___proto___** . Una carga útil común se verá así:

```JSON
{
	"foo": "bar",
	"__proto__":{
				"polluted": "true",
				}
}
```

Si pasa esta carga útil a su operación de fusión sin desinfectar los campos, contaminará por completo los prototipos de sus objetos.

La gravedad de la contaminación depende del tipo de carga útil y de cómo utilices tus objetos. Si los usas para autorizar a los administradores

```JS
if(user.isAdmin){
	//hacer algo
}
```

Luego permitirá que el atacante obtenga acceso a información confidencial contaminando el alcance con la propiedad **_isAdmin_** . Si el atacante cambia algún atributo existente a un tipo de retorno inesperado (por ejemplo, el atributo toString para devolver un tipo entero), provocará que su aplicación falle ( **Denegación de servicio** ) o si está utilizando su servidor para la ejecución de código como **_node.js exec_** o **_eval_** . entonces incluso podría conducir a **la ejecución remota de código (RCE)** .

**CLONE**

**_Operación clone_** que es básicamente una operación merge en un objeto vacío.

```JS
var clon = function(objectToBeCloned) {  
return merge({}, objectToBeCloned);  
}
```

**_Operación de path assignment_** donde puede definir la propiedad de un objeto accediendo directamente a su ruta. Una operación de path assignment vulnerable se parecerá al siguiente fragmento de código.

```JS
var pathAssignment = (obj, path, value) => {
	var segments = path.split(".");
	var key = segments.splice(0,1)[0];
	if(segments.length) {
		if(obj[key]) {
			obj[key] = pathAssignment(obj[key], segments.join('.'), value);
		} else {
			obj[key] = pathAssignment({}, segments.join('.'), value);
		}
	} else {
		obj[key] = value;
	}
	return obj;
};
```

El siguiente fragmento de código explica cómo el método anterior provoca la Prototype Pollution

```
var obj = pathAssignment({}, 'foo', 'bar');   
// Esto creará un objeto { foo: "bar" }var obj = pathAssignment({}, '__proto__.polluted', true);   
// ¡Esto contaminará totalmente los prototipos de tus objetos!
```

Si está utilizando una operación vulnerable como la operación de combinación que vimos anteriormente, puede solucionarla simplemente impidiendo la combinación si el nombre de la clave es **"_____proto_____"** como el siguiente código.
