# HU-CD-37 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-37 – Creación DO – Integración con Agencia de Aduanas
**HU Inicial:** `HU_Inicial/HU-CD-37 Creación DO – Integració.md`
**HU Final:** `HU_final/HU-CD-37.md`
**Fecha de revisión:** 2026-03-04

---

## Resumen General

La HU inicial era correcta y detallada. La versión final añade el contexto de cómo funciona la integración SIACO/FileZilla (no es API directa sino por archivos), clarifica el significado del BTN en este contexto, agrega la explicación de la integración en 4 pasos, la tabla de actualizaciones progresivas desde la agencia, y el escenario de DO duplicado.

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Mecanismo de integración** | Se mencionaba "conexión con FileZilla/SIACO" genéricamente. | Se especifica que **no es API directa** sino **transferencia de archivos estructurados** via FileZilla. | Afecta el diseño técnico: el equipo de TI necesita saber si implementar una API o un proceso de FTP/transferencia de archivos. |
| 2 | **Clarificación de BTN** | Solo mencionado como "Clasificación arancelaria (BTN)". | Se documenta que el **BTN lo asigna la agencia** (SIACO) y lo retorna al SII; no lo ingresa manualmente el analista. Además, se conecta con el uso del BTN en HU-CD-34. | Resuelve la pregunta abierta sobre el BTN que venía desde REQ-34: el BTN en la alerta de remanufacturados (HU-CD-34) viene de la clasificación arancelaria asignada previamente por la agencia. |
| 3 | **Tabla de actualizaciones progresivas** | Descripción en texto. | Se convierte en **tabla**: tipo de actualización + descripción. | Más claro para el equipo de desarrollo al diseñar el mecanismo de recepción de datos desde la agencia. |
| 4 | **Escenario DO duplicado** | No existía. | Se agrega como **Escenario 4**. | Caso crítico: si por un error técnico el analista intenta generar el DO dos veces, el sistema debe proteger la integridad del proceso. |
| 5 | **Nota sobre SIACO** | No existía. | Se agrega nota: SIACO **no interactúa con la pantalla Z95/INV** (confirmado en sesión HU-CD-27); la interacción ocurre solo en creación del DO. | Evita confusión entre el rol de SIACO en HU-CD-27 vs HU-CD-37. |

---

## Decisión de Diseño Clave

> **El BTN circula así en el proceso CrossDocking:**
> 1. **HU-CD-34 (Validación Reman):** El BTN es la subpartida arancelaria ya conocida, que aparece en la alerta para identificar la referencia con problema de licencia.
> 2. **HU-CD-37 (DO):** La agencia de aduanas asigna/confirma el BTN durante la clasificación arancelaria y lo retorna al SII.
> Ambos usos son del mismo dato; la diferencia es el momento del proceso.
