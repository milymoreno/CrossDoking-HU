# HU-CD-31 — Glosario de Términos de Negocio (Estilo Dummies)

**Aplica a:** HU-CD-31 – Parcialización de Documento de Transporte (Guía)
**Fecha:** 2026-03-04

---

## D

**Documento de Transporte (Guía de Embarque)**
Es el documento oficial que acompaña la mercancía desde el origen hasta el destino. Para el proceso CrossDocking es el equivalente a un "manifiesto de carga" — dice exactamente qué se está moviendo, cuántos unidades y de qué factura provienen. Sin guía, la mercancía no puede ser transportada ni procesada en la aduana.

---

## P

**Parcialización**
Es la capacidad de dividir una factura (Z95) o parte de ella en diferentes guías de embarque. En lugar de mover todo o nada, el sistema permite mover exactamente lo que se necesita:

| Modalidad | Analogía simple |
|-----------|----------------|
| Por Factura | Embarca toda la "caja" Z95 completa |
| Por Referencia | Escoge qué "productos" de la caja embarca hoy |
| Por Unidades | Escoge cuántas unidades de un producto embarca hoy |

**Ejemplo práctico:**
- Z95 tiene: REF-A (10 und) + REF-B (5 und)
- Guía 1: solo REF-A (5 und) → saldo queda: REF-A (5 und) + REF-B (5 und)
- Guía 2: REF-B completa → saldo queda: REF-A (5 und)
- Guía 3: REF-A restante → Z95 queda en estado "Procesada"

---

## S

**Saldo disponible**
Es la cantidad de unidades de una referencia dentro de una Z95 que aún no ha sido asignada a ninguna guía. Cuando el saldo llega a cero, la referencia (o la Z95 completa) queda en estado "Procesada" y desaparece de la tabla de pendientes.

**Sobreconsumo**
Intentar incluir en una guía más unidades de las que hay disponibles en el saldo. El sistema debe **bloquear** esto automáticamente. En el sistema actual (manual), el sobreconsumo es un riesgo real porque el control se hace en Excel.

---

## T

**Tabla de pendientes**
Es la pantalla del sistema donde se muestran todas las facturas Z95 que aún tienen saldo disponible (que no han sido procesadas completamente). Es el "inbox" del equipo de Commex para saber qué mercancía está lista para embarcar.
