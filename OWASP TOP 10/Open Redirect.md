La vulnerabilidad de redirección abierta, también conocida como **Open Redirect**, es una vulnerabilidad común en aplicaciones web que puede ser explotada por los atacantes para dirigir a los usuarios a sitios web maliciosos. Esta vulnerabilidad se produce cuando una aplicación web permite a los atacantes manipular la URL de una página de redireccionamiento para redirigir al usuario a un sitio web malicioso.

Por ejemplo, supongamos que una aplicación web utiliza un parámetro de redireccionamiento en una URL para dirigir al usuario a una página externa después de que se haya autenticado. Si esta URL no valida adecuadamente el parámetro de redireccionamiento y permite a los atacantes modificarlo, los atacantes pueden dirigir al usuario a un sitio web malicioso, en lugar del sitio web legítimo.

Un ejemplo de cómo los atacantes pueden explotar la vulnerabilidad de redirección abierta es mediante la creación de correos electrónicos de **phishing** que parecen legítimos, pero que en realidad contienen enlaces manipulados que redirigen a los usuarios a un sitio web malicioso. Los atacantes pueden utilizar técnicas de **ingeniería social** para convencer al usuario de que haga clic en el enlace, como ofrecer una oferta atractiva o una oportunidad única.

______________

[Open Redirect 1](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Url-redirection)

```bash
npm install
npm start
```

localhost:5000

Podemos ver una web la cual esta obsoleta y un botón que nos dirige al nuevo sitio web

![[Pasted image 20231008202124.png]]

si observamos como se tramita la petición podemos notar que se realiza por **POST** utilizando unos parámetros de los cuales nos podemos aprovechar e inyectar en la url del dominio victima y así redirigir la web a donde nosotros queramos en nuestro caso a _**https://google.es

![[Pasted image 20231008203430.png]]

Esto nos tendría que redirigir a la pagina de google

![[Pasted image 20231008203652.png]]

![[Pasted image 20231008203813.png]]

BUALA!

________________________

 [Open Redirect 2](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Url-redirection-harder)

```bash
npm install
npm start
```

**CARACTER EXCLUTION**

Como podemos observar si intentamos realizar el redirect nos da un mensaje diciendo que el **"."** no puede ir incluido en la dirección escrita

![[Pasted image 20231008215741.png]]


Se puede **url encodear** uno o mas carácteres para evitar algún tipo de sanitización 

```
https://google.cl
url encode "." --> https://google%2ees
url encode "%" --> https://google%252ees
```

![[Pasted image 20231008215344.png]]

![[Pasted image 20231008215411.png]]

BUALA!

_______________

[Open Redirect 3](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Url-redirection-harder2)

Es posible también omitir las barras _**"//"**_ cuando se trata de _**https**_ 

```
https:google%252ees
```

![[Pasted image 20231008220715.png]]

![[Pasted image 20231008215411.png]]

BUALA!

______________________________
