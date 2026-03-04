# HU-CD-39: Generación de Declaración de Importación (DIM)

**Proceso:** CROSSDOCKING
**Subproceso:** GENERAR DIM
**Requerimiento Original:** REQ-39 — El sistema debe contar con todos los módulos que tiene la agencia de aduanas para generación de embarques, declaraciones de importación y sus respectivos archivos, asegurando el proceso ante fallas. Debe permitir impulsar declaraciones y habilitar campos como modalidad, acuerdo, forma de pago, manifiesto de carga, fecha, tipo impo, cod depósito, TRM, fletes, bultos, embalaje, peso bruto. Debe permitir cualquier modalidad y tipo de declaración.
**Versión:** 2.0
**Fecha de revisión:** 2026-03-04
**Estado:** Revisada con base en sesiones de levantamiento (25-Feb, 27-Feb y 02-Mar-2026)

---

## Historia de Usuario

### Como:
Analista de Comercio Exterior (Commex)

### Quiero:
Generar la Declaración de Importación (DIM) directamente desde el SII, con todos los campos requeridos por la DIAN, capacidad de impulsar la declaración y control de versiones, incluso ante fallas del sistema externo de la agencia.

### Para:
Garantizar continuidad operativa y autonomía del proceso aduanero, sin depender exclusivamente de la disponibilidad del sistema de la agencia de aduanas.

---

## Contexto del Proceso

La DIM es el documento legal más importante del proceso de importación. Se presenta ante la DIAN para declarar qué se importa, en qué cantidad, a qué valor y bajo qué condiciones. Hasta el momento, este proceso dependía completamente de los sistemas de la agencia de aduanas (SIACO). El SII 2.0 debe poder generarla de forma autónoma.

---

## Prerrequisitos Obligatorios

| Prerrequisito | HU de referencia |
|--------------|-----------------|
| Guía generada y válida | HU-CD-33 |
| Validación visual de FOB y cantidades OK | HU-CD-36 |
| DO creado y en estado "Listo para DIM" | HU-CD-37 |
| Licencias de importación asignadas (cuando aplica) | HU-CD-32 |
| Remanufacturados validados (cuando aplica) | HU-CD-34 |
| Sin discrepancias activas | HU-CD-28 |

---

## Campos de la DIM

El sistema debe habilitar dinámicamente los siguientes campos según modalidad:

| # | Campo | Descripción |
|---|-------|-------------|
| 1 | Tipo DIM | Regular / Anticipada / Temporal / otras |
| 2 | Modalidad de importación | Consumo / Transformación / País especial / etc. |
| 3 | Acuerdo comercial | TLC, preferencia arancelaria aplicable |
| 4 | Forma de pago | Contado / Diferido / Otras |
| 5 | Manifiesto de carga | Número del manifiesto de la guía aérea/marítima |
| 6 | Fecha declaración | Fecha oficial de la declaración |
| 7 | Tipo importación | Normal / Urgente / etc. |
| 8 | Código depósito | Código de la bodega/depósito aduanero |
| 9 | TRM | Tasa Representativa del Mercado aplicable a la fecha |
| 10 | Fletes | Valor del flete internacional (USD) |
| 11 | Bultos | Número de paquetes/cajas del embarque |
| 12 | Tipo de embalaje | Caja / Pallet / Contenedor / etc. |
| 13 | Peso bruto | Peso total del embarque (kg) |
| 14 | País de origen | País de fabricación del producto |
| 15 | País de procedencia | País desde donde se embarcó |

---

## Funcionalidad "Impulsar Declaración"

Cuando la DIM esté en estado **"Validada"**, el sistema debe permitir:

1. Marcar la declaración como lista para transmisión.
2. Generar el **archivo estructurado** requerido por la DIAN/agencia.
3. Validar consistencia final (FOB, cantidades, subpartidas, licencias).
4. Enviar electrónicamente o dejar listo para envío manual.

---

## Autonomía Operativa

El sistema debe garantizar que el analista pueda **generar y preparar la DIM localmente** incluso si:
- La agencia de aduanas tiene una falla temporal.
- El sistema SIACO no está disponible.

