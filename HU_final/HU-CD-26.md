# HU-CD-26: Recepción de Información Consolidada del Agente de Carga

**Proceso:** CROSSDOCKING
**Subproceso:** RECIBIR INFORMACIÓN DEL AGENTE DE CARGA
**Requerimiento Original:** REQ-26 — El sistema deberá tener la capacidad de interactuar con el agente de carga en cuanto a la recepción de la información consolidada (Guías, facturas Z95, pesos y dealers).
**Versión:** 2.0
**Fecha de revisión:** 2026-03-04
**Estado:** Revisada con base en sesiones de levantamiento (25-Feb y 27-Feb-2026)

---

## Historia de Usuario

### Como:
Analista de Comercio Exterior / Operaciones CrossDocking

### Quiero:
Que el sistema reciba la información consolidada enviada por el agente de carga (DHL u otro agente logístico), incluyendo guías, facturas Z95, pesos y dealers, mediante un mecanismo de carga automático o manual con formato estándar definido.

### Para:
Validar que las facturas Z95 estén disponibles en el sistema antes de iniciar el proceso de discrepancias, asegurar la trazabilidad del embarque y habilitar las siguientes etapas del flujo CrossDocking.

---

## Contexto del Proceso (As-Is)

1. El agente de carga (DHL) genera un archivo con la información del consolidado del embarque.
2. Actualmente, DHL executa un **bot** que puede enviar dicha información; sin embargo, el proceso **no está estandarizado**.
3. Como alternativa, el archivo es subido **manualmente** al SII por el equipo de operaciones.
4. En Dynamics 365, Caterpillar (CAT / compañía 500) genera facturas **Z95** que vienen asociadas a las órdenes de compra de Panamerican hacia CAT.
5. Dynamics genera automáticamente dos interfaces hacia el SII:
   - **Interfaz 1:** Archivo plano con datos de la **Z95** (Caterpillar → Panamerican).
   - **Interfaz 2:** Archivo plano con datos de las **INB/INR** (Panamerican → Gecolsa / Relianz), ya con la Z95 asociada.
6. Errores frecuentes incluyen asociaciones incorrectas de Z95 con INB, los cuales se corrigen actualmente en el SII en **5 campos específicos de la INB**.
7. Las interfaces corren en ventanas de tiempo definidas (aprox. 9:00 AM y 1:00 PM), con duración variable según el volumen de líneas.

---

## Descripción Funcional

El sistema debe:

1. **Recibir la información del agente de carga** por alguno de los siguientes mecanismos (en orden de preferencia):
   - **Carga automática:** Integración directa con DHL mediante archivo en formato estándar previamente acordado.
   - **Carga manual:** El operador sube el archivo desde interfaz web (botón de carga de archivo).
   - **Copia y pegado estructurado:** Como mecanismo transitorio, permitir ingreso semi-manual con validación de campos.

2. **Validar el archivo recibido**, verificando:
   - Que el formato corresponda al estándar definido.
   - Que los campos obligatorios estén presentes: número de guía, factura Z95, peso, dealer, fecha.
   - Que las facturas Z95 reportadas existan en el sistema (integradas desde Dynamics).

3. **Cruzar la información del agente de carga con las interfaces de Dynamics**, confirmando:
   - Que la Z95 reportada por DHL coincide con la Z95 recibida de Dynamics.
   - Que la Z95 esté asociada a la INB/INR correspondiente.

4. **Permitir corrección de inconsistencias menores** en los 5 campos identificados de la INB, directamente desde el SII, sin necesidad de regresar a Dynamics.

5. **Mostrar el resultado de la validación al usuario**, indicando:
   - Facturas Z95 validadas exitosamente.
   - Facturas Z95 no encontradas en el sistema.
   - Guías con inconsistencias detectadas.

6. **Filtrar y consultar la información** por:
   - Número de factura Z95
   - Número de guía
   - Compañía (Gecolsa / Relianz)
   - Dealer
   - Fecha
   - Estado (Recibida / Pendiente validación / Aprobada)

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Carga automática desde DHL exitosa
```gherkin
Dado que DHL ejecuta el bot de envío de información del consolidado
Cuando el archivo llega al sistema con el formato estándar acordado
Entonces el sistema debe procesar el archivo automáticamente
  Y registrar las guías, Z95, pesos y dealers sin intervención manual
  Y mostrar confirmación de carga exitosa al operador.
```

