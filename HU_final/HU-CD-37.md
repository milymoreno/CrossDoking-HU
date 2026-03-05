# HU-CD-37: Creación DO – Integración con Agencia de Aduanas

**Proceso:** CROSSDOCKING
**Subproceso:** CREACIÓN DO (Módulo: **Centro de Despacho (DO)**)
**Requerimiento Original:** REQ-37 — El sistema debe tener conexión con FILEZILA (programa de SIACO) donde se cargan las facturas INV para creación de DO. El programa debe generar un DO por medio de interfaz que permita interacción entre el sistema interno y la agencia de aduanas, y que se actualice a medida que avance el proceso (clasificación arancelaria, certificados VoBo, etc.).
**Versión:** 2.0
**Fecha de revisión:** 2026-03-04
**Estado:** Revisada con base en sesiones de levantamiento (25-Feb, 27-Feb y 02-Mar-2026)

---

## Historia de Usuario

### Como:
Analista de Comercio Exterior (Rol: **Coordinador Aduanero**)

### Quiero:
Crear el Documento Operativo (DO) desde el SII y transmitirlo automáticamente al sistema de la agencia de aduanas (SIACO / FileZilla), con actualización progresiva del estado a medida que la agencia avanza en el proceso aduanero.

### Para:
Iniciar formalmente el proceso aduanero con la agencia, mantener trazabilidad en tiempo real del estado del DO y reducir el trabajo manual de doble digitación entre sistemas.

---

## Contexto del Proceso

**DO (Documento Operativo):** Es el documento interno que consolida toda la información del embarque (facturas INV, cantidades, dealer, valor FOB) y que la agencia de aduanas necesita para iniciar el proceso de clasificación arancelaria, validar certificados VoBo y preparar la DIM.

**SIACO / FileZilla:** SIACO es el sistema de la agencia de aduanas. FileZilla es el programa de transferencia de archivos mediante el cual el SII deposita los documentos (facturas INV) para que la agencia los procese. 

**Retiro de API soe360grados:** El sistema actual utiliza el endpoint `https://siiapi.soe360grados.com.co:8443/`. Este servicio será retirado y sus funciones serán asumidas por el nuevo modelo de datos y procesos internos del SII:
1.  **GetToken**: Reemplazado por el nuevo esquema de autenticación Oauth2/JWT del SII.
2.  **GetResource**: Reemplazado por consultas directas a las tablas `guia` y `dim_declaracion` para identificar guías sin DIM.
3.  **GetOrderNumbers**: Reemplazado por la relación entre `fac_inv` y `oc_orden_compra`.
4.  **Información_DIAN.prn**: Se implementará un proceso que conecte al SFTP de SIACO, descargue el archivo `.prn` y lo deposite en el FTP interno del SII, siguiendo la estructura de carpetas oficial (Años / NITs).

### Estructura de Carpetas SIACO (FileZilla)
Según lo definido con la Agencia de Aduanas, el intercambio de archivos se organiza bajo la siguiente jerarquía en el servidor remoto:
- `/trabaja/documentos/`
  - `[AÑO]/` (ej. 2026)
    - `[NIT_COMPAÑIA]/` (ej. 860002576 para Gecolsa, 900806600 para Relianz)
      - `Informacion_DIAN` (Carpeta para archivos .prn)
      - `pendientes` (Documentos pendientes de proceso)
      - `reporte_stickers` (Archivos para impresión de etiquetas)
      - `SMUI...` (Archivos de interfaz de aduana)

---

## Envíos Courier

El sistema debe permitir la gestión de envíos vía Courier (ej. DHL Express, FedEx) que tienen un tratamiento aduanero simplificado:
- **Validación**: Se clasifican como "Courier" en la vía de transporte.
- **Diferencia**: No siempre requieren un DO formal ante SIACO si el trámite lo hace directamente el Courier.
- **Impacto**: El sistema debe permitir marcar una guía como "Courier" para que no bloquee procesos de nacionalización ordinaria.

---

## Tablas Legacy Involucradas

Para la migración y coexistencia, se referencian las siguientes tablas del sistema anterior:
- `LIBSII/SIPDLRC0`: Tabla maestra de dealers logísticos.
- `UPPGUISE0`: Tabla de cabecera de guías en el entorno legacy.

---

## Descripción Funcional

### Paso 1 — Generar DO desde guía validada
- El sistema toma la guía previamente validada (HU-CD-36: sin diferencias).
- Consolida en el DO:
  - Facturas INV PSC incluidas
  - Referencias y cantidades
  - Valor FOB
  - Información logística (dealer, aduana, vía transporte, incoterm)

### Paso 2 — Generar archivo estructurado
- El sistema genera un **archivo en el formato que requiere SIACO/FileZilla**.
- El formato exacto (CSV, XML, TXT con delimitadores) debe confirmarse con la agencia de aduanas.

### Paso 3 — Transmitir al sistema de la agencia
- El archivo se deposita automáticamente en la ubicación compartida de FileZilla.
- El sistema registra: fecha, hora y resultado del depósito.

