# HU-CD-30: Control de Consecutivos Repetidos Z95 por Año

**Proceso:** CROSSDOCKING
**Subproceso:** ACTUALIZACIÓN DE DATOS – CONTROL DE CONSECUTIVOS
**Requerimiento Original:** REQ-30 — El sistema debe traer la información de las facturas para generación del año en curso, dado que CAT cada dos años repite consecutivos. Ejemplo: Z95123456 – 2023-01-06 / Z95123456 – 2025-01-06.
**Versión:** 2.0
**Fecha de revisión:** 2026-03-04
**Estado:** Revisada con base en sesiones de levantamiento (25-Feb, 27-Feb y 02-Mar-2026)

---

## Historia de Usuario

### Como:
Sistema SII 2.0

### Quiero:
Identificar y diferenciar correctamente facturas Z95 cuando el mismo número de consecutivo se repite en distintos años, construyendo una clave única que incluya el año de la factura.

### Para:
Evitar colisiones de registros, cruces incorrectos, asociaciones erróneas INV-Z95, y generación equivocada de guías o discrepancias.

---

## Problema Identificado

Caterpillar **reutiliza el mismo número de factura Z95 cada dos años**. Esto significa que en el sistema pueden coexistir registros con el mismo número pero de años diferentes:

| Número Z95 | Fecha | Año |
|------------|-------|-----|
| Z95123456 | 2023-01-06 | 2023 |
| Z95123456 | 2025-01-06 | 2025 |

Si el sistema solo usa el número Z95 como identificador, los dos registros colisionarían y el sistema procesaría datos del año equivocado.

---

## Solución Funcional

### 1. Clave Única Compuesta

El sistema debe identificar cada factura Z95 por la combinación de:

```
Clave única = Número Z95 + Año de Factura + Compañía
```

Ejemplo:
- `Z95123456 | 2023 | Gecolsa` → Registro A
- `Z95123456 | 2025 | Gecolsa` → Registro B (distinto al anterior)

### 2. Aplicación de la Clave en Todos los Procesos

Esta clave única debe aplicarse en:

| Proceso | Aplicación |
|---------|-----------|
| Discrepancias | Solo cruzar Z95 e INV del mismo año |
| Generación de guía | Solo incluir Z95 del año en curso (por defecto) |
| Asociación INV | No asociar INV 2025 con Z95 2023 |
| Consulta de facturas | Mostrar año como campo visible y filtrable |
| DIM (Declaración) | Usar clave compuesta para evitar errores en aduana |

---

## Comportamiento por Defecto

- Los procesos activos (discrepancias, generación de guía, asociación INV) trabajan con **facturas del año en curso** de forma predeterminada.
- El usuario puede aplicar **filtro manual** para consultar años anteriores.
- Las facturas históricas **no se eliminan** del sistema; solo quedan inactivas para procesos del año en curso.

---

## Visualización en Sistema

La pantalla de facturas Z95 debe mostrar:

| Campo | Visible | Filtrable |
|-------|---------|-----------|
| Número Z95 | ✅ | ✅ |
| Fecha completa | ✅ | ✅ |
| Año | ✅ | ✅ |
| Compañía | ✅ | ✅ |
| Estado | ✅ | ✅ |

> Los registros del mismo consecutivo de distintos años deben aparecer como filas **separadas e identificables**, nunca mezcladas.

---

## Validaciones Técnicas

- ❌ No permitir que una INV del 2025 se asocie a una Z95 del 2023.
- ❌ No permitir generar guía usando una Z95 de año incorrecto.
- ❌ No permitir cruce de discrepancias entre facturas de años distintos.
- ✅ Permitir consulta histórica con filtro manual de año.
- ✅ Mostrar alerta al usuario si intenta operar con facturas de años distintos.

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Consecutivos repetidos diferenciados correctamente
```gherkin
Dado que existen dos facturas Z95-123456 en el sistema
  Una del 2023 y otra del 2025
Cuando el sistema las consulte
Entonces deben aparecer como dos registros distintos con su año visible
  Y no deben mezclarse en ningún proceso.
```

### Escenario 2 – Filtro por año en curso por defecto
```gherkin
Dado que el usuario ingresa al módulo CrossDocking en 2025
Cuando consulte facturas Z95 disponibles
Entonces el sistema debe mostrar solo las del año 2025 por defecto.
```

### Escenario 3 – Bloqueo de asociación entre años distintos
```gherkin
Dado que una INV pertenece al año 2025
Cuando el usuario intente asociarla a una Z95 del año 2023
Entonces el sistema debe bloquear la operación
  Y mostrar el mensaje: "No se puede asociar facturas de años distintos".
```

### Escenario 4 – Consulta histórica habilitada con filtro manual
```gherkin
Dado que el usuario necesita revisar facturas del año 2023
Cuando aplique el filtro manual con año = 2023
Entonces el sistema debe mostrar los registros históricos de ese año.
```

---

## Reglas de Negocio

| ID | Regla |
|----|-------|
| RN-01 | La clave única de Z95 no puede ser solo el consecutivo; debe incluir año y compañía. |
| RN-02 | El año forma parte obligatoria del identificador lógico en todos los procesos activos. |
| RN-03 | Los procesos activos deben trabajar por defecto con el año vigente. |
| RN-04 | No se permiten cruces interanuales en discrepancias, guías ni DIM. |
| RN-05 | La información histórica no debe eliminarse; debe ser consultable con filtro manual. |
| RN-06 | La lógica de clave compuesta aplica a discrepancias, guías, asociación INV y DIM. |
