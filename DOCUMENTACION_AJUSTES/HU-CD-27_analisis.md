# Análisis Comparativo: HU-CD-27 (Recepción y Visualización de Relación Facturación Z95 vs INV PSC)

## ¿Qué tienen en común todas las versiones?
1. **Objetivo Principal**: Posibilitar la visualización cruzada y automática entre facturas Z95 provenientes de Caterpillar y facturas INV generadas por Panamerican (PSC), obtenidas de D365.
2. **Criterio de Remanufacturados**: Todas coinciden firmemente en que el campo oficial para determinar un producto remanufacturado es `Reman Indicator = 1`. Coinciden en que los valores *Premium* y *Cargo Core* deben sumarse y la cantidad debe ser igual a uno.
3. **Manejo de referencias**: Establecen que el sistema no debe truncar las referencias ni poner límite de caracteres ni excluir caracteres especiales.

## Diferencias Principales (Aportes de cada versión)

### Versión de Oscar C:
- **Detalle de Estados**: Conceptualiza explícitamente los estados de la facturación en pantalla (Relacionada, Pendiente_INV, Inconsistente, Procesada).
- **Manejo de Concurrencia**: Presenta explícitamente el requisito no funcional de que la información debe permitir la simultaneidad de lectura entre usuarios para compañías como Gecolsa o Relianz sin bloqueos de tabla.
*(Nota: Incluía modelo técnico relacional de tablas y rutas API, los cuales se han retirado en el consolidado por reglas de negocio).*

### Versión de Oscar E:
- **Foco Funcional Frontal**: Se centra 100% en el comportamiento esperado en la interfaz de visualización por parte del Analista de Comercio Exterior.
- **Claridad Normativa Reman**: Enfatiza la regla de exclusión de lectura de sufijos como "R" o "CC" para validar mercancía remanufacturada.

### Versión Final (Mily):
- **Profundidad Funcional**: Es la versión más descriptiva e integra fuertemente las lógicas de negocio requeridas, como la "Cardinalidad 1:N" (Una Z95 puede estar ligada a múltiples INVs).
- **Formatos y Exportación**: Define puntualmente la estructura que debe tener el reporte descargable en Excel.
- **Deuda y Pendientes**: Mantenía la tabla de responsabilidades y tareas técnicas de seguimiento.

---

## Estrategia para la Versión Consolidada
Para este archivo consolidado (`HU-CD-27.md`) la base principal fue la **Versión Final (Mily)** debido a sus reglas de negocio avanzadas y exhaustiva documentación de reportes. 

De las versiones de **Oscar C.** y **Oscar E.** se extrajo y estandarizó la nomenclatura visual de los **Estados (Relacionada / Pendiente INV / Inconsistente)** integrándolos de forma nativa a los escenarios Gherkin existentes. Se validó adicionalmente que el archivo `modelo_crossdocking.sql` actual de la base de datos ya soporta completamente los campos de remanufacturado (`reman_indicator`), por lo que no hizo falta alterar el esquema SQL para esta historia.

*(Todos los nombres propios en la lista de pendientes han sido sustituidos por nombres estructurales de roles: Equipo TI, Líder Funcional, Equipo de Transformación).*