### Escenario 2 – Carga manual de archivo por operador
```gherkin
Dado que el bot de DHL no está disponible o falla
Cuando el operador sube manualmente el archivo desde la interfaz web
Entonces el sistema debe validar el formato del archivo
  Y si el formato es correcto, procesar la información automáticamente
  Y si el formato es incorrecto, mostrar el error con detalle de campo fallido.
```

### Escenario 3 – Factura Z95 no encontrada en Dynamics
```gherkin
Dado que el agente de carga reporta una factura Z95
Cuando el sistema verifica contra las interfaces de Dynamics
  Y la Z95 no ha sido procesada por las interfaces
Entonces el sistema debe informar que la Z95 no está disponible
  Y bloquear el avance a discrepancias para esa guía.
```

### Escenario 4 – Corrección de campos INB en el SII
```gherkin
Dado que una Z95 llegó asociada incorrectamente a una INB
Cuando el operador identifica la inconsistencia en el SII
Entonces el sistema debe permitir la corrección de los 5 campos habilitados de la INB
  Y registrar la corrección con usuario, fecha y justificación (auditoría).
```

### Escenario 5 – Error de rango de fechas en interfaces
```gherkin
Dado que una Z95 no llegó dentro del rango de fechas esperado de las interfaces
Cuando el operador consulta la guía correspondiente
Entonces el sistema debe informar que la Z95 está fuera del rango de carga
  Y el operador puede solicitar carga manual de esa Z95 específica.
```

---

## Reglas de Negocio

| ID   | Regla |
|------|-------|
| RN-01 | Solo se procesarán facturas Z95 reportadas por el agente de carga que existan en el sistema. |
| RN-02 | No se podrá avanzar al proceso de discrepancias si la Z95 no está validada en el SII. |
| RN-03 | El sistema debe soportar carga automática (DHL bot) y carga manual como mecanismos alternativos. |
| RN-04 | El formato del archivo del agente de carga debe ser estándar y acordado con DHL; el sistema debe validarlo. |
| RN-05 | Las correcciones sobre los 5 campos de la INB deben quedar registradas con trazabilidad (usuario + fecha + justificación). |
| RN-06 | El sistema debe validar por compañía (Gecolsa y Relianz operan con dealers distintos). |
| RN-07 | Los procesos de carga de interfaces de Dynamics no son configurables desde el SII; se ejecutan en ventanas de tiempo definidas (~9:00 AM y ~1:00 PM). |
| RN-08 | El pendiente de definición del formato DHL debe resolverse antes del desarrollo de la integración automática; mientras tanto, la carga manual es el mecanismo de contingencia. |

---

## Campos Obligatorios del Archivo del Agente de Carga

| Campo | Descripción | Tipo |
|-------|-------------|------|
| Número de Guía | Identificador del embarque | Texto |
| Factura Z95 | Número de factura Caterpillar | Texto |
| Peso | Peso del embarque | Numérico |
| Dealer | Código del dealer asignado | Texto |
| Fecha de consolidado | Fecha del reporte del agente | Fecha |

---

## Pendientes / Compromisos Identificados en Sesión

| # | Compromiso | Responsable |
|---|-----------|-------------|
| 1 | Validar y acordar el formato estándar del archivo de DHL (bot o Excel). | Eder / Operaciones con DHL |
| 2 | Confirmar con Caterpillar el mecanismo de intercambio de información (¿archivo plano o API?). | César / TI |
| 3 | Documentar los 5 campos de la INB que pueden corregirse en el SII. | Daisy / Equipo de Transformación |
| 4 | Definir las reglas de carga automática: rangos de filas, frecuencia, manejo de errores. | Oscar / Equipo desarrollo |

---

## Notas Técnicas

- Dynamics 365 genera **2 archivos planos** que alimentan el SII: uno con Z95 y otro con INB/INR.
- Las interfaces corren de manera automática; los errores en la carga son visibles en el SII pero la corrección varía según el origen del error.
- La integración con DHL (bot) se encuentra en fase de pruebas; el formato del archivo no está finalizado.
- La versión anterior a este sistema (S400/versión 1) realizaba carga de guías masivamente mediante archivos con parámetros definidos.
