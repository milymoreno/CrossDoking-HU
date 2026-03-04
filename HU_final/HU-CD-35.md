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
Que el sistema valide automáticamente el dealer logístico seleccionado al momento de generar la guía, asegurando que esté parametrizado correctamente, corresponda a la compañía de la operación, y que no se mezcle mercancía nueva con remanufacturada en la misma guía.

### Para:
Evitar mezclas indebidas de mercancía, incumplimientos operativos o contractuales, y errores de destino que comprometan el proceso aduanero.

---

## Contexto del Negocio

Los **Dealers** son los destinatarios logísticos de la mercancía CrossDocking. Cada compañía (Gecolsa / Relianz) tiene un conjunto de dealers asignados que no deben intercambiarse. Además, operativamente la mercancía nueva y la remanufacturada tiene condiciones aduaneras distintas y no pueden viajar juntas en la misma guía (declaración diferente ante DIAN).

---

## Descripción Funcional

Al momento de generar la guía, el sistema debe ejecutar **3 validaciones de dealer**:

### Validación 1 — Existencia en Tabla de Parámetros
- El dealer seleccionado debe existir en la **Tabla 1** de parametrización oficial del sistema.
- Si no existe, el sistema debe bloquear y mostrar mensaje de error.

### Validación 2 — Correspondencia con Compañía
- Solo se pueden usar dealers configurados para la compañía de las facturas seleccionadas:
  - Facturas de **Gecolsa** → solo dealers de Gecolsa
  - Facturas de **Relianz** → solo dealers de Relianz
- No se puede mezclar facturas de Gecolsa con facturas de Relianz en la misma guía.

### Validación 3 — No Mezcla Nueva + Remanufacturada
- El sistema analiza el `Reman Indicator` de todas las referencias incluidas:
  - Si **todas** tienen `Reman Indicator = 0` → Mercancía Nueva → Permitir
  - Si **todas** tienen `Reman Indicator = 1` → Mercancía Reman → Permitir
  - Si **hay mezcla** de 0 y 1 → Bloquear generación de guía

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
| RN-02 | No se permite mezcla de mercancía nueva (Reman Indicator=0) y remanufacturada (Reman Indicator=1) en la misma guía. |
| RN-03 | No se permite mezclar compañías (Gecolsa / Relianz) en la misma guía. |
| RN-04 | La validación de dealer debe ejecutarse antes de asignar el número de guía. |
| RN-05 | Toda validación fallida queda registrada en auditoría. |
| RN-06 | La compañía de las facturas determina qué dealers son válidos. |
| RN-07 | El Reman Indicator es la única fuente oficial para determinar tipo de mercancía. |

---

## Pendientes / Compromisos de Sesión

| # | Compromiso | Responsable |
|---|-----------|-------------|
| 1 | Compartir la **Tabla 1** de dealers con los casos especiales mencionados en el requerimiento. | Commex |
| 2 | Confirmar si un dealer puede estar habilitado para **ambas compañías** (Gecolsa y Relianz) o si siempre es exclusivo. | Commex |
