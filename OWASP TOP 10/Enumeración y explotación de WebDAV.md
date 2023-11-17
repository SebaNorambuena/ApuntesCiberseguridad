_________________________________

**WebDAV** (**Web Distributed Authoring and Versioning**) es una extensión del protocolo HTTP que permite a los usuarios **acceder** y **manipular** **archivos** en un servidor web a través de una conexión segura.

Cuando hablamos de enumerar un servidor WebDAV, a lo que nos referimos es al proceso de recopilar información sobre los recursos disponibles en el servidor WebDAV. Los atacantes pueden utilizar herramientas de enumeración de WebDAV para buscar recursos protegidos en el servidor, como archivos de configuración, contraseñas y otros datos confidenciales. Los atacantes pueden utilizar la información recopilada durante la enumeración para planificar ataques más sofisticados contra el servidor.

Asimismo, esta fase inicial de reconocimiento implica el intentar identificar las **extensiones de archivo** permitidas en el servidor. Una vez detectadas las extensiones de archivo permitidas, los atacantes pueden aprovecharse de esto para cargar y ejecutar archivos que contengan código malicioso. Si los archivos maliciosos se cargan y ejecutan con éxito en el servidor web, los atacantes pueden obtener acceso no autorizado al servidor y comprometer la seguridad del sistema.
____________________________________________

[WebDav](https://hub.docker.com/r/bytemark/webdav)

**Despliegue**

```bash
docker pull bytemark/webdav
docker run --restart always -v /srv/dav:/var/lib/dav -e AUTH_TYPE=Digest -e USERNAME=admin -e PASSWORD=richard --publish 80:80 -d bytemark/webdav
```
__________________
**Reccon**

```bash
whatweb <url>
```

![[Pasted image 20231008222439.png]]

Podemos ver que es un _**webDAV**_ gracias a _**whatweb**_ 
___________

**Tools**

**DAVTEST**

```bash
davtest -url <url> -auth <user>:<password>
```

fuerza bruta con _**bash**_ y _**davtest**_ 

```bash
cat <diccionary> | while read password; do response=$(davtest -url <url> -auth <user>:$password 2>&1 | grep -i succeed); if [ $response ]; then echo "[+] la contraseña correcta es $password"; break; fi; done
```
_________________
**CADAVER**

```bash
apt install cadaver
```

```bash
cadaver <url>
```

______________________________

