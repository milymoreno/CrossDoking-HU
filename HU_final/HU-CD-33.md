# HU-CD-33: Generación de Documento de Transporte (Guía)

**Proceso:** CROSSDOCKING
**Subproceso:** GENERAR GUÍA
**Requerimiento Original:** REQ-33 — El proceso debe ser generado por DEALERS. El sistema tendrá la capacidad de asignar las facturas a un número de documento de transporte, relacionando: # Guía, Dealer, fecha guía, tipo mercancía, exportador, aduana, vía transporte, transportadora, embarcadora, fletes, moneda, incoterm, país compra-origen-procedencia.
**Versión:** 2.0
**Fecha de revisión:** 2026-03-04
**Estado:** Revisada con base en sesiones de levantamiento (25-Feb, 27-Feb y 02-Mar-2026)

---

## Historia de Usuario

### Como:
Analista de Comercio Exterior (Commex)

### Quiero:
Generar un Documento de Transporte (Guía de Embarque) asociando facturas Z95/INV previamente validadas, agrupadas por Dealer, con todos los datos logísticos y aduaneros requeridos.

### Para:
Consolidar correctamente los embarques antes de iniciar el proceso aduanero, garantizando que toda la información esté completa, validada y auditada.

---

## Contexto del Proceso (As-Is)

La generación de guías actualmente se realiza en el SII legado, ingresando manualmente los datos logísticos por pantalla. Anderson (operador) ingresa al subsistema IC400, carga la guía con los datos del dealer y asocia las facturas. El nuevo sistema debe replicar y mejorar este flujo, añadiendo los prerrequisitos de validación de discrepancias, licencias y años.

---

## Prerrequisitos Obligatorios

Antes de generar una guía, el sistema debe verificar que cada factura Z95 asociada cumpla **todos** los siguientes requisitos:

| Validación | HU de referencia | ¿Bloquea? |
|-----------|-----------------|-----------|
| Sin discrepancias activas | HU-CD-28 | ✅ Sí |
| Año correcto (clave compuesta) | HU-CD-30 | ✅ Sí |
| Licencia asignada (cuando aplica) | HU-CD-32 | ✅ Sí |
| Remanufacturados validados | HU-CD-34 | ✅ Sí |
| Saldo disponible (si es parcial) | HU-CD-31 | ✅ Sí |

Si alguna validación falla, el sistema debe **bloquear la generación** y mostrar el motivo detallado.

---

## Descripción Funcional

### Paso 1 — Selección de Facturas
- El usuario selecciona una o más facturas Z95 validadas de la tabla de pendientes.
- Las facturas deben pertenecer al mismo **Dealer** y a la misma compañía.

### Paso 2 — Registro de Datos Obligatorios de la Guía

| Campo | Descripción | Tipo |
|-------|-------------|------|
| Número de Guía | Asignado automáticamente (único por compañía y año) | Automático |
| Dealer logístico | Código del dealer de destino | Obligatorio |
| Fecha guía | Fecha de emisión del documento | Obligatorio |
| Tipo de mercancía | Nueva / Remanufacturada | Obligatorio |
| Exportador | Razón social del exportador | Obligatorio |
| Aduana | Aduana de ingreso al país | Obligatorio |
| Vía de transporte | Aérea / Marítima / Terrestre / Courier | Obligatorio |
| Transportadora | Empresa transportadora | Obligatorio |
| Embarcadora | Responsable del embarque | Obligatorio |
| Fletes | Valor del flete | Obligatorio |
| Moneda | Moneda del flete (USD, COP, etc.) | Obligatorio |
| Incoterm | Condición de entrega (FOB, CIF, etc.) | Obligatorio |
| País de compra | País donde se realizó la compra | Obligatorio |
| País de origen | País de fabricación del producto | Obligatorio |
| País de procedencia | País desde donde se embarca | Obligatorio |

### Paso 3 — Generación y Confirmación
- El sistema muestra un resumen de la guía antes de confirmar: facturas incluidas, cantidades, dealer, totales.
- El usuario confirma → El sistema genera el número de guía y cambia el estado de las facturas.

---

## Lógica Post-Generación

Tras generar exitosamente la guía:
- Las Z95 incluidas **cambian de estado** según consumo (Ver HU-CD-31).
- Las Z95 totalmente consumidas **desaparecen de la tabla de pendientes**.
- El número de guía queda asociado a las facturas para trazabilidad.

