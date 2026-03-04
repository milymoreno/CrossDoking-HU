# HU-CD-29: Sincronización de Facturas PSC y Z95 desde D365

**Proceso:** CROSSDOCKING
**Subproceso:** SINCRONIZACIÓN DE FACTURACIÓN – ACTUALIZACIÓN DE DATOS
**Requerimiento Original:** REQ-29 — Actualización de datos desde D365.
**Versión:** 2.0
**Fecha de revisión:** 2026-03-04
**Estado:** Revisada con base en sesiones de levantamiento (25-Feb, 27-Feb y 02-Mar-2026)

---

## Historia de Usuario

### Como:
Sistema SII 2.0

### Quiero:
Sincronizar automáticamente desde Dynamics 365 las facturas Z95 de Caterpillar y las facturas INV de Panamerican, aplicando reglas de unicidad y control de duplicados, y registrando cada sincronización en un log de auditoría.

### Para:
Mantener la información del SII siempre actualizada y consistente con D365, sin generar duplicados, sobrescrituras indebidas ni reprocesos de registros ya procesados.

---

## Contexto del Proceso (As-Is)

Las facturas llegan al SII mediante **3 interfaces de Dynamics 365**:
- **Interfaz 1:** Facturas Z95 (Caterpillar → SII)
- **Interfaz 2:** Facturas INV + Z95 relacionada (Panamerican → SII)
- **Interfaz 3:** Órdenes de Compra (Gecolsa/Relianz/Panamerican → SII)

Cada interfaz se ejecuta de forma programada o manual. El SII debe controlar que los datos que llegan no generen duplicidades, especialmente en el caso de facturas históricas o consecutivos repetidos (ver HU-CD-30).

---

## Arquitectura de Integración

- La integración se realiza mediante **servicios (API)** entre Dynamics 365 y el SII.
- **No** se permite acceso directo a bases de datos productivas de D365.
- La arquitectura debe evitar **acoplamiento fuerte** entre sistemas (el fallo de uno no debe derribar al otro).
- Cada interfaz tiene su propio proceso de sincronización; fallen independientemente.

---

## Reglas de Sincronización

| ID | Regla |
|----|-------|
| RN-01 | La **clave única** de una factura es: Número + Fecha (año) + Compañía. Si ya existe, no se vuelve a cargar. |
| RN-02 | Facturas de **años anteriores ya procesadas** no se cargan automáticamente. Solo pueden visualizarse si no fueron procesadas. |
| RN-03 | Órdenes asociadas a facturas de años pasados ya procesados **no se cargan** en SII 2.0. |
| RN-04 | Si llega una factura con el **mismo número** pero **nueva fecha** (año), se considera un registro válido nuevo y se carga. |
| RN-05 | Si una sincronización falla parcialmente, los registros exitosos se conservan y se registra cada error individual. |
| RN-06 | La sincronización no debe alterar registros ya procesados (con guía, con declaración o con levante). |

---

## Monitoreo del Proceso

El sistema debe exponer un panel o log de sincronización con:

| Campo | Descripción |
|-------|-------------|
| Fecha y hora | Momento exacto de la sincronización |
| Interfaz | Nombre (Z95 / INV / OC) |
| Cantidad procesados | Número de registros cargados exitosamente |
| Cantidad con error | Número de registros rechazados |
| Estado | Exitosa / Parcial / Fallida |
| Detalle de errores | Tipo de error y registro afectado |

---

## Consulta de Registros Sincronizados

El sistema debe permitir filtrar por:

- Número de factura (Z95 o INV)
- Fecha / Rango de fechas
- Compañía (Gecolsa / Relianz)
- Dealer
- Estado de la factura (Sin guía / Con guía / Con declaración / Con levante)
- Tipo de error (si aplica)
- Interfaz de origen (Interfaz 1 / 2 / 3)

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Factura ya procesada no se duplica
```gherkin
Dado que una factura con número Z95-001 y fecha 2025-01-15 ya existe en el SII
Cuando la interfaz envíe nuevamente esa misma factura con los mismos datos
Entonces el sistema no debe cargarla nuevamente
  Y debe registrar el rechazo en el log indicando "registro duplicado".
```

### Escenario 2 – Factura de año anterior no se carga automáticamente
```gherkin
Dado que llega una factura Z95-999 con fecha 2023-03-01
  Y ya fue procesada en el ciclo 2023
Cuando se ejecute la sincronización automática
Entonces el sistema no debe cargarla.
```

### Escenario 3 – Misma numeración, nueva fecha = nuevo registro
```gherkin
Dado que existe Z95-001 del año 2023
  Y llega Z95-001 con fecha 2025-01-10
Cuando se ejecute la sincronización
Entonces el sistema debe crear un nuevo registro diferenciado por año.
```

### Escenario 4 – Error parcial en sincronización
```gherkin
Dado que durante la sincronización 3 de 10 registros tienen errores de formato
Cuando finalice el proceso
Entonces los 7 registros válidos deben estar cargados
  Y los 3 con error deben quedar registrados en el log consultable.
```

### Escenario 5 – No modificar registros procesados
```gherkin
Dado que una factura Z95 tiene estado "Con guía"
Cuando la interfaz intente cargar nuevamente esa factura con datos diferentes
Entonces el sistema debe rechazar la operación
  Y notificar en el log "factura en estado no modificable".
```

---

## Pendientes / Compromisos de Sesión

| # | Compromiso | Responsable |
|---|-----------|-------------|
| 1 | Confirmar el esquema técnico exacto del API de D365 (endpoints o método de integración). | Daisy / Equipo TI |
| 2 | Definir la frecuencia de ejecución automática de las interfaces (¿cada cuánto tiempo se sincronizan?). | Equipo TI / Lider funcional |
| 3 | Definir el rol/usuario que puede consultar el log de sincronización. | Commex / TI |
