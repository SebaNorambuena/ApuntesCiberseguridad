________________
Los **Json Web Tokens** (**JWT**) son un tipo de token utilizado en la **autenticación** y **autorización** de usuarios en aplicaciones web. JWT es un estándar abierto (**RFC 7519**) que define un formato compacto y seguro para transmitir información entre diferentes partes de forma confiable.

En lo que respecta a la fase de enumeración y explotación de un JWT, esto se produce cuando un atacante es capaz de obtener información sobre los JWT que se utilizan en la aplicación, lo que podría permitir al atacante explotar las debilidades en la autenticación y autorización de la aplicación.

La enumeración de los JWT se produce cuando un atacante utiliza técnicas de fuerza bruta o cierta ingeniería inversa para obtener información sobre los JWT utilizados por la aplicación web. Por ejemplo, el atacante podría intentar adivinar los valores de los JWT mediante la construcción de **tokens falsos**, tratando de validar en todo momento si la aplicación web los acepta o no. Si el atacante tiene éxito en la enumeración de un JWT válido, podría obtener información confidencial, como nombres de usuario, contraseñas, roles de usuario y otros datos de autenticación y autorización.

La explotación de los JWT se produce cuando un atacante utiliza la información obtenida de la enumeración del JWT para explotar debilidades en la aplicación. Por ejemplo, si la aplicación web utiliza JWT para la autenticación, pero no valida adecuadamente la **firma** del JWT, un atacante podría falsificar el token y acceder a la aplicación web como si fuera un usuario legítimo.
________________________________

[SKF-LABS](https://github.com/blabla1337/skf-labs)

web [_**jwt.io**_](https://jwt.io)
![[Pasted image 20231011150352.png]]

```bash
# Manipulacion de JWT el cual es posible darle valor NONE al algoritmo haciendo que no necesite signature

echo -n '{"alg":"NONE","typ":"JWT"}' | base64
> valor base64(1)
echo -n '{"id":2,"iat":123456,"exp":654321}' | base64
> valor base64(2)

valos base64(1).valor base64(2).   # Quedaria como un JWT valido y abriamos manipulado los datos

```

También podemos realizar fuerza bruta en el código secreto