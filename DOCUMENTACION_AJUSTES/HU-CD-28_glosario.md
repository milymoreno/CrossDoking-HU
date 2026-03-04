# HU-CD-28 — Glosario de Términos de Negocio (Estilo Dummies)

**Aplica a:** HU-CD-28 – Gestión de Discrepancias Z95 vs INV PSC
**Fecha:** 2026-03-04

> Los términos ya definidos en glosarios anteriores (Z95, INV, Dynamics, SII, Dealer, DHL, etc.) no se repiten aquí. Este glosario se enfoca en los conceptos nuevos de esta historia.

---

## C

**Cruce / Comparación (de facturas)**
Es la operación que hace el sistema de comparar, campo por campo, los datos de la factura Z95 contra los datos de la factura INV. El objetivo es detectar si "lo que vendió CAT" coincide con "lo que facturó Panamerican". Las diferencias encontradas son las **discrepancias**.

---

## D

**Discrepancia**
Es cualquier diferencia encontrada entre lo que dice la factura Z95 (CAT) y lo que dice la factura INV (Panamerican). Por ejemplo:
- CAT facturó 10 unidades de una referencia, pero Panamerican facturó 9 → **discrepancia de cantidad**
- El valor FOB en Z95 no coincide con el valor en INV → **discrepancia de valor**
- Hay una Z95 pero no existe INV correspondiente → **discrepancia de ausencia**

> Si hay discrepancias, el proceso de importación no puede avanzar a la siguiente etapa. Hay que resolverlas primero.

---

## E

**Estado de la Factura**
Es la "etiqueta" que el sistema le pone a cada factura para indicar en qué momento del proceso de importación se encuentra. Los estados son:

| Estado | Qué significa en lenguaje simple |
|--------|----------------------------------|
| **Sin guía** | La factura llegó al sistema pero todavía no se ha generado el documento de transporte. Está esperando. |
| **Con guía** | Ya se generó el embarque; la factura fue asignada a una guía. |
| **Con declaración** | Ya se creó la Declaración de Importación (DIM) para esta factura. La aduana ya sabe que viene. |
| **Con levante** | La aduana aprobó la importación y la mercancía puede ser retirada del puerto. Proceso completado. |

---

## F

**FOB (Free On Board)**
Es el precio de la mercancía desde el punto de origen hasta el punto de embarque, sin incluir flete ni seguro. Es uno de los valores que se compara en el proceso de discrepancias. Si el FOB que Caterpillar reporta en la Z95 no coincide con el FOB que Panamerican reporta en la INV, eso es una discrepancia.

---

## G

**Giro al Exterior**
Es el pago que se hace desde Colombia hacia el proveedor extranjero (Caterpillar) para cancelar las facturas importadas. El equipo de **Tesorería (Z giros)** gestiona estos pagos y para ello necesita saber en qué estado está cada factura en el SII: si ya tiene guía, si ya tiene declaración, etc.

---

## N

**Nacionalización**
Es el proceso legal mediante el cual la mercancía extranjera entra oficialmente al país y paga los impuestos y aranceles correspondientes. En este contexto, "nacionalizar de más" significa importar más cantidad de la que se debe (generando problemas tributarios); "nacionalizar de menos" significa importar menos de lo due se debería (perdiendo mercancía o incumpliendo pedidos).

El proceso de discrepancias existe precisamente para **evitar ambos escenarios**.

---

## R

**Reporte de Discrepancias**
Es un archivo Excel que el sistema genera automáticamente cuando detecta diferencias entre Z95 e INV. Contiene el detalle de cada inconsistencia encontrada y se envía por correo electrónico al equipo de Operaciones para que gestionen la corrección. No es un archivo que el usuario descarga manualmente; el sistema lo genera y envía solo.

---

## S

**Sub-nacionalización**
Importar menos mercancía de la que se facturó. Puede generar incumplimiento de pedidos a dealers, diferencias en inventario y problemas con la aduana.

**Sobre-nacionalización**
Importar más mercancía de la que se tiene derecho a importar según los documentos de aduanas. Puede generar multas, diferencias de impuestos y problemas legales con la DIAN.

---

## T

**Tesorería / Z Giros**
Es el área financiera de la empresa encargada de gestionar los pagos al exterior (giros divisas). Utilizan la pantalla de estados de facturas del SII para determinar cuándo pueden hacer el giro a Caterpillar. Un giro no se hace hasta que la factura tenga al menos estado "Con guía", lo que confirma que la mercancía está en proceso de importación.

---

## Flujo de una Discrepancia (Lenguaje Simple)

```
1. Llegaron las facturas de CAT (Z95) y de Panamerican (INV) al sistema.
2. El analista ejecuta el proceso de "discrepancias".
3. El sistema compara Z95 vs INV.
        ¿Coinciden?
       SÍ ──────────► Factura aprobada → puede generarse la guía.
        │
       NO ──────────► El sistema genera un Excel con las diferencias.
                      Lo envía por correo a Operaciones.
                      Operaciones corrige el error en Dynamics (o en el SII si aplica).
                      La interfaz se reprocesa.
                      El analista vuelve a ejecutar discrepancias.
                      Repite hasta que no haya diferencias.
```
