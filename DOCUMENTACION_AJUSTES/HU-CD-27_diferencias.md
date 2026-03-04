# HU-CD-27 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-27 – Recepción y Visualización de Relación Facturación Z95 vs INV PSC
**HU Inicial:** `HU_Inicial/HU-CD-27 Recepción y Visualización.md`
**HU Final:** `HU_final/HU-CD-27.md`
**Fecha de revisión:** 2026-03-04
**Fuentes consultadas:**
- Sesión de levantamiento: `TRANSCRIPT/HU Crossdocking 2502 10am.txt` (especialmente líneas 1100–1500)
- Requerimiento original: `REQUERIMIENTOS/requeriments.txt` (REQ-27)

---

## Resumen General

La HU inicial documentaba correctamente los conceptos clave del requerimiento (remanufacturados, relación Z95 vs INV, límite de caracteres). Sin embargo, las sesiones con el equipo técnico y funcional revelaron detalles de arquitectura de interfaces, correcciones terminológicas y restricciones operativas que enriquecen y precisan la historia.

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Número de interfaces de Dynamics** | Se mencionaban 2 interfaces (Z95 e INV). | Se documentan **3 interfaces**: Z95, INV y **Órdenes de Compra (OC)**. | En sesión (líneas 1123–1195, transcript 2502 10am), Daisy confirmó que Dynamics genera 3 interfaces hacia el SII; la interfaz de OC se usa para validaciones de discrepancias. |
| 2 | **Corrección terminológica: INB → INV** | Se usaba el término "INB" en toda la HU. | El término correcto es **INV** (Invoice). | Fabián corrigió explícitamente en sesión (línea 1492, transcript 2502 10am): "INB es I-N-V, no B de burrito". Todos los documentos deben unificarse con INV. |
| 3 | **Arquitectura de la vista en pantalla** | La HU inicial no especificaba cómo se visualizaban los datos. | Se documenta que la vista es un **JOIN dinámico de 3 tablas** (Z95, INV, OC) en una sola pantalla. | Daisy describió explícitamente (líneas 1257–1269, transcript 2502 10am): "esta pantalla es como un join, tomamos la información de tres tablas y lo representamos acá". |
| 4 | **Comportamiento cuando INV no está disponible** | No contemplado. | Si la Interfaz 2 (INV) no ha llegado, el sistema muestra la Z95 con estado "Pendiente INV" y no bloquea la consulta. | Daisy explicó (líneas 1275–1282): "si en dos tablas no está la información, solo me va a mostrar la primera"; esto indica que Panamerican todavía no ha facturado. |
| 5 | **Relación con robot de SIACO** | No se mencionaba. | Se especifica explícitamente que el robot de SIACO **NO interactúa** con esta pantalla. | Daisy confirmó en sesión (líneas 1380–1387): "El robot no va a interactuar con esto". Esta aclaración evita expectativas erróneas en el equipo de desarrollo. |
| 6 | **Simultaneidad de usuarios** | No contemplada. | El sistema debe soportar que **múltiples usuarios** operen simultáneamente por compañía (ej. uno en Gecolsa y otro en Relianz). | Ricardo lo planteó explícitamente (líneas 1480–1485, transcript 2502 10am): "el sistema también permita simultaneidad de usuario, que M. Anderson esté haciendo Gecolsa y otra persona esté haciendo Relianz". |
| 7 | **Estado de auditoría de interfaces** | Se mencionaba "auditoría de carga automática" de forma genérica. | Se especifica que cada interfaz cargada debe quedar auditada con **fecha, hora y usuario** que ejecutó. | Requisito funcional implícito confirmado como necesario para trazabilidad operativa en sesión. |
| 8 | **Regla del Reman Indicator** | Presente en la HU inicial con buen detalle. | Se mantiene y refuerza: **Reman Indicator = 1** es la única fuente oficial; se prohíbe explícitamente lógica por sufijos. | Confirmado en la HU inicial con referencia a sesión; se refuerza como RN-05 para dejar trazabilidad clara. |
| 9 | **Contexto As-Is** | No existía. | Se agrega sección con el flujo actual de 3 interfaces y la forma en que el sistema actual presenta los datos. | Necesario para que el equipo de desarrollo entienda qué debe replicar y mejorar del sistema legado. |
| 10 | **Campos de la vista consolidada** | No existían en la HU inicial. | Se agrega tabla completa de campos con su fuente (Interfaz 1/2/3 o Sistema). | El equipo de Commex y TI necesitan tener claro qué campos se muestran y de dónde vienen para diseñar la integración. |

---

## Ajustes que NO cambiaron

- La lógica de remanufacturado (Premium + Core = Valor único SII, cantidad = 1 unidad).
- La identificación por Reman Indicator = 1 (no por sufijos).
- La prohibición de limitar la longitud o caracteres de referencias.
- La obligatoriedad de relacionar Z95 ↔ INV antes de habilitar discrepancias.
- El estado de una factura: Recibida / Pendiente discrepancia / Lista para guía.

---

## Decisiones de Diseño Clave

> **3 interfaces, no 2:** El diseño de la base de datos debe contemplar 3 tablas de entrada independientes para Z95, INV y OC. Mezclarlas en una sola tabla desde el inicio comprometería la trazabilidad por interfaz.

> **INV, no INB:** Se debe hacer un reemplazo global del término en todos los documentos, pantallas, campos de base de datos y mensajes del sistema. Usar INB es un error ya corregido en sesión.

> **La pantalla es de consulta, no de acción:** Para esta HU, la pantalla solo muestra el estado de relación. Las correcciones de campos se hacen en HU-CD-26 (campos INV) o en la HU de discrepancias (HU-CD-28).
