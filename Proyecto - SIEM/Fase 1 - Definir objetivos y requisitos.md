---
sticker: emoji//1f947
---
### **Objetivos del Proyecto**

El objetivo principal de este proyecto es construir un Sistema de Gestión de Información y Eventos de Seguridad (SIEM) básico a medio, que permita la recolección, normalización, almacenamiento, correlación y visualización de eventos y logs provenientes de diversas fuentes. Este SIEM ofrecerá capacidades de análisis de eventos de seguridad, generación de alertas en tiempo real y análisis forense básico.

###### ¿Qué tipos de eventos monitorear?

- **Logs de Servidores**:
    - **Autenticación**: Eventos relacionados con inicios de sesión exitosos, fallidos o bloqueos de cuenta.
    - **Errores de sistema**: Caídas de servicios, problemas de hardware o software.
    - **Accesos no autorizados**: Intentos de acceso a áreas restringidas del servidor.
    
- **Logs de Aplicaciones**:
    - **Errores y excepciones**: Cualquier error en las aplicaciones críticas que pueda comprometer el funcionamiento normal.
    - **Accesos**: Accesos de usuarios a las aplicaciones y posibles intentos de escalación de privilegios.
    - **Manipulación de datos**: Cambios en bases de datos, integridad de los datos, y modificaciones no autorizadas.
    
- **Eventos de Red**:
    - **Tráfico inusual**: Análisis de tráfico de red, como picos de tráfico inusuales, detección de escaneos de puertos, o tráfico malicioso.
    - **IPS/IDS Logs**: Logs de sistemas de prevención o detección de intrusiones que indiquen intentos de ataques o accesos no autorizados.
    - **Conexiones**: Conexiones externas, como VPN o túneles, especialmente las que provienen de ubicaciones geográficas no habituales.

- **Eventos de Seguridad (Firewalls, IPS/IDS)**:
    - **Cortafuegos**: Bloqueos de paquetes, cambios en las reglas del cortafuegos.
    
- **Logs de dispositivos físicos (IoT, dispositivos de red)**:
    - **Dispositivos IoT**: Logs de cámaras de seguridad, sensores de movimiento, termostatos inteligentes, etc.
    - **Dispositivos de red**: Logs de switches, routers, APs que indiquen fallos o accesos no autorizados.


---

##### ¿Cuántas fuentes de logs se van recolectar?

Las fuentes de logs dependerán del tamaño del entorno que vas a monitorear.

- **Dispositivos**:
    - 5-10 servidores (Linux, Windows).
    - 3-5 dispositivos de red (firewalls, routers, switches).
    - 2-3 dispositivos IoT o de seguridad física (cámaras, sensores).

- **Aplicaciones**:
    - Aplicaciones críticas (por ejemplo, servidores web Apache/Nginx, bases de datos como MySQL/PostgreSQL, etc.).

- **Clientes/Usuarios**:
    - 5-10 equipos de trabajo (PCs, laptops).
    - Usuarios autenticados en la red o sistemas.

En total, esto puede equivaler a 10-20 fuentes de logs diferentes.


---

##### ¿Qué capacidad de almacenamiento necesito a corto y mediano plazo?

La capacidad de almacenamiento para logs dependerá de la cantidad de eventos generados diariamente y del tiempo que quiera retener esos logs.

- **Corto plazo (6 meses)**:
    - Asumiendo que cada fuente genera aproximadamente entre 100-500 MB de logs al día, puedes esperar entre 1 GB y 10 GB diarios en total.
    - Para una retención de 6 meses, necesitarías entre 180 GB y 1.8 TB de almacenamiento, dependiendo del volumen de logs.

- **Mediano plazo (1 año)**:
    - Para una retención de un año, necesitarías entre 365 GB y 3.6 TB.


---

#### ¿Qué tipo de alertas implementar?

