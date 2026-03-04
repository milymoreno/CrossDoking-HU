# HU-CD-32: Asignación de Registro y/o Licencia de Importación en CrossDocking

**Proceso:** CROSSDOCKING  
**Subproceso:** ASIGNACIÓN REGISTRO Y/O LICENCIA DE IMPORTACIÓN  
**Requerimiento Original:** 32 - Que el sistema permita asignar al modelo y tipo del equipo el registro y/o licencia de importación según se requiera, que controle saldos y vencimientos.

---

### Como:
Analista de Comercio Exterior

### Quiero:
Asignar un registro o licencia de importación a las referencias o equipos que lo requieran antes de generar la guía o la DIM

### Para:
Garantizar cumplimiento normativo y evitar nacionalizaciones sin respaldo de licencia válida.

---

## Descripción Funcional

El sistema debe permitir:

1️⃣ Identificar si la referencia/modelo requiere:
   - Registro de importación (R)
   - Licencia de importación (L)
   - No requiere (según parametrización)

2️⃣ Asociar la licencia correspondiente antes de:
   - Generar guía
   - Generar DIM

3️⃣ Validar automáticamente:

   - Saldo disponible
   - Fecha de vencimiento
   - Estado de la licencia
   - Tipo correcto (R o L)

---

## Lógica de Asignación

Al momento de generar guía o DIM:

- El sistema debe consultar:
  - Tipo de equipo
  - Modelo
  - Subpartida
  - Parametrización normativa

Si requiere licencia:

- Debe obligar a seleccionar una licencia válida.
- Debe validar saldo suficiente.
- Debe validar vigencia.

---

## Control de Saldos

El sistema debe:

- Descontar automáticamente el saldo de la licencia al momento de:
  - Generar DIM.
- No permitir consumo superior al saldo.
- Cambiar estado a:
  - Agotada (cuando saldo llegue a cero)
  - Vencida (cuando fecha supere vigencia)

---

## Validaciones Obligatorias

- No permitir generar guía o DIM si:
  - No existe licencia asignada (cuando aplica).
  - La licencia está vencida.
  - El saldo es insuficiente.
  - El tipo de documento no corresponde.

- No permitir usar licencia de otra compañía.

---

## Consideraciones Especiales (Conexión con Remanufacturados)

- Si la referencia está parametrizada como remanufacturada:
  - El sistema debe validar si requiere o no licencia.
  - No debe bloquear incorrectamente procesos reman sin obligación normativa.
  - (Esto se conecta con Req 34).

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Licencia válida
Dado que una referencia requiere licencia  
Cuando el usuario seleccione una licencia vigente con saldo suficiente  
Entonces el sistema debe permitir continuar.

---

### Escenario 2 – Saldo insuficiente
Dado que el saldo disponible es menor a la cantidad requerida  
Cuando se intente asignar la licencia  
Entonces el sistema debe bloquear la operación.

---

### Escenario 3 – Licencia vencida
Dado que la licencia está vencida  
Cuando se intente utilizar  
Entonces el sistema debe impedir la asignación.

---

### Escenario 4 – No requiere licencia
Dado que la referencia está parametrizada como no requiere licencia  
Cuando se genere la guía  
Entonces el sistema no debe exigir asignación.

---

### Escenario 5 – Descuento automático
Dado que la DIM se genera correctamente  
Cuando se confirme la operación  
Entonces el sistema debe descontar el saldo de la licencia.

---

## Reglas de Negocio

- RN-01: La licencia es obligatoria cuando la parametrización lo indique.
- RN-02: El control de saldo debe ser en tiempo real.
- RN-03: No se permite sobreconsumo.
- RN-04: No se permite usar licencia vencida.
- RN-05: La asignación debe quedar auditada.
- RN-06: El consumo definitivo se realiza al generar la DIM.
- RN-07: La lógica aplica para CrossDocking, Equipos y Marcas Aliadas.