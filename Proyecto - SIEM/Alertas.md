### 1. **Alertas basadas en firmas (Signature-based alerts):**

- **Detección de malware conocido:** Basado en patrones de firmas conocidas de malware.
- **Detección de ataques de red (IDS/IPS):** Activado cuando se detecta una firma de ataque de red específica (como una vulnerabilidad o exploit conocido).
- **Ataques de denegación de servicio (DDoS):** Detección de tráfico anómalo que corresponde a ataques de DDoS.

### 2. **Alertas basadas en anomalías (Anomaly-based alerts):**

- **Comportamiento inusual de usuario (UBA):** Detección de actividades anormales de usuarios, como accesos inusuales fuera de horario o a recursos no comunes.
- **Anomalías de red:** Actividades de red fuera de lo común, como aumento en el tráfico no esperado.
- **Accesos no autorizados o múltiples intentos fallidos:** Alertas por intentos de acceso no autorizados repetidos.

### 3. **Alertas basadas en correlación de eventos:**

- **Acceso simultáneo desde múltiples ubicaciones:** Si un usuario inicia sesión desde dos ubicaciones geográficas distantes en un corto período de tiempo.
- **Combinación de eventos sospechosos:** Un SIEM puede correlacionar múltiples eventos que por sí solos no parecen sospechosos, pero juntos forman un patrón de ataque (por ejemplo, un escaneo de puertos seguido de una elevación de privilegios).
### 4. **Alertas de integridad de archivos (FIM - File Integrity Monitoring):**

- **Cambios no autorizados en archivos críticos:** Alertas cuando archivos importantes del sistema o de la empresa son modificados, agregados o eliminados sin autorización.

### 5. **Alertas de comportamiento de red:**

- **Escaneos de puertos:** Detección de un escaneo de puertos realizado por un atacante en busca de vulnerabilidades.
- **Comunicaciones con dominios sospechosos:** Si se detecta tráfico a dominios o IPs marcados como maliciosos.
- **Túneles de datos o tráfico cifrado sospechoso:** Tráfico que intenta ocultarse mediante técnicas de túnel o cifrado inusual.

### 6. **Alertas de cumplimiento (Compliance-based alerts):**

- **Accesos no autorizados a datos sensibles:** Detección de intentos de acceso a información confidencial sin los permisos adecuados.
- **Falta de logs o logs incompletos:** Cuando los dispositivos no están enviando logs o se detecta que los logs han sido alterados o eliminados.

### 7. **Alertas de actividad de privilegios:**

- **Elevación de privilegios:** Detecta si un usuario cambia su nivel de acceso a uno más alto sin autorización.
- **Creación de cuentas privilegiadas:** Se genera una alerta si se detecta la creación de cuentas con acceso de administrador u otros permisos elevados sin la debida justificación.

### 8. **Alertas de vulnerabilidades:**

- **Explotación de vulnerabilidades conocidas:** Alertas generadas cuando un atacante intenta explotar una vulnerabilidad identificada en el sistema.
- **Actualizaciones o parches faltantes:** Detección de sistemas que no tienen las actualizaciones de seguridad más recientes aplicadas.

### 9. **Alertas de malware:**

- **Detección de ransomware:** Identificación de comportamientos que indican una posible infección por ransomware.
- **Ataques de phishing:** Alerta generada cuando se detecta un intento de ataque de phishing o un correo malicioso.

### 10. **Alertas de actividad sospechosa de aplicaciones:**

- **Aplicaciones que se ejecutan fuera del comportamiento esperado:** Detección de aplicaciones que muestran comportamientos fuera de lo común o sospechosos (por ejemplo, un servidor web que comienza a ejecutar comandos de shell).
- **Inyecciones de código o SQL:** Detección de intentos de inyección de código en aplicaciones web o bases de datos.

### 11. **Alertas de disponibilidad:**

- **Caída o fallo de sistemas críticos:** Genera alertas si un sistema crítico (servidor, aplicación, etc.) no está disponible o está fallando.
- **Alto uso de recursos:** Alerta cuando se detecta un uso anormal de CPU, memoria o disco en sistemas críticos.


> Estas alertas, una vez generadas, son revisadas por el equipo de seguridad o el SOC (Security Operations Center) para determinar si requieren acción inmediata o si son falsos positivos. Dependiendo de la configuración del SIEM y las políticas de seguridad de la organización, las alertas pueden ser automatizadas o requerir una intervención manual.


