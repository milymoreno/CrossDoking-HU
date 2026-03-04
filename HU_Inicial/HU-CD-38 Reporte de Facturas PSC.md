# HU-CD-38: Reporte de Facturas PSC Asociadas a Documento de Transporte

**Proceso:** CROSSDOCKING  
**Subproceso:** REPORTE FACTURAS GENERADAS  
**Requerimiento Original:** 38 - El programa debe contar con una opción que permita generar un reporte de facturas PSC creadas por documento de transporte.

---

### Como:
Analista de Comercio Exterior / Área Financiera

### Quiero:
Generar un reporte consolidado de las facturas PSC asociadas a un documento de transporte (Guía)

### Para:
Tener trazabilidad clara del embarque, facilitar conciliaciones y soportar procesos contables y aduaneros.

---

## Descripción Funcional

El sistema debe permitir:

1️⃣ Seleccionar un número de guía.  
2️⃣ Generar un reporte que incluya todas las facturas PSC asociadas.  
3️⃣ Visualizar o exportar el reporte en formato:
   - Excel
   - PDF
   - Vista en pantalla

---

## Información Mínima del Reporte

El reporte debe incluir:

- Número de Guía
- Compañía (Gecolsa / Relianz)
- Dealer
- Fecha Guía
- Número Factura Z95
- Número Factura INV PSC
- Referencia
- Cantidad
- Valor Unitario
- Valor FOB
- Tipo de mercancía (Nueva / Reman)
- Estado del DO
- Estado del DIM (si aplica)

---

## Filtros Disponibles

El sistema debe permitir filtrar por:

- Número de Guía
- Compañía
- Dealer
- Fecha
- Estado (Generada / En DO / Con DIM)

---

## Comportamiento del Sistema

- El reporte debe reflejar:
  - Solo las facturas realmente asociadas a la guía.
  - Cantidades ya parcializadas correctamente.
  - Valores consolidados (incluyendo reman totalizado).

- No debe incluir facturas pendientes o no asociadas.

---

## Integración con Procesos Posteriores

Este reporte debe servir como base para:

- Validación previa a DIM (HU-CD-39).
- Conciliación con información aduanera (HU-CD-40).
- Migración a D365 para costeo (HU-CD-41).

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Generación exitosa
Dado que existe una guía con facturas asociadas  
Cuando el usuario genere el reporte  
Entonces el sistema debe listar todas las facturas correctamente.

---

### Escenario 2 – Guía parcial
Dado que la guía fue generada parcialmente  
Cuando se genere el reporte  
Entonces debe mostrar únicamente las cantidades efectivamente incluidas.

---

### Escenario 3 – Exportación
Dado que el usuario solicita exportación  
Cuando genere el reporte  
Entonces el sistema debe permitir descargarlo en Excel o PDF.

---

### Escenario 4 – Guía inexistente
Dado que el número de guía no existe  
Cuando se consulte  
Entonces el sistema debe mostrar mensaje informativo.

---

## Reglas de Negocio

- RN-01: El reporte debe reflejar datos consolidados reales.
- RN-02: No se permiten datos fuera del alcance de la guía seleccionada.
- RN-03: El reporte no modifica información.
- RN-04: Debe quedar registro de generación del reporte (auditoría).
- RN-05: Debe soportar grandes volúmenes sin afectar rendimiento.