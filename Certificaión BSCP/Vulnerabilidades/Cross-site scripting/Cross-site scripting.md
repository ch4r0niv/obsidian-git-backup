---
tags:
  - XSS
  - Cross-Site-Scripting
  - "#vulnerabilidad"
  - owasp
  - xss
---
# Que es Cross-site scripting (XSS)

> **Cross-site scripting** (también conocido como **XSS**) es una vulnerabilidad de seguridad web que permite a un atacante comprometer las interacciones que los usuarios tienen con una aplicación vulnerable. Permite a un atacante eludir la política del mismo origen, que está diseñada para segregar diferentes sitios web entre sí. Las vulnerabilidades de scripting entre sitios normalmente permiten a un atacante hacerse pasar por un usuario víctima, llevar a cabo cualquier acción que el usuario sea capaz de realizar y acceder a cualquiera de los datos del usuario. Si el usuario víctima tiene acceso privilegiado dentro de la aplicación, entonces el atacante podría ser capaz de obtener el control total sobre todas las funcionalidades y datos de la aplicación.

# ¿Como funciona?

> **El cross-site scripting (XSS) funciona manipulando un sitio web vulnerable para que devuelva JavaScript malicioso a los usuarios. Cuando el código malicioso se ejecuta dentro del navegador de la víctima, el atacante puede comprometer completamente su interacción con la aplicación.**

![[xss.png]]

# ¿Cuáles son los tipos de ataques XSS?

Hay tres tipos principales de ataques XSS. Estos son:

1. **XSS Reflejado**: Donde el script malicioso proviene de la solicitud HTTP actual.
2. **XSS Almacenado**: Donde el script malicioso proviene de la base de datos del sitio web.
3. **XSS Basado en DOM**: Donde la vulnerabilidad existe en el código del lado del cliente en lugar del código del lado del servidor.

--------------------------------------------------------------------------
## **XSS Reflejado**

El XSS reflejado es la variedad más simple de scripting entre sitios. Surge cuando una aplicación recibe datos en una solicitud HTTP e incluye esos datos en la respuesta inmediata de manera insegura.

Aquí hay un ejemplo simple de una vulnerabilidad XSS reflejada:

https://insecure-website.com/status?message=All+is+well

La aplicación no realiza ningún otro procesamiento de los datos, por lo que un atacante puede fácilmente construir un ataque como este:

https://insecure-website.com/status?message=<script>/_+Bad+stuff+here...+_/</script>

Si el usuario visita la URL construida por el atacante, el script del atacante se ejecuta en el navegador del usuario, en el contexto de la sesión del usuario con la aplicación. En ese punto, el script puede llevar a cabo cualquier acción y recuperar cualquier dato al que el usuario tenga acceso.

--------------------------------------------------------------------------
## **XSS Almacenado**

El XSS almacenado (también conocido como persistente o de segunda orden) surge cuando una aplicación recibe datos de una fuente no confiable e incluye esos datos en sus respuestas HTTP posteriores de manera insegura.

Los datos en cuestión pueden enviarse a la aplicación a través de solicitudes HTTP; por ejemplo, comentarios en una publicación de blog, apodos de usuario en una sala de chat o detalles de contacto en un pedido de cliente. En otros casos, los datos pueden llegar de otras fuentes no confiables; por ejemplo, una aplicación de correo web que muestra mensajes recibidos por SMTP, una aplicación de marketing que muestra publicaciones en redes sociales o una aplicación de monitoreo de red que muestra datos de paquetes de tráfico de red.

Aquí hay un ejemplo simple de una vulnerabilidad XSS almacenada. Una aplicación de tablón de mensajes permite a los usuarios enviar mensajes, que se muestran a otros usuarios:


```html
<p>Hello, this is my message!</p>
```

La aplicación no realiza ningún otro procesamiento de los datos, por lo que un atacante puede fácilmente enviar un mensaje que ataque a otros usuarios:

```html
<p><script>/* Bad stuff here... */</script></p>
```

--------------------------------------------------------------------------

## **XSS Basado en DOM**

El XSS basado en DOM (también conocido como DOM XSS) surge cuando una aplicación contiene algún JavaScript del lado del cliente que procesa datos de una fuente no confiable de manera insegura, generalmente escribiendo los datos de nuevo en el DOM.

En el siguiente ejemplo, una aplicación utiliza JavaScript para leer el valor de un campo de entrada y escribir ese valor en un elemento dentro del HTML:

```js
var search = document.getElementById('search').value;
var results = document.getElementById('results');
results.innerHTML = 'You searched for: ' + search;
```

Si el atacante puede controlar el valor del campo de entrada, puede fácilmente construir un valor malicioso que cause que su propio script se ejecute:

```html
You searched for: <img src=1 onerror='/* Bad stuff here... */'>
```

En un caso típico, el campo de entrada se llenaría a partir de parte de la solicitud HTTP, como un parámetro de la cadena de consulta de la URL, lo que permite al atacante entregar un ataque usando una URL maliciosa, de la misma manera que el XSS reflejado.

