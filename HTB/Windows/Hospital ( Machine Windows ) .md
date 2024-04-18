---
sticker: emoji//1f3e5
---
## Reconocimiento

Escaneo con Nmap.

![](ss/Hospital/nmap.png)
![](ss/Hospital/nmap2.png)

## Puerto 443 ( HTTPS )

Esta maquina en el puerto 443 tiene corriendo un servicio HTTPS que es una pagina web con el cliente Roundcube para gestionar emails, por detras esta un windows server.

![](ss/Hospital/https.png)

## Puerto 8080 ( HTTP )

En el puerto 8080 tiene una web propia del hospital que por detras esta corriendo php.

![](ss/Hospital/http.png)

Al intentar ingresar con el user 'admin' te muestra un mensaje que dice 'The username is already taken' lo cual significa que el usuario admin existe.

porcedo a crear una cuenta con el user y password 'admin2':'admin123'.

![](ss/Hospital/user_admin_exists.png)

Al ingresar, al parecer es solo una web para que el paciente suba su registro medico.

![](ss/Hospital/index_http.png)

El tipo de archivo que espera es una imagen.

![](ss/Hospital/type_of_file.png)

Lo siguiente es interceptar la peticion con [[burpsuite]] y probar con otro tipo de extensiones como por ejemplo sabemos que esta correindo php por detras por lo tanto podemos probar si podemos subir un archivo.php o extensiones como php5,phtm,phar,etc.

![](ss/Hospital/burp_http_upload.png)

Failed con extension .php.

![](ss/Hospital/failed.png)

Success con extension .phtm.

![](ss/Hospital/success.png)

Vemos un upload.php lo que significa que hay una ruta en donde se guardan las imagenes que puede ser uploads.

![](ss/Hospital/uploads.png)
## Fuzzing extension file with ffuf

Me copio la request de burpsuite y le coloco la palabra clave para aplicar fuzzing con tools como ffuf, wfuzz, etc.

![](ss/Hospital/upload_req.png)

```
ffuf -request upload.req -request-proto http -w /opt/Seclists/Fuzzing/extensions-most-common.fuzz.txt
```

![](ss/Hospital/ffuf_1.png)

Para filtrar por extensiones que realmente esten funcionando aplicamos un '-mr' para aplicar un regex que filtre por responses que tengan success en su body.

```
ffuf -request upload.req -request-proto http -w /opt/Seclists/Fuzzing/extensions-most-common.fuzz.txt -mr success
```

Tenemos varios matches, pero no usare ninguno de esas extensiones probe la extension 'phar' manualmente y me funciona. 

![](ss/Hospital/ffuf_success.png)

Le paso el phpinfo() para ver las disable_functions.

![](ss/Hospital/phpinfo.png)
![](ss/Hospital/disable_functions.png)

## Bypassing the disable_functions

https://github.com/teambi0s/dfunc-bypasser

Lo que voy hacer es crear un foreach en php que me muestre si una funcion peligrosa existe.

![](ss/Hospital/script_disable_functions.png)

Hay seis funciones peligrosas que puedo usar para ejecutar comandos en el sistema.
Probare con la funcion 'popen'.

![](ss/Hospital/danger_functions.png)

Para eso vamos a la documentación.

![](ss/Hospital/popen_example.png)
![](ss/Hospital/popen_1.png)
## RCE ( HTTP 8080 )

Ya estamos ejecutando comandos en el sistema.

![](ss/Hospital/RCE_8080.png)

Lo siguiente es crear una reverse shell con bash y ponernme en escucha con [[nc]].

![](ss/Hospital/rev_shell_bash.png)
![](ss/Hospital/nc_1.png)

Credenciales de la base de datos expuestas en el config.php

![](ss/Hospital/pass_db.png)

```
mysql -u root -p
```

![](ss/Hospital/mysql.png)

Tenemos hashes que podemos intentar romper.

![](ss/Hospital/users_table.png)

Tipo de hash con hashid.

![](ss/Hospital/hashid.png)

```
hashcat -a 0 -m 3200 hashes.8080 /usr/share/wordlists/rockyou.txt
```