##### **1. Alertas críticas de seguridad:**
- **Intentos de acceso fallidos consecutivos:** Si hay más de 5 intentos fallidos de inicio de sesión en 10 minutos, esto puede indicar un ataque de fuerza bruta.
- **Elevación de privilegios no autorizada:** Intentos fallidos o no autorizados de usar comandos como `sudo` en Linux o intentos de escalación de privilegios en Windows.
- **Detección de malware/virus:** Identificación y cuarentena de archivos maliciosos basados en firmas o comportamientos sospechosos.
- **Intentos de acceso fuera del horario laboral:** Detección de intentos de iniciar sesión en horarios no comunes (por ejemplo, fuera de horas laborales).
- **Creación de cuentas no autorizadas:** Detección de creación de cuentas sin la debida justificación o fuera de procesos aprobados.

##### **2. Alertas de disponibilidad del sistema:**
- **Falta de logs de servicios críticos:** Si un servicio importante deja de enviar logs durante más de 10 minutos, puede indicar una caída o fallo del servicio.
- **Caídas de red o pérdida de conectividad:** Detección de desconexiones de red, fallos de routers, switches u otros componentes críticos de red.
- **Bajo rendimiento de sistemas:** Alertas si un sistema clave está utilizando recursos de manera anormal, como CPU o memoria excesiva.
- **Fallo de backups automáticos:** Si los respaldos programados fallan o no se completan, puede ser una señal de un problema mayor.

##### **3. Alertas de comportamiento inusual:**
- **Picos inusuales de tráfico de red:** Tráfico inusualmente alto o picos repentinos pueden indicar intentos de escaneo o ataque DDoS.
- **Escaneo de puertos no autorizado:** Detección de intentos de escaneo de puertos por parte de actores internos o externos.
- **Accesos desde ubicaciones geográficas no habituales:** Intentos de acceso desde un país o ubicación inesperada, lo que puede indicar un acceso comprometido.
- **Intentos de acceso a servicios no usados regularmente:** Detección de intentos de acceder a servicios o aplicaciones que no están activamente en uso, como servidores FTP inactivos.
- **Conexiones no habituales desde direcciones IP sospechosas:** Tráfico de red hacia o desde direcciones IP marcadas como maliciosas o sospechosas.

##### **4. Alertas de integridad de datos:**
- **Cambios no autorizados en archivos sensibles:** Detección de modificaciones en archivos críticos del sistema o bases de datos que no fueron aprobados.
- **Eliminación de archivos o datos críticos:** Alertas cuando se eliminan archivos importantes sin autorización.
- **Modificación de logs de auditoría:** Detección de intentos de modificar o borrar logs que son fundamentales para el seguimiento de actividades.
- **Acceso no autorizado a datos sensibles:** Intentos de leer, copiar o modificar datos sensibles sin los permisos adecuados.


---

### **Requisitos Técnicos**

##### Sistemas Operativos: ¿De dónde vendrán los logs?

### **1. Linux:**

Los sistemas Linux generan una variedad de logs que se almacenan en el directorio `/var/log/`.  los más importantes que a monitorear:

- **`/var/log/syslog` o `/var/log/messages`:** Contiene mensajes del sistema, incluyendo logs de inicio, fallos y mensajes generados por los procesos en ejecución.
- **`/var/log/auth.log` (o `secure` en algunas distribuciones):** Registra todos los eventos de autenticación, como intentos de inicio de sesión exitosos o fallidos (SSH, Sudo), así como la creación de nuevas cuentas o cambios de contraseñas.
- **`/var/log/dmesg`:** Contiene mensajes del kernel, incluyendo información sobre el hardware y eventos del sistema operativo durante el arranque.
- **`/var/log/faillog`:** Almacena información sobre los intentos de inicio de sesión fallidos.
- **`/var/log/boot.log`:** Registra los eventos del proceso de arranque del sistema.
- **`/var/log/cron.log`:** Logs de las tareas cron (trabajos programados).
- **`/var/log/apt/` (para distribuciones basadas en Debian/Ubuntu):** Guarda logs relacionados con la instalación, actualización y eliminación de paquetes.
- **Logs de aplicaciones específicas:** Algunas aplicaciones y servicios, como Apache, Nginx, MySQL, también generan sus propios logs en `/var/log/`.

**Distribuciones Linux:**

- **CentOS/RHEL:** Los logs más relevantes estarán en `syslog`, `secure`, `messages` y `dmesg`.
- **Ubuntu/Debian:** Utilizan principalmente `auth.log`, `syslog`, y `dmesg` para fines de auditoría y monitoreo.

