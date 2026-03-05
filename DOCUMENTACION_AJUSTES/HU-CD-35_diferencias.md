# HU-CD-35 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-35 – Validación de Dealer Logístico en Generación de Guía
**HU Inicial:** `HU_final/HU-CD-35.md` (Versión previa)
**HU Final:** `HU_final/HU-CD-35.md` (Versión ajustada)
**Fecha de revisión:** 2026-03-05
**Fuentes consultadas:**
- Nota técnica de Deisy Rincón (RQ 35) y diagrama de mapeo por chat.
- Requerimiento original: `REQUERIMIENTOS/requeriments.txt` (REQ-35)

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Concepto de Dealer** | Se trataba como un código único directo de la factura. | **Dealer CAT vs Logístico**: Distinción entre el origen (Dynamics) y el destino real (Estructura interna). | Según la nota de Deisy Rincón, un código de CAT (ej. R15Q) debe poder mapearse a un dealer logístico nuestro (ej. R460) según la vía de transporte. |
| 2 | **Regla REMAN** | Validación genérica de no mezcla. | **Validación por Dealer Logístico**: El bloqueo ocurre al agrupar referencias para un mismo punto de entrega final. | Se aclaró que la restricción aduanera de no mezcla aplica a nivel de la guía consolidada para el depósito/distribuidor. |
| 3 | **Casos Especiales** | No contemplados. | **Mapeo por Vía y Tipo**: Inclusión de lógica para diferenciar Aéreo de Marítimo en el destino del dealer. | La imagen proporcionada por el usuario muestra desgloses diferentes según la vía de transporte (Aéreo vs Marítimo). |
| 4 | **Responsable Parametrización** | No definido. | **Analista Commex CD**: Encargado de mantener la tabla de mapeo y relaciones. | Se confirmó que este rol es el dueño de la verdad operativa para la distribución. |

---

## Decisiones de Diseño Clave

> **Normalización de Tablas**: Se creó la tabla `cat_dealer_mapeo` en el SQL para evitar duplicidad de datos y permitir que el sistema sea flexible ante cambios en la red logística de Gecolsa/Relianz sin alterar el código fuente.

> **Bloqueo Preventivo**: La validación se movió al inicio del proceso de generación de guías para evitar que el usuario trabaje sobre lotes de facturas que técnicamente no pueden viajar juntas.
