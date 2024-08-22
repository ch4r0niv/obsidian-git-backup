---
tags:
  - "#stored-xss"
  - "#XSS"
---
# ¿Qué es el cross-site scripting almacenado?

El cross-site scripting almacenado (también conocido como XSS de segundo orden o persistente) ocurre cuando una aplicación recibe datos de una fuente no confiable y luego incluye esos datos en sus respuestas HTTP posteriores de manera insegura.

Supongamos que un sitio web permite a los usuarios enviar comentarios en publicaciones de blog, los cuales se muestran a otros usuarios. Los usuarios envían comentarios usando una solicitud HTTP como la siguiente:

```http
POST /post/comment HTTP/1.1 
Host: vulnerable-website.com 
Content-Length: 100  postId=3&comment=Este+post+fue+extremadamente+útil.&name=Carlos+Montoya&email=carlos%40normal-user.net
```

Después de que se ha enviado este comentario, cualquier usuario que visite la publicación del blog recibirá lo siguiente dentro de la respuesta de la aplicación:

```html
<p>Este post fue extremadamente útil.</p>
```

Suponiendo que la aplicación no realice ningún otro procesamiento de los datos, un atacante puede enviar un comentario malicioso como este:

```js
<script>/* Cosas malas aquí... */</script>
```

Dentro de la solicitud del atacante, este comentario se codificaría en URL como:

```http
comment=%3Cscript%3E%2F*%2BCosas%2Bmalas%2B%28...%2B*%2F%3C%2Fscript%3E
```

Cualquier usuario que visite la publicación del blog ahora recibirá lo siguiente dentro de la respuesta de la aplicación:

```html
<p><script>/* Cosas malas aquí... */</script></p>
```

El script suministrado por el atacante se ejecutará entonces en el navegador del usuario víctima, en el contexto de su sesión con la aplicación.

--------------------------------------------------------------------------
## Impacto de los ataques de XSS almacenado

Si un atacante puede controlar un script que se ejecuta en el navegador de la víctima, entonces generalmente puede comprometer por completo a ese usuario. El atacante puede llevar a cabo cualquier de las acciones que son aplicables al impacto de las vulnerabilidades de XSS reflejado.

En términos de explotabilidad, la diferencia clave entre XSS reflejado y XSS almacenado es que una vulnerabilidad de XSS almacenado permite ataques que están contenidos dentro de la propia aplicación. El atacante no necesita encontrar una manera externa de inducir a otros usuarios a hacer una solicitud particular que contenga su exploit. En cambio, el atacante coloca su exploit dentro de la aplicación misma y simplemente espera a que los usuarios lo encuentren.

La naturaleza autónoma de los exploits de XSS almacenado es particularmente relevante en situaciones donde una vulnerabilidad de XSS solo afecta a los usuarios que están actualmente conectados a la aplicación. Si el XSS es reflejado, entonces el ataque debe ser cronometrado fortuitamente: un usuario que es inducido a hacer la solicitud del atacante en un momento en que no está conectado no será comprometido. En contraste, si el XSS es almacenado, entonces se garantiza que el usuario estará conectado en el momento en que encuentre el exploit.

--------------------------------------------------------------------------
## Cómo encontrar y probar vulnerabilidades de XSS almacenado

Muchas vulnerabilidades de XSS almacenado se pueden encontrar utilizando el escáner de vulnerabilidades web de Burp Suite.

Probar manualmente las vulnerabilidades de XSS almacenado puede ser desafiante. Necesitas probar todos los "puntos de entrada" relevantes a través de los cuales los datos controlables por el atacante pueden ingresar al procesamiento de la aplicación, y todos los "puntos de salida" en los que esos datos pueden aparecer en las respuestas de la aplicación.

#### Puntos de entrada en el procesamiento de la aplicación incluyen:

- Parámetros u otros datos dentro de la cadena de consulta de la URL y el cuerpo del mensaje.
- La ruta del archivo en la URL.
- Encabezados HTTP que podrían no ser explotables en relación con XSS reflejado.
- Cualquier ruta fuera de banda a través de la cual un atacante pueda entregar datos a la aplicación. Las rutas que existen dependen completamente de la funcionalidad implementada por la aplicación: una aplicación de correo web procesará datos recibidos en correos electrónicos; una aplicación que muestra un feed de Twitter podría procesar datos contenidos en tuits de terceros; y un agregador de noticias incluirá datos originados en otros sitios web.

#### Los puntos de salida para ataques de XSS almacenado son todas las posibles respuestas HTTP que se devuelven a cualquier tipo de usuario de la aplicación en cualquier situación.

El primer paso en la prueba de vulnerabilidades de XSS almacenado es localizar los enlaces entre los puntos de entrada y salida, mediante los cuales los datos enviados a un punto de entrada se emiten desde un punto de salida. Las razones por las cuales esto puede ser desafiante son:

- Los datos enviados a cualquier punto de entrada podrían, en principio, ser emitidos desde cualquier punto de salida. Por ejemplo, los nombres de usuario proporcionados por el usuario podrían aparecer en un registro de auditoría obscuro que solo es visible para algunos usuarios de la aplicación.
- Los datos que están actualmente almacenados por la aplicación a menudo son vulnerables a ser sobrescritos debido a otras acciones realizadas dentro de la aplicación. Por ejemplo, una función de búsqueda podría mostrar una lista de búsquedas recientes, que se reemplaza rápidamente a medida que los usuarios realizan otras búsquedas.

Para identificar de manera exhaustiva los enlaces entre puntos de entrada y salida, se implicaría probar cada permutación por separado, enviando un valor específico al punto de entrada, navegando directamente al punto de salida y determinando si el valor aparece allí. Sin embargo, este enfoque no es práctico en una aplicación con más de unas pocas páginas.

En su lugar, un enfoque más realista es trabajar sistemáticamente a través de los puntos de entrada de datos, enviando un valor específico en cada uno y monitoreando las respuestas de la aplicación para detectar casos en los que el valor enviado aparece. Se puede prestar especial atención a funciones relevantes de la aplicación, como los comentarios en publicaciones de blog. Cuando se observa el valor enviado en una respuesta, necesitas determinar si los datos realmente se están almacenando a través de diferentes solicitudes, en lugar de ser simplemente reflejados en la respuesta inmediata.

Una vez que hayas identificado los enlaces entre puntos de entrada y salida en el procesamiento de la aplicación, cada enlace debe ser probado específicamente para detectar si existe una vulnerabilidad de XSS almacenado. Esto implica determinar el contexto dentro de la respuesta donde aparecen los datos almacenados y probar payloads XSS candidatos adecuados para ese contexto. En este punto, la metodología de prueba es en gran medida la misma que para encontrar vulnerabilidades de XSS reflejado.