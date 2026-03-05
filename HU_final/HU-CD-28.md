# HU-CD-28: Gestión de Discrepancias entre Facturas Z95 (CAT) vs INV PSC

**Proceso:** CROSSDOCKING
**Subproceso:** DISCREPANCIAS
**Requerimiento Original:** REQ-28 — El sistema debe permitir hacer un paralelo entre las facturas CAT vs las facturas PSC para asegurar que no se va a nacionalizar de más o de menos (ej. cantidades facturadas).
**Versión:** 2.0
**Fecha de revisión:** 2026-03-04
**Estado:** Revisada con base en sesiones de levantamiento (25-Feb, 27-Feb y 02-Mar-2026)

---

## Historia de Usuario

### Como:
Analista de Comercio Exterior (Commex)

### Quiero:
Ejecutar un proceso de validación que compare automáticamente las facturas **Z95 de Caterpillar** contra las facturas **INV de Panamerican (PSC)**, identifique diferencias y genere un reporte de inconsistencias, para que el equipo de operaciones pueda gestionarlas antes de generar la guía de embarque.

### Para:
Garantizar que la cantidad y el valor de lo que se va a nacionalizar corresponde exactamente a lo facturado, evitando sobre-nacionalización o sub-nacionalización de mercancía.

---

## Contexto del Proceso (As-Is)

1. Una vez que las **3 interfaces de Dynamics** (Z95, INV, OC) están disponibles en el SII, el analista de Commex ejecuta el proceso de discrepancias.
2. El cruce se realiza entre la **Z95** (factura CAT) y la **INV** (factura PSC) usando la orden de compra como referencia adicional.
3. Si se detectan discrepancias, el SII genera un reporte que se envía al equipo de operaciones.
4. Las correcciones **pueden originarse en Dynamics** (cuando el error es de asociación Z95 vs INV o cantidades de fábrica) o **directamente en el SII** (cuando se trata de los **5 campos editables de la INV** para nacionalización).
5. Los **5 campos editables en el SII** son:
   - **Subpartida Arancelaria (BTN)**
   - **Unidad de Medida**
   - **Peso Neto**
   - **País de Origen**
   - **Descripción Arancelaria**
6. Una vez corregido, la interfaz se vuelve a ejecutar (si el cambio fue en Dynamics) o se guarda el cambio local (si fue en el SII) y se reprocesa el cruce.
6. Los **estados de la factura** en el sistema son visibles para el equipo de Tesorería (Z giros), quienes los usan para gestionar los giros al exterior.
7. Una Z95 que ya fue utilizada en una guía **no debe aparecer nuevamente** en el proceso de discrepancias.

---

## Estados del Ciclo de Vida de la Factura

| Estado | Descripción |
|--------|-------------|
| **Sin guía** | La factura llegó al SII pero aún no se ha generado una guía de embarque. |
| **Con guía** | La factura fue asignada a una guía de embarque. |
| **Con declaración** | La declaración de importación (DIM) fue generada para esta factura. |
| **Con levante** | La mercancía fue levantada (salió de la zona aduanera). |

> Estos estados son utilizados también por el equipo de **Tesorería (Z giros)** para gestionar los pagos al exterior.

---

## Descripción Funcional

El sistema debe:

1. **Ejecutar el proceso de discrepancias** a partir de las facturas Z95 ya validadas en el SII, cruzándolas contra las INV PSC correspondientes.

2. **Identificar y clasificar las discrepancias** encontradas:
   - Diferencias en **cantidades** (Z95 vs INV)
   - Diferencias en **valores** (FOB)
   - Facturas Z95 **sin INV asociada** (Panamerican aún no facturó)
   - INV **sin Z95 relacionada** (inconsistencia de datos)
   - **Referencias no coincidentes** entre Z95 e INV
   - **Asociación incorrecta** de Z95 con INV

3. **Permitir ejecución selectiva:** el usuario puede seleccionar facturas Z95 específicas para ejecutar discrepancias (no necesariamente todas).

4. **Generar reporte de discrepancias** en caso de inconsistencias:
   - Formato: Excel (.xlsx)
   - Contenido: datos Z95, datos INV, diferencia detectada, tipo de inconsistencia, cantidades comparadas, valores
   - Distribución: envío automático al correo del equipo de Operaciones definido como parámetro

5. **Controlar el estado de la factura** durante el proceso:
   - Factura sin guía → disponible para discrepancias
   - Factura con guía → bloqueada para discrepancias (ya fue procesada)

6. **Permitir revalidación:** una vez corregida la discrepancia en Dynamics o en el SII (5 campos editables de INV), el usuario vuelve a ejecutar el proceso para confirmar que no hay inconsistencias.

7. **Bloquear generación de guía** si existen discrepancias activas sin resolver.

8. **No alterar datos originales** de las interfaces; las correcciones deben quedar auditadas.

---

## Flujo Operativo

