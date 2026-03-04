# HU-CD-41 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-41 – Costeo de Embarques y Generación de DRS en D365
**HU Inicial:** `HU_Inicial/HU-CD-41 Costeo de Embarques y Ge.md`
**HU Final:** `HU_final/HU-CD-41.md`
**Fecha de revisión:** 2026-03-04

---

## Resumen General

La HU inicial era completa. La versión final agrega el contexto de cierre financiero, desarrolla la tabla de campos a migrar, detalla la lógica proporcional del costeo por línea, especifica los campos que retorna D365 al confirmar el DRS, y eleva los pendientes del 2% como punto crítico de validación con Costos.

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Contexto de cierre financiero** | No existía. | Se explica que HU-CD-41 es el **cierre del ciclo CrossDocking**, cerrando la Orden de Compra en D365. | Sin contexto, el equipo podría tratar esta HU como un proceso técnico genérico sin entender su impacto contable. |
| 2 | **Tabla de campos de migración** | Lista de viñetas. | Tabla numerada con **10 campos y descripción** de cada uno. | El equipo de TI necesita el listado exacto de campos para diseñar la estructura del mensaje enviado a D365. |
| 3 | **Tabla del 2% estimado** | Un párrafo. | Se convierte en **tabla** con: porcentaje, base de cálculo (a confirmar), distribución y configurabilidad. | Hace explícito que la base de cálculo del 2% aún NO está confirmada y requiere validación con Costos antes de implementar. |
| 4 | **Campos de respuesta del DRS** | Solo mencionados. | Se convierte en **tabla**: número DRS, fecha, estado. | Necesario para diseñar el contrato de respuesta entre D365 y SII al confirmar la generación del DRS. |
| 5 | **RN-08 (validación con Costos)** | Implícito. | Declarado explícitamente como regla de negocio. | El REQ-41 dice "validar con COSTOS" — eso debe quedar documentado como regla para que no se implemente el 2% sin esa validación. |

---

## Decisiones de Diseño Clave

> **El 2% es el punto más crítico de esta HU:** El requerimiento original dice "validar con COSTOS". Esto significa que el porcentaje podría cambiar, la base de cálculo podría ser diferente al FOB, y la distribución podría hacerse por peso en lugar de por valor. **Esta HU no puede ir a desarrollo sin esa validación del área de Costos.**

> **El DRS es el "recibo" del proceso:** Cuando D365 genera el DRS, la Orden de Compra queda cerrada contablemente. Eso activa el proceso de pago al proveedor (Panamerican → Caterpillar). Por eso su generación correcta tiene impacto financiero directo.
