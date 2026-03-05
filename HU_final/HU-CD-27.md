# HU-CD-27: Recepción y Visualización de Relación Facturación Z95 vs INV PSC

**Proceso:** CROSSDOCKING
**Subproceso:** RECEPCIÓN DE FACTURACIÓN
**Requerimiento Original:** REQ-27 — El sistema debe permitir que la facturación emitida por PSC desde D365 llegue de manera automática a una tabla o archivo que permita visualizar la relación entre las Z95 CAT y las INV PSC. No debe limitar caracteres. Debe contemplar particularidades como remanufacturados (Premium + Core = Valor único total en SII).
**Versión:** 2.0
**Fecha de revisión:** 2026-03-04
**Estado:** Revisada con base en sesiones de levantamiento (25-Feb y 02-Mar-2026)

---

## Historia de Usuario

### Como:
Analista de Comercio Exterior (Commex)

### Quiero:
Que el sistema consolide y muestre en una sola pantalla la relación entre las facturas **Z95 de Caterpillar** y las facturas **INV de Panamerican (PSC)**, obtenidas automáticamente desde Dynamics 365. El sistema debe soportar una **cardinalidad 1:N** (una factura Z95 puede estar relacionada con múltiples facturas INV PSC) y permitir la visualización de referencias sin restricción de caracteres.

### Para:
Asegurar que no se va a nacionalizar de más ni de menos, que todas las referencias están correctamente registradas, y que el proceso de generación de guía puede habilitarse sin inconsistencias.

---

## Contexto del Proceso (As-Is)

1. **Dynamics 365 genera 3 interfaces** hacia el SII para el proceso CrossDocking:
   - **Interfaz 1:** Archivo plano con datos de la **Z95** (Caterpillar → Panamerican).
   - **Interfaz 2:** Archivo plano con datos de las **INV** (Panamerican → Gecolsa / Relianz), ya con la Z95 asociada.
   - **Interfaz 3:** Archivo plano con las **Órdenes de Compra** (usada principalmente para validación de discrepancias).
2. El SII actual muestra esta información como **un JOIN de tres tablas** en una sola pantalla.
3. Si solo están presentes los datos de la Interfaz 1 (Z95) pero no los de la Interfaz 2 (INV), significa que **Panamerican aún no ha facturado** al destinatario.
4. El robot de SIACO (agencia de aduanas) **NO interactúa** con esta pantalla.
5. El sistema debe soportar **simultaneidad de usuarios** (ej. un operador procesando Gecolsa y otro Relianz al mismo tiempo).
6. Actualmente, el término correcto es **INV** (no INB).

---

## Descripción Funcional

El sistema debe:

1. **Recibir automáticamente desde Dynamics 365** (vía archivos planos / interfaces):
   - Facturas **Z95** (CAT → Panamerican) — Interfaz 1
   - Facturas **INV PSC** (Panamerican → Gecolsa / Relianz) con Z95 ya asociada — Interfaz 2
   - Órdenes de Compra relacionadas — Interfaz 3

2. **Almacenar la información en tablas independientes** según interfaz de origen, conservando la trazabilidad de cada fuente.

3. **Mostrar en pantalla una vista consolidada (JOIN)** que relacione:
   - Z95 ↔ INV ↔ Órden de Compra
   - Visible por: Compañía, Dealer, Referencia, Cantidad, Valor, Estado

4. **Indicar visualmente el estado de relación**:
   - ✅ Z95 e INV correctamente relacionadas
   - ⚠️ Z95 presente pero sin INV asociada (Panamerican aún no ha facturado)
   - ❌ INV sin Z95 (inconsistencia que requiere gestión)

5. **Soportar referencias sin restricción de caracteres**:
   - Sin límite de longitud
   - Con caracteres especiales, mayúsculas y minúsculas
   - Combinaciones alfanuméricas

6. **Consolidar el valor de productos remanufacturados**:
   - Identifica el remanufacturado por el campo **Reman Indicator = 1** de Dynamics (no por sufijos como "R" o "CC")
   - Suma: `Valor SII = Product Price (Premium) + Cargo Core`
   - La **cantidad** permanece como **una sola unidad** (no se duplica por el Cargo Core)

7. **Permitir consulta auditable** por:
   - Número Z95
   - Número INV
   - Compañía (Gecolsa / Relianz)
   - Dealer
   - Fecha
   - Estado de relación

8. **Soportar simultaneidad de usuarios** sin bloqueos entre compañías.

9. **Manejo de Cardinalidad 1:N**: Permitir que una sola Z95 se desglose en múltiples INV PSC según el destinatario final (Gecolsa/Relianz) o parcializaciones de despacho.

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Visualización correcta de la relación Z95 ↔ INV
```gherkin
Dado que Dynamics ha procesado las 3 interfaces hacia el SII
Cuando el analista consulta la pantalla de facturación
Entonces el sistema debe mostrar la Z95, la INV asociada y la OC en una sola vista
  Y debe indicar que la relación está correctamente establecida.
```

### Escenario 2 – Z95 sin INV asociada
```gherkin
Dado que la Interfaz 1 (Z95) llegó pero la Interfaz 2 (INV) aún no
Cuando el analista consulta la pantalla
Entonces el sistema debe mostrar la Z95 con el estado "Pendiente INV"
  Y debe indicar que Panamerican aún no ha facturado a la compañía correspondiente.
```