--------------------------------------------------------------------------
## **¿Para qué se puede usar XSS?**

Un atacante que explota una vulnerabilidad XSS normalmente puede:

- Suplantar o hacerse pasar por el usuario víctima.
- Realizar cualquier acción que el usuario pueda llevar a cabo.
- Leer cualquier dato al que el usuario pueda acceder.
- Capturar las credenciales de inicio de sesión del usuario.
- Realizar un desfiguramiento virtual del sitio web.
- Inyectar funcionalidades trojanas en el sitio web.

--------------------------------------------------------------------------

# **Impacto de las vulnerabilidades XSS**

El impacto real de un ataque XSS generalmente depende de la naturaleza de la aplicación, su funcionalidad y datos, y el estado del usuario comprometido. Por ejemplo:

- En una aplicación de solo presentación, donde todos los usuarios son anónimos y toda la información es pública, el impacto suele ser mínimo.
- En una aplicación que contiene datos sensibles, como transacciones bancarias, correos electrónicos o registros de salud, el impacto suele ser grave.
- Si el usuario comprometido tiene privilegios elevados dentro de la aplicación, entonces el impacto generalmente será crítico, permitiendo al atacante tomar el control total de la aplicación vulnerable y comprometer a todos los usuarios y sus datos.

--------------------------------------------------------------------------

# **Cómo encontrar y probar vulnerabilidades XSS**

La gran mayoría de las vulnerabilidades XSS se pueden encontrar de manera rápida y confiable utilizando el escáner de vulnerabilidades web de Burp Suite.

Probar manualmente XSS reflejado y almacenado normalmente implica enviar una entrada simple y única (como una cadena alfanumérica corta) en cada punto de entrada en la aplicación, identificando cada ubicación donde la entrada enviada se devuelve en las respuestas HTTP, y probando cada ubicación individualmente para determinar si una entrada adecuadamente elaborada puede ser utilizada para ejecutar JavaScript arbitrario. De esta manera, puedes determinar el contexto en el que ocurre el XSS y seleccionar una carga útil adecuada para explotarlo.

Probar manualmente XSS basado en DOM que surge de parámetros de URL implica un proceso similar: colocar una entrada simple y única en el parámetro, usar las herramientas de desarrollo del navegador para buscar esta entrada en el DOM, y probar cada ubicación para determinar si es explotable. Sin embargo, otros tipos de DOM XSS son más difíciles de detectar. Para encontrar vulnerabilidades basadas en DOM en entradas no basadas en URL (como `document.cookie`) o sumideros no basados en HTML (como `setTimeout`), no hay sustituto para revisar el código JavaScript, lo cual puede ser extremadamente laborioso. El escáner de vulnerabilidades web de Burp Suite combina análisis estático y dinámico de JavaScript para automatizar de manera confiable la detección de vulnerabilidades basadas en DOM.

# **Política de Seguridad de Contenidos (CSP)**

La Política de Seguridad de Contenidos (CSP) es un mecanismo del navegador que tiene como objetivo mitigar el impacto del XSS y otras vulnerabilidades. Si una aplicación que emplea CSP contiene un comportamiento similar al XSS, entonces la CSP puede obstaculizar o prevenir la explotación de la vulnerabilidad. A menudo, la CSP puede ser eludida para permitir la explotación de la vulnerabilidad subyacente.

# **Dangling markup injection**

La inyección de marcado pendiente es una técnica que se puede usar para capturar datos entre dominios en situaciones donde un exploit completo de XSS no es posible, debido a filtros de entrada u otras defensas. A menudo se puede explotar para capturar información sensible que es visible para otros usuarios, incluidos tokens CSRF que se pueden usar para realizar acciones no autorizadas en nombre del usuario.

# **Cómo prevenir los ataques XSS**

Prevenir el cross-site scripting es trivial en algunos casos, pero puede ser mucho más difícil dependiendo de la complejidad de la aplicación y las formas en que maneja los datos controlables por el usuario.

En general, prevenir efectivamente las vulnerabilidades XSS probablemente involucrará una combinación de las siguientes medidas:

1. **Filtrar entrada al llegar**: En el punto donde se recibe la entrada del usuario, filtra de manera estricta lo que se espera o es válido.
2. **Codificar datos en la salida**: En el punto donde se salen datos controlables por el usuario en las respuestas HTTP, codifica la salida para evitar que se interprete como contenido activo. Dependiendo del contexto de salida, esto puede requerir aplicar combinaciones de codificación HTML, URL, JavaScript y CSS.
3. **Usar encabezados de respuesta apropiados**: Para prevenir XSS en respuestas HTTP que no están destinadas a contener HTML o JavaScript, puedes usar los encabezados Content-Type y X-Content-Type-Options para asegurar que los navegadores interpreten las respuestas de la manera que deseas.
4. **Política de Seguridad de Contenidos**: Como última línea de defensa, puedes usar la Política de Seguridad de Contenidos (CSP) para reducir la gravedad de cualquier vulnerabilidad XSS que aún ocurra.

# Cheat sheet
<https://portswigger.net/web-security/cross-site-scripting/cheat-sheet>


