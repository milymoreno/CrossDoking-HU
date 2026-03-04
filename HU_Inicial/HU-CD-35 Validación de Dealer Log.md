# HU-CD-35: Validación de Dealer Logístico en Generación de Guía

**Proceso:** CROSSDOCKING  
**Subproceso:** VALIDACIÓN DEALER  
**Requerimiento Original:** 35 - El sistema deberá generar las guías conforme a los DEALERS logísticos (ver tabla 1 de reportes y casos especiales). No deberá permitir generar guías con DEALERS que contengan mercancía nueva y mercancía remanufacturada. Adicional deberá realizar distinción de los DEALERS asignados para Gecolsa y para Relianz.

---

### Como:
Analista de Comercio Exterior

### Quiero:
Que el sistema valide correctamente el dealer logístico al momento de generar la guía

### Para:
Evitar mezclas indebidas de mercancía y errores operativos o contractuales.

---

## Descripción Funcional

El sistema debe validar que:

1️⃣ El dealer seleccionado:
   - Exista en la parametrización oficial (Tabla 1).
   - Esté asociado correctamente a la compañía correspondiente.

2️⃣ No se permita generar una guía que mezcle:
   - Mercancía nueva
   - Mercancía remanufacturada

3️⃣ No se permita usar:
   - Dealer asignado a Gecolsa en guía de Relianz.
   - Dealer asignado a Relianz en guía de Gecolsa.

---

## Identificación Tipo de Mercancía

El sistema debe identificar:

- Mercancía Nueva → Reman Indicator = 0  
- Mercancía Reman → Reman Indicator = 1 :contentReference[oaicite:1]{index=1}  

Esta validación debe realizarse antes de consolidar facturas en la guía.

---

## Lógica de Validación

Al momento de generar guía:

- El sistema debe analizar todas las facturas seleccionadas.
- Verificar si hay mezcla de:
  - Referencias nuevas
  - Referencias remanufacturadas

Si existe mezcla:
- Bloquear la generación.
- Mostrar mensaje explicativo.

---

## Distinción por Compañía

Cada guía debe:

- Validar la compañía de las facturas.
- Permitir solo dealers configurados para:
  - Gecolsa
  - Relianz

No debe permitir mezclar facturas de compañías distintas en una misma guía.

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Dealer correcto
Dado que las facturas pertenecen a Gecolsa  
Cuando el usuario seleccione un dealer válido para Gecolsa  
Entonces el sistema debe permitir generar guía.

---

### Escenario 2 – Dealer incorrecto
Dado que las facturas pertenecen a Relianz  
Cuando el usuario seleccione dealer de Gecolsa  
Entonces el sistema debe bloquear la operación.

---

### Escenario 3 – Mezcla Nueva + Reman
Dado que existen referencias nuevas y reman seleccionadas  
Cuando se intente generar una sola guía  
Entonces el sistema debe impedir la operación.

---

### Escenario 4 – Solo Reman
Dado que todas las referencias son reman  
Cuando el dealer sea válido  
Entonces el sistema debe permitir la generación.

---

## Reglas de Negocio

- RN-01: El dealer debe estar parametrizado en Tabla 1.
- RN-02: No se permite mezcla Nueva + Reman en una misma guía.
- RN-03: No se permite mezclar compañías en una misma guía.
- RN-04: La validación debe ejecutarse antes de asignar número de guía.
- RN-05: Toda validación fallida debe quedar auditada.
- RN-06: La compañía de la factura determina el dealer permitido.