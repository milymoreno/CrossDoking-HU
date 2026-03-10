# HU-CD-30: Control de Consecutivos Repetidos Z95 por Año

**Proceso:** CROSSDOCKING
**Subproceso:** ACTUALIZACIÓN DE DATOS – CONTROL DE CONSECUTIVOS
**Requerimiento Original:** REQ-30 — El sistema debe traer la información de las facturas para procesos basados en el año en curso, dado que Caterpillar (origen) reutiliza números de facturas Z95 periódicamente (ej. Z95123456 en 2023 y luego Z95123456 en 2025).
**Versión Consolidada:** 3.0
**Estado:** Integrada (Aportes Negocio + Funcional)

---

## Historia de Usuario

### Como:
Sistema SII 2.0 (Motores de Proceso) y Analista de Comercio Exterior (**Usuario Final**)

### Quiero:
Que el sistema diferencie de manera unívoca las facturas Z95 a través del tiempo, reconociendo como "Clave Única Compuesta" el Número de Factura, su Compañía dueña, y el Año de Emisión.

### Para:
Evitar colisiones lógicas en base de datos, prevenir cruces incorrectos (Ej: asociar una INV de 2025 con una Z95 idéntica pero del 2023) y asegurar que las discrepancias y guías de carga se formen exclusivamente con inventario del ciclo actual.

---

## El Problema de Negocio (As-Is)

Por reglas normativas internas, Caterpillar repite secuencias numéricas de sus facturas internacionales aproximadamente cada dos o tres años.

Esto genera que en el Sistema de Información Integrado (SII) empiecen a coexistir facturas con el mismo Número, pero separadas en el tiempo.
Si las lógicas de cruce, visualización o embarque usaran *Únicamente* el `Número de Z95` como llave maestra, el sistema podría:
- Intentar cruzar facturas actuales con baches históricos.
- Generar Declaraciones de Importación erradas.
- Fracasar la carga masiva y sincronización automática (Falso Duplicado).

---

## Solución Funcional: La Clave de Identidad

El SII adoptará un patrón de identificación **Tridimensional** para cualquier operación automatizada o manual que involucre facturación Z95.

`Identificador Lógico = Número de Factura Z95 + Año de Emisión de la Factura + Compañía Adquirente`

Ejemplo Práctico en BD y en UI:
- `Z95-TEST-999 | Año 2023 | Relianz`  **(Registro Histórico)**
- `Z95-TEST-999 | Año 2025 | Relianz`  **(Registro Activo, Carga Permitida)**
Ambos registros coexistirán de manera autónoma pero sin interactuar entre sí.

---

## Reglas de Comportamiento del Sistema

| ID | Regla Funcional |
|----|-------|
| RN-01 | **Filtro Automático de Vigencia:** Todo módulo operativo que demande agrupaciones masivas o acciones directas (*Monitor de Facturación, Discrepancias, Generación de Guías - D.O, Declaraciones*), debe mostrar en pantalla y usar por defecto **Únicamente Las Facturas Del Año En Curso**. |
| RN-02 | **Cruce Interanual Prohibido:** El sistema jamás asociará una Factura INV emitida por Panamerican en el año *Y*, con una Factura Z95 emitida por Caterpillar en el año *X*, sin importar que sus números de orden base coincidan. Deben cruzarse facturas del mismo año contable de emisión. |
| RN-03 | **Visibilidad Universal:** El campo *"Año de Factura"* o *"Fecha de Factura"* pasa a ser un elemento de nivel 1 de jerarquía; siempre será columna visible en las grillas frontales, siempre será exportable, y siempre servirá de agrupador principal. |
| RN-04 | **Protección de Datos Antiguos:** Las facturas históricas (años pasados) **NO se eliminan ni se ocultan**. El Analista conservará la capacidad de aplicar un filtro manual en el modulo de *Consulta Histórica* seleccionando explícitamente "Año 2023", "Año 2022", etc, para fines de auditoría cruzada o inspecciones aduaneras. |
| RN-05 | **Independencia Multicompañía:** Dado que Gecolsa y Relianz operan el mismo código transaccional de Dynamics, la "Compañía" también forma parte de este muro de contención. La Z95-99 (2025) de Gecolsa es indiferente y ajena a la Z95-99 (2025) de Relianz. |

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Inserción de un falso duplicado (Repetición CAT)
```gherkin
Dado que el sistema tiene en bodega virtual la factura Z95-123 | Año 2023 | Gecolsa
Cuando la sincronización programada (HU-CD-29) traiga un registro indicando Z95-123 | Año 2025 | Gecolsa
Entonces el sistema debe aceptarlo como un registro válido 100% original
  Y asignarle de inmediato un nuevo ID interno
  Y no emitir errores de colisión.
```

### Escenario 2 – Aislamiento proactivo en procesos activos
```gherkin
Dado que un Analista de Transporte inicia el módulo de "Discrepancias" en el año 2025
Cuando seleccione la factura Z95-123 para procesar
Entonces el sistema arrastrará solo el detalle y los valores unitarios del Registro del Año 2025
  Y el Registro del Año 2023 se mantendrá opaco y sin ser llamado durante el cruce contable.
```

### Escenario 3 – Consulta forzada de histórico
```gherkin
Dado que hay una inspección y se necesita verificar cruces del año viejo
Cuando el usuario abra la "Consulta de Facturación", quite el parámetro por defecto (Año en Curso) y seleccione manualmente "2023"
Entonces el sistema liberará a pantalla la vista de la factura Z95-123 | Año 2023 | Gecolsa, pero en un entorno de solo lectura (Histórico).
```

### Escenario 4 – Cruce interanual denegado
```gherkin
Dado que entra una factura INV-PSC en 2025
 Cuando el motor de enrutamiento intente casarla con la matriz madre
Entonces validará la fecha y si la Z95 coincidente en código es del año 2024, arrojará error de "Cruce Interanual Delineado" y no cerrará la agrupación (La deja en "Pendiente INV" - Ver HU-CD-27).
```

---

## Pendientes / Compromisos
Ninguno derivado estrictamente para esta funcionalidad, ya que la dependencia recae enteramente en cómo `HU-CD-29` define la puerta de entrada de nuevos datos (donde el año validado aquí se convierte en el candado principal). Se retiran todos los mandatos técnicos de indexación SQL dejándolos a discreción del DBA en su proceso de Migración.
