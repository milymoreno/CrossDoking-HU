# HU-CD-30 — Glosario de Términos de Negocio (Estilo Dummies)

**Aplica a:** HU-CD-30 – Control de Consecutivos Repetidos Z95 por Año
**Fecha:** 2026-03-04

---

## C

**Clave Compuesta**
Es una combinación de más de un campo para identificar de forma única un registro. En el caso de las facturas Z95, se necesitan 3 campos juntos para que el registro sea único:

```
Número Z95 + Año de Factura + Compañía
```

Usando solo el número Z95 no es suficiente porque Caterpillar reutiliza los mismos números cada dos años. Si no se usa la clave compuesta, el sistema pensaría que una factura del 2023 y una del 2025 son la misma.

**Colisión de registros**
Es cuando dos registros diferentes tienen la misma clave identificadora en la base de datos, causando que uno sobrescriba al otro o que el sistema no sepa cuál es el correcto. Es el problema principal que genera la reutilización de consecutivos por CAT.

**Consecutivo**
Es el número de secuencia que CAT asigna a cada factura Z95. El problema es que Caterpillar **reinicia o reutiliza** estos números cada dos años, lo que significa que el número de factura solo no es suficiente para identificar de forma única una transacción.

---

## C

**Cruce interanual**
Ocurre cuando el sistema intenta comparar o asociar datos de dos años diferentes. Por ejemplo: tomar una Z95 del 2023 y asociarla con una INV del 2025. Esto es un error porque son transacciones de períodos distintos y los valores, cantidades y condiciones puede que ya no correspondan. El sistema debe **bloquear** cualquier cruce interanual.

---

## F

**Filtro por defecto**
Es la configuración que el sistema aplica automáticamente sin que el usuario tenga que pedirlo. En este caso, cuando un usuario abre el módulo de CrossDocking, el sistema **por defecto solo muestra facturas del año en curso**. Si quiere ver años anteriores, debe aplicar un filtro manual.

---

## H

**Histórico / Información histórica**
Son los registros de años anteriores que ya fueron procesados. El sistema los conserva para consulta (no los borra), pero no permite operarlos en procesos activos del año en curso. Son como un archivo muerto que uno puede consultar pero no modificar.

---

## R

**Reutilización de consecutivos**
Es la práctica de Caterpillar de volver a usar los mismos números de factura Z95 después de un período de tiempo (aproximadamente dos años). Esto no es un error de CAT — es una característica de su sistema de facturación. El SII debe estar diseñado para manejarlo correctamente.

---

## Ejemplo Visual del Problema

```
❌ SIN clave compuesta (incorrecto):
   Z95123456 (2023) → Registro en BD: {numero: "Z95123456", datos: "...2023..."}
   Z95123456 (2025) → COLISIÓN: el sistema no sabe cuál es el correcto

✅ CON clave compuesta (correcto):
   Z95123456 + 2023 + Gecolsa → Registro A (intocable para procesos de 2025)
   Z95123456 + 2025 + Gecolsa → Registro B (activo para procesos de 2025)
```
