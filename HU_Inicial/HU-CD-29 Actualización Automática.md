# HU-CD-29: Sincronización de Facturas PSC y Z95 desde D365

**Proceso:** CROSSDOCKING  
**Subproceso:** SINCRONIZACIÓN DE FACTURACIÓN  
**Requerimiento Original:** 29 - Actualización de datos desde D365

---

### Como:
Sistema SII 2.0

### Quiero:
Sincronizar automáticamente las facturas PSC y Z95 desde D365

### Para:
Mantener información actualizada sin generar duplicidades ni reprocesos indebidos.

---

## Arquitectura de Integración

- La integración debe realizarse mediante servicios (API).
- No se permitirá acceso directo a bases de datos productivas de D365.
- La arquitectura debe evitar acoplamiento fuerte entre sistemas.

---

## Reglas de Sincronización

### RN-01: Clave de unicidad
Factura + Fecha = identificador único de registro.

Si ya fue procesada:
- No debe volver a cargarse.
- No debe sobrescribirse.

---

### RN-02: Facturas de años anteriores

- No deben procesarse automáticamente.
- Solo podrán visualizarse si no han sido procesadas previamente.

---

### RN-03: Órdenes asociadas a facturas antiguas

- No deben cargarse en SII 2.0 si ya fueron procesadas en periodos anteriores.

---

### RN-04: Reprocesos

Si una factura llega nuevamente con:
- Mismo número
- Nueva fecha

Debe considerarse un nuevo registro válido.

---

## Monitoreo del Proceso

El sistema debe permitir visualizar:

- Fecha y hora de última sincronización.
- Cantidad de registros procesados.
- Cantidad de errores.
- Estado: Exitosa / Parcial / Fallida.

---

## Consulta de Registros Sincronizados

El sistema debe permitir filtrar por:

- Número de factura
- Fecha
- Dealer
- Estado
- Tipo de error
- Rango de fechas de sincronización

---

## Criterios de Aceptación

Escenario 1 – Registro ya procesado  
Dado que una factura + fecha ya existe  
Cuando llegue nuevamente en sincronización  
Entonces no debe cargarse nuevamente.

Escenario 2 – Factura año anterior ya procesada  
Entonces no debe cargarse.

Escenario 3 – Factura misma numeración nueva fecha  
Entonces debe cargarse como nuevo registro.

Escenario 4 – Error de sincronización  
Entonces debe quedar registrado en log consultable.