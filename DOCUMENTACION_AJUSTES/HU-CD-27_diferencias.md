# HU-CD-27 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-27 – Recepción y Visualización de Relación Facturación Z95 vs INV PSC
**HU Inicial:** `HU_Inicial/HU-CD-27 Recepción y Visualizació.md`
**HU Final:** `HU_final/HU-CD-27.md`
**Fecha de revisión:** 2026-03-05
**Fuentes consultadas:**
- Nota de reunión y diagrama proporcionado por el usuario vía chat (HU-CD-27).
- Requerimiento original: `REQUERIMIENTOS/requeriments.txt` (REQ-27)

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Cardinalidad Z95-INV** | No especificada claramente (se asumía 1:1). | **Cardinalidad 1:N**: Una Z95 puede estar relacionada con múltiples facturas INV PSC. | El diagrama de proceso y la nota técnica confirman que el desglose de una factura Caterpillar (Z95) puede generar múltiples facturas proforma (INV) según el destinatario final hacia Gecolsa/Relianz. |
| 2 | **Longitud de Referencias** | Atado a tipos de datos estándar (50 caracteres). | **Sin límite**: Soporte para longitud extendida, caracteres especiales y case-sensitivity. | El requerimiento explícito indica que el sistema no debe ser limitante con los caracteres de la OC, permitiendo incluso mayúsculas/minúsculas y caracteres especiales. |
| 3 | **Lógica de Valor REMAN** | Mencionado como Premium + Core. | **Consolidación en Valor Único**: Dynamics envía Premium + Cargo Core, pero en SII se refleja como un único valor total. | Se aclaró que para el proceso de nacionalización en el SII, el valor debe ser único (suma de ambos componentes de Dynamics), manteniendo la cantidad como 1 unidad. |
| 4 | **Responsables Técnicos** | Genéricos. | Asignados específicamente: **Deisy Rincón** y **Fabián Barragán** para validaciones e integración. | Nombres confirmados en las notas de reunión de febrero. |

---

## Decisiones de Diseño Clave

> **Soporte de Caracteres en DB**: Se ajustó el tipo de dato en `cat_referencia` a `VARCHAR(200)` y se eliminaron restricciones de validación de formato restrictivas para cumplir con la flexibilidad solicitada en la OC.

> **Visualización Multi-INV**: La pantalla de relación debe permitir expandir una Z95 para ver todas sus INV asociadas sin perder la trazabilidad de la cantidad total despachada.