Para esto, el SII debe:
- Conservar respaldo de todos los archivos generados.
- Mantener historial de versiones de la DIM.
- Permitir retransmisión cuando el sistema externo se recupere.

---

## Estados de la DIM

| Estado | Descripción |
|--------|-------------|
| Borrador | DIM creada pero no validada. |
| Validada | Todos los campos completos y consistentes. |
| Impulsada | Lista para transmisión a la DIAN. |
| Transmitida | Enviada electrónicamente a la DIAN. |
| Aceptada | DIAN aprobó la declaración. |
| Pagada | Tributos de importación pagados. |
| Con levante | DIAN autorizó la salida de la mercancía. |
| Rechazada | DIAN rechazó la declaración; requiere corrección. |
| Anulada | Declaración cancelada en el sistema. |

---

## Control de Versiones

Si la DIM requiere corrección (antes o después de transmitir):

- El sistema permite generar una **nueva versión** de la DIM.
- La versión anterior queda en estado "Reemplazada" y es consultable.
- Cada versión registra: número de versión, usuario, fecha y motivo del cambio.
- **No se modifica la DIM original** — siempre se crea una nueva versión.

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Generación exitosa de DIM
```gherkin
Dado que todos los prerrequisitos están cumplidos para la guía G001
Cuando el analista genere la DIM
Entonces el sistema debe crear la declaración en estado "Borrador"
  Y asociarla a la guía G001 y al DO correspondiente.
```

### Escenario 2 – Impulsar declaración
```gherkin
Dado que la DIM está en estado "Validada" con todos los campos completos
Cuando el analista ejecute "Impulsar declaración"
Entonces el sistema debe generar el archivo estructurado para la DIAN
  Y cambiar el estado de la DIM a "Impulsada".
```

### Escenario 3 – Bloqueo por validación incompleta
```gherkin
Dado que una referencia en la guía no tiene subpartida arancelaria (BTN) asignada
Cuando el analista intente generar la DIM
Entonces el sistema debe bloquear la operación
  Y mostrar el detalle de qué referencia tiene el campo faltante.
```

### Escenario 4 – Múltiples modalidades
```gherkin
Dado que el analista selecciona modalidad "Transformación" para la DIM
Cuando cambie la modalidad
Entonces el sistema debe habilitar dinámicamente los campos específicos de esa modalidad
  Y ocultar los campos que no aplican.
```

### Escenario 5 – Control de versiones
```gherkin
Dado que la DIM fue rechazada por la DIAN por error en el valor FOB
Cuando el analista genere una versión corregida
Entonces el sistema debe crear la DIM V2 con el valor correcto
  Y conservar la DIM V1 en el historial como "Reemplazada".
```

### Escenario 6 – Autonomía ante falla de agencia
```gherkin
Dado que SIACO (sistema de la agencia) no está disponible
Cuando el analista complete todos los campos y genere la DIM
Entonces el sistema debe crear y guardar la DIM localmente
  Y permitir la retransmisión cuando el sistema externo se recupere.
```

---

## Reglas de Negocio

| ID | Regla |
|----|-------|
| RN-01 | No se puede generar DIM sin guía válida. |
| RN-02 | No se puede generar DIM con discrepancias activas. |
| RN-03 | El sistema debe soportar cualquier modalidad de importación permitida por la normativa vigente. |
| RN-04 | La DIM debe ser auditable en todas sus etapas y versiones. |
| RN-05 | El sistema debe permitir operación autónoma ante fallas de sistemas externos. |
| RN-06 | No se permite modificar una DIM aceptada — se debe crear nueva versión. |
| RN-07 | La información de la DIM debe estar disponible para migración a D365 (HU-CD-41). |
| RN-08 | El saldo de licencias de importación se descuenta definitivamente al impulsar/transmitir la DIM. |

---

## Pendientes / Compromisos de Sesión

| # | Compromiso | Responsable |
|---|-----------|-------------|
| 1 | Confirmar el formato exacto del archivo que requiere la DIAN para la transmisión electrónica. | Commex / Agencia de aduanas |
| 2 | Definir cuál es la fuente oficial de la TRM que debe usar el sistema (¿Banco de la República? ¿Manual?). | Commex / Financiero |
| 3 | Confirmar si el código de depósito es fijo por operación o variable por embarque. | Commex |
