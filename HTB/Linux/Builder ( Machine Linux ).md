---
sticker: emoji//1f477
tags:
  - Jenkins
  - Linux
  - ssh
  - Groovy
  - CVE-2024-23897
  - nmap
  - Jetty
  - docker
  - hashid
  - hashcat
  - bcrypt
  - base64
  - misconfiguration
banner: ss/Builder/builder.png
---

# Reconocimiento

## [[Nmap]]

El escaneo con nmap me muestra los puertos `22` y `8080` abiertos, voy a proceder a pasarle una serie de scripts de reconocimiento de nmap para identificar los servicios expuestos en esos puertos y ver versiones.

![](ss/Builder/nmap1.png)

Dejare [[SSH]] de un lado por el momento, ya que no cuento con credenciales validas y la version no es vulnerable.

En el puerto `8080` esta un servidor Jetty.

![](ss/Builder/nmap2.png)

Tiene montado un Jenkins ( Jenkins es una herramienta de automatización ).

En la parte inferior derecha esta la version que se esta usando.

![](ss/Builder/jenkins1.png)

La version `2.441` es vulnerable a una vulnerabilidad con CVE-2024-23897(Arbitrary File Read Vulnerability).

![](ss/Builder/vuln1.png)

Antes de ver el PoC, primero de dare un vistazo a la pagina.

Pobre iniciar sesion con credenciales por default, pero no funciono.

![](ss/Builder/default.png)

![](ss/Builder/login1.png)

En el apatado de `People` al parecer son usuarios validos en el sistema `jennifer` y `anonymous`.

![](ss/Builder/people.png)

En el apartado de `Credentials` hay una private key de SSH del usuario root, pero no la puedo ver.

![](ss/Builder/creds1.png)

![](ss/Builder/creds2.png)

Despues de buscar un buen repo en github, encontre este que te da la forma en la que puedes hacer manual.

![](ss/Builder/cve.png)

Y en efecto es vulnerable.

Me esta mostrando el archivo `/etc/passwd` de la maquina linux que esta detras.

Para que se vea mas limpio voy a jugar con `grep` para filtrar la data que me interesa.

![](ss/Builder/user1.png)

Parece la estructura de un contenedor [[Docker]].

Para confirmalo en lugar de leer el `/etc/passwd` voy a leer el `/etc/hostname`.

![](ss/Builder/user2.png)

Y en efecto es un contenedor.

![](ss/Builder/user3.png)

Aqui ya podemos leer la flag `user.txt`.

![](ss/Builder/userflag.png)

Ahora lo siguiente es elevar privilegios a root.

No hay ninguna `id_rsa` que leer para conectarme por [[SSH]].

# Jenkins en local con docker ( para ver estructura )

Lo que voy hacer es montarme un Jenkins en docker para ver la estructura y ver si hay algun directorio que guarde contraseñas como en los gestores de contenido como wordpress,etc.

![](ss/Builder/docker1.png)

Bingo. 

![](ss/Builder/docker2.png)

Dentro de el directorio de pos3idon_169... hay un archivo `config.xml`, dentro de ese archivo esta la contraseña de el usuario en hash.

![](ss/Builder/docker3.png)

Ahora lo que voy hacer es mira el archivo `users.xml` para ver los usuarios.

Solo esta el usuario `jennifer` que ya habia visto en el apartado de `people` en la pagina de jenkins.

Lo siguiente es ver la contraseña del usuario `jennifer`.

![](ss/Builder/user4.png)

```
java -jar jenkins-cli.jar -s http://10.10.11.10:8080/ connect-node @/var/jenkins_home/users/jennifer_12108429903186576833/config.xml 2>&1 | grep -oP '".*?"'
```

![](ss/Builder/passuser.png)

Ahora toca decifrar el hash.

Para ello primero voy e ver que tipo de hash es con la herramienta [[hashid]].

![](ss/Builder/passuser2.png)

Voy suponer que es `bcrypt` y voy a comenzar el crackeo con [[hashcat]].

```
hashcat -a 0 -m 3200 hash /usr/share/wordlists/rockyou.txt
```

La contraseña del usuario `jennifer` es `princess`.

![](ss/Builder/passuser3.png)

Inicio de sesion con el usuario `jennifer`.

![](ss/Builder/jenni1.png)

Sigo sin poder ver la private key del user root, pero me sale algo que no estaba antes.

![](ss/Builder/priv1.png)

Al darle click me lleva al apartdao de `update` para actualizar las credenciales.

![](ss/Builder/priv2.png)

Ahi vemos la Private Key SSH del usuario `root`.

Esto esta tan mal securizado que puedo ver la private key en el html.

![](ss/Builder/key.png)

Esta en formato `base64`.

![](ss/Builder/key1.png)

Y esta cifrado.

![](ss/Builder/key2.png)

Hay una forma de decifrar esto y es con una funcion propia de jenkins.

Esto se hara en el apartado de script con lenguaje groovy.

```
println(hudson.util.Secret.decrypt("{XXX=}"))
```

![](ss/Builder/script.png)

![](ss/Builder/script1.png)

# Bruhh

![](ss/Builder/script2.png)

Ahora lo que hay que hacer es copiar y guardar en un archivo `id_rsa` esa private key y asignarle los permisos correctos.

![](ss/Builder/key3.png)

## SSH

Ahora solo tocaria conectarse mediante ssh, tenemos la private key y el usuario que es `root`.

![](ss/Builder/root.png)

# R00T3D 

![](ss/Builder/root1.png)





