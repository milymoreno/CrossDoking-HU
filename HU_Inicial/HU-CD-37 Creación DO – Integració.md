# HU-CD-37: Creación DO – Integración con Agencia de Aduanas

**Proceso:** CROSSDOCKING  
**Subproceso:** CREACIÓN DO  
**Requerimiento Original:** 37 - El sistema debe tener conexión con FILEZILA (programa de SIACO) donde se cargan las facturas INV para creación de DO. El programa debe generar un DO por medio de interfaz que permita interacción entre el sistema interno y la agencia de aduanas, y que se actualice a medida que avance el proceso (clasificación arancelaria, certificados VoBo, etc.).

---

### Como:
Analista de Comercio Exterior

### Quiero:
Crear el DO (Documento Operativo) desde el sistema SII e integrarlo con el sistema de la agencia de aduanas

### Para:
Iniciar formalmente el proceso aduanero y mantener sincronización entre ambos sistemas.

---

## Descripción Funcional

El sistema debe permitir:

1️⃣ Generar un DO desde la guía previamente validada (HU-CD-36).

2️⃣ Consolidar en el DO:
   - Facturas INV PSC
   - Referencias
   - Cantidades
   - Valor FOB
   - Información logística

3️⃣ Generar archivo o estructura compatible con:
   - FileZila / SIACO

4️⃣ Enviar automáticamente la información al sistema de la agencia.

---

## Integración Externa

El sistema debe:

- Permitir conexión mediante carga/descarga de archivos estructurados.
- Registrar fecha y hora de envío.
- Registrar confirmación de recepción.
- Permitir reproceso en caso de error técnico.

---

## Actualización Progresiva del DO

Una vez creado el DO:

El sistema debe poder recibir actualizaciones provenientes de la agencia, tales como:

- Clasificación arancelaria (BTN).
- Certificados VoBo.
- Información complementaria aduanera.
- Estado del proceso.

Estas actualizaciones deben reflejarse automáticamente en el DO dentro del SII.

---

## Estados del DO

El DO debe tener estados controlados:

- Creado
- Enviado a Agencia
- En Clasificación
- Con VoBo
- Listo para DIM
- Cerrado
- Rechazado

---

## Validaciones Obligatorias

- No permitir crear DO si:
  - Existen discrepancias activas.
  - No se generó guía válida.
  - Faltan licencias cuando aplican.

- No permitir duplicar DO sobre la misma guía.

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Creación exitosa DO
Dado que la guía fue validada  
Cuando el usuario genere DO  
Entonces el sistema debe crear el registro y enviar información a la agencia.

---

### Escenario 2 – Confirmación de envío
Dado que el DO fue generado  
Cuando el archivo se transmita  
Entonces el sistema debe registrar confirmación de envío.

---

### Escenario 3 – Recepción clasificación
Dado que la agencia asigne clasificación arancelaria  
Cuando el sistema reciba actualización  
Entonces el DO debe reflejar la información actualizada.

---

### Escenario 4 – Error en transmisión
Dado que falle la transmisión  
Cuando el sistema detecte error  
Entonces debe permitir reproceso controlado.

---

## Reglas de Negocio

- RN-01: Un DO pertenece a una única guía.
- RN-02: No se permite crear DO duplicado.
- RN-03: La actualización desde agencia es automática.
- RN-04: El DO debe conservar trazabilidad completa.
- RN-05: No se permite modificar manualmente información enviada.
- RN-06: El DO es prerrequisito para generación de DIM.