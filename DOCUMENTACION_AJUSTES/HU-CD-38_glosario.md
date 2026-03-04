# HU-CD-38 — Glosario de Términos de Negocio (Estilo Dummies)

**Aplica a:** HU-CD-38 – Reporte de Facturas PSC Asociadas a Documento de Transporte
**Fecha:** 2026-03-04

> Los términos Guía de Embarque, Factura PSC (INV), Z95, Dealer, FOB, DO, DIM ya están definidos en glosarios anteriores. Aquí se definen los conceptos nuevos de esta HU.

---

## C

**Conciliación (financiera)**
Es el proceso de comparar dos fuentes de información para verificar que coincidan. En este contexto, el área Financiera o de Tesorería necesita conciliar las facturas PSC del reporte con los valores registrados en D365, para asegurarse de que lo que se importó es exactamente lo que se contabilizó y pagó.

---

## E

**Exportar reporte**
Es la función que permite descargar el contenido de una tabla del sistema en un archivo externo. En esta HU se requiere exportación en:
- **Excel (.xlsx):** Para análisis, filtros adicionales, envío a otras áreas.
- **PDF:** Para archivo formal, presentación a auditores o como soporte documental.

La exportación no modifica datos — solo "fotografía" la información en ese momento.

---

## R

**Reporte de trazabilidad**
Un reporte que permite seguir el rastro completo de un proceso, desde su origen hasta su estado final. En este caso, el reporte de la guía permite ver: de qué Z95 vino la mercancía, qué factura INV la represente, cuánto se embarcó, y en qué estado está el trámite aduanero (DO, DIM).

---

## V

**Valor Unitario**
Es el precio de **una sola unidad** de la referencia (en USD). Se diferencia del Valor FOB de la línea, que es = Valor Unitario × Cantidad. El reporte muestra ambos para que el analista pueda verificar que los precios son correctos antes de la declaración aduanera.
