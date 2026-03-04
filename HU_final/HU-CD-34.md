# HU-CD-34: Validación de Referencias Remanufacturadas sin Licencia o Saldo

**Proceso:** CROSSDOCKING
**Subproceso:** VALIDACIÓN REMANUFACTURADOS
**Requerimiento Original:** REQ-34 — El sistema tendrá la capacidad de identificar las referencias remanufacturadas parametrizadas que no cuentan con licencias y saldos en las licencias de importación y de esta manera arroje una alerta que permita visualizar: la OC, la factura PSC, la referencia, la caja, BTN, la cantidad y el comentario del error.
**Versión:** 2.0
**Fecha de revisión:** 2026-03-04
**Estado:** Revisada con base en sesiones de levantamiento (25-Feb, 27-Feb y 02-Mar-2026)

---

## Historia de Usuario

### Como:
Analista de Comercio Exterior (Commex)

### Quiero:
Que el sistema identifique automáticamente las referencias remanufacturadas que requieren licencia de importación pero no la tienen (o tienen saldo insuficiente o licencia vencida), y genere una alerta detallada antes de permitir generar la guía o la DIM.

### Para:
Evitar generar guías o declaraciones de importación sin respaldo normativo para productos remanufacturados, previniendo sanciones por parte de la DIAN y garantizando el cumplimiento aduanero.

---

## Identificación de Remanufacturados

El sistema debe identificar si una referencia es remanufacturada usando **únicamente** el campo:

> **Reman Indicator = 1** (proveniente de Dynamics 365, Interfaz 1 y/o 2)

**No debe usarse** ninguno de los siguientes criterios alternativos:
- ❌ Sufijo "R" al final de la referencia
- ❌ Sufijo "CC" en el código
- ❌ Cualquier convención manual o visual

---

## Lógica de Validación (4 Pasos)

Para cada referencia con `Reman Indicator = 1`, el sistema ejecuta la siguiente secuencia:

```
Paso 1: ¿La referencia requiere licencia según parametrización?
    NO → Permitir continuar (no bloquear)
    SÍ → Continuar a Paso 2

Paso 2: ¿Existe licencia asignada en el sistema?
    NO → Generar alerta: "Sin licencia"
    SÍ → Continuar a Paso 3

Paso 3: ¿La licencia está vigente (fecha actual < fecha vencimiento)?
    NO → Generar alerta: "Licencia vencida"
    SÍ → Continuar a Paso 4

Paso 4: ¿El saldo disponible ≥ Cantidad a importar?
    NO → Generar alerta: "Saldo insuficiente"
    SÍ → Permitir continuar ✅
```

---

## Campos de la Alerta

Cuando se detecte una inconsistencia, el sistema debe generar una alerta visible en pantalla que muestre:

| Campo | Descripción |
|-------|-------------|
| OC | Número de Orden de Compra relacionada |
| Factura PSC | Número de la factura INV de Panamerican |
| Z95 | Número de la factura Z95 de Caterpillar |
| Referencia | Código de la referencia remanufacturada con problema |
| Caja | Número de caja/empaque donde se encuentra la referencia |
| BTN | Subpartida arancelaria de la referencia según nomenclatura aduanera |
| Cantidad | Cantidad de unidades de la referencia en cuestión |
| Tipo de inconsistencia | Sin licencia / Saldo insuficiente / Licencia vencida / No parametrizada |
| Comentario del error | Descripción específica del problema detectado |

> ⚠️ **Nota sobre BTN:** BTN corresponde a la **subpartida / posición arancelaria** que identifica el producto en la nomenclatura de la DIAN para efectos aduaneros. Es el código que determina el arancel aplicable. Su composición exacta y los campos que almacena deben confirmarse con el área de Commex (pendiente).

---

## Comportamiento del Sistema

