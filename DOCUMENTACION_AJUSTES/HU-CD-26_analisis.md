# Análisis Comparativo: HU-CD-26 (Recepción de Información Consolidada)

## ¿Qué tienen en común todas las versiones?
1. **Objetivo Principal**: Recibir y validar la información enviada por el agente de carga (ej. DHL).
2. **Campos obligatorios**: Número de guía, Factura Z95, Peso, Dealer, Fecha consolidado.
3. **Validación Core**: Todas establecen que la principal validación es verificar la existencia de la factura Z95 en el sistema.

## Diferencias Principales (Aportes de cada versión)

### Versión de Oscar C:
- **Enfoque en Auditoría y Trazabilidad**: Aporta la necesidad de almacenar el archivo (Blob Storage), auditar quién lo subió, e historializar la ejecución (total registros, etc.).
- **Detalle de Estados**: Propone estados granulares para la carga (Cargado, Validado, Parcial, Error) basándose en si se encontraron o no las Z95.
*(Nota: Originalmente incluía el diseño de BD y endpoints de API, pero fueron excluidos del documento final por ser puramente funcional).*

### Versión de Oscar E:
- **Enfoque Operativo y Sencillo**: Estructura de historia directa y concisa.
- **Validación Específica**: Aporta explícitamente la necesidad de validar el cruce por compañía (Gecolsa vs Relianz).

### Versión Final (Mily):
- **Enfoque de Negocio As-Is**: Documenta detalladamente cómo actúan actualmente las interfaces de Dynamics, lo cual da contexto real al proceso.
- **Rigor en Reglas**: Aporta reglas de negocio estrictas (ej. no avanzar a discrepancias sin Z95 validada, prevención de duplicidad).
- **Pendientes de Sesión**: Registra la lista de compromisos técnicos y operativos pendientes de resolver (formatos, campos INB editables, etc.).

---

## Estrategia para la Versión Consolidada
Para el archivo consolidado (`HU-CD-26.md`) se tomó la **Descripción Funcional y Contexto de Negocio de la versión Final (Mily)**. Se le sumó el esquema de **Estados de Carga y Trazabilidad** estructural pensado por Oscar C, y se mantuvo la regla de exclusividad por compañía mencionada por Oscar E y Mily.

Se aprovecharon estos aportes para actualizar el Modelo de Datos (`modelo_crossdocking.sql`), incorporando campos logísticos de auditoría para la tabla `consolidado_dhl`, alineados con la visión técnica pero manteniendo la HU limpia de código.
