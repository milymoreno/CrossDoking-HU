# HU-CD-33: Generación de Documento de Transporte (Guía)

**Proceso:** CROSSDOCKING  
**Subproceso:** GENERAR GUÍA  
**Requerimiento Original:** 33 - El proceso debe ser generado por DEALERS (ver tabla 1). El sistema tendrá la capacidad de asignar las facturas a un # de documento de transporte, relacionando: # Guía, Dealer, fecha guía, tipo mercancía, export, aduana, vía transporte, transportadora, embarcadora, fletes, moneda, incoterm, país compra-origen-procedencia.

---

### Como:
Analista de Comercio Exterior

### Quiero:
Generar un documento de transporte (Guía) asociando facturas Z95/INV previamente validadas

### Para:
Consolidar embarques correctamente antes de iniciar proceso aduanero.

---

## Prerrequisitos Obligatorios

Antes de generar guía:

- La factura debe:
  - No tener discrepancias activas (HU-CD-28)
  - Tener año correcto (HU-CD-30)
  - Tener licencia asignada si aplica (HU-CD-32)
  - Tener saldo disponible si es parcial (HU-CD-31)

Si alguna validación falla:
- El sistema debe bloquear la generación.

---

## Descripción Funcional

El sistema debe permitir:

1️⃣ Seleccionar facturas disponibles (Z95 validadas).  
2️⃣ Asignarlas a un número único de Guía.  
3️⃣ Registrar los siguientes datos obligatorios:

- Número de Guía
- Dealer logístico
- Fecha guía
- Tipo de mercancía (Nueva / Reman)
- Exportador
- Aduana
- Vía de transporte
- Transportadora
- Embarcadora
- Fletes
- Moneda
- Incoterm
- País compra
- País origen
- País procedencia

---

## Lógica Operativa Definida en Sesión

- Una vez generada la guía:
  - La Z95 debe desaparecer de la tabla de pendientes.
  - El sistema debe descontar automáticamente las cantidades utilizadas.
  - Debe cambiar estado a:
    - Parcial
    - Procesada (si consumo total)

- La guía consolida múltiples facturas si pertenecen al mismo dealer y cumplen reglas logísticas.

---

## Validaciones Dealer

El sistema debe:

- Validar que el dealer sea válido para la compañía.
- No permitir generación con dealer incorrecto.
- No permitir mezclar mercancía nueva con remanufacturada en el mismo dealer (conexión Req 35).

---

## Control de Integridad

- No permitir modificar guía una vez:
  - Se haya generado DIM.
- Permitir anulación controlada si aún no se ha iniciado proceso aduanero.
- Registrar auditoría completa:
  - Usuario
  - Fecha
  - Facturas asociadas
  - Cantidades

---

## Estados de Guía

La guía debe tener estados:

- Generada
- En proceso DIM
- DIM generada
- Cerrada
- Anulada

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Generación exitosa
Dado que las facturas cumplen validaciones  
Cuando el usuario genere la guía  
Entonces el sistema debe asignar número único y cambiar estado.

---

### Escenario 2 – Discrepancia activa
Dado que una factura tenga discrepancia  
Cuando el usuario intente generar guía  
Entonces el sistema debe bloquear la operación.

---

### Escenario 3 – Parcialización
Dado que la factura tenga saldo restante  
Cuando se genere guía parcial  
Entonces el sistema debe dejar saldo disponible.

---

### Escenario 4 – Dealer inválido
Dado que el dealer no corresponde a la compañía  
Cuando se intente generar guía  
Entonces el sistema debe impedir la operación.

---

## Reglas de Negocio

- RN-01: No se puede generar guía con discrepancias activas.
- RN-02: La guía debe tener número único.
- RN-03: No se permiten mezclas indebidas por dealer.
- RN-04: La guía debe quedar auditada.
- RN-05: La factura no debe reaparecer en pendientes tras generación.
- RN-06: No se permite modificación posterior a DIM.