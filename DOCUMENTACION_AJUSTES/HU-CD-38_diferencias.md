# HU-CD-38 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-38 – Reporte de Facturas PSC Asociadas a Documento de Transporte
**HU Inicial:** `HU_Inicial/HU-CD-38 Reporte de Facturas PSC.md`
**HU Final:** `HU_final/HU-CD-38.md`
**Fecha de revisión:** 2026-03-04

---

## Resumen General

La HU inicial estaba bien estructurada. La versión final amplía los filtros con tipo de columna, convierte la lista de campos a tabla numerada, agrega el escenario de filtro por estado, incorpora la nota sobre remanufacturados en el reporte, y documenta la integración con los procesos posteriores (DIM, conciliación, D365).

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Tabla de campos numerada** | Lista de viñetas sin numeración. | Convertida a **tabla con # y descripción** de cada campo. | Los 13 campos necesitan referencia unívoca para el diseño de la pantalla y del modelo de datos. |
| 2 | **Nota sobre remanufacturados** | No contemplada. | Se especifica que las referencias reman muestran el **valor total consolidado** (Premium + Core), consistente con HU-CD-27. | El reporte debe ser consistente con la pantalla de visualización. Una reman no debe mostrar dos líneas separadas. |
| 3 | **Tabla de integración con procesos** | Mencionada en texto libre. | Se convierte en **tabla de proceso → HU → uso del reporte**. | Facilita entender para qué sirve exactamente este reporte en cada etapa siguiente del proceso. |
| 4 | **Escenario de filtro por estado** | No existía. | Añadido como **Escenario 5**. | La capacidad de filtrar por estado es uno de los filtros definidos; necesita su escenario de aceptación. |
| 5 | **RN-06 (guía parcial)** | No existía. | Añadida: el reporte de guía parcial solo muestra cantidades efectivamente incluidas. | Necesario para que el equipo de QA entienda que no se deben mostrar saldos restantes. |

---

## Decisiones de Diseño Clave

> **El reporte es de solo lectura:** No modifica ningún dato del sistema. Su función es únicamente presentar y exportar información ya existente.

> **La clave del reporte es la Guía, no la Factura:** El usuario busca por guía, y el sistema retorna todas las facturas asociadas. Esto es el inverso de la pantalla Z95/INV (HU-CD-27), donde se busca por factura.
