# HU-CD-39 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-39 – Generación de Declaración de Importación (DIM)
**HU Inicial:** `HU_Inicial/HU-CD-39 Generación de Declaració.md`
**HU Final:** `HU_final/HU-CD-39.md`
**Fecha de revisión:** 2026-03-04

---

## Resumen General

La HU inicial estaba bien estructurada y era técnicamente completa. La versión final agrega el contexto operativo (por qué el SII necesita autonomía frente a SIACO), desarrolla la tabla de campos con descripciones, formaliza los prerrequisitos como tabla con referencias cruzadas, y agrega el escenario de autonomía ante fallas de la agencia.

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Contexto de autonomía operativa** | Mencionado brevemente. | Se explica que **SIACO puede tener fallas** y el SII debe poder generar la DIM localmente sin depender de la disponibilidad de la agencia. | El equipo de desarrollo necesita entender por qué este requisito existe, para diseñar correctamente el almacenamiento local de archivos. |
| 2 | **Tabla de campos con descripciones** | Lista de campos sin contexto. | Convertida en **tabla con descripción** de cada campo. | Los 15 campos son términos técnicos de comercio exterior; sin descripción, el equipo de desarrollo no sabe qué dato capturar. |
| 3 | **Tabla de prerrequisitos con HU de referencia** | Lista de viñetas. | Tabla con **prerrequisito + HU de referencia**. | Facilita el mapa de dependencias entre HUs para el equipo técnico. |
| 4 | **Escenario de autonomía ante falla** | No existía. | Añadido como **Escenario 6**. | Es uno de los requisitos más críticos del REQ-39 ("asegurando el proceso ante fallas"). Necesita su propio criterio de aceptación. |
| 5 | **RN-08 (saldo de licencias en DIM)** | No documentado explícitamente. | Añadida: el saldo de licencias se descuenta al impulsar/transmitir la DIM, no al generarla como borrador. | Conecta con HU-CD-32 donde se definió que el consumo definitivo es en la DIM. |
| 6 | **Campos dinámicos por modalidad** | Mencionado como "habilitar dinámicamente". | Se especifica que deben **ocultarse** los campos que no aplican para la modalidad seleccionada. | La UX debe ser limpia: mostrar solo lo relevante para cada modalidad. |

---

## Decisiones de Diseño Clave

> **La DIM es el documento más crítico del proceso:** Un error en la DIM puede derivar en sanciones de la DIAN, retención de mercancía o multas. Por eso el sistema debe tener control de versiones inmutable: nunca se modifica una DIM existente, siempre se crea una nueva versión.

> **TRM como campo sensible:** La Tasa Representativa del Mercado varía diariamente. El sistema debe registrar la TRM aplicada en el momento de generar la DIM y no permitir cambiarla posteriormente, ya que es un dato oficial que determina el valor en pesos de los tributos.