### **2. Windows:**

En los sistemas Windows, los eventos de seguridad, sistema y aplicaciones se registran en el **Visor de Eventos** (Event Viewer), que puedes consultar utilizando herramientas como `eventvwr.msc`. Los logs clave a monitorear:

- **Log de seguridad:** Registra eventos relacionados con la autenticación, como intentos de inicio de sesión (éxitos y fallidos), cambios en los permisos de usuario, y uso de privilegios elevados.
- **Log del sistema:** Almacena eventos generados por el sistema operativo, como fallos de hardware, controladores, y eventos importantes del sistema.
- **Log de aplicaciones:** Guarda los eventos relacionados con el software que se ejecuta en la máquina, como errores de aplicaciones, actualizaciones, y advertencias.
- **Log de eventos de auditoría avanzada:** Configurado a través de políticas de grupo para rastrear eventos específicos, como cambios en la configuración del sistema, modificaciones de archivos y creación o eliminación de cuentas de usuario.
- **Log de eventos de PowerShell:** Registra todas las actividades de PowerShell, incluidas las ejecuciones de scripts, lo cual es importante en la detección de ataques basados en scripts.
- **Log de eventos de Active Directory (si aplica):** Registra eventos críticos relacionados con cambios en las políticas de acceso, modificaciones en grupos de seguridad, y movimientos de cuentas de usuario dentro de un entorno de AD.

### **3. Logs de autenticación, permisos de usuario y cambios de configuración del sistema:**

**Linux:**

- **Autenticación:** `auth.log` (o `secure`) y `/var/log/faillog` son los principales archivos para monitorear intentos de inicio de sesión y cambios en los permisos de usuario.
- **Permisos de usuario:** Los eventos relacionados con el uso de sudo se registran en `auth.log`, donde se pueden ver intentos de escalamiento de privilegios.
- **Cambios de configuración:** Cualquier cambio en los archivos de configuración del sistema, como archivos en `/etc/` (por ejemplo, `passwd` o `shadow`), se puede monitorear utilizando herramientas de integridad de archivos como `auditd` o `tripwire`.

**Windows:**

- **Autenticación:** El log de seguridad contiene eventos de inicio de sesión exitosos y fallidos, y cambios en las políticas de autenticación.
- **Permisos de usuario:** Eventos de modificación de permisos de archivos y carpetas se registran en el log de seguridad.
- **Cambios de configuración del sistema:** Los cambios críticos de configuración del sistema, incluyendo actualizaciones de políticas y configuraciones del registro, se registran en los logs de seguridad y sistema.

---

### **Protocolos de Comunicación: ¿Cómo recolectar los logs?**

Definir cómo los agentes y servidores del SIEM recibirán los logs.

### **1. Syslog (Unix/Linux):**

Syslog es el protocolo estándar para la recolección y transmisión de logs en sistemas Unix y Linux.

**Alternativas a Syslog:**

- **Filebeat:** Utiliza Filebeat para recoger y enviar logs de archivos específicos a un servidor SIEM. Es útil para recolectar logs de aplicaciones personalizadas.
- **Auditd:** En Linux, `auditd` puede ser utilizado para recolectar y registrar eventos específicos del sistema y la seguridad, que luego pueden ser enviados a través de Syslog o directamente a un SIEM.
- **Journalbeat:** Para sistemas que utilizan `systemd`, `journalbeat` puede ser utilizado para enviar logs del journal de `systemd` a un SIEM.

### **2. Event Forwarding (Windows):**

En entornos Windows.

**Windows Event Forwarding (WEF):**

- **Configuración del Servidor:** Configura un servidor de recopilación de eventos (Event Collector) para recibir eventos de los servidores Windows. Puedes hacerlo a través del Event Viewer (Visor de Eventos) en la sección de “Subscriptions” (Suscripciones).
- **Configuración del Cliente:** Configura los servidores para enviar eventos al servidor de recopilación. Esto se puede hacer mediante el comando `wecutil` o mediante directivas de grupo (Group Policy).
- **Configuración de Eventos:** Especifica qué tipos de eventos quieres recopilar (seguridad, sistema, aplicaciones) y define el rango de tiempo y otras opciones.
- **Verificación:** Revisa los logs en el servidor de recopilación para asegurarte de que los eventos están siendo correctamente recibidos y enviados al servidor SIEM.