| Condición | Acción del sistema |
|-----------|-------------------|
| Reman sin licencia requerida | Bloquear guía/DIM + Mostrar alerta |
| Reman con licencia vencida | Bloquear guía/DIM + Mostrar alerta |
| Reman con saldo insuficiente | Bloquear guía/DIM + Mostrar alerta |
| Reman parametrizada como "No requiere" | Permitir continuar sin bloqueo |
| Referencia NO es Reman | No aplica esta validación |

---

## Cuándo se Ejecuta Esta Validación

| Proceso | Momento de ejecución |
|---------|---------------------|
| Generación de Guía (HU-CD-33) | Antes de confirmar la generación |
| Generación de DIM (HU-CD-39) | Antes de generar la declaración |
| Control de saldos (HU-CD-32) | Durante asignación de licencia |

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Remanufacturado sin licencia asignada
```gherkin
Dado que una referencia con Reman Indicator = 1 requiere licencia
  Y no tiene licencia asignada en el sistema
Cuando el usuario intente generar la guía
Entonces el sistema debe bloquear la operación
  Y mostrar la alerta con OC, Factura PSC, Referencia, Caja, BTN, Cantidad y tipo "Sin licencia".
```

### Escenario 2 – Saldo insuficiente en licencia de remanufacturado
```gherkin
Dado que la referencia reman tiene licencia asignada
  Pero el saldo disponible es menor a la cantidad a importar
Cuando se intente generar la guía
Entonces el sistema debe bloquear la operación
  Y mostrar alerta con tipo "Saldo insuficiente".
```

### Escenario 3 – Remanufacturado sin obligación de licencia
```gherkin
Dado que la referencia tiene Reman Indicator = 1
  Pero está parametrizada como "No requiere licencia"
Cuando el usuario genere la guía
Entonces el sistema debe permitir continuar sin bloqueo
  Y no mostrar alerta de licencia.
```

### Escenario 4 – Licencia vencida
```gherkin
Dado que la licencia asignada tiene fecha de vencimiento anterior a la fecha actual
Cuando el usuario intente generar la guía
Entonces el sistema debe bloquear la operación
  Y mostrar alerta con tipo "Licencia vencida".
```

### Escenario 5 – Múltiples referencias con alerta
```gherkin
Dado que en una guía hay 3 referencias remanufacturadas con problemas distintos
Cuando se ejecute la validación
Entonces el sistema debe mostrar una alerta por cada referencia con problema
  Y el usuario debe poder ver el resumen completo antes de decidir qué hacer.
```

---

## Reglas de Negocio

| ID | Regla |
|----|-------|
| RN-01 | El **Reman Indicator = 1** es la única fuente oficial de identificación de remanufacturado. |
| RN-02 | No se permite generar guía ni DIM si hay referencias reman con licencia faltante, vencida o sin saldo (cuando aplique). |
| RN-03 | Las alertas deben ser descriptivas e incluir todos los campos del REQ-34 (OC, Factura PSC, Referencia, Caja, BTN, Cantidad, Comentario). |
| RN-04 | La validación debe ejecutarse en tiempo real antes de generar el documento. |
| RN-05 | La alerta no debe alterar los datos originales de las facturas ni de las licencias. |
| RN-06 | El sistema debe registrar cada intento fallido en el log de auditoría. |
| RN-07 | Esta validación aplica antes de generar guía Y antes de generar DIM. |
| RN-08 | Las referencias reman parametrizadas como "No requiere licencia" deben pasar sin bloqueo. |

---

## Pendientes / Compromisos de Sesión

| # | Compromiso | Responsable |
|---|-----------|-------------|
| 1 | **Confirmar definición exacta del BTN:** composición del código arancelario, campos que almacena y de dónde viene (¿de Dynamics? ¿del paramétrico del SII?). | Commex / Daisy |
| 2 | Confirmar si la alerta es solo en pantalla o también se debe enviar por correo a Operaciones. | Commex |
| 3 | Definir qué hace el usuario cuando recibe la alerta (¿puede gestionar la licencia desde esa misma pantalla o debe ir a otro módulo?). | Commex / TI |
