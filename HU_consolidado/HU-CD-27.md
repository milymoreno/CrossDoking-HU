# HU-CD-27: Recepción y Visualización de Relación Facturación Z95 vs INV PSC

**Proceso:** CROSSDOCKING
**Subproceso:** RECEPCIÓN DE FACTURACIÓN (Módulo: **Monitor de Facturación CD**)
**Requerimiento Original:** REQ-27 — El sistema debe permitir que la facturación emitida por PSC desde D365 llegue de manera automática a una tabla o archivo que permita visualizar la relación entre las Z95 CAT y las INV PSC. No debe limitar caracteres. Debe contemplar particularidades como remanufacturados (Premium + Core = Valor único total en SII).
**Versión Consolidada:** 3.0
**Estado:** Integrada (Aportes Negocio + Funcional)

---

## Historia de Usuario

### Como:
Analista de Comercio Exterior (Rol: **Analista Commex CD**)

### Quiero:
Que el sistema consolide y muestre en una sola pantalla la relación entre las facturas **Z95 de Caterpillar** y las facturas **INV de Panamerican (PSC)**, obtenidas automáticamente desde Dynamics 365, soportando una cardinalidad 1:N (múltiples INVs por Z95) y sin restricción de caracteres en las referencias.

### Para:
Validar que la facturación esté completa y consistente antes de habilitar el proceso de generación de guía y asegurar que no se va a nacionalizar mercancía de más ni de menos.

---

## Contexto del Proceso (As-Is)

1. **Dynamics 365 genera 3 interfaces** hacia el SII para el proceso CrossDocking:
   - **Interfaz 1:** Archivo plano con datos de la **Z95** (Caterpillar → Panamerican).
   - **Interfaz 2:** Archivo plano con datos de las **INV** (Panamerican → Gecolsa / Relianz), ya con la Z95 asociada.
   - **Interfaz 3:** Archivo plano con las **Órdenes de Compra**.
2. Si solo están presentes los datos de la Interfaz 1 (Z95) pero no los de la Interfaz 2 (INV), significa que **Panamerican aún no ha facturado** al destinatario.
3. El término estandarizado aplicable en esta etapa es **INV** (Invoice), no INB.

---

## Descripción Funcional

El sistema debe:

1. **Recibir automáticamente desde Dynamics 365**:
   - Facturas **Z95** (Interfaz 1)
   - Facturas **INV PSC** (Interfaz 2)
   - Órdenes de Compra (Interfaz 3)

2. **Mostrar en pantalla una vista consolidada (JOIN dinámico)** que relacione:
   - Z95 ↔ INV ↔ Órden de Compra

3. **Indicar visualmente el estado de relación**:
   - ✅ **Relacionada**: Z95 e INV correctamente asociadas.
   - ⚠️ **Pendiente INV**: Z95 presente pero sin INV asociada (Panamerican aún no ha facturado).
   - ❌ **Inconsistente**: INV sin Z95 (inconsistencia que requiere gestión).

4. **Soportar referencias sin restricción**:
   - Sin límite de longitud, soportando caracteres especiales, mayúsculas y minúsculas.

5. **Consolidación correcta de productos remanufacturados**:
   - Identifica el remanufacturado EXCLUSIVAMENTE por el campo **Reman Indicator = 1** de Dynamics.
   - Suma en SII de manera totalizada: `Premium + Cargo Core`.
   - La **cantidad** permanece consolidada como **una sola unidad**.

6. **Permitir consulta auditable por Múltiples Filtros**: Z95, INV, Compañía, Dealer, Fecha y Estado de relación.

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

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Visualización correcta de la relación Z95 ↔ INV
```gherkin
Dado que Dynamics ha procesado las 3 interfaces hacia el SII
Cuando el analista consulta la pantalla de facturación
Entonces el sistema debe mostrar la Z95, la INV asociada y la OC en una sola vista
  Y debe indicar que la relación está correctamente establecida (Relacionada).
```

