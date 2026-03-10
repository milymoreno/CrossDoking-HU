# HU-CD-28: Gestión de Discrepancias entre Facturas Z95 (CAT) vs INV PSC

**Proceso:** CROSSDOCKING
**Subproceso:** GESTIÓN DE DISCREPANCIAS (Módulo: **Mesa de Discrepancias**)
**Requerimiento Original:** REQ-28 — El sistema debe permitir hacer un paralelo entre las facturas CAT vs las facturas PSC para asegurar que no se va a nacionalizar de más o de menos.
**Versión Consolidada:** 4.0
**Estado:** Integrada (Aportes Negocio + Funcional)

---

## Historia de Usuario

### Como:
Analista de Comercio Exterior (Rol: Auditor Commex)

### Quiero:
Ejecutar un proceso de validación que compare automáticamente las facturas **Z95 de Caterpillar** contra las facturas **INV de Panamerican (PSC)**, identifique diferencias (en cant, valores, ref) y genere un reporte de inconsistencias.

### Para:
Garantizar que la cantidad y el valor de lo que se va a nacionalizar corresponde exactamente a lo facturado, evitando sobre-nacionalización o sub-nacionalización de mercancía, y bloqueando la generación de guías si hay errores.

---

## Contexto del Proceso (As-Is)

1. El cruce se realiza entre la **Z95** (factura CAT) y la **INV** (factura PSC).
2. Las correcciones pueden originarse en Dynamics (errores de asociación o cantidades) o directamente en el SII para los **5 campos editables de la INV**:
   - Subpartida Arancelaria (BTN), Unidad de Medida, Peso Neto, País de Origen, Descripción Arancelaria.
3. Los estados de la factura son visibles para el equipo de Tesorería (Z giros) para gestionar pagos al exterior.

### Estados del Ciclo de Vida de la Factura:
| Estado | Descripción |
|--------|-------------|
| **Sin guía** | Disponible para discrepancias. |
| **Con guía** | Asignada a guía (bloqueada para discrepancias). |
| **Con declaración** | DIM generada. |
| **Con levante** | Mercancía liberada. |

---

## Descripción Funcional

1. **Cruce Automático:** El sistema cruza Z95 validadas vs INV PSC correspondientes.
2. **Tipos de Discrepancia:**
   - Cantidades y Valores (FOB) diferentes.
   - Referencias no coincidentes.
   - Z95 sin INV o INV sin Z95.
   - Asociación de compañía (Gecolsa o Relianz) incorrecta.
3. **Bloqueo:** No se puede generar guía con discrepancias activas.
4. **Ejecución Parcial:** Opción de seleccionar un subconjunto de facturas para validar.

---

## Campos del Reporte de Discrepancias (Excel)

Si existen errores o diferencias, se genera un archivo de reporte tipo Excel que será enviado automáticamente al equipo de Operaciones (correo configurable parametrizado). A continuación se describe cada campo del reporte:

| Campo | Descripción |
|-------|-------------|
| **Número Z95** | Identificador de la factura proveniente de Caterpillar (Interfaz 1). |
| **Número INV** | Identificador de la factura proveniente de Panamerican (Interfaz 2). |
| **Compañía** | Entidad a la que pertenece la operación comercial (Gecolsa o Relianz). |
| **Dealer** | Código del distribuidor asociado. |
| **Referencia Z95** | Número de parte o referencia de artículo reportada en la factura Z95. |
| **Referencia INV** | Número de parte o referencia de artículo reportada en la factura INV. |
| **Cantidad Z95** | Cantidad total facturada en origen por Caterpillar. |
| **Cantidad INV** | Cantidad total facturada en origen por Panamerican. |
| **Diferencia de Cantidad** | Valor calculado que muestra la disparidad numérica (Cantidad Z95 - Cantidad INV). |
| **Valor FOB Z95** | Valor monetario (FOB) reportado en la factura de Caterpillar. |
| **Valor FOB INV** | Valor monetario (FOB) reportado en la factura de Panamerican. |
| **Diferencia de Valor** | Valor calculado que muestra la disparidad monetaria (Valor FOB Z95 - Valor FOB INV). |
| **Tipo de Inconsistencia** | Clasificación de la discrepancia detectada (ej. Diferencia de valor, Z95 sin INV, Referencia no encontrada). |
| **Observación** | Comentario complementario o mensaje de detalle emitido automáticamente por el sistema. |

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Sin discrepancias (Aprobación)
**Dado** que Z95 e INV coinciden en cantidades, referencias y valores.
**Cuando** el analista ejecuta el proceso.
**Entonces** no se genera reporte y la factura queda "Sin guía" (estando disponible para el flujo siguiente).

### Escenario 2 – Diferencias detectadas
**Dado** que existe diferencia en valor o cantidad.
**Cuando** se ejecuta el proceso.
**Entonces** el sistema detecta la diferencia, genera un Excel auditado, lo guarda en Blob Storage, lo envía al correo de operaciones, y bloquea internamente la creación de la guía.

### Escenario 3 – Corrección y revalidación
**Dado** una discrepancia fue corregida en Dynamics o SII.
**Cuando** la interfaz se reprocesa y el analista re-ejecuta el proceso de discrepancias.
**Entonces** la factura no debe tener inconsistencias reportadas y queda disponible.

### Escenario 4 – Integridad / Factura ya procesada
**Dado** que una Z95 ya fue usada formalmente en una guía ("Con guía").
**Cuando** se consulte la vista general.
**Entonces** no debe volver a aparecer ni ser evaluada.

---

## Reglas de Negocio
- **RN-01:** Bloqueo de guía si existen discrepancias activas.
- **RN-02:** La validación debe realizarse por compañía (Gecolsa vs Relianz).
- **RN-03:** Las correcciones de interfaz (cantidades, asociación) deben hacerse en Dynamics; el SII solo altera sus 5 campos propios editables.
- **RN-04:** Todo reporte y corrección debe estar auditado y trazable en el sistema.
- **RN-05:** Parámetro de correo de destino no debe quedar fijo (no hardcodeado).

---

## Pendientes de Sesión

- Pendiente -> Documentar exactamente los 5 campos de la INV que son editables en el SII.
- Pendiente -> Definir el correo (o lista de distribución) de Operaciones para el envío del reporte de discrepancias.
- Pendiente -> Confirmar si el cruce de la Orden de Compra (Interfaz 3) interviene de manera activa en la validación de discrepancias o es solamente de referencia informativa.
- Pendiente -> Definir el criterio estricto de "Z95 válida para discrepancias" (establecer si necesariamente deba tener una INV asociada o pueda ejecutarse sin ella inicialmente).
