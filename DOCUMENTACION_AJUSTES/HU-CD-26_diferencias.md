# HU-CD-26 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-26 – Recepción de Información Consolidada del Agente de Carga
**HU Inicial:** `HU_Inicial/HU-CD-26 Recepción Información Co.md`
**HU Final:** `HU_final/HU-CD-26.md`
**Fecha de revisión:** 2026-03-04
**Fuentes consultadas:**
- Sesión de levantamiento: `TRANSCRIPT/HU Crossdocking 2502 10am.txt`
- Requerimiento original: `REQUERIMIENTOS/requeriments.txt` (REQ-26)

---

## Resumen General

La HU inicial capturaba el proceso a un nivel conceptual alto. Tras la revisión de los transcripts de las sesiones con el equipo de CrossDocking (Anderson Fabián, Daisy, Ricardo, Alan y otros), se identificaron detalles técnicos, restricciones operativas y pendientes de negocio que enriquecieron significativamente la historia.

---

## Tabla de Diferencias

| # | Elemento | HU Inicial | HU Final | Justificación |
|---|----------|-----------|----------|---------------|
| 1 | **Mecanismo de recepción** | Se menciona genéricamente "recibir información del agente de carga" sin especificar cómo. | Se define una jerarquía de mecanismos: **carga automática (DHL bot) > carga manual (archivo desde UI) > copia estructurada** como contingencia. | En sesión (líneas 367–500, transcript 2502 10am) se constató que DHL utiliza un bot en pruebas cuyo formato **no está aún estandarizado**. El equipo acordó que se necesita un mecanismo alternativo mientras se define el formato estándar. |
| 2 | **Validación del formato del archivo** | No se contemplaba validación del formato. | Se agrega validación explícita del formato del archivo (campos obligatorios, estructura). | El equipo señaló que el archivo de DHL debe tener un estructura fija; si varía, el proceso falla. Se deben definir y validar los campos mínimos obligatorios. |
| 3 | **Integración con interfaces de Dynamics** | La HU solo mencionaba "validar que las Z95 estén integradas desde D365". | Se documenta que Dynamics genera **2 archivos planos** (Z95 e INB/INR) en ventanas de tiempo (~9 AM y ~1 PM). El SII debe cruzar con ambas interfaces. | Daisy explicó en detalle (líneas 566–670, transcript 2502 10am) que Dynamics genera dos interfaces distintas que llegan como archivos planos; esta información es crítica para el diseño de la integración. |
| 4 | **Corrección de inconsistencias en SII** | No contemplada; la HU inicial asumía que los datos llegaban correctos. | Se agrega funcionalidad para **corregir los 5 campos de la INB** directamente en el SII sin volver a Dynamics. | Daisy informó (líneas 1082–1096, transcript 2502 10am) que actualmente en el SII se corrigen problemas de asociación Z95-INB, especialmente en 5 campos específicos. Esto debe mantenerse en el nuevo sistema. |
| 5 | **Filtros de consulta** | Solo se mencionaban: factura Z95, compañía, dealer, fecha. | Se añade filtro por **número de guía** y **estado** (Recibida / Pendiente validación / Aprobada). | El proceso operativo requiere rastrear cada guía de manera independiente, y el estado de validación es necesario para habilitar discrepancias. |
| 6 | **Contexto del proceso As-Is** | No existía. | Se agrega sección completa de **contexto As-Is** describiendo el flujo desde CAT → Panamerican → Gecolsa/Relianz con el rol de las órdenes de compra e interfaces. | Ricardo и el equipo explicaron el flujo completo (líneas 693–840, transcript 2502 10am): Gecolsa emite OC a Panamerican, Panamerican emite OC a CAT, luego CAT emite Z95 a Panamerican, quien genera INBs por compañía. Este contexto es esencial para entender las reglas de negocio. |
| 7 | **Reglas de negocio** | 3 reglas simples (RN-01 a RN-03). | 8 reglas de negocio extendidas (RN-01 a RN-08), incluyendo manejo de ventanas de tiempo, trazabilidad de correcciones, y pendiente de acuerdo con DHL. | Las sesiones revelaron restricciones operativas que deben ser reglas formales: las interfaces tienen ventanas horarias, los archivos de DHL requieren acuerdo de formato, las correcciones deben auditarse. |
| 8 | **Pendientes / Compromisos** | No contemplados. | Se documentan **4 compromisos** identificados en sesión con responsable asignado. | Los compromisos fueron levantados explícitamente en sesión (líneas 390–430, transcript 2502 10am) entre el equipo de operaciones, TI y el equipo de transformación. Documentarlos evita que se pierdan en el tiempo. |
| 9 | **Actor de la historia** | "Analista de Comercio Exterior". | "Analista de Comercio Exterior / Operaciones CrossDocking". | En sesión se evidenció que el proceso lo ejecutan tanto el equipo de Comercio Exterior como el de Operaciones; ambos interactúan con la pantalla de recepción. |
| 10 | **Notas técnicas** | No existían. | Se agrega sección de notas técnicas sobre Dynamics, la S400/versión 1 anterior y el bot de DHL. | Contexto necesario para el equipo de desarrollo para no ignorar el legado tecnológico y los acuerdos pendientes con terceros. |

---

## Ajustes que NO cambiaron

- El proceso sigue siendo: **recibir → validar → habilitar discrepancias**.
- La factura Z95 sigue siendo el objeto central de la validación.
- El filtro por compañía (Gecolsa / Relianz) se mantuvo.
- El bloqueo del proceso si Z95 no existe en el sistema se conservó (RN-01 y RN-02).

---

## Decisiones de Diseño Clave

> **Mecanismo de carga dual (automático + manual):** Se definió que mientras no se estandarice el formato con DHL, la carga manual debe estar siempre disponible como mecanismo de contingencia. El sistema no puede depender únicamente del bot de DHL.

> **Corrección de 5 campos de INB en SII:** Esta funcionalidad que existe en el sistema actual (S400/v1) debe mantenerse. Eliminiarla obligaría al equipo a volver a Dynamics para corregir datos, generando cuellos de botella operativos.

> **Pendiente crítico antes de desarrollo:** El formato estándar del archivo DHL debe acordarse **antes** del inicio del sprint de desarrollo de la integración automática.
