# HU-CD-41 — Glosario de Términos de Negocio (Estilo Dummies)

**Aplica a:** HU-CD-41 – Costeo de Embarques y Generación de DRS en D365
**Fecha:** 2026-03-04

---

## C

**Cierre financiero del embarque**
Es el proceso de registrar contablemente todos los costos del embarque en el sistema ERP (D365) una vez que la mercancía ya tiene levante aduanero. Incluye el costo de la mercancía (FOB), fletes, tributos y costos estimados. Sin este cierre, la empresa no puede pagar oficialmente al proveedor ni tener el costo real del inventario.

**Costeo por línea**
Es el proceso de asignar a cada referencia específica del embarque su parte proporcional de los costos totales (flete + tributos + 2% estimado). No todos los productos del mismo embarque tienen el mismo costo unitario — se distribuye en función del valor de cada línea.

**Costo estimado del 2%**
Es un porcentaje adicional que se añade al costo del embarque para cubrir gastos imprevistos o de difícil asignación directa (comisiones, gastos portuarios, etc.). El 2% es el valor provisional definido en el requerimiento, **pendiente de validación con el área de Costos** antes de implementar.

---

## D

**DRS (Documento de Recepción de Servicio)**
Es el documento contable que D365 genera para cerrar formalmente una Orden de Compra (OC). En el proceso CrossDocking, el DRS certifica que la mercancía fue recibida, los costos fueron asignados y la OC puede cerrarse. Es la "firma" en el sistema que autoriza el pago al proveedor. Sin DRS, la OC queda abierta indefinidamente en D365.

---

## M

**Migración de datos**
En este contexto, es el proceso de transferir la información del embarque desde el SII hacia D365 para que el sistema contable/financiero pueda procesarla. No es una copia manual — debe ser automática, sin duplicaciones y con confirmación de éxito.

---

## Flujo del Cierre en Lenguaje Simple

```
Levante de DIAN (HU-CD-40)
       ↓
SII migra datos del embarque → D365
(guía, DO, DIM, facturas, FOB, flete, tributos, 2%)
       ↓
D365 aplica costeo por línea (distribuye costos a cada referencia)
       ↓
D365 genera DRS (cierra la Orden de Compra)
       ↓
SII recibe confirmación del número DRS
       ↓
✅ Embarque CERRADO — proceso CrossDocking completado
```
