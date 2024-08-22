# ¿Qué es el Cross-Site Scripting (XSS) basado en DOM?

Las vulnerabilidades de XSS basadas en DOM suelen surgir cuando JavaScript toma datos de una fuente controlable por el atacante, como la URL, y los pasa a un destino que admite la ejecución dinámica de código, como `eval()` o `innerHTML`. Esto permite a los atacantes ejecutar JavaScript malicioso, lo que típicamente les permite secuestrar las cuentas de otros usuarios.

Para llevar a cabo un ataque de XSS basado en DOM, necesitas colocar datos en una fuente para que se propaguen a un destino y causen la ejecución de JavaScript arbitrario.

La fuente más común para XSS basado en DOM es la URL, que generalmente se accede con el objeto `window.location`. Un atacante puede construir un enlace para enviar a una víctima a una página vulnerable con un payload en la cadena de consulta y en las partes del fragmento de la URL. En ciertas circunstancias, como al apuntar a una página 404 o a un sitio web que ejecuta PHP, el payload también puede colocarse en la ruta.

Para una explicación detallada del flujo de contaminación entre fuentes y destinos, consulta la página de vulnerabilidades basadas en DOM.

--------------------------------------------------------------------------
## Cómo probar las vulnerabilidades de XSS basadas en DOM

La mayoría de las vulnerabilidades de XSS basadas en DOM se pueden encontrar de manera rápida y fiable utilizando el escáner de vulnerabilidades web de Burp Suite. Para probar manualmente el XSS basado en DOM, generalmente necesitas usar un navegador con herramientas de desarrollador, como Chrome. Necesitas trabajar a través de cada fuente disponible por turnos y probar cada una individualmente.

--------------------------------------------------------------------------
## Pruebas de destinos HTML

Para probar XSS basado en DOM en un destino HTML, coloca una cadena alfanumérica aleatoria en la fuente (como `location.search`), luego usa las herramientas de desarrollador para inspeccionar el HTML y encontrar dónde aparece tu cadena. Ten en cuenta que la opción "Ver fuente" del navegador no funcionará para pruebas de XSS basado en DOM porque no tiene en cuenta los cambios que se han realizado en el HTML por JavaScript. En las herramientas de desarrollador de Chrome, puedes usar `Control+F` (o `Command+F` en MacOS) para buscar la cadena en el DOM.

Para cada ubicación donde aparece tu cadena en el DOM, necesitas identificar el contexto. Basado en este contexto, debes refinar tu entrada para ver cómo se procesa. Por ejemplo, si tu cadena aparece dentro de un atributo entre comillas dobles, intenta inyectar comillas dobles en tu cadena para ver si puedes salir del atributo.

Ten en cuenta que los navegadores se comportan de manera diferente con respecto al URL-encoding. Chrome, Firefox y Safari URL-encodearán `location.search` y `location.hash`, mientras que IE11 y Microsoft Edge (pre-Chromium) no URL-encodearán estas fuentes. Si tus datos se URL-encoded antes de ser procesados, entonces es poco probable que un ataque de XSS funcione.

--------------------------------------------------------------------------
## Pruebas de destinos de ejecución de JavaScript

Probar destinos de ejecución de JavaScript para XSS basado en DOM es un poco más difícil. Con estos destinos, tu entrada no necesariamente aparece en el DOM, por lo que no puedes buscarla. En su lugar, necesitarás usar el depurador de JavaScript para determinar si y cómo tu entrada se envía a un destino.

Para cada fuente potencial, como `location`, primero necesitas encontrar casos dentro del código JavaScript de la página donde se referencia la fuente. En las herramientas de desarrollador de Chrome, puedes usar `Control+Shift+F` (o `Command+Alt+F` en MacOS) para buscar en todo el código JavaScript de la página.

Una vez que hayas encontrado dónde se lee la fuente, puedes usar el depurador de JavaScript para agregar un punto de interrupción y seguir cómo se usa el valor de la fuente. Podrías descubrir que la fuente se asigna a otras variables. Si este es el caso, necesitarás usar la función de búsqueda nuevamente para rastrear estas variables y ver si se pasan a un destino. Cuando encuentres un destino al que se le asignan datos que se originaron en la fuente, puedes usar el depurador para inspeccionar el valor pasando el cursor sobre la variable para mostrar su valor antes de que se envíe al destino. Luego, al igual que con los destinos HTML, debes refinar tu entrada para ver si puedes entregar un ataque XSS exitoso.

--------------------------------------------------------------------------
## Pruebas de XSS basado en DOM usando DOM Invader

Identificar y explotar XSS basado en DOM en la práctica puede ser un proceso tedioso, a menudo requiriendo que examines manualmente JavaScript complejo y minimizado. Sin embargo, si usas el navegador de Burp, puedes aprovechar la extensión DOM Invader incorporada, que realiza gran parte del trabajo duro por ti.

--------------------------------------------------------------------------
# Exploiting

