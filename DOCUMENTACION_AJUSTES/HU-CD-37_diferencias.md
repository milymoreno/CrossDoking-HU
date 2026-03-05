# HU-CD-37 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-37 (Creación DO – Integración con Agencia de Aduanas)
**Fecha de revisión:** 2026-03-05
**Estado:** Aplicado según requerimientos de Deisy Rincón y Fabián Barragán (RQ 37)

---

## 1. Integración con Agencia (SIACO/FileZilla)

### Ajustes Realizados:
- **Retiro de API soe360grados**: Se documentó formalmente el apagado del endpoint legacy y cómo el SII asume sus funciones (GetToken, GetResource, GetOrderNumbers).
- **Flujo de Archivos `.prn`**: Se estableció el procedimiento de seguridad para descargar `Informacion_DIAN.prn` desde el SFTP de SIACO al FTP interno para mitigar riesgos.
- **Responsabilidades**: Se asignó a Fabián Barragán la definición del formato de carga y a Deisy Rincón/Cesar Forero la caracterización de los datos de servicios.

### Justificación:
Necesidad de modernizar la comunicación con la agencia de aduanas y eliminar dependencias de servicios externos (API soe360grados) que ya no son compatibles con el nuevo modelo de base de datos.

---

## 2. Envíos Courier

### Ajustes Realizados:
- Se añadió la vía de transporte **Courier** al sistema.
- Se definió que estas guías pueden seguir un flujo simplificado o exceptuarse de la creación de DO ordinaria si el trámite lo realiza directamente el transportador (DHL/FedEx).

### Justificación:
Evitar bloqueos operativos en el monitor de nacionalización para embarques pequeños que no siguen el flujo estándar de SIACO.

---

## 3. Impacto en el Modelo SQL

### Cambios en `modelo_crossdocking.sql`:
- **Tabla `comunicacion_agencia`**: Nueva tabla para registrar el log de intercambio de archivos (.prn, envíos DO).
- **Tabla `cat_via_transporte`**: Inserción del código 'C' para Courier.
- **Notas Legacy**: Inclusión de comentarios sobre las tablas `SIPDLRC0` y `UPPGUISE0` para facilitar el mapeo con AS/400.

---
*Documento generado por Antigravity para soporte de trazabilidad de requerimientos.*
