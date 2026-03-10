# HU-CD-29: Sincronización de Facturas PSC y Z95 desde D365

**Proceso:** CROSSDOCKING
**Subproceso:** SINCRONIZACIÓN DE FACTURACIÓN – ACTUALIZACIÓN DE DATOS
**Requerimiento Original:** REQ-29 — Actualización de datos desde D365.
**Versión Consolidada:** 3.0
**Estado:** Integrada (Aportes Negocio + Funcional)

---

## Historia de Usuario

### Como:
Sistema SII 2.0 (Proceso Automático)

### Quiero:
Sincronizar automáticamente desde Dynamics 365 las facturas Z95 de Caterpillar y las facturas INV de Panamerican, aplicando estrictas reglas de unicidad y control de duplicados, y registrando cada sincronización en un log de auditoría.

### Para:
Mantener la información del SII siempre actualizada y consistente con el sistema de origen, sin generar duplicidades, sobrescrituras indebidas ni reprocesos de registros ya cargados.

---

## Contexto del Proceso (As-Is)

Las facturas llegan al proceso de CrossDocking mediante **3 interfaces provenientes de Dynamics 365**:
- **Interfaz 1:** Archivo con datos de las facturas Z95 (Caterpillar → Panamerican).
- **Interfaz 2:** Archivo con datos de las facturas INV + Z95 relacionada (Panamerican → Destinatario).
- **Interfaz 3:** Archivo con datos de Órdenes de Compra.

La sincronización se ejecutará de manera periódica (programada) o manual (forzada) a través de servicios / eventos (API). El SII debe analizar la data que entra y decidir si inserta el registro o lo descarta por duplicidad.

---

## Arquitectura Lógica de Integración

- La integración debe darse por servicios (API/Eventos) y **no** mediante conexiones directas a bases de datos de Dynamics.
- La arquitectura debe asegurar que los fallos en una interfaz no derriben a las demás (desacoplamiento).
- Cada sincronización actuará únicamente a nivel de inserción sobre las tablas maestras de facturación de CrossDocking, respetando los estados operativos posteriores.

---

## Reglas de Sincronización

| ID | Regla Funcional |
|----|-------|
| RN-01 | **Clave Única**: El identificador definitivo de una factura para saber si ya existe es la combinación de: `Número + Fecha de Factura (Año) + Compañía`. No se usan IDs autogenerados para la detección de duplicados que entran por interfaz. |
| RN-02 | Si el registro validado mediante RN-01 ya existe: No debe volver a cargarse ni debe sobrescribirse. Se omite. |
| RN-03 | Facturas de **años anteriores ya procesadas** no se cargan automáticamente al ciclo actual. Solo pueden consultarse de forma histórica o manual. |
| RN-04 | Las Órdenes de Compra asociadas a facturas de años pasados ya procesados **no se cargan** ni se arrastran hacia el ciclo actual de operación SII 2.0. |
| RN-05 | **Reproceso Legítimo**: Si llega una factura con el **mismo número de factura**, pero con **nueva fecha (año diferente)**, el sistema la considera un registro totalmente válido y nuevo (Ver manejo de HU-CD-30). |
| RN-06 | La sincronización **jamás debe alterar** registros que ya se encuentran avanzados en el flujo logístico (estados: *Con guía*, *Con declaración*, *Con levante*). |
| RN-07 | Si un lote de sincronización falla en algunos de sus registros por error de formato, los registros exitosos del lote se insertan en firme (conservación de datos), y los fallidos se llevan a un log. |

---

## Monitoreo y Auditoría del Proceso

El sistema debe exponer un **Log de Sincronización** consultable, conteniendo los siguientes datos mínimos de control:

| Campo | Descripción |
|-------|-------------|
| Fecha y hora | Momento exacto de finalización de la sincronización. |
| Origen / Interfaz | Qué interfaz se procesó (Z95 / INV / OC). |
| Registros Recibidos | Totalidad del bloque analizado. |
| Registros Nuevos | Cuántos fueron insertados con éxito. |
| Registros Omitidos | Cuántos se ignoraron por ser duplicados detectados. |
| Registros Fallidos | Cuántos fueron rechazados por error de integridad o formato. |
| Estado Global | Sincronización: Exitosa / Parcial / Fallida. |
| Detalle Errores | Listado del registro exacto que falló y la causa técnica de su rechazo. |

El log debe permitir filtros de consulta por:
- Interfaz (origen).
- Rango de fechas de ejecución.
- Estado de ejecución (Exitosa/Parcial/Fallida).

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Abandono de registro duplicado
```gherkin
Dado que una factura con número Z95-001 de la compañía Gecolsa, con año 2025, ya existe en SII
Cuando la Interfaz 1 envíe nuevamente un bloque que contenga esta factura
Entonces el sistema debe omitir la inserción
  Y sumar "1" en el contador de Registros Omitidos del log
  Y el registro original en base de datos no debe ser sobrescrito ni modificado.
```

### Escenario 2 – Nueva factura validada (Misma numeración, distinto año)
```gherkin
Dado que existe en base de datos una factura Z95-999 del año 2023
Cuando se ejecuta la interfaz de Z95 de Dynamics
  Y recibe una factura idéntica Z95-999 pero del año 2025
Entonces el sistema debe identificarla como válida
  Y debe insertarla creando un nuevo registro en el maestro de facturas
  Y sumar "1" en el contador de Registros Nuevos.
```

### Escenario 3 – Manejo Parcial de Errores de Interfaz
```gherkin
Dado que un proceso de interfaz obtiene un bloque de 10 facturas INV
  Pero 2 de esas facturas carecen de 'Compañía' o tienen fecha inválida
Cuando finalice el proceso programado
Entonces el sistema debe haber insertado 8 facturas INV correctamente
  Y debe registrar el evento como estado "Parcial"
  Y detallar los 2 errores en el log con referencia clara a qué factura falló y por qué.
```

### Escenario 4 – Intento de sobre-escritura en flujo avanzado
```gherkin
Dado que la Z95-101 (2025) tiene estado operativo "Con guía en curso"
Cuando una sincronización forzada traiga actualización de esta Z95 desde D365
Entonces el sistema debe rechazar tajantemente cualquier actualización a dicho registro
  Y documentar en el log de error que la "factura está en estado no modificable".
```

---

## Pendientes / Compromisos

* **Pendiente** -> Confirmar el esquema técnico exacto de payloads de D365 y el mecanismo real de escucha de la Azure Service Bus. Responsable: Equipo de Desarrollo Backend / TI.
* **Pendiente** -> Definir oficialmente la cadencia automática de cada interfaz (frecuencia CRON/Schedulers) basándose en volúmenes. Responsable: Líder Funcional.
* **Pendiente** -> Definir el rol o usuarios (Commex o SOP) que recibirán autorización explícita para pulsar el botón "Sincronizar Manualmente" y ver el panel de logs. Responsable: Dueño del Proceso Empresarial.
