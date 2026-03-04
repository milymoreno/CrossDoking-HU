# HU-CD-28: Gestión de Discrepancias entre Facturas Z95 (CAT) vs INV PSC

**Proceso:** CROSSDOCKING  
**Subproceso:** DISCREPANCIAS  
**Requerimiento Original:** 28 - El sistema debe permitir hacer un paralelo entre las facturas CAT vs las facturas PSC para asegurar que no se va a nacionalizar de más o de menos (ej. cantidades facturadas).

---

### Como:
Analista de Comercio Exterior / Operaciones

### Quiero:
Ejecutar un proceso de validación que compare las facturas Z95 vs las facturas INV PSC

### Para:
Detectar diferencias en cantidades o valores antes de generar la guía y evitar nacionalizar mercancía incorrecta.

---

## Descripción Funcional

El sistema debe permitir ejecutar un proceso de “Discrepancias” que:

1. Tome las facturas Z95 previamente integradas.
2. Cruce la información contra las facturas INV PSC.
3. Compare como mínimo:
   - Cantidades
   - Referencias
   - Asociación Z95 ↔ INV
   - Compañía
   - Orden de compra (cuando aplique)

---

## Flujo Operativo (Definido en Sesión)

### 1️⃣ Validación previa
Antes de ejecutar discrepancias:
- El usuario valida que las Z95 reportadas por el freight forwarder:
  - Ya estén cargadas en el sistema.
  - Estén disponibles para procesamiento.

---

### 2️⃣ Ejecución de Discrepancias

El usuario selecciona facturas Z95 y ejecuta la opción “Discrepancias”.

El sistema debe:

- Generar un cruce automático Z95 vs INV.
- Identificar:
  - Diferencias de cantidades.
  - Facturas Z95 sin INV asociada.
  - INV con diferencias frente a Z95.
  - Referencias no coincidentes.

---

### 3️⃣ Generación de Reporte

Si existen discrepancias:

- El sistema debe generar automáticamente un archivo tipo Excel.
- El archivo debe contener:
  - Datos Z95
  - Datos INV
  - Campo de diferencia
  - Tipo de inconsistencia
  - Cantidades comparadas

El archivo debe enviarse al correo definido para Operaciones.

---

### 4️⃣ Gestión en Operaciones

Dependiendo del tipo de discrepancia:

- Puede requerir:
  - Corrección en Dynamics.
  - Reproceso de facturación.
  - Ajuste manual autorizado.
- Una vez corregido en origen (D365), la interfaz vuelve a ejecutarse.

---

### 5️⃣ Revalidación

El usuario vuelve a ejecutar discrepancias.

Si ya no existen diferencias:
- La factura queda lista para generación de guía.

---

### 6️⃣ Cambio de Estado

Cuando una Z95 se utiliza para generar guía:

- Debe eliminarse automáticamente de la tabla de pendientes.
- No debe volver a aparecer en discrepancias.
- Debe cambiar su estado a “Procesada”.

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Sin discrepancias
Dado que la Z95 y la INV coinciden en cantidades  
Cuando el usuario ejecute discrepancias  
Entonces el sistema no debe generar reporte de error.

---

### Escenario 2 – Cantidades diferentes
Dado que la cantidad en Z95 es distinta a la INV  
Cuando se ejecute discrepancias  
Entonces el sistema debe generar reporte detallado.

---

### Escenario 3 – Z95 sin INV
Dado que existe una Z95 sin INV asociada  
Cuando se ejecute discrepancias  
Entonces el sistema debe reportarla como inconsistente.

---

### Escenario 4 – Corrección posterior
Dado que la discrepancia fue corregida en Dynamics  
Cuando la interfaz se reprocesa  
Entonces el sistema debe permitir que la factura quede sin inconsistencias.

---

### Escenario 5 – Eliminación tras generación de guía
Dado que la Z95 ya fue utilizada en una guía  
Cuando se consulte la tabla de discrepancias  
Entonces no debe aparecer nuevamente.

---

## Reglas de Negocio

- RN-01: No se podrá generar guía si existen discrepancias activas.
- RN-02: La validación debe realizarse por compañía.
- RN-03: El sistema debe permitir ejecución parcial (por selección).
- RN-04: El reporte debe quedar auditado.
- RN-05: Las discrepancias deben resolverse en el sistema origen cuando aplique.
- RN-06: El proceso no debe alterar datos originales.