### Escenario 3 – Referencia con caracteres especiales o longitud extendida
```gherkin
Dado que una referencia contiene caracteres especiales o más de N caracteres
Cuando el sistema recibe la información desde Dynamics
Entonces debe almacenarla sin truncar ni rechazar la referencia
  Y debe mostrarse completa en pantalla.
```

### Escenario 4 – Producto remanufacturado con Premium + Core
```gherkin
Dado que Dynamics envía un producto con Reman Indicator = 1
  Y el producto tiene una línea de Product Price (Premium) y una línea de Cargo Core
Cuando el sistema procesa la interfaz
Entonces debe sumar ambos valores y almacenar un único valor total en SII
  Y la cantidad debe reflejarse como una sola unidad.
```

### Escenario 5 – Simultaneidad de usuarios
```gherkin
Dado que un analista está procesando la compañía Gecolsa
Cuando otro analista accede simultáneamente para procesar Relianz
Entonces el sistema no debe bloquear a ninguno de los dos
  Y cada uno debe ver únicamente los datos de su compañía.
```

### Escenario 6 – Identificación correcta de remanufacturado
```gherkin
Dado que el campo Reman Indicator = 1 en Dynamics
Cuando el sistema procesa la referencia
Entonces debe marcarla como remanufacturada independientemente de su código o sufijo.
```

---

## Reglas de Negocio

| ID | Regla |
|-----|-------|
| RN-01 | Las 3 interfaces (Z95, INV, OC) deben almacenarse en tablas independientes en el SII. |
| RN-02 | La vista de relación Z95 ↔ INV debe ser un JOIN dinámico que se actualice con cada ejecución de interface. |
| RN-03 | Si la INV no está disponible, el sistema debe mostrar alerta pero no bloquear la consulta. |
| RN-04 | La relación Z95 ↔ INV debe estar validada antes de habilitar el proceso de discrepancias. |
| RN-05 | El campo **Reman Indicator** de Dynamics es la única fuente oficial para identificar remanufacturado. No se permite lógica basada en sufijos de referencia ("R", "CC", etc.). |
| RN-06 | El valor almacenado en SII para remanufacturados debe ser la suma: `Premium + Cargo Core`. |
| RN-07 | La cantidad de unidades para remanufacturados debe ser 1 (no se duplica por el Cargo Core). |
| RN-08 | El sistema no debe limitar la longitud ni los caracteres de las referencias. |
| RN-09 | El sistema debe soportar simultaneidad de usuarios por compañía sin bloqueos. |
| RN-10 | Todas las interfaces cargadas deben quedar auditadas (fecha, hora, usuario que ejecutó). |
| RN-11 | El robot de SIACO no interactúa con esta pantalla; cualquier integración con SIACO corresponde a una HU posterior (DO). |
| RN-12 | El término correcto es **INV** (Invoice), no INB. Todos los sistemas y documentación deben usar INV. |
| RN-13 | **Multi-INV**: El sistema debe agrupar y mostrar todas las INV asociadas a una misma Z95 en la vista de relación. |
| RN-14 | **Caracteres de Referencia**: La referencia NO debe ser limitante; debe aceptar mayúsculas, minúsculas, caracteres especiales y longitud extendida según venga en la OC. |

---

## Campos de la Vista Consolidada

| Campo | Fuente | Descripción |
|-------|--------|-------------|
| Número Z95 | Interfaz 1 | Factura Caterpillar |
| Número INV | Interfaz 2 | Factura Panamerican |
| Compañía | Interfaz 2 | Gecolsa / Relianz |
| Dealer | Interfaz 2 | Código del dealer asignado |
| Referencia | Interfaz 1/2 | Código de pieza (sin límite de caracteres) |
| Cantidad | Interfaz 1/2 | Unidades (1 para reman, sin duplicar) |
| Valor Z95 | Interfaz 1 | Valor facturado por CAT |
| Valor INV | Interfaz 2 | Valor facturado por PSC (Premium + Core para reman) |
| Reman Indicator | Interfaz 2 | 1 = Remanufacturado / 0 = Nuevo |
| OC relacionada | Interfaz 3 | Orden de compra asociada |
| Estado | Sistema | Relacionada / Pendiente INV / Inconsistente |
| Fecha carga | Sistema | Fecha de ejecución de la interfaz |

---

## Pendientes / Compromisos Identificados en Sesión

| # | Compromiso | Responsable |
|---|-----------|-------------|
| 1 | Documentar los campos exactos que llegan en cada una de las 3 interfaces desde Dynamics. | Ricardo / Equipo de Transformación |
| 2 | Confirmar si la Interfaz 3 (OC) se visualiza en la misma pantalla o solo se usa para discrepancias. | Daisy / Alan |
| 3 | Definir el criterio de "completitud" de la relación Z95-INV para habilitar discrepancias. | Commex / Líder funcional |
| 4 | Validar con Caterpillar los campos específicos de la Z95 que deben mostrarse en pantalla. | César / TI |

---

## Notas Técnicas

- La pantalla de facturación es un **JOIN de 3 tablas** (Z95, INV, OC); si alguna no está disponible, se muestra con estado de alerta.
- Las interfaces de Dynamics no son configurables desde el SII; corren en ventanas de tiempo (≈9 AM y ≈1 PM).
- El tiempo de procesamiento de las interfaces varía según el volumen de líneas de facturación del día.
- La corrección del término **INB → INV** fue confirmada en sesión (transcript 2502 10am, línea 1492).
