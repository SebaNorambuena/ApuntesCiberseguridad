_____________________
**GraphQL** es un lenguaje de consulta para **APIs** (**Application Programming Interfaces**) que se ha vuelto cada vez más popular en los últimos años. A diferencia de las APIs tradicionales que tienen endpoints fijos, GraphQL permite a los clientes solicitar sólo la información que necesitan y obtener una respuesta personalizada en función de sus necesidades.

**Introspection** en GraphQL es un mecanismo que permite a los clientes **obtener información** sobre el esquema GraphQL de una API. Esto significa que los clientes pueden explorar y descubrir los tipos de datos, los campos y las relaciones que existen en la API, lo que puede ser muy útil para los desarrolladores que necesitan construir clientes GraphQL. Sin embargo, la introspección también puede ser utilizada por los atacantes para obtener información sensible sobre la estructura y los datos que existen en la API, lo que puede ser utilizado para llevar a cabo ataques más sofisticados.

Por otro lado, las **Mutations** en GraphQL son operaciones que permiten a los clientes **modificar los datos** en la API. A diferencia de las consultas, que sólo permiten la lectura de datos, las mutaciones permiten a los clientes agregar, actualizar o eliminar datos. Esto significa que las mutaciones tienen el potencial de ser utilizadas para realizar cambios importantes en la base de datos subyacente de la API. Si no se protegen adecuadamente, las mutaciones pueden ser explotadas por los atacantes para realizar cambios malintencionados en la API, como la eliminación de datos importantes o la creación de nuevos registros.

En el contexto de GraphQL, los **IDORs** pueden ocurrir cuando un atacante es capaz de adivinar o enumerar identificadores (**IDs**) de objetos dentro de la API, y puede utilizar esos IDs para acceder a objetos a los que no debería tener acceso. Esto puede ocurrir porque los desarrolladores de la API pueden no haber implementado adecuadamente los mecanismos de autenticación y autorización en su API.

Por ejemplo, supongamos que una API GraphQL permite a los usuarios acceder a información sobre sus propios pedidos utilizando el ID de pedido. Si el desarrollador de la API no ha implementado la autorización adecuada, un atacante podría utilizar la introspección de GraphQL para descubrir todos los IDs de pedido existentes en la API, incluyendo los pedidos de otros usuarios. El atacante podría luego utilizar estos IDs para acceder a los pedidos de otros usuarios sin la debida autorización, lo que constituye un IDOR.

Los atacantes también pueden utilizar las Mutaciones de GraphQL para llevar a cabo ataques IDOR. Por ejemplo, supongamos que una API GraphQL permite a los usuarios actualizar la información de sus propios pedidos. Si el desarrollador de la API no ha implementado adecuadamente la autorización, un atacante podría utilizar una mutación para actualizar los datos de los pedidos de otros usuarios, utilizando los IDs de pedido que ha descubierto mediante la introspección.
___________________________
[GraphQL Introspection](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Graphql-Introspection)
[GraphQL Mutations](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Graphql-Mutations)
[GraphQL Injection](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Graphql-Injection)
[GraphQL IDOR](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/Graphql-IDOR)

IDOR 

```bash
gobuster dir -u <url> -w <diccionario> -t 20
```

se descubre _**/settings**_ y en esta te muestra datos del usuario

si interceptamos con _**Burpsuite**_ vemos que envía una petición a _**GraphQL**_ la cual es posible modificar un campo llamado _**ID**_ y de esta forma podemos ver datos de usuarios diferentes al nuestro
____________________________________
Instrospection

[HackTriks-GraphQL](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/graphql)

Interceptamos la petición con _**Burpsuite**_ y nos fijamos que envía una query _**/GraphQL**_ esto nos abrirá una app en la cual insertar querys

[GraphQL-Voyager](https://ivangoncharov.github.io/graphql-voyager/)

- CHANGE SCHEMA
- INSTROSPECTION

```
# Esta query va en el campo <url>/graphql como un parametro

/?query=fragment%20FullType%20on%20Type%20{+%20%20kind+%20%20name+%20%20description+%20%20fields%20{+%20%20%20%20name+%20%20%20%20description+%20%20%20%20args%20{+%20%20%20%20%20%20...InputValue+%20%20%20%20}+%20%20%20%20type%20{+%20%20%20%20%20%20...TypeRef+%20%20%20%20}+%20%20}+%20%20inputFields%20{+%20%20%20%20...InputValue+%20%20}+%20%20interfaces%20{+%20%20%20%20...TypeRef+%20%20}+%20%20enumValues%20{+%20%20%20%20name+%20%20%20%20description+%20%20}+%20%20possibleTypes%20{+%20%20%20%20...TypeRef+%20%20}+}++fragment%20InputValue%20on%20InputValue%20{+%20%20name+%20%20description+%20%20type%20{+%20%20%20%20...TypeRef+%20%20}+%20%20defaultValue+}++fragment%20TypeRef%20on%20Type%20{+%20%20kind+%20%20name+%20%20ofType%20{+%20%20%20%20kind+%20%20%20%20name+%20%20%20%20ofType%20{+%20%20%20%20%20%20kind+%20%20%20%20%20%20name+%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20%20%20%20%20ofType%20{+%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20kind+%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20name+%20%20%20%20%20%20%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20%20%20}+%20%20%20%20%20%20}+%20%20%20%20}+%20%20}+}++query%20IntrospectionQuery%20{+%20%20schema%20{+%20%20%20%20queryType%20{+%20%20%20%20%20%20name+%20%20%20%20}+%20%20%20%20mutationType%20{+%20%20%20%20%20%20name+%20%20%20%20}+%20%20%20%20types%20{+%20%20%20%20%20%20...FullType+%20%20%20%20}+%20%20%20%20directives%20{+%20%20%20%20%20%20name+%20%20%20%20%20%20description+%20%20%20%20%20%20locations+%20%20%20%20%20%20args%20{+%20%20%20%20%20%20%20%20...InputValue+%20%20%20%20%20%20}+%20%20%20%20}+%20%20}+}
```

Resolvemos errores que aparezcan 

y copiamos la respuesta para introducirla en el _**Graphql-Voyager**_ esto nos dará un esquema de como se relaciona todo

de esta forma podemos modificar la query que interceptamos en un principio para mostrar lo que queramos

```
{
	"query": "{Users{id,username,isAdmin}}"
}
```
________________________


