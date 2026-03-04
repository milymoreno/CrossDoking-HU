# HU-CD-36: Validación Visual de Guía – Cantidades y Valor FOB

**Proceso:** CROSSDOCKING
**Subproceso:** VALIDACIÓN GUÍA
**Requerimiento Original:** REQ-36 — El programa debe permitir generar una visual que confirme las cantidades generadas entre las intercompañías y las facturas emitidas por CAT, y a su vez el valor FOB generado.
**Versión:** 2.0
**Fecha de revisión:** 2026-03-04
**Estado:** Revisada con base en sesiones de levantamiento (25-Feb, 27-Feb y 02-Mar-2026)

---

## Historia de Usuario

### Como:
Analista de Comercio Exterior (Commex)

### Quiero:
Visualizar un resumen comparativo que muestre en paralelo las cantidades y valores FOB de las facturas Z95 (CAT), las facturas INV (PSC) y la guía generada, para detectar cualquier diferencia antes de continuar al proceso aduanero.

### Para:
Garantizar que lo que se va a declarar ante aduana corresponde exactamente a lo facturado entre las intercompañías, evitando sobre-declaración o sub-declaración de mercancía.

---

## Contexto del Proceso

Esta pantalla de validación actúa como **punto de control obligatorio** entre la generación de la guía (HU-CD-33) y la creación del DO/DIM (HU-CD-37/HU-CD-39). Ningún proceso aduanero puede iniciarse si existen diferencias entre los tres documentos.

---

## Pantalla de Validación

El sistema debe mostrar una vista comparativa con tres columnas:

| Concepto | Z95 (CAT) | INV PSC | Guía |
|----------|-----------|---------|------|
| Total referencias | # | # | # |
| Total unidades | # | # | # |
| Valor FOB total (USD) | $ | $ | $ |
| ¿Hay diferencia? | — | — | ✅ OK / ⚠️ Diferencia |

---

## Campos de Cabecera del Resumen

El encabezado de la pantalla debe mostrar:

| Campo | Descripción |
|-------|-------------|
| Número de guía | Identificador de la guía generada |
| Compañía | Gecolsa / Relianz |
| Dealer | Dealer logístico destino |
| Fecha guía | Fecha de generación del documento |
| Facturas incluidas | Listado de Z95 e INV asociadas |

---

## Lógica de Validación

El sistema debe verificar:

1. **Cantidades:** Las unidades en la guía deben coincidir exactamente con las unidades seleccionadas de las Z95 y con las cantidades reflejadas en las INV PSC.
2. **Valor FOB:** El valor FOB total de la guía debe ser igual a la suma consolidada de los valores FOB de las facturas Z95/INV incluidas.
3. **Sin duplicados:** El valor FOB no debe estar contabilizado dos veces.
4. **Sin omisiones:** Ninguna factura incluida en la guía puede estar ausente del comparativo.

---

## Comportamiento según Resultado

| Resultado | Acción del sistema |
|-----------|-------------------|
| ✅ Todo coincide | Mostrar confirmación verde; habilitar botón "Continuar a DO" |
| ⚠️ Diferencia en cantidades | Mostrar alerta con detalle; bloquear avance a DO y DIM |
| ⚠️ Diferencia en valor FOB | Mostrar alerta con valor esperado vs. valor encontrado; bloquear avance |

---

## Posición en el Flujo del Proceso

```
HU-CD-33 (Generación Guía)
    ↓
HU-CD-36 (Validación Visual) ← OBLIGATORIA
    ↓
HU-CD-37 (Creación DO)
    ↓
HU-CD-39 (Generación DIM)
```

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Coincidencia total
```gherkin
Dado que las cantidades y valores FOB de la guía coinciden con Z95 e INV PSC
Cuando el analista acceda a la pantalla de validación visual
Entonces el sistema debe mostrar confirmación en verde
  Y habilitar el botón "Continuar a DO".
```

### Escenario 2 – Diferencia en cantidades
```gherkin
Dado que la guía contiene 100 unidades
  Pero la suma de Z95 registra 102 unidades
Cuando se ejecute la validación
Entonces el sistema debe mostrar la diferencia de 2 unidades
  Y bloquear el acceso a DO y DIM.
```

### Escenario 3 – Diferencia en valor FOB
```gherkin
Dado que el valor FOB consolidado de la guía es USD 50,000
  Pero la suma de las facturas Z95 e INV suma USD 48,000
Cuando se ejecute la validación
Entonces el sistema debe mostrar alerta de diferencia de USD 2,000
  Y no permitir continuar al proceso aduanero.
```

### Escenario 4 – Bloqueo previo a DIM con diferencia activa
```gherkin
Dado que existe una diferencia activa detectada en la validación visual
Cuando el usuario intente generar la DIM desde otra pantalla
Entonces el sistema debe bloquear la operación
  Y redirigir al usuario a la pantalla de validación pendiente.
```

---

## Reglas de Negocio

| ID | Regla |
|----|-------|
| RN-01 | No se puede avanzar a proceso aduanero (DO o DIM) con diferencias activas en la validación. |
| RN-02 | El valor FOB debe ser consistente entre Z95, INV PSC y guía. |
| RN-03 | La validación visual es **obligatoria** tras generar la guía. |
| RN-04 | No se puede omitir la pantalla de validación. |
| RN-05 | La validación no modifica datos; solo compara y reporta. |
| RN-06 | Toda validación (exitosa o fallida) queda registrada en auditoría. |
| RN-07 | Si se detecta diferencia, el sistema debe indicar exactamente qué factura o referencia la genera. |

---

## Pendientes / Compromisos de Sesión

| # | Compromiso | Responsable |
|---|-----------|-------------|
| 1 | Confirmar si la validación visual debe considerar también el valor en COP o solo USD. | Commex |
| 2 | Definir si el analista puede "justificar" y forzar avance con diferencia tolerada (ej. redondeo menor a USD 1), o si toda diferencia es bloqueante absoluta. | Commex / Lider funcional |
