# HU-CD-30: Control de Consecutivos Repetidos Z95 por Año

**Proceso:** CROSSDOCKING  
**Subproceso:** ACTUALIZACIÓN DE DATOS – CONTROL DE CONSECUTIVOS  
**Requerimiento Original:** 30 - El sistema debe traer la información de las facturas para generación del año en curso, dado que CAT cada dos años repite consecutivos.  
Ejemplo:  
Z95123456 – 2023-01-06  
Z95123456 – 2025-01-06  

---

### Como:
Sistema SII 2.0

### Quiero:
Identificar correctamente facturas Z95 cuando existan consecutivos repetidos en diferentes años

### Para:
Evitar colisiones de registros, cruces incorrectos y generación errónea de guías o discrepancias.

---

## Problema Identificado

Caterpillar reutiliza el mismo número de factura Z95 cada dos años.

Esto implica que:

- No se puede utilizar únicamente el número Z95 como identificador único.
- Puede existir el mismo consecutivo en diferentes años.
- Puede existir información histórica aún visible en el sistema.

---

## Solución Funcional

El sistema debe:

1. Construir la clave única utilizando como mínimo:
   - Número Z95
   - Fecha factura (año)
   - Compañía

2. Al momento de:
   - Ejecutar discrepancias
   - Generar guía
   - Asociar INV
   - Consultar facturas

   Debe considerar el año correspondiente.

---

## Visualización en Sistema

En la tabla de facturas Z95:

- Debe mostrarse:
  - Número Z95
  - Fecha completa
  - Año
  - Estado
  - Compañía

- No deben mezclarse facturas del mismo consecutivo de diferentes años.

---

## Filtro Año en Curso

Para generación de guía y procesos activos:

- El sistema debe traer por defecto:
  - Solo facturas del año en curso.
- Debe permitir consulta histórica bajo filtro manual.

---

## Validaciones Técnicas

- No permitir que una INV del 2025 se asocie a una Z95 del 2023.
- No permitir generar guía con año incorrecto.
- No permitir cruce de discrepancias entre años distintos.

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Consecutivo repetido en diferente año
Dado que existe una Z95 con el mismo número en 2023 y 2025  
Cuando el sistema procese facturas  
Entonces debe diferenciarlas correctamente por año.

---

### Escenario 2 – Filtro por año en curso
Dado que el usuario ingrese al módulo CrossDocking  
Cuando consulte facturas disponibles  
Entonces el sistema debe mostrar únicamente las del año en curso por defecto.

---

### Escenario 3 – Asociación incorrecta bloqueada
Dado que una INV pertenece al 2025  
Cuando el usuario intente asociarla a una Z95 del 2023  
Entonces el sistema debe bloquear la operación.

---

### Escenario 4 – Consulta histórica
Dado que el usuario requiera revisar facturas de años anteriores  
Cuando aplique filtro manual  
Entonces el sistema debe permitir visualizar registros históricos.

---

## Reglas de Negocio

- RN-01: La clave única de Z95 no puede ser solo el consecutivo.
- RN-02: El año forma parte obligatoria del identificador lógico.
- RN-03: Los procesos activos deben trabajar por defecto con el año vigente.
- RN-04: No se permiten cruces interanuales.
- RN-05: La información histórica no debe eliminarse.
- RN-06: La lógica debe aplicarse a discrepancias, guías y DIM.