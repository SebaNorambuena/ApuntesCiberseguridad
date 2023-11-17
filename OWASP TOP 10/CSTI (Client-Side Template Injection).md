El **Client-Side Template Injection** (**CSTI**) es una vulnerabilidad de seguridad en la que un atacante puede inyectar código malicioso en una **plantilla de cliente**, que se ejecuta en el **navegador** del usuario en lugar del servidor.

A diferencia del **Server-Side Template Injection** (**SSTI**), en el que la plantilla de servidor se ejecuta en el servidor y es responsable de generar el contenido dinámico, en el CSTI, la plantilla de cliente se ejecuta en el navegador del usuario y se utiliza para generar contenido dinámico en el lado del cliente.

-----------------------------------------

Practica

```bash
git clone https://github.com/blabla1337/skf-labs
```

ingresar a la carpeta del proyecto /CSTI

instalar pip2

```bash
apt install python2
wget https://bootstrap.pypa.io/pip/2.7/get-pip.py
python2 get-pip.py
```

```bash
pip2 install -r requirements.txt
python2 CSTI.py
```

[CSTI --> XSS in Angular](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/XSS%20in%20Angular.md)

----------------------------------