### Escenario 2 – Z95 sin INV asociada
```gherkin
Dado que la Interfaz 1 (Z95) llegó pero la Interfaz 2 (INV) aún no
Cuando el analista consulta la pantalla
Entonces el sistema debe mostrar la Z95 con el estado "Pendiente INV"
  Y debe indicar visualmente que Panamerican aún no ha facturado al destinatario final.
```

### Escenario 3 – Producto remanufacturado con Premium + Core
```gherkin
Dado que Dynamics envía un producto con Reman Indicator = 1 y dos líneas (Premium y Core)
Cuando el sistema procesa la interfaz
Entonces debe sumar ambos valores y almacenar un único valor total de INV en SII
  Y la cantidad de unidades en SII debe reflejarse estrictamente como "1".
```

### Escenario 4 – Integridad de referencias largas o con caracteres especiales
```gherkin
Dado que una referencia excede los caracteres tradicionales o contiene caracteres especiales
Cuando el sistema almacena los datos de la interfaz
Entonces la referencia no debe truncarse ni causar bloqueo de carga.
```

---

## Reglas de Negocio

| ID | Regla |
|-----|-------|
| RN-01 | La vista de relación Z95 ↔ INV debe ser un JOIN dinámico que se actualice con cada ejecución de interface. |
| RN-02 | El campo **Reman Indicator** de Dynamics es la única fuente oficial para identificar remanufacturado. No se permite lógica basada en sufijos de referencia ("R", "CC", etc.). |
| RN-03 | El valor reportado en las vistas de SII para artículos remanufacturados debe ser obligatoriamente la suma totalizada (`Premium + Cargo Core`), manteniendo `Cantidad = 1`. |
| RN-04 | Si una Z95 carece de INV, se muestra alerta visual pero no prohíbe el uso de la pantalla de consulta. |
| RN-05 | La relación Z95 ↔ INV debe estar "Relacionada" y validada antes de habilitar los embarques hacia el proceso de discrepancias de la HU-CD-28. |
| RN-06 | Multi-INV: El sistema debe soportar cardinalidad 1:N y agrupar todas las INV asociadas a la misma Z95 visualmente. |
| RN-07 | El sistema debe soportar simultaneidad concurrente de usuarios por compañía sin causar bloqueos de tabla. |

---

## Formato de Exportación Excel

**Filtros de Exportación:** Número Z95, Número INV, Año (Año Proceso / Año Factura).

**Columnas del reporte:**
| Columna | Origen | Descripción |
|-------|--------|-------------|
| Compañía | INV PSC | Gecolsa / Relianz |
| Factura Z95 | Z95 | Factura Caterpillar |
| Año Z95 | Z95 | Año de emisión |
| Factura INV | INV PSC | Factura Panamerican |
| Orden de Compra | OC | OC asociada a la línea |
| Referencia | Z95 / INV | Código de parte (exacto a la OC, sin recortes) |
| Cantidad Facturada | Z95 / INV | Unidades |
| Valor FOB USD | Z95 | Valor CAT |
| Valor SII (Total) | INV PSC | Valor PSC (Suma para REMAN) |
| Estado Relación | Sistema | Relacionada / Pendiente INV / Inconsistente |

---

## Pendientes / Compromisos de Sesión

* **Pendiente** -> Documentar los campos exactos y especificaciones técnicas que llegarán en las 3 interfaces (planos txt/csv) desde Dynamics. Responsable: Equipo de Transformación.
* **Pendiente** -> Confirmar en UI si la Interfaz 3 (OC) se utilizará en esta misma pantalla como criterio de consolidación visual principal. Responsable: Analistas Commex CD.
* **Pendiente** -> Definir oficialmente el evento o "trigger" que confirma la "completitud" para soltar las facturas al módulo de discrepancias. Responsable: Líder Funcional.
* **Pendiente** -> Validar con Caterpillar qué datos específicos son estrictamente necesarios en pantalla según el SLA de negocio. Responsable: Equipo TI.
