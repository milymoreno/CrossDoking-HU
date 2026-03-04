# HU-CD-32: Asignación de Registro y/o Licencia de Importación en CrossDocking

**Proceso:** CROSSDOCKING
**Subproceso:** ASIGNACIÓN REGISTRO Y/O LICENCIA DE IMPORTACIÓN
**Requerimiento Original:** REQ-32 — Que el sistema permita asignar al modelo y tipo del equipo el registro y/o licencia de importación según se requiera, que controle saldos y vencimientos.
**Versión:** 2.0
**Fecha de revisión:** 2026-03-04
**Estado:** Revisada con base en sesiones de levantamiento (25-Feb, 27-Feb y 02-Mar-2026)

---

## Historia de Usuario

### Como:
Analista de Comercio Exterior (Commex)

### Quiero:
Asignar automáticamente el registro o licencia de importación correspondiente a cada referencia o modelo de equipo antes de generar la guía o la DIM, con validación en tiempo real de saldo disponible y vigencia.

### Para:
Garantizar el cumplimiento normativo aduanero, evitar nacionalizaciones sin respaldo legal y controlar el consumo de licencias en tiempo real sin intervención manual.

---

## Contexto del Proceso (As-Is)

Actualmente el control de licencias y registros de importación se realiza de forma manual en Excel. El analista verifica la licencia aplicable, descuenta el saldo y controla el vencimiento sin soporte automático del sistema. El nuevo SII debe automatizar este control para eliminar errores humanos y garantizar trazabilidad.

---

## Tipos de Documento de Importación

| Tipo | Código | Descripción |
|------|--------|-------------|
| Registro de importación | R | Documento que habilita la importación de un tipo de producto. No es de cantidades fijas. Requiere vigencia. |
| Licencia de importación | L | Documento que autoriza la importación de una cantidad específica de un producto. Tiene saldo y vigencia. |
| No requiere | — | Algunos productos están parametrizados como exentos de este requisito. |

---

## Descripción Funcional

El sistema debe:

### 1. Identificar el tipo de documento requerido
Al seleccionar la referencia o modelo:
- Consultar la paramétrica de productos para determinar si requiere R, L o ninguno.
- Este resultado debe mostrarse automáticamente al analista.

### 2. Forzar asignación cuando aplica
Si el producto requiere R o L:
- El sistema debe **obligar** al analista a seleccionar el documento antes de continuar.
- No debe permitir generar guía ni DIM sin este paso completo.

### 3. Validar en tiempo real
Al seleccionar el documento:

| Validación | Condición para continuar |
|-----------|-------------------------|
| Tipo correcto | El documento debe ser R o L según lo que requiere la referencia |
| Vigencia | La fecha actual debe ser menor a la fecha de vencimiento |
| Saldo disponible | Saldo ≥ Cantidad a importar |
| Compañía | Solo puede utilizarse el documento de la compañía correspondiente (Gecolsa / Relianz) |

### 4. Descontar saldo
El consumo definitivo se realiza al **generar la DIM** (Declaración de Importación).
- Se descuenta automáticamente la cantidad usada.
- El saldo se actualiza en tiempo real.
- Si el saldo llega a 0, el estado de la licencia cambia a **"Agotada"**.
- Si la fecha supera la vigencia, el estado cambia a **"Vencida"**.

---

## Parametrización

El sistema debe permitir configurar:
- Qué modelos/tipos de equipo requieren R, L o ninguno.
- Vigencia de cada documento.
- Saldo inicial de cada licencia.

Esta parametrización debe ser mantenible por el área de Commex sin necesidad de desarrollo.

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Referencia con licencia válida
```gherkin
Dado que una referencia requiere licencia de importación tipo L
  Y existe una licencia vigente con saldo de 100 unidades
Cuando el analista la seleccione para importar 20 unidades
Entonces el sistema debe permitir continuar con la generación de guía.
```

### Escenario 2 – Saldo insuficiente
```gherkin
Dado que el saldo disponible de la licencia es 5 unidades
Cuando el analista intente importar 15 unidades con esa licencia
Entonces el sistema debe bloquear la operación
  Y mostrar el mensaje: "Saldo insuficiente: disponible [5] unidades".
```

### Escenario 3 – Licencia vencida
```gherkin
Dado que la fecha de vencimiento de la licencia es anterior a la fecha actual
Cuando el analista intente utilizarla
Entonces el sistema debe bloquear la asignación
  Y mostrar el mensaje: "Licencia vencida. Utilice otro documento válido".
```

### Escenario 4 – Referencia sin obligación de licencia
```gherkin
Dado que una referencia está parametrizada como "No requiere licencia"
Cuando el analista genere la guía
Entonces el sistema no debe exigir asignación de documento
  Y debe permitir continuar sin ese paso.
```

### Escenario 5 – Descuento automático al generar DIM
```gherkin
Dado que se genera una DIM con 30 unidades de una referencia con licencia L
  Y el saldo de la licencia es 80 unidades
Cuando la DIM se confirme exitosamente
Entonces el saldo de la licencia debe actualizarse a 50 unidades
  Y la operación debe quedar auditada.
```

### Escenario 6 – Licencia de otra compañía
```gherkin
Dado que el analista selecciona una licencia perteneciente a Relianz
  Y la operación es de Gecolsa
Entonces el sistema debe bloquear la asignación
  Y mostrar el mensaje: "Licencia no válida para esta compañía".
```

---

## Reglas de Negocio

| ID | Regla |
|----|-------|
| RN-01 | La licencia es obligatoria para las referencias que así lo indique la parametrización. |
| RN-02 | El control de saldo debe ser en tiempo real. |
| RN-03 | No se permite sobreconsumo: saldo disponible ≥ cantidad a importar. |
| RN-04 | No se permite usar licencia vencida. |
| RN-05 | Toda asignación y consumo queda auditada (usuario, fecha, documento, cantidad). |
| RN-06 | El descuento definitivo del saldo ocurre al generar la DIM, no al generar la guía. |
| RN-07 | La lógica aplica transversalmente: CrossDocking, Equipos y Marcas Aliadas. |
| RN-08 | No se permite usar documentos de importación de una compañía diferente a la de la operación. |
| RN-09 | Los remanufacturados deben someterse a la misma validación (no bloquear si no requieren licencia). |

---

## Conexión con otras HUs

- **HU-CD-30:** La clave compuesta (Z95 + Año + Compañía) aplica también a la licencia asignada.
- **HU-CD-34:** La validación de licencias con saldo insuficiente o inexistente genera alertas que se documentan en esa HU.
- **Req 34:** Los remanufacturados tienen su propia lógica paramétrica de licencia.

---

## Pendientes / Compromisos de Sesión

| # | Compromiso | Responsable |
|---|-----------|-------------|
| 1 | Confirmar el formato y fuente de los registros/licencias de importación (¿vienen de la DIAN, de un sistema externo o se parametrizan manualmente?). | Commex / Líder funcional |
| 2 | Definir qué modelos y tipos de equipo requieren R, L o ninguno (tabla de parametrización inicial). | Commex |
| 3 | Confirmar en qué momento exacto se considera "consumida" la licencia: ¿al generar la DIM o al obtener levante? | Commex / TI |
