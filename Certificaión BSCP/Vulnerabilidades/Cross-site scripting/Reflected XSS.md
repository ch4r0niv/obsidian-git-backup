# ¿Que es el Reflected XSS?

El cross-site scripting reflejado (o XSS reflejado) ocurre cuando una aplicación recibe datos en una solicitud HTTP e incluye esos datos en la respuesta inmediata de manera insegura.

# Impacto de los ataques de XSS reflejado

Si un atacante puede controlar un script que se ejecuta en el navegador de la víctima, entonces generalmente puede comprometer por completo a ese usuario. Entre otras cosas, el atacante puede:

- Realizar cualquier acción dentro de la aplicación que el usuario pueda realizar.
- Ver cualquier información que el usuario pueda ver.
- Modificar cualquier información que el usuario pueda modificar.
- Iniciar interacciones con otros usuarios de la aplicación, incluidos ataques maliciosos, que parecerán originarse del usuario víctima original.

Existen varias formas en las que un atacante podría inducir a un usuario víctima a realizar una solicitud que controlen, para llevar a cabo un ataque de XSS reflejado. Estas incluyen colocar enlaces en un sitio web controlado por el atacante, o en otro sitio web que permita la generación de contenido, o enviando un enlace en un correo electrónico, tuit u otro mensaje. El ataque podría dirigirse directamente contra un usuario conocido o podría ser un ataque indiscriminado contra cualquier usuario de la aplicación.

La necesidad de un mecanismo externo para entregar el ataque significa que el impacto del XSS reflejado generalmente es menos grave que el del XSS almacenado, donde un ataque autónomo puede ser entregado dentro de la propia aplicación vulnerable.

--------------------------------------------------------------------------
# XSS reflejado en diferentes contextos

Existen muchas variedades de cross-site scripting reflejado. La ubicación de los datos reflejados dentro de la respuesta de la aplicación determina qué tipo de payload se requiere para explotarlo y también puede afectar el impacto de la vulnerabilidad.

Además, si la aplicación realiza alguna validación u otro tipo de procesamiento en los datos enviados antes de reflejarlos, esto generalmente afectará el tipo de payload de XSS que se necesita.

--------------------------------------------------------------------------

# Cómo encontrar y probar vulnerabilidades de XSS reflejado

La gran mayoría de las vulnerabilidades de XSS reflejado se pueden encontrar de manera rápida y confiable utilizando el escáner de vulnerabilidades web de Burp Suite.

# Pruebas manuales para detectar vulnerabilidades de XSS reflejado

Para probar manualmente vulnerabilidades de XSS reflejado, sigue los siguientes pasos:

1. **Prueba cada punto de entrada**: Verifica por separado cada punto de entrada de datos en las solicitudes HTTP de la aplicación. Esto incluye parámetros u otros datos dentro de la cadena de consulta de la URL y el cuerpo del mensaje, así como la ruta del archivo en la URL. También incluye encabezados HTTP, aunque el comportamiento similar a XSS que solo puede ser desencadenado a través de ciertos encabezados HTTP puede no ser explotable en la práctica.

3. **Envía valores alfanuméricos aleatorios**: Para cada punto de entrada, envía un valor aleatorio único y determina si ese valor se refleja en la respuesta. El valor debe estar diseñado para sobrevivir a la mayoría de las validaciones de entrada, por lo que debe ser relativamente corto y contener solo caracteres alfanuméricos. Pero también debe ser lo suficientemente largo para hacer que las coincidencias accidentales dentro de la respuesta sean muy poco probables. Un valor alfanumérico aleatorio de aproximadamente 8 caracteres es normalmente ideal. Puedes usar los payloads numéricos de Burp Intruder con valores hexadecimales generados aleatoriamente para generar valores aleatorios adecuados. Y puedes usar la configuración de grep payloads de Burp Intruder para marcar automáticamente las respuestas que contienen el valor enviado.

3. **Determina el contexto de la reflexión**: Para cada ubicación dentro de la respuesta donde se refleja el valor aleatorio, determina su contexto. Esto podría estar en texto entre etiquetas HTML, dentro de un atributo de etiqueta que podría estar entre comillas, dentro de una cadena de JavaScript, etc.

4. **Prueba un payload candidato**: Basado en el contexto de la reflexión, prueba un payload XSS candidato inicial que activará la ejecución de JavaScript si se refleja sin modificaciones en la respuesta. La forma más fácil de probar payloads es enviar la solicitud a Burp Repeater, modificar la solicitud para insertar el payload candidato, emitir la solicitud y luego revisar la respuesta para ver si el payload funcionó. Una forma eficiente de trabajar es dejar el valor aleatorio original en la solicitud y colocar el payload XSS candidato antes o después de él. Luego, configura el valor aleatorio como el término de búsqueda en la vista de respuesta de Burp Repeater. Burp resaltará cada ubicación donde aparece el término de búsqueda, permitiéndote localizar rápidamente la reflexión.

5. **Prueba payloads alternativos**: Si la aplicación modificó o bloqueó el payload XSS candidato, entonces necesitarás probar payloads y técnicas alternativas que puedan generar un ataque XSS exitoso basado en el contexto de la reflexión y el tipo de validación de entrada que se esté realizando. Para más detalles, consulta los **contextos de cross-site scripting**.

6. **Prueba el ataque en un navegador**: Finalmente, si logras encontrar un payload que parece funcionar dentro de Burp Repeater, transfiere el ataque a un navegador real (pegando la URL en la barra de direcciones o modificando la solicitud en la vista de interceptación de Burp Proxy) y verifica si el JavaScript inyectado se ejecuta. A menudo, es mejor ejecutar un JavaScript simple como `alert(document.domain)` que activará una ventana emergente visible en el navegador si el ataque tiene éxito.

----------------------------------------------

### ¿Cuál es la diferencia entre XSS reflejado y XSS almacenado?

El XSS reflejado ocurre cuando una aplicación toma algún dato de una solicitud HTTP y lo inserta en la respuesta inmediata de manera insegura. En cambio, con el XSS almacenado, la aplicación guarda el dato y lo inserta en una respuesta posterior de manera insegura.

### ¿Cuál es la diferencia entre XSS reflejado y self-XSS?

El self-XSS (o XSS autoejecutado) implica un comportamiento de la aplicación similar al XSS reflejado normal, pero no se puede desencadenar de manera normal a través de una URL manipulada o una solicitud de otro dominio. En su lugar, la vulnerabilidad solo se activa si la propia víctima envía el payload de XSS desde su navegador. Realizar un ataque de self-XSS normalmente requiere engañar a la víctima para que pegue algún dato proporcionado por el atacante en su navegador. Por lo tanto, generalmente se considera un problema de bajo impacto y poco significativo.
