---
sticker: emoji//0032-fe0f-20e3
---
### **Lenguajes de Programación**

#### **Backend:**

El backend será desarrollado en **Python**, ya que es ideal para manejar los datos, procesar los logs y gestionar las alertas.

**Bibliotecas útiles en Python:**

- **Flask:** Para crear una API REST que pueda recibir logs, gestionar reglas de correlación y enviar alertas.
- **PyMongo:** Para interactuar con MongoDB, que será el núcleo del almacenamiento.
- **Loguru o logging:** Para gestionar el logging dentro del sistema.
- **Multiprocessing:** Para manejar múltiples fuentes de logs de forma paralela.

#### **Agentes de Recolección de Logs:**

- **Python o Go:** desarrollar los agentes en **Python** o **Go** para que los agentes sean más eficientes.


---

### **Almacenamiento de Logs con MongoDB**


---

### **Frameworks de Desarrollo Web**

#### **Backend (API para Web y App Móvil):**

- **Flask:** Para desarrollar las rutas de API que servirán tanto al frontend web como a la app móvil.
    - **Rutas para logs:** `/logs` para buscar logs, `/alerts` para mostrar las alertas.

#### **Frontend Web:**

- **Dash Plotly o React.js :**
    - Dash Plotly
    - React.js

#### **Aplicación Móvil:**

- React Native

**Notificaciones Push:**

- **Firebase Cloud Messaging (FCM):** Para enviar notificaciones push a la aplicación móvil cuando se generen alertas críticas. Esto te permitirá mantener a los usuarios informados en tiempo real.

**Ejemplo de flujo básico de alertas:**

1. El backend recibe logs y los procesa para detectar patrones sospechosos.
2. Si una regla de correlación es activada, el backend genera una alerta.
3. La alerta se inserta en MongoDB y se notifica al sistema de notificaciones push (como Firebase).
4. La app móvil recibe la notificación en tiempo real.


---

### **Notificaciones y Alertas en la App Móvil**

#### **Push Notifications (Tiempo Real):**

Las notificaciones push serán esenciales para alertar a los usuarios sobre eventos críticos en tiempo real.

- **Firebase Cloud Messaging (FCM):** Es el estándar más común y confiable para notificaciones push tanto en Android como en iOS. Permite enviar alertas desde tu servidor a la app móvil en cualquier dispositivo.
