# HU-CD-29 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-29 – Sincronización de Facturas PSC y Z95 desde D365
**HU Inicial:** `HU_Inicial/HU-CD-29 Actualización Automática.md`
**HU Final:** `HU_final/HU-CD-29.md`
**Fecha de revisión:** 2026-03-04
**Fuentes consultadas:**
- Sesión 25-Feb: `TRANSCRIPT/HU Crossdocking 2502 10am.txt` (líns. 1100–1500 — interfaces y sincronización)
- Sesión 27-Feb AM: `TRANSCRIPT/CrossDocking-27Feb-Am.txt`
- Requerimiento original: `REQUERIMIENTOS/requeriments.txt` (REQ-29)

---

## Resumen General

La HU inicial era técnicamente sólida pero escueta. La versión final amplía el contexto operativo con la arquitectura de las 3 interfaces (Z95, INV, OC), refuerza las reglas de sincronización con el ciclo de vida de la factura documentado en HU-CD-28, y agrega escenarios de aceptación completos en formato Gherkin.

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Contexto de las 3 interfaces** | No mencionado. | Se documenta que la sincronización aplica a **3 interfaces independientes** (Z95, INV, OC), cada una con su propio proceso. | Confirmado en sesión 25-Feb (líns. 1123–1195): Daisy explicó que Dynamics genera 3 interfaces al SII; la sincronización debe gestionar cada una de forma independiente. |
| 2 | **Fallo independiente por interfaz** | No contemplado. | Se especifica que si una interfaz falla, las demás **no se interrumpen** y los registros exitosos se conservan. | Principio derivado del diseño de desacoplamiento ("evitar acoplamiento fuerte") que la HU inicial mencionaba pero no desarrollaba. |
| 3 | **Estado de factura como restricción de sincronización** | No contemplado. | El sistema **no debe modificar** facturas ya en estado "Con guía", "Con declaración" o "Con levante", aunque lleguen nuevamente por la interfaz. | Consistencia con los estados del ciclo de vida documentados en HU-CD-28. Un registro ya procesado no debe ser afectado por una re-sincronización. |
| 4 | **Filtro de interfaz de origen en consulta** | No contemplado. | Se agrega el filtro por **interfaz de origen** (Interfaz 1 / 2 / 3) en la pantalla de consulta de registros. | Necesario para que el analista pueda diagnosticar rápidamente si un problema es de la Z95, la INV o la OC. |
| 5 | **Escenario: no modificar registros en estado avanzado** | No existía. | Se agrega como Escenario 5 en los criterios de aceptación. | Detectado como necesario al cruzar la lógica de sincronización con el ciclo de vida de la factura. |
| 6 | **Formato Gherkin** | Los criterios de aceptación estaban en texto libre. | Se convierten a formato **Gherkin estándar** (Dado/Cuando/Entonces). | Formato requerido por el equipo de QA y desarrollo para ejecución de pruebas automatizadas. |

---

## Ajustes que NO cambiaron

- La arquitectura API sin acceso directo a BD productivas de D365.
- La clave única Factura + Fecha (año) + Compañía (RN-01).
- Las reglas de facturas de años anteriores ya procesadas (RN-02 y RN-03).
- El monitoreo (fecha, cantidad procesados, errores, estado).
- Los filtros de consulta de registros.

---

## Decisiones de Diseño Clave

> **Sincronización independiente por interfaz:** El sistema no debe procesar Z95, INV y OC como un bloque monolítico. Si la INV falla, la Z95 ya cargada debe conservarse. Esto reduce el impacto de fallos parciales.

> **Estados como barrera de re-sincronización:** Las facturas en estados avanzados (Con guía, Con declaración, Con levante) son inmutables desde la perspectiva de las interfaces. Cualquier corrección en esas etapas requiere un proceso controlado diferente.
