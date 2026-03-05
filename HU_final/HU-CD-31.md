# HU-CD-31: Parcialización de Documento de Transporte (Guía)

**Proceso:** CROSSDOCKING
**Subproceso:** PARCIALIZAR GUÍA
**Requerimiento Original:** REQ-31 — El sistema tiene la capacidad de generar documentos de transporte parciales de acuerdo a los siguientes criterios: parcializar guía por factura, por referencia, por unidades de referencia.
**Versión:** 2.0
**Fecha de revisión:** 2026-03-04
**Estado:** Revisada con base en sesiones de levantamiento (25-Feb, 27-Feb y 02-Mar-2026)

---

## Historia de Usuario

### Como:
Analista de Comercio Exterior (Commex)

### Quiero:
Generar documentos de transporte (guías de embarque) de manera parcial, pudiendo seleccionar solo una factura, un subconjunto de referencias o una cantidad de unidades menor a la disponible en una Z95.

### Para:
Embarcar mercancía de forma flexible según la necesidad operativa (disponibilidad logística, capacidad de transporte, acuerdos con el dealer) sin perder el control de cantidades pendientes ni afectar registros originales.

---

## Contexto del Proceso (As-Is)

Actualmente, el sistema permite generar guías a nivel de factura completa. La parcialización por referencia y por unidades se gestiona manualmente con control en Excel. El nuevo sistema debe automatizar el control de saldos y reemplazar el Excel.

---

## Descripción Funcional

El sistema debe soportar **3 modalidades de parcialización**:

### Modalidad 1 — Por Factura (Z95 completa)
- El usuario selecciona una factura Z95 específica.
- El sistema toma **todas sus referencias y cantidades**.
- Se genera la guía con la totalidad de esa Z95.

### Modalidad 2 — Por Referencia
- El usuario selecciona una o varias **referencias específicas** dentro de una Z95.
- El sistema genera la guía solo con esas referencias.
- Las referencias **no seleccionadas son movidas por el sistema a una nueva "Guía Hija"** (con sufijo `-001`, `-002`, etc.) que queda disponible en la tabla de pendientes.

### Modalidad 3 — Por Unidades de Referencia
- El usuario selecciona una referencia y especifica una **cantidad parcial** (menor a la disponible).
- El sistema genera la guía con esa cantidad.
- El **saldo restante es movido a una nueva "Guía Hija"** (con sufijo `-001`, `-002`, etc.) para su posterior procesamiento.

---

## Lógica de Saldos y Estados

Al generar cualquier guía parcial, el sistema debe:

| Acción | Efecto en el sistema |
|--------|---------------------|
| Generar guía parcial | Descontar cantidad usada del saldo disponible |
| Actualizar saldo | Mostrar saldo restante en tiempo real |
| Saldo llega a 0 | Cambiar estado Z95 a "Procesada" y retirar de tabla de pendientes |
| Saldo > 0 | Cambiar estado Z95 a "Parcialmente utilizada" |

**Estados de la Z95 durante parcialización:**

| Estado | Descripción |
|--------|-------------|
| Pendiente | Sin ninguna guía generada |
| Parcialmente utilizada | Al menos una guía generada; se han creado guías "hijas" para el saldo restante |
| Lista para guía | Sin discrepancias activas, disponible para procesar |
| Bloqueada por discrepancia | No se puede generar guía hasta resolver discrepancias |
| Procesada | Saldo = 0; la "Madre" o "Hija" final queda cerrada |

---

## Validaciones Obligatorias

- ❌ No permitir tomar más unidades de las disponibles en el saldo.
- ❌ No permitir generar guía si existen discrepancias activas para esa Z95.
- ❌ No permitir mezclar Z95 de años distintos en la misma guía (ver HU-CD-30).
- ❌ No permitir tomar facturas ya en estado "Procesada".
- ✅ Toda generación parcial debe quedar auditada: usuario, fecha, hora, cantidades.

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Parcial por referencia
```gherkin
Dado que una Z95 contiene las referencias REF-A (5 und) y REF-B (3 und)
Cuando el usuario seleccione solo REF-A para generar la guía
Entonces el sistema debe generar la guía con REF-A (5 und)
  Y REF-B debe permanecer con estado "Pendiente" en la tabla de pendientes
  Y el estado de la Z95 debe cambiar a "Parcialmente utilizada".
```

### Escenario 2 – Parcial por unidades
```gherkin
Dado que una referencia tiene 10 unidades disponibles
Cuando el usuario genere una guía seleccionando 4 unidades
Entonces el sistema debe generar la guía con 4 unidades
  Y el saldo restante debe mostrar 6 unidades disponibles.
```

### Escenario 3 – Uso total de una Z95
```gherkin
Dado que la Z95 tiene un saldo de 3 unidades en una referencia
Cuando el usuario genere una guía con esas 3 unidades
  Y el saldo llegue a 0
Entonces la Z95 debe cambiar su estado a "Procesada"
  Y debe retirarse de la tabla de pendientes.
```

### Escenario 4 – Bloqueo por discrepancia activa
```gherkin
Dado que una Z95 tiene discrepancias activas sin resolver
Cuando el usuario intente generar una guía parcial con esa Z95
Entonces el sistema debe bloquear la operación
  Y mostrar el detalle de las discrepancias pendientes.
```

### Escenario 5 – Sobreconsumo bloqueado
```gherkin
Dado que una referencia tiene 5 unidades disponibles
Cuando el usuario intente incluir 8 unidades en la guía
Entonces el sistema debe bloquear la operación
  Y mostrar el mensaje: "Saldo insuficiente: disponible 5 unidades".
```

---

## Reglas de Negocio

| ID | Regla |
|----|-------|
| RN-01 | La parcialización no debe alterar los datos originales de la factura Z95. |
| RN-02 | El saldo disponible debe actualizarse en tiempo real tras cada generación de guía. |
| RN-03 | No se permite sobreconsumo: la cantidad en guía no puede superar el saldo disponible. |
| RN-04 | No se puede generar guía si hay discrepancias activas para la Z95 involucrada. |
| RN-05 | Toda generación parcial queda auditada (usuario, fecha, hora, cantidades). |
| RN-06 | El estado de la Z95 refleja el nivel de consumo en tiempo real. |
| RN-07 | No se pueden mezclar Z95 de diferentes años en la misma guía. |
| RN-08 | El sistema genera automáticamente el sufijo `-001`, `-002`, etc., para identificar las guías hijas resultantes de una parcialización. |
| RN-09 | Una vez consolidada una guía (Madre o Hija), sus datos de cabecera y líneas procesadas no pueden ser modificados. |

---

## Pendientes / Compromisos de Sesión

| # | Compromiso | Responsable |
|---|-----------|-------------|
| 1 | Confirmar si existe un límite de guías parciales que se pueden generar por Z95. | Commex |
| 2 | Definir si una Z95 parcialmente procesada puede usarse en otra guía diferente o solo en la misma. | Commex / TI |
