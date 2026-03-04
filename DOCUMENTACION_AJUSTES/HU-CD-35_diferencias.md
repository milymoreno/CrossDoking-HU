# HU-CD-35 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-35 – Validación de Dealer Logístico en Generación de Guía
**HU Inicial:** `HU_Inicial/HU-CD-35 Validación de Dealer Log.md`
**HU Final:** `HU_final/HU-CD-35.md`
**Fecha de revisión:** 2026-03-04

---

## Resumen General

La HU inicial era correcta y concisa. La versión final agrega el contexto de negocio sobre por qué no se puede mezclar mercancía nueva y reman, desarrolla una tabla de casos con todas las combinaciones posibles (dealer vs compañía vs tipo de mercancía), y agrega el escenario de dealer no parametrizado.

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Contexto de negocio** | No existía. | Se explica que nueva y reman tienen **condiciones aduaneras distintas** y por eso no pueden ir en la misma guía. | Sin este contexto, el equipo de desarrollo podría entenderlo como una regla arbitraria. |
| 2 | **Tabla de casos** | Solo texto descriptivo. | Se agrega **tabla con 6 combinaciones** posibles (compañía × dealer × tipo mercancía) y su resultado. | Elimina ambigüedad y facilita la implementación de la lógica de validación. |
| 3 | **Escenario de dealer no parametrizado** | No existía. | Se agrega como **Escenario 5**. | Caso básico que faltaba: ¿qué pasa si el dealer simplemente no existe en el sistema? |
| 4 | **RN-07 (Reman Indicator como fuente única)** | Implícito. | Declarado explícitamente como regla de negocio. | Consistencia con HU-CD-34 y HU-CD-35 donde se establece que el Reman Indicator es la **única** fuente oficial. |

---

## Decisiones de Diseño Clave

> **La Tabla 1 es un compromiso pendiente:** El REQ-35 hace referencia explícita a una "Tabla 1 de reportes y casos especiales" que debe proporcionar Commex. Sin esa tabla, el equipo de desarrollo no puede implementar la validación de dealers válidos. Este es el pendiente más crítico de la HU.
