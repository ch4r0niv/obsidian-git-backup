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

Al buscar cualquier dato en el input te muestra la siguiente imgen.

![](ss/Jeeves/imgError.png)

Por lo tanto descartamos este puerto.

## Puerto 135 (  )