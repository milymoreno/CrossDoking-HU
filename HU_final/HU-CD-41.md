# HU-CD-41: Costeo de Embarques y Generación de DRS en D365

**Proceso:** CROSSDOCKING
**Subproceso:** COSTEO Y DRS
**Requerimiento Original:** REQ-41 — El sistema permite migrar información aduanera a D365 donde se costearán las líneas y posterior generará DRS. Costeo embarques y mercancía (Validar en conjunto con COSTOS). Costo estimado de mercancía (se carga un 2% por embarque, validar con COSTOS).
**Versión:** 2.0
**Fecha de revisión:** 2026-03-04
**Estado:** Revisada con base en sesiones de levantamiento (25-Feb, 27-Feb y 02-Mar-2026)

---

## Historia de Usuario

### Como:
Sistema SII 2.0

### Quiero:
Migrar la información aduanera consolidada del embarque a D365 para que el área de Costos pueda costear cada línea importada, aplicar el estimado del 2% por embarque, y generar los DRS (Documentos de Recepción de Servicio) que cierran financieramente la importación.

### Para:
Completar el cierre financiero y contable del proceso de importación, reflejar correctamente los costos en D365 y permitir el pago oficial a los proveedores.

---

## Contexto del Proceso

HU-CD-41 es el **cierre financiero del proceso CrossDocking**. Una vez que la mercancía tiene levante (HU-CD-40), el SII migra toda la información aduanera a D365 para que:

1. El área de **Costos** pueda asignar el costo exacto a cada referencia importada.
2. El sistema calcule y distribuya los **gastos asociados** (flete, tributos, 2% estimado).
3. D365 genere el **DRS** como evidencia formal del cierre financiero.

> ⚠️ **Pendiente crítico:** El porcentaje exacto del costo estimado (actualmente 2%) y su metodología de distribución deben ser **validados con el área de Costos** antes de la implementación.

---

## Prerrequisitos Obligatorios

| Prerrequisito | HU de referencia |
|--------------|-----------------|
| DIM en estado "Con Levante" | HU-CD-40 |
| Valores FOB validados y consistentes | HU-CD-36 |
| Licencias correctamente descontadas | HU-CD-32 |
| Sin inconsistencias activas en el embarque | HU-CD-28 |

---

## Información a Migrar a D365

| # | Campo | Descripción |
|---|-------|-------------|
| 1 | Número de Guía | Identificador del embarque |
| 2 | Número DO | Documento Operativo asociado |
| 3 | Número DIM | Declaración de Importación |
| 4 | Facturas INV (PSC) | Facturas del embarque |
| 5 | Cantidades por referencia | Unidades por línea |
| 6 | Valor FOB por línea | Valor de la mercancía en origen |
| 7 | Fletes | Costo del transporte internacional |
| 8 | TRM aplicada | Tasa de cambio del día de la DIM |
| 9 | Tributos pagados | Arancel + IVA pagado a la DIAN |
| 10 | Costo estimado 2% | Sobre el total del embarque |

---

## Lógica de Costeo

### Costeo por Línea
Cada referencia del embarque debe recibir la proporción de:
- **Valor FOB propio** (ya conocido desde la guía).
- **Distribución de fletes**: proporcional al peso o al valor FOB (a confirmar con Costos).
- **Distribución de tributos**: proporcional al valor FOB de cada línea.
- **Distribución del 2% estimado**: proporcional entre todas las líneas.

### Costo Estimado del 2%
| Criterio | Definición |
|---------|-----------|
| Porcentaje | 2% sobre el valor total del embarque |
| Base de cálculo | A confirmar con Costos (¿FOB? ¿FOB + flete?) |
| Distribución | Proporcional entre todas las referencias del embarque |
| Parametrizable | ✅ El porcentaje debe ser configurable en el sistema |

---

## Generación del DRS

Una vez aplicado el costeo en D365:

- **D365 genera automáticamente** el DRS para cada factura INV del embarque.
- El DRS cierra el ciclo de la Orden de Compra (PSC → Gecolsa/Relianz).
- El **SII recibe confirmación** de D365 con:

| Campo | Descripción |
|-------|-------------|
| Número DRS | Identificador único del documento en D365 |
| Fecha de generación | Fecha en que D365 creó el DRS |
| Estado | Generado / Error |

---

## Estados del Proceso de Costeo

| Estado | Descripción |
|--------|-------------|
| Listo para Costeo | DIM tiene levante; embarque habilitado para migración. |
| Enviado a D365 | Información migrada. Esperando confirmación. |
| Costeado | D365 confirmó que el costeo fue aplicado. |
| DRS Generado | DRS creados en D365; cierre financiero completado. |
| Error de Migración | Fallo técnico en la transmisión; pendiente reproceso. |

---

## Control de Errores en Migración

Si falla la migración a D365:
- Registrar el error técnico con detalle del fallo.
- Dejar el embarque en estado "Error de Migración".
- **Permitir reproceso controlado** sin duplicar registros en D365.
- Notificar al área de TI y Costos.

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Migración y costeo exitoso
```gherkin
Dado que el embarque tiene DIM en estado "Con Levante"
Cuando el SII migre la información a D365
Entonces D365 debe aplicar el costeo por línea
  Y el sistema debe cambiar el estado a "Costeado"
  Y recibir la confirmación de D365.
```

### Escenario 2 – Aplicación del 2% estimado
```gherkin
Dado que el embarque es enviado a D365 para costeo
Cuando se distribuya el costo estimado
Entonces el sistema debe incluir el 2% del total del embarque
  Y distribuirlo proporcionalmente entre todas las referencias.
```

### Escenario 3 – Generación de DRS
```gherkin
Dado que el costeo fue aplicado en D365
Cuando D365 genere los DRS
Entonces el SII debe recibir confirmación con número de DRS y fecha
  Y cambiar el estado del embarque a "DRS Generado".
```

### Escenario 4 – Bloqueo sin levante
```gherkin
Dado que la DIM no tiene levante (estado diferente a "Con Levante")
Cuando se intente iniciar el proceso de costeo
Entonces el sistema debe bloquear la operación
  Y mostrar: "El embarque debe tener levante antes de iniciar el costeo".
```

### Escenario 5 – Error de migración con reproceso
```gherkin
Dado que la transmisión a D365 falla por error técnico
Cuando el sistema detecte el error
Entonces debe registrar el fallo con detalle
  Y permitir al analista reintentar la migración
  Y verificar que no se crearon registros duplicados en D365.
```

---

## Reglas de Negocio

| ID | Regla |
|----|-------|
| RN-01 | El levante es prerrequisito obligatorio para iniciar costeo. |
| RN-02 | No se permite costeo sin aceptación y pago previos de la DIM. |
| RN-03 | El porcentaje del costo estimado (2%) debe ser **parametrizable** en el sistema; no debe estar hardcodeado. |
| RN-04 | Toda la migración debe quedar registrada en log de auditoría. |
| RN-05 | No se permite doble migración del mismo embarque (anti-duplicidad). |
| RN-06 | El DRS es la evidencia formal del cierre financiero del embarque. |
| RN-07 | No se permite modificación de valores aduaneros después del costeo sin control formal. |
| RN-08 | El área de Costos debe validar la metodología de distribución antes de go-live. |

---

## Pendientes / Compromisos de Sesión

| # | Compromiso | Responsable |
|---|-----------|-------------|
| 1 | **Validar con el área de Costos** el porcentaje exacto (¿2% o diferente?) y la base de cálculo (¿FOB? ¿FOB + flete?). | Costos / Commex |
| 2 | Confirmar si la **distribución de fletes** se hace por peso, por valor FOB o por otra metodología. | Costos |
| 3 | Definir la **interfaz de migración** entre SII y D365 (API directa vs. archivo estructurado). | TI |
| 4 | Confirmar si el **DRS se genera automáticamente** en D365 o si requiere aprobación manual de alguien. | Costos / Financiero |
