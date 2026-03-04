# HU-CD-34: Validación de Referencias Remanufacturadas sin Licencia o Saldo

**Proceso:** CROSSDOCKING  
**Subproceso:** VALIDACIÓN REMANUFACTURADOS  
**Requerimiento Original:** 34 - El sistema tendrá la capacidad de identificar las referencias remanufacturadas parametrizadas que no cuentan con licencias y saldos en las licencias de importación y de esta manera arroje una alerta que permita visualizar: la OC, la factura PSC, la referencia, la caja, BTN, la cantidad y el comentario del error.

---

### Como:
Analista de Comercio Exterior

### Quiero:
Que el sistema identifique referencias remanufacturadas que no tengan licencia válida o saldo disponible

### Para:
Evitar generación de guías o DIM sin respaldo normativo y prevenir sanciones.

---

## Identificación de Remanufacturados

El sistema debe identificar referencias remanufacturadas utilizando:

- Campo **Reman Indicator** proveniente de Dynamics (1 = Reman, 0 = No Reman). :contentReference[oaicite:1]{index=1}  

No debe basarse únicamente en:

- Sufijo “R”
- Sufijo “CC”
- Convenciones manuales

---

## Lógica de Validación

Cuando una referencia tenga:

- Reman Indicator = 1

El sistema debe validar:

1️⃣ Si la referencia requiere licencia según parametrización normativa.  
2️⃣ Si existe licencia asignada (HU-CD-32).  
3️⃣ Si la licencia tiene saldo suficiente.  
4️⃣ Si la licencia está vigente.  

---

## Generación de Alerta

Si no cumple alguna validación:

El sistema debe generar alerta visible en pantalla que muestre:

- Número de Orden de Compra (OC)
- Número de Factura PSC
- Número Z95
- Referencia
- Número de Caja
- BTN (subpartida)
- Cantidad
- Tipo de inconsistencia:
  - Sin licencia
  - Saldo insuficiente
  - Licencia vencida
  - No parametrizada

---

## Comportamiento del Sistema

- Debe impedir generación de guía si:
  - La referencia reman requiere licencia y no la tiene.
- Debe permitir continuar si:
  - La referencia reman está parametrizada como no requiere licencia.
- Debe registrar evento en auditoría.

---

## Integración con Otros Procesos

Esta validación debe ejecutarse:

- Antes de generar guía (HU-CD-33).
- Antes de generar DIM (HU-CD-39).
- Durante control de saldos (HU-CD-32).

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Reman sin licencia
Dado que una referencia tiene Reman Indicator = 1  
Y requiere licencia  
Cuando el usuario intente generar guía  
Entonces el sistema debe bloquear y mostrar alerta detallada.

---

### Escenario 2 – Reman con saldo insuficiente
Dado que la licencia existe  
Pero el saldo es menor a la cantidad requerida  
Cuando se intente generar guía  
Entonces el sistema debe bloquear la operación.

---

### Escenario 3 – Reman parametrizado como no requiere
Dado que la referencia reman no requiere licencia según parametrización  
Cuando se genere guía  
Entonces el sistema debe permitir continuar.

---

### Escenario 4 – Licencia vencida
Dado que la licencia asociada está vencida  
Cuando se intente usar  
Entonces el sistema debe impedir la operación.

---

## Reglas de Negocio

- RN-01: Reman Indicator es la única fuente oficial de identificación.
- RN-02: No se permite continuar sin licencia válida cuando aplique.
- RN-03: Las alertas deben ser descriptivas.
- RN-04: La validación debe ejecutarse en tiempo real.
- RN-05: La alerta no debe alterar datos originales.
- RN-06: El sistema debe registrar el intento fallido.
- RN-07: Esta validación aplica antes de guía y antes de DIM.