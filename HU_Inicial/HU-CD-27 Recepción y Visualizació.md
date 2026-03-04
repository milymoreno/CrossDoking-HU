# HU-CD-27: Recepción y Visualización Relación Facturación Z95 vs INV PSC

**Proceso:** CROSSDOCKING  
**Subproceso:** RECEPCIÓN DE FACTURACIÓN  
**Requerimiento Original:** 27 - El sistema debe permitir que la facturación emitida por PSC desde D365 llegue de manera automática a una tabla o archivo que permita visualizar la relación entre las Z95 CAT y las INV PSC. No debe limitar caracteres. Debe contemplar particularidades como remanufacturados (Premium + Core = Valor único total en SII).

---

### Como:
Analista de Comercio Exterior

### Quiero:
Que la facturación emitida por PSC desde Dynamics 365 se reciba automáticamente en el SII y permita visualizar la relación entre las facturas Z95 (CAT) y las INV (PSC)

### Para:
Garantizar que la información esté completa, consistente y lista para validación de discrepancias y posterior generación de guía.

---

## Descripción Funcional

El sistema debe:

1. Recibir automáticamente desde Dynamics 365:
   - Facturas Z95 (CAT → Panamerican)
   - Facturas INV PSC (Panamerican → Gecolsa / Relianz)

2. Almacenar la información en tablas que permitan:
   - Visualizar la relación Z95 vs INV
   - Identificar compañía
   - Identificar dealer
   - Identificar referencia
   - Identificar cantidades
   - Identificar valores

3. Permitir consulta por:
   - Número Z95
   - Número INV
   - Compañía
   - Dealer
   - Fecha
   - Estado

---

## Manejo de Referencias (NO LIMITANTE)

El sistema:

- No debe limitar la cantidad de caracteres de la referencia.
- Debe soportar:
  - Caracteres especiales
  - Mayúsculas
  - Minúsculas
  - Combinaciones alfanuméricas
- No debe bloquear la carga por longitud de referencia.

---

## Manejo de Remanufacturados

Según lo definido en sesión:

1. La identificación de remanufacturado NO se realizará por:
   - Código que termine en “R”
   - Código que termine en “CC”

2. La identificación oficial será mediante el campo:
   - **Reman Indicator (1 = Reman / 0 = No Reman)** proveniente de Dynamics. :contentReference[oaicite:1]{index=1}  

3. En Dynamics:
   - El producto remanufacturado puede venir dividido en:
     - Product Price (Premium)
     - Cargo Core

4. En SII:
   - Debe llegar un único valor total:
     
     ```
     Valor SII = Premium + Cargo Core
     ```

5. La cantidad debe mantenerse como una sola unidad (no duplicarse por el cargo core).

---

## Funcionalidades Clave

- Asociación automática Z95 ↔ INV.
- Visualización consolidada por compañía.
- Consolidación automática del valor remanufacturado.
- Registro de estado:
  - Recibida
  - Pendiente discrepancia
  - Lista para guía
- Auditoría de carga automática.

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Recepción automática exitosa
Dado que Dynamics emite una factura Z95 y una INV  
Cuando la interfaz se ejecute  
Entonces el sistema debe almacenarlas correctamente y relacionarlas.

---

### Escenario 2 – Referencia con caracteres especiales
Dado que una referencia contiene caracteres especiales o longitud extendida  
Cuando el sistema la reciba  
Entonces no debe rechazarla ni truncarla.

---

### Escenario 3 – Remanufacturado con Premium + Core
Dado que Dynamics envía un producto remanufacturado con:
- Product Price
- Cargo Core  
Cuando el sistema procese la información  
Entonces debe sumar ambos valores y almacenar un único valor total en SII.

---

### Escenario 4 – Identificación Reman correcta
Dado que el campo Reman Indicator = 1  
Cuando el sistema procese la factura  
Entonces debe marcar el producto como remanufacturado independientemente del código.

---

### Escenario 5 – Cantidad única en reman
Dado que el producto tenga línea Premium y línea Core  
Cuando se consolide en SII  
Entonces la cantidad debe permanecer como una sola unidad.

---

## Reglas de Negocio

- RN-01: El campo Reman Indicator es la única fuente oficial para determinar remanufacturado.
- RN-02: No se permitirá lógica basada únicamente en sufijos de referencia.
- RN-03: El sistema no debe limitar la longitud de referencias.
- RN-04: El valor almacenado en SII debe ser totalizado para reman.
- RN-05: Las interfaces deben quedar auditadas.
- RN-06: La relación Z95 vs INV debe ser obligatoria para continuar a discrepancias.