# HU-CD-31 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-31 – Parcialización de Documento de Transporte (Guía)
**HU Inicial:** `HU_Inicial/HU-CD-31 Parcialización de Docume.md`
**HU Final:** `HU_final/HU-CD-31.md`
**Fecha de revisión:** 2026-03-04
**Fuentes consultadas:**
- Requerimiento original: `REQUERIMIENTOS/requeriments.txt` (REQ-31)
- Sesión 25-Feb y 27-Feb: contexto general del proceso de generación de guías

---

## Resumen General

La HU inicial era estructuralmente completa y correcta. La versión final amplía la descripción de cada modalidad de parcialización, formaliza los estados de la Z95, agrega escenarios de aceptación adicionales (incluyendo sobreconsumo) y documenta el contexto del proceso actual (As-Is manualmente en Excel).

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Contexto As-Is** | No existía. | Se documenta que hoy la parcialización se gestiona **manualmente en Excel**. | El equipo de desarrollo necesita saber qué proceso físico está reemplazando. |
| 2 | **Tabla de estados de Z95** | Los estados se listaban como viñetas. | Se convierte en **tabla estructurada** con descripción de cada estado. | Más claro para el equipo de desarrollo; facilita el diseño del flujo de estados en la base de datos. |
| 3 | **Tabla de lógica de saldos** | Texto descriptivo. | Se convierte en **tabla de acción → efecto**. | Facilita el mapeo directo a reglas de negocio en el código. |
| 4 | **Escenario de sobreconsumo bloqueado** | No existía como escenario separado. | Se agrega como **Escenario 5** con mensaje de error específico. | El sobreconsumo es el riesgo principal de la parcialización; debe tener su propio criterio de aceptación. |
| 5 | **Modalidades con ejemplos detallados** | Las 3 modalidades se describían en formato de viñetas cortas. | Se expanden con explicación de efecto en el sistema para cada una. | Facilita la comprensión para el equipo técnico y el cliente. |
| 6 | **Pendientes de sesión** | No existían. | Se agregan 2 compromisos pendientes de confirmar. | Temas que afectan el diseño pero no se discutieron explícitamente en sesión. |

---

## Ajustes que NO cambiaron

- Las 3 modalidades de parcialización: por factura, por referencia, por unidades.
- La lógica de descuento automático y actualización de saldo.
- Las validaciones obligatorias (sobreconsumo, discrepancias activas, años distintos).
- Las 6 reglas de negocio originales (ampliadas a 7 con la regla de años).
- La obligatoriedad de auditoría de toda parcialización.

---

## Decisiones de Diseño Clave

> **El saldo es transaccional:** Cada guía parcial debe registrarse como una transacción que consume saldo. No puede ser solo un campo numérico que se actualiza; debe haber un historial de consumos por Z95 para trazabilidad auditora.

> **El estado de la Z95 es derivado, no manual:** El sistema debe calcular el estado automáticamente según el saldo disponible. El analista nunca debe cambiar el estado manualmente.
