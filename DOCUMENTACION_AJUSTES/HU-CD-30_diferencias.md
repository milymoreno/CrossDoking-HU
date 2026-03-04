# HU-CD-30 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-30 – Control de Consecutivos Repetidos Z95 por Año
**HU Inicial:** `HU_Inicial/HU-CD-30 Control de Consecutivos.md`
**HU Final:** `HU_final/HU-CD-30.md`
**Fecha de revisión:** 2026-03-04
**Fuentes consultadas:**
- Requerimiento original: `REQUERIMIENTOS/requeriments.txt` (REQ-30)
- Sesión 25-Feb: contexto general de interfaces y Z95

---

## Resumen General

La HU inicial tenía el problema bien identificado y las reglas correctas. La versión final amplía la visualización con una tabla de campos, detalla la aplicación de la clave compuesta en **todos** los procesos del sistema, y formaliza los criterios de aceptación en formato Gherkin.

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Tabla de campos visualizables** | Se listaban los campos en texto. | Se convierte en **tabla estructurada** con columnas "Visible" y "Filtrable". | Formato más claro para el equipo de desarrollo frontend. |
| 2 | **Aplicación de clave compuesta en DIM** | No estaba incluida. | Se agrega la **DIM (Declaración de Importación)** como proceso que debe usar la clave compuesta. | La DIM se genera a partir de la Z95; si usa un número incorrecto por año, la declaración ante la DIAN sería inválida. |
| 3 | **Alerta al usuario en operación interanual** | No contemplada. | Se especifica que el sistema debe mostrar un **mensaje de error descriptivo** al intentar operar con facturas de años distintos. | Un bloqueo silencioso confunde al usuario; necesita saber exactamente por qué la operación fue rechazada. |
| 4 | **Formato Gherkin** | Los criterios eran texto descriptivo. | Convertidos a formato **Gherkin estándar**. | Consistencia con el resto de HUs del proyecto. |
| 5 | **Ejemplo explícito del problema** | Solo se indicaba que CAT repite consecutivos. | Se agrega una **tabla de ejemplo** mostrando los dos registros con el mismo Z95 pero diferente año. | Facilita la comprensión inmediata del problema para el equipo de desarrollo. |

---

## Ajustes que NO cambiaron

- La clave única: Número Z95 + Año + Compañía (RN-01, RN-02).
- Los procesos afectados: discrepancias, guías, asociación INV (ampliado a incluir DIM).
- El filtro por defecto = año en curso.
- La consulta histórica con filtro manual.
- La prohibición de cruces interanuales.
- La conservación del historial (no eliminar registros).

---

## Decisiones de Diseño Clave

> **La clave compuesta es transversal:** No es una regla de un solo módulo. Debe aplicarse en TODOS los procesos que manejen facturas Z95: discrepancias, generación de guía, asociación INV, DIM, y cualquier proceso futuro que se agregue. Esta debe implementarse a nivel de modelo de datos, no como validación de pantalla.

> **El problema es de base de datos:** Si la clave única en la base de datos del SII es solo el número Z95, el segundo registro con el mismo número simplemente colisionaría o sobrescribiría al anterior. La solución debe aplicarse en el diseño del esquema de datos, no en la capa de presentación.
