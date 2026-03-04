# HU-CD-36: Validación Visual de Guía – Cantidades y Valor FOB

**Proceso:** CROSSDOCKING  
**Subproceso:** VALIDACIÓN GUÍA  
**Requerimiento Original:** 36 - El programa debe permitir generar una visual que confirme las cantidades generadas entre las intercompañías y las facturas emitidas por CAT, y a su vez el valor FOB generado.

---

### Como:
Analista de Comercio Exterior

### Quiero:
Visualizar un resumen comparativo de la guía generada frente a las facturas Z95 e INV PSC

### Para:
Confirmar que las cantidades y valores FOB sean correctos antes de continuar con el proceso aduanero.

---

## Descripción Funcional

El sistema debe generar una pantalla de validación que muestre:

1️⃣ Información consolidada de la guía:
- Número de guía
- Compañía
- Dealer
- Total referencias
- Total cantidades
- Total valor FOB

2️⃣ Comparativo contra:
- Facturas Z95 (CAT)
- Facturas INV PSC

---

## Validaciones Obligatorias

El sistema debe confirmar:

- Que las cantidades en guía coincidan con:
  - Cantidades seleccionadas de Z95
  - Cantidades reflejadas en INV PSC

- Que el valor FOB total:
  - Sea igual a la suma consolidada de las facturas.
  - No esté duplicado.
  - No tenga omisiones.

---

## Visualización Requerida

La pantalla debe mostrar:

| Concepto | Z95 | INV PSC | Guía |
|----------|-----|----------|------|
| Total referencias | X | X | X |
| Total unidades | X | X | X |
| Valor FOB total | X | X | X |
| Diferencia | 0 / Diferencia detectada |

Si existe diferencia:

- Mostrar alerta visual.
- Bloquear avance a DIM.

---

## Integración con Proceso

Esta validación debe ejecutarse:

- Después de generar guía.
- Antes de permitir creación de DO (HU-CD-37).
- Antes de permitir generación de DIM (HU-CD-39).

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Coincidencia total
Dado que las cantidades y valores coinciden  
Cuando el usuario consulte validación  
Entonces el sistema debe mostrar confirmación sin alertas.

---

### Escenario 2 – Diferencia en cantidades
Dado que la guía contiene cantidades distintas a las facturas  
Cuando se ejecute validación  
Entonces el sistema debe mostrar diferencia y bloquear proceso siguiente.

---

### Escenario 3 – Diferencia en valor FOB
Dado que el valor FOB consolidado no coincide  
Cuando se consulte validación  
Entonces el sistema debe generar alerta y no permitir continuar.

---

### Escenario 4 – Control previo a DIM
Dado que existe diferencia detectada  
Cuando el usuario intente generar DIM  
Entonces el sistema debe impedir la operación.

---

## Reglas de Negocio

- RN-01: No se puede avanzar a proceso aduanero con diferencias activas.
- RN-02: El valor FOB debe ser consistente entre intercompañías.
- RN-03: La validación es obligatoria.
- RN-04: No se permite omitir la pantalla de validación.
- RN-05: La validación no modifica datos.
- RN-06: Toda validación debe quedar registrada en auditoría.