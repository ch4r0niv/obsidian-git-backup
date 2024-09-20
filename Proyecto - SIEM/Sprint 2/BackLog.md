### **Backlog del Sprint 2 (Recolección y Parsing de Logs)**

#### **1. Desarrollar el Agente de Recolección de Logs**

- **Tarea 1.1:** Investigar y seleccionar una biblioteca para la recolección de logs en Linux (p. ej., `rsyslog`, `syslog-ng`).
- **Tarea 1.2:** Implementar un script en Python para recolectar logs de Syslog en Linux.
- **Tarea 1.3:** Testear la recolección de logs desde un sistema Linux y asegurar que se envían al servidor.

#### **2. Desarrollar el Agente de Recolección de Logs para Windows**

- **Tarea 2.1:** Investigar cómo utilizar Winlogbeat o Event Forwarding para recolectar logs en Windows.
- **Tarea 2.2:** Configurar y probar Winlogbeat para enviar logs al servidor.
- **Tarea 2.3:** Asegurarse de que los logs recolectados estén en un formato estandarizado.

#### **3. Configurar el Servidor para Recepción de Logs**

- **Tarea 3.1:** Configurar el servidor para recibir logs desde los agentes (puerto, protocolo).
- **Tarea 3.2:** Implementar un endpoint en el servidor que reciba los logs.
- **Tarea 3.3:** Probar la conexión entre los agentes y el servidor, asegurando que los logs se reciben correctamente.

#### **4. Implementar el Sistema de Parsing de Logs**

- **Tarea 4.1:** Diseñar el formato JSON para almacenar los logs en MongoDB.
- **Tarea 4.2:** Implementar la lógica de parsing para convertir logs de texto en JSON.
- **Tarea 4.3:** Testear el sistema de parsing con diferentes formatos de logs y validar la salida.

#### **5. Pruebas y Validación**

- **Tarea 5.1:** Realizar pruebas de integración para verificar que los logs se recolectan y parsean correctamente.
- **Tarea 5.2:** Documentar el flujo de recolección y parsing en Obsidian.
- **Tarea 5.3:** Resolver cualquier error o bloqueo que surja durante las pruebas.

### **Priorización**

- **Alta Prioridad:** Tareas relacionadas con la recolección de logs y la configuración del servidor.
- **Media Prioridad:** Implementación del sistema de parsing y pruebas.
- **Baja Prioridad:** Documentación y revisión.

### **Asignación de Responsabilidades**

Cada miembro del equipo puede tomar tareas específicas según sus habilidades e intereses. Por ejemplo:

- Un miembro podría enfocarse en el agente de Linux, mientras que otro se encargue del de Windows.
- Un tercero podría trabajar en la configuración del servidor y el cuarto en el sistema de parsing.