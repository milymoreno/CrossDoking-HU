# HU-CD-32 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-32 – Asignación de Registro y/o Licencia de Importación en CrossDocking
**HU Inicial:** `HU_Inicial/HU-CD-32 Asignación de Registro y.md`
**HU Final:** `HU_final/HU-CD-32.md`
**Fecha de revisión:** 2026-03-04
**Fuentes consultadas:**
- Requerimiento original: `REQUERIMIENTOS/requeriments.txt` (REQ-32)
- Sesión 27-Feb AM: contexto sobre parametrización y tablas del sistema

---

## Resumen General

La HU inicial ya tenía la estructura funcional correcta. La versión final agrega el contexto del proceso actual (manual en Excel), desarrolla los tipos de documento con una tabla descriptiva, expande la lógica de parametrización, agrega el escenario de licencia de otra compañía, y documenta la conexión con otras HUs del proyecto.

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Contexto As-Is** | No existía. | Se documenta que hoy el control es **manual en Excel** sin soporte automático. | Imprescindible para que el equipo de desarrollo entienda el problema que resuelve la HU. |
| 2 | **Tabla de tipos de documento** | Solo se mencionaban R y L sin descripción. | Se agrega **tabla completa** con código, nombre y descripción de cada tipo. | El equipo de desarrollo y el cliente necesitan la distinción clara: R (habilitación sin saldo) vs L (con saldo limitado). |
| 3 | **Parametrización mantenible por Commex** | No se mencionaba explícitamente. | Se especifica que la parametrización (qué modelos requieren qué tipo) debe ser **modificable por Commex sin desarrollo**. | Diseñar una tabla de parámetros mantenible es crítico para la autonomía del área. |
| 4 | **Escenario de licencia de otra compañía** | No existía. | Se agrega como **Escenario 6**: bloqueo al intentar usar documento de Relianz para una operación de Gecolsa. | Identificado como RN-08 en la HU inicial pero sin escenario de aceptación; ahora formalizado. |
| 5 | **Tabla de validaciones en tiempo real** | Se listaban como viñetas. | Se reformatea como **tabla de validación → condición** para aplicar en el momento de seleccionar el documento. | Formato más claro para el equipo de QA al momento de diseñar los casos de prueba. |
| 6 | **Conexión con otras HUs** | Se mencionaba solo el Req 34 brevemente. | Se documentan las conexiones con **HU-CD-30** (clave compuesta), **HU-CD-34** (alertas de licencias) y **Req 34** (remanufacturados). | Importante para que el equipo de desarrollo entienda las dependencias entre HUs. |

---

## Ajustes que NO cambiaron

- La obligatoriedad de licencia según parametrización.
- El control de saldo en tiempo real.
- La prohibición de sobreconsumo y de usar licencias vencidas.
- La auditoría de toda asignación y consumo.
- El momento de descuento definitivo: al generar la DIM (no al generar la guía).
- Las 7 reglas de negocio originales (ampliadas a 9 en la versión final).

---

## Decisiones de Diseño Clave

> **R vs L no es solo terminología:** El Registro (R) habilita la importación de un tipo de producto sin límite de cantidad (mientras esté vigente). La Licencia (L) además tiene saldo en unidades o valor. El sistema debe tratar cada tipo de forma diferente: el R solo valida vigencia; el L valida vigencia Y saldo.

> **El consumo definitivo es en la DIM, no en la guía:** La guía es un compromiso logístico; la DIM es el compromiso legal ante la DIAN. Si se consumen saldos al generar la guía y luego la DIM se cancela, el saldo quedaría incorrectamente descontado. El diseño debe garantizar reversibilidad entre guía y DIM.
