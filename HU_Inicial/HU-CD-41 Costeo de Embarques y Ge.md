# HU-CD-41: Costeo de Embarques y Generación de DRS en D365

**Proceso:** CROSSDOCKING  
**Subproceso:** COSTEO Y DRS  
**Requerimiento Original:** 41 - El sistema permite migrar información aduanera a D365 donde se costearán las líneas y posterior generará DRS. Costeo embarques y mercancía (Validar en conjunto con COSTOS). Costo estimado de mercancía (se carga un 2% por embarque, validar con COSTOS).

---

### Como:
Sistema SII 2.0

### Quiero:
Migrar la información aduanera consolidada a D365 para realizar el costeo de las líneas y generar los DRS correspondientes

### Para:
Cerrar financieramente el proceso de importación y reflejar correctamente los costos en el sistema contable.

---

## Prerrequisitos

Antes de iniciar costeo:

- DIM en estado "Con Levante" (HU-CD-40).
- Valores FOB validados (HU-CD-36).
- Licencias correctamente descontadas (HU-CD-32).
- Sin inconsistencias activas.

---

## Descripción Funcional

El sistema debe:

1️⃣ Consolidar la información del embarque:

- Número de Guía
- DO
- DIM
- Facturas INV
- Cantidades
- Valor FOB
- Fletes
- TRM
- Tributos pagados

2️⃣ Generar estructura de migración hacia D365.

3️⃣ Enviar la información mediante interfaz.

4️⃣ Permitir confirmación de recepción en D365.

---

## Lógica de Costeo

El sistema debe permitir:

### Costeo por Línea

Cada referencia debe recibir:

- Valor FOB proporcional.
- Distribución de fletes.
- Distribución de tributos.
- Otros gastos asociados.

---

### Costo Estimado 2%

El sistema debe:

- Aplicar un costo estimado del 2% por embarque (según parametrización).
- Permitir validación conjunta con equipo de Costos.
- Permitir parametrización futura del porcentaje.

Este 2% debe:

- Distribuirse proporcionalmente entre líneas.
- Quedar registrado como costo estimado.

---

## Generación de DRS

Una vez migrado y costeo aplicado:

- D365 debe generar automáticamente los DRS.
- El sistema SII debe recibir confirmación de:
  - Número DRS
  - Fecha generación
  - Estado

---

## Validaciones Obligatorias

- No permitir costeo si:
  - No hay levante.
  - No hay aceptación/pago.
  - Existen diferencias en valores.
- No permitir migración duplicada.
- No permitir alterar valores aduaneros después de costeo.

---

## Estados del Proceso de Costeo

El embarque debe tener estados:

- Listo para Costeo
- Enviado a D365
- Costeado
- DRS Generado
- Error de Migración

---

## Control de Errores

Si falla migración:

- Registrar error técnico.
- Permitir reproceso controlado.
- No duplicar registros en D365.

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Costeo exitoso
Dado que el embarque tiene levante  
Cuando se migre información a D365  
Entonces el sistema debe recibir confirmación y cambiar estado a Costeado.

---

### Escenario 2 – Aplicación 2%
Dado que el embarque es enviado a costeo  
Cuando se aplique distribución  
Entonces el sistema debe incluir 2% estimado distribuido proporcionalmente.

---

### Escenario 3 – Error en migración
Dado que falle la interfaz  
Cuando el sistema detecte error  
Entonces debe permitir reproceso sin duplicidad.

---

### Escenario 4 – Bloqueo sin levante
Dado que la DIM no tiene levante  
Cuando se intente iniciar costeo  
Entonces el sistema debe bloquear la operación.

---

## Reglas de Negocio

- RN-01: El levante es prerrequisito obligatorio.
- RN-02: No se permite costeo sin aceptación y pago.
- RN-03: El 2% debe ser parametrizable.
- RN-04: La migración debe ser auditable.
- RN-05: No se permite doble costeo.
- RN-06: El DRS es evidencia de cierre financiero.
- RN-07: No se permite modificación posterior al costeo sin control formal.