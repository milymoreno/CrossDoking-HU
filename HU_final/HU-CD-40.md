# HU-CD-40: Recepción de Información Aduanera – Aceptación, Pago y Levante

**Proceso:** CROSSDOCKING
**Subproceso:** RECEPCIÓN INFORMACIÓN ADUANERA
**Requerimiento Original:** REQ-40 — El sistema debe tener la capacidad de recibir la información de Aceptación, pago (Autoadhesivo) y levante.
**Versión:** 2.0
**Fecha de revisión:** 2026-03-04
**Estado:** Revisada con base en sesiones de levantamiento (25-Feb, 27-Feb y 02-Mar-2026)

---

## Historia de Usuario

### Como:
Sistema SII 2.0 (proceso automático integrado con la agencia de aduanas)

### Quiero:
Recibir y registrar automáticamente las actualizaciones aduaneras que llegan después de transmitir la DIM: aceptación de la DIAN, comprobante de pago (autoadhesivo) y levante de la mercancía.

### Para:
Mantener el estado del proceso de importación actualizado en tiempo real, garantizar que los pasos se cumplan en el orden correcto, y habilitar el cierre financiero del embarque (costeo en D365 — HU-CD-41).

---

## Contexto del Proceso

Después de que la DIM es transmitida a la DIAN (HU-CD-39), el proceso aduanero avanza por tres hitos que la DIAN/agencia comunica al SII:

| Hito | ¿Qué significa? |
|------|----------------|
| **Aceptación** | La DIAN recibió y aceptó la DIM. El número oficial de aceptación es asignado. |
| **Pago (Autoadhesivo)** | Los tributos de importación fueron pagados al banco. El banco genera un comprobante llamado "autoadhesivo". |
| **Levante** | La DIAN autoriza la salida de la mercancía de la zona aduanera. Es el paso definitivo del proceso aduanero. |

---

## Información a Recibir por Hito

### 1. Aceptación
| Campo | Descripción |
|-------|-------------|
| Número de aceptación | Código oficial de la DIAN para la DIM aceptada |
| Fecha de aceptación | Fecha en que la DIAN aceptó la declaración |
| Estado de validación | Resultado de la revisión de la DIAN |

### 2. Pago (Autoadhesivo)
| Campo | Descripción |
|-------|-------------|
| Número de autoadhesivo | Código único del comprobante de pago bancario |
| Fecha de pago | Fecha en que se realizó el pago al banco |
| Valor pagado | Monto total pagado (tributos de importación) |
| Confirmación bancaria | Referencia del banco que confirma el pago |

### 3. Levante
| Campo | Descripción |
|-------|-------------|
| Fecha de levante | Fecha en que la DIAN autorizó la salida |
| Tipo de levante | Automático (sin inspección) / Físico (con inspección de la mercancía) |
| Observaciones | Anotaciones especiales del inspector de aduana (si aplica) |

---

## Flujo de Estados de la DIM

El sistema debe garantizar el avance secuencial y no permitir saltos de estado:

```
Transmitida → Aceptada → Pagada → Con Levante → Cerrada
```

| Transición | Permitida | Condición |
|-----------|---------|-----------|
| Transmitida → Aceptada | ✅ | Al recibir número de aceptación de la DIAN |
| Aceptada → Pagada | ✅ | Al recibir número de autoadhesivo bancario |
| Pagada → Con Levante | ✅ | Al recibir autorización de levante |
| Con Levante → Cerrada | ✅ | Al completar costeo en D365 (HU-CD-41) |
| Cualquier → Aceptada (sin Transmitida) | ❌ | No permitido |
| Pagada (sin Aceptada) | ❌ | No permitido |
| Levante (sin Pago) | ❌ | No permitido |

---

## Actualización Propagada

Cuando se recibe cada hito, la actualización debe reflejarse en:

| Documento | Qué se actualiza |
|-----------|-----------------|
| DIM | Estado + datos del hito recibido |
| DO | Estado sincronizado con la DIM |
| Guía | Estado general del embarque |
| Panel de embarques | Vista consolidada de todos los estados |

---

## Visualización en el Sistema

El sistema debe mostrar para cada DIM:

- Estado actual (con indicador visual de color).
- Historial de cambios con: fecha, hora y fuente del cambio (sistema/interfaz).
- Campos de cada hito recibido (número de aceptación, autoadhesivo, tipo de levante).

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Recepción de aceptación
```gherkin
Dado que la DIM fue transmitida a la DIAN
Cuando el sistema reciba el número de aceptación
Entonces la DIM debe cambiar de estado "Transmitida" a "Aceptada"
  Y registrar la fecha y número de aceptación.
```

### Escenario 2 – Recepción de pago
```gherkin
Dado que la DIM está en estado "Aceptada"
Cuando el sistema reciba el comprobante de pago (autoadhesivo)
Entonces la DIM debe cambiar a estado "Pagada"
  Y registrar número de autoadhesivo, valor pagado y confirmación bancaria.
```

### Escenario 3 – Recepción de levante
```gherkin
Dado que la DIM está en estado "Pagada"
Cuando el sistema reciba la autorización de levante
Entonces la DIM debe cambiar a estado "Con Levante"
  Y habilitar el proceso de costeo en D365 (HU-CD-41).
```

### Escenario 4 – Intento de pago sin aceptación previa
```gherkin
Dado que la DIM está en estado "Transmitida" (sin aceptar aún)
Cuando el sistema intente registrar el pago
Entonces debe bloquear la operación
  Y mostrar: "No se puede registrar pago sin aceptación previa de la DIAN".
```

### Escenario 5 – Consulta de historial de auditoría
```gherkin
Dado que la DIM pasó por todos los estados (Aceptada, Pagada, Con Levante)
Cuando el analista consulte el historial
Entonces el sistema debe mostrar cada cambio con: estado, fecha, hora y fuente.
```

---

## Reglas de Negocio

| ID | Regla |
|----|-------|
| RN-01 | Los estados de la DIM deben avanzar en orden secuencial estricto. |
| RN-02 | La información aduanera solo puede provenir de fuente oficial (DIAN / agencia). |
| RN-03 | No se permite alterar manualmente el estado de la DIM después de Aceptada. |
| RN-04 | El sistema debe conservar histórico completo e inmutable de todos los estados. |
| RN-05 | El estado "Con Levante" es el que habilita el costeo (HU-CD-41). |
| RN-06 | No se puede cerrar un embarque sin que la DIM tenga levante. |
| RN-07 | El levante tipo "Físico" debe registrar las observaciones del inspector. |

---

## Pendientes / Compromisos de Sesión

| # | Compromiso | Responsable |
|---|-----------|-------------|
| 1 | Confirmar si la **recepción de los hitos es automática** (la agencia los empuja al SII) o si el analista debe **consultarlos manualmente** desde SIACO. | Commex / TI |
| 2 | Definir el tiempo máximo esperado entre transmisión de DIM y recepción de aceptación. | Commex |
