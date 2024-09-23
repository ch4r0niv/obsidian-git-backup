---
sticker: emoji//0034-fe0f-20e3
---

# LogCollector

Características Adicionales para `LogCollector`

**Configuración de Intervalos**:

- Intervalo de espera configurable.

**Detección de Archivos Rotados**:

- Detectar si el archivo ha sido rotado. Verificar si el `file_handle` genera un `FileNotFoundError` o si el tamaño del archivo cambia.
