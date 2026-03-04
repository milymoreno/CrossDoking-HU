# HU-CD-36 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-36 – Validación Visual de Guía – Cantidades y Valor FOB
**HU Inicial:** `HU_Inicial/HU-CD-36 Validación Visual de Guí.md`
**HU Final:** `HU_final/HU-CD-36.md`
**Fecha de revisión:** 2026-03-04

---

## Resumen General

La HU inicial tenía la estructura correcta. La versión final expande el contexto de posición en el flujo del proceso, agrega los campos de cabecera del resumen, formaliza el comportamiento según cada resultado (tabla de condición → acción), incluye el diagrama de flujo del proceso, y agrega RN-07 sobre identificación de la factura que genera la diferencia.

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Posición en el flujo** | Mencionado informalmente. | Se agrega **diagrama de flujo** del proceso completo (Guía → Validación Visual → DO → DIM). | El equipo de desarrollo necesita saber exactamente en qué momento se ejecuta esta pantalla. |
| 2 | **Campos de cabecera** | No documentados. | Se agrega **tabla de campos del encabezado** de la pantalla. | La UI debe mostrar contexto suficiente para que el analista identifique de qué guía es el comparativo. |
| 3 | **Tabla de comportamiento** | Descripto en texto. | Se convierte en **tabla de condición → acción**: OK = habilitar botón; Diferencia = bloquear. | Más preciso para el equipo de desarrollo (UX y backend). |
| 4 | **RN-07** | No existía. | Regla añadida: el sistema debe indicar **qué factura o referencia específica** genera la diferencia. | Sin esto, el analista solo sabe que hay diferencia pero no dónde. Es indispensable para que pueda corregirla. |
| 5 | **Pendiente sobre tolerancia** | No contemplado. | Se agrega como compromiso: ¿existe una diferencia tolerada (ej. redondeo)? | En comercio exterior pueden existir diferencias de centavos por tipo de cambio; es importante definirlo. |

---

## Decisiones de Diseño Clave

> **Esta pantalla no modifica datos:** Su única función es comparar y reportar. Nunca debe alterar valores de facturas ni de la guía. Si hay diferencia, el analista debe corregir en origen (en la guía o en las facturas) y volver a validar.

> **El botón "Continuar a DO" es una compuerta de proceso:** Solo aparece habilitado cuando la validación es exitosa. Esto impide que el proceso aduanero avance sobre datos inconsistentes.
