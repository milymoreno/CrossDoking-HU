# HU-CD-40 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-40 – Recepción de Información Aduanera – Aceptación, Pago y Levante
**HU Inicial:** `HU_Inicial/HU-CD-40 Recepción de Información.md`
**HU Final:** `HU_final/HU-CD-40.md`
**Fecha de revisión:** 2026-03-04

---

## Resumen General

La HU inicial era correcta y clara. La versión final agrega el contexto explicativo de cada hito (¿qué es la aceptación, el autoadhesivo y el levante?), las tablas de campos por hito, la tabla de transiciones de estado con indicación de cuáles son permitidas y cuáles no, y la nota sobre propagación de estados a DO y Guía.

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Contexto de cada hito** | Solo se listaban los 3 hitos sin explicar qué son. | Se agrega **tabla explicativa** de cada hito. | El equipo de desarrollo necesita saber qué dato representa el "autoadhesivo" y qué significa el "levante físico vs automático" para diseñar la estructura de datos correcta. |
| 2 | **Tabla de campos por hito** | Lista de viñetas agrupadas. | Cada hito tiene su **tabla de campos** con descripción. | Facilita el diseño del modelo de datos y los contratos de integración con la agencia/DIAN. |
| 3 | **Tabla de transiciones de estado** | Flujo como viñetas. | Convertido en **tabla con transiciones ✅ permitidas y ❌ bloqueadas**. | Más claro para el equipo de QA al diseñar los casos de prueba de validación de orden de estados. |
| 4 | **RN-07 (levante físico)** | No existía. | Regla añadida: el levante físico debe registrar las observaciones del inspector. | Diferencia operativa importante: el levante físico implica inspección real de la mercancía y puede generar hallazgos que afecten el proceso. |
| 5 | **Pendiente sobre recepción automática** | No documentado. | Añadido como compromiso: ¿los hitos los empuja la agencia o los consulta el analista? | Afecta el diseño técnico de la integración (push vs pull). |

---

## Decisiones de Diseño Clave

> **Los 3 hitos son secuenciales e irreversibles:** Una vez que la DIM avanza a "Pagada", no puede volver a "Aceptada". El sistema debe implementar esta restricción a nivel de lógica de negocio, no solo en la UI.

> **El levante es la puerta al cierre financiero:** Sin levante, el proceso costeo (HU-CD-41) no puede iniciarse. Esto evita que se haga el cierre contable antes de tener certeza de que la mercancía efectivamente entró al país.