**Agentes como Winlogbeat:**

- Instala Winlogbeat en los servidores Windows. Winlogbeat es un agente que envía logs de eventos de Windows a un servidor SIEM o a un procesador de logs como Elasticsearch.

- **Alternativas a WEF y Winlogbeat:**
    - **NXLog:** Una solución flexible que soporta la recolección y envío de logs desde Windows a un SIEM. Puede manejar una variedad de formatos de logs y protocolos.
    - **LogRhythm:** Ofrece soluciones específicas para Windows que pueden integrarse con el SIEM para recolectar logs y eventos.

### **3. Agentes Personalizados:**

Para aplicaciones o sistemas que no soportan Syslog o Event Forwarding.

- **Agentes Personalizados:**
    
    - **Desarrollo:** Desarrolla un agente que recoja los logs de la aplicación y los envíe al SIEM. Este agente puede ser escrito en varios lenguajes, como Python, Java, o Go, y debe incluir funcionalidades para la recolección, formateo y envío de logs.
    - **Transmisión de Logs:** Puedes usar protocolos como HTTP/HTTPS, TCP, o UDP para enviar los logs al servidor SIEM. Asegúrate de que el formato de los logs es compatible con lo que el SIEM espera.
    - **Configuración:** Configura el agente para que se ejecute como un servicio o tarea programada en el sistema operativo de origen.
    - **Verificación:** Monitorea la salida del agente y el SIEM para verificar que los logs están siendo enviados y procesados correctamente.


---

### Almacenamiento de logs: ¿Dónde guardar los logs?

### **1. Base de Datos NoSQL (MongoDB):**

- **Ventajas:**
    - **Flexibilidad de Datos:** MongoDB es ideal para almacenar logs no estructurados y semi-estructurados. Puedes adaptar el esquema a medida que cambian los requisitos del sistema.
    - **Escalabilidad Horizontal:** Permite escalar fácilmente agregando más nodos, lo cual es útil a medida que el volumen de logs crece.
    - **Consultas Rápidas:** Ofrece buenas capacidades de consulta.

- **Desventajas:**    
    - **Consistencia Eventual:** Aunque MongoDB es generalmente adecuado para logs, su modelo de consistencia eventual podría no ser ideal para todas las aplicaciones, aunque para logs no suele ser un problema crítico.


---

### Alertas y correlación: ¿Qué tipo de reglas de correlación implementar inicialmente?

Definir reglas básicas de correlación que detecten patrones sospechosos en los logs.

### **1. Reglas de Autenticación:**

Estas reglas se enfocan en detectar intentos sospechosos de acceso no autorizado y comportamientos anómalos en los patrones de inicio de sesión.

- **Intentos fallidos consecutivos:**
    
    - **Regla:** Si un usuario realiza más de **x** intentos de autenticación fallidos en un período de **y** minutos, genera una alerta.
    - **Motivo:** Este patrón puede indicar intentos de fuerza bruta para comprometer una cuenta.

- **Inicios de sesión desde múltiples ubicaciones geográficas:**
    
    - **Regla:** Si un usuario intenta autenticarse desde varias ubicaciones geográficas diferentes en un corto período de tiempo (por ejemplo, en cuestión de minutos o horas), genera una alerta.
    - **Motivo:** Podría indicar una actividad sospechosa, como un posible compromiso de credenciales y uso de las mismas desde ubicaciones remotas.

- **Inicio de sesión fuera de horas laborales:**
    
    - **Regla:** Si un usuario accede a sistemas críticos fuera del horario laboral definido, genera una alerta.
    - **Motivo:** Actividades fuera de horas usuales pueden señalar accesos malintencionados o negligentes.

### **2. Reglas de Tráfico de Red:**

Estas reglas buscan identificar patrones inusuales en el tráfico de red que podrían ser señales de ataques o intentos de reconocimiento.

- **Escaneo de puertos:**
    
    - **Regla:** Si se detectan más de **x** conexiones fallidas a un servicio específico en un corto período de tiempo (por ejemplo, en cuestión de segundos o minutos), genera una alerta sobre un posible escaneo de puertos.
    - **Motivo:** Los escaneos de puertos a menudo son el primer paso en la búsqueda de vulnerabilidades en un sistema.

