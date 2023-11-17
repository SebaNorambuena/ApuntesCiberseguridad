____________________

El **Squid Proxy** es un servidor web **proxy-caché** con licencia **GPL** cuyo objetivo es funcionar como **proxy** de la red y también como zona caché para almacenar páginas web, entre otros. Se trata de un servidor situado entre la máquina del usuario y otra red (a menudo Internet) que actúa como protección separando las dos redes y como zona caché para acelerar el acceso a páginas web o poder restringir el acceso a contenidos.

Ahora bien, puede darse el caso en el que un servidor Squid Proxy se encuentre **mal configurado**, permitiendo en consecuencia a los atacantes recopilar información de dispositivos a los que normalmente no deberían tener acceso.

Por ejemplo, en este tipo de situaciones, un atacante podría ser capaz de realizar peticiones a direcciones IP internas pasando sus consultas a través del Squid Proxy, pudiendo así realizar un escaneo de puertos contra determinados servidores situados en una **red interna**.

_____________________
Si el proxy no cuenta con credenciales de acceso podemos con _**curl**_ pasar a través del proxy para ver recursos internos del servidor como puertos que solo están abiertos internamente

```bash
curl --proxy http://<ip>:3128 http://<ip>:<port>
```

Gracias a esto podemos diseñar formas para enumerar puertos abiertos internamente

un ejemplo de un script en _**python**_ para la recolección de puertos internos

```python
#!/usr/bin/python3
import sys, signal, request

def def_handler(sig, frame):
	print("\n\n[!] Saliendo...\n")
	sys.exit(1)

#Ctrl+C
signal.signal(signal.SIGINT, def_handler)

main_url = "http://127.0.0.1"
squid_proxy = {'http': '<url>:3128'}

def portDiscovery():

	for tcp_port in range(0, 65535):
		r = request.get(main_url + ':' + str(tcp_port), proxies=squid_proxy)
		if r.status_code != 503:
			print("\n[+] Port " + str(tcp_port) + " - OPEN")

if __name__ == '__main__'
	portDiscovery()

```
____________________________________

Si se necesitan credenciales podemos insertarlas en la misma _**url**_

```bash
curl --proxy http://<user>:<password>@<ip>:3128 http://<ip>:<port>
```

[Máquina SickOs 1.1](https://www.vulnhub.com/entry/sickos-11,132/)
