# HU-CD-34 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-34 – Validación de Referencias Remanufacturadas sin Licencia o Saldo
**HU Inicial:** `HU_Inicial/HU-CD-34 Validación de Referencia.md`
**HU Final:** `HU_final/HU-CD-34.md`
**Fecha de revisión:** 2026-03-04
**Fuentes consultadas:**
- Requerimiento original: `REQUERIMIENTOS/requeriments.txt` (REQ-34)
- HU-CD-27 (contexto de Reman Indicator)

---

## Resumen General

La HU inicial era correcta y detallada. La versión final añade el flujo secuencial de validación en 4 pasos, amplía la tabla de campos de la alerta con descripciones, aclara el significado del BTN, agrega el escenario de múltiples alertas simultáneas, y documenta el comportamiento del sistema para cada condición posible.

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Flujo de validación en 4 pasos** | La lógica se describía en texto libre. | Se convierte en **diagrama de flujo ASCII secuencial** (4 pasos con condiciones). | Permite al equipo de desarrollo implementar la lógica en el orden correcto; evita que se valide saldo antes de verificar si la licencia existe. |
| 2 | **Tabla de campos de la alerta con descripciones** | Se listaban los campos como viñetas. | Se convierte en **tabla con campo + descripción**. | El equipo de UI necesita saber exactamente qué mostrar en cada columna de la alerta. |
| 3 | **Clarificación del BTN** | Solo se mencionaba "BTN" sin definición. | Se añade nota explicativa: BTN = **subpartida arancelaria** (posición arancelaria DIAN). | El BTN no se definía en ningún documento anterior; la HU inicial mencionaba "BTN (subpartida)" entre paréntesis, lo que nos da la pista. |
| 4 | **Tabla de comportamiento por condición** | No existía. | Se agrega **tabla de condición → acción** cubriendo los 5 casos posibles. | Evita ambigüedad: el sistema sabe exactamente qué hacer en cada escenario sin interpretación. |
| 5 | **Escenario de múltiples alertas simultáneas** | Solo se cubrían los escenarios individuales. | Se agrega **Escenario 5**: cuando hay más de una referencia con problemas, el sistema debe mostrar todas. | Operativamente común: en una guía con 20+ referencias reman, varias pueden tener problemas distintos. |
| 6 | **RN-08 (reman sin obligación de licencia)** | No estaba como regla de negocio explícita. | Se agrega: las referencias reman parametrizadas como "No requiere" pasan sin bloqueo. | Evitar bloquear incorrectamente productos reman que legalmente no necesitan licencia. |

---

## Ajustes que NO cambiaron

- El Reman Indicator = 1 como única fuente oficial de identificación.
- La prohibición de usar sufijos R o CC como criterio alternativo.
- Los 3 tipos de inconsistencia (Sin licencia / Saldo insuficiente / Licencia vencida).
- La ejecución antes de guía y antes de DIM.
- La auditoría de intentos fallidos.

---

## Decisiones de Diseño Clave

> **El flujo de validación es secuencial:** Primero verificar si requiere licencia; solo si aplica, verificar existencia, vigencia y saldo — en ese orden. No se debe saltar al paso 3 (saldo) si no se sabe si existe licencia (paso 2).

> **BTN es un pendiente crítico:** La alerta incluye el BTN según el requerimiento original, pero no se documentó en sesión qué es exactamente, cómo viene del sistema (¿Dynamics? ¿paramétrico?) ni cómo se compone. Esto debe resolverse antes de diseñar la pantalla de alertas.