- **Posible ataque DDoS:**
    
    - **Regla:** Si el tráfico de red en un puerto o servicio específico excede un umbral definido (por ejemplo, un pico inusual de solicitudes en un puerto), genera una alerta de posible ataque DDoS.
    - **Motivo:** Un aumento repentino en el tráfico puede ser un indicio de un ataque de denegación de servicio.

- **Acceso a servicios no utilizados:**
    
    - **Regla:** Si hay intentos de conexión a servicios no activos o deshabilitados en el sistema, genera una alerta.
    - **Motivo:** Esto podría ser indicativo de alguien intentando explotar servicios que deberían estar desactivados.

### **3. Reglas de Disponibilidad del Sistema:**

Estas reglas ayudan a detectar caídas o problemas de disponibilidad en los servicios y servidores monitoreados.

- **Falta de logs de un servicio crítico:**
    
    - **Regla:** Si un servicio crítico (por ejemplo, servidor web, base de datos) deja de generar logs por más de **x** minutos, genera una alerta.
    - **Motivo:** Esto podría ser indicativo de una caída del sistema o un fallo en el servicio.

- **Falta de respuesta de un servidor:**
    
    - **Regla:** Si un servidor deja de responder o deja de generar logs por más de **x** minutos, alerta sobre un posible fallo de hardware o ataque.
    - **Motivo:** Un fallo de hardware, ataque de denegación de servicio o incluso problemas de red podrían ser la causa de la falta de respuesta.

- **Servicios que superan los umbrales de uso:**
    
    - **Regla:** Si un servicio crítico (como el uso de CPU, memoria, o tráfico de red) excede un umbral predefinido, genera una alerta.
    - **Motivo:** Puede indicar problemas de rendimiento, sobrecarga, o posible ataque.

### **4. Reglas de Modificación de Archivos:**

Estas reglas se centran en detectar cambios sospechosos en archivos críticos del sistema o de configuración.

- **Modificación de archivos sensibles:**
    
    - **Regla:** Si un archivo crítico en el sistema (por ejemplo, en **/etc/** en Linux) es modificado sin los permisos correctos o fuera de los procedimientos normales, genera una alerta.
    - **Motivo:** Modificaciones no autorizadas pueden ser indicativas de un compromiso del sistema o de una escalación de privilegios.
- **Cambios en archivos de configuración del sistema:**
    
    - **Regla:** Si se detectan cambios en archivos de configuración sensibles (por ejemplo, archivos de configuración del firewall o del servidor), genera una alerta.
    - **Motivo:** Cambios no autorizados podrían debilitar la seguridad del sistema y exponerlo a ataques.
- **Eliminación de archivos importantes:**
    
    - **Regla:** Si un archivo importante (por ejemplo, un log crítico o archivo de configuración) es eliminado sin autorización, genera una alerta.
    - **Motivo:** Esto puede señalar una tentativa de ocultar actividad maliciosa o interrumpir el funcionamiento del sistema.

### **5. Otras Reglas Iniciales:**

- **Elevación de privilegios:**
    
    - **Regla:** Si un usuario sin autorización intenta elevar sus privilegios (por ejemplo, usando comandos como `sudo` en Linux o intentos de modificación en la configuración de roles en Windows), genera una alerta.
    - **Motivo:** Un intento no autorizado de elevación de privilegios puede ser una señal de que alguien está tratando de ganar control adicional sobre el sistema.

- **Activación de cuentas deshabilitadas:**
    
    - **Regla:** Si una cuenta deshabilitada o eliminada intenta ser utilizada o autenticada, genera una alerta.
    - **Motivo:** Esto puede señalar intentos de acceso no autorizado mediante cuentas obsoletas o comprometidas.

Estas reglas de correlación iniciales proporcionan un punto de partida para identificar comportamientos sospechosos, ataques en progreso, o fallos en los sistemas que estás monitoreando. Es importante recordar que las reglas de correlación deben evolucionar y adaptarse conforme entiendas mejor los patrones de comportamiento en tu entorno y las amenazas emergentes.

