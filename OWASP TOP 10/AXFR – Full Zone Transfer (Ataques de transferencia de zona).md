
Los ataques de transferencia de zona, también conocidos como ataques **AXFR**, son un tipo de ataque que se dirige a los servidores **DNS** (**Domain Name System**) y que permite a los atacantes obtener información sensible sobre los dominios de una organización.

En términos simples, los servidores DNS se encargan de traducir los nombres de dominio legibles por humanos en direcciones IP utilizables por las máquinas. Los ataques AXFR permiten a los atacantes obtener información sobre los registros DNS almacenados en un servidor DNS.

El ataque AXFR se lleva a cabo enviando una solicitud de transferencia de zona desde un servidor DNS falso a un servidor DNS legítimo. Esta solicitud se realiza utilizando el protocolo de transferencia de zona DNS (AXFR), que es utilizado por los servidores DNS para transferir registros DNS de un servidor a otro.

Si el servidor DNS legítimo no está configurado correctamente, puede responder a la solicitud de transferencia de zona y proporcionar al atacante información detallada sobre los registros DNS almacenados en el servidor. Esto incluye información como los nombres de dominio, direcciones IP, servidores de correo electrónico y otra información sensible que puede ser utilizada en futuros ataques.

La sintaxis para aplicar el AXFR en un servidor DNS es la siguiente:

```bash
dig axfr @<DNS-server> <domain-name>
```

Donde:

<DNS-server> es la dirección IP del servidor DNS que se desea consultar.
<domain-name> es el nombre de dominio del cual se desea obtener la transferencia de zona.
AXFR es el tipo de consulta que se desea realizar, que indica al servidor DNS que se desea una transferencia de zona completa.



