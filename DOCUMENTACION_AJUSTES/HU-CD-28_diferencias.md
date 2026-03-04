# HU-CD-28 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-28 – Gestión de Discrepancias entre Facturas Z95 vs INV PSC
**HU Inicial:** `HU_Inicial/HU-CD-28 Gestión de Discrepancias.md`
**HU Final:** `HU_final/HU-CD-28.md`
**Fecha de revisión:** 2026-03-04
**Fuentes consultadas:**
- Sesión de levantamiento: `TRANSCRIPT/HU Crossdocking 2502 10am.txt` (líneas 1575–1640)
- Sesión AM 27-Feb: `TRANSCRIPT/CrossDocking-27Feb-Am.txt`
- Requerimiento original: `REQUERIMIENTOS/requeriments.txt` (REQ-28)

---

## Resumen General

La HU inicial tenía un buen nivel de detalle estructural (flujo operativo en 6 pasos, criterios de aceptación, reglas de negocio). Los ajustes en la versión final amplían el contexto operativo, agregan los **estados del ciclo de vida de la factura**, documentan la visibilidad del proceso para Tesorería, y precisan las reglas de corrección según el origen del error.

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Estados del ciclo de vida de la factura** | No contemplados. | Se documentan los 4 estados: **Sin guía / Con guía / Con declaración / Con levante**. | Ricardo mencionó explícitamente estos estados en sesión (líneas 1576–1596, transcript 2502 10am): "sale un primer estado que es: factura sin guía; después si tiene guía; con declaración; y el último que es cuando nosotros enviamos". Son críticos para el diseño del flujo. |
| 2 | **Visibilidad para Tesorería (Z giros)** | No contemplada. | Se especifica que el equipo de **Tesorería (Z giros)** consume los estados de las facturas del SII para gestionar los giros al exterior. | Ricardo indicó (líneas 1589–1616, transcript 2502 10am) que Tesorería "mira mucho esa pantalla de las facturas, que si tienen guía, el estado en que está la mercancía o la factura". Esta información es relevante para no romper interfaces existentes. |
| 3 | **Origen de correcciones** | Genéricamente: "corrección en Dynamics o ajuste manual". | Se precisa: errores de Dynamics se corrigen en Dynamics; errores de asociación INV se corrigen en SII en los **5 campos habilitados**. | Daisy aclaró en sesión (transcript 2502 10am, líneas 1062–1082) que hay dos tipos de correcciones con diferente origen; el SII solo puede editar 5 campos específicos de la INV. |
| 4 | **Validación de valor FOB** | Solo se mencionaban cantidades como campo de comparación. | Se agrega explícitamente el **valor FOB** como campo de cruce en el reporte. | Fabián (Anderson) confirmó en sesión 27-Feb-Am (línea 646): "El valor. El valor FOB. Ahí dice. No solamente las cantidades, sino también el valor FOB". |
| 5 | **Tabla de campos del reporte Excel** | El reporte se describía en términos generales. | Se documenta la **tabla completa de campos** del reporte con su fuente (Interfaz 1, 2 o calculado). | Necesario para que el equipo de desarrollo pueda construir el reporte sin ambigüedades. |
| 6 | **Correo configurable como parámetro** | Se mencionaba "envío al correo definido para Operaciones" sin más detalle. | Se especifica que el correo debe ser **configurable como parámetro del sistema** (no hardcodeado). | Buena práctica de diseño; el equipo de Operaciones puede cambiar la distribución sin necesidad de un desarrollo adicional. |
| 7 | **Diagrama de flujo** | El flujo se describía en 6 pasos numerados con texto. | Se agrega un **diagrama de flujo ASCII** que muestra de forma visual el camino feliz y el camino de corrección. | Facilita la comprensión del proceso para el equipo de desarrollo y el cliente. |
| 8 | **Contexto As-Is** | No existía. | Se agrega la sección de contexto explicando el proceso actual con las 3 interfaces y las condiciones de corrección. | Permite al equipo de desarrollo entender qué existe hoy y qué debe replicarse o mejorarse. |

---

## Ajustes que NO cambiaron

- El bloqueo de guía si existen discrepancias activas (RN-01 de la HU inicial → RN-01 en final).
- La validación por compañía (Gecolsa / Relianz por separado).
- La ejecución parcial por selección de facturas.
- La auditoría del reporte generado.
- La regla de que la Z95 utilizada en guía no debe aparecer nuevamente en discrepancias.
- La secuencia: ejecutar → detectar → reportar → corregir en origen → re-ejecutar.

---

## Decisiones de Diseño Clave

> **Los estados de factura son compartidos con Tesorería:** El diseño de los estados no puede ignorar que el equipo de Tesorería los consume. Cualquier cambio en la nomenclatura o lógica de estados debe validarse con el área de Tesorería (Z giros) antes de implementarse.

> **El SII no puede corregir todo:** Solo 5 campos de la INV son editables en el SII. Los demás errores deben resolverse en Dynamics y reprocesarse vía interfaz. El equipo de desarrollo debe conocer exactamente cuáles son esos 5 campos (pendiente de Daisy).

> **El reporte Excel no es opcional:** Es el mecanismo de comunicación formal con Operaciones. Debe generarse automáticamente y enviarse al correo configurado; no debe depender de que el usuario lo descargue manualmente.