### Paso 4 — Recibir actualizaciones progresivas
Una vez creado el DO, la agencia retorna información que debe reflejarse en el SII:

| Actualización | Descripción |
|--------------|-------------|
| Clasificación arancelaria (BTN) | El código arancelario asignado por la agencia a cada referencia |
| Certificados VoBo | Validaciones/aprobaciones de entidades de control (ej. ICA, INVIMA) |
| Estado del proceso | En qué etapa va el trámite aduanero |

---

## ¿Qué es el BTN en este contexto?

> En HU-CD-34 se mencionó el BTN como campo de las alertas de remanufacturados. Aquí queda clarificado:

**El BTN es la clasificación arancelaria asignada por la agencia de aduanas** (SIACO) al producto en el momento del proceso de importación. Corresponde a la **subpartida arancelaria** de la nomenclatura DIAN. Esta información:
- **No es ingresada por el analista de Commex** manualmente.
- **La asigna la agencia de aduanas** y la retorna al SII como parte de la actualización progresiva del DO.
- **Debe almacenarse en el DO** del SII una vez recibida.

En REQ-34, el BTN aparece porque las alertas de remanufacturados deben mostrar la subpartida que ya está parametrizada en el sistema o fue asignada previamente.

---

## Estados del DO

| Estado | Descripción |
|--------|-------------|
| Creado | DO generado en el SII; pendiente de envío. |
| Enviado a Agencia | Archivo transmitido a FileZilla/SIACO correctamente. |
| En Clasificación | La agencia está asignando subpartidas arancelarias (BTN). |
| Con VoBo | Certificados de VoBo recibidos de entidades de control. |
| Listo para DIM | Clasificación completa; disponible para generar DIM. |
| Cerrado | Proceso aduanero completado con levante. |
| Rechazado | La agencia rechazó el DO; requiere corrección y reenvío. |

---

## Validaciones Obligatorias

- ❌ No crear DO si existen discrepancias activas en las facturas.
- ❌ No crear DO si la guía no fue previamente validada (HU-CD-36 debe estar OK).
- ❌ No crear DO si faltan licencias de importación cuando aplica.
- ❌ No permitir duplicar DO sobre la misma guía.
- ✅ Permitir reproceso controlado si la transmisión falla técnicamente.

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Creación y envío exitoso del DO
```gherkin
Dado que la guía fue validada sin diferencias (HU-CD-36 OK)
Cuando el analista genere el DO
Entonces el sistema debe crear el registro interno
  Y transmitir el archivo a FileZilla/SIACO
  Y registrar fecha, hora y confirmación de envío.
```

### Escenario 2 – Actualización de clasificación arancelaria (BTN)
```gherkin
Dado que la agencia de aduanas asignó la clasificación arancelaria (BTN)
Cuando el sistema reciba la actualización desde SIACO
Entonces el DO debe refleja el BTN en cada referencia
  Y cambiar su estado a "En Clasificación" o "Con VoBo" según corresponda.
```

### Escenario 3 – Error en transmisión
```gherkin
Dado que la transmisión a FileZilla falla por error técnico
Cuando el sistema detecte el error
Entonces debe registrar el intento fallido con detalle del error
  Y permitir al analista reintentar el envío sin crear un DO duplicado.
```

### Escenario 4 – Bloqueo por DO duplicado
```gherkin
Dado que ya existe un DO para la guía G001
Cuando el usuario intente crear otro DO para la misma guía
Entonces el sistema debe bloquear la operación
  Y mostrar el mensaje: "Ya existe un DO para esta guía. No se permite duplicar".
```

---

## Reglas de Negocio

| ID | Regla |
|----|-------|
| RN-01 | Un DO pertenece a una única guía; relación 1 a 1. |
| RN-02 | No se permite crear DO duplicado sobre la misma guía. |
| RN-03 | Las actualizaciones desde la agencia se reflejan automáticamente en el DO del SII. |
| RN-04 | El DO debe conservar trazabilidad completa de cada actualización recibida. |
| RN-05 | No se puede modificar manualmente la información enviada a la agencia. |
| RN-06 | El DO en estado "Listo para DIM" es prerrequisito para generar la DIM (HU-CD-39). |
| RN-07 | Toda transmisión (exitosa o fallida) queda registrada en el log. |

---

## Pendientes / Compromisos de Sesión

| # | Compromiso | Responsable |
|---|-----------|-------------|
| 1 | **Definir el formato exacto del archivo para FileZilla/SIACO** (CSV, XML, TXT). | Fabián Barragán |
| 2 | Caracterización de datos requeridos en la consulta de servicios para el reemplazo de la API actual. | Deisy Rincón / Cesar Forero |
| 3 | Confirmar el endpoint y credenciales del nuevo SFTP de SIACO para el archivo `.prn`. | TI / Agencia de Aduanas |
| 4 | Definir el flujo simplificado para envíos Courier y su impacto en el costeo. | Commex |
