# HU-CD-26: Recepción Información Consolidada del Agente de Carga

**Proceso:** CROSSDOCKING  
**Subproceso:** RECEPCIÓN INFORMACIÓN AGENTE DE CARGA  
**Requerimiento Original:** 26 - El sistema deberá tener la capacidad de interactuar con el agente de carga en cuanto a la recepción de la información consolidada (Guías, facturas Z95, pesos y dealers).

---

### Como:
Analista de Comercio Exterior

### Quiero:
Que el sistema reciba la información consolidada enviada por el agente de carga

### Para:
Validar que las facturas Z95 reportadas estén disponibles en el sistema y puedan ser procesadas en CrossDocking.

---

## Descripción Funcional

El sistema debe permitir:

- Recibir información consolidada proveniente del agente de carga (ej. DHL).
- Validar que las facturas Z95 reportadas:
  - Existan en el sistema.
  - Hayan sido correctamente integradas desde D365.
- Permitir filtro por:
  - Número de factura Z95
  - Compañía
  - Dealer
  - Fecha

El sistema debe permitir realizar una validación previa antes de ejecutar el proceso de discrepancias.

---

## Criterios de Aceptación

### Escenario 1 – Validación exitosa
Dado que el agente de carga reporta facturas Z95  
Cuando el usuario las consulte en el sistema  
Entonces el sistema debe confirmar que ya fueron integradas.

### Escenario 2 – Factura no encontrada
Dado que una factura Z95 no esté cargada  
Cuando el usuario la consulte  
Entonces el sistema debe informar que no ha sido recibida desde D365.

---

## Reglas de Negocio

- RN-01: Solo se procesarán facturas reportadas por el agente de carga.
- RN-02: No se podrán ejecutar discrepancias si la factura no existe en el sistema.
- RN-03: El sistema debe permitir validación por compañía.