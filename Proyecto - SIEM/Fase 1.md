#### **Objetivos del Proyecto**

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