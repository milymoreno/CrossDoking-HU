# HU-CD-26: Recepción de Información Consolidada del Agente de Carga

**Proceso:** CROSSDOCKING
**Subproceso:** RECEPCIÓN CONSOLIDADO (Módulo: **Gestor de Guías**)
**Requerimiento Original:** REQ-26 — El sistema deberá tener la capacidad de interactuar con el agente de carga en cuanto a la recepción de la información consolidada (Guías, facturas Z95, pesos y dealers).
**Versión Consolidada:** 3.0
**Estado:** Integrada (Aportes Negocio + Funcional)

---

## Historia de Usuario

### Como:
Analista de Comercio Exterior / Operaciones CrossDocking (Rol: **Operador Logístico CD**)

### Quiero:
Que el sistema reciba y valide la información consolidada enviada por el agente de carga (DHL u otro agente logístico), mediante un mecanismo de carga automático o manual, validando la existencia de las facturas Z95.

### Para:
Confirmar que las facturas Z95 reportadas en los embarques estén disponibles en el sistema, asegurar la trazabilidad del archivo recibido, y habilitar el inicio del proceso de Gestión de Discrepancias.

---

## Contexto del Proceso (As-Is)

1. El agente de carga genera un archivo consolidado del embarque.
2. Actualmente, el mecanismo de envío no está estandarizado (puede ser un bot o subida manual al SII por Operaciones).
3. En Dynamics 365, Caterpillar (CAT / compañía 500) genera facturas **Z95** asociadas a las órdenes de compra.
4. Dynamics genera dos interfaces hacia el SII:
   - **Interfaz 1:** Archivo plano con datos de la **Z95**.
   - **Interfaz 2:** Archivo plano con datos de las **INB/INR**, ya con la Z95 asociada.
5. Errores frecuentes incluyen asociaciones incorrectas de Z95 con INB, los cuales se corrigen actualmente en el SII en **5 campos específicos de la INB**.
6. Las interfaces corren en ventanas de tiempo definidas (~9:00 AM y ~1:00 PM).

---

## Descripción Funcional

El sistema debe:

1. **Recibir la información del agente de carga** por:
   - **Carga automática:** Integración directa mediante archivo estándar o API.
   - **Carga manual (Principal transitorio):** El operador sube el archivo (Excel/CSV) desde la interfaz web.

2. **Validar la estructura del archivo**, verificando:
   - Que el formato corresponda al estándar.
   - Restricciones como tamaño máximo, no duplicidad del archivo (ya cargado previamente), y no duplicidad de líneas dentro del mismo archivo.

3. **Cruzar la información del archivo con el sistema**, confirmando:
   - Que la Z95 reportada existe en el sistema (integrada desde Dynamics).
   - Validar la compañía asociada y el dealer.

4. **Registrar la Trazabilidad del Archivo:** El sistema debe historializar cada carga con métricas: nombre del archivo, usuario que cargó, fecha, tamaño y total de registros.

5. **Clasificar el Proceso en Estados Visibles:**
   - **Cargado:** Archivo en sistema pero sin validar.
   - **Validado:** Todas las Z95 existen en el sistema.
   - **Parcial:** Algunas Z95 no existen aún.
   - **Error:** Falla estructural o ninguna Z95 existe.

6. **Permitir limpieza visual del flujo:** Facturas procesadas correctamente deben salir de la vista general para no generar ruido al Analista.

7. **Permitir corrección de inconsistencias menores** en los 5 campos identificados de la INB desde el SII, con registro de auditoría.

8. **Filtros de Búsqueda:** Poder consultar el historial por Número de Z95, Guía, Compañía, Dealer, Fecha y Estado.

---

## Reglas de Negocio

| ID   | Regla |
|------|-------|
| RN-01 | Solo se procesarán facturas Z95 reportadas por el agente de carga que existan en el sistema. |
| RN-02 | No se podrá avanzar al proceso de discrepancias (HU-CD-28) de un embarque cuya Z95 no esté validada en el SII. |
| RN-03 | El sistema debe validar la compañía (Gecolsa y Relianz operan con dealers distintos). |
| RN-04 | Validaciones de integridad del archivo: tamaño permitido, campos obligatorios, y prevención de duplicidad del mismo archivo en el día. |
| RN-05 | Las correcciones sobre los 5 campos de la INB en el SII deben registrar trazabilidad (rol, fecha, justificación). |
| RN-06 | La suma de registros válidos e inválidos mostrados en el resultado debe coincidir con el total de registros del archivo original. |

---

## Campos Obligatorios del Archivo del Agente de Carga

| Campo | Descripción |
|-------|-------------|
| Número de Guía | Identificador del documento de transporte |
| Factura Z95 | Factura emitida por Caterpillar |
| Peso | Peso del embarque |
| Dealer | Dealer logístico |
| Fecha de consolidado | Fecha del reporte del agente |

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Carga manual y validación exitosa
```gherkin
Dado que el operador logístico carga el archivo consolidado del agente de carga
Cuando el archivo cumple el formato definido y todas las Z95 existen
Entonces el sistema debe procesarlo correctamente
  Y registrar el estado general como "Validado"
  Y guardar la métrica de total de registros.
```

### Escenario 2 – Validación parcial por retraso de interfaz
```gherkin
Dado que un archivo contiene varias facturas Z95
Cuando el sistema valida contra los registros del SII
  Y algunas Z95 aún no han llegado desde Dynamics
Entonces el estado del proceso de carga debe ser marcado como "Parcial"
  Y las guías sin Z95 quedan bloqueadas para pasar a Discrepancias.
```

### Escenario 3 – Prevención de carga duplicada
```gherkin
Dado que un archivo consolidado ya fue cargado exitosamente hoy
Cuando el usuario intenta cargar el mismo archivo nuevamente
Entonces el sistema debe advertir duplicidad y rechazar la carga operativa.
```

### Escenario 4 – Archivo con formato estructural inválido
```gherkin
Dado que el archivo de carga tiene una estructura inválida (ej. vacío o columnas erróneas)
Cuando el sistema intenta procesarlo
Entonces debe rechazarlo inmediatamente y mostrar error general de estructura, sin analizar línea por línea.
```

---

## Pendientes / Compromisos de Sesión

* **Pendiente** -> Validar y acordar el formato estándar del archivo (bot o Excel). Responsable: Líder de Operaciones / Proveedor Logístico.
* **Pendiente** -> Confirmar el mecanismo final de intercambio de información automatizada (¿archivo plano, SFTP, API?). Responsable: Arquitectura TI.
* **Pendiente** -> Documentar explícitamente los 5 campos de la INB que pueden corregirse en el SII con sus respectivos dominios de datos. Responsable: Analistas Commex.
* **Pendiente** -> Validar ventanas de tiempo para que la generación automática no colisione con las interfaces D365. Responsable: Líder de Operaciones.
