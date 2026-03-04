# HU-CD-31: Parcialización de Documento de Transporte (Guía)

**Proceso:** CROSSDOCKING  
**Subproceso:** PARCIALIZAR GUÍA  
**Requerimiento Original:** 31 - El sistema tiene la capacidad de generar documentos de transporte parciales de acuerdo a los siguientes criterios:
- Parcializar guía por factura
- Parcializar guía por referencia
- Parcializar guía por unidades de referencia

---

### Como:
Analista de Comercio Exterior

### Quiero:
Generar documentos de transporte (guías) de manera parcial

### Para:
Poder embarcar mercancía de forma flexible según necesidad operativa sin afectar el control de cantidades.

---

## Descripción Funcional

El sistema debe permitir generar una guía parcial basada en:

1️⃣ Por Factura (Z95 completa)  
- Seleccionar una factura específica.
- Tomar todas sus referencias.
- Generar guía con esa factura.

2️⃣ Por Referencia  
- Seleccionar solo algunas referencias dentro de una Z95.
- Dejar el resto disponible en tabla pendiente.

3️⃣ Por Unidades de Referencia  
- Seleccionar una referencia.
- Indicar cantidad parcial.
- Dejar saldo disponible para futuras guías.

---

## Lógica de Descuento Automático

Cuando se genere una guía parcial:

- El sistema debe:
  - Descontar automáticamente la cantidad utilizada.
  - Actualizar saldo disponible.
  - Mantener visible el saldo restante.
  - Cambiar estado de la Z95 según corresponda:
    - Parcialmente utilizada
    - Totalmente utilizada

Si el saldo llega a cero:
- Debe eliminarse de la tabla de pendientes.
- Cambiar estado a “Procesada”.

---

## Validaciones Obligatorias

- No permitir tomar más unidades de las disponibles.
- No permitir generar guía si existen discrepancias activas.
- No permitir mezclar años distintos (ver HU-CD-30).
- No permitir tomar facturas ya procesadas.

---

## Estados Requeridos

Cada Z95 debe tener estado:

- Pendiente
- Parcial
- Lista para guía
- Procesada
- Bloqueada por discrepancia

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Parcial por referencia
Dado que una Z95 contiene varias referencias  
Cuando el usuario seleccione solo una  
Entonces el sistema debe generar guía parcial y mantener saldo restante.

---

### Escenario 2 – Parcial por unidades
Dado que una referencia tiene 10 unidades  
Cuando el usuario seleccione 4  
Entonces el sistema debe dejar 6 disponibles.

---

### Escenario 3 – Uso total
Dado que se utilicen todas las unidades  
Cuando se genere la guía  
Entonces la Z95 debe desaparecer de la tabla de pendientes.

---

### Escenario 4 – Bloqueo por discrepancia
Dado que una Z95 tenga discrepancias activas  
Cuando el usuario intente parcializar  
Entonces el sistema debe bloquear la operación.

---

## Reglas de Negocio

- RN-01: La parcialización no debe alterar datos originales.
- RN-02: El saldo debe actualizarse en tiempo real.
- RN-03: No se permite sobreconsumo.
- RN-04: No se permite generar guía con discrepancias activas.
- RN-05: Toda generación parcial debe quedar auditada.
- RN-06: El estado debe reflejar el nivel de consumo.