```
[Interfaces cargadas] → [Analista ejecuta discrepancias] → ¿Hay diferencias?
       │ NO                                                        │ SÍ
       ▼                                                           ▼
[Factura lista para guía] ←← [Corrección en Dynamics / SII] ← [Reporte Excel enviado a Operaciones]
                                         ↓
                              [Interfaz se reprocesa]
                                         ↓
                              [Analista re-ejecuta discrepancias]
```

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Sin discrepancias: factura aprobada
```gherkin
Dado que una Z95 y su INV asociada tienen las mismas cantidades, referencias y valores
Cuando el analista ejecuta el proceso de discrepancias
Entonces el sistema no debe generar reporte de inconsistencias
  Y la factura debe quedar disponible para generación de guía.
```

### Escenario 2 – Cantidades diferentes
```gherkin
Dado que la cantidad en Z95 es distinta a la cantidad en INV
Cuando se ejecuta el proceso de discrepancias
Entonces el sistema debe identificar la diferencia
  Y generar el reporte Excel con el detalle de la inconsistencia
  Y enviar el reporte al correo de Operaciones configurado.
```

### Escenario 3 – Z95 sin INV asociada
```gherkin
Dado que existe una Z95 para la cual Panamerican aún no ha generado INV
Cuando se ejecuta el proceso de discrepancias
Entonces el sistema debe reportarla como "Z95 sin INV"
  Y no debe bloquear la ejecución del proceso para las demás facturas.
```

### Escenario 4 – Corrección y revalidación
```gherkin
Dado que una discrepancia fue corregida en Dynamics
  Y la interfaz se volvió a ejecutar actualizando los datos en el SII
Cuando el analista re-ejecuta el proceso de discrepancias
Entonces la factura ya no debe aparecer como inconsistente
  Y debe quedar disponible para generación de guía.
```

### Escenario 5 – Bloqueo de guía por discrepancia activa
```gherkin
Dado que una Z95 tiene discrepancias activas sin resolver
Cuando el usuario intenta generar una guía que incluya esa Z95
Entonces el sistema debe bloquear la operación
  Y mostrar el detalle de las discrepancias pendientes.
```

### Escenario 6 – Factura ya procesada no aparece en discrepancias
```gherkin
Dado que una Z95 ya fue utilizada para generar una guía
Cuando el analista consulta la vista de discrepancias
Entonces esa Z95 no debe aparecer como pendiente
  Y su estado debe ser "Con guía".
```

### Escenario 7 – Ejecución parcial por selección
```gherkin
Dado que el analista selecciona únicamente un subconjunto de facturas Z95
Cuando ejecuta el proceso de discrepancias
Entonces el sistema debe procesar únicamente las facturas seleccionadas
  Y no debe afectar el estado de las demás.
```

---

## Reglas de Negocio

| ID | Regla |
|----|-------|
| RN-01 | No se puede generar una guía si existen discrepancias activas para las facturas Z95 involucradas. |
| RN-02 | La validación de discrepancias debe realizarse por compañía (Gecolsa o Relianz por separado). |
| RN-03 | El proceso debe permitir ejecución parcial por selección de facturas. |
| RN-04 | Todo reporte generado debe quedar auditado: fecha, hora, usuario y correo de destino. |
| RN-05 | Las discrepancias de asociación (Z95 ↔ INV) o cantidades de fábrica deben resolverse en Dynamics; el SII solo permite ajustar los **5 campos críticos de la INV** (BTN, Unidad, Peso, Origen, Descripción). |
| RN-06 | El proceso de discrepancias no debe alterar los datos originales de las interfaces; solo puede registrar correcciones auditadas. |
| RN-07 | Una factura Z95 en estado "Con guía" queda bloqueada y no debe aparecer en el proceso de discrepancias. |
| RN-08 | El correo de destino del reporte debe ser configurable como parámetro del sistema (no hardcodeado). |
| RN-09 | El estado de la factura es visible para el equipo de Tesorería (Z giros): sin guía / con guía / con declaración / con levante. |
| RN-10 | El cruce incluye como mínimo: cantidades, valores FOB, referencias y asociación Z95 ↔ INV. |

---

## Campos del Reporte de Discrepancias (Excel)

| Campo | Fuente |
|-------|--------|
| Número Z95 | Interfaz 1 |
| Número INV | Interfaz 2 |
| Compañía | Interfaz 2 |
| Dealer | Interfaz 1 |
| Referencia Z95 | Interfaz 1 |
| Referencia INV | Interfaz 2 |
| Cantidad Z95 | Interfaz 1 |
| Cantidad INV | Interfaz 2 |
| Diferencia de cantidad | Calculado |
| Valor FOB Z95 | Interfaz 1 |
| Valor FOB INV | Interfaz 2 |
| Diferencia de valor | Calculado |
| Tipo de inconsistencia | Sistema |
| Comentario / Observación | Sistema |

---

## Pendientes / Compromisos de Sesión

| # | Compromiso | Responsable |
|---|-----------|-------------|
| 1 | Documentar exactamente los 5 campos de la INV que son editables en el SII. | Daisy / Equipo de Transformación |
| 2 | Definir el correo (o lista de distribución) de Operaciones para el reporte de discrepancias. | Commex / Operaciones |
| 3 | Confirmar si el cruce de la OC (Interfaz 3) interviene en la validación de discrepancias o es solo de referencia. | Alan / Daisy |
| 4 | Definir el criterio de "Z95 válida para discrepancias" (¿debe tener INV asociada o puede ejecutarse sin ella?). | Commex / Lider funcional |