---

## Generación Individual vs. Masiva

| Modalidad | Descripción |
|-----------|-------------|
| **Individual** | El usuario selecciona manualmente las facturas a incluir. |
| **Masiva** | El sistema agrupa automáticamente las facturas por Dealer y genera múltiples guías en un solo proceso. |

---

## Estados de la Guía

| Estado | Descripción |
|--------|-------------|
| Generada | Guía creada; proceso logístico iniciado. |
| En proceso DIM | Declaración de Importación en elaboración. |
| DIM generada | Declaración enviada a aduana. |
| Cerrada | Proceso completado y levante obtenido. |
| Anulada | Guía cancelada antes del inicio del proceso aduanero. |

---

## Validaciones Dealer

- El dealer debe ser **válido para la compañía** (Gecolsa o Relianz).
- No se permite mezclar mercancía nueva y remanufacturada en la misma guía/dealer (ver Req 35).
- No se permite mezclar dealers distintos en la misma guía.

---

## Control de Integridad y Auditoría

- **No se puede modificar** una guía una vez generada la DIM.
- Se permite **anulación controlada** siempre que no se haya iniciado el proceso aduanero.
- Toda guía generada queda auditada: usuario, fecha, facturas asociadas, cantidades, dealer.

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Generación exitosa
```gherkin
Dado que las facturas Z95 seleccionadas cumplen todas las validaciones
Cuando el usuario completa los datos de la guía y confirma
Entonces el sistema debe asignar un número único de guía
  Y cambiar el estado de las facturas según consumo
  Y registrar la auditoría completa.
```

### Escenario 2 – Bloqueo por discrepancia activa
```gherkin
Dado que una factura Z95 incluida en la selección tiene discrepancias activas
Cuando el usuario intente generar la guía
Entonces el sistema debe bloquear la operación
  Y mostrar el detalle de la factura con discrepancia pendiente.
```

### Escenario 3 – Parcialización desde generación de guía
```gherkin
Dado que una Z95 tiene 10 unidades disponibles
  Y el usuario incluye solo 4 unidades en la guía
Cuando la guía se genere exitosamente
Entonces la Z95 debe quedar con estado "Parcialmente utilizada"
  Y las 6 unidades restantes deben seguir disponibles en tabla de pendientes.
```

### Escenario 4 – Dealer inválido
```gherkin
Dado que el dealer seleccionado no corresponde a la compañía de la operación
Cuando el usuario intente generar la guía
Entonces el sistema debe bloquear la operación
  Y mostrar el mensaje: "Dealer no válido para esta compañía".
```

### Escenario 5 – Resumen de confirmación
```gherkin
Dado que el usuario ha seleccionado facturas y completado los datos de la guía
Cuando presione el botón "Generar guía"
Entonces el sistema debe mostrar un resumen con: facturas, cantidades, valor total y dealer
  Y esperar confirmación del usuario antes de ejecutar.
```

---

## Reglas de Negocio

| ID | Regla |
|----|-------|
| RN-01 | No se puede generar guía con discrepancias activas en las facturas incluidas. |
| RN-02 | El número de guía es único por compañía y año; asignado automáticamente por el sistema. |
| RN-03 | No se permite mezclar dealers distintos en la misma guía. |
| RN-04 | Toda guía generada debe quedar auditada (usuario, fecha, facturas, cantidades). |
| RN-05 | Las facturas completamente procesadas desaparecen de la tabla de pendientes tras generar la guía. |
| RN-06 | No se puede modificar la guía una vez generada la DIM. |
| RN-07 | La generación masiva respeta las mismas validaciones que la individual. |
| RN-08 | El sistema debe mostrar resumen de confirmación antes de ejecutar la generación. |

---

## Pendientes / Compromisos de Sesión

| # | Compromiso | Responsable |
|---|-----------|-------------|
| 1 | Confirmar el formato del número de guía (prefijo, longitud, año incluido). | Commex / TI |
| 2 | Definir los campos que pueden pre-completarse desde la parametrización (ej. transportadora por defecto por dealer). | Commex |
| 3 | Confirmar si hay tipos de mercancía adicionales además de Nueva / Remanufacturada. | Commex |
