# HU-CD-33 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-33 – Generación de Documento de Transporte (Guía)
**HU Inicial:** `HU_Inicial/HU-CD-33 Generación de Documento.md`
**HU Final:** `HU_final/HU-CD-33.md`
**Fecha de revisión:** 2026-03-04
**Fuentes consultadas:**
- Sesión 27-Feb AM: transcript CrossDocking-27Feb-Am.txt (contexto de generación de guías, Anderson en IC400, validaciones de dealer)
- Requerimiento original: `REQUERIMIENTOS/requeriments.txt` (REQ-33)

---

## Resumen General

La HU inicial ya tenía los elementos clave. La versión final profundiza el contexto del proceso actual (Anderson en IC400), clarifica la diferencia entre generación individual y masiva, añade la tabla completa de campos obligatorios, agrega el paso de resumen de confirmación, y formaliza todos los escenarios de aceptación.

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Contexto As-Is** | No existía. | Se documenta el proceso actual: Anderson entra al IC400, carga manualmente los datos del dealer y asocia facturas. | Necesario para que el equipo entienda qué está reemplazando. |
| 2 | **Tabla de campos obligatorios** | Se listaban los campos como viñetas sin descripción. | Se convierte en **tabla estructurada** con campo, descripción y tipo (automático / obligatorio). | Más claro para diseño de pantalla y validaciones de backend. |
| 3 | **Generación individual vs. masiva** | No distinguía entre modalidades. | Se documenta la diferencia: individual = selección manual; masiva = agrupación automática por dealer. | La generación masiva es una necesidad operativa cuando hay múltiples guías del mismo dealer. |
| 4 | **Escenario de resumen de confirmación** | No existía. | Se agrega como **Escenario 5**: el sistema debe mostrar un resumen antes de confirmar la generación. | Mencionado en sesión 27-Feb AM (líneas 582-596): Fabián pidió un "resumen de lo que está recopilando" antes de grabar. |
| 5 | **Tabla de prerrequisitos con referencias HU** | Listados como viñetas sin referencia cruzada. | Se convierte en **tabla con HU de referencia y tipo de bloqueo**. | Facilita al equipo de desarrollo entender las dependencias exactas. |
| 6 | **RN-07 y RN-08 (generación masiva y resumen)** | No existían como reglas. | Agregadas formalmente a la lista de reglas de negocio. | Derivadas de los ajustes realizados en la versión final. |

---

## Ajustes que NO cambiaron

- Los 15 campos obligatorios de la guía (se mantienen del requerimiento original).
- Los 5 estados de la guía (Generada / En proceso DIM / DIM generada / Cerrada / Anulada).
- El bloqueo por discrepancias activas y dealer inválido.
- La prohibición de modificar la guía después de generar DIM.
- La auditoría obligatoria.

---

## Decisiones de Diseño Clave

> **El número de guía es automático:** No debe ser ingresado por el usuario. El sistema lo genera internamente garantizando unicidad por compañía y año. Esto evita errores manuales de duplicación.

> **El resumen de confirmación es obligatorio:** Antes de confirmar la generación, el sistema debe mostrar exactamente qué va a hacer (facturas, cantidades, dealer). Este paso fue solicitado explícitamente en sesión y es una práctica de UX importante para operaciones críticas.
