# HU-CD-28 y HU-CD-34 — Documento de Diferencias y Justificaciones de Ajuste

**HU:** HU-CD-28 (Discrepancias) y HU-CD-34 (Alertas REMAN)
**Fecha de revisión:** 2026-03-05
**Estado:** Aplicado según notas de Fabián Higareda

---

## 1. Gestión de Discrepancias (HU-CD-28)

### Ajustes Realizados:
- **Definición de Campos Editables**: Se incorporó la lista explícita de los 5 campos que el SII permite corregir para la nacionalización (Subpartida, Unidad, Peso, Origen, Descripción).
- **Origen de Corrección**: Se clarificó que los errores de asociación Z95 vs INV pertenecen a Dynamics, mientras que los errores de datos maestros de nacionalización se resuelven en el SII.

### Justificación:
Basado en la nota técnica (Imagen 2) que indica que el SII debe permitir la edición de estos campos específicos para no bloquear el proceso de nacionalización cuando hay errores menores en la factura de origen.

---

## 2. Alertas REMAN - Sin Licencia (HU-CD-34)

### Ajustes Realizados:
- **Automatización de Notificación**: Se eliminó la referencia a la notificación manual por correo electrónico. El sistema ahora genera una alerta automática de "Sin Licencia" en el monitor de discrepancias.
- **Responsabilidad Directa**: Se asignó al Analista Commex CD como el único responsable de gestionar y resolver estas alertas.

### Justificación:
Siguiendo la instrucción de Fabián Higareda (Imagen 1) para agilizar el proceso y centralizar el control de las referencias remanufacturadas sin licencia vigente.

---

## 3. Impacto en el Modelo SQL

### Cambios en `modelo_crossdocking.sql`:
- **Tabla `fac_inv_linea`**: Se añadieron los campos `subpartida_sii`, `unidad_medida_sii`, `peso_neto_sii`, `pais_origen_sii` y `descripcion_sii`.
- **Tabla `discrepancia`**: Se unificó el tipo de error para incluir "Sin Licencia", permitiendo una trazabilidad centralizada de todos los bloqueos de guía.

---
*Documento generado por Antigravity para soporte de trazabilidad de requerimientos.*
