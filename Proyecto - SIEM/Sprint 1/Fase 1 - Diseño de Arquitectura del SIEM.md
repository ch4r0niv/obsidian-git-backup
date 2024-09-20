---
sticker: emoji//1f6e1-fe0f
---
### Diagrama de Arquitectura

1. **Recolección de Logs**:
    
    - Los agentes instalados en sistemas como Linux y Windows (usando Syslog, Winlogbeat, u otros) y dispositivos de red recopilan los eventos de los sistemas. Estos agentes actúan como intermediarios entre el SIEM y los logs que se generan en diferentes máquinas y servicios.
    - Los logs pueden incluir información como intentos de inicio de sesión, accesos fallidos, cambios en archivos o configuraciones, actividad de red, entre otros.
    - Los agentes envían los logs al servidor central del SIEM para ser procesados.

1. **Parsing y Normalización**:
    
    - Cuando los logs llegan al servidor central, se realiza una transformación en tiempo real.
    - Los formatos de logs pueden variar según el sistema (Windows, Linux, aplicaciones), por lo que se aplica un proceso de normalización. Este proceso convierte todos los logs en un formato estándar y común (por ejemplo, JSON), lo que facilita la posterior correlación y almacenamiento.
    - Durante esta fase, se extraen campos importantes como IPs, timestamps, usuarios, etc., que serán necesarios para las reglas de correlación.

1. **Almacenamiento en MongoDB**:
    
    - Los logs ya normalizados se almacenan en MongoDB. Esta base de datos NoSQL es ideal para manejar grandes cantidades de logs de forma eficiente.
    - Los logs se organizan en colecciones y están indexados por campos importantes como timestamp, severidad, IP de origen, y tipo de evento. Esto permite una consulta eficiente en fases posteriores, como la búsqueda y el análisis.

1. **Motor de Correlación**:
    
    - El motor de correlación es el corazón del sistema SIEM. Aquí es donde se aplican reglas predefinidas para analizar patrones sospechosos.
    - Estas reglas incluyen la detección de intentos de acceso fallidos consecutivos, elevaciones de privilegios no autorizadas, picos inusuales de tráfico, o modificaciones de archivos sensibles.
    - Si los logs coinciden con una regla de correlación, se genera una alerta de seguridad. Este proceso puede ejecutarse en tiempo real para una respuesta rápida a posibles incidentes.

1. **Alertas y Notificaciones**:
    
    - Una vez que se genera una alerta, el sistema de notificaciones se encarga de enviarla a los administradores del sistema.
    - Las alertas se envían a través de diferentes canales. En el caso de la app móvil, se puede integrar con Firebase para gestionar las notificaciones push. Así, cualquier alerta crítica se enviará de inmediato a los dispositivos móviles de los administradores.
    - También pueden enviarse alertas a través del dashboard o por correo electrónico.

1. **Dashboard para Visualización**:
    
    - La interfaz web permite que los usuarios busquen, filtren y visualicen los logs y las alertas. Esta interfaz podría estar construida con tecnologías como React.js y Flask.
    - En el dashboard, se mostrarán gráficos y tablas con información relevante. Los administradores podrán visualizar tendencias, consultar logs históricos y ver las alertas activas.
    - La app móvil tendrá un sistema de alertas donde se mostrarán notificaciones importantes, manteniendo a los usuarios informados en todo momento.