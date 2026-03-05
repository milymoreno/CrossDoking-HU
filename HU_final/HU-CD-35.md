# HU-CD-35: Validación de Dealer Logístico en Generación de Guía

**Proceso:** CROSSDOCKING
**Subproceso:** VALIDACIÓN DEALER
**Requerimiento Original:** REQ-35 — El sistema deberá generar las guías conforme a los DEALERS logísticos (ver tabla 1 de reportes y casos especiales). No deberá permitir generar guías con DEALERS que contengan mercancía nueva y mercancía remanufacturada. Adicional deberá realizar distinción de los DEALERS asignados para Gecolsa y para Relianz.
**Versión:** 2.0
**Fecha de revisión:** 2026-03-04
**Estado:** Revisada con base en sesiones de levantamiento (25-Feb, 27-Feb y 02-Mar-2026)

---

## Historia de Usuario

### Como:
Analista de Comercio Exterior (Commex)

### Quiero:
Que el sistema valide automáticamente la relación entre el **Dealer CAT** (proveniente de Dynamics) y el **Dealer Logístico** (nuestro punto de entrega/depósito) al momento de generar la guía, asegurando que no se mezcle mercancía nueva con remanufacturada en la misma guía y que se respete la separación por compañía (Gecolsa/Relianz).

### Para:
Cumplir con la "Tabla 1 de Dealers y Casos Especiales", evitar mezclas de inventarios con diferentes regímenes aduaneros, asegurar la correcta distribución física de la mercancía y centralizar la responsabilidad de creación en el rol adecuado (**Fabian Barragan**).

---

## Contexto del Negocio

Los **Dealers** son los destinatarios logísticos de la mercancía CrossDocking. Cada compañía (Gecolsa / Relianz) tiene un conjunto de dealers asignados que no deben intercambiarse. Además, operativamente la mercancía nueva y la remanufacturada tiene condiciones aduaneras distintas y no pueden viajar juntas en la misma guía (declaración diferente ante DIAN).

---

## Descripción Funcional

Al momento de generar la guía, el sistema debe ejecutar **3 validaciones de dealer**:

### Validación 1 — Existencia en Tabla de Parámetros
- El dealer seleccionado debe existir en la **Tabla 1** de parametrización oficial del sistema.
- Si no existe, el sistema debe bloquear y mostrar mensaje de error.

### Validación 2 — Mapeo CAT vs Logístico
- El código de dealer recibido desde Dynamics (**Dealer CAT**) debe tener un mapeo activo hacia un **Dealer Logístico** propio.
- Se debe validar la vía de transporte (Aéreo/Marítimo) para aplicar el caso especial correspondiente (ej. R15Q de CAT → R460 Logístico en Aéreo).

### Validación 3 — Correspondencia con Compañía
- El Dealer Logístico resultante debe pertenecer a la compañía de las facturas (Gecolsa/Relianz).
- No se permite mezclar facturas de diferentes compañías en el mismo Dealer Logístico en una misma guía.

### Validación 4 — No Mezcla Nueva + Remanufacturada
- El sistema BLOQUEARÁ la generación de la guía si el Dealer Logístico contiene simultáneamente referencias con `Reman Indicator = 0` (Nueva) y `Reman Indicator = 1` (Reman).
- **Regla Estricta**: Una guía para un Dealer Logístico específico debe ser 100% Nueva o 100% Reman.

---

### Roles y Condiciones de Creación (Fabián Barragán)

#### Perfil Responsable
- **Analista Commex CD**: Único rol con permisos para "Crear" y "Parametrizar" dealers y sus mapeos en el sistema.

#### Condiciones de Integridad para Creación
1. **Compañía Obligatoria**: Todo dealer logístico debe estar asignado exclusivamente a una compañía (010 Gecolsa / 020 Relianz).
2. **Mapeo Existente**: No se puede activar un dealer logístico si no tiene al menos un código de Dealer CAT asociado en la tabla de mapeo.
3. **Validación de Casos Especiales**: Al crear el mapeo, el sistema debe obligar a definir la **Vía de Transporte** (Aéreo, Marítimo) y el **Tipo de Inventario** (Express, Backorder, Stock, Reman) según la Tabla 1.
4. **Estado**: Los dealers se crean por defecto como `Activos`. No se permite la eliminación física de dealers con histórico de guías (Soft Delete).

---

## Tabla de Casos y Resultado

| Facturas | Compañía | Dealer | Mercancía | Resultado |
|----------|---------|--------|-----------|-----------|
| Z95 de Gecolsa | Gecolsa | Dealer Gecolsa | Solo Nueva | ✅ Permitido |
| Z95 de Gecolsa | Gecolsa | Dealer Gecolsa | Solo Reman | ✅ Permitido |
| Z95 de Gecolsa | Gecolsa | Dealer Gecolsa | Nueva + Reman | ❌ Bloqueado |
| Z95 de Relianz | Relianz | Dealer Relianz | Solo Nueva | ✅ Permitido |
| Z95 de Gecolsa | Gecolsa | Dealer Relianz | Cualquiera | ❌ Bloqueado |
| Z95 Gecolsa + Z95 Relianz | — | Cualquiera | Cualquiera | ❌ Bloqueado |

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Dealer correcto, solo mercancía nueva
```gherkin
Dado que las facturas pertenecen a Gecolsa
  Y todas las referencias tienen Reman Indicator = 0
Cuando el usuario seleccione un dealer válido para Gecolsa
Entonces el sistema debe permitir generar la guía.
```

### Escenario 2 – Dealer incorrecto (cruce de compañía)
```gherkin
Dado que las facturas pertenecen a Relianz
Cuando el usuario seleccione un dealer configurado para Gecolsa
Entonces el sistema debe bloquear la operación
  Y mostrar el mensaje: "Dealer no válido para Relianz".
```

### Escenario 3 – Mezcla Nueva + Remanufacturada
```gherkin
Dado que existen referencias con Reman Indicator = 0 y otras con Reman Indicator = 1
  En las facturas seleccionadas
Cuando el usuario intente generar una sola guía con todas ellas
Entonces el sistema debe bloquear la operación
  Y mostrar el mensaje: "No se permite mezclar mercancía nueva y remanufacturada en la misma guía".
```

### Escenario 4 – Solo mercancía remanufacturada
```gherkin
Dado que todas las referencias tienen Reman Indicator = 1
  Y el dealer es válido para la compañía
Entonces el sistema debe permitir generar la guía.
```

### Escenario 5 – Dealer no parametrizado
```gherkin
Dado que el usuario ingresa un código de dealer que no existe en la Tabla 1
Cuando intente generar la guía
Entonces el sistema debe bloquear la operación
  Y mostrar el mensaje: "Dealer no encontrado en el sistema".
```

---

## Reglas de Negocio

| ID | Regla |
|----|-------|
| RN-01 | El dealer debe estar registrado en la Tabla 1 de parametrización oficial. |
| RN-02 | No se permite mezcla de mercancía nueva (Reman Indicator=0) y remanufacturada (Reman Indicator=1) en la misma guía para un mismo Dealer Logístico. |
| RN-03 | La relación entre Dealer CAT y Dealer Logístico debe estar previamente parametrizada por el Analista Commex CD. |
| RN-04 | No se permite mezclar compañías (Gecolsa / Relianz) en un mismo Dealer Logístico dentro de una guía. |
| RN-05 | La validación de dealer debe ejecutarse antes de la asignación de guías en el Monitor de Facturación. |
| RN-06 | El sistema debe soportar los "Casos Especiales" donde múltiples dealers CAT consolidan en un solo dealer logístico nuestro. |
| RN-07 | El Reman Indicator es la única fuente oficial para determinar tipo de mercancía. |

---

## Pendientes / Compromisos de Sesión

| # | Compromiso | Responsable |
|---|-----------|-------------|
| 1 | Compartir la **Tabla 1** completa de dealers con todos los sub-casos (Express, Backorder, Stock). | Fabian Barragan / Deisy Rincón |
| 2 | Definir el flujo de **Inactivación** de dealers obsoletos para evitar errores en guías futuras. | Fabian Barragan |
