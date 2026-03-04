# HU-CD-35 — Glosario de Términos de Negocio (Estilo Dummies)

**Aplica a:** HU-CD-35 – Validación de Dealer Logístico en Generación de Guía
**Fecha:** 2026-03-04


> Los términos Reman Indicator, Gecolsa, Relianz, Guía de Embarque ya están definidos en glosarios anteriores (HU-CD-26, HU-CD-27).

---

## D

**Dealer (revisado)**
En CrossDocking, el Dealer es el **destinatario logístico final del embarque**: la empresa, sucursal o punto de recepción al que va dirigida la mercancía una vez que supera el proceso aduanero.

- **No es** el espacio físico de almacenamiento dentro de un centro de distribución.
- **No es** un proveedor — es quien **recibe** la mercancía.
- Puede ser una sucursal regional de Gecolsa (ej. Gecolsa Barranquilla) o de Relianz.
- Cada Dealer tiene un código único y está asignado exclusivamente a **una compañía** (Gecolsa O Relianz, nunca ambas).

> El sistema usa el código del Dealer para agrupar facturas de la misma guía y para validar que no se mezclen destinatarios de compañías distintas.

---

## M

**Mezcla indebida (Nueva + Reman)**
Ocurre cuando se intenta incluir en la misma guía mercancía nueva (Reman Indicator = 0) junto con mercancía remanufacturada (Reman Indicator = 1). Esto está **prohibido** porque:
- La DIAN trata cada tipo de mercancía con distintos requisitos aduaneros.
- Las licencias de importación aplican diferente para nueva vs reman.
- Mezclarlas en una misma declaración puede generar multas o rechazos en aduana.

> Es como mezclar pasajeros con carga en el mismo manifiesto de vuelo — las autoridades los tratan de forma diferente y no se pueden consolidar.

---

## T

**Tabla 1 de Dealers**
Es el catálogo oficial de dealers válidos para el proceso CrossDocking, con la asignación de cada dealer a su compañía (Gecolsa / Relianz). Esta tabla debe ser mantenible en el sistema por el área de Commex. Sin esta tabla parametrizada, el sistema no puede validar correctamente qué dealers son permitidos para cada compañía.

> ⚠️ **Pendiente crítico:** Esta tabla debe ser proporcionada por Commex antes de que el equipo de desarrollo pueda implementar la validación de dealers